# Comparison notes

| | Claude (original) | gpt-oss:20b | qwen3-coder:30b (optional) |
|---|---|---|---|
| How it was made | Several rounds of conversation | One prompt | One prompt |
| Time to answer | Several sessions | ~7.5 minutes | |
| Opened and worked on first try? | Yes | No: blank list (one JavaScript syntax error stops the whole script) | |
| Features that work (out of 10) | 10 | | |
| What broke or was missing | | See below | |
| Look and feel | | | |

## Observations

-

### gpt-oss:20b, first attempt (`local/pink-gpt-oss.html`)

Tested in a headless browser.

- **Fatal:** `panel.style.background=var(--bg);` is missing quotes, which is a syntax error, so no JavaScript runs and the list is empty.
- With only that line fixed, the 25 restaurants appear and search works ("docbs" finds Doc B's), but:
  - The "Not checked" filter count shows "undefined" (it counts `'Not'` when the stored value is `'Not checked'`).
  - The big + "Add restaurant" button does nothing because it's never connected to any code.
  - The Cancel buttons call `closeModal()`, which the page can't reach, so they fail.
  - The list isn't alphabetical.
  - Shared text uses the heading "Not", which Import doesn't recognize, so a round trip files Not-checked restaurants under No.
  - Undo after renaming a restaurant adds a duplicate instead of reverting it.
  - "Last changed" dates for the starting list are all the moment the page first loaded.

### gpt-oss:20b, second turn (debugging its own code)

Follow-up prompt: the console error ("Unexpected token 'var'") plus a request to check the rest of the code. Its full thinking is in `local/gpt-oss-fix-thinking.txt`.

What its thinking found, compared with the browser test:

| Problem | Found by the model? |
|---|---|
| `var(--bg)` syntax error (fatal) | Yes, with the right fix (`'var(--bg)'`) |
| "Not checked" count shows undefined | Yes |
| + Add button never connected | No. It checked the *other* add button and said "Good" |
| Cancel buttons can't reach `closeModal()` | No |
| List not alphabetical | No |
| Share/Import heading mismatch ("Not" vs "Not checked") | No |
| Undo after rename creates a duplicate | No. It looked at undo and decided to "ignore" it |

How it reasons: it reads the code and judges each part rather than running it, so a lot of lines end in "fine," "ok," or "Good." It also hedges ("Might show variable but will apply") and starts by guessing the error is in the CSS before finding the real line.

### gpt-oss:20b, version 2 (`local/pink-gpt-oss-v2.html`)

Browser test of the code it returned after debugging:

- ✅ Fixed: the list now loads (25 restaurants, no script errors) and the "Not checked" count shows 3.
- ❌ Still broken: the + Add button does nothing; Cancel fails ("closeModal is not defined"); the list isn't alphabetical; undo after a rename creates a duplicate.
- ❌ Newly found: the ☰ menu button sits *behind* the title, so you can't click it. Share and Import can't be reached.
- ❌ **New bug introduced by the "fix":** in Share it changed `groups[cat==='Not'?'Not':cat]` to `groups[cat==='Not'?'Not':'Not']`, so every heading (Pink, No, Not) lists the three Not-checked restaurants. A fix made something else worse.
