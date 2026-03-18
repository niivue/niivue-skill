# NiiVue API Reference

## Installation

```bash
npm install niivuegpu
```

```js
import NiiVue from 'niivuegpu'              // Both backends (auto-selects WebGPU, falls back to WebGL2)
import NiiVue from 'niivuegpu/webgpu'       // WebGPU only
import NiiVue from 'niivuegpu/webgl2'       // WebGL2 only
```

## Initialization

```js
const nv = new NiiVue({
  backend: 'webgpu',                         // 'webgpu' (default) or 'webgl2'
  isAntiAlias: true,                         // Anti-aliasing (default true)
  backgroundColor: [0, 0, 0, 1],            // RGBA [0..1]
  sliceType: SLICE_TYPE.MULTIPLANAR,
  isColorbarVisible: true,
})

await nv.attachToCanvas(document.getElementById('gl'))
await nv.loadVolumes([{ url: '/path/to/volume.nii.gz' }])
```

All constructor options use the same flat property names as the controller setters below.

## Property Reference

Property setters auto-trigger `drawScene()`. Multiple sets in one synchronous block batch into a single render frame.

### Scene (no prefix)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `azimuth` | `number` | `110` | Camera azimuth (degrees) |
| `elevation` | `number` | `10` | Camera elevation (degrees) |
| `crosshairPos` | `[number, number, number]` | `[0.5, 0.5, 0.5]` | Crosshair in scene fraction [0..1] |
| `pan2Dxyzmm` | `[number, number, number, number]` | `[0,0,0,1]` | 2D pan (x,y,z mm + zoom) |
| `scaleMultiplier` | `number` | `1` | 3D render scale |
| `gamma` | `number` | `1` | Display gamma |
| `backgroundColor` | `[number, number, number, number]` | `[0,0,0,1]` | Background RGBA |
| `clipPlaneColor` | `[number, number, number, number]` | `[0.7,0,0.7,0.5]` | Clip plane color (alpha sign controls behavior) |
| `isClipPlaneCutaway` | `boolean` | `false` | Clip plane cutaway mode |

### Layout (no prefix)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `sliceType` | `number` | `SLICE_TYPE.MULTIPLANAR` | View layout (see SLICE_TYPE) |
| `mosaicString` | `string` | `''` | Mosaic layout string (e.g. `'A -20 0 20; C 0; S 0'`) |
| `showRender` | `number` | `SHOW_RENDER.AUTO` | Force 3D render tile (0=never, 1=always, 2=auto) |
| `multiplanarType` | `number` | `0` | Multiplanar layout (0=auto, 1=column, 2=grid, 3=row) |
| `heroFraction` | `number` | `0` | Hero tile fraction of canvas width (0..1) |
| `heroSliceType` | `number` | `SLICE_TYPE.RENDER` | Hero tile content |
| `isEqualSize` | `boolean` | `false` | Equal-size tiles in multiplanar |
| `isMosaicCentered` | `boolean` | `true` | Center mosaic tiles |
| `tileMargin` | `number` | `0` | Pixel margin between tiles |
| `isRadiological` | `boolean` | `false` | Radiological convention |

