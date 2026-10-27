# Keystay — Booking Assurance for Hotels

**Predicting hotel booking cancellations to cut revenue loss.**
An AI system that flags risky reservations at booking time and turns each prediction into a revenue decision: a reminder, a deposit, an offer, or no action.

Built on **119,390 real hotel bookings** (Kaggle *Hotel booking demand*) and applied to the Indian hospitality context (OYO, Taj, MakeMyTrip).

> **Live demo:** `https://<your-username>.github.io/keystay/` (see *Deploy* below)

---

## Highlights

| | |
|---|---|
| Bookings analysed | 119,390 (Jul 2015 – Aug 2017) |
| Cancellation rate | 37.0% |
| Revenue lost to cancellations | ≈ ₹158.9 Cr (€16.73 M at €1 = ₹95) |
| Model | Logistic regression, 35 features, trained on 2015–16 and tested on unseen 2017 bookings |
| Test ROC-AUC | **0.858** (accuracy 78.0%, precision 78.1%) |
| Policy impact (one season) | ≈ ₹9.85 Cr net revenue protected |

## Features

- **Login and sign-up** with password strength meter (demo authentication: accounts are stored only in the visitor's browser)
- **Refresh-safe routing**: reloading keeps you on the same page, and filters and sliders are remembered
- **Overview**: revenue lost, cancellation rate, monthly trend, with hotel / year / exchange-rate filters
- **Cancellation drivers**: lead time, segment, special requests, deposit type, guest history, country
- **Prediction model**: Phase 1 vs Phase 2 metrics, ROC curve, interactive confusion matrix, coefficient chart
- **Booking risk scorer**: score any reservation live, with plain-language reasons and a recommended action
- **Revenue policy simulator**: ₹ impact of a deposit/reminder policy at every risk threshold
- **Guest risk tiers**: every 2017 booking placed in Secure / Watch / At risk, with the actual cancellation rate and recommended action for each tier
- **A/B test lab**: sample-size planner and two-proportion z-test with confidence intervals
- **Upload & score (add-on)**: upload any bookings CSV in the Kaggle format. Every row gets a cancellation probability, risk tier and recommended action, and you can download the scored CSV. If the file has `is_canceled`, the model is also evaluated on it. Try it with `data/sample_bookings.csv`.

All analysis, including model training, runs **in the browser** on the real dataset. There is no backend.

## Project structure

```
keystay/
├── index.html              # app shell: auth + 8 dashboard pages
├── styles.css              # design system (white linen, ink navy, brass accent)
├── app.js                  # auth, routing, charts, model training, simulators, CSV scoring
├── data.js                 # compact encoding of the dataset (generated)
├── data/
│   ├── hotel_bookings.csv  # original Kaggle dataset (119,390 × 32)
│   └── sample_bookings.csv # 500 real 2017 bookings to try the upload page
├── scripts/
│   └── prepare_data.py     # rebuilds data.js from the CSV (standard library only)
└── docs/
    └── PROJECT_REPORT.md   # full project report: problem, data, method, results
```

## Run locally

Open `index.html` in any modern browser. It needs an internet connection for fonts and Chart.js.

To regenerate `data.js` from the CSV:

```bash
python scripts/prepare_data.py data/hotel_bookings.csv data.js
```

## Deploy (GitHub Pages)

1. Push this folder to a public repository named `keystay`.
2. On GitHub, open **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, then branch `main` and folder `/ (root)`, and click **Save**.
4. After about a minute the site is live at `https://<your-username>.github.io/keystay/`.

## Methodology in brief

1. **Leakage control:** removed `reservation_status`, `reservation_status_date`, `assigned_room_type` and `booking_changes`, which are only known after the outcome.
2. **Time-based validation:** train on 2015–16 arrivals (78,703), test on 2017 arrivals (40,687).
3. **Two-phase model:** booking basics only (ROC-AUC 0.680), then guest history, channel and deposit added (ROC-AUC 0.858).
4. **Decision layer:** risk tiers (Secure / Watch / At risk), a ₹ impact simulator and an A/B test design. GenAI guest messaging is documented in the project report.

See [`docs/PROJECT_REPORT.md`](docs/PROJECT_REPORT.md) for the full write-up.

## Dataset and citation

- Kaggle: [Hotel booking demand](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand)
- Antonio, N., de Almeida, A., & Nunes, L. (2019). *Hotel booking demand datasets.* **Data in Brief**, 22, 41–49. https://doi.org/10.1016/j.dib.2018.11.126

The data comes from two hotels in Portugal and is used here as a proxy for an Indian hotel chain. Prices are in euros, converted at an editable rate.

## Disclaimer

Academic project. A/B test figures are illustrative until a real pilot is run, and GenAI messages need human review before sending. Sign-in is a front-end demo, so don't reuse a real password.
