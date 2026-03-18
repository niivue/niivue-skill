---
name: niivue
description: "Use when building web applications with the NiiVue or NiiVueGPU neuroimaging visualization library, when code imports 'niivuegpu' or 'niivue', or when the user asks about displaying brain images, medical volumes, or neuroimaging meshes in a web browser"
version: 1.0.0
---

# NiiVue Development Guide

NiiVueGPU is a WebGPU/WebGL2 neuroimaging visualization library for displaying brain volumes and meshes in the browser. Package: `niivuegpu`. Import subpaths: `niivuegpu/webgpu`, `niivuegpu/webgl2`.

## Quick Start

```js
import NiiVue from 'niivuegpu'

const nv = new NiiVue({ backgroundColor: [0, 0, 0, 1] })
await nv.attachToCanvas(document.getElementById('gl'))
await nv.loadVolumes([{ url: '/volumes/mni152.nii.gz' }])
```

## Core Patterns

**Async initialization** -- `attachToCanvas()` and all `load*()` methods are async. Always `await` them.

**Flat property setters** auto-trigger rendering (RAF-batched, so multiple sets in one sync block = one render):

```js
nv.sliceType = SLICE_TYPE.MULTIPLANAR  // no prefix for scene/layout/ui
nv.volumeIllumination = 0.6            // 'volume' prefix
nv.meshXRay = 0.1                      // 'mesh' prefix
nv.drawIsEnabled = true                // 'draw' prefix
```

**Constructor** accepts the same flat property names as config:

```js
const nv = new NiiVue({
  backgroundColor: [0, 0, 0, 1],
  sliceType: SLICE_TYPE.MULTIPLANAR,
  isColorbarVisible: true,
  primaryDragMode: DRAG_MODE.crosshair,
})
```

**Events** use the standard `EventTarget` API:

```js
nv.addEventListener('locationChange', (e) => {
  console.log(e.detail.string)  // human-readable location
  console.log(e.detail.values)  // per-volume intensity values
})
```

**Per-item options** use the same keys for loading and updating:

```js
await nv.loadVolumes([{ url: '...', calMin: 30, colormap: 'hot' }])
nv.setVolume(0, { calMin: 40, colormap: 'warm' })
```

## Quick Reference

| Task | Code |
|------|------|
| Set slice view | `nv.sliceType = SLICE_TYPE.AXIAL` |
| 3D rotation | `nv.azimuth = 110; nv.elevation = 15` |
| Show colorbar | `nv.isColorbarVisible = true` |
| Change colormap | `nv.setVolume(0, { colormap: 'hot' })` |
| Load mesh | `await nv.loadMeshes([{ url: '...mz3' }])` |
| Navigate frames | `nv.setFrame4D(vol.id, frameNum)` |
| Sync instances | `nv1.broadcastTo([nv2])` |
| Crosshair event | `nv.addEventListener('locationChange', handler)` |
| Drawing mode | `nv.createEmptyDrawing(); nv.drawIsEnabled = true` |
| Mosaic layout | `nv.mosaicString = 'A -20 0 20; C 0; S 0'` |

## Common Mistakes

1. **Forgetting `await`** on `attachToCanvas()` or `loadVolumes()` -- causes silent failures
2. **Wrong prefix** -- scene/layout/UI properties have NO prefix (`sliceType`, not `layoutSliceType`); volume/mesh/draw DO have prefixes (`volumeIllumination`, `meshXRay`, `drawPenValue`)
3. **Using setter methods instead of properties** -- use `nv.azimuth = 110` not `nv.setAzimuth(110)`
4. **Boolean naming** -- booleans use `is`/`has` AFTER domain prefix: `volumeIsAlphaClipDark` not `isVolumeAlphaClipDark`
5. **Mutating data directly** -- use `setVolume(idx, opts)` to change per-volume settings, not direct property mutation

## Additional Resources

For detailed API tables and constants, see `api-reference.md` in this skill directory.
For complete working code examples, see `examples.md` in this skill directory.
