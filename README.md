# DealSense

DealSense is a local e-commerce price-intelligence demo. It compares a listing's current price with an ML-estimated fair price and stored price history, then explains the deal score and category. Product prices, histories and model training observations bundled with this project are synthetic demonstrations, not live retailer data.

## Run locally

Requirements: Python 3.10 or newer and an internet connection for the first dependency installation. Product photos and the optional Google Fonts load from the web; the app and its analysis still work if those external assets are unavailable.

### Windows PowerShell

```powershell
cd path\to\DealSense
py -3 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python app.py
```

### macOS / Linux

```bash
cd path/to/DealSense
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python app.py
```

Open [http://127.0.0.1:8000](http://127.0.0.1:8000). A trained model is included. On first start the app creates `data/dealsense.sqlite3`, importing products and price history from the bundled catalog. If the model artifact is missing, its feature schema has changed, or its scikit-learn version differs, the app retrains and refreshes the model report. Stop the server with `Ctrl+C`.

## Use the app

- Search the sample shelf by product name, brand or category, then analyze the listing.
- Use **Enter a product manually** to check a product not in the sample catalog.
- Review its estimated fair price, stored history, MRP discount check, factors and similar products.
- Compare two products from the Analyze view.
- Save sample products to the SQLite watchlist and review dashboard metrics from Dashboard.
- Open **Model details** or read [docs/MODEL_CARD.md](docs/MODEL_CARD.md) for methodology and limitations.

SQLite stores products, price-history observations, watchlist items and recent analysis activity. `data/products.json` initializes a new database; any existing `data/watchlist.json` entries are imported once. No retailer scraping, API keys or external service account is required.

## Retrain and test

With the virtual environment activated and dependencies installed:

```bash
python -m tools.train_model
python -m unittest discover -s tests -v
```

Training writes `artifacts/price_model.joblib`, `data/synthetic_training_data.csv`, and `reports/model_metrics.json`. The web server trains the model automatically if the artifact is missing.

## Project layout

```text
DealSense/
|-- app.py                     Flask routes and deal evaluation
|-- ml/price_model.py          Reproducible model, training and metrics
|-- data/products.json         Seed catalog for products and price history
|-- database.py                SQLite schema, migration and query functions
|-- pricing.py                 Central deal thresholds, scoring and explanations
|-- data/dealsense.sqlite3     Created on first app start
|-- static/                    Responsive interface, styles and local assets
|-- tools/train_model.py       Explicit model-training command
|-- tests/                     API smoke tests
|-- docs/                      Project notes and model card
|-- requirements.txt           Python dependencies
```

## Technical notes

The service binds to `127.0.0.1:8000`. JSON endpoints include `GET /api/health`, `GET /api/products?q=...`, `GET /api/products/<id>`, `POST /api/analyze`, `GET /api/products/<id>/history`, `GET /api/products/<id>/similar`, `GET /api/watchlist`, `POST /api/watchlist`, `DELETE /api/watchlist/<id>`, `GET /api/dashboard` and `POST /api/compare`. The frontend uses the browser Canvas API for price history. See [docs/PROJECT_NOTES.md](docs/PROJECT_NOTES.md) and [docs/MODEL_CARD.md](docs/MODEL_CARD.md) for scope and limitations.