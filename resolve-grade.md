# DaVinci Resolve — Grade

Node structures and specific moves. Resolve is the grade stage only in this
workflow: a flattened ProRes master comes in, a graded ProRes master goes out.

---

## Project setup

- Color Science: **DaVinci YRGB Color Managed**
- Input Color Space: **Rec.709 Gamma 2.4** for 60D H.264 footage (it is not log)
- Timeline Color Space: **DaVinci WG / Intermediate**
- Output Color Space: **Rec.709 Gamma 2.4**

If you are grading a flattened Premiere master, everything is already Rec709.
Do not treat it as log. Do not apply a log conversion LUT to Rec709 footage,
which is the single most common self-inflicted grading wound.

---

## The default node tree

Build this every time. Serial nodes unless stated.

```
[01 BALANCE] → [02 CONTRAST] → [03 SKIN] → [04 LOOK] → [05 GLOW ⇄ parallel] → [06 OUTPUT]
```

### Node 01 — Balance
Neutralize before you stylize. No creative choices here.

- **Primaries Wheels.** Lift until the darkest real black sits at 0 on the
  waveform. Gain until the brightest real white sits just under 1023 (or 100
  on a percentage scale). Offset for overall exposure.
- Kill any color cast: look at the **Parade**. In a neutral area of the frame,
  R, G, and B traces should sit at the same height. Correct with Offset colour
  wheel first, Lift/Gamma/Gain second.
- Do not clip. Nothing should touch the top or bottom rail of the waveform
  unless it is a specular highlight or a true black.

### Node 02 — Contrast
- **Custom Curve.** A gentle S. Two points: pull down around 25% input, push up
  around 75%. Move each by 5-8%, not 20%.
- Or use Contrast + Pivot in the Primaries panel. Pivot around 0.4 for a
  bright look, 0.3 for a moodier one.
- Check the waveform after. If your S-curve crushed shadows to a flat line at
  0, back it off. You cannot ungrade clipped data.

### Node 03 — Skin
- **Qualifier** > eyedropper on the mid-tone of the face. Widen Hue until the
  whole face is selected, then tighten Luma to reject the background.
- Click the **highlight** button to see the matte in black and white. Clean it:
  Denoise 2-5, Blur Radius 2-4, In/Out softness to feather.
- Add a **Power Window** around the face and set the qualifier to intersect,
  so a wooden desk elsewhere in frame does not get graded as skin.
- The move: pull saturation down 5-10% and push Gamma very slightly warm.
  Over-saturated skin is the fastest way to make a cinematic grade look cheap.
- **Vectorscope check:** skin should sit on or very near the skin tone line
  (the line running from center toward roughly 11 o'clock). If it is off the
  line, rotate Hue in the qualifier node until it lands.

### Node 04 — Look
This is where the creative grade lives. Everything above was repair.

- **Color Warper** (Resolve 18+) is better than a LUT for a custom look. Grab
  a hue/sat region and drag. Non-destructive, no banding.
- **Split-tone:** Lift wheel toward blue/cyan, Gain wheel toward orange/yellow.
  Small moves. If you can see you did it, you did too much.
- For a bright tech look: raise Lift very slightly (a lifted black, roughly
  2-4% off zero), keep highlights clean and neutral, push saturation in the
  cyans and blues only, using the Hue vs Sat curve.
- If using a LUT, put it in this node and use the node's **Key > Output Gain**
  to dial back its intensity. Never apply a LUT at 100% without a mixer to
  reduce it.

### Node 05 — Glow / halation (parallel node)
Alt+P for a parallel node, or use a Layer Mixer.

- **Bloom** effect, or: Qualifier on luminance only (Luma high, 70-100),
  then Blur radius up, then set the node's composite mode to **Add** at low
  opacity via the Key panel Output Gain.
- **Halation** specifically: same luma key, blur it, then tint the blurred
  result orange/red before adding. This is what makes digital footage read as
  film.
- Keep it under 15% strength. Glow is a seasoning.

### Node 06 — Output
- **Film grain** (Effects > ResolveFX Texture > Film Grain), or a simple Noise.
  Grain does two jobs: it hides banding from the export encoder and it unifies
  footage from different sources.
- Grain strength 0.3-0.6 for a subtle modern look. 1.0+ reads as a filter.
- **Sharpening** last, if at all. Radius 0.5, Scaling 0.3. Any more and it
  ringing-artifacts on export compression.
- Final **soft clip** in the Primaries panel to protect the highlight rail:
  High Soft 15-25.

---

## Reading the scopes (this is the whole skill)

| Scope | What it tells you | The move |
|---|---|---|
| **Waveform** | Exposure and contrast distribution across the frame | Lift/Gamma/Gain |
| **Parade** | Color balance, per channel, per luminance range | Offset wheel, then LGG |
| **Vectorscope** | Saturation amount and hue direction | Sat, hue curves |
| **Histogram** | Clipping. Nothing else useful. | Back off Gain/Lift |

The rule: **balance on the parade, contrast on the waveform, skin on the
vectorscope.** Your eyes adapt within seconds and will lie to you about every
one of these.

---

## Round-trip

**Preferred, for short-form:**
1. Premiere: export a flattened master. ProRes 422 HQ, 1080x1920, 30fps, no
   compression concerns.
2. Resolve: import, grade, deliver ProRes 422 HQ.
3. Premiere: import the graded master, cut in nothing else, export H.264 for
   platform.

**Why not XML round-trip on a 30-second reel:** every graphic, every AE
dynamic link, every speed ramp is a potential relink failure. The flattened
master path has zero failure surface and costs you one extra render. For a
30-second video that render is 90 seconds. Take the trade.

**When XML is worth it:** a long-form piece where you need per-clip grading
control and the shots come from multiple cameras.

---

## Grading 8-bit 4:2:0 footage (which is what the 60D gives you)

Be honest about the ceiling. See `references/footage-constraints.md`.

- Do not push saturation hard. 4:2:0 chroma subsampling means color detail is
  quarter resolution. Aggressive saturation reveals blocky color edges.
- Do not lift shadows more than about a stop. Noise and banding appear fast.
- Do not attempt heavy secondary qualification on a compressed 8-bit source.
  The qualifier will grab macro-blocking artifacts along with the subject.
- **Work in 32-bit float** (project settings) even with 8-bit source. It stops
  banding from accumulating between nodes.
- Add grain at the end. Grain is the single most effective tool for making
  8-bit footage read as if it had more latitude than it does.
