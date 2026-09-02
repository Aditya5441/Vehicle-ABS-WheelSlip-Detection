<div align="center">

# 🚗 Vehicle ABS Wheel-Slip Detection

### Simulated CAN network + CAPL logic in Vector CANoe

![Tool](https://img.shields.io/badge/TOOL-CANoe-1f2937?style=for-the-badge&labelColor=1f2937&color=1D4ED8)
![Language](https://img.shields.io/badge/LANGUAGE-CAPL-1f2937?style=for-the-badge&labelColor=1f2937&color=EA580C)
![Bus](https://img.shields.io/badge/BUS-CAN-1f2937?style=for-the-badge&labelColor=1f2937&color=15803D)
![Network](https://img.shields.io/badge/NETWORK-3%20ECUs-1f2937?style=for-the-badge&labelColor=1f2937&color=475569)

![Security](https://img.shields.io/badge/LOGIC-ENGINE%20VS%20WHEEL%20SPEED-1f2937?style=for-the-badge&labelColor=1f2937&color=7C3AED)
![Database](https://img.shields.io/badge/DATABASE-DBC-1f2937?style=for-the-badge&labelColor=1f2937&color=0EA5E9)
![Status](https://img.shields.io/badge/STATUS-SIMULATION%20VALIDATED-1f2937?style=for-the-badge&labelColor=1f2937&color=65A30D)


</div>

---

## 📖 Overview

This project simulates a small automotive CAN network — **Engine ECU**,
**Wheel Speed Sensor ECU**, and **ABS ECU** — inside Vector CANoe, and
implements the ABS/wheel-slip decision logic in **CAPL**.

Every 100 ms, the ABS node compares engine speed against wheel speed. If the
engine is spinning significantly faster than the wheels (i.e. the drive
wheel is slipping), the node raises `ABS_Active` and `Slip_Flag` and
broadcasts the result back onto the bus.

<p align="center">
  <img src="docs/01_main_canoe_workspace_.png" alt="CANoe workspace" width="90%">
</p>

---

## ⚙️ How It Works

```
 ┌──────────────┐      EMS_Data_Msg       ┌─────────────┐
 │  Engine_ECU  │ ───────────────────────▶ │             │
 └──────────────┘      (Engine_Speed)      │   ABS_ECU   │
                                            │ (CAPL logic)│──▶ ABS_Status_Msg
 ┌───────────────────┐  Wheel_Speed_Msg    │             │    (ABS_Active,
 │ Wheel_Sensor_ECU   │ ──────────────────▶ │             │     Slip_Flag)
 └───────────────────┘   (Wheel_Speed)     └─────────────┘
```

**Decision rule** (runs on a 100 ms timer):

```c
if (Engine_Speed > 1.5 * Wheel_Speed)
    ABS_Active = 1;   Slip_Flag = 1;   // slip detected
else
    ABS_Active = 0;   Slip_Flag = 0;   // normal traction
```

---

## 🗂️ Repository Structure

```
Vehicle-ABS-WheelSlip-Detection-CANoe-CAPL/
├── src/
│   └── ABS_SlipDetection.can   # CAPL node logic (ABS_ECU)
├── dbc/
│   └── P_2.dbc                 # CAN database (signals & messages)
├── docs/                       # Workspace screenshots
├── README.md
└── .gitignore
```

---

## 📡 Signals

| Message           | Signal          | Unit    | Description                     |
|--------------------|-----------------|---------|----------------------------------|
| `EMS_Data_Msg`      | `Engine_Speed`  | RPM     | Engine rotational speed          |
| `Wheel_Speed_Msg`   | `Wheel_Speed`   | km/h    | Wheel speed from sensor          |
| `ABS_Status_Msg`    | `ABS_Active`    | –       | 1 = ABS intervention active      |
| `ABS_Status_Msg`    | `Slip_Flag`     | –       | 1 = wheel slip detected          |
| `ABS_Status_Msg`    | `Gear_Position` | Degree  | Current gear position indicator  |

---

## 🖼️ Screenshots

| File | Description |
|---|---|
| <img src="docs/01_main_canoe_workspace_.png" alt="CANoe workspace" width="90%"> | Full CANoe workspace — simulation setup, write, data, trace & graphics windows |
| <img src="docs/02_trace_window.png" alt="CANoe workspace" width="90%"> | CAN trace: `EMS_Data_Msg`, `Wheel_Speed_Msg`, `ABS_Status_Msg` frames |
| <img src="docs/03_simulation_setup.png" alt="CANoe workspace" width="90%">| Node/network setup — ABS_ECU, Engine_ECU, Wheel_Sensor_ECU on CAN 1 |
| <img src="docs/04_measurement_setup.png" alt="CANoe workspace" width="90%">| Measurement setup block diagram |
| <img src="docs/05_gauges_panel.png" alt="CANoe workspace" width="90%">| RPM and km/h gauge panel |
| <img src="docs/06_write_window_log.png" alt="CANoe workspace" width="90%">| Write window log of received Engine Speed values |
| <img src="docs/07_data_window.png" alt="CANoe workspace" width="90%">| Live signal values (ABS_Active, Engine_Speed, Gear_Position, Slip_Flag, Wheel_Speed) |
| <img src="docs/08_graphics_window.png" alt="CANoe workspace" width="90%">| Graphics window plotting signals over time |

---

## ▶️ Usage

1. Open the `.cfg` in **Vector CANoe**.
2. Load `dbc/P_2.dbc` as the CAN database.
3. Attach `src/ABS_SlipDetection.can` to the `ABS_ECU` node.
4. Start the simulated bus and watch `ABS_Active` / `Slip_Flag` update live
   in the Data, Graphics, and Trace windows.

---

<div align="center">
Built and simulated with Vector CANoe · CAPL
</div>
