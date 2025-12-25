# GPU Cellular Automata

A WebGL2-accelerated cellular automata visualizer with multiple simulation modes, real-time parameter control, and Max/MSP integration for audiovisual performance.

## Overview

This project provides a GPU-accelerated environment for exploring various cellular automata systems, from classic binary rules to continuous reaction-diffusion systems. All computation runs on the GPU via fragment shaders, enabling smooth real-time visualization at high resolutions.

### Features

- **5 simulation modes** ranging from binary to continuous systems
- **GPU-accelerated** computation using WebGL2 ping-pong framebuffers
- **Real-time parameter control** via GUI or Max/MSP
- **Mouse/touch drawing** to seed patterns
- **Data output** for sonification or analysis
- **Presets** for rules and initial states

---

## Simulation Modes

### 0. Classic (Binary)

Traditional cellular automata with binary alive/dead states using Birth/Survival rules.

| Parameter | Description |
|-----------|-------------|
| Birth rules | Which neighbor counts cause a dead cell to become alive (0-8) |
| Survival rules | Which neighbor counts allow a living cell to survive (0-8) |

**Presets:**
- Conway's Life (B3/S23)
- Seeds (B2/S)
- Day & Night (B3678/S34678)
- HighLife (B36/S23)
- Diamoeba (B35678/S5678)
- Maze (B3/S12345)

### 1. Classic + Fade

Binary rules with continuous decay trails. Cells fade out smoothly rather than dying instantly.

| Parameter | Range | Description |
|-----------|-------|-------------|
| Decay Rate | 0.01-0.3 | How quickly dead cells fade |
| Birth Threshold | 0.1-0.9 | Value above which a cell counts as "alive" |

### 2. Smooth Life

Continuous generalization of Game of Life using inner/outer radius averaging and sigmoid transitions.

| Parameter | Range | Description |
|-----------|-------|-------------|
| Inner Radius | 2-10 | Radius for "self" averaging |
| Outer Radius | 6-20 | Radius for "neighbor" averaging |
| Birth Min (b1) | 0-1 | Lower birth threshold |
| Birth Max (b2) | 0-1 | Upper birth threshold |
| Death Min (d1) | 0-1 | Lower survival threshold |
| Death Max (d2) | 0-1 | Upper survival threshold |
| Alpha | 0.01-0.5 | Smoothing/transition rate |

### 3. Lenia

Kernel-based continuous cellular automata producing organic, lifelike patterns.

| Parameter | Range | Description |
|-----------|-------|-------------|
| Radius | 5-30 | Kernel radius in cells |
| Growth μ | 0-1 | Center of growth function |
| Growth σ | 0.001-0.2 | Width of growth function |
| Time Step | 0.05-1.0 | Integration step size |

**Presets:**
- Orbium (glider)
- Geminium (splitting)
- Hydrogeminium

### 4. Gray-Scott

Two-chemical reaction-diffusion system producing spots, stripes, spirals, and mitosis patterns.

| Parameter | Range | Description |
|-----------|-------|-------------|
| Feed Rate (f) | 0.01-0.1 | Rate of chemical A replenishment |
| Kill Rate (k) | 0.03-0.07 | Rate of chemical B removal |
| Diffusion A | 0.1-1.5 | Diffusion rate of chemical A |
| Diffusion B | 0.1-1.0 | Diffusion rate of chemical B |

**Presets:**
- Mitosis
- Coral Growth
- Spirals
- Spots
- Waves

---

## GUI Controls

### System
Select the simulation mode from the dropdown.

### Playback
| Control | Description |
|---------|-------------|
| Play | Run simulation continuously |
| Pause | Stop simulation |
| Step | Advance one frame (useful when paused) |
| Speed | Iterations per frame (1-20) |

### Initialize
| Control | Description |
|---------|-------------|
| Preset | Random, Center Blob, Multiple Seeds, Clear |
| Initialize | Apply the selected preset |
| Clear | Clear the grid |
| Density | Fill density for random initialization |
| Brush Size | Radius for mouse drawing |

### Display
| Control | Description |
|---------|-------------|
| Cell Size | Pixels per cell (1-8) |
| Color 1 | Alive/high value color |
| Color 2 | Dead/low value color |
| Color 3 | Gray-Scott chemical B color |
| Wrap Edges | Toroidal wrapping |

### Data Output
| Control | Description |
|---------|-------------|
| Enable Output | Toggle data output on/off |
| Grid Cols | Horizontal subdivision (4-64) |
| Grid Rows | Vertical subdivision (4-64) |
| Rate (ms) | Output interval (10-200ms) |

---

## Max/MSP Integration

Load the HTML file in a `jweb` object. All parameters can be controlled via messages.

### Global Control

| Message | Values | Description |
|---------|--------|-------------|
| `mode` | 0-4 | 0=Classic, 1=Classic+Fade, 2=SmoothLife, 3=Lenia, 4=Gray-Scott |
| `running` | 0/1 | Play/pause |
| `speed` | 1-20 | Iterations per frame |
| `step` | bang | Single step |
| `init` | 0-3 | 0=Random, 1=Center, 2=Multi, 3=Clear |
| `clear` | bang | Clear grid |
| `density` | 0.01-1.0 | Random fill density |
| `brush_size` | 1-30 | Drawing brush radius |
| `wrap` | 0/1 | Edge wrapping |
| `cell_size` | 1-8 | Pixel size per cell |

