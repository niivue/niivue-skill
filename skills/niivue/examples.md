# NiiVue Code Examples

Complete working examples for common NiiVue use cases.

## 1. Minimal Viewer

The simplest possible NiiVue setup -- 3 lines of code.

```html
<canvas id="gl"></canvas>
<script type="module">
  import NiiVue from 'niivuegpu'
  const nv = new NiiVue()
  await nv.attachToCanvas(document.getElementById('gl'))
  await nv.loadVolumes([{ url: '/volumes/mni152.nii.gz' }])
</script>
```

## 2. Volume Viewer with Controls

Slice type selector, colormap picker, orientation toggle, and location display.

```html
<select id="sliceType">
  <option value="0">Axial</option>
  <option value="1">Coronal</option>
  <option value="2">Sagittal</option>
  <option value="4">Render</option>
  <option value="3" selected>A+C+S+R</option>
</select>
<select id="colormapSelect"></select>
<canvas id="gl"></canvas>
<div id="location"></div>

<script type="module">
  import NiiVue from 'niivuegpu'

  const nv = new NiiVue({
    backgroundColor: [0.2, 0.2, 0.2, 1],
    isColorbarVisible: true,
  })

  nv.addEventListener('locationChange', (e) => {
    document.getElementById('location').textContent = e.detail.string
  })

  await nv.attachToCanvas(document.getElementById('gl'))

  // Populate colormap dropdown
  for (const cmap of nv.colormaps) {
    const opt = document.createElement('option')
    opt.value = cmap
    opt.textContent = cmap
    if (cmap === 'gray') opt.selected = true
    colormapSelect.appendChild(opt)
  }

  colormapSelect.onchange = () => nv.setVolume(0, { colormap: colormapSelect.value })
  sliceType.onchange = () => nv.sliceType = parseInt(sliceType.value)

  await nv.loadVolumes([{ url: '/volumes/mni152.nii.gz' }])
</script>
```

## 3. Mesh Viewer

Load meshes and switch shaders.

```html
<select id="shaderSelect"></select>
<canvas id="gl"></canvas>

<script type="module">
  import NiiVue from 'niivuegpu'

  const nv = new NiiVue({ backgroundColor: [0.5, 0.3, 0.7, 1] })
  await nv.attachToCanvas(document.getElementById('gl'))

  // Populate shader dropdown
  for (const shader of nv.meshShaders) {
    const opt = document.createElement('option')
    opt.value = shader
    opt.textContent = shader
    shaderSelect.appendChild(opt)
  }

  shaderSelect.onchange = () => {
    nv.setMesh(0, { shaderType: shaderSelect.value })
  }

  await nv.loadMeshes([{ url: '/meshes/BrainMesh_ICBM152.lh.mz3' }])
</script>
```

## 4. Multi-Instance Sync

Two viewers on separate canvases with bidirectional sync.

```html
<canvas id="gl1"></canvas>
<canvas id="gl2"></canvas>

<script type="module">
  import NiiVue from 'niivuegpu'

  const nv1 = new NiiVue({ backgroundColor: [1, 1, 1, 1] })
  await nv1.attachToCanvas(document.getElementById('gl1'))
  await nv1.loadVolumes([{ url: '/volumes/mni152.nii.gz' }])

  const nv2 = new NiiVue({ backgroundColor: [1, 1, 1, 1] })
  await nv2.attachToCanvas(document.getElementById('gl2'))
  await nv2.loadVolumes([{ url: '/volumes/mni152.nii.gz' }])

  // Bidirectional sync -- crosshair, rotation, zoom all sync
  nv1.broadcastTo([nv2])
  nv2.broadcastTo([nv1])
</script>
```

## 5. Drawing / Annotation

Create a voxel drawing overlay, draw with pen, undo, save.

```html
<select id="penValue">
  <option value="-1" selected>Off</option>
  <option value="0">Erase</option>
  <option value="1">Red</option>
  <option value="2">Green</option>
  <option value="3">Blue</option>
</select>
<button id="undoBtn">Undo</button>
<button id="saveBtn">Save</button>
<canvas id="gl"></canvas>

<script type="module">
  import NiiVue from 'niivuegpu'

  const nv = new NiiVue()
  await nv.attachToCanvas(document.getElementById('gl'))
  await nv.loadVolumes([{ url: '/volumes/mni152.nii.gz' }])

  // Create empty drawing overlay
  nv.createEmptyDrawing()

  penValue.onchange = () => {
    const val = parseInt(penValue.value)
    if (val < 0) {
      nv.drawIsEnabled = false
    } else {
      nv.drawIsEnabled = true
      nv.drawPenValue = val
    }
  }

  undoBtn.onclick = () => nv.drawUndo()
  saveBtn.onclick = () => nv.saveDrawing('drawing.nii.gz')
</script>
```

