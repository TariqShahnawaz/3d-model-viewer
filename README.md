# 3d-model-viewer
A browser-based interactive 3D model viewer built with Three.js. Drop any .glb file onto the page to load it into a staged scene with lighting, shadows, and orbit controls. A configurator panel lets you tweak material color, metalness, roughness, wireframe, environment preset, and auto-rotate speed.


# 3D Model Viewer & Configurator

**Live URL:** https://claude.ai/artifact/Jncy9LxyyuqyMiMiq4fGaz

---

## What I Built

A **browser-based interactive 3D model viewer** with a real-time configurator panel. It works as both a GLB drag-and-drop loader *and* a standalone scene that's never empty thanks to three built-in procedural models.

### Features

| Feature | Detail |
|---|---|
| **3D scene** | Three.js r128 with `WebGLRenderer`, ACES filmic tone mapping, sRGB output encoding |
| **Orbit controls** | Mouse drag / touch to orbit, pinch to zoom, damped for smoothness |
| **Drag-and-drop GLB** | Drop any `.glb` / `.gltf` onto the page — or use the file picker |
| **Procedural default models** | Geometric composition, Torus Knot, Sphere Cluster — never an empty page |
| **Material configurator** | Color picker, metalness, roughness, wireframe toggle, flat-shading toggle |
| **Lighting system** | 9 environment presets (Studio, Sunset, Dawn, Night, Warehouse, Forest, Apartment, City, Lobby) — each changes ambient, directional, and fill light color + intensity |
| **Exposure control** | ACES tone mapping exposure slider |
| **Auto-rotate speed** | 0–5× configurable |
| **Shadow toggle** | PCF soft shadows with a shadow-receiving ground plane |
| **Camera FOV** | 20–100° slider |
| **FPS + draw call counter** | Live perf readout in the bottom-left corner |
| **Reduced-motion support** | `prefers-reduced-motion: reduce` disables auto-rotate |
| **WebGL fallback** | If WebGL is unavailable, a static fallback UI is shown with a help message |
| **Mobile** | Touch orbit/zoom works; panel collapses behind a ⚙️ toggle button; pixel ratio capped at 2× |

---

## Performance Note

**Bundle / load strategy:**

- Three.js r128 loads from **cdnjs** (~580 KB gzip). Pinned to an exact version (`r128`) — no `@latest` wildcards.
- GLTFLoader + OrbitControls load from **jsDelivr** at the same pinned version.
- No bundler, no build step — the page is a single self-contained HTML file, zero KB of custom JS payload.
- The canvas is rendered inside a `ResizeObserver`-managed div, so it responds instantly to layout changes without a polling loop.
- `renderer.setPixelRatio(Math.min(devicePixelRatio, 2))` caps the pixel budget on high-DPI screens — the most impactful single mobile perf tweak.
- Shadow map is `1024×1024` (not 2048) — enough for soft results without doubling VRAM.
- FPS and draw calls are exposed in the UI via `renderer.info` — no external profiler needed to spot issues.

**FE-10 lens:**

| Metric | Result |
|---|---|
| External scripts | 3 (Three.js, GLTFLoader, OrbitControls) — all CDN, cacheable |
| Custom JS | ~180 lines inline, no parse overhead from a bundle |
| Pixel ratio cap | ✅ (max 2×) |
| Shadow map size | 1024×1024 — balanced |
| Reduced-motion | ✅ auto-rotate disabled |
| WebGL fallback | ✅ static UI with help text |
| Touch support | ✅ OrbitControls handles pointer events |
| Frame budget risk | Low — one directional shadow + ambient, <10 draw calls on built-in models |

---

## What I'd Add With More Time

1. **DRACO / meshopt decompression** — `DRACOLoader` integration so large GLBs compress 5–10× on disk before transfer
2. **HDR environment maps** — load a real `.hdr` with `RGBELoader` + `PMREMGenerator` for physically correct IBL reflections
3. **Animation playback** — scrubber for glTF `AnimationClip`s embedded in the model
4. **Model info panel** — triangle count, material list, texture memory pulled from `renderer.info`
5. **Screenshot export** — `canvas.toBlob()` → download PNG
6. **Lazy canvas mount** — `IntersectionObserver` to defer renderer init until the canvas scrolls into view (useful if embedded in a larger page)
7. **Progress bar** — `onProgress` callback from GLTFLoader for large file feedback

---

## Stack

- **Three.js r128** (UMD, CDN)
- **GLTFLoader** (Three.js example, CDN)
- **OrbitControls** (Three.js example, CDN)
- Vanilla HTML/CSS/JS — no React, no bundler, ships as one file