### Colors (RGB floats 0-1)

| Message | Values | Description |
|---------|--------|-------------|
| `color1` | r g b | Alive/high color |
| `color2` | r g b | Dead/low color |
| `color3` | r g b | Gray-Scott chemical B |

### Classic / Classic+Fade (modes 0-1)

| Message | Values | Description |
|---------|--------|-------------|
| `birth` | 0/1 ×9 | Birth rules for 0-8 neighbors |
| `survival` | 0/1 ×9 | Survival rules for 0-8 neighbors |
| `fade_decay` | 0.01-0.3 | Decay rate (fade mode) |
| `fade_birth_thresh` | 0.1-0.9 | Alive threshold |

### Smooth Life (mode 2)

| Message | Range | Description |
|---------|-------|-------------|
| `sl_ri` | 2-10 | Inner radius |
| `sl_ra` | 6-20 | Outer radius |
| `sl_b1` | 0-1 | Birth min |
| `sl_b2` | 0-1 | Birth max |
| `sl_d1` | 0-1 | Death min |
| `sl_d2` | 0-1 | Death max |
| `sl_alpha` | 0.01-0.5 | Smoothing |

### Lenia (mode 3)

| Message | Range | Description |
|---------|-------|-------------|
| `lenia_r` | 5-30 | Kernel radius |
| `lenia_mu` | 0-1 | Growth center |
| `lenia_sigma` | 0.001-0.2 | Growth width |
| `lenia_dt` | 0.05-1.0 | Time step |

### Gray-Scott (mode 4)

| Message | Range | Description |
|---------|-------|-------------|
| `gs_f` | 0.01-0.1 | Feed rate |
| `gs_k` | 0.03-0.07 | Kill rate |
| `gs_da` | 0.1-1.5 | Diffusion A |
| `gs_db` | 0.1-1.0 | Diffusion B |

### Mouse Emulation

| Message | Values | Description |
|---------|--------|-------------|
| `paint` | x y click | Normalized coords (0-1), click state (0/1). Interpolates lines when click=1 |
| `paint_at` | x y | Single point paint, no interpolation |

### Data Output Control

| Message | Values | Description |
|---------|--------|-------------|
| `sonify` | 0/1 | Enable/disable data output |
| `sonify_grid` | cols rows | Grid subdivision (1-64 each) |
| `sonify_rate` | ms | Output interval (10-1000ms) |

### Outlets (from jweb)

| Outlet | Format | Description |
|--------|--------|-------------|
| `ready` | 1 | Sent when loaded |
| `generation` | int | Current generation count |
| `fps` | int | Frames per second |
| `mode` | string | Current mode name |
| `row` | index val0 val1 ... | Row data (row index, then N floats 0-1) |
| `mass` | float | Average activity (0-1) |
| `center` | x y | Center of mass, normalized (0-1) |

---

## Example Max Patches

### Basic Control

```
[loadbang]
|
[mode 3]        <- Set to Lenia
|
[jweb cellular-automata.html @size 800 600]
```

### Frame-by-Frame Control

```
[loadbang]
|
[running 0]     <- Pause on load
|
[jweb]

[metro 100]     <- Or any trigger
|
[step]
|
[jweb]
```

### Parameter Automation

```
[line 0.]
|
[* 0.1]         <- Scale to 0-0.1
|
[prepend lenia_mu]
|
[jweb]
```

### Data Output for Sonification

```
[sonify 1]
[sonify_grid 16 16]
[sonify_rate 50]
|
[jweb]
|
[route row mass center]
|        |       |
|        |       [unpack 0. 0.]
|        |       |         |
|        |       [pan~]    [something]
|        |
|        [* 100.]
|        |
|        [sig~]
|
[zl.nth 1]      <- Get first column
|
[unpack 0. 0. 0. ...]  <- Unpack row values
```

### Hand Tracking Input

```
[your-hand-tracker]   <- e.g. MediaPipe via OSC
|
[unpack 0. 0. 0.]     <- x y pinch
|     |     |
[pack 0. 0. 0.]
|
[prepend paint]
|
[jweb]
```

---

## Technical Notes

### Architecture

- **Rendering**: WebGL2 with RGBA16F float textures
- **Computation**: Fragment shader ping-pong between two framebuffers
- **UI**: Vanilla HTML/CSS/JS, no dependencies
- **Max Integration**: `window.max` API for inlet/outlet binding

### Performance

- GPU handles all CA computation
- Data output reads pixels from GPU (expensive) - use moderate grid sizes (16×16 to 32×32)
- Cell size 1 may have issues on some systems; cell size 2+ recommended

### Browser Compatibility

Requires WebGL2 and `EXT_color_buffer_float` extension. Works in:
- Chrome/Chromium
- Firefox
- Safari 15+
- Max/MSP jweb

---

## References

- [Lenia](https://chakazul.github.io/lenia.html) - Bert Wang-Chak Chan
- [Smooth Life](https://arxiv.org/abs/1111.1567) - Stephan Rafler
- [Gray-Scott](http://mrob.com/pub/comp/xmorphia/) - Robert Munafo
- [Flow-Lenia](https://sites.google.com/view/flowlenia/) - Plantec et al.

---

## License

MIT License - Free for personal and commercial use.
