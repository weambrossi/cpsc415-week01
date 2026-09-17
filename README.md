# Lyrics Trainer (Week 01)

## What it does

A small tool for memorizing Shakespeare's Sonnet 18 (`lyrics.txt`). It shows one line at a time with a counter like "Line 1 of 14", plus **Previous** and **Next** buttons. Both wrap around: Next on line 14 goes back to line 1, and Previous on line 1 goes to line 14.

**How to open it:** double-click `index.html` in Finder (or File Explorer). You don't need a web server, an install, or an internet connection. The poem is built into the file.

## Harness and model

- **Coding agent:** Claude Code in the Claude desktop app (Code tab), running the model Claude Opus 5 (`claude-opus-5`).
- **The app itself** is plain HTML, CSS, and JavaScript. It makes no model calls, API calls, or network requests. The AI was used only to write the code.

## My change: a Previous button

I added a Previous button so I can go back and re-read a line I just missed, without clicking Next through the whole poem. I chose the boundary behavior before the edit: **Previous on line 1 wraps to line 14**, the mirror image of Next.

How my instructions shaped the result: I asked for wraparound at line 1 and said Next, the counter, and wraparound had to keep working. So the agent kept the Next handler exactly as it was and added a separate Previous handler with the same modulo pattern. It didn't clamp at line 1 and didn't restructure the existing code.

## A correction I made

My first boundary check pressed Previous on line 2 instead of line 1, so it never tested the wraparound. I reran it on a fresh page load and it passed (line 1 → Line 14 of 14). I also added an extra check (Previous twice from line 1, then Next twice). Details are in `CHECKS.md`.

## One thing I can explain in the code

The wraparound uses the remainder operator `%` in `index.html`:

- Line 101 (Next): `index = (index + 1) % lines.length;`. On the last line, `index` is 13, so `(13 + 1) % 14` is `0`, which is back to the first line.
- Line 107 (Previous): `index = (index - 1 + lines.length) % lines.length;`. On the first line, `(0 - 1 + 14) % 14` is `13`, which is the last line. Adding `lines.length` first keeps the number from going negative.

After either change, `render()` (line 95) writes `lines[index]` into the page and updates the counter to `index + 1`.

## Usage evidence

Unavailable. This lab ran through a Claude Code subscription session in the desktop app, not a pay-per-use API key, so there is no per-lab dollar amount on a provider activity page to report.

## Remaining problems / blockers

- The Claude in Chrome extension wasn't connected, so I checked behavior by running the page's script with a stubbed DOM instead of clicking in a real browser. The **Tab → Enter** keyboard check is still **pending** and needs someone to try it in a real browser.
- No pairing help was used, and I didn't need the book's fallback example.
