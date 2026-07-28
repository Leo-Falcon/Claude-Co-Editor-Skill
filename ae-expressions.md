# After Effects Expression Library

Copy-paste ready. All tested against the **JavaScript** expression engine
(Project Settings > Expressions > JavaScript). Where the legacy ExtendScript
engine behaves differently, it is noted.

Hand these over with the exact property they go on and the exact values to
change. Never hand over an expression without saying which property it lives on.

---

## 1. Inertial bounce (the one you will use most)

**Property:** any transform property with at least 2 keyframes
**Effect:** the property overshoots and settles after the last keyframe

```js
amp = 0.12;
freq = 2.2;
decay = 4.0;

n = 0;
if (numKeys > 0) {
  n = nearestKey(time).index;
  if (key(n).time > time) n--;
}

if (n > 0) {
  t = time - key(n).time;
  v = velocityAtTime(key(n).time - thisComp.frameDuration / 10);
  value + v * amp * Math.sin(freq * t * 2 * Math.PI) / Math.exp(decay * t);
} else {
  value;
}
```

**Tuning:**
- `amp` 0.05 = restrained, 0.12 = default, 0.25 = cartoon
- `freq` 1.5 = heavy object, 2.2 = default, 4 = light and fast
- `decay` 2 = long ring-out, 4 = default, 8 = one bounce and done

For a stat or number hitting hard: `amp 0.18, freq 3, decay 6`.
For a calm conceptual reveal: `amp 0.06, freq 1.8, decay 5`.

---

## 2. Overshoot from a single keyframe pair (spring)

**Property:** Scale, on a two-keyframe move
Cleaner than inertial bounce when you want one crisp overshoot.

```js
n = 0;
if (numKeys > 0) {
  n = nearestKey(time).index;
  if (key(n).time > time) n--;
}
if (n > 0 && n == numKeys) {
  t = time - key(n).time;
  amp = 22;
  freq = 3.0;
  decay = 7.0;
  value + amp * Math.sin(freq * t * 2 * Math.PI) / Math.exp(decay * t);
} else {
  value;
}
```

---

## 3. Auto fade in / out (no keyframes)

**Property:** Opacity
Respects layer in/out points, so trimming the layer retrims the fade.

```js
fadeInFrames = 6;
fadeOutFrames = 8;

fIn  = timeToFrames(time - inPoint);
fOut = timeToFrames(outPoint - time);

Math.min(
  linear(fIn, 0, fadeInFrames, 0, 100),
  linear(fOut, 0, fadeOutFrames, 0, 100)
);
```

---

## 4. Camera shake rig

**Property:** Position of a Null, with the camera or comp parented to it
Use a null. Never wiggle the camera directly, you lose the ability to keyframe
the real move underneath.

```js
amp  = 14;   // pixels
freq = 2.6;  // shakes per second
seedRandom(index, true);
wiggle(freq, amp);
```

Add Z rotation shake on the same null:

```js
seedRandom(index + 100, true);
wiggle(2.2, 1.4);
```

**Handheld feel** (slow drift, not shake): `amp 6, freq 0.6`
**Impact shake** (decays after a marker): see #9.

`seedRandom(index, true)` is what stops every wiggled layer moving identically.
Without it, five shaking layers all shake in lockstep and it reads as fake.

---

## 5. Loops

**Property:** any keyframed property

```js
loopOut("cycle");                  // repeat forever
loopOut("pingpong");               // back and forth
loopOut("offset");                 // continue accumulating (great for scroll)
loopOut("continue");               // maintain final velocity in a straight line
loopOut("cycle", 2);               // loop only the last 2 keyframes
loopIn("cycle");                   // same, before the first keyframe
loopOutDuration("cycle", 1.5);     // loop the last 1.5 seconds
```

`loopOut("offset")` on Position is how you build an infinite scrolling
background from two keyframes.

---

## 6. Stagger a group of layers off one control layer

**Property:** any property, on every layer in the stack
Animate one layer named `CTRL`, every other layer follows with an offset.

```js
delayFrames = 2;
d = (index - 1) * delayFrames * thisComp.frameDuration;
thisComp.layer("CTRL").transform.position.valueAtTime(time - d);
```

Swap `transform.position` for whatever property you are driving.
This is the single highest ratio of perceived production quality to effort in
the entire list. Simultaneous animation reads as templated. Staggered does not.

---

## 7. Anchor point locked to text bounds

