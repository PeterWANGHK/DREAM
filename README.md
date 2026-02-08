# DREAM
DREAM: Dynamic Risk-Aware Enhanced Autonomous Maneuver Planning for Highway Interactive Driving

This project extends from both [DRIFT](https://github.com/PeterWANGHK/DRIFT.git) and [IDEAM](https://github.com/YimingShu-teay/IDEAM.git) as a joint work by Zian Wang and [Yiming Shu](https://github.com/YimingShu-teay)

Stay tuned
### demonstration of gap searching for LC with stop-and-go traffic (MPC-based planning):
![simple snapshot for quick understanding](assests/DREAM_demo3.gif)

### demonstration of LC for emergency vehicle with safety-critical considerations ([IDEAM](https://github.com/YimingShu-teay/IDEAM.git)-based planning):
![simple snapshot for quick understanding](assests/DREAM_demo4.gif)

### proposed framework:
```
┌─────────────────────────────────────────────────────────────┐
│                      DREAM ARCHITECTURE                     │
│                                                             │
│  ┌────────────┐        ┌──────────────┐      ┌──────────┐   │
│  │   DRIFT    │───────▶   DREAM        ─────▶  IDEAM   │   │
│  │ Risk Field │ Risk   │  Controller  │ Mod. │ LMPC+CBF │   │
│  │   (PDE)    │ Query  │              │Params│          │   │
│  └────────────┘        └──────────────┘      └──────────┘   │
│       ▲                        │                     │      │
│       │                        ▼                     ▼      │
│  ┌────┴──────┐         ┌──────────────┐      ┌──────────┐   │
│  │ Traffic   │         │   Decision   │      │ Control  │   │
│  │  State    │         │   Gating     │      │ Actions  │   │
│  └───────────┘         └──────────────┘      └──────────┘   │
└─────────────────────────────────────────────────────────────┘
```
# DRIFT Risk Field Visualization for IDEAM

Overlay DRIFT's propagating risk field onto IDEAM emergency scenarios without modifying control behavior.

## 🚀 Quick Start

### Step 1: Run Visualization Simulation

```bash
cd C:\DREAM
python emergency_test_with_risk_viz.py
```

**Output:**
- Frames saved to `figsave_risk_viz/`
- Risk values saved to `figsave_risk_viz/risk_at_ego.npy`

### Step 2: Create Video Animation

```bash
# You may change the file name every epoch you run the simulation and then save the video
python video_generation.py
```

### Step 3: Analyze Risk Data

```bash
python risk_analysis_utils.py figsave_risk_viz/risk_at_ego.npy
```

**Output:**
- `risk_timeline.png` - Risk over time with threshold lines
- `risk_histogram.png` - Distribution of risk levels
- `risk_analysis.png` - Comprehensive multi-panel analysis
- `risk_events.csv` - High-risk events exported to CSV

---

## 🎨 Customizing Visualization

### Option A: Use Presets (Easiest)

Edit `emergency_test_with_risk_viz.py`, add after imports:

```python
from risk_viz_config import RiskVizConfig as viz_cfg

# Choose a preset
viz_cfg.preset_subtle()      # Low-contrast, clean
viz_cfg.preset_dramatic()    # High-contrast, emphasizes risk
viz_cfg.preset_scientific()  # Publication-ready with colorbar
viz_cfg.preset_highcontrast() # For presentations

# Then replace hardcoded values
RISK_ALPHA = viz_cfg.RISK_ALPHA
RISK_CMAP = viz_cfg.RISK_CMAP
# ... etc
```

### Option B: Manual Tuning

Edit these variables in `emergency_test_with_risk_viz.py` (around line 140):

```python
RISK_ALPHA = 0.4         # Transparency (0.0-1.0)
RISK_CMAP = 'hot'        # Colormap: 'hot', 'YlOrRd', 'plasma', 'inferno'
RISK_LEVELS = 15         # Number of contour levels
RISK_VMAX = 3.0          # Max risk value for color scale
SHOW_CONTOUR = True      # Show contour lines?
SHOW_HEATMAP = True      # Show filled heatmap?
```

**Colormap Options:**
- `'hot'` - Black → Red → Yellow → White (classic heat)
- `'YlOrRd'` - Yellow → Orange → Red (warning colors)
- `'Reds'` - White → Red (simple gradient)
- `'plasma'` - Purple → Pink → Yellow (perceptually uniform)
- `'inferno'` - Black → Purple → Orange → Yellow
- `'RdYlGn_r'` - Red → Yellow → Green (reversed)

---

## 📊 Understanding the Risk Field

### DRIFT Risk Sources

The risk field combines three sources:

1. **Vehicle-Induced Risk** (Anisotropic Gaussian kernels)
   - Higher in front of moving vehicles (direction of travel)
   - Decays with distance
   - Intensity scales with relative velocity

2. **Occlusion-Induced Risk** (Shadow regions)
   - Elevated behind large vehicles (trucks, trailers)
   - Represents hidden hazards in sensor blind spots
   - Propagates based on uncertainty

3. **Merge Pressure** (Topological conflicts)
   - High in lane-change zones
   - Elevated where lanes converge
   - Captures structural road geometry risks

### Risk Propagation Dynamics

- **Advection**: Risk flows with traffic
- **Diffusion**: Risk spreads spatially (uncertainty)
- **Telegraph term**: Finite propagation speed (wave-like)

---

## 🔍 Interpreting Results

### High Risk Scenarios

You should see elevated risk (red zones) in:

1. **Dense traffic** - Multiple vehicles close together
2. **Behind large vehicles** - Occlusion shadows
3. **Lane change maneuvers** - Merge pressure zones
4. **Emergency braking** - Sudden deceleration events

### Analysis Metrics

From `risk_analysis_utils.py`:

- **Mean Risk**: Average exposure level
- **Peak Risk**: Maximum risk encountered
- **Time in High Risk**: Duration above threshold
- **Risk Events**: Discrete high-risk episodes

---

## 📐 Technical Details

### Simulation Parameters

- **Grid**: From `config.py` (default: 400m × 60m, 1m resolution)
- **PDE Substeps**: 3 (for numerical stability)
- **Timestep**: 0.1s (matches IDEAM)
- **Horizon**: 400 timesteps (40 seconds)

### Performance

- **Frame generation**: ~2-3 seconds per frame (depends on grid size)
- **Full simulation**: ~15-20 minutes for 400 frames
- **Memory**: ~500MB for storing risk fields (if enabled)
---


## 📈 Example Workflow

```bash
# 1. Run simulation with visualization
python emergency_test_with_risk_viz.py

# Output: figsave_risk_viz/0.png, 1.png, ..., 399.png
#         figsave_risk_viz/risk_at_ego.npy

# 2. Create video
python video_generation.py

# 3. Analyze risk data
python risk_analysis_utils.py figsave_risk_viz/risk_at_ego.npy

# Output: risk_timeline.png, risk_histogram.png, risk_analysis.png, risk_events.csv

# 4. Review results
# - Check risk_analysis.png for statistics
# - Inspect risk_events.csv for high-risk moments
```

