# 🏗️ InfraGuard AI v2

### AI-Powered Infrastructure Failure Prediction, Monitoring & Pothole Detection

InfraGuard AI is a multimodal infrastructure monitoring and risk prediction system designed to identify potential infrastructure failures using machine learning, geospatial information, and real-time external data sources.

The platform combines infrastructure risk prediction, environmental and traffic data aggregation, infrastructure alerts, city-level reports, and image-based pothole detection to help assess infrastructure conditions and prioritize maintenance.

---

## ✨ Features

### 🏗️ Infrastructure Failure Prediction
- Predict infrastructure failure probability using geographic coordinates and infrastructure type.
- Combine machine learning models with environmental and geospatial information.
- Generate infrastructure risk assessments to support maintenance planning.

### 🚧 AI-Powered Pothole Detection
- Analyze road images to identify potential potholes.
- Estimate pothole severity using scores from 0–100.
- Assess overall road condition and potential failure risk.
- Estimate traffic disruption, including jam probability and potential speed reduction.
- Generate step-by-step repair recommendations.
- Estimate repair costs.

**Note:** Pothole detection can use a heuristic fallback when trained models are unavailable. Its outputs should be validated against real, annotated road images before being used for operational decisions.

### 🌐 Real-Time Data Integration
Integrate infrastructure-related information from external sources, including:

- OpenStreetMap (OSM) and Overpass API
- ReliefWeb
- USGS seismic data
- World Bank Open Data
- TomTom traffic data
- OpenWeatherMap weather data
- World Air Quality Index (WAQI)
- NOAA climate and flood data

Availability depends on the source, API configuration, and network connectivity. Some providers require API keys.

### 🚨 Infrastructure Alerts
- Retrieve infrastructure alerts through API endpoints.
- Support real-time alert streaming through WebSockets.
- Help monitor infrastructure risks by location.

### 📊 City-Level Reports
- Generate infrastructure reports for cities.
- Retrieve aggregated information through report endpoints.
- Support infrastructure monitoring and maintenance planning.

### ⚙️ Backend & Infrastructure
- FastAPI backend
- Machine learning integration using XGBoost and CNN-based components
- Redis caching with an in-memory fallback
- PostgreSQL for persistent data storage
- Celery background workers and scheduled tasks
- Docker Compose support

---

## 🛠️ Technology Stack

| Component | Technology |
|---|---|
| Backend API | Python, FastAPI |
| API documentation | Swagger UI, ReDoc |
| Machine learning | XGBoost, CNN-based models, YOLO-based pothole detection |
| Computer vision | PyTorch, Torchvision, Ultralytics |
| Geospatial data | OpenStreetMap, Overpass API |
| External data | Weather, traffic, seismic, climate and air-quality APIs |
| Caching | Redis, in-memory fallback |
| Database | PostgreSQL, SQLAlchemy/async database integration where configured |
| Background tasks | Celery, Celery Beat |
| Deployment | Docker, Docker Compose, Uvicorn |

The exact models and dependencies available depend on the installed requirements, configuration, and trained model weights.

---

## 📋 Prerequisites

For local development:

- Python 3.11 or later
- pip
- Git (for cloning repositories and datasets)
- Internet connection for external data sources

Optional components:

- Docker 20+ and Docker Compose v2+
- Redis 7.x
- PostgreSQL 16+
- PyTorch and Torchvision
- Ultralytics YOLO
- A GPU for faster model training

Redis and PostgreSQL are optional for local development according to the current run guide. Some functionality may be limited without them.

---

## 🚀 Quick Start — Windows

### 1. Open the project directory

Open PowerShell and navigate to the directory containing `main.py`.

```powershell
cd "C:\Users\anish\OneDrive\Desktop\InfraGuard AI\ig2"
```

Replace the example path with your actual project location.

### 2. Create a virtual environment

Run this only when creating the environment for the first time.

```powershell
python -m venv venv
```

### 3. Activate the environment

```powershell
.\venv\Scripts\Activate.ps1
```

If PowerShell blocks script activation, use the virtual environment's Python executable directly instead.

### 4. Install dependencies

```powershell
python -m pip install --upgrade pip
pip install -r requirements.txt
```

**Important:** The existing run guide notes that PyTorch and Torchvision are commented out in `requirements.txt`. Install the additional ML dependencies only when required by the functionality you intend to use.

For pothole model training:

```powershell
pip install ultralytics torch torchvision
```

### 5. Configure environment variables

Copy the template:

```powershell
Copy-Item .env.template .env
```

Edit `.env` to configure API keys, database connections, and other settings.

The following data sources are listed as working without API keys in the current run guide:

- OSM Overpass
- ReliefWeb
- USGS Seismic
- World Bank Open Data

