# Yield Curve Comparison Tool
 
![Python](https://img.shields.io/badge/Python-3.10%2B-blue?style=flat-square&logo=python)
![Dash](https://img.shields.io/badge/Dash-Plotly-informational?style=flat-square)
![Status](https://img.shields.io/badge/Status-In%20Development-orange?style=flat-square)
![Data](https://img.shields.io/badge/Data-Bloomberg%20%2F%20ECB-lightgrey?style=flat-square)
 
---
 
> [!WARNING]
> ## Work in Progress
>
> This project is currently under development as part of a personal fixed income analytics initiative.
> The interactive code (Dash application) will be published shortly.
>
> **Coming soon** 

 
---
 
## Preview

 <img width="2592" height="1765" alt="yield_curve_final_v3" src="https://github.com/user-attachments/assets/a7ef1980-f4ac-4628-96a0-0fed0ad7a7f8" />

*Interactive dashboard — current curve (solid blue) vs. historical snapshot (dashed orange),  
with country tabs, date slider, manual date input, and key rate metrics.*
 
---
 
## Project Overview
 
This tool is a **rates strategy dashboard** built to visualise and compare sovereign yield curves across time and geographies.
 
The core use case is curve movement analysis: understanding how the shape of a yield curve has shifted between two dates, and what that implies in terms of central bank policy, inflation expectations, and term premium.

 
**Key capabilities:**
 
- Overlay the current yield curve against any historical snapshot selected via slider or manual date entry
- Identify and quantify curve movements (parallel shifts, steepening, flattening, butterfly)
- Display key metrics: per-maturity spreads (in bp) and 2s10s slope for both dates
- Switch between countries with a single click

**Data source:** The tool reads from a standardised Excel template compatible with any data provider : Bloomberg Terminal, ECB Data Portal, FRED, or manual entry.
 
---
 
## Usage
 
### 1. Populate the data template
 
Fill `yield_curve_template.xlsx` with your own data following the schema below, or use the mock data already included.
 
**Sheet `yield_curve`** : one row per (date, country) snapshot:
  
> Rates in % (e.g. `2.44` = 2.44%). Date format: `YYYY-MM-DD`.

**Sheet `rate_history`** : time series for a single maturity point:

 
### 2. Run the data loader
 
```python
from data_loader import load, get_curve, get_history
 ```
 
### 3. Launch the dashboard *(coming soon)*
 
```bash
pip install -r requirements.txt
python app.py
# Open http://localhost:8050
```
 
## Requirements
 
```
pandas
openpyxl
scipy
plotly
dash
```
 
---
 
## Yield Curve Movements — Reference Guide
 
Understanding what changes between two curve snapshots is the analytical core of this tool. Below are the four canonical curve movements used in fixed income strategy.
 
---
 
### 1. Parallel Shift
 
**What it is:** All maturities move by approximately the same number of basis points — the curve moves up or down uniformly without changing its shape.
 
**What it signals:** A broad repricing of the risk-free rate, typically driven by a change in central bank policy expectations that affects the entire term structure. A hawkish surprise produces an upward parallel shift; a dovish pivot produces a downward one.
 
**How to read it on the chart:** The two curves run roughly parallel — the gap between them is consistent across all maturities.
 
**Real-world example:** The ECB hiking cycle of 2022 produced a near-parallel upward shift of ~200bp across the entire German sovereign curve within 12 months.

 <img width="1603" height="487" alt="move_parallel" src="https://github.com/user-attachments/assets/cc7e8660-a60a-444c-bcb2-2f6e7ceb9620" />

---
 
### 2. Steepening
 
**What it is:** The spread between short-term and long-term rates widens — the curve becomes steeper. The 2s10s spread (10Y minus 2Y) increases.
 
Two distinct variants:
 
- **Bear Steepening** — long-end rates rise faster than short-end rates. The curve steepens as long-term yields sell off, often driven by rising inflation expectations or term premium at the long end.
- **Bull Steepening** — short-end rates fall faster than long-end rates. Typically occurs when the market prices in imminent rate cuts while long-term inflation expectations remain anchored.
**What it signals:** Bear steepening reflects risk-off dynamics or rising supply concerns at the long end. Bull steepening is the classic early signal of an easing cycle — the front end rallies in anticipation of cuts while the long end lags.
 
**How to read it on the chart:** The two curves diverge toward the right (long maturities). A rising 2s10s spread in the stats bar confirms steepening.
 
**Real-world example:** Early 2024 showed bull steepening in the eurozone as markets began pricing ECB cuts, with the 2Y Bund falling faster than the 10Y.

 <img width="1603" height="487" alt="move_steepening" src="https://github.com/user-attachments/assets/e800a754-1a6c-4eb6-815a-2c09c95738c1" />

---
 
### 3. Flattening
 
**What it is:** The spread between short-term and long-term rates narrows — the curve becomes flatter. The 2s10s spread decreases, and can turn negative (curve inversion).
 
Two variants:
 
- **Bear Flattening** — short-end rates rise faster than long-end rates. The front end sells off as the market aggressively prices in rate hikes. The most common form during tightening cycles.
- **Bull Flattening** — long-end rates fall faster than short-end rates. A growth slowdown or flight-to-quality narrative pulls long yields down while short rates remain sticky.
**What it signals:** Bear flattening is the classic signature of a central bank hiking cycle. When 2s10s goes negative (inversion), it has historically been a reliable leading indicator of recession. Bull flattening signals slowing growth expectations with delayed monetary easing.
 
**How to read it on the chart:** The two curves converge toward the right. A falling or negative 2s10s in the stats bar confirms flattening.
 
**Real-world example:** 2022–2023 saw aggressive bear flattening in the US and Europe as the Fed and ECB hiked, pushing 2Y rates above 10Y rates and producing the deepest US curve inversion since the 1980s.

<img width="1603" height="487" alt="move_flattening" src="https://github.com/user-attachments/assets/05695246-3c5e-4ee6-a6e6-05efd3257d28" />
---
 
### 4. Butterfly / Twist
 
**What it is:** The belly of the curve (medium-term maturities: roughly 2Y–7Y) moves differently from the wings (short end and long end). A **positive butterfly** means the belly rises relative to the wings. A **negative butterfly** means the belly drops — mid-curve inversion or depression relative to the wings.
 
**What it signals:** Butterfly movements reflect uncertainty about the *timing* or *pace* of the rate cycle rather than just its direction. They often occur around central bank decision points where the market debates whether cuts will be front-loaded or gradual, or in response to specific supply/demand dynamics at intermediate maturities.
 
The butterfly spread is measured as: `2 × belly rate − (short wing + long wing)`. Positive = belly elevated; Negative = belly depressed.
 
**How to read it on the chart:** The mid-curve (2Y–5Y) deviates noticeably from a smooth interpolation between the wings — the curve develops a visible hump or dip in the middle.
 
**Real-world example:** During the ECB's 2023 pause, the 5Y Bund temporarily traded richer than surrounding maturities, creating a negative butterfly as the market debated whether the first cut would come in March or June 2024.
 
<img width="1603" height="487" alt="move_butterfly" src="https://github.com/user-attachments/assets/0b44decf-4b58-4136-96ca-8005b2e6ff1a" /> 
---
 

---
 
*Personal project — fixed income rates strategy analytics.*  
*Data: Bloomberg Terminal / ECB Statistical Data Warehouse / FRED.*
