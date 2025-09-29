# Deploy SIH_agri to Firebase Hosting + Cloud Run

This app is a Flask server (Python) packaged with Docker. Firebase Hosting proxies requests to a Cloud Run service that runs the container.

## Prerequisites
- Google Cloud SDK (gcloud)
- Firebase CLI (firebase-tools)
- A Firebase project linked to a Google Cloud project

## 1) Install Google Cloud SDK (Windows)
- Recommended: `winget install Google.CloudSDK --accept-source-agreements --accept-package-agreements`
- Or download installer: https://cloud.google.com/sdk/docs/install#windows
- After install, open a new PowerShell and verify:

```powershell
gcloud --version
```

## 2) Authenticate and select project
Replace `YOUR_GCP_PROJECT` with your Google Cloud project ID.

```powershell
gcloud auth login
gcloud config set project YOUR_GCP_PROJECT
gcloud services enable run.googleapis.com cloudbuild.googleapis.com artifactregistry.googleapis.com
```

## 3) Deploy to Cloud Run
Run from the repo root. Cloud Build will use the Dockerfile.

```powershell
cd d:\SIH\SIH3\SIH_agri

gcloud run deploy sih-agri-api `
  --source . `
  --region us-central1 `
  --allow-unauthenticated `
  --set-env-vars DEMO_MODE=1,FLASK_DEBUG=0
```

Notes:
- DEMO_MODE=1 is required on Cloud Run; MATLAB is not available there.
- After deploy, copy the Service URL to test directly.

## 4) Configure Firebase Hosting rewrite
`firebase.json` is already set to proxy all routes to the Cloud Run service `sih-agri-api` in `us-central1`.
If you deployed with a different name/region, update `firebase.json` accordingly.

## 5) Deploy Firebase Hosting
```powershell
firebase login
firebase use --add   # select your Firebase project
firebase deploy --only hosting
```

## 6) Test
- Open your Firebase Hosting URL (https://<your-site>.web.app)
- Click "Run Analysis"; demo images and a summary should appear.

## Troubleshooting
- If you see the default Firebase Hosting page, re-check `firebase.json` and redeploy Hosting.
- If Cloud Run returns MATLAB errors, ensure the service has `DEMO_MODE=1`.
- Local run (optional):

```powershell
cd d:\SIH\SIH3\SIH_agri\flask_server
$env:DEMO_MODE="1"
python -m flask run --host 0.0.0.0 --port 5001
```
