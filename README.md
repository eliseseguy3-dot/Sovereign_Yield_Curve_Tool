# Yield Curve Analysis Tool · APAC

## Preview

 <img width="1841" height="852" alt="image" src="https://github.com/user-attachments/assets/a944f780-2946-490b-9c40-8ade73e51b26" />

👉 Try it: **[Interactive Yield Curve Dashboard](https://eliseseguy3-dot.github.io/Sovereign_Yield_Curve_Tool/yield_curve_APAC.html)**

*Interactive dashboard - current curve (solid) vs. historical snapshot (dashed orange),
with country tabs, synced date slider + selector, live curve animation, policy-rate overlays,
hover tooltips, and key rate metrics.*

---
> ⚠️ **Disclaimer:** The data currently shipped with this tool is test data for demonstration purposes only - no accuracy, completeness, or reliability of the model or its outputs is guaranteed.

---

## Project Overview

This tool is a **rates dashboard** built to visualise and compare sovereign yield curves across time and across the APAC region.

The core use case is curve movement analysis: understanding how the shape of a yield curve has shifted between two dates, and what that implies in terms of central bank policy, inflation expectations, and term premium.

**Coverage:** Japan (JP), China (CN), Singapore (SG), South Korea (KR), India (IN) - each with its own central bank (BoJ, PBOC, MAS, BOK, RBI) and local-currency sovereign curve.

**Key capabilities:**

- Overlay the current yield curve against any historical snapshot, selected via a top date-picker or a slider (the two stay in sync)
- **Live curve animation** - drag the slider to watch the curve morph in real time, or press Play to sweep through every date automatically
- **Policy-rate overlays** - horizontal dotted lines show the central-bank policy rate in effect at each date, so you can see the curve relative to the anchor
- **Hover tooltips** - a vertical guide line follows the cursor and a box reports the current yield, historical yield, delta, and both policy rates at that maturity
- Identify and quantify curve movements (parallel shifts, steepening, flattening, butterfly)
- Key metrics: per-maturity spreads (2Y / 5Y / 10Y / 30Y, in bp), 2s10s slope for both dates, last central-bank decision, and next scheduled meeting with a live countdown

---

## Why a standalone HTML file?

The entire dashboard is a **single self-contained HTML file** (`yield_curve_APAC.html`): the data is embedded directly inside it, and the only external dependency (Plotly.js) is loaded from a CDN. This was a deliberate choice:

- **Zero install, zero server.** A recruiter or colleague just opens the file in any browser - no Python, no `pip install`, no local server to spin up. This matters a lot when sharing a demo: the friction of "set up an environment first" is exactly what stops people from ever trying it.
- **Shareable as a live link.** Because it is pure client-side HTML/JS, it can be hosted for free on GitHub Pages and shared as a single URL that works instantly.
- **Genuinely fluid interaction.** A native `<input type="range">` slider fires on every pixel of movement, so the curve animates *live* while dragging. Server-based frameworks (Streamlit, Dash) only update once the slider is released, because every change round-trips to a Python backend - which kills the "watch the curve evolve" effect this project is built around.
- **Self-documenting and portable.** One file is trivial to version, email, or archive. There is no build step and nothing to break between machines.

The trade-off is that heavy data processing has to happen in JavaScript rather than Python. For this project that is a non-issue - the curve maths (cubic-spline smoothing, spread and slope calculations) is light and runs instantly in the browser. The data prep that *does* benefit from Python (reading Bloomberg exports, cleaning the Excel template) is done offline, once, and the result is baked into the file.

---

## Usage

### 1. Prepare the data

Fill `yield_curve_template_APAC.xlsx` following the schema below, or use the mock data already included (realistic BoJ / PBOC / MAS / BOK / RBI dynamics, 2022–2025).

**Sheet `yield_curve`** - one row per (date, country) snapshot, columns `3M ... 30Y`.

> Rates in % (e.g. `2.44` = 2.44%). Date format: `YYYY-MM-DD`.

**Sheet `rate_history`** - time series for a single maturity point.

**Sheet `cb_events`** - central-bank events: last policy change (date, bp, resulting rate level) and next scheduled meeting per country.

### 2. Open the dashboard

```
Double-click yield_curve_APAC.html
```

That's it - it opens in your browser, no installation required. To share it as a live link, drop it into a GitHub Pages repo.

---

## Yield Curve Movements - Reference Guide

Understanding what changes between two curve snapshots is the analytical core of this tool. Below are the four canonical curve movements used in fixed income strategy.

---

### 1. Parallel Shift

**What it is:** All maturities move by approximately the same number of basis points - the curve moves up or down uniformly without changing its shape.

**What it signals:** A broad repricing of the risk-free rate, typically driven by a change in central bank policy expectations that affects the entire term structure. A hawkish surprise produces an upward parallel shift; a dovish pivot produces a downward one.

**How to read it on the chart:** The two curves run roughly parallel - the gap between them is consistent across all maturities.

**Real-world example:** The ECB hiking cycle of 2022 produced a near-parallel upward shift of ~200bp across the entire German sovereign curve within 12 months. In APAC, the BoJ was the notable exception - Yield Curve Control kept the JGB curve pinned while peers repriced.

 <img width="1603" height="487" alt="move_parallel" src="https://github.com/user-attachments/assets/cc7e8660-a60a-444c-bcb2-2f6e7ceb9620" />

---

### 2. Steepening

**What it is:** The spread between short-term and long-term rates widens - the curve becomes steeper. The 2s10s spread (10Y minus 2Y) increases.

Two distinct variants:

- **Bear Steepening** - long-end rates rise faster than short-end rates. The curve steepens as long-term yields sell off, often driven by rising inflation expectations or term premium at the long end.
- **Bull Steepening** - short-end rates fall faster than long-end rates. Typically occurs when the market prices in imminent rate cuts while long-term inflation expectations remain anchored.

**What it signals:** Bear steepening reflects risk-off dynamics or rising supply concerns at the long end. Bull steepening is the classic early signal of an easing cycle - the front end rallies in anticipation of cuts while the long end lags.

**How to read it on the chart:** The two curves diverge toward the right (long maturities). A rising 2s10s spread in the stats bar confirms steepening.

**Real-world example:** China's CGB curve bull-steepened through 2024 as the PBOC eased aggressively, pulling the front end down while the long end lagged.

 <img width="1603" height="487" alt="move_steepening" src="https://github.com/user-attachments/assets/e800a754-1a6c-4eb6-815a-2c09c95738c1" />

---

### 3. Flattening

**What it is:** The spread between short-term and long-term rates narrows - the curve becomes flatter. The 2s10s spread decreases, and can turn negative (curve inversion).

Two variants:

- **Bear Flattening** - short-end rates rise faster than long-end rates. The front end sells off as the market aggressively prices in rate hikes. The most common form during tightening cycles.
- **Bull Flattening** - long-end rates fall faster than short-end rates. A growth slowdown or flight-to-quality narrative pulls long yields down while short rates remain sticky.

**What it signals:** Bear flattening is the classic signature of a central bank hiking cycle. When 2s10s goes negative (inversion), it has historically been a reliable leading indicator of recession. Bull flattening signals slowing growth expectations with delayed monetary easing.

**How to read it on the chart:** The two curves converge toward the right. A falling or negative 2s10s in the stats bar confirms flattening.

**Real-world example:** 2022–2023 saw aggressive bear flattening in the US and Europe as the Fed and ECB hiked, pushing 2Y rates above 10Y rates and producing the deepest US curve inversion since the 1980s.

<img width="1603" height="487" alt="move_flattening" src="https://github.com/user-attachments/assets/05695246-3c5e-4ee6-a6e6-05efd3257d28" />

---

### 4. Butterfly / Twist

**What it is:** The belly of the curve (medium-term maturities: roughly 2Y-7Y) moves differently from the wings (short end and long end). A **positive butterfly** means the belly rises relative to the wings. A **negative butterfly** means the belly drops - mid-curve inversion or depression relative to the wings.

**What it signals:** Butterfly movements reflect uncertainty about the *timing* or *pace* of the rate cycle rather than just its direction. They often occur around central bank decision points where the market debates whether cuts will be front-loaded or gradual, or in response to specific supply/demand dynamics at intermediate maturities.

The butterfly spread is measured as: `2 x belly rate - (short wing + long wing)`. Positive = belly elevated; Negative = belly depressed.

**How to read it on the chart:** The mid-curve (2Y-5Y) deviates noticeably from a smooth interpolation between the wings - the curve develops a visible hump or dip in the middle.

**Real-world example:** During the ECB's 2023 pause, the 5Y Bund temporarily traded richer than surrounding maturities, creating a negative butterfly as the market debated whether the first cut would come in March or June 2024.

<img width="1603" height="487" alt="move_butterfly" src="https://github.com/user-attachments/assets/0b44decf-4b58-4136-96ca-8005b2e6ff1a" />

---

## Metrics Displayed

| Metric | Definition | Interpretation |
|---|---|---|
| **2Y / 5Y / 10Y / 30Y Spread** | Current rate minus historical rate, in bp | Green = rates rose - Orange = rates fell |
| **2s10s Current / Historical** | 10Y minus 2Y at each date | Positive = normal slope - Negative = inverted |
| **Last Decision** | Most recent central-bank move | Date, bp change, resulting policy rate |
| **Next Meeting** | Next scheduled central-bank meeting | Date + live countdown |

---

*Personal project - fixed income rates strategy analytics.*
