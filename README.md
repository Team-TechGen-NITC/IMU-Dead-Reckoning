# AI/ML-Powered Intelligent Dead Reckoning System

> Seamless smartphone navigation when GNSS disappears: tunnels, underpasses, parking levels, urban canyons.

## Table of Contents

| | | |
|---|---|---|
| [![Section](https://img.shields.io/badge/SECTION-PROBLEM_STATEMENT-e05d44?labelColor=555555&style=flat-square)](#problem-statement) | [![Section](https://img.shields.io/badge/SECTION-OVERVIEW-0078d4?labelColor=555555&style=flat-square)](#overview) | [![Section](https://img.shields.io/badge/SECTION-ARCHITECTURE-008080?labelColor=555555&style=flat-square)](#architecture) 
| [![Section](https://img.shields.io/badge/SECTION-RESULTS-66b512?labelColor=555555&style=flat-square)](#Results-so-far) | [![Section](https://img.shields.io/badge/SECTION-CURRENT_STATUS-00e5ff?labelColor=555555&style=flat-square)](#Current-status) | [![Section](https://img.shields.io/badge/SECTION-TECH_STACK-d4a017?labelColor=555555&style=flat-square)](#tech-stack) 
| [![Section](https://img.shields.io/badge/SECTION-DATASET-f07a30?labelColor=555555&style=flat-square)](#dataset) | [![Section](https://img.shields.io/badge/SECTION-DEPLOYMENT-8e0a8e?labelColor=555555&style=flat-square)](#deployment) | [![Section](https://img.shields.io/badge/SECTION-FILES-4b0082?labelColor=555555&style=flat-square)](#Files) |

---

```mermaid
%%{init: {'theme':'base','themeVariables':{
'fontSize':'17px',
'git0':'#0e7490',
'gitBranchLabel0':'#f8fafc',
'cScale0':'#0e7490',
'cScale1':'#92400e',
'cScale2':'#5b21b6',
'cScale3':'#9d174d',
'cScale4':'#991b1b',
'cScale5':'#166534',
'cScaleLabel0':'#f8fafc',
'cScaleLabel1':'#fef3c7',
'cScaleLabel2':'#ede9fe',
'cScaleLabel3':'#fce7f3',
'cScaleLabel4':'#fee2e2',
'cScaleLabel5':'#dcfce7',
'lineColor':'#64748b'
}}}%%
mindmap
  root((Intelligent Dead Reckoning))
    Script A
      Median Filter
      1D Kalman Filter
      Mount Calibration
      Gravity Removal
      ZUPT
    Script B
      2 s Rolling Window
      AI Speed Model
      Evaluation
    Script C
      Heading Estimation
      Dead Reckoning + NHC
      Map Matching
    Script D
      GPS/INS Fusion
      EKF / UKF
      Seamless Handover
    Deployment
      Model Export
      Mobile App
      Edge Engine
```
---
## Problem Statement

```mermaid
flowchart LR
    A["Vehicle enters tunnel / canyon / parking"]:::bad --> B["GNSS signal lost"]:::bad
    B --> C["Nav app freezes or jumps"]:::bad
    C --> D["Missed exits, delays, safety risk"]:::bad
    B --> E["Phone IMU is noisy and biased"]:::warn
    E --> F["Position drifts within seconds"]:::warn
    F --> G["Need: AI/ML dead reckoning + GNSS/INS fusion"]:::good

    classDef bad fill:#5c1a1a,stroke:#e05d44,color:#fff
    classDef warn fill:#5c4a1a,stroke:#d4a017,color:#fff
    classDef good fill:#1a5c2e,stroke:#66b512,color:#fff
```

| Challenge | Why it hurts |
|---|---|
| Consumer MEMS IMU | Bias, thermal noise, error grows exponentially |
| Road vibration | Potholes, engine harmonics, braking |
| No OBD-II speed feed | Speed must come from the phone alone |
| Loose phone mount | Orientation relative to the vehicle is unknown |

---

## Overview

```mermaid
flowchart LR
    subgraph IN["Inputs"]
        I1["Accelerometer XYZ"]
        I2["Gyroscope XYZ"]
        I3["GNSS when available"]
        I4["Offline OSM map"]
    end
    subgraph CORE["Engine"]
        C1["Clean + align signal"]
        C2["AI speed estimate"]
        C3["Heading + path"]
        C4["Map match"]
        C5["GNSS/INS fusion"]
    end
    subgraph OUT["Output"]
        O1["Smooth vehicle icon"]
        O2["Lane-level position"]
    end
    I1 --> C1
    I2 --> C1
    C1 --> C2 --> C3 --> C4 --> C5
    I3 --> C3
    I3 --> C5
    I4 --> C4
    C5 --> O1
    C5 --> O2

    style IN fill:#0d2b45,stroke:#0078d4,color:#fff
    style CORE fill:#0d3b3b,stroke:#008080,color:#fff
    style OUT fill:#2b1a45,stroke:#8e0a8e,color:#fff
```

---

## Architecture

The 10-step pipeline, grouped into the 4 scripts.

```mermaid
flowchart TB
    subgraph A["Script A: Sensor Preprocessing (steps 2-5)"]
        A1["Raw accel + gyro"] --> A2["Trailing median filter"]
        A2 --> A3["Causal 1D Kalman filter"]
        A3 --> A4["Mount calibration"]
        A4 --> A5["Gravity removal"]
        A5 --> A6["ZUPT stationary detection"]
    end
    subgraph B["Script B: AI Speed Model (step 6)"]
        B1["~2 s rolling window"] --> B2["Trained speed estimator"]
    end
    subgraph C["Script C: Trajectory Reconstruction (steps 7-9)"]
        C1["Heading estimation"] --> C2["Dead reckoning + NHC"]
        C2 --> C3["Map matching"]
    end
    subgraph D["Script D: GPS/INS Fusion (step 10)"]
        D1["EKF / UKF blend"] --> D2["Final position + velocity"]
    end

    A6 -->|"clean signal"| B1
    B2 -->|"speed"| C1
    C3 -->|"matched path"| D1
    A6 -.->|"stationary anchor"| A4
    GPS[("GNSS")] -.-> C1
    GPS -.-> D1

    style A fill:#0d3b3b,stroke:#008080,color:#fff
    style B fill:#3b0d3b,stroke:#8e0a8e,color:#fff
    style C fill:#3b2a0d,stroke:#f07a30,color:#fff
    style D fill:#1a3b0d,stroke:#66b512,color:#fff
```
---
## Results so far
1. Noise filtering ![Noise_filtering](Results/Noise_Filtering.png)
3. ZUPT  ![zupt](Results/ZUPT.png)
4. Automatic mount recalibration ![AMR](Results/Automatic_Mount_Recalibration.png)
5. Gravity removal ![gravity_rmoval](Results/Gravity_Removal.png)

---

## Current Status

```mermaid
%%{init: {'theme':'dark'}}%%
flowchart TB
    subgraph TODO["NOT STARTED"]
        direction LR
        T1["Heading estimation"]
        T2["Map matching"]
        T3["GNSS/INS fusion"]
    end

    subgraph WIP["IN PROGRESS"]
        direction LR
        W1["AI speed model"]
        W2["Heading"]
        W3["Dead reckoning<br/>(needs improvement)"]
    end

    subgraph DONE["DONE"]
        direction LR
        D1["Data merge and time-sync verification"]
        D2["Noise filtering"]
        D3["Calibration checks"]
    end

    style DONE fill:#14532d,stroke:#4ade80,color:#dcfce7
    style WIP fill:#713f12,stroke:#fbbf24,color:#fef3c7
    style TODO fill:#334155,stroke:#94a3b8,color:#e2e8f0
```

- **Done:** data merge and time-sync verification, noise filtering, calibration checks
- **In progress:** AI speed model, heading, dead reckoning (needs improvement)
- **Not started:** heading estimation, map matching, GNSS/INS fusion

---

## Tech Stack

> Proposed stack, adjust to what the team finalises.

```mermaid
flowchart TB
    subgraph LANG["Language"]
        PY["Python"]
    end
    subgraph DEV["Development Tools"]
        G["Git / GitHub"]
        J["Jupyter"]
        V["VS Code"]
    end
    subgraph ML["Machine Learning"]
        SK["Scikit-learn"]
        PT["PyTorch"]
        EX["ONNX / TFLite export"]
    end
    subgraph DATA["Data Processing"]
        PD["Pandas"]
        NP["NumPy / SciPy"]
    end
    subgraph MAP["Maps and Filtering"]
        OSM["OpenStreetMap (offline)"]
        KF["EKF / UKF"]
    end
    subgraph VIZ["Visualization"]
        MP["Matplotlib"]
        SB["Seaborn"]
    end
    PY --> DEV
    PY --> ML
    PY --> DATA
    DATA --> MAP
    DATA --> VIZ

    style LANG fill:#3b2a0d,stroke:#f07a30,color:#fff
    style DEV fill:#0d3b3b,stroke:#008080,color:#fff
    style ML fill:#1a3b0d,stroke:#66b512,color:#fff
    style DATA fill:#0d2b45,stroke:#0078d4,color:#fff
    style MAP fill:#3b0d3b,stroke:#8e0a8e,color:#fff
    style VIZ fill:#3b0d1a,stroke:#e05d44,color:#fff

```
---
## Dataset

**IO-VNBD**: Inertial and Odometry benchmark for ground vehicle positioning.

```mermaid
flowchart LR
    D["IO-VNBD"] --> V["V- sets: vehicle CAN bus + GPS (10 Hz)"]
    D --> S["S- sets: smartphone accel, gyro, mag, GPS"]
    S --> SY["Synchronised V + S folder"]
    V --> SY
    style D fill:#0d2b45,stroke:#0078d4,color:#fff
```

| Fact | Value |
|---|---|
| Total data | ~5,700 km over ~98 h |
| Countries | UK, France, Nigeria |
| Sensors | Accelerometer, gyroscope, magnetometer, GPS |
| Scenarios | 32 (hard brake, roundabouts, potholes, rain, motorway, etc.) |
| Stationary data | 20+ min for sensor bias estimation |

---
## Deployment

```mermaid
flowchart LR
    subgraph TRAIN["Cloud / Desktop"]
        T1["Train speed model"] --> T2["Export ONNX / TFLite"]
    end
    subgraph PHONE["Smartphone"]
        P1["Live IMU + GNSS"] --> P2["On-device inference"] --> P3["Navigation UI"]
    end
    subgraph EDGE["Edge Engine"]
        E1["External IMU (e.g. FOG, ~200 Hz)"] --> E2["Same models"] --> E3["Position output"]
    end
    T2 --> P2
    T2 --> E2

    style TRAIN fill:#0d2b45,stroke:#0078d4,color:#fff
    style PHONE fill:#1a3b0d,stroke:#66b512,color:#fff
    style EDGE fill:#3b2a0d,stroke:#f07a30,color:#fff
```
---
## Files

- `Data_preprocessing_verified.py`: merges phone + vehicle data and checks time sync
- `Script_A.py`: noise filtering, calibration, ZUPT
- `Script_B_Part_1`: AI speed model (partial)

| Path | Content |
|---|---|
| `merged_raw.csv` | Merged and timestamp-aligned dataset |
| `script_a_clean_continuous.csv` | Cleaned vehicle-frame dataset |
| `results/per_trip_summary.csv` | Per-trip calibration and validation metrics |
| `results/sensor_noise_estimate.json` | Measured sensor noise variance per axis |
| `figures/*.png` | Validation figures shown above |
| `trip_split_report.csv` | Report of splitting dataset |
| `windowed_data.npz` | Windowed and split dataset |
