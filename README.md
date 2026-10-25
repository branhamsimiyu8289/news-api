# News API

A small FastAPI app that serves the Field Notes News page and fetches articles
from NewsAPI.

## Run locally (Windows PowerShell)

From this directory:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

Copy `.env.example` to `.env` and put your NewsAPI key in it:

```text
NEWS_API_KEY=your_newsapi_key_here
FRONTEND_ORIGINS=http://localhost:3000,http://127.0.0.1:3000
```

Start the development server:

```powershell
python -m uvicorn main:app --reload
```

Open <http://127.0.0.1:8000>. The interactive API docs are at
<http://127.0.0.1:8000/docs>.

## Deploy the backend to Render

1. Push this project to a Git repository and create a Render web service from it
   using the included `render.yaml` Blueprint.
2. In the Render service environment, set `NEWS_API_KEY` to your NewsAPI key.
3. The known Vercel origin, `https://news-api-yjml.vercel.app`, is allowed by
   default. Set `FRONTEND_ORIGINS` to add any other exact origins as a
   comma-separated list. Do not include trailing slashes.
4. Confirm the service is healthy at `https://your-service.onrender.com/healthz`.

The Render service binds to Render's `PORT` and has a `/healthz` health check.
The API reads secrets from environment variables; never commit `.env`.

## Deploy the frontend to Vercel

Use this directory as the Vercel project root. The included `vercel.json`
builds and publishes only `index.html` as a static site. Before deploying, set
the `api-base-url` meta tag in `index.html` to your Render service URL, such as
`https://your-service.onrender.com` (no trailing slash). After changing the
URL, redeploy the Vercel project.

Keep any additional Vercel/custom domains in Render's `FRONTEND_ORIGINS` setting
in sync with the frontend's production domain. CORS is restricted to the
configured exact origins; local development defaults to `http://localhost:3000`
and `http://127.0.0.1:3000`.

## Debug

- Keep the server terminal open; Python errors and request logs appear there.
- For breakpoints, open this folder in VS Code, create `.vscode/launch.json`
  with the configuration below, then set breakpoints in `main.py` and start
  **News API** from **Run and Debug**. `--reload` is intentionally omitted so
  the debugger stays attached to one process.

  ```json
  {
    "version": "0.2.0",
    "configurations": [
      {
        "name": "News API",
        "type": "debugpy",
        "request": "launch",
        "module": "uvicorn",
        "args": ["main:app", "--host", "127.0.0.1", "--port", "8000"]
      }
    ]
  }
  ```
- Check `/api/categories` first, then `/api/news?category=politics` to test the
  NewsAPI-backed endpoint. A missing key is reported as an API error.
- In browser developer tools, use **Network** and **Console** to inspect
  requests made by the page.

Keep `.env` private and do not commit or share your API key.