**Property:** Anchor Point on a Text layer
Text layers scale from the baseline by default, which looks wrong. This centers
the anchor on the actual glyphs, live, even as the text changes.

```js
r = thisLayer.sourceRectAtTime(time, false);
[r.left + r.width / 2, r.top + r.height / 2];
```

Use `sourceRectAtTime(time, true)` to include extents (strokes, shadows).

---

## 8. Number counter with comma formatting

**Property:** Source Text on a Text layer
**Setup:** add a Slider Control named `Count` to the same layer, keyframe it

```js
n = Math.round(effect("Count")("Slider").value).toString();
out = "";
for (i = 0; i < n.length; i++) {
  out += n.charAt(i);
  rem = n.length - i - 1;
  if (rem > 0 && rem % 3 === 0) out += ",";
}
out;
```

Written manually rather than using `toLocaleString()` so it behaves identically
on both expression engines.

For a percentage: replace the last line with `out + "%"`.
For currency: `"$" + out`.

---

## 9. Impact shake triggered by layer markers

**Property:** Position on a null
Add a marker (`*` on numpad) at every impact frame. The shake fires and decays
from each one.

```js
amp = 30;
freq = 4.0;
decay = 9.0;

n = 0;
if (marker.numKeys > 0) {
  n = marker.nearestKey(time).index;
  if (marker.key(n).time > time) n--;
}

if (n > 0) {
  t = time - marker.key(n).time;
  seedRandom(index, true);
  offset = (wiggle(freq, amp, 1, 0.5, time) - position);
  value + offset / Math.exp(decay * t);
} else {
  value;
}
```

This is the correct way to sync shake to a kick or an SFX hit. Manual keyframes
for this are a waste of your life.

---

## 10. Audio-reactive scale

**Setup:** select the audio layer, Animation > Keyframe Assistant > Convert
Audio to Keyframes. This creates a layer called `Audio Amplitude`.

**Property:** Scale

```js
a = thisComp.layer("Audio Amplitude").effect("Both Channels")("Slider");
base = 100;
sens = 0.35;
s = base + a.value * sens;
[s, s];
```

Clamp it so a loud transient does not blow the layer off screen:

```js
a = thisComp.layer("Audio Amplitude").effect("Both Channels")("Slider");
s = 100 + Math.min(a.value * 0.35, 22);
[s, s];
```

---

## 11. Time-offset a precomp against itself

**Property:** Time Remap (enable with Ctrl/Cmd+Alt+T)
For duplicate layers that should not be in sync.

```js
offset = (index - 1) * 0.12; // seconds per layer
time - offset;
```

---

## 12. Motion blur that follows velocity (fake, but cheap)

**Effect:** Directional Blur > Blur Length
**Property:** Blur Length

```js
v = length(thisLayer.transform.position.velocity);
v * 0.02;
```

**Effect:** Directional Blur > Direction

```js
vel = thisLayer.transform.position.velocity;
radiansToDegrees(Math.atan2(vel[0], -vel[1]));
```

Use this instead of real motion blur when real motion blur is killing your
render times and the move is fast enough that nobody will scrutinise it.

---

## 13. Wiggle only between two times

**Property:** any

```js
startT = 1.0;
endT   = 2.4;
if (time > startT && time < endT) {
  seedRandom(index, true);
  wiggle(4, 20);
} else {
  value;
}
```

---

## 14. Snap a value to whole frames (kills sub-pixel softness)

**Property:** Position

```js
[Math.round(value[0]), Math.round(value[1])];
```

Use on UI/HUD elements and text that must stay crisp. Do not use on anything
that moves slowly, it will judder.

---

## 15. Link a property to another comp's layer

**Property:** any

```js
comp("PRECOMP NAME").layer("LAYER NAME").transform.scale;
```

---

## Debugging expressions

- Red triangle on the layer = expression error. Click it, read the actual text.
- `numKeys` is 0 if you forgot to keyframe. Half of all inertial bounce
  failures are this.
- `thisComp.layer("X")` fails silently on a rename. Renaming a control layer
  breaks every expression pointing at it.
- Alt+click the stopwatch to add/remove an expression.
- To bake an expression into keyframes: right-click the property >
  Keyframe Assistant > Convert Expression to Keyframes. Do this before handing
  a project to someone else.
- Expressions evaluate every frame on every layer. 200 layers with expressions
  is a real render cost. Bake them before final render on heavy comps.
