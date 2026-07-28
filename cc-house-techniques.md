# Creators Club House Techniques

The technique families that define the Creators Club / Keanu Visuals style, and
how to break each one down into executable steps.

This exists so that when the user says "the car edit thing" or "the AR style,"
there is a shared vocabulary and a concrete build path rather than a vague
gesture at a look.

---

## 1. The seamless object transition (the "fly-through" family)

A camera move appears to travel through, into, or past an object and emerge in
a different scene. The signature move of the whole style.

**Build:**
1. Shoot A: push in hard toward the object. Shoot B: pull out from a matching
   position in the new scene. Match the **motion direction and speed** at the
   join, not the framing.
2. In AE, find the frame in A where the object fills the most frame, and the
   frame in B where the corresponding shape fills the most frame.
3. Scale-and-position keyframe A's outgoing scale up past 100% and B's incoming
   scale from above 100% down. The join happens at maximum scale on both.
4. Mask the object's silhouette on A's final frames, track the mask (Mocha if
   the shape is planar-ish, Roto Brush if organic).
5. Add **directional blur** driven by position velocity (see the expression
   library, #12) across the join. The blur is what sells it. Without it, the
   eye catches the seam.
6. Cross-dissolve over 2-4 frames maximum at peak blur.

**The whole trick is that the eye cannot resolve detail during fast motion.**
Everything above is in service of hiding the cut inside a moment of maximum
motion blur. If the join is not fast, it will not work, no matter how clean
the mask is.

**Common failure:** the speeds do not match at the join. A slows to 60% while
B starts at 140% and the brain reads the discontinuity even if it cannot name
it. Match the velocity, not the position.

---

## 2. Car edits

The family that built the style. Speed ramps, tracked graphics, low-angle
movement, hard sound design.

**The pattern:**
- Establish wide, low. Hold longer than feels comfortable. Let the shot earn it.
- Cut on **motion**, not on stillness. The car enters frame, cut. The car exits
  frame, cut.
- Speed ramp INTO the action, not out of it. Ramp down as it approaches, hit
  100% at the moment of impact/pass, then hard cut.
- Track a graphic to the car body with Mocha (a car panel is close enough to
  planar over 20 frames), never with a 2D point track.
- Ground the graphic: add a subtle drop shadow and match the graphic's
  perspective to the surface. An unshaded HUD floating over a car reads as a
  sticker.

**Sound:** the engine is the score. Cut the picture to engine transients. A
gear change is a cut point. This is the single biggest thing that separates a
good car edit from a montage.

---

## 3. AR / augmented reality editing style

Graphics that appear to exist in the 3D space of the shot.

**Build:**
1. 3D Camera Track the shot (see `ae-techniques.md`). Solve error under 1.0.
2. Set the ground plane and origin from three floor points before creating
   anything.
3. Create Null and Camera. Build every graphic as a 3D layer parented to nulls
   positioned in the solved space.
4. **Match the light.** Sample the dominant light direction in the plate and
   put your graphic's brightest edge on that side. This is what makes it sit
   in the scene.
5. **Occlusion sells it more than anything.** If a graphic is behind a real
   object, roto the real object and put it on top. One occluded element does
   more work than ten perfectly tracked ones.
6. Add the plate's grain and chromatic aberration on top of the graphic, not
   under it. Clean graphics over grainy footage always read as composited.

**Order of importance for believability:** occlusion > grain match > light
match > track accuracy. Editors obsess over track accuracy, which is the
least important of the four once it is roughly right.

---

## 4. Talking-head animation edits

Graphics that respond to what the speaker is saying, in sync with the words.

**Build:**
1. Word-level transcript first. Premiere's transcription gets you line-level;
   for word-level, run WhisperX and get JSON with per-word start/end.
2. Beat map: which specific word triggers which graphic. Every graphic ties to
   one word or one concept. If you cannot name the word, cut the graphic.
3. Trigger everything off **layer markers** placed on the word frames, and drive
   the animation with the marker-triggered expressions (`ae-expressions.md` #9).
   Manual keyframing this is a waste of a night.
4. Stagger multi-element reveals off a single CTRL layer (`ae-expressions.md`
   #6).
5. Push in on the talking head very slightly (2-4% over the shot) during
   graphic-free sections so the frame is never fully static.

**The density rule:** heavy motion graphics means depth per sequence, not more
sequences. Three graphics with four animation stages each beats twelve graphics
with one stage each. Every time.

---

## 5. HUD design

See `ae-techniques.md` for the construction chain. The style-specific notes:

- Monospace or a technical grotesque for HUD text. Never a humanist sans.
- Thin strokes, 2-4px at 1080 width. Thick strokes read as a game UI.
- One accent color, one neutral. Two accents and it reads as clip-art.
- Animate the **structure** in with Trim Paths before the **content** fades in.
  Structure first, data second, always.
- Never center a HUD. Off-center with intentional negative space reads as
  designed; centered reads as default.

---

## 6. Where this style goes wrong

Worth naming, because it is where most people in the community land:

- **Technique without a reason.** A tracked HUD on a shot that did not need
  one. Every graphic must illustrate something specific. If you cannot say what
  it illustrates in one sentence, cut it.
- **Same easing everywhere.** Five sequences all using the same spring config
  reads as templated even when every individual animation is well built.
- **Speed ramps on everything.** A ramp is punctuation. Punctuating every word
  is not emphasis, it is noise.
- **Sound as afterthought.** The look is 50% sound design. An edit with perfect
  tracking and default audio is a worse edit than a rough cut with great sound.
- **Copying the reference frame-for-frame.** The techniques are transferable.
  The specific shot is not. Learn the mechanism, apply it to your own footage.
