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
