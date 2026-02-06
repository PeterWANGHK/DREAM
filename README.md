# DREAM
DREAM: Dynamic Risk-Aware Enhanced Autonomous Maneuver Planning for Highway Interactive Driving

This project will extend from both [DRIFT](https://github.com/PeterWANGHK/DRIFT.git) and [IDEAM](https://github.com/YimingShu-teay/IDEAM.git) as a joint work by Zian Wang and [Yiming Shu](https://github.com/YimingShu-teay)

Stay tuned

### proposed framework:
┌─────────────────────────────────────────────────────────────┐
│                      DEAM ARCHITECTURE                      │
│                                                             │
│  ┌────────────┐        ┌──────────────┐      ┌──────────┐   │
│  │   DRIFT    │───────▶   PRIDEAM     ─────▶  IDEAM    │   │
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
