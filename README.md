# Subsea Pump Performance Monitoring & Anomaly Detection

![Dashboard](pump_dashboard.png)

A Python-based project simulating operational sensor data (pressure, flow
and temperature) from a subsea pumping system, and applying condition-
monitoring techniques — anomaly detection, a composite health score, and a
rule-based root-cause hypothesis — to flag performance degradation early
and explain what might be causing it.

## Why this project

Remote condition monitoring of subsea equipment relies on continuously
analysing pressure, flow and temperature (PVT) data to catch performance
deviations before they become failures, and on turning that analysis into
something a project team or client can act on. This project simulates that
workflow end to end: generating PVT-coupled sensor data with a gradual
fault injected partway through, detecting the resulting anomaly, flagging
the early-warning trend before it fully develops, scoring overall equipment
health with one combined number, and producing a report that groups
detected events together with a hypothesis for what likely caused each one
— similar in spirit to a monthly performance/status report.

## What's in this repo

| File | Description |
|---|---|
| `create_data.py` | Generates simulated pump sensor data (flow, pressure, temperature) with a gradual, PVT-coupled fault injected partway through (pressure drops, flow drifts up, temperature rises), and saves it to `pump_data.csv`. |
| `analyze_data.py` | Defines a `SubseaMonitor` class that loads `pump_data.csv`, detects anomalies against a rolling baseline, computes a composite health score, generates a root-cause hypothesis report (`rca_report.csv`), and builds a 4-panel dashboard (`pump_dashboard.png`). |
| `requirements.txt` | Python packages needed to run the project. |
| `pump_data.csv` | Generated sensor data (created after running `create_data.py`). |
| `rca_report.csv` | Detected anomaly events grouped together, each with duration, severity and a root-cause hypothesis (created after running `analyze_data.py`). |
| `pump_dashboard.png` | The final 4-panel dashboard image (created after running `analyze_data.py`). |

## Method

1. **Data simulation** — flow, pressure and temperature are generated with
   realistic relationships (pressure and temperature both depend on flow),
   and a gradual performance drop is injected over a 200-reading window to
   mimic real equipment degradation rather than a sudden step change. As
   pressure drops during the fault, flow is also nudged upward — a
   simplified stand-in for the PVT effect where a drop in pressure (and
   rise in temperature) reduces fluid density, so volumetric flow rate
   rises to carry the same mass flow.
2. **Anomaly detection** — a rolling mean and standard deviation (50-reading
   / ~8-hour window) define what "normal" looks like at each point in
   time, and any pressure reading more than 2.5 standard deviations from
   that rolling baseline is flagged.
3. **Early warning** — a rolling rate-of-change on pressure flags a
   sustained downward trend, so degradation can be surfaced before it
   fully deviates from normal.
4. **Composite health index** — Pressure, Flow and Temperature are each
   converted to a Z-score and averaged into one Composite_Health value per
   reading, with a `System_Alert` flag above a threshold of 2.0 — a single
   number reflecting overall equipment state rather than three separate
   charts.
5. **Root-cause hypothesis** — flagged readings are grouped into discrete
   events, and each event's average Pressure/Flow/Temperature is compared
   against the dataset baseline to suggest a likely cause (e.g. pressure
   down + temperature up → suspected impeller wear/recirculation). This is
   a simplified rule-of-thumb for demonstration, not a validated
   diagnostic model.
6. **Visualisation** — a 4-panel dashboard shows the pressure time series
   with detected events and flagged points, the early-warning
   rate-of-change signal, a flow–pressure scatter coloured by temperature
   (the PVT relationship), and the composite health index over time
   against its alert threshold.

## How to run

```bash
pip install -r requirements.txt
python create_data.py
python analyze_data.py
```

This produces `pump_data.csv`, `rca_report.csv` and `pump_dashboard.png`.

## Tools used

Python, Pandas, NumPy, Matplotlib

## Note on the data

The sensor data in this project is synthetically generated for
demonstration purposes; it is not real operational data from any equipment
or company. Detection thresholds and root-cause logic are illustrative
values chosen to behave sensibly on this synthetic dataset, not values
calibrated against real equipment or failure history.