### UI (no prefix)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `isColorbarVisible` | `boolean` | `false` | Show colorbars |
| `isOrientCubeVisible` | `boolean` | `true` | Show 3D orientation cube |
| `isOrientationTextVisible` | `boolean` | `true` | Show orientation labels |
| `is3DCrosshairVisible` | `boolean` | `true` | Show crosshair in 3D |
| `isGraphVisible` | `boolean` | `false` | Show 4D time-series graph |
| `isRulerVisible` | `boolean` | `false` | Show ruler |
| `isCrossLinesVisible` | `boolean` | `false` | Show cross-reference lines |
| `isLegendVisible` | `boolean` | `true` | Show legend |
| `isPositionInMM` | `boolean` | `false` | Show position in mm (vs voxels) |
| `isMeasureUnitsVisible` | `boolean` | `true` | Show measurement units |
| `isThumbnailVisible` | `boolean` | `false` | Show thumbnail image |
| `thumbnailUrl` | `string` | `''` | Thumbnail image URL |
| `crosshairColor` | `[number, number, number, number]` | `[1,0,0,1]` | Crosshair RGBA |
| `crosshairGap` | `number` | `10` | Crosshair gap in pixels |
| `crosshairWidth` | `number` | `1` | Crosshair line width |
| `fontColor` | `[number, number, number, number]` | `[0.5,0.5,0.5,1]` | Font RGBA |
| `fontScale` | `number` | `0.4` | Font size scale |
| `fontMinSize` | `number` | `13` | Minimum font size |
| `selectionBoxColor` | `[number, number, number, number]` | `[1,1,1,0.5]` | Selection box RGBA |
| `measureLineColor` | `[number, number, number, number]` | `[1,0,0,1]` | Measurement line RGBA |
| `measureTextColor` | `[number, number, number, number]` | `[1,0,0,1]` | Measurement text RGBA |
| `rulerWidth` | `number` | `2` | Ruler line width |
| `graphNormalizeValues` | `boolean` | `false` | Normalize 4D graph values |
| `graphIsRangeCalMinMax` | `boolean` | `false` | Fix graph range to cal min/max |

### Volume (prefix `volume`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `volumeIllumination` | `number` | `0` | Volume lighting intensity (0=off) |
| `volumeOutlineWidth` | `number` | `0` | Volume outline width |
| `volumeAlphaShader` | `number` | `1` | Alpha shader mode |
| `volumeIsBackgroundMasking` | `boolean` | `false` | Mask non-brain in 3D |
| `volumeIsAlphaClipDark` | `boolean` | `false` | Clip dark voxels in 3D |
| `volumeIsNearestInterpolation` | `boolean` | `false` | Nearest-neighbor sampling |
| `volumeIsV1SliceShader` | `boolean` | `false` | V1 fiber direction shader |
| `volumeMatcap` | `string` | `''` | Matcap texture name |
| `volumePaqdUniforms` | `[number, number, number, number]` | `[0.01,0.5,0.25,0.4]` | PAQD atlas rendering uniforms |

### Mesh (prefix `mesh`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `meshXRay` | `number` | `0` | X-ray transparency (0=opaque, 1=full) |
| `meshThicknessOn2D` | `number` | `Infinity` | Clip thickness on 2D slices (mm) |

### Draw (prefix `draw`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `drawIsEnabled` | `boolean` | `false` | Enable drawing pen |
| `drawPenValue` | `number` | `1` | Pen label index (0=erase) |
| `drawPenSize` | `number` | `1` | Pen size in voxels |
| `drawIsFillOverwriting` | `boolean` | `true` | Overwrite existing drawn voxels |
| `drawOpacity` | `number` | `0.8` | Drawing overlay opacity |
| `drawRimOpacity` | `number` | `-1` | Rim-only opacity (-1=solid) |
| `drawColormap` | `string` | `'_draw'` | Drawing colormap |

### Annotation (prefix `annotation`)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `annotationIsEnabled` | `boolean` | `false` | Enable annotation tools |
| `annotationActiveLabel` | `number` | `1` | Active annotation label |
| `annotationActiveGroup` | `string` | `'default'` | Active annotation group |
| `annotationBrushRadius` | `number` | `2.0` | Brush radius |
| `annotationIsErasing` | `boolean` | `false` | Eraser mode |
| `annotationIsVisibleIn3D` | `boolean` | `false` | Show annotations in 3D |
| `annotationStyle` | `AnnotationStyle` | `{fillColor, strokeColor, strokeWidth}` | Annotation visual style |
| `annotationTool` | `string` | `'freehand'` | Active tool name |

### Interaction (no prefix)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `primaryDragMode` | `number` | `DRAG_MODE.crosshair` | Left-button drag mode |
| `secondaryDragMode` | `number` | `DRAG_MODE.contrast` | Right-button drag mode |
| `isSnapToVoxelCenters` | `boolean` | `false` | Snap crosshair to voxel centers |
| `isDragDropEnabled` | `boolean` | `true` | Allow file drag-and-drop |
| `isYoked3DTo2DZoom` | `boolean` | `false` | Yoke 3D zoom to 2D zoom |

## Methods

### Initialization & Lifecycle

