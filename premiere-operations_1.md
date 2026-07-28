# Premiere Pro Operations

Keyboard-level. Give the keys, not the menu path, unless the menu path is the
only route. Windows shortcuts listed; Mac substitutes Cmd for Ctrl and Opt for
Alt.

---

## Project setup for 9:16 short-form

1. New Sequence > Settings tab (do not use a preset).
2. Editing Mode: **Custom**. Frame Size **1080 x 1920**. Timebase **30fps**.
   Pixel Aspect Ratio **Square Pixels**. Fields **No Fields**.
3. Video Previews: **QuickTime / Apple ProRes 422**. Preview size matching
   sequence. This makes your previews actually usable.
4. Audio: 48000 Hz, Stereo.
5. Save it as a preset the first time so you never do this again.

**Bin structure**, made once at project creation:
```
01_FOOTAGE   02_AUDIO   03_MUSIC   04_SFX   05_GFX   06_AE_COMPS   07_EXPORTS
```
Reorganizing bins mid-edit is procrastination. Set it up in 20 seconds at the
start and never touch it again.

---

## Assembly

| Key | Action |
|---|---|
| `J K L` | Shuttle back / stop / forward. Tap L repeatedly to speed up. |
| `I` / `O` | Set in / out point |
| `,` | Insert (ripples the timeline) |
| `.` | Overwrite |
| `F` | Match frame (find the source clip at this frame) |
| `\` | Zoom timeline to fit sequence |
| `+` / `-` | Zoom in / out on timeline |
| `Ctrl+K` | Add edit at playhead, targeted tracks |
| `Ctrl+Shift+K` | Add edit at playhead, all tracks |
| `Shift+Delete` | Ripple delete |
| `Q` | Ripple trim previous edit to playhead |
| `W` | Ripple trim next edit to playhead |
| `Shift+E` | Enable / disable clip |
| `Alt+drag` | Duplicate a clip |
| `Alt+click` | Select video or audio of a linked clip independently |
| `M` | Add marker |
| `Shift+M` | Jump to next marker |
| `Ctrl+D` | Apply default video transition |
| `Ctrl+Shift+D` | Apply default audio transition |
| `Ctrl+R` | Speed / Duration dialog |
| `R` | Rate Stretch tool |
| `N` | Rolling edit tool |
| `B` | Ripple edit tool |
| `Y` | Slip tool |
| `U` | Slide tool |
| `T` | Type tool |
| `Ctrl+M` | Export |

**Q and W are the two most underused keys in Premiere.** Park the playhead
where the cut should be, hit Q or W, done. No dragging, no snapping fights.

---

## The assembly pass, in order

1. **Radio edit first.** Cut the audio only. Get the story right with no
   picture decisions. Every good short-form edit is an audio edit first.
2. Mark every line break with `M` while listening back at 1x.
3. Lay picture against the locked audio.
4. Only then trim for rhythm.

Doing picture first means you will protect shots you love that the story does
not need. This is the most common reason a technically clean edit feels flat.

---

## Speed ramps in Premiere

1. Right-click the clip > **Show Clip Keyframes > Time Remapping > Speed**.
   (Or click the fx badge dropdown on the clip.)
2. Ctrl+click on the speed rubber band to add a keyframe. This creates a
   two-handle split keyframe.
3. Drag the two halves apart horizontally. The gap is the ramp duration.
4. Drag the blue Bezier handle in the gap to shape the acceleration curve.
5. Raise or lower the band between keyframes to set the speed.
6. Clip > Video Options > **Time Interpolation**:
   - **Frame Sampling** — duplicates frames, cheapest, judders on slow-mo
   - **Frame Blending** — cross-dissolves frames, mushy but smooth
   - **Optical Flow** — generates new frames, best for slow-mo, artifacts on
     motion blur and occlusion. Requires a render to evaluate honestly.

**Do not judge Optical Flow from the timeline preview.** Render the section
(Enter key) and then look.

---

## Captions

1. Window > Text > **Captions** tab > Create Transcription.
2. Transcribe > choose language and speaker labeling. Wait.
3. **Captions > Create captions from transcript.** Set the style:
   - Max characters per line: 20-24 for vertical
   - Max lines: 1 or 2, never 3
   - Min duration: 0.8s
4. Style the caption track via the Essential Graphics panel > Track Style.
   Create a Track Style so every caption updates at once.
5. Export the transcript as SRT (Captions panel > ... > Export) if you want
   word timings for a Remotion build.

**For word-level timings** (needed for kinetic captions), Premiere's SRT is
line-level and not good enough. Run the audio through WhisperX or similar for
word-level JSON, then build the captions in Remotion.

---

## Proxies

Canon 60D H.264 files are surprisingly painful to scrub because H.264 is
long-GOP. Proxies fix this.

1. Select clips in the Project panel > right-click > **Proxy > Create Proxies**.
2. Format: **QuickTime**, Preset **ProRes Proxy** or **GoPro CineForm**.
3. Resolution: half.
4. Toggle the Proxy button in the Program monitor (add it via the + button in
   the button editor if it is not there).
5. Premiere automatically relinks to full res on export. Verify this on the
   export settings screen before you render.

---

## Round-trip to After Effects

**To AE:** select clips in the timeline > right-click > **Replace With After
Effects Composition**. Premiere creates the comp, links it, and updates live.

**Do not** use Dynamic Link for heavy comps. It will make Premiere unusable.
For anything with 3D, particles, or heavy effects: render the AE comp to
ProRes 4444 (for alpha) or ProRes 422 HQ, and import the file.

**To Resolve for grade:** File > Export > **Final Cut Pro XML** (or AAF).
Better path for short-form: render a flattened ProRes 422 HQ master out of
Premiere, grade that single file in Resolve, and bring the graded master back
for the final export. Round-tripping a 30-second reel through XML is more
failure surface than it is worth.

---

## Export settings for Reels / Shorts / TikTok

Format: **H.264**
Preset: start from Match Source - High bitrate, then override:

- Frame size 1080x1920, 30fps
- Profile: **High**, Level 4.2
- Bitrate encoding: **VBR, 2 pass**
- Target bitrate: **12 Mbps**, Maximum **16 Mbps**
- Key frame distance: leave automatic
- Render at Maximum Depth: **on**
- Use Maximum Render Quality: **on** (only matters if you are scaling)
- Audio: AAC, 320 kbps, 48 kHz

Every platform re-encodes. You cannot control that. What you can control is
giving them a clean source, which means: no banding in gradients, no
compression damage already baked in, and correct color.

**Banding fix:** add a very subtle noise layer (Noise effect, 1.5-2.5%,
Use Color Noise off) over gradients before export. Grain hides banding from
the encoder. This is not optional on dark cinematic gradients.

---

## Audio, the part most editors under-serve

1. **Room tone under everything.** Cutting between clips with different silence
   is audible and reads as amateur even to people who cannot name why. Lay a
   continuous room tone bed under the whole timeline.
2. **Levels:** dialogue peaking around -6 dB, averaging -12 to -16 LUFS.
   Music ducked to -18 to -22 dB under dialogue.
3. **Essential Sound panel:** tag the clip as Dialogue > Loudness > Auto-Match.
   Then DeNoise and DeReverb only as much as needed. Over-denoised dialogue
   sounds underwater and cannot be undone.
4. **Ducking:** tag music as Music > Ducking > Duck against Dialogue >
   Generate Keyframes. Then hand-correct the keyframes, because the automatic
   ones are always slightly late.
5. **Silence is a sound.** A hard cut to complete silence before a reveal is
   more powerful than any riser. Use it.
6. **Impacts** should land on the frame of the cut, not one frame after. Zoom
   in and check the transient against the cut point.

---

## Common failure diagnoses

| Symptom | Cause | Fix |
|---|---|---|
| Export looks softer than timeline | Scaling without Max Render Quality | Enable it, or match source resolution |
| Export looks darker/washed | Color space mismatch, usually Rec709 vs sRGB | Sequence Settings > Color Management, or export a test and compare in VLC |
| Red bar over timeline that will not go away | Effects requiring render | Enter to render, or ignore it, it does not affect export |
| Audio out of sync after export | Mixed frame rates or variable frame rate source | Conform the VFR file first (HandBrake to CFR), then re-import |
| Premiere crashes on a specific clip | Corrupt media or an unsupported codec variant | Transcode that clip to ProRes and relink |
| Clip is offline after moving files | Path broken | Right-click > Link Media, point at the folder, Locate |
| Playback stutters but the machine is idle | Long-GOP H.264 source | Proxies |
