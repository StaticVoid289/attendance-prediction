# Drop-In Daycare Attendance Forecasting (Time Series + ML)

Forecast short- and medium-term attendance for a drop-in daycare to improve staffing, purchasing, and cash-flow planning. This repo includes data prep, feature engineering, multiple model families (statistical + machine learning + deep learning), and backtesting.

## Objectives

Daily & intraday forecasts (e.g., 15–60 min granularity) for the next 1–8 weeks

Scenario support: school calendars, weather, holidays, price changes, promotions

Operational outputs: staffing targets by hour, expected meals/snacks, capacity alerts

KPIs: RMSE, MAE, MAPE, sMAPE, WAPE; service-level (e.g., “≤±2 children 80% of hours”)

## Data Sources & Schema

Core fact table (attendance_events)

- timestamp (UTC), room/program, check_in_out (in/out)

TODO: Calendars (exogenous/XREG)

- is_school_day, district, break_type (fall/spring/winter), teacher_inservice, no_school_friday

- Local events: parades, markets, holidays, snow days

TODO: Weather (exogenous)

- temp, precip, snow, wind, condition_code

Feature table (daily or intraday)

- Target: y (headcount per bucket or daily total)

## Exploratory Data Analysis (EDA)

Seasonality: weekly pattern (Mon–Fri vs Sat/Sun), Friday “no-school” effects, summer vs school-year

Holidays: Thanksgiving week, winter break, spring break spikes

TODO: Weather: snow/precipitation boosts attendance (parents seek care) vs travel shutdowns

TODO: Intraday: opening ramp, lunch dip, after-school spike; align to bell schedules

## Feature Engineering

Calendar joins (center closures, inservices), federal/state holidays

TODO: Weather joins (daily + intraday), lag/rolling transforms

Leakage-safe train/validation/test splits by time (no shuffling)

## Models

We compare two families and ensemble them:

### Statistical

SARIMAX (targets + XREG calendars/weather)

Pros: interpretable; Cons: weaker nonlinearities

Gradient boosting (XGBoost/LightGBM/CatBoost)

On lagged/rolling features + exogenous

Pros: strong tabular baseline; Cons: needs careful CV

### Prophet

Captures trend/seasonality/holidays quickly

Pros: fast iteration; Cons: limited complex interactions

We also fit a naïve seasonal baseline (e.g., yesterday or last-week same-time) to ground improvements.

## Backtesting & Evaluation

Rolling-origin backtest (time series CV):

Metrics: RMSE, MAPE

Model selection: mean rank across folds + cost-weighted metric (understaffing is worse than overstaffing by factor λ).

## Security & Compliance

No raw PII in the repo; use visit counts only

Align with childcare licensing rules when exporting data and reports

## Roadmap

 Add N-BEATS or TFT baseline for multi-horizon

 Heteroscedastic PI with quantile regression / conformal prediction

 Multi-site hierarchical model (borrow strength across locations)

 Holiday/snow-day nowcasting from district feeds + real-time weather radar

 Cost-aware optimizer that converts forecast → shift recommendations

## License

MIT (or set your preferred license). See LICENSE.