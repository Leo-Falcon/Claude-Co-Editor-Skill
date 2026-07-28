# Footage Constraints — Canon EOS 60D

Know the ceiling before promising a fix. Half of all "can you fix this in post"
answers are determined by what the camera physically recorded.

---

## What the 60D actually records

| Mode | Resolution | Frame rates |
|---|---|---|
| Full HD | 1920x1080 | 30 / 25 / 24 fps |
| HD | 1280x720 | 60 / 50 fps |
| SD | 640x480 | 30 / 25 fps |

Codec: H.264, **.MOV** container, 8-bit, **4:2:0** chroma subsampling.
Sensor: APS-C CMOS, 18MP, 1.6x crop.

---

## The consequences, in post

### No 1080p slow motion
60fps only exists at 720p. If you want a 50% slow-mo shot at 1080, your options
are:

1. Shoot 720p60 and upscale. Softer, but the motion is real. Usually the right
   call for a fast action beat where softness is masked by movement.
2. Shoot 1080p30 and use Optical Flow / Pixel Motion to interpolate. Sharp, but
   the interpolation artifacts on motion blur and occlusion.
3. Don't slow it down. Use a speed ramp that goes fast, not slow.

**Say this out loud when a slow-mo request comes up.** Do not silently
recommend Optical Flow as if it were free.

### 8-bit grading latitude
8-bit means 256 values per channel. Log-style flat profiles do not help much
here because you have no bit depth to redistribute.

- **Shoot Neutral picture style**, contrast -2, sharpness 0, saturation -1.
  Not a flat/log profile. On 8-bit, a flat profile costs you more in banding
  than it gains you in latitude.
- Recoverable highlight range: roughly 1/3 to 2/3 of a stop. Clipped is gone.
- Recoverable shadow range: roughly 1 stop before noise and banding dominate.
- Grade in 32-bit float even though the source is 8-bit, so rounding errors do
  not compound between operations.

### 4:2:0 chroma and keying
Green screen on a 60D is possible but not pleasant. Chroma resolution is a
quarter of luma resolution, so edges will be blocky.

- Light the screen evenly and separate the subject by at least 1.5m.
- Key in AE with **Keylight 1.2**: Screen Colour eyedropper, then View >
  Status to check the matte, then Screen Gain / Screen Balance, then Clip Black
  and Clip White in the Screen Matte section.
- Add **Advanced Spill Suppressor** and a 1px Matte Choke.
- Do not expect a clean hair edge. Frame to avoid needing one.

### Rolling shutter
The 60D has significant rolling shutter. Fast pans and handheld whips will skew
vertical lines.

- AE: Effect > Distort > **Rolling Shutter Repair**. Rolling Shutter Rate
  starts at 50%, adjust until verticals are vertical. Method: Warp for most
  shots, Pixel Motion if Warp artifacts.
- Do this **before** stabilizing or tracking. Stabilizing skewed footage bakes
  the skew in.

### Long-GOP H.264 playback
The 60D's H.264 is long-GOP, meaning most frames are reconstructed from
neighbours. This is why scrubbing is painful even on a fast machine. It is not
your computer. Make proxies.

### Moiré and aliasing
The 60D line-skips to read out video, producing moiré on fine patterns
(fabric, brick, screens, fences). There is no reliable post fix.

- Prevention: avoid fine repeating patterns in wardrobe and background, or
  throw the background out of focus.
- Partial fix: mask the affected area, add a very slight blur plus a
  desaturation of the false color. It never fully goes away.

### Audio
The 60D's internal audio preamp is poor. Assume all usable audio is recorded
externally and needs syncing.

- Sync in Premiere: select both clips > right-click > **Synchronize** > Audio.
- If the waveforms will not match (external recorder at a different level),
  clap sync manually against the transient.
- See the video-editor skill's audio-sync reference for the full procedure.

---

## The general rule

When a fix is not possible, say it is not possible in one line, then give the
best available mitigation and name what it costs.

**Bad:** "You could try Optical Flow, or maybe warp stabilizer, or shoot again."

**Good:** "That highlight is clipped, 8-bit has nothing to recover. Best you get
is rolling the shoulder with a curve so it stops reading as a hard white blob.
It'll look intentional, not fixed. Takes 30 seconds."