## 6. 4D Volumes (Time Series)

Navigate frames in a 4D volume with graph display.

```html
<button id="prevFrame">Back</button>
<button id="nextFrame">Forward</button>
<label><input type="checkbox" id="graphCheck" checked> Graph</label>
<canvas id="gl"></canvas>

<script type="module">
  import NiiVue from 'niivuegpu'

  const nv = new NiiVue({
    backgroundColor: [0.1, 0.1, 0.1, 1],
    isGraphVisible: true,
  })

  nv.addEventListener('frameChange', (e) => {
    console.log(`Frame ${e.detail.frame}`)
  })

  await nv.attachToCanvas(document.getElementById('gl'))
  await nv.loadVolumes([{ url: '/volumes/fmri.nii.gz', limitFrames4D: 5 }])

  prevFrame.onclick = () => {
    const vol = nv.volumes[0]
    nv.setFrame4D(vol.id, nv.getFrame4D(vol.id) - 1)
  }
  nextFrame.onclick = () => {
    const vol = nv.volumes[0]
    nv.setFrame4D(vol.id, nv.getFrame4D(vol.id) + 1)
  }
  graphCheck.onchange = () => nv.isGraphVisible = graphCheck.checked
</script>
```

## 7. Layout Modes

Mosaic strings, hero layout, and multiplanar options.

```html
<input type="text" id="mosaicStr" value="A -20 0 20; C 0; S 0" />
<input type="range" id="heroSlide" min="0" max="8" value="0" />
<canvas id="gl"></canvas>

<script type="module">
  import NiiVue, { SLICE_TYPE } from 'niivuegpu'

  const nv = new NiiVue({
    isColorbarVisible: true,
    backgroundColor: [0.2, 0.2, 0.2, 1],
  })
  await nv.attachToCanvas(document.getElementById('gl'))
  await nv.loadVolumes([{ url: '/volumes/mni152.nii.gz' }])

  // Mosaic layout -- set mosaicString to activate
  mosaicStr.addEventListener('keyup', () => {
    nv.mosaicString = mosaicStr.value
  })

  // Hero layout -- fraction 0 = disabled, 0.5 = half canvas
  heroSlide.oninput = () => {
    nv.mosaicString = ''  // clear mosaic first
    nv.sliceType = SLICE_TYPE.MULTIPLANAR
    nv.heroFraction = heroSlide.value * 0.1
  }
</script>
```

Mosaic string syntax: `A`/`C`/`S` for orientation, `R` for 3D render, `X` for cross-lines, `L` for labels, `;` for row break, numbers for mm positions. Example: `A -20 0 20 ; S R X 0 S R X -0`.

## 8. Events

All NiiVue events with per-event detail formatting. Subscribes to every event in `NVEventMap`.