```js
await nv.attachToCanvas(canvasElement)                  // Initialize GPU + attach to canvas
await nv.attachTo('canvasId')                           // Convenience: find by ID, then attach
await nv.reinitializeView({ backend: 'webgl2' })        // Switch backend or toggle antialiasing
nv.drawScene()                                          // Force redraw
nv.destroy()                                            // Tear down GPU resources + listeners
```

### Loading Data

```js
await nv.loadVolumes([{ url: '...', calMin: 30, colormap: 'hot' }])
await nv.addVolume({ url: '...' })                      // Add single volume (keeps existing)
await nv.loadMeshes([{ url: '...mz3', shaderType: 'phong' }])
await nv.addMesh({ url: '...' })                        // Add single mesh (keeps existing)
await nv.addMeshLayer(meshIndex, { url: '...' })         // Add scalar overlay to mesh
await nv.loadImage(pathOrFile, options)                  // Auto-detect: volume or mesh by extension
await nv.addImage(pathOrFile, options)                   // Auto-detect + add (keeps existing)
await nv.loadDocument(urlOrFile)                         // Load NVD scene document
await nv.loadDrawing(urlOrFile)                          // Load drawing overlay (NIfTI)
await nv.loadDeferred4DVolumes(volumeId)                 // Load remaining 4D frames
```

### Updating Volumes

```js
await nv.setVolume(index, { calMin: 40, calMax: 90, colormap: 'warm' })
await nv.setFrame4D(volumeId, frameNumber)               // Navigate 4D frames
await nv.recalculateCalMinMax(volumeIndex, frame)        // Recalibrate display range
await nv.setColormapLabel(volumeIndex, labelMap)         // Apply atlas label colormap (object)
await nv.setColormapLabelFromUrl(volumeIndex, jsonUrl)   // Apply atlas label colormap (URL)
await nv.setModulationImage(targetId, modulatorId)       // Modulate overlay by another volume
await nv.updateGLVolume()                                // Force GPU texture refresh
```

### Updating Meshes

```js
await nv.setMesh(index, { shaderType: 'toon', opacity: 0.8 })
await nv.setMeshLayerProperty(meshIndex, layerIndex, key, value)
await nv.setMeshLayerFrame4D(meshIndex, layerIndex, frame)
await nv.setTractOptions(meshIndex, { fiberRadius: 0.5, decimation: 0.1 })
await nv.setConnectomeOptions(meshIndex, { nodeScale: 2, edgeScale: 1 })
```

### Removing Data

```js
await nv.removeMesh(meshIndex)
await nv.removeMeshLayer(meshIndex, layerIndex)
await nv.removeAllVolumes()
await nv.removeAllMeshes()
```

### Drawing

```js
nv.createEmptyDrawing()                  // Create blank drawing bitmap
nv.drawUndo()                            // Undo last stroke
nv.closeDrawing()                        // Destroy drawing + GPU textures
await nv.saveDrawing('drawing.nii.gz')   // Save as NIfTI
await nv.loadDrawing(urlOrFile)          // Load existing drawing
```

### Annotations (Vector)

```js
nv.addAnnotation(annotationObject)
nv.removeAnnotation(id)
nv.clearAnnotations()
nv.annotationUndo()
nv.annotationRedo()
```

### Scene Control

```js
nv.setClipPlane([depth, azimuth, elevation])
nv.setClipPlanes([[d, az, el], ...])
nv.moveCrosshairInVox(di, dj, dk)       // Step crosshair by voxels
nv.broadcastTo([otherNv1, otherNv2])     // Sync scene state to targets
nv.broadcastTo()                         // Stop syncing
nv.setDragMode('contrast')               // Set drag mode by string name
```

### Saving / Export

```js
await nv.saveBitmap('screenshot.png')
await nv.saveMesh(meshIndex, 'output.mz3')
await nv.saveVolume(volumeIndex, 'output.nii.gz')
nv.saveDocument('scene.nvd')
await nv.saveDrawing('drawing.nii.gz')
```

### Getters

