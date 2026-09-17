# Behavior Checks

How these were observed: the Claude in Chrome extension was not connected, so the click checks ran the page's own `<script>` from `index.html` in Node with a small stand-in for the page (a stubbed DOM). Each "click" called the real Next/Previous handlers, and the table records the line text and counter the page produced. The keyboard row can't be tested without a real browser, so it is marked **Pending** until someone checks it by hand.

| Check | Expected | Observed | Pass/fail |
|---|---|---|---|
| Open or refresh the page | First line; counter 1 of 14 | "Shall I compare thee to a summer's day?"; Line 1 of 14 | Pass |
| Click Next once | Second line; counter 2 of 14 | "Thou art more lovely and more temperate:"; Line 2 of 14 | Pass |
| Continue to the last line | Last line; counter 14 of 14 | "So long lives this, and this gives life to thee."; Line 14 of 14 | Pass |
| Click Next at the last line | First line; counter 1 of 14 | "Shall I compare thee to a summer's day?"; Line 1 of 14 | Pass |
| Tab to Next, then press Enter | Button advances one line | Not observed yet: needs a real browser (Tab goes to Previous first, then Next) | Pending |
| My change: ordinary case (Previous on line 3) | Line 2 "Thou art more lovely…"; counter 2 of 14 | "Thou art more lovely and more temperate:"; Line 2 of 14 | Pass |
| My change: boundary case (Previous on line 1) | Wraps to line 14; counter 14 of 14 | "So long lives this, and this gives life to thee."; Line 14 of 14 | Pass |
| Extra: Previous twice from line 1, then Next twice | 14 → 13, then 14 → 1 | Line 14, Line 13, Line 14, Line 1 of 14 | Pass |
| Extra: drawing shows on load | Shakespeare portrait is visible above the first line | First try, loading from separate .jpeg files: only the broken-image placeholder showed (reported after opening the page). I embedded the images in index.html; after a refresh, the drawing shows (confirmed by me in the browser) | Pass (initially failed) |
| Extra: drawing switches on each click | Load shows drawing 1; each Next/Previous click switches to the other drawing | Load → drawing 1; Next → 2; Next → 1; Previous → 2; Previous → 1; Previous (wrap to line 14) → 2. Only the image `src` was checked, not how it looks in a real browser | Pass (logic only) |

## Note on the first failed attempt

My first try at the boundary check was wrong. The check script pressed Previous on **line 2** (after going 1→2→3→2) instead of on a fresh line 1, so it showed "Line 1 of 14" and didn't test the wraparound at all. That was a mistake in the check, not in the app. I reran it on a fresh page load, pressed Previous on line 1, and got Line 14 of 14 as expected. Because this new step went back through the Next wraparound, I also reran Next at line 14 → line 1.
