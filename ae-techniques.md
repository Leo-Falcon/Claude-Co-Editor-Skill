# After Effects Techniques

Operation chains, in order, with the settings that actually matter. Hand these
over as steps, not as concepts.

---

## Motion tracking (2D)

**Use when:** attaching text or a graphic to something moving in frame.

1. Select the footage layer. Window > Tracker. Click **Track Motion**.
2. Choose the track type:
   - **Position** — object moves, does not rotate or scale
   - **Position + Rotation** — object tilts
   - **Position + Scale** — object moves toward/away from camera
   - **Perspective corner pin** — flat surface (screen, sign, wall)
3. Place the track point on a **high-contrast corner**, not a flat area.
   Inner box = the feature. Outer box = how far it can move per frame.
4. Sizing rule: inner box just big enough to contain a unique pattern, outer
   box big enough to cover the fastest single-frame movement in the shot.
5. Options > Channel: use **Luminance** for most shots, **RGB** if the feature
   is defined by color not brightness, **Saturation** for a colored marker.
6. Enable **Subpixel Positioning**. Adaptive Feature On.
7. Track forward. Watch it. Stop the moment it drifts.
8. When it drifts: move the playhead back to the last good frame, reposition
   the track point manually, continue tracking. Do not let it run through a
   bad section and fix it later.
9. Edit Target > pick the null. Apply > X and Y.

**Always track to a null, never directly to the graphic.** The graphic parents
to the null. This keeps your ability to offset, scale, and animate the graphic
independently of the track.

**When 2D tracking fails:** motion blur on the feature, feature leaves frame,
feature changes shape. Switch to Mocha.

---

## Mocha AE (planar tracking)

**Use when:** the feature is a surface, or 2D point tracking failed.

1. Effect > Boris FX Mocha > Mocha AE. Click the Mocha button in Effect Controls.
2. Draw an X-spline around a **planar region** (a flat surface that moves as one
   unit). Do not draw around the whole object.
3. Turn off **Scale, Shear, Perspective** in the track parameters unless the
   surface actually does those things. Fewer degrees of freedom = more stable.
4. Track forward. Scrub back and check the surface overlay for slipping.
5. Use the **AdjustTrack** module if it slips: pick a corner, correct it, Mocha
   redistributes.
6. Export: Track Data > Copy to After Effects (Corner Pin, or Transform).
   Paste onto a null.

**Planar means planar.** A face is not planar. A t-shirt is not planar. Track
the flat part and offset from it.

---

## Warp Stabilizer

**Use when:** handheld footage needs to sit still, or a whip needs smoothing.

1. Effect > Distort > Warp Stabilizer VFX. Let the analysis finish.
2. Result:
   - **Smooth Motion** — keeps the camera move, removes the jitter. Default.
   - **No Motion** — locks it off entirely. Only for a shot that was meant to
     be static.
3. Smoothness: start at 20%. Every 10% you add costs you frame edges and adds
   warp. 50% is usually too much.
4. Method, in ascending order of aggression:
   - **Position** — cheapest, safest
   - **Position, Scale, Rotation**
   - **Perspective**
   - **Subspace Warp** — default, best results, most artifacts
5. Framing: **Stabilize, Crop, Auto-Scale**. Watch the Auto-Scale value. Over
   115% and you are visibly softening the shot.
6. Borders > Advanced > Detailed Analysis if the track is failing on a
   low-contrast shot.

**Failure mode:** rubbery warping on straight lines and faces. Fix by dropping
Method to Position/Scale/Rotation, or by lowering Smoothness. Never fix it by
adding more stabilization.

**Pre-stabilization rule:** stabilize before you track anything to the shot.
Stabilizing after you have tracked invalidates the track.

---

## 3D Camera Tracker

**Use when:** placing an element into a scene with real parallax.

1. Effect > Perspective > 3D Camera Tracker. Analysis runs in background.
2. **Advanced > Detailed Analysis** if the solve error is above 1.0 pixels.
   Below 1.0 is good. Below 0.5 is excellent. Above 2.0, the solve is unusable.
3. Set **Shot Type** if you know it. If the shot has no parallax at all
   (pure pan from a tripod), the tracker cannot solve it. This is a physics
   limitation, not a settings problem.
4. Hover over the track points until you see a red target. Select 3+ points on
   the same surface, right-click > **Create Null and Camera**, or
   **Create Text and Camera**.
5. Ground plane: select 3 points on the floor, right-click > Set Ground Plane
   and Origin. Do this before creating anything or your world axis is arbitrary.
6. Parent your element to the null. Do not animate the null.

**When the solve fails:**
- No parallax (tripod pan) → cannot solve, use Mocha planar instead
- Rolling shutter skew → apply Rolling Shutter Repair first
- Too much motion blur → track fewer frames, or shoot at a higher shutter
- Moving subject dominates frame → mask the subject out, then solve

---

## Rotoscoping

**Roto Brush 3** (AE 2023+) is the default. Use it first.