```js
nv.volumes              // NVImage[] -- loaded volumes
nv.meshes               // NVMesh[] -- loaded meshes
nv.colormaps            // string[] -- available colormap names
nv.meshShaders          // string[] -- available mesh shader names
nv.volumeExtensions     // string[] -- supported volume file extensions
nv.meshExtensions       // string[] -- supported mesh file extensions
nv.volumeWriteExtensions // string[] -- writable volume formats
nv.meshWriteExtensions  // string[] -- writable mesh formats
nv.volumeTransforms     // string[] -- available volume transforms
nv.drawingColormaps     // string[] -- available drawing colormaps
nv.backend              // 'webgpu' | 'webgl2' | undefined
nv.isAntiAlias          // boolean -- current anti-aliasing state
nv.devicePixelRatio     // number -- current DPR (-1 = auto)
nv.getFrame4D(id)       // number -- current frame index for volume
nv.annotations          // readonly VectorAnnotation[]
nv.selectedAnnotation   // string | null -- selected annotation ID
```

## Events

NiiVue extends `EventTarget`. All events are typed via `NVEventMap`.

### User Interaction

| Event | Detail Type | Key Fields |
|-------|------------|------------|
| `locationChange` | `NiiVueLocation` | `mm`, `vox`, `frac`, `values`, `string` |
| `frameChange` | `FrameChangeDetail` | `volume`, `frame` |
| `dragRelease` | `DragReleaseDetail` | `mmStart`, `mmEnd`, `mmLength`, `axCorSag` |
| `measurementCompleted` | `MeasurementDetail` | `startMM`, `endMM`, `distance` |
| `angleCompleted` | `AngleDetail` | `firstLine`, `secondLine`, `angle` |
| `pointerUp` | `PointerUpDetail` | `x`, `y`, `button` |

### Loading & Removal

| Event | Detail Type | Key Fields |
|-------|------------|------------|
| `volumeLoaded` | `VolumeLoadedDetail` | `volume` |
| `meshLoaded` | `MeshLoadedDetail` | `mesh` |
| `volumeRemoved` | `VolumeRemovedDetail` | `volume`, `index` |
| `meshRemoved` | `MeshRemovedDetail` | `mesh`, `index` |
| `documentLoaded` | (none) | -- |

### View Lifecycle

| Event | Detail Type | Key Fields |
|-------|------------|------------|
| `viewAttached` | `ViewAttachedDetail` | `canvas`, `backend` |
| `viewDestroyed` | (none) | -- |
| `canvasResize` | `CanvasResizeDetail` | `width`, `height` |

### View Control

| Event | Detail Type | Key Fields |
|-------|------------|------------|
| `azimuthElevationChange` | `AzimuthElevationChangeDetail` | `azimuth`, `elevation` |
| `clipPlaneChange` | `ClipPlaneChangeDetail` | `clipPlane` |
| `sliceTypeChange` | `SliceTypeChangeDetail` | `sliceType` |

### Data Updates

| Event | Detail Type | Key Fields |
|-------|------------|------------|
| `volumeUpdated` | `VolumeUpdatedDetail` | `volumeIndex`, `volume`, `changes` |
| `meshUpdated` | `MeshUpdatedDetail` | `meshIndex`, `mesh`, `changes` |

### Drawing

| Event | Detail Type | Key Fields |
|-------|------------|------------|
| `drawingChanged` | `DrawingChangedDetail` | `action` (`'stroke'`\|`'create'`\|`'close'`\|`'undo'`) |
| `drawingEnabled` | `DrawingEnabledDetail` | `isEnabled` |
| `penValueChanged` | `PenValueChangedDetail` | `penValue` |

### Annotations

| Event | Detail Type | Key Fields |
|-------|------------|------------|
| `annotationAdded` | `AnnotationAddedDetail` | `annotation` |
| `annotationRemoved` | `AnnotationRemovedDetail` | `id` |
| `annotationChanged` | `AnnotationChangedDetail` | `action` |

### Generic

| Event | Detail Type | Key Fields |
|-------|------------|------------|
| `change` | `PropertyChangeDetail` | `property`, `value` |

## Constants

```js
import { SLICE_TYPE, DRAG_MODE, COLORMAP_TYPE, MULTIPLANAR_TYPE, SHOW_RENDER } from 'niivuegpu'
```

### SLICE_TYPE

