# Steel Beam Cutting Dashboard (Grant Vessels case)

Reads a CSV (ItemNumber, Length (mm), Number of beams to cut), plans every batch
(one row = one batch, fresh beams, leftovers are loss) from 6000/8000/13000 mm
stock with minimum waste, and shows the results in a table, summary and charts.

## Run
    pip install -r requirements.txt
    python main.py                      # dashboard (needs Tkinter)
    python main.py data.csv --cli --export results.csv --chart chart.png
    python -m unittest discover -s tests -v

## Method
Per batch of size s and quantity n, a beam of length L yields floor(L/s) pieces.
Dynamic programming finds the least total beam length giving >= n pieces
(ties: fewest beams). Total component length is fixed, so least beam length =
least waste, so the result is optimal. Brute force (test oracle) and an adapted
First Fit Decreasing (baseline column "FFD waste") are included.

## Layout
    beamcut/loader.py     CSV validation, errors with line numbers
    beamcut/optimiser.py  DP solver, brute force, FFD baseline, plan verification
    beamcut/reporter.py   per-batch rows, totals, CSV export
    beamcut/charts.py     volume-vs-waste scatter + waste histogram
    beamcut/dashboard.py  Tkinter UI (background thread, double-click = cut list)
    tests/test_all.py     tests named after the test plan in the report (T1..T13)

## Decisions to confirm / note in your report
- Quantity 0 is accepted as an empty batch (test T5); negative or non-numeric is an error.
- Loader uses the standard csv module (not pandas) to get exact line numbers.
- Unlimited stock, no kerf, rows never merged (as stated in your assumptions).
- Package with:  pyinstaller --onefile --windowed main.py
