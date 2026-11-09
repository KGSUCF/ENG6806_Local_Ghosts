# Comparison notes

| | Claude (original) | gpt-oss:20b | qwen3-coder:30b (optional) |
|---|---|---|---|
| How it was made | Several rounds of conversation | One prompt | One prompt |
| Time to answer | Several sessions | ~7.5 minutes | Under 10 minutes |
| Opened and worked on first try? | Yes | No: blank list (one JavaScript syntax error stops the whole script) | Yes |
| Features that work (out of 10) | 10 | About 4 after one fix round | 6 fully, 1 partly, 3 broken |
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

### qwen3-coder:30b, first attempt (`local/pink-qwen3-coder.html`)

Same original prompt, one try, under 10 minutes. Opened with no script errors. Browser test of the 10 requested features:

| # | Feature | Result |
|---|---|---|
| 1 | Title and subtitle | ✅ |
| 2 | Search ignoring caps, apostrophes, spaces | ✅ "docbs" finds Doc B's. ⚠️ No highlighting when you type it that way |
| 3 | Filter buttons with counts | ⚠️ Work, but counts don't update after adding a restaurant, and the label reads "Not-checked" |
| 4 | Alphabetical list with colored badges | ✅ |
| 5 | Offer to add a restaurant a search didn't find | ❌ The button appears, but the panel closes the moment it opens |
| 6 | Add restaurant, warn on duplicates | ✅ "Tap42" correctly flagged as a duplicate |
| 7 | Edit answer, fix name, delete with confirmation, show date | ✅ Works. ⚠️ The "last updated" dates for the starting list (2023–2024) are **made up** |
| 8 | Message with Undo after changes | ❌ Undo button shows but does nothing (it reloads the already-saved list) |
| 9 | Saves between visits | ✅ |
| 10 | Share and Import from the menu | ❌ Menu opens, but both panels close instantly because a "click outside to close" rule catches the same click that opened them |

Its closing summary claimed every feature worked, including undo, share and import. Three of those claims are false.

Compared with gpt-oss: it worked on the first try, sorted the list, and connected the + button. It also made much more design effort (rounded cards, dark mode, a pink round menu button). It didn't test its own work either, so the failures were the kind that only show when you click.

### qwen3-coder:30b, version 2 (`local/pink-qwen3-coder-v2.html`)

Follow-up prompt: three symptoms described in plain terms (Share/Import vanish, "Add this restaurant" vanishes, Undo does nothing), plus "check the rest of the code for other bugs." It didn't report finding anything else.

Browser test:

| Problem | Fixed? |
|---|---|
| Share and Import panels vanish | ✅ Both open and work. An imported list adds new restaurants and updates existing ones |
| "Add this restaurant" panel vanishes | ✅ Opens, with the searched name filled in |
| Undo does nothing | ⚠️ Partly. Undo now reverses an add or a delete, but **not an edit**: change YOLO from Pink to No, press Undo, and it stays No. Its saved copy of the list points to the same restaurant records it then changes, so there's nothing to go back to |
| Filter counts don't update (deliberately not mentioned) | ❌ Not noticed. After adding and importing, the count still reads All (25) with 26 restaurants on screen |
| Made-up 2023–2024 dates | Unchanged |

Score after one fix round: **about 8 of 10 features working fully**, with undo partly working and the filter counts stale. gpt-oss reached about 4 after its fix round, and its "fix" introduced a new bug. qwen's fixes introduced none that the test found.
