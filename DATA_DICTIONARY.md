# Data dictionary — churn_dataset.csv (Live betting)

## Churn label definition

| Term | Value |
|------|-------|
| **observation_start_date** | `2025-10-03` |
| **observation_end_date** | `2025-12-31` (model snapshot date) |
| **Outcome window** | the next **30** calendar days after observation_end |
| **churn = 1** | player has no live bets in the outcome window |
| **churn = 0** | player has at least one live bet in the outcome window |

## Columns

| Column | Type | Description |
|--------|------|-------------|
| user_id | string | Unique player ID |
| registration_date | date | Account registration date (always ≤ first_live_bet_date if player has bets) |
| observation_start_date | date | Start of the observation period |
| observation_end_date | date | End of the observation period |
| last_live_bet_date | date | Date of the last live bet in the observation period |
| first_live_bet_date | date | Date of the first live bet in the observation period |
| tenure_days | int | Days from registration to observation_end |
| live_bets_count | int | Number of live bets in the observation period |
| avg_bet_amount | float | Average stake per bet (EUR); equals `total_turnover / live_bets_count` |
| total_turnover | float | Total live turnover in the observation period |
| total_payout | float | Total payout in the observation period |
| ggr | float | turnover − payout |
| ggr_margin | float | ggr / turnover |
| deposit_count | int | Number of deposits in the observation period |
| total_deposit_amount | float | Total deposit amount in the observation period |
| deposit_to_turnover_ratio | float | total_deposit_amount / total_turnover |
| days_active_in_observation | int | Number of days with at least one live bet in the observation period |
| bet_day_rate | float | days_active_in_observation / 90 |
| days_since_last_bet | int | Days from last_live_bet_date to observation_end |
| live_bets_count_outcome_window | int | Number of live bets in the outcome window (after observation_end) |
| churn | int | Target variable (0/1) — **do not use as a feature** |

## Notes

- Data is aggregated at player level (no individual tickets).
- Currency: **EUR**. Product: **live betting**.
- Some columns contain missing values (`avg_bet_amount`, deposits); handle them in your code.
- Review the date definitions and decide which columns are appropriate for model training.