Optional API keys for additional data sources:

| Variable | Purpose |
|---|---|
| `TOMTOM_API_KEY` | Traffic data |
| `OPENWEATHER_API_KEY` | Weather data |
| `WAQI_API_KEY` | Air-quality data |
| `NOAA_API_KEY` | Climate and flood data |
| `REDIS_URL` | Redis connection |
| `DATABASE_URL` | PostgreSQL connection |

Refer to `.env.template` for the actual configuration expected by the application. Never commit real API keys or passwords to GitHub.

### 6. Create required directories

```powershell
New-Item -ItemType Directory -Force -Path logs, ml\weights
```

### 7. Start the API server

```powershell
python -m uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

### 8. Open the application

| URL | Purpose |
|---|---|
| http://localhost:8000 | Root endpoint |
| http://localhost:8000/health | Health check |
| http://localhost:8000/docs | Interactive Swagger API documentation |
| http://localhost:8000/redoc | ReDoc API documentation |

---

## 🚧 Pothole Detection — Quick Start

### 1. Run the built-in test

From the `ig2` project directory:

```powershell
python test_pothole_detection.py
```

The test is designed to generate a synthetic image and exercise the pothole analysis workflow.

Expected outputs may include:

- Detected pothole count
- Severity scores
- Overall road condition
- Failure-risk score
- Repair instructions
- Estimated repair costs
- Traffic-impact predictions

The synthetic test verifies application behavior; it does not establish real-world detection accuracy.

### 2. Analyze a road image using Python

```python
from ml.pothole_detector import analyze_pothole_image

result = analyze_pothole_image("path/to/your/road_image.jpg")

print(f"Potholes: {result['potholes_detected']}")
print(f"Condition: {result['overall_condition']}")
print(f"Risk: {result['failure_risk_score']}/100")
```

Replace the example image path with the actual location of your road image.

The function and returned fields must match the implementation in `ml/pothole_detector.py`.

### 3. Analyze an image using the API

Start the application:

```powershell
python -m uvicorn main:app --reload
```

In a second terminal, submit a road image:

```bash
curl -X POST "http://localhost:8000/api/pothole/quick-analyze" \
  -F "file=@road_image.jpg"
```

Replace `road_image.jpg` with an image available in the current terminal directory.

The endpoint shown here comes from the pothole quick start guide. Verify that it is registered in the running application before relying on it.

### 4. Example response

The following is an illustrative response format, not a guaranteed result from every image:

```json
{
  "potholes_detected": 3,
  "overall_condition": "POOR",
  "failure_risk_score": 74.5,
  "traffic_impact": {
    "jam_probability": 0.68,
    "jam_risk": "HIGH",
    "speed_reduction_pct": 41
  },
  "repair_instructions": [
    {
      "step": 1,
      "title": "Safety Setup & Traffic Control",
      "duration": "15-30 minutes",
      "equipment": [
        "Traffic cones",
        "Warning signs"
      ]
    }
  ],
  "estimated_repair_cost": {
    "total_usd": 485.50
  }
}
```

Risk scores, traffic probabilities, repair instructions, and cost estimates should be treated as model-generated estimates until calibrated and validated against real-world observations.

---

## 🧠 Training the Pothole Detection Models

InfraGuard AI's pothole workflow can be fine-tuned using annotated datasets.

### Step 1: Obtain a dataset

Potential sources include:

| Dataset | Approximate size stated in the project guide | Notes |
|---|---:|---|
| Annotated Potholes Dataset on Kaggle | 665 images | Pothole-focused images |
| Road Damage Detection 2022 (RDD2022) | 47,000+ images | Multiple road-damage categories |
| German Asphalt Pavement Distress Dataset (GAPs) | 1,969 images | Asphalt pavement distress |

Dataset size and availability may change. Check the source for the current version and license before use.

Links:

- RDD2022: https://github.com/sekilab/RoadDamageDetector
- Annotated Potholes Dataset: https://www.kaggle.com/datasets/chitholian/annotated-potholes-dataset
- GAPs: https://www.tu-ilmenau.de/neurob/data-sets-code/german-asphalt-pavement-distress-dataset-gaps

**Dataset compatibility:** These datasets do not necessarily share the same annotation format or severity labels. Convert and validate annotations to match the training scripts before starting training.

### Step 2: Download Kaggle datasets

Install the Kaggle CLI:

```powershell
pip install kaggle
```

Configure your Kaggle credentials using the account settings at:

https://www.kaggle.com/settings/account

For PowerShell, set the credentials for the current session:

```powershell
$env:KAGGLE_USERNAME = "your_username"
$env:KAGGLE_KEY = "your_key"
```

Download a dataset using the project script:

```powershell
python ml/download_kaggle_dataset.py --dataset potholes
```

Other dataset options listed in the guide:

```powershell
python ml/download_kaggle_dataset.py --dataset road-damage
python ml/download_kaggle_dataset.py --dataset crack-detection
```

The guide describes this script as downloading datasets directly. Confirm the script's behavior and destination before assuming that archives are never saved.

Keep your Kaggle credentials private.

### Step 3: Prepare the dataset structure

Create the expected dataset directories:

```powershell
python ml/prepare_dataset.py --action create
```

The YOLO dataset is expected to follow a structure similar to:

```text
data/
├── pothole_yolo/
│   ├── train/
│   │   ├── images/
│   │   └── labels/
│   ├── val/
│   │   ├── images/
│   │   └── labels/
│   └── test/
│       ├── images/
│       └── labels/
└── severity/
    └── train/
        ├── minimal/
        ├── low/
        ├── medium/
        ├── high/
        └── critical/
```

The severity directory is illustrative of the intended class organization; use the actual directory structure generated and required by your preparation script.

YOLO label files must match the corresponding images and class configuration. Severity classification also requires correctly labeled examples.

Add your images and annotations to the directories expected by the training code.

### Step 4: Validate annotations

```powershell
python ml/prepare_dataset.py --action validate-yolo
```

Resolve any missing labels, invalid class IDs, malformed annotation files, or dataset split issues before training.

### Step 5: Train the models

```powershell
python ml/train_pothole_model.py --mode both --epochs 50
```

The `both` mode is intended to train both models. Confirm the available training options and output weight paths in the script.

The original project guide estimates:

- GPU: approximately 2–4 hours
- CPU: approximately 8–12 hours

Actual training time depends on hardware, image resolution, dataset size, model configuration, and other settings.

### Step 6: Evaluate the trained models

Before using trained weights in production:

- Evaluate precision, recall, and mean average precision for object detection.
- Review false positives and missed potholes.
- Evaluate severity-classification performance separately.
- Test on images that were excluded from training.
- Check performance on different road surfaces, lighting conditions, and camera angles.
- Confirm that the application loads the correct trained weights.

Do not assume that training completed successfully simply because the script exited without an error.

---

## 🔌 API Reference

The following endpoints are documented in the project run guide or pothole quick start guide. Check the application source and `/docs` for the exact routes, request schemas, and supported parameters.

### Core endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/` | Welcome message |
| `GET` | `/health` | Health check |
| `GET` | `/docs` | Swagger UI |
| `GET` | `/redoc` | ReDoc |

### Infrastructure prediction

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/v1/predict/` | Predict infrastructure failure probability |

The prediction endpoint accepts parameters including latitude, longitude, and infrastructure type, as described in the run guide.

### Pothole detection

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/pothole/quick-analyze` | Analyze an uploaded road image |

### Additional endpoint groups

| Category | Route prefix or example | Purpose |
|---|---|---|
| Data sources | `/api/v1/data/...` | Retrieve external data |
| Alerts | `/api/v1/alerts/...` | Infrastructure alerts |
| WebSocket alerts | `/api/v1/alerts/ws?city=Mumbai` | Stream alerts |
| Reports | `/api/v1/reports/...` | City-level reports |
| Scraper | `/api/v1/scraper/...` | On-demand data collection |

The ellipses represent route groups, not literal endpoint paths. Consult the running API documentation for complete endpoint details.

---

## 🐳 Docker Compose Deployment

Docker Compose can start the main API and supporting services together.

### Build and start the stack

```powershell
docker compose up --build
```

Run in the background:

```powershell
docker compose up --build -d
```

View API logs:

```powershell
docker compose logs -f api
```

Stop the services:

```powershell
docker compose down
```

To remove associated volumes and persisted container data:

```powershell
docker compose down -v
```

**Warning:** The `-v` option can delete database and other persistent volume data. Back up important data before using it.

### Service ports

| Service | Port |
|---|---:|
| FastAPI | 8000 |
| Redis | 6379 |
| PostgreSQL | 5432 |
| Celery Flower, when enabled | 5555 |

To start with Celery monitoring:

```powershell
docker compose --profile monitoring up --build
```

If the Compose configuration includes the monitoring profile, Flower should be available at http://localhost:5555.

---

## 🧪 Running Tests

Run the project's automated tests from the `ig2` directory:

```powershell
python -m pytest tests/ -v
```

Run the pothole-specific test:

```powershell
python test_pothole_detection.py
```

These commands test different aspects of the project. A successful synthetic pothole test does not replace the full automated test suite or real-image model evaluation.

---

## 🗂️ Project Structure

