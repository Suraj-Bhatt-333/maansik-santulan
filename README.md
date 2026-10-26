# Masik Santulan — Student Mental Health Signal

A small machine-learning project that estimates a student mental-health score from reported social-media habits, academic details, lifestyle, and stress level. The project includes a notebook for data exploration/model development, a FastAPI prediction service, and a browser-based form.

# 🌐 live link:
- frontend:  https://maansik-santulan-1.onrender.com/
- backend :  https://maansik-santulan.onrender.com/

> **Important:** This is an educational, informational estimate—not a diagnosis or a substitute for professional care.

## Project contents

- `index.html`, `style.css`, `script.js` — the browser interface.
- `main.py` — FastAPI app with the `/predict` endpoint.
- `Mental_Health_Model.pkl` — serialized model loaded by the API at startup.
- `Student Social Media And Mental Health Impact.csv` — dataset used for the project.
- `project_notebook.ipynb` and `notebook.ipynb` — notebooks for the project and analysis.
- `requirements.txt` — Python dependencies.

## Run locally

Use a Python environment compatible with the installed dependencies.

1. From the project directory, install dependencies:

   ```bash
   python -m pip install -r requirements.txt
   ```

2. Start the API:

   ```bash
   uvicorn main:app --reload --host 127.0.0.1 --port 2200
   ```

   The API is available at `http://127.0.0.1:2200`. Interactive API documentation is at `http://127.0.0.1:2200/docs`.

3. In a separate terminal, serve the frontend from the project directory:

   ```bash
   python -m http.server 5500
   ```

4. Open `http://127.0.0.1:5500` in your browser and submit the form.

The frontend currently sends prediction requests to `http://127.0.0.1:2200`. If you use a different local API address or deploy the API, update `API_BASE` near the top of `script.js` to match it.

## API

### `POST /predict`

Example request:

```json
{
  "age": 21,
  "gender": "Female",
  "country": "India",
  "academic_level": "Undergraduate",
  "most_used_platform": "Instagram",
  "purpose_of_use": "Entertainment",
  "avg_daily_usage_hours": 4.5,
  "daily_unlocks": 60,
  "study_hours": 3,
  "physical_activity_hours": 1,
  "sleep_hours_per_night": 7,
  "stress_level": "Medium"
}
```

Successful response:

```json
{
  "predicted_mental_health_score": 6.78
}
```

The API validates the input using Pydantic. Age must be between 10 and 100; hour fields must be between 0 and 24; categorical values must match the options accepted by the API. Invalid input returns HTTP `422`.

### `GET /`

Returns a small welcome response to confirm that the API is running.

## Deploy on Render

Create a **Web Service** for the Python API, with the repository root as the Root Directory if `main.py`, `requirements.txt`, and the model file are at the repository root.

- **Build Command:** `pip install -r requirements.txt`
- **Start Command:** `uvicorn main:app --host 0.0.0.0 --port $PORT`

Ensure `requirements.txt` and `Mental_Health_Model.pkl` are committed and included in the deployed repository. Check the Render build logs to confirm Uvicorn and the other dependencies install successfully.

The API service does not serve the HTML frontend. Deploy `index.html`, `style.css`, and `script.js` separately as a static site, then change `API_BASE` in `script.js` to the public URL of the Render API (without a trailing slash). The API currently allows cross-origin requests; for a production deployment, restrict CORS to the frontend's origin.

## Notes

- The model is loaded from `Mental_Health_Model.pkl` when `main.py` starts. Keep that file in the API service's working directory.
- Retrain or re-export the model from the notebooks if you change the expected feature schema.
- The predicted score should be interpreted only in the context of the dataset and model; it is not a clinical assessment.
