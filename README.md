# 🎬 Movie Recommender System

[![Python](https://img.shields.io/badge/Python-3.11+-blue?logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.128-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Streamlit](https://img.shields.io/badge/Streamlit-1.54-FF4B4B?logo=streamlit&logoColor=white)](https://streamlit.io/)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-1.8-F7931E?logo=scikit-learn&logoColor=white)](https://scikit-learn.org/)
[![TMDB](https://img.shields.io/badge/TMDB-API-01B4E4?logo=themoviedatabase&logoColor=white)](https://www.themoviedb.org/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

A full-stack movie recommendation web app powered by **TF-IDF content-based filtering** and the **TMDB API**. Search any movie, explore details with posters, and get instant content-similar and genre-based recommendations — all through a clean Streamlit UI backed by a FastAPI REST API.

---

## 🌐 Live Demo

| Service | Link |
|---|---|
| 🎬 **Streamlit App** | [movie-recommender-system-dzpcuhe9yey2liiyrudee6.streamlit.app](https://movie-recommender-system-dzpcuhe9yey2liiyrudee6.streamlit.app/) |
| ⚙️ **FastAPI Backend** | [movie-recommender-system-yir6.onrender.com](https://movie-recommender-system-yir6.onrender.com) |
| 📖 **API Docs (Swagger)** | [/docs](https://movie-recommender-system-yir6.onrender.com/docs) |

> ⚠️ The backend is hosted on Render's free tier and may take **~30–60 seconds to wake up** on first visit.

---

## ✨ Features

- 🔍 **Keyword Search** — Real-time TMDB search with autocomplete dropdown suggestions
- 🏠 **Home Feed** — Browse Trending, Popular, Top Rated, Now Playing, and Upcoming movies
- 🎞️ **Movie Detail Page** — Full overview, release date, genres, poster, and backdrop image
- 🤖 **TF-IDF Recommendations** — Content-based filtering using cosine similarity on a pre-trained TF-IDF matrix
- 🎭 **Genre Recommendations** — TMDB Discover API surfaces the most popular movies in the same genre
- ⚡ **Decoupled Architecture** — FastAPI backend fully separated from Streamlit frontend
- 🖼️ **Poster Grid UI** — Responsive multi-column layout with click-to-details navigation

---

## 🏗️ Architecture

```
┌──────────────────────────────────┐
│        Streamlit Frontend        │  ← app.py
│  (Search, Home Feed, Details UI) │
└─────────────┬────────────────────┘
              │  HTTP (REST API)
              ▼
┌──────────────────────────────────┐
│          FastAPI Backend         │  ← main.py
│   (ML Inference + TMDB Client)   │
└────────┬─────────────┬───────────┘
         │             │
         ▼             ▼
  ┌────────────┐  ┌──────────────┐
  │  Local PKL │  │   TMDB API   │
  │  TF-IDF    │  │  (posters,   │
  │  Model +   │  │   search,    │
  │  DataFrame │  │  discover)   │
  └────────────┘  └──────────────┘
```

The Streamlit app communicates exclusively with the FastAPI backend. The backend handles all ML inference (TF-IDF cosine similarity) and all external TMDB API calls, keeping the frontend clean and stateless.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Streamlit 1.54 |
| Backend | FastAPI 0.128, Uvicorn |
| ML / Recommendations | scikit-learn (TF-IDF), SciPy (sparse cosine similarity), NumPy |
| Data | Pandas, Pickle (pre-trained artifacts) |
| External API | TMDB (The Movie Database) |
| HTTP Client | HTTPX (async) |
| Config | python-dotenv |
| Deployment | Streamlit Cloud + Render |

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

Edit `.env` and paste your TMDB API key:

```env
TMDB_API_KEY=your_tmdb_api_key_here
```

### 4. Run the FastAPI backend

```bash
uvicorn main:app --reload --port 8000
```

- API → `http://127.0.0.1:8000`
- Swagger UI → `http://127.0.0.1:8000/docs`

### 5. Run the Streamlit frontend

Open a second terminal:

```bash
streamlit run app.py
```

App opens at `http://localhost:8501`.

> **Note:** By default, `app.py` points to the deployed Render backend. To use your local backend instead, update `API_BASE` in `app.py` to `http://127.0.0.1:8000`.

---

## 🔌 API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/health` | Health check |
| `GET` | `/home?category=` | Home feed — `trending`, `popular`, `top_rated`, `now_playing`, `upcoming` |
| `GET` | `/tmdb/search?query=` | Keyword search (raw TMDB results for suggestions + grid) |
| `GET` | `/movie/id/{tmdb_id}` | Full movie details (poster, overview, genres, backdrop) |
| `GET` | `/movie/search?query=` | Bundle: movie details + TF-IDF recs + genre recs |
| `GET` | `/recommend/tfidf?title=` | TF-IDF-only content recommendations |
| `GET` | `/recommend/genre?tmdb_id=` | Genre-based recommendations via TMDB Discover |

**Example:**

```bash
curl "https://movie-recommender-system-yir6.onrender.com/movie/search?query=Inception&tfidf_top_n=10"
```

---

## 🤖 How Recommendations Work

1. **TF-IDF Content Filtering** — Movie metadata is vectorized into a sparse TF-IDF matrix at training time. At startup, the backend loads `tfidf_matrix.pkl` and `indices.pkl` into memory. When a movie is selected, cosine similarity is computed against the entire matrix and the top-N closest matches are returned as recommendations.

2. **Genre-Based Filtering** — The selected movie's primary genre ID is sent to TMDB's `/discover/movie` endpoint, which returns the most popular currently available movies in that genre.

Both recommendation types are fetched in the same `/movie/search` bundle request and rendered side-by-side on the Details page.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 🙋‍♂️ Author

**Arshad Murtaza** — [GitHub](https://github.com/arshadmurtaza03)

*Data Science & GenAI Enthusiast | ML • Deep Learning • NLP*

---

<p align="center">If you found this project helpful, consider giving it a ⭐ on GitHub!</p>