```html
<canvas id="gl"></canvas>
<div id="event-log" style="font-family: monospace; font-size: 0.8rem; max-height: 300px; overflow-y: auto;"></div>

<script type="module">
  import NiiVue from 'niivuegpu'

  const log = document.getElementById('event-log')

  function logEvent(name, detail) {
    const el = document.createElement('div')
    let info = ''
    switch (name) {
      // -- User Interaction --
      case 'locationChange':
        info = detail.string || ''
        break
      case 'frameChange':
        info = `frame ${detail.frame}`
        break
      case 'dragRelease':
        info = `${detail.mmLength.toFixed(1)}mm`
        break
      case 'pointerUp':
        info = `(${detail.x}, ${detail.y}) btn=${detail.button}`
        break
      case 'measurementCompleted':
        info = `${detail.distance.toFixed(1)}mm`
        break
      case 'angleCompleted':
        info = `${detail.angle.toFixed(1)}\u00B0`
        break
      // -- Loading & Removal --
      case 'volumeLoaded':
      case 'volumeRemoved':
        info = detail.volume?.name || ''
        break
      case 'meshLoaded':
      case 'meshRemoved':
        info = detail.mesh?.name || ''
        break
      case 'documentLoaded':
        break
      // -- View Lifecycle --
      case 'viewAttached':
        info = detail.backend
        break
      case 'viewDestroyed':
        break
      case 'canvasResize':
        info = `${detail.width}\u00D7${detail.height}`
        break
      // -- View Control --
      case 'azimuthElevationChange':
        info = `az=${detail.azimuth} el=${detail.elevation}`
        break
      case 'clipPlaneChange':
        info = detail.clipPlane.map((v) => v.toFixed(1)).join(', ')
        break
      case 'sliceTypeChange':
        info = `type=${detail.sliceType}`
        break
      // -- Data Updates --
      case 'volumeUpdated':
        info = `[${detail.volumeIndex}] ${Object.entries(detail.changes).map(([k, v]) => `${k}=${v}`).join(', ')}`
        break
      case 'meshUpdated':
        info = `[${detail.meshIndex}] ${Object.entries(detail.changes).map(([k, v]) => `${k}=${v}`).join(', ')}`
        break
      // -- Drawing --
      case 'drawingChanged':
        info = detail.action
        break
      case 'drawingEnabled':
        info = detail.isEnabled ? 'on' : 'off'
        break
      case 'penValueChanged':
        info = `pen=${detail.penValue}`
        break
      // -- Annotations --
      case 'annotationAdded':
        info = `id=${detail.annotation.id}`
        break
      case 'annotationRemoved':
        info = `id=${detail.id}`
        break
      case 'annotationChanged':
        info = detail.action
        break
      // -- Generic --
      case 'change':
        info = `${detail.property} = ${JSON.stringify(detail.value)}`
        break
      default:
        info = detail ? JSON.stringify(detail) : ''
    }
    el.textContent = `[${name}] ${info}`
    log.prepend(el)
  }

  const nv = new NiiVue({ backgroundColor: [0.2, 0.2, 0.2, 1] })

  // Subscribe to every event in NVEventMap
  const ALL_EVENTS = [
    // User Interaction
    'locationChange', 'frameChange', 'dragRelease', 'pointerUp',
    'measurementCompleted', 'angleCompleted',
    // Loading & Removal
    'volumeLoaded', 'meshLoaded', 'volumeRemoved', 'meshRemoved', 'documentLoaded',
    // View Lifecycle
    'viewAttached', 'viewDestroyed', 'canvasResize',
    // View Control
    'azimuthElevationChange', 'clipPlaneChange', 'sliceTypeChange',
    // Data Updates
    'volumeUpdated', 'meshUpdated',
    // Drawing
    'drawingChanged', 'drawingEnabled', 'penValueChanged',
    // Annotations
    'annotationAdded', 'annotationRemoved', 'annotationChanged',
    // Generic
    'change',
  ]

  for (const name of ALL_EVENTS) {
    nv.addEventListener(name, (e) => {
      const detail = e instanceof CustomEvent ? e.detail : undefined
      logEvent(name, detail)
    })
  }

  await nv.attachToCanvas(document.getElementById('gl'))
  await nv.loadVolumes([{ url: '/volumes/mni152.nii.gz' }])
</script>
```

## 9. Atlas / Label Volumes

Load an atlas overlay with discrete label colormap.

```html
<canvas id="gl"></canvas>

<script type="module">
  import NiiVue from 'niivuegpu'

  const nv = new NiiVue({ backgroundColor: [0.3, 0.3, 0.5, 1] })

  nv.addEventListener('locationChange', (e) => {
    // e.detail.values includes label names for atlas volumes
    console.log(e.detail.string)
  })

  await nv.attachToCanvas(document.getElementById('gl'))

  // Load background + atlas overlay
  await nv.loadVolumes([
    { url: '/volumes/mni152.nii.gz', calMin: 30, calMax: 80 },
    { url: '/volumes/aal.nii.gz' },
  ])

  // Apply label colormap (JSON mapping index -> name + color)
  await nv.setColormapLabelFromUrl(1, '/volumes/aal.json')

  // Atlas-specific settings
  nv.volumeIsNearestInterpolation = true
  nv.volumeIsAlphaClipDark = true
  nv.isColorbarVisible = true
</script>
```

## 10. Shared Canvas (Bounds)

Multiple NiiVue instances sharing a single canvas via normalized bounds.

```html
<canvas id="gl"></canvas>

<script type="module">
  import NiiVue from 'niivuegpu'

  // Left half of canvas
  const nv1 = new NiiVue({
    backgroundColor: [0.3, 0, 0.1, 1],
    bounds: [[0, 0], [0.5, 1]],
    showBoundsBorder: true,
    boundsBorderColor: [0.3, 0.3, 0.3, 1],
  })

  // Right half of canvas
  const nv2 = new NiiVue({
    backgroundColor: [0.15, 0.15, 0.3, 1],
    bounds: [[0.5, 0], [1, 1]],
    showBoundsBorder: true,
    boundsBorderColor: [0.3, 0.3, 0.3, 1],
  })

  // Both attach to the SAME canvas
  await nv1.attachToCanvas(document.getElementById('gl'))
  await nv2.attachToCanvas(document.getElementById('gl'))

  await nv1.loadVolumes([{ url: '/volumes/mni152.nii.gz' }])
  await nv2.loadVolumes([{ url: '/volumes/mni152.nii.gz', colormap: 'hot' }])

  // Sync crosshair between instances
  nv1.broadcastTo([nv2])
  nv2.broadcastTo([nv1])
</script>
```

Bounds format: `[[x1, y1], [x2, y2]]` where y=0 is bottom, y=1 is top.