1. Double-click the layer to open the Layer panel.
2. Roto Brush tool (Alt+W). Paint a stroke down the **middle** of the subject,
   not along the edge.
3. Alt+drag to subtract areas.
4. Arrow forward one frame at a time. Correct with short strokes as it drifts.
   Do not scrub ahead and hope.
5. Refine Edge tool for hair and soft edges. Paint over the fuzzy region only.
6. Freeze the propagation when done (the Freeze button) so it caches.
7. Alt+click the matte icon to check the alpha in black and white. This is
   where you find the chatter you cannot see in the composite.

**Manual mask roto**, when Roto Brush is not clean enough:
- Fewer points. A 12-point mask animates cleanly. A 60-point mask chatters.
- Keyframe on 2s or 3s and let AE interpolate, then correct. Keyframing every
  frame produces jitter, not accuracy.
- Mask Feather 1.5 to 3px for a hard-edged subject. Use the Feather tool
  (variable-width feather) where the edge softness changes along the shape.
- Motion blur: roto the shape as if it were sharp, then add Pixel Motion Blur.

---

## Speed ramps in AE

Premiere is better at simple ramps. Use AE when you need frame blending
quality or the ramp is tied to a graphic.

1. Enable Time Remapping: Ctrl/Cmd+Alt+T.
2. Set keyframes at the ramp in and out points.
3. Move the middle keyframe to change speed between them.
4. Select all Time Remap keyframes > F9 (Easy Ease), then open the graph editor
   (Shift+F3) and shape the **value** graph, not the speed graph.
5. Frame blending: enable on the layer (the two-box icon) and on the comp
   (the Enable Frame Blending toggle in the timeline header). Set the layer to
   **Pixel Motion** for slow-mo, **Frame Mix** for anything else.

**Pixel Motion warps on:** motion blur, occlusion (things crossing in front of
each other), and low-contrast areas. If it artifacts, drop to Frame Mix and
accept the judder, or reshoot at a higher frame rate.

---

## HUD / UI graphic construction

The technique family behind most "modern tech edit" looks.

1. Build all elements in a **precomp at 1:1 scale**, then scale the precomp.
   Building at final scale means you cannot reuse it.
2. Shape layers, not solids. Shape layers scale without softening and their
   stroke width is animatable.
3. Stroke animation: add **Trim Paths** to the shape group, keyframe End 0 to
   100. Offset staggers multiple strokes.
4. Corner brackets: rectangle with a stroke, no fill, plus 4 rectangular masks
   to knock out the middle of each side. Or Trim Paths with Start/End set to
   show only the corners.
5. Scan-line / grid: use a single line, then **Repeater** in the shape group.
   Never duplicate layers for this.
6. Glow: do not use the Glow effect on the layer directly. Duplicate the layer,
   Fast Box Blur the duplicate 12-20px, set to **Add** at 40-60% opacity, put
   it underneath. Cheaper to render and far more controllable.
7. Chromatic aberration: duplicate 3x, set channels to R / G / B via the
   Channel Mixer or Set Channels, offset R and B by 1-3px in opposite
   directions.

**Readability check:** downscale the final frame to 200px wide. If the HUD is
noise at that size, it is noise on a phone.

---

## Cleanup

**Removing an object:**
1. Content-Aware Fill (Window > Content-Aware Fill) for a static-ish shot.
   Mask the object, set Alpha Expansion 4-8, Fill Method **Object** for a
   moving object, **Surface** for a texture, **Edge Blend** for a flat area.
2. If CAF fails: clone from a clean plate. Track a null to the background,
   parent a duplicate layer with an offset, mask it over the object.

**Fixing a blown highlight:** you cannot recover 8-bit clipped data. You can
only make it less offensive. Curves to roll the shoulder, then a subtle
gradient overlay in the clipped region. Say this out loud when it comes up
rather than pretending a fix exists.

**Denoise:** Remove Grain effect is slow and mushy. Neat Video if available.
Otherwise, denoise in Resolve during the grade, not in AE.

---

## Render performance

In order of impact:

1. **Purge and reduce layer count.** Precomp finished sections and collapse.
2. **Turn off effects you are not looking at.** The fx toggle in the timeline.
3. **Motion blur off during work**, on for final. It is the single most
   expensive toggle.
4. **Multi-Frame Rendering** is on by default in modern AE. Give AE more RAM in
   Preferences > Memory, leaving 6GB for the OS.
5. **Disk cache** on a fast drive, not the same drive as your footage.
6. Do not use **Adjustment Layers** across the whole comp for effects that only
   need to affect one layer. An adjustment layer rasterizes everything below it.
7. Effects that are render killers: Glow, Camera Lens Blur, Turbulent Displace
   at high complexity, Particular with high particle counts, CC Force Motion
   Blur. Precomp and pre-render these.
8. Final render: **Render Queue for AE-only output**, Media Encoder if you are
   batching. Render to a lossless intermediate (QuickTime, Apple ProRes 422 HQ
   or 4444 if you need alpha), then compress separately. Never render straight
   to H.264 out of AE for anything you will further edit.
