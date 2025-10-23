├── notebook_1.ipynb ← file (Colab notebook)
├── csv_files/ ← folder (raw + processed CSVs)
│ ├── historical_data.csv ← file (raw trade data - rename if needed)
│ ├── fear_greed.csv ← file (raw sentiment data)
│ └── daily_summary.csv ← file (processed aggregation produced by the notebook/script)
├── outputs/ ← folder (figures & generated outputs)
│ ├── pnl_boxplot.png
│ ├── leverage_dist.png
│ └── clusters_scatter.png
├── ds_report.pdf ← file (analysis summary / deliverable)

## Quick start — run in Google Colab (recommended)

1. Open `notebook_1.ipynb` in Google Colab.
2. Upload the two raw CSVs into `csv_files/` (or use the notebook cell upload widget). Required raw files:
   - `historical_data.csv` (trades)
   - `fear_greed.csv` (sentiment) — may also be `fear_greed_index.csv`, `fear_greed.csv`, or `sentiment.csv`
3. Run all cells (Runtime → Restart and run all if needed).
4. The notebook will produce:
   - `ds_Saiteja_Puppala/csv_files/daily_summary.csv`
   - figures in `ds_Saiteja_Puppala/outputs/`

If you want to mount Drive to persist results, add `drive.mount('/content/drive')` and set `ROOT = Path('/content/drive/MyDrive/ds_Saiteja_Puppala')` in the notebook.

## Quick start — run locally

1. Put the two CSVs in `ds_Saiteja_Puppala/csv_files/`:
   - `historical_data.csv`
   - `fear_greed.csv`
2. Install dependencies (recommended in a venv):
   ```bash
   pip install pandas numpy matplotlib seaborn scipy scikit-learn statsmodels
3.Example (if using the script produce_daily_summary.py):
  python produce_daily_summary.py
4.Outputs will be saved in ds_Saiteja_Puppala/csv_files/daily_summary.csv and images in ds_Saiteja_Puppala/outputs/.

Expected input files & recommended columns
  historical_data.csv (raw trades) — useful columns (common names accepted)
    account — string (account id). Alternatives: account_id, user_id.
    time or timestamp or date — trade timestamp (ISO or epoch).
    symbol — traded symbol (e.g., BTC-USD).
    side — buy / sell (or long/short).
    size — numeric (signed or unsigned; script computes absolute volume).
    execution_price or price — numeric.
    closedpnl or pnl or realized_pnl — numeric realized P&L.
    leverage — numeric (optional).

The pipeline attempts to auto-detect and standardize common alternatives. If your CSV uses different names, either rename columns or adjust the notebook/script mapping.

  fear_greed.csv (sentiment) — expected columns
    timestamp — epoch (int) or ISO datetime (string). The notebook converts int epoch to datetime automatically.
    value — numeric sentiment score (0–100).
    classification — string (Fear, Greed, Extreme Fear, etc.)
    date — optional; pipeline derives it from timestamp.
  Output (daily_summary.csv) — columns & meaning
    The processed file saved to csv_files/daily_summary.csv will contain (at minimum):
    Date — YYYY-MM-DD (UTC date)
    Classification — daily sentiment (Fear, Greed, or NoLabel)
    sent_value — numeric sentiment index (if available)
    sent_bin — convenience: 1 for Greed, 0 for Fear, -1 for missing
    trade_count — integer number of trades on that day
    unique_accounts — number of distinct accounts active that day
    volume — sum of absolute trade sizes (total volume)
    net_volume — signed sum of sizes (buys positive)
    avg_trade_size — mean absolute trade size
    total_pnl — sum of closed PnL for the day (NaN if closedPnL missing)
    avg_pnl — mean closedPnL per trade
    pnl_std — stddev of closedPnL
    win_rate — fraction of trades with closedpnl > 0 (NaN if not available)
    median_leverage, avg_leverage — leverage stats (if present)
    *_lag1 — lag-1 features for selected columns (e.g., total_pnl_lag1)