| Name | Value | Description |
|------|-------|-------------|
| `AXIAL` | `0` | Axial slice |
| `CORONAL` | `1` | Coronal slice |
| `SAGITTAL` | `2` | Sagittal slice |
| `MULTIPLANAR` | `3` | A+C+S+Render |
| `RENDER` | `4` | 3D render only |

### DRAG_MODE

| Name | Value | Description |
|------|-------|-------------|
| `none` | `0` | No drag action |
| `contrast` | `1` | Adjust contrast |
| `measurement` | `2` | Distance measurement |
| `pan` | `3` | Pan view |
| `slicer3D` | `4` | 3D slicer |
| `callbackOnly` | `5` | Event only |
| `roiSelection` | `6` | ROI selection box |
| `angle` | `7` | Angle measurement |
| `crosshair` | `8` | Move crosshair |
| `windowing` | `9` | Window/level |

### COLORMAP_TYPE

| Name | Value | Description |
|------|-------|-------------|
| `MIN_TO_MAX` | `0` | Map [calMin, calMax] across LUT |
| `ZERO_TO_MAX_TRANSPARENT_BELOW_MIN` | `1` | Map [0, calMax], transparent below calMin |
| `ZERO_TO_MAX_TRANSLUCENT_BELOW_MIN` | `2` | Map [0, calMax], faded below calMin |

### MULTIPLANAR_TYPE

| Name | Value |
|------|-------|
| `AUTO` | `0` |
| `COLUMN` | `1` |
| `GRID` | `2` |
| `ROW` | `3` |

### SHOW_RENDER

| Name | Value |
|------|-------|
| `NEVER` | `0` |
| `ALWAYS` | `1` |
| `AUTO` | `2` |

## Volume Options

Used with `loadVolumes()`, `addVolume()`, and `setVolume()`:

| Option | Type | Description |
|--------|------|-------------|
| `url` | `string` | Volume URL (load only) |
| `calMin` | `number` | Display minimum |
| `calMax` | `number` | Display maximum |
| `calMinNeg` | `number` | Negative colormap minimum |
| `calMaxNeg` | `number` | Negative colormap maximum |
| `colormap` | `string` | Colormap name (e.g. `'gray'`, `'hot'`, `'winter'`) |
| `colormapNegative` | `string` | Negative colormap name |
| `colormapType` | `number` | COLORMAP_TYPE value |
| `isTransparentBelowCalMin` | `boolean` | Transparent below calMin |
| `opacity` | `number` | Overlay opacity [0..1] |
| `isColorbarVisible` | `boolean` | Per-volume colorbar |
| `isLegendVisible` | `boolean` | Per-volume legend |
| `frame4D` | `number` | Initial 4D frame |
| `limitFrames4D` | `number` | Max frames to load initially |

## Mesh Options

Used with `loadMeshes()`, `addMesh()`, and `setMesh()`:

| Option | Type | Description |
|--------|------|-------------|
| `url` | `string` | Mesh URL (load only) |
| `opacity` | `number` | Mesh opacity [0..1] |
| `color` | `[number, number, number, number]` | Mesh color RGBA |
| `shaderType` | `string` | Shader name (`'phong'`, `'flat'`, `'toon'`, `'outline'`, etc.) |
| `isColorbarVisible` | `boolean` | Per-mesh colorbar |

Available shaders: `phong`, `flat`, `matte`, `toon`, `outline`, `rim`, `silhouette`, `crevice`, `vertexColor`, `crosscut`.

## Naming Convention Summary

- **Scene/Layout/UI/Interaction**: No prefix -- `azimuth`, `sliceType`, `isColorbarVisible`, `primaryDragMode`
- **Volume**: `volume` prefix -- `volumeIllumination`, `volumeIsAlphaClipDark`
- **Mesh**: `mesh` prefix -- `meshXRay`, `meshThicknessOn2D`
- **Draw**: `draw` prefix -- `drawIsEnabled`, `drawPenValue`
- **Annotation**: `annotation` prefix -- `annotationIsEnabled`, `annotationTool`
- **Booleans**: `is`/`has` comes AFTER domain prefix -- `volumeIsAlphaClipDark`, not `isVolumeAlphaClipDark`
