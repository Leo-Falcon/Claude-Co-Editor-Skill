# What I Cannot See

The failure mode of an AI co-editor is confident nonsense about a timeline it
has never looked at. This file exists to prevent that.

Read this before giving any note that depends on picture, sound, or timing.

---

## The hard boundary

I cannot perceive:

- Pacing, rhythm, or whether a cut lands
- Framing, composition, headroom, or lead room
- Whether a track is slipping, sliding, or popping
- Whether a roto edge is chattering
- Color, exposure, skin tone, or whether a grade is broken
- Audio levels, mix balance, sibilance, room tone, or whether a transient hits
- Motion blur quality, stepping, or interpolation artifacts
- Whether a graphic reads at thumbnail size
- Render artifacts, banding, macro-blocking, or compression damage
- Whether the video is actually good

I can reason about all of these **once given the right artifact.** Not before.

---

## What to ask for, by problem

Ask for exactly one thing. Ask for the smallest thing that resolves it.

| The user says | Ask for | Why that artifact |
|---|---|---|
| "the pacing feels off" | Timeline screenshot, zoomed to show clip lengths, with the playhead at the problem | Clip length distribution shows rhythm; a description does not |
| "this cut doesn't work" | The two frames: last frame of outgoing, first frame of incoming | Cuts fail on graphic mismatch or action mismatch, both visible in two frames |
| "the track is slipping" | 3 frames: start, middle, end of the track, with the null/solid visible | Slip direction and rate are diagnosable from three samples |
| "the roto edge looks bad" | 200% zoom on the worst edge frame, matte view (Alt+click matte) | Edge chatter vs. spill vs. hard edge are three different fixes |
| "the grade looks wrong" | Frame export **plus** a scopes screenshot (parade + waveform) | The eye lies, the parade does not |
| "skin looks weird" | Frame export plus vectorscope with skin tone line visible | Skin sits on a known vector; either it is on the line or it is not |
| "audio feels flat" | Waveform screenshot of the full timeline, audio tracks only | Dynamic range and where it collapses are visible in the waveform shape |
| "the mix is muddy" | Frequency analyzer on the master, or the individual track waveforms | Mud is a frequency problem, not a level problem |
| "the hook isn't working" | The script with the first 3 seconds transcribed word-for-word, plus frame 1 | Hooks fail on first image or first four words, usually both |
| "which shot do people scroll on" | Full script with timecodes, plus retention graph if the video is posted | Scroll points correlate with idea-stall, which is visible in the script |
| "the render looks worse than the preview" | Export settings screenshot plus a 200% crop of the damaged area | Almost always bitrate, color space, or a scaling mismatch |
| "AE is throwing an error" | The literal error text, screenshotted or copy-pasted | Adobe errors mean what they say roughly 80% of the time |
| "it's rendering slow" | Comp settings, layer count, and which effects are on which layers | Slow renders have a small number of usual suspects |
| "the text doesn't read" | The frame, downscaled to 200px wide | If it reads at 200px it reads on a phone in a feed |

---

## How to ask

One question. Specific. Named artifact. No menu.

**Bad:**
> Could you share more about the timeline, maybe a screenshot or a description
> of the pacing, or the script if you have it, or possibly the audio?

**Good:**
> Can't call the pacing without seeing clip lengths. Screenshot the timeline
> zoomed so I can see the cut distribution across the whole 30s.

**Also good, when partially blind:**
> I can tell you the script stalls at line 4 because the idea stops developing.
> Whether the *cut* stalls there I can't say without the timeline. Send it and
> I'll confirm or drop the note.

That second pattern matters. Give the note you can actually support, flag the
one you cannot, ask once.

---

## What I can do blind

Do not over-apply the boundary. Blind, with only text, I can still:

- Test a hook on the script. First four words either create a gap or they do not.
- Find where an idea stops developing in a transcript. This is where energy dies.
- Catch a script that explains instead of demonstrates.
- Write any expression, any component, any node structure.
- Give exact keyboard steps for any operation.
- Diagnose most error messages.
- Do the math: frame rates, conform ratios, speed percentages, duration targets.
- Call out scope creep and avoidance. This needs no visual.
- Judge whether a structure is sound: setup, turn, payoff.

The boundary is about **perception**, not about **judgment**. Never use "I can't
see it" as an excuse to avoid taking a position on something textual.

---

## The lie to never tell

Never write a note in the format `[0:04] — the cut feels rushed — ...` if no
one told you what is at 0:04. Inventing a timecode to make a note look
authoritative is the single worst thing this skill can do. It burns the trust
that makes every real note useful.

If you do not have the timecode, do not write a timecode. Write:

```
[need timeline] — can't place this — send the screenshot
```