The following is a consolidated view of the documented project layout. Some files and directories are optional or may differ in the actual checkout.

```text
ig2/
├── main.py
├── requirements.txt
├── .env.template
├── .env
├── Dockerfile
├── docker-compose.yml
│
├── routers/
│   ├── predict.py
│   ├── data_sources.py
│   ├── alerts.py
│   ├── reports.py
│   └── scraper.py
│
├── ml/
│   ├── model.py
│   ├── data_fetcher.py
│   ├── pothole_detector.py
│   ├── prepare_dataset.py
│   ├── train_pothole_model.py
│   ├── download_kaggle_dataset.py
│   └── weights/
│
├── data/
│   ├── synthetic/
│   └── pothole_yolo/
│
├── scrapers/
│   ├── osm_scraper.py
│   └── gov_scraper.py
│
├── utils/
│   ├── config.py
│   ├── cache.py
│   └── tasks.py
│
├── logs/
└── tests/
```

**Note:** The listed files should be checked against the actual repository. The pothole-specific scripts and routes must exist in the expected locations for their corresponding commands to work.

---

## 🛠️ Troubleshooting

### 1. Missing Python modules

If you encounter `ModuleNotFoundError`:

```powershell
python -m pip install -r requirements.txt
```

For the pothole training dependencies:

```powershell
python -m pip install ultralytics torch torchvision
```

Use the same Python environment to install dependencies and run the application.

### 2. Port 8000 is already in use

Find the process using the port:

```powershell
netstat -ano | findstr :8000
```

Stop the relevant process only after confirming its PID and purpose, or use another port:

```powershell
python -m uvicorn main:app --port 8001
```

### 3. Redis is unavailable

The application is documented as supporting an in-memory cache fallback when Redis is unavailable.

For local development, this may be acceptable. An in-memory cache is not a replacement for shared caching across multiple application processes.

### 4. PostgreSQL connection errors

PostgreSQL is optional for local development according to the run guide. However, database-backed persistence or particular application features may not work without a valid database connection.

Verify `DATABASE_URL` and the database service configuration when persistence is required.

### 5. Unicode errors in the Windows terminal

If Python reports a `UnicodeEncodeError`, configure UTF-8 output:

```powershell
$env:PYTHONIOENCODING = "utf-8"
chcp 65001
```

Restart the application after changing the terminal encoding.

### 6. Pothole detection returns unexpected results

Check the following:

- The supplied image exists and is readable.
- The correct Python environment is active.
- The required image-processing and ML dependencies are installed.
- The expected trained model weights are present, if the implementation requires them.
- Dataset labels and class IDs match the training configuration.
- The output is not being generated by a heuristic fallback.
- The API endpoint is registered in the running application.

### 7. External data is missing

Verify network access, API credentials, provider limits, and the configuration in `.env`. Some data sources may return incomplete information or be unavailable temporarily.

---

## 🔐 Security & Responsible Use

- Keep `.env`, API keys, credentials, and private datasets out of public repositories.
- Do not expose development servers or API documentation publicly without appropriate access controls.
- Validate uploaded images and enforce suitable file-size limits.
- Use authentication, authorization, rate limiting, and secure deployment configuration for public-facing APIs.
- Treat infrastructure risk scores as decision-support outputs, not definitive structural safety assessments.
- Have qualified personnel verify road conditions, repair recommendations, and cost estimates before taking real-world action.

---

## 🗺️ Roadmap

Potential future improvements include:

- Improved pothole detection and severity classification
- Larger and more diverse annotated road-image datasets
- Evaluation dashboards for ML performance
- Geographic pothole visualization and reporting
- Better calibration of traffic-impact predictions
- Region-specific repair-cost estimates
- Integration of pothole reports with infrastructure risk monitoring
- Automated model evaluation and retraining workflows

These are potential improvements, not claims that the features are already implemented.

---

## 📚 Additional Documentation

Refer to the following project documentation when available:

- `ml/POTHOLE_DETECTION_GUIDE.md` — detailed pothole detection and training instructions
- `POTHOLE_SYSTEM_SUMMARY.md` — pothole subsystem overview
- `.env.template` — environment variable configuration
- `/docs` — interactive API documentation after starting the server
- `/redoc` — alternative API documentation

---

## 📄 License

Add the license applicable to this repository here. Do not claim an open-source license unless one has been selected and added to the project.

---

## 👨‍💻 About InfraGuard AI

InfraGuard AI v2 brings together machine learning, real-time data integration, geospatial analysis, and computer vision to support infrastructure monitoring and maintenance planning.

Its goal is to make infrastructure risk information more accessible and help identify potential problems earlier through data-driven analysis.

**Built with Python, FastAPI, machine learning, and real-time data integration.**
