# Lyrics Trainer (Week 01)

## What it does

A small tool for memorizing Shakespeare's Sonnet 18 (`lyrics.txt`). It shows one line at a time with a counter like "Line 1 of 14", plus **Previous** and **Next** buttons. Both wrap around: Next on line 14 goes back to line 1, and Previous on line 1 goes to line 14. A drawing of Shakespeare sits above the text and switches between two drawings every time you click Previous or Next.

**How to open it:** double-click `index.html` in Finder (or File Explorer). You don't need a web server, an install, or an internet connection. The poem is built into the file. The two Shakespeare drawings are built into the file too, so `index.html` is the only file you need.

## Harness and model

- **Coding agent:** Claude Code in the Claude desktop app (Code tab), running the model Claude Opus 5 (`claude-opus-5`).
- **The app itself** is plain HTML, CSS, and JavaScript. It makes no model calls, API calls, or network requests. The AI was used only to write the code.

## My change: a Previous button

I added a Previous button so I can go back and re-read a line I just missed, without clicking Next through the whole poem. I chose the boundary behavior before the edit: **Previous on line 1 wraps to line 14**, the mirror image of Next.

How my instructions shaped the result: I asked for wraparound at line 1 and said Next, the counter, and wraparound had to keep working. So the agent kept the Next handler exactly as it was and added a separate Previous handler with the same modulo pattern. It didn't clamp at line 1 and didn't restructure the existing code.

## Visual addition: switching Shakespeare drawings

After the Previous button, I added two Shakespeare drawings above the poem: an ink portrait and a cartoon of him writing with a quill. The page starts on the portrait, and every Previous or Next click switches to the other drawing, so each new line gets a small visual change too.

How it works: `swapPortrait()` (lines 117–120 of `index.html`) uses the same `%` wraparound as the line counter to flip `portraitIndex` between 0 and 1, then sets the `<img>` element's `src`. Both button handlers call it right after `render()`. The `.portrait` CSS rule gives the image a fixed box with `object-fit: contain`, so the square portrait and the wider cartoon take up the same space without being cropped and the buttons don't jump around.

**Correction:** I first loaded the drawings from separate `.jpeg` files next to `index.html`. When I opened the page, I only saw the broken-image placeholder. To fix it, I embedded both drawings directly in `index.html` as base64 data URLs (the `portraits` array). Now there are no separate files to load, and the app is back to one file that opens from disk, with no dependencies or network requests. The trade-off is that `index.html` is bigger (about 115 KB).

## A correction I made

My first boundary check pressed Previous on line 2 instead of line 1, so it never tested the wraparound. I reran it on a fresh page load and it passed (line 1 → Line 14 of 14). I also added an extra check (Previous twice from line 1, then Next twice). Details are in `CHECKS.md`.

## One thing I can explain in the code

The wraparound uses the remainder operator `%` in `index.html`:

- Line 123 (Next): `index = (index + 1) % lines.length;`. On the last line, `index` is 13, so `(13 + 1) % 14` is `0`, which is back to the first line.
- Line 130 (Previous): `index = (index - 1 + lines.length) % lines.length;`. On the first line, `(0 - 1 + 14) % 14` is `13`, which is the last line. Adding `lines.length` first keeps the number from going negative.

After either change, `render()` (line 111) writes `lines[index]` into the page and updates the counter to `index + 1`.

## Usage evidence

This lab was done on a **Claude Pro subscription** through Claude Code in the Claude desktop app. It did not use a pay-per-use API key, so there is no dollar cost per request. These numbers come from the app's usage panel, read at the end of the lab on 2026-09-17:

- **Extra (paid) usage spent:** $0.00. Everything stayed inside the Pro plan limits.
- **5-hour plan limit:** 6% used.
- **Weekly limit (all models):** 5% used.
- **Context used by this coding session:** about 72,000 tokens out of a 1,000,000-token window (7%).

The plan-limit percentages cover my whole account during those windows, not only this lab, so they are an upper bound for the lab's usage.

## Remaining problems / blockers

- The Claude in Chrome extension wasn't connected, so I checked behavior by running the page's script with a stubbed DOM instead of clicking in a real browser. The **Tab → Enter** keyboard check is still **pending** and needs someone to try it in a real browser.
- No pairing help was used, and I didn't need the book's fallback example.
