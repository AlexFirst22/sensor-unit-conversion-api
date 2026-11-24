# Sensor Unit Conversion API (Flask on Azure)

A small REST API and web frontend for converting weather-sensor readings between units. It stores every conversion, shows the history and returns statistics per conversion type. The app is deployed to an Azure Web App through a GitHub Actions pipeline on every push to `main`.

## Features

- Converts three kinds of sensor values:

  | Type | Conversion |
  | --- | --- |
  | `c_to_f` | Temperature: °C → °F |
  | `hpa_to_mmhg` | Air pressure: hPa → mmHg |
  | `ms_to_kmh` | Wind speed: m/s → km/h |

- Validates input: a missing value or an unknown conversion type returns HTTP 400.
- Saves each conversion with a timestamp to `prevody.json`.
- Returns statistics per type: count, average, minimum and maximum.
- Two HTML pages: a converter and a conversion history.

## API

| Endpoint | Description |
| --- | --- |
| `GET /api/prevod?hodnota=<number>&typ=<type>` | Converts a value and saves the result. `typ` defaults to `c_to_f`. |
| `GET /api/historia-prevodov` | Returns all saved conversions. |
| `GET /api/statistika` | Returns `pocet`, `priemer`, `min` and `max` for each type. |
| `GET /` | Converter page |
| `GET /historia` | History page |

Example:

```bash
curl "http://localhost:5000/api/prevod?hodnota=1013&typ=hpa_to_mmhg"
```

## Run locally

```bash
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
python app.py
```

The app starts on `http://localhost:5000`.

## Deployment (CI/CD)

The workflow in `.github/workflows/main_weebsiti.yml` runs on every push to `main` or manually:

1. **Build:** sets up Python 3.12, installs dependencies into a virtual environment and packages the app as a build artifact.
2. **Deploy:** signs in to Azure with credentials stored in GitHub Secrets and deploys the package to the Azure Web App with `azure/webapps-deploy`.

## Tech stack

Python 3.12 · Flask · JSON file storage · HTML templates · GitHub Actions · Azure App Service

## Possible improvements

- Replace the JSON file with a database to handle concurrent writes safely.
- Turn off Flask debug mode in production.
- Add automated tests to the pipeline before the deploy step.
