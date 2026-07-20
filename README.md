# Rotman MMA Team 4 Airbnb

Rotel is an AI-powered vacation rental recommendation prototype built for the Rotman MMA Team 4 Airbnb project. The app lets users create a profile, save travel preferences, search for rental listings, and receive personalized recommendations based on semantic similarity plus budget and group-size filters.

## Project Overview

The repository contains two main application pieces:

- `api/src/frontend.py`: Streamlit user interface for account creation, login, profile editing, and property search.
- `api/src/image_api.py`: FastAPI image service that resizes listing images and returns them to the Streamlit UI.

Recommendation logic lives in `api/utils/similarity_utils.py`. It uses sentence-transformer embeddings with a FAISS vector index to retrieve vacation rentals from the local listing data.

## Repository Structure

```text
.
|-- api/
|   |-- data/
|   |   |-- vacation_rentals_final.json   # sample vacation rental listings
|   |   |-- users.db                       # SQLite user database
|   |   `-- images/                        # listing image assets
|   |-- src/
|   |   |-- frontend.py                    # Streamlit app
|   |   `-- image_api.py                   # FastAPI image endpoints
|   `-- utils/
|       |-- db_utils.py                    # SQLite helpers
|       |-- similarity_utils.py            # FAISS search and embedding logic
|       |-- image_generator.py             # Unsplash helper prototype
|       `-- faiss_index/                   # saved FAISS index artifacts
`-- app/
    `-- src/                              # placeholder Vite/React starter app
```

## Features

- User registration and login backed by SQLite
- Editable user profile with group size, environment preference, and budget range
- Semantic property search using Hugging Face embeddings and FAISS
- Search filtering by nightly price and group capacity
- Local image API for resized listing images
- Streamlit-based interactive prototype UI

## Data

The main listing dataset is `api/data/vacation_rentals_final.json`. It currently contains 50 generated vacation rental records with fields such as:

- `property_id`
- `location`
- `type`
- `price_per_night`
- `capacity`
- `features`
- `tags`
- `description`

The user database is stored in `api/data/users.db`.

## Setup

Create a fresh Python environment instead of relying on the committed `venv` folder, because virtual environments are machine-specific.

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install fastapi uvicorn pillow streamlit requests pandas numpy streamlit-card python-dotenv faiss-cpu langchain-community langchain-core langchain-huggingface sentence-transformers
```

Create or confirm `api/.env`:

```text
DB_PATH=../data/users.db
```

## Running the App

Open one terminal for the image API:

```powershell
cd api\src
uvicorn image_api:app --reload --port 8000
```

Open a second terminal for the Streamlit UI:

```powershell
cd api\src
streamlit run frontend.py
```

Then open the local Streamlit URL shown in the terminal, usually:

```text
http://localhost:8501
```

If recommendation search cannot load the FAISS index, check the index path. The current search code loads `../data/faiss_index` from `api/src`, while the committed FAISS artifacts are stored in `api/utils/faiss_index`.

## API Endpoints

The FastAPI image service exposes:

- `GET /image/{image_number}`: returns a resized image from `api/data/images`.
- `GET /images/{folder_number}`: returns all PNG images in a listing image folder as base64 data URIs.
- `GET /images/ads/{ad_id}`: returns a resized ad image from `api/data/images/ads` if that folder exists locally.

Example:

```text
http://localhost:8000/image/900?n=300&m=300&format=png
```

## Recommendation Flow

1. A user creates a profile with group size, preferred environment, and budget.
2. The user enters a search query in the Streamlit app.
3. The app combines the profile preference with the search query.
4. `similarity_utils.py` embeds the combined query with `sentence-transformers/all-mpnet-base-v2`.
5. FAISS retrieves similar listings from the saved vector index.
6. Results are filtered by price range and group capacity.
7. Matching properties and images are shown in the Streamlit UI.

## Notes for Contributors

- `app/src` is currently the default Vite/React starter UI and is not wired into the Streamlit app.
- The repo includes generated dependency folders such as `venv` and `app/node_modules`; future cleanup should move dependencies into standard files such as `requirements.txt` and `package.json`.
- Some utility scripts are exploratory prototypes and are not required to run the main app.
- The checked-in `venv` may not work across machines. Build a fresh environment with the setup commands above.
- Do not commit real API keys. Replace any local keys with environment variables before sharing or deploying the project.

## Team

Rotman MMA Team 4
