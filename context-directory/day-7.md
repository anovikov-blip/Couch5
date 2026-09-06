# Day 7 — What Claude Code Is (and why not just use the chat box)

## The one-line version

Claude Code is Claude with hands. Same model you'd talk to in a browser tab, except
it can read your files, run your commands, see what breaks, and fix it — without you
playing courier.

## The honest case for it

If you've used AI for code in a browser, you know the loop:

1. Copy a file into the chat.
2. Get back a suggestion that's 80% right.
3. Paste it in, run it, watch it fail.
4. Copy the stack trace back into the chat.
5. Repeat until you give up or it works.

You are the integration layer. Every round trip costs you attention, and the model is
guessing at everything you didn't paste — your other 40 files, your actual dependency
versions, what your test suite says.

Claude Code closes that loop. It runs in your terminal (or IDE, or the web) with access
to the real repo. It greps for the function instead of asking you what it's called. It
runs the tests and reads the failure itself. It makes a change across six files because
it can actually see all six.

The difference isn't "smarter answers." It's that the model gets **feedback** — and
feedback is what turns a plausible guess into a correct one.

## What it's actually good at

- Multi-file changes where you'd otherwise be pasting all day
- "Why does this break?" — it can reproduce instead of theorize
- Tedious-but-not-hard work: migrations, test coverage, renames, cleanup
- Getting oriented in a codebase nobody documented

## Where the skepticism is warranted

- **It's not autonomous.** It's a fast pair, not a replacement. You review the diff.
  Anything you'd catch in a human's PR, you still have to catch here.
- **It can be confidently wrong.** The advantage is that running tests makes wrong-ness
  visible fast, not that it stops happening.
- **It's not free** — of tokens or of your judgment. Handing it something you don't
  understand yourself is how you end up with code you can't maintain.

## When the browser tab is still fine

Conceptual questions. "Explain this pattern." Anything not touching a real codebase.
Don't reach for the heavier tool when you just want to think out loud.

## The actual reason to try it

The gap between "AI that suggests code" and "AI that runs code" is bigger than it
sounds. Most of the frustration with the first kind comes from the model working blind.
Give it eyes and a shell, and a lot of that frustration just goes away.

Try it on something boring and mechanical first. That's where the case makes itself.
