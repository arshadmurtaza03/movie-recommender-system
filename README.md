# 🎬 Movie Recommender System

![Python](https://img.shields.io/badge/Python-3.11+-blue?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.128-009688?logo=fastapi&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-1.54-FF4B4B?logo=streamlit&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.8-F7931E?logo=scikit-learn&logoColor=white)
![TMDB](https://img.shields.io/badge/TMDB-API-01B4E4?logo=themoviedatabase&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)

A full-stack movie recommendation web app powered by **TF-IDF content-based filtering** and the **TMDB API**. Search any movie, explore details with posters, and get instant content-similar and genre-based recommendations — all through a clean Streamlit UI backed by a FastAPI REST API.

> 🚀 **Live Backend:** [https://movie-recommender-system-yir6.onrender.com](https://movie-recommender-system-yir6.onrender.com)

---

## ✨ Features

- 🔍 **Keyword Search** — Real-time TMDB search with autocomplete dropdown suggestions
- 🏠 **Home Feed** — Browse Trending, Popular, Top Rated, Now Playing, and Upcoming movies
- 🎞️ **Movie Detail Page** — Full overview, release date, genres, poster, and backdrop image
- 🤖 **TF-IDF Recommendations** — Content-based filtering using cosine similarity on a pre-trained TF-IDF matrix
- 🎭 **Genre Recommendations** — TMDB Discover API suggests popular movies in the same genre
- ⚡ **Dual Architecture** — Decoupled FastAPI backend + Streamlit frontend
- 🖼️ **Poster Grid UI** — Responsive multi-column poster layout with click-to-details navigation

---

## 🏗️ Architecture

```
┌─────────────────────────────────┐
│        Streamlit Frontend        │  ← app.py
│  (Search, Home Feed, Details UI) │
└────────────┬────────────────────┘
             │  HTTP (REST API)
             ▼
┌─────────────────────────────────┐
│         FastAPI Backend          │  ← main.py
│   (Pickle Models + TMDB Client)  │
└───────┬─────────────┬───────────┘
        │             │
        ▼             ▼
 ┌────────────┐  ┌──────────────┐
 │ Local PKL  │  │   TMDB API   │
 │ TF-IDF     │  │  (posters,   │
 │ Model +    │  │  search,     │
 │ DataFrame  │  │  discover)   │
 └────────────┘  └──────────────┘
```

The Streamlit app communicates exclusively with the FastAPI backend. The backend handles all ML inference (TF-IDF cosine similarity) and all TMDB API calls.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Streamlit 1.54 |
| Backend | FastAPI 0.128, Uvicorn |
| ML / Recommendations | scikit-learn (TF-IDF), SciPy (sparse cosine sim), NumPy |
| Data | Pandas, Pickle (pre-trained artifacts) |
| External API | TMDB (The Movie Database) |
| HTTP Client | HTTPX (async) |
| Config | python-dotenv |

---

## 📁 Project Structure

```
movie-recommender-system/
├── app.py                  # Streamlit frontend (UI, routing, API calls)
├── main.py                 # FastAPI backend (ML inference, TMDB integration)
│
├── df.pkl                  # Preprocessed movie DataFrame
├── indices.pkl             # Title → matrix-index mapping
├── tfidf.pkl               # Fitted TF-IDF vectorizer
├── tfidf_matrix.pkl        # Pre-computed sparse TF-IDF matrix
│
├── notebooks/              # Jupyter notebooks (EDA, model building)
│
├── requirements.txt        # Pinned dependencies
├── pyproject.toml          # Project metadata
├── uv.lock                 # uv lockfile
├── .env.example            # Environment variable template
├── .python-version         # Python version pin
└── .gitignore
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.11+
- A free [TMDB API Key](https://www.themoviedb.org/settings/api)

### 1. Clone the repository

```bash
git clone https://github.com/arshadmurtaza03/movie-recommender-system.git
cd movie-recommender-system
```

### 2. Set up the environment

```bash
# Using pip
pip install -r requirements.txt

# OR using uv (recommended)
uv sync
```

### 3. Configure environment variables

```bash
cp .env.example .env
```

Edit `.env` and add your TMDB API key:

```env
TMDB_API_KEY=your_tmdb_api_key_here
```

### 4. Run the FastAPI backend

```bash
uvicorn main:app --reload --port 8000
```

The API will be available at `http://127.0.0.1:8000`.
Interactive docs at `http://127.0.0.1:8000/docs`.

### 5. Run the Streamlit frontend

Open a second terminal:

```bash
streamlit run app.py
```

The app will open at `http://localhost:8501`.

> **Note:** By default, `app.py` points to the deployed Render backend. To use your local backend, update `API_BASE` in `app.py` to `http://127.0.0.1:8000`.

---

## 🔌 API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/health` | Health check |
| `GET` | `/home` | Home feed (trending, popular, top_rated, now_playing, upcoming) |
| `GET` | `/tmdb/search?query=` | Keyword search (returns TMDB results) |
| `GET` | `/movie/id/{tmdb_id}` | Full movie details (poster, overview, genres) |
| `GET` | `/movie/search?query=` | Bundle: details + TF-IDF recs + genre recs |
| `GET` | `/recommend/tfidf?title=` | TF-IDF-only recommendations |
| `GET` | `/recommend/genre?tmdb_id=` | Genre-based recommendations via TMDB Discover |

**Example:**

```bash
curl "http://127.0.0.1:8000/movie/search?query=Inception&tfidf_top_n=10"
```

---

## 🤖 How the Recommendation Works

1. **TF-IDF Content Filtering** — Movie metadata (title, overview, genres, etc.) is vectorized using a TF-IDF vectorizer. On startup, the backend loads `tfidf_matrix.pkl` (a precomputed sparse matrix) and `indices.pkl` (title→row mapping). For a query movie, cosine similarity scores are computed against the entire matrix, and the top-N most similar titles are returned.

2. **Genre-Based Filtering** — The selected movie's primary genre ID is passed to the TMDB `/discover/movie` endpoint, which returns the most popular movies in that genre.

Both recommendation types run in parallel and are displayed together in the Details view.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 🙋‍♂️ Author

**Arshad Murtaza** — [GitHub](https://github.com/arshadmurtaza03)

*Data Science & GenAI Enthusiast | ML • Deep Learning • NLP*
