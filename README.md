# VoxFlow 4Linux

**A complete, buildable specification for a push-to-talk voice dictation tool on Linux.**

This repository does not contain an application. It contains the **blueprint** for one — a single document detailed enough that a capable coding agent can build the entire tool from it, or that you can implement by hand. Every constant, every prompt, every threshold, and every failure mode is written down, along with *why* it is that way.

```
┌──────────────────────────────────────────────────────────────┐
│  What it builds:                                             │
│                                                              │
│  Hold Ctrl+Super ....... speak your primary language         │
│  Hold Ctrl+Shift ....... speak your second language          │
│  Double-tap either ..... latch it and keep talking           │
│                                                              │
│  Text lands at the caret, in any X11 application,            │
│  about 450 ms after you stop talking.                        │
└──────────────────────────────────────────────────────────────┘
```

## How to use this

```
1. Download voxflow-4linux-blueprint.md
2. Give it to a coding agent — Claude Code, Cursor, Aider, or any
   LLM with file access — and say: "build this"
3. It interviews you first (languages, chords, cloud or fully
   offline, install location), then builds and verifies the tool
```

The blueprint opens with an **interview phase**: the agent asks you six questions before writing any code, each with a sensible default. It walks you through getting a free API key if you want the cloud path, or configures a fully offline build if you don't. Nothing is left as "configure as appropriate" — every decision is either locked in or explicitly handed to you with the tradeoffs spelled out.

You can also just read it. It is written to be understood, not only executed.

## What the resulting tool does

- **System-wide dictation.** Terminal, editor, browser, chat client — anywhere you can paste.
- **Two languages, two keys.** A different chord for each — and only two, deliberately. No mode switching, no auto-detect guessing wrong on a short sentence.
- **Hands-free latch.** Double-tap either chord and it keeps recording until you tap again, for the passages you don't want to hold a key through.
- **Live caption** while you speak, so you know the microphone is working before you finish the sentence.
- **Context-aware cleanup.** A small LLM strips fillers and false starts, matching its tone to the app you are dictating into — terse in a terminal, complete sentences in an email client. Deadline-bounded to 1.2 s and skipped entirely for short phrases.
- **Personal dictionary** for your jargon, project names and acronyms.
- **Works offline.** No key or no network falls back to a local Whisper model automatically.
- **Audio never touches the disk.** Not once, not as a temp file.
- **No wake word.** The microphone opens only while a key is physically held.

**Requires:** Linux with an **X11** session (not Wayland), Python 3.11+, a microphone.

## Why publish a spec instead of the code

Because the spec is the part that was actually hard.

Anyone can wire up a speech-to-text API. What takes weeks is discovering that voice-activity detection has to run *before* gain or it classifies your noise floor as speech; that a silent clip sent to a speech model returns a confident sentence nobody said; that `xclip` forks a child which takes clipboard ownership *after* the parent exits, so you have to poll before pasting; that Tk blocks Python's signal handlers until you give it a heartbeat; that right Alt is AltGr on most of the world's keyboard layouts and must be excluded from every chord; that an undo key bound to a letter fires hundreds of times under autorepeat and succeeds twice.

All of that is in here, each with the symptom that led to it. A code dump would hide those decisions inside implementation. A specification makes them the point.

## What's in the blueprint

| Section | Contents |
|---|---|
| Interview | six questions to answer before building, with defaults |
| Architecture | data flow diagram, and the key design decisions with rejected alternatives |
| Constants | every tuned value, with the empirical basis for the ones that were measured |
| Stack | each dependency, its exact role, and the one version pin that matters |
| Functional spec | hotkeys and latch, audio pipeline, speech-to-text routing, dictionary, cleanup prompts (verbatim), injection, overlay, captions, history |
| Build phases | 0-9, starting with a mandatory environment gate that halts on Wayland |
| Edge cases | 24 boundary conditions and their exact handling |
| Verification | 18-point checklist to confirm the build actually works |
| Appendix A | running over RDP or a remote desktop, and the audio pitfalls unique to it |
| Appendix B | troubleshooting "it got worse" — almost always the mic level, not the model |
| Appendix C | findings already measured and ruled out, so you don't repeat the work |

## Provenance

This blueprint documents a working tool that has been in daily use since September 2026 — it is a description of something real, not a design sketch. The measurements quoted in it (latency percentiles, voiced-audio ratios, peak amplitudes, model benchmark results) are from that build.

## License

**[Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International](LICENSE)** (CC BY-NC-SA 4.0).

You may read, share, translate and adapt this document freely. Three conditions:

- **Attribution** — credit Rodrigo Cossi and link back to this repository.
- **NonCommercial** — you may not sell it or use it for commercial advantage. No paid courses, no ebooks, no paywalled republication.
- **ShareAlike** — if you adapt or build on the document, distribute your version under this same license.

**The software you build from these instructions is your own work.** These terms cover the document — the writing, structure and explanations — not the tool it describes. Build it, use it at work, use it commercially; that is what the blueprint is for.

Copyright © 2026 Rodrigo Cossi

---

*Written by [Rodrigo Cossi](https://github.com/RodrigoCossi) · [LinkedIn](https://www.linkedin.com/in/rodrigo-cossi-867679162/)*
