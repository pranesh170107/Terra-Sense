# TERRA-SENSE — Terrain Intelligence Platform

Real-time Carbon Absorption Rate (CAR) monitoring system for NH-39 Defense Corridor, Singrauli, Madhya Pradesh. 14 active open-cast coal mines destroy soil microorganisms causing ground instability and road subsidence. TERRA-SENSE detects, predicts, and recommends remediation for terrain degradation before collapse occurs.

---

## Problem

Singrauli hosts **14 active open-cast mines** along the NH-39 defense corridor. Mining destroys soil microorganisms, biological glue breaks down, CAR drops, ground collapses without warning. **23 road subsidence incidents in 2023** despite all mines being legally compliant. Zero real-time CAR monitoring existed before this system.

---

## CAR Scale

| Range | Status |
| --- | --- |
| 0.65 – 1.00 | 🟢 STABLE |
| 0.40 – 0.65 | 🔵 WATCH |
| 0.25 – 0.40 | 🟡 WARNING |
| 0.00 – 0.25 | 🔴 CRITICAL |

---

## Hardware

ESP32-S3 solar-powered Tactical Soil Stake planted every 500m along corridor.

- **MQ-135** — CO2 (380–1200 PPM)
- **Capacitive Sensor** — Soil Moisture (0–100%)
- **DHT22** — Temperature + Humidity
- **SW-420** — Vibration (mining detection only)
- **NDVI** — Satellite vegetation index

Data transmitted via Serial at 115200 baud. AES-128 encrypted before LoRa transmission.

---

## AI Models

### XGBoost Regressor
Predicts current CAR from 8 live sensor features.
- 200 trees, max depth 6, learning rate 0.05
- **R² = 0.91 | RMSE = 0.042**

### LSTM Forecaster
Predicts CAR 24 hours ahead using 48-reading history window.
- 2 layers, 64 hidden cells, dropout 0.2, 30 epochs
- **Loss = 0.0038**

### Isolation Forest
Detects anomalies — illegal mining, sinkholes, blasts.
- 100 trees, contamination 5%
- **Detection accuracy = 96% | Anomaly rate = 4.97%**

---

## CAR Formula

CAR = (NDVI × 0.35) + (Moisture_normalized × 0.25) + (CO2_inverse × 0.25) + (Temperature_stress × 0.15)

> [!NOTE]
> Vibration is **not** part of CAR. It is used only for illegal mining detection.

---

## Illegal Mining Detection

| Signal | Points |
| --- | --- |
| CAR drop > 0.3 in 24h | +40 pts |
| Vibration detected | +35 pts |
| Night hours (10PM–5AM) | +12 pts |

- Above **30%** → Investigate
- Above **60%** → Alert HQ
- Above **80%** → 🚨 Classified Defense Alert

---

## Dataset

`singrauli_sensor_data.csv` — 43,200 rows, 180 days, 5 zones, one reading every 30 minutes. All 3 models trained and validated on this dataset.

---

## Tech Stack

| Layer | Technology |
| --- | --- |
| Hardware | ESP32-S3, MQ-135, DHT22, SW-420, Capacitive |
| Firmware | Arduino C++ with AES-128 encryption |
| Backend | FastAPI + Python 3.10 |
| ML | XGBoost, PyTorch LSTM, Scikit-learn |
| Database | MongoDB 7.0 + SQLite (backup) |
| Dashboard | Pure HTML + Chart.js |

---

## How to Run

**1. Install dependencies**

```bash
pip install fastapi uvicorn xgboost torch scikit-learn pandas numpy pyserial pymongo openpyxl cryptography
```

**2. Install MongoDB**

Download MongoDB Community Server and install with defaults. Runs automatically on port 27017.

**3. Connect ESP32**

Install CP210x driver from silabs.com. Check Device Manager for COM port. Backend auto-detects COM12, COM4, COM5, /dev/ttyUSB0.

**4. Start backend**

```bash
uvicorn backend.app:app --reload --port 8000
```

**5. Open dashboard**

Open `terrasense_final_v5.html` in Chrome. Auto-detects ESP32. If disconnected, falls back to dataset values automatically.

---

## Zone Status

| Zone | Location | CAR | Status |
| --- | --- | --- | --- |
| Zone 1A | KM 0–2 | 0.196 | 🔴 CRITICAL |
| Zone 2B | KM 2–4 | 0.413 | 🟡 WARNING |
| Zone 3C | KM 4–6 | 0.412 | 🟡 WARNING |
| Zone 4D | KM 6–8 | 0.587 | 🔵 WATCH |
| Zone 5E | KM 8–10 | Live | 🟢 ESP32 Baseline |

---

## Remediation — BRaaS

**Biological Remediation as a Service** — auto-generated 3-phase plan:

- **Immediate** — Reroute convoys via NH-75 (+23 min), deploy MLC steel plates
- **Short Term** — Backfill cavities with fly ash, install geo-drainage channels
- **Long Term** — Plant _Chrysopogon zizanioides_ (Vetiver grass)

> [!TIP]
> Vetiver roots grow **4 meters deep**, evolved for dry tropical climates, stitches damaged soil layers like biological rebar. CAR recovers from **0.196 → 0.55+** in 90 days.

---

_TERRA = Latin for Earth/Soil | SENSE = Detect/Monitor_
