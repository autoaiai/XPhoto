<div align="center">

# XPhoto · Commercial Portrait Retouching

[简体中文](README.md) · English

A Windows desktop retouching app for commercial portrait workflows. Skin smoothing, liquify, body shaping,
color grading, ID photos, AI cutout and object removal all happen in one window —
**every pixel is processed locally; your photos never leave the machine**.

### Website · <https://autoaiai.com/xphoto/>

[Website](https://autoaiai.com/xphoto/) ·
[Download installer](https://github.com/autoaiai/XPhoto/releases/latest) ·
[User manual](https://autoaiai.com/xphoto/help/help.html) ·
[Older releases](https://github.com/autoaiai/XPhoto/releases) ·
[Experimental multi-platform builds](https://github.com/autoaiai/XPhoto/releases/tag/v1.1.9-cross) ·
[Changelog](#changelog)

`v1.1.9` · Windows 10 / 11 (x64) · 8 UI languages · installer ≈ 113 MB

<img src="https://autoaiai.com/xphoto/assets/p1.webp" width="420" alt="XPhoto retouched sample portrait">

<sub>Sample portrait · 3 images ship with the app, so you can start right after installing</sub>

</div>

---

## What it is

XPhoto is built for the "dozens of images due today" commercial portrait workflow: retouch an ID photo or a
set of corporate headshots without shuttling files between Photoshop, skin-smoothing plugins, cutout websites
and ID-photo layout tools.

- **One window, end to end**: import → face and body retouch → color and crop → ID photo layout → export.
- **On-device inference, nothing uploaded**: face detection, body segmentation, matting, object removal and
  denoising all run locally. There is no path that sends a photo out.
- **Recompute only what changed**: the canvas redraws incrementally by dirty rectangles, so dragging a slider
  or painting a mask updates in tens of milliseconds instead of reprocessing a full 6000×8000 image.
- **Output-grade detail**: ID photos are laid out at 300 dpi, and export recomputes at the original resolution —
  not an upscaled screenshot of the viewport.

### Three steps to start

1. Load a photo from the left **Library** tab or the top **Open image** button; the built-in samples work too.
2. Pick a tool in the left rail (Beauty / Body / Presets / Color / Crop / ID Photo / AI Retouch) and drag
   sliders in the right panel — the canvas updates live.
3. Hit **Export** at the top for PNG or JPEG. For a one-pass result, **One-Click Retouch** derives parameters
   from measurements of the detected face.

## Download & install

| Type | File | Notes |
| --- | --- | --- |
| **Installer** | `XPhoto-Setup-<version>.exe` | Choose the install folder; creates Start Menu and desktop shortcuts; supports one-click updates |

- Downloads (always use the newest version here): <https://github.com/autoaiai/XPhoto/releases>
- Website and online manual: <https://autoaiai.com/xphoto/>

> Since v1.1.7 only the NSIS installer is published; portable builds are gone and Release asset names are ASCII.

The installer **ships its own VC++ runtime**, independent of whatever version is installed system-wide —
mismatched runtimes were the real cause of the early "crashes when opening a photo" reports.

### Other platforms (v1.1.9 experimental builds)

These live in the [v1.1.9-cross pre-release](https://github.com/autoaiai/XPhoto/releases/tag/v1.1.9-cross).
They were cross-built on a single Windows machine and are **not official releases**. The official build is
still the Windows x64 NSIS installer above.

| Platform | File | Notes |
| --- | --- | --- |
| Windows ARM64 | `XPhoto-1.1.9-win-arm64.exe` | NSIS installer, ships the ARM64 VC++ runtime |
| Linux x64 / ARM64 | `XPhoto-1.1.9-linux-x64.tar.gz`, `XPhoto-1.1.9-linux-arm64.tar.gz` | Executable bits fixed; unpack and run `./XPhoto` |
| macOS Apple Silicon | `XPhoto-1.1.9-mac-arm64-UNSIGNED.zip` | Unsigned zip, see the notes below |
| macOS Intel | `XPhoto-1.1.9-mac-x64-UNSIGNED.zip` | Same as above, plus **no AI cutout / segmentation / denoise** |

Known limitations, in full:

- **macOS builds are unsigned, unnotarized, and never verified on real Mac hardware.** There is no dmg.
  Before first launch, run `xattr -cr XPhoto.app && codesign -s - --deep --force XPhoto.app` on the Mac.
- Upstream `onnxruntime-node` 1.30 ships no `darwin/x64` binary, so on Intel Macs AI cutout, segmentation
  and denoise are unavailable; everything else works.
- Linux ships `tar.gz` only; AppImage / deb require a real Linux host.
- **No auto-update on macOS / Linux** (the update feed only publishes the Windows NSIS installer). AI model
  weights are not bundled either — use the in-app "Download models" button on first run.
- `manifest.json` in the release lists every file's size and sha256; please verify after downloading.

For properly signed dmg / AppImage builds, rebuild on a real Mac / Linux host with
`scripts/cross-pack.mjs` from the full source project (this public repository contains the home page only).

## Why XPhoto

- **Fully offline.** Face detection, body segmentation, matting, object removal and denoising run locally;
  the original image is never uploaded and there is no path to an external service.
- **Skin smoothing is not blur.** Neutral-gray frequency separation plus normalized convolution flattens color
  blotches on smooth skin while keeping real structure — pores, eyelashes, lip edges.
- **Masks are real masks.** Face edits stay on the face, body edits stay on the body, background swaps keep
  stray hairs; bare skin the model cannot classify (chest, collarbone) can be added with a Pen-tool contour,
  and the same path can be inverted to subtract skin.
- **Only the person changes.** Body shaping uses human segmentation plus keypoint constraints, so the
  background is not stretched along with it; full-body whitening is gated by skin color and excludes the
  clothing class, so a white shirt or gold satin does not brighten with the skin.
- **It does not bluff.** With no face detected it says so plainly ("this is a frame centered by image ratio,
  not a valid ID-photo composition"); camera values it cannot read are marked as unreadable; known defects
  are documented rather than glossed over.

<p align="center">
<img src="https://autoaiai.com/xphoto/assets/shots/mask.webp" width="360" alt="Segmentation classes: clothing, skin, hair">
<br><sub>Segmentation classes (blue = clothing · green = skin/neck · red = hair): smoothing and whitening apply to skin only, clothing is excluded</sub>
</p>

## Features

### Library & import

- Import a single image, a multi-selection, or an entire folder; thumbnail filmstrip with RAW / HEIC badges.
- EXIF in the top-left of the stage: camera, lens, focal length, aperture, shutter, ISO, dimensions, exposure compensation.
- Tethered capture: camera detection, watched-folder auto import (reads only after the write finishes, never half a file),
  and USB live-view frame grab.

### Beauty

Eight parameter groups and well over a hundred sliders, all applied only to detected facial skin:

| Group | Controls |
| --- | --- |
| Skin tone | Smoothing, texture retention, whitening, even tone, fullness, clarity, de-oil, wrinkle removal, dark circles, sharpening, edge sharpening |
| Face shape | Slim face, smaller head (hair included), narrow face, jawline, cheekbones, shorter face, V-line, chin, three-section balance, hairline |
| Features | Eyes (size / brighten / spacing / corners / height / tilt), nose (bridge / wings / tip), mouth (smile / lip thickness / corners), brows (height / tilt) |
| Makeup | Lip color, blush, contour, aegyo sal, lashes, eyeliner, eyeshadow, color intensity, highlighter, freckles |

- **50 makeup presets** (soft flush, fresh-faced, sweet doll, Chinese-style, retro Korean, orchid, clean glass,
  ballet and more) and **7 face-shape presets** (pink doll, Korean clean, peach, ageless, soft glow, airy, girlish light).
- **Full-body skin tone** is a separate layer: whitening extends beyond the face (arms, neck, legs), gated by
  skin color with the clothing class excluded.
- **Multiple faces**: adjust all at once, one selected face, or toggle each face independently; thumbnails
  render live for the current photo.
- **Skin mask Pen tool**: the model has no "torso skin" class, so place Bézier anchors along the contour and
  close the path to include chest / collarbone skin in whitening and smoothing; switch to "subtract skin" to
  invert the selection.

### Body

Two groups — smart body shaping (human segmentation + keypoint constraints) and manual shaping: slimming,
waist, leg length, leg thinning, swan neck, arms, shoulder width, head-to-shoulder ratio, bust, stretching,
full-body smoothing and whitening. **Only the person changes; the background is never stretched.**

### Presets

- 13 color looks: textured skin, creamy skin, natural skin, cool white, Fuji film, Leica M9, Hasselblad blue,
  warm sun, blue ice, monochrome, ink shadow, cinematic, fresh.
- Save your own parameter sets with a strength slider; apply to all faces in one click.

### Color

Basic (exposure, contrast, highlights, shadows, whites, blacks, temperature, tint, vibrance, saturation,
clarity, dehaze, texture, vignette, grain), HSL mixing, curves, stylization, white balance
(as shot / auto / daylight / cloudy / shade / incandescent / fluorescent / flash).

### Crop

Ratio presets (original, 1:1, 3:4, 2:3, 4:5, 9:16, free) plus a manual crop box: drag the box to move,
drag a corner to resize; edit while seeing the full image, then confirm to fill the frame.

### ID photos

- **12 standard sizes**: Chinese 1-inch / large 1-inch / small 1-inch / 2-inch / small 2-inch / large 2-inch,
  passport, national ID, US visa, driver's license, social security, marriage certificate. One click places
  the crop box from the detected face (headroom, hair-to-chin ratio, face centering).
- **8 backgrounds**: keep original, white, light blue, blue, dark blue, red, gray, custom color. Swaps use the
  subject mask and preserve hair edges.
- **Print layout**: 5 / 6 / 7-inch and A4, automatically tiled at 300 dpi with crop marks.

### Local tools (bottom of the left rail)

| Tool | What it does |
| --- | --- |
| Liquify | Four Photoshop-style brushes: forward warp, pucker, bloat, twirl; freeze / thaw mask, brush size / strength / radius / feather |
| Spot healing | Paint over a blemish and it is filled from surrounding texture |
| Smart removal | Box or paint over an object; a model reconstructs the background |
| AI spot repair | Larger sampling area and smoother transitions than spot healing — for color spots, oily shine, small debris |
| Defringe | Two modes: matte white fringe and lens chromatic fringe |
| Guides | Thirds, center, golden ratio, grid, or draw and drag your own |
| Face control points | Node-based face reshaping with a familiar Photoshop feel |

Brush shortcuts: `[` / `]` for size, `Shift + [` / `Shift + ]` for feather.

### AI Retouch

Four tabs; a missing model gets an inline download button right where it is needed:

- **Cutout** — portrait matting to transparent PNG, with two paths: hair-level (RVM) and fine detail (BiRefNet-lite fp16).
- **Remove** — drag a rectangle over the object and the model fills it in (LaMa); `Esc` cancels the selection.
- **Segment & denoise** — after portrait segmentation, denoise the whole frame or skin only; removes noise and compression artifacts.
- **Models** — see what is installed / missing and download one or all; the directory is always writable
  (`%APPDATA%\portrait-lab\models`), never a read-only install folder.

GPU inference prefers DirectML; if probing or validation fails it falls back to CPU and the UI states the
active backend. If the previous run crashed inside the inference library, AI features are disabled with an
explanation instead of crash-looping.

### Tethered capture

- **Camera detection**: enumerate connected Windows imaging devices.
- **Watched folder**: pick a directory and new photos dropped by the camera vendor tool or Wi-Fi appear in the filmstrip.
- **USB live view**: system UVC preview with a round shutter button to grab a frame. This is webcam-level
  preview — no camera parameters, no remote shutter or focus — and the UI says so.

### One-Click Retouch

Parameters are derived from actual face measurements, not a hardcoded table: detect → lighting → repair →
reshape → color, five steps with visible progress; apply to all faces or the current one.

### Export

Lossless PNG or JPEG — the encoder follows the extension you choose (`.jpg` really produces a JPEG).
Export recomputes at the original resolution, so liquify, spot healing, removal and masks are all in the file.

## Interface

```
┌────────────────────────────────────────────────────────────────┐
│ Top: Open image · Before/After · Zoom · Undo/Redo · Export     │
├──────┬──────────────────────────────────────────┬──────────────┤
│ Left │                                          │              │
│ Lib  │            Canvas (zoom / pan)           │  Parameters  │
│ Edit │                                          │  (active     │
│ Fun  │                                          │   tool)      │
│      │                                          │              │
│ ──── │                                          │              │
│ Tool │                                          │              │
│ bar  │                                          │              │
├──────┴──────────────────────────────────────────┴──────────────┤
│ Filmstrip: switch between photos in the same batch             │
└────────────────────────────────────────────────────────────────┘
```

- Wheel pans vertically and horizontally; `Ctrl + wheel` zooms around the cursor (the point under the cursor
  stays put); buttons switch between fit-to-window, 100%, and step zoom in / out.
- Three left tabs: Library, Retouch, Creative. The Retouch tab is the vertical tool rail; drawing tools sit at the bottom.
- Top **Original / Retouched** comparison, or switch to a single image.

## Keyboard shortcuts

| Action | Keys |
| --- | --- |
| Undo / Redo | `Ctrl + Z` / `Ctrl + Shift + Z` |
| Brush size | `[` / `]` |
| Brush feather | `Shift + [` / `Shift + ]` |
| Collapse current tool and parameter panel | `Esc` |
| Pan canvas | Wheel, or drag empty canvas |
| Zoom canvas | `Ctrl + wheel` |
| Cancel selection (AI Remove) | `Esc` |

## Supported file formats

| Type | Extensions | Decoding |
| --- | --- | --- |
| Camera RAW (29) | `CR2 CR3 CRW NEF NRW ARW SRF SR2 DNG RAF ORF RW2 PEF SRW RAW RWL DCR KDC MRW X3F 3FR FFF IIQ MOS ERF MEF RWZ GPR BRAW` | Extract the embedded RAW preview and rewrite the orientation flag |
| HEIC / HEIF | `HEIC HEIF HIF` | libheif (WebAssembly), decoded locally |
| Standard bitmaps | `JPG JPEG JPE JFIF PNG WEBP BMP GIF TIF TIFF AVIF` | Chromium native decoding |

All three are auto-rotated from EXIF orientation; RAW is not re-encoded, so there is zero quality loss.
Very large images are downsampled for display only — export still processes the original.

> **RAW limitation**: full development (demosaic + white balance + color matrix) needs a native library such as
> LibRaw. XPhoto currently uses the embedded preview and labels it "embedded preview" in the UI. Most cameras
> embed a preview at sensor output size, so export resolution is usually unaffected.

## Languages

简体中文, 繁體中文, English, 日本語, 한국어, Español, Deutsch, Français.
The default follows the system language; the language button in the title bar switches instantly (the UI reloads).
All UI strings, dialogs and error messages come from language packs; a few deep panel strings are not yet
covered and fall back through "current language → English → Chinese", so you never see a blank label or a raw key.

## Privacy

- Photos, face data, masks and exports are all processed locally and never uploaded.
- The only outbound traffic: update checks and AI model downloads. The external-LLM subpage and API key vault
  have been removed, so there is no channel that uploads a photo.
- Logs and crash dumps are written only to `<userData>\logs\`.

## Updates

Borrows AICUT's check cadence, but **install timing is adapted for a retouching app**: no countdown auto-install,
and nothing installed behind your back on exit.

- Checks 15 seconds after launch, then every 6 hours; 15 seconds after waking from sleep; retries every
  5 minutes on network failure instead of waiting the full 6 hours.
- On a new version → **silent background download** to a local cache: no popup, no countdown, no interruption
  while you retouch. Downloads use source failover and resume, then pass three checks (file size, PE header, sha256).
- Nothing is shown after the download; a line is written to `<userData>\updates\pending.json`.
- **Installation happens at the next launch**, silently, followed by a restart. No photo is open at that
  moment, so unsaved edits are naturally safe; quitting normally never installs.
- To install immediately: Help → Software Update → Check now → "Install and update"; the app restarts afterwards.
- Status goes to `<userData>\logs\updates.log` (status and version numbers only, no addresses); the cache keeps
  the 2 most recent installers and clears the rest.

## System requirements

| Item | Requirement |
| --- | --- |
| OS | Windows 10 / 11 (x64) |
| GPU | Optional. A DirectML-capable GPU accelerates AI inference; CPU works for everything |
| Disk | ≈ 390 MB after install; AI models download on demand (≈ 100–210 MB each, never all at once) |
| Network | Only for model downloads and update checks; everything else is offline |

## FAQ

**The app says AI features are disabled?**
If the previous run crashed inside the inference library, a circuit breaker prevents a crash loop. Once you can
open photos normally, refresh under AI Retouch → Models, or start with `--force-ai` to clear the record.

**AI tools do nothing / models are missing?**
AI Retouch → Models lists each missing file with a download button. Downloads land in
`%APPDATA%\portrait-lab\models` (override with `PORTRAIT_MODELS_DIR`).

**Export dimensions are not what I expected?**
Export always recomputes at the loaded image's original resolution, independent of screen zoom. Two cases are
smaller: a RAW embedded preview (capped at that preview's size), or an EXIF-oriented original where output uses
the rotated frame.

**The image looks soft for a moment while dragging a slider?**
During dragging the preview drops to a faster tier; 400 ms after you stop it recomputes at full resolution,
and the idle tier computes one more full-quality pass.

**Only one person in a group shot needs retouching?**
Switch the right-panel Multiple faces control to "adjust one face", or turn off that face's toggle. Note that
full-body skin tone is applied to the whole frame and still covers a disabled face — otherwise that face would
be left with a visible color seam.

## Changelog

### v1.1.9

- The Color **Curves** panel is rebuilt as a **draggable point curve**: click to add a point and drag it,
  right-click to delete (up to 8 intermediate points), drag the endpoints vertically. RGB / Red / Green / Blue
  each keep their own control points; the parametric curve tab and three split sliders remain available.
- Fixed full-frame color grading (including point curves) **not being written into exported images**; slider
  tracks in the Beauty / Body panels now share one aligned left edge.
- The skin mask is now a Pen path (Bézier anchors); the user manual and repository home page were updated in
  step, and an English README was added.
- Added [experimental v1.1.9 builds](https://github.com/autoaiai/XPhoto/releases/tag/v1.1.9-cross) for macOS,
  Linux and Windows ARM64 (separate pre-release; macOS builds are unsigned). The official Windows release and
  its auto-update feed are unchanged.

### v1.1.8

- Auto-update changed to "silent background download + **install and restart at next launch**": checks at
  15 s / every 6 h / after wake, 5-minute retry on network failure; no popup, countdown, or interruption while
  retouching. Manual check can still install immediately.
- **Removed the entire AI Retouch → Large Model subpage**: six cloud / local model features, external model
  configuration and the API key vault were deleted along with their main-process calls. AI Retouch now has only
  Cutout / Remove / Segment & denoise / Models, and no path uploads a photo. The single-rectangle selection for
  object removal was kept and simplified.
- Added a full README and website links to the repository home page.

### v1.1.7

- Help became a complete offline user manual (18 chapters, printable), openable in-app, with an online version.
- Website and online manual moved to <https://autoaiai.com/xphoto/>.
- Update source switched to a fixed GitHub Releases address (site manifest as fallback); installer downloads
  gained source failover and resume.
- **NSIS installer only** — portable builds discontinued.

### v1.1.6

- Updates became one-click: on detecting a new version it downloads to a local cache with progress, then
  "Install and update" silently installs and restarts — no manual 113 MB download.
- Three-way installer validation (size / PE header / sha256); interrupted downloads are never treated as
  successful and can be retried in place.

### v1.1.1

- The app ships its own VC++ runtime, no longer affected by mixed system runtime versions — the real root
  cause of "crashes when opening a photo".
- Crash fallback and forensics: abnormal exits write logs and dumps; a crash traced to the inference library
  disables AI automatically.
- Crash dialogs report the runtime version actually detected and how to fix it, instead of a generic error.
- Fixed the update checker reading the wrong filename, which suppressed new-version prompts.

Earlier versions: [Releases](https://github.com/autoaiai/XPhoto/releases).

---

## Development

Stack: Electron + TypeScript (main / preload / renderer, `contextIsolation` on);
image algorithms in `beauty.js` / `skin.js` / `liquify.js` / `body.js`;
AI inference via `onnxruntime-node` (segmentation, matting, removal, denoising) and MediaPipe
(human segmentation and keypoints); face detection and 68-point landmarks come from face-api.js, bundled offline.

```bash
npm install
npm start          # compile and launch
npm run dist       # build the NSIS installer only
npm test           # full regression (hundreds of pixel-level assertions, incl. i18n / packaging / crash handling)
```

| Doc | Contents |
| --- | --- |
| [`docs/DEV-NOTES.md`](docs/DEV-NOTES.md) | Developer notes: measured numbers, pitfalls and algorithm trade-offs for every feature |
| [`docs/XPhoto-使用手册.html`](docs/XPhoto-使用手册.html) | Complete user manual (18 chapters, offline, printable) |
| [`docs/i18n-plan.md`](docs/i18n-plan.md) | The 8-language UI plan and scripts |
| [`ai/README.md`](ai/README.md) | Model provenance, download mirror benchmarks, training roadmap and blockers |
| `src/photo/` | Image pipeline: RAW / HEIC / EXIF / model directories / inference backends |
| `test/` | Regression probes behind each `npm run xxx` |

Regression rule: assertions pin down **measured defects** (pixel alignment, landing error, displacement, edge
contrast), not "the function exists". Known red tests are documented with their cause, not hidden.

## Links

| Purpose | URL |
| --- | --- |
| Website | <https://autoaiai.com/xphoto/> |
| Online user manual | <https://autoaiai.com/xphoto/help/help.html> |
| Latest installer | <https://github.com/autoaiai/XPhoto/releases/latest> |
| All versions / release notes | <https://github.com/autoaiai/XPhoto/releases> |

## Credits

Third-party components: Electron, ONNX Runtime, MediaPipe, face-api.js, libheif, exifr.
AI model weights follow their respective upstream licenses (BiRefNet, LaMa, RVM and others);
copyright remains with their authors.
