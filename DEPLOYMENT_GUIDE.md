# HK8 Translation Service — Stage 2 Deployment Guide

## 1. Project purpose

This is a static Translation Service for HK8.

It does not connect to:

- HK8 BO
- Any database
- Any private API

It contains no Access ID, Access Token, Merchant ID, API Secret, or BO password.

## 2. Final directory

```text
HK8_Translation_Stage2_Zeabur/
├── DEPLOYMENT_GUIDE.md
└── public/
    ├── _headers
    ├── health.txt
    ├── version.json
    └── translations/
        ├── en.json
        ├── ms.json
        ├── zh.json
        ├── vi.json
        ├── th.json
        └── bn.json
```

`_headers` is a Zeabur static-site configuration file. It sets JSON MIME types and browser cache rules; it is not an application or an extra service.

## 3. Upload to GitHub

### Method A — GitHub website

1. Sign in to GitHub.
2. Click **New repository**.
3. Repository name suggestion: `hk8-translation-service`.
4. Choose **Private** or **Public**.
5. Create the repository.
6. Upload the complete project contents:
   - `public/`
   - `DEPLOYMENT_GUIDE.md`
7. Commit the files.

### Method B — Git command

Run these commands inside the extracted project folder:

```bash
git init
git add .
git commit -m "Stage 2: Zeabur translation service"
git branch -M main
git remote add origin https://github.com/YOUR_ACCOUNT/hk8-translation-service.git
git push -u origin main
```

Replace `YOUR_ACCOUNT` with the GitHub account name.

## 4. Connect GitHub to Zeabur

1. Sign in to Zeabur.
2. Create a new Zeabur project.
3. Click **Add Service** or **Deploy New Service**.
4. Choose **GitHub** / **Deploy from source code**.
5. Authorize GitHub when requested.
6. Select the `hk8-translation-service` repository.
7. In the service configuration, set **Root Directory** to:

```text
public
```

This is required so the public URLs become `/translations/en.json`, `/version.json`, and `/health.txt`, without `/public/` in the URL.

8. Do not add a database.
9. Do not add environment variables.
10. Deploy the service.

## 5. Generate a Zeabur domain

1. Open the deployed service.
2. Open the **Domain** tab.
3. Click **Generate Domain**.
4. Zeabur will provide a domain similar to:

```text
https://xxxxx.zeabur.app
```

## 6. Required URL tests

Replace `xxxxx.zeabur.app` with the generated domain and open:

```text
https://xxxxx.zeabur.app/translations/en.json
https://xxxxx.zeabur.app/translations/ms.json
https://xxxxx.zeabur.app/translations/zh.json
https://xxxxx.zeabur.app/translations/vi.json
https://xxxxx.zeabur.app/translations/th.json
https://xxxxx.zeabur.app/translations/bn.json
https://xxxxx.zeabur.app/version.json
https://xxxxx.zeabur.app/health.txt
```

Every URL must return HTTP 200.

Expected health response:

```text
OK
```

Expected initial version response:

```json
{
  "version": "1.0.0",
  "updated_at": "AUTO"
}
```

## 7. Browser cache rules

The `public/_headers` file applies these rules:

- Language JSON: cache for 1 hour and revalidate.
- `version.json`: always revalidate so the future Loader can detect updates.
- `health.txt`: no cache.
- JSON files explicitly return `application/json; charset=utf-8`.
- Zeabur/Caddy normally supplies validators such as `ETag` and/or `Last-Modified` for static files. Confirm the actual deployed response headers in the browser Network panel.

## 8. Update translations

For a future translation update:

1. Replace only the changed JSON file(s) inside `public/translations/`.
2. Do not merge all languages into one file.
3. Do not rename translation keys.
4. Update `public/version.json`.
5. Commit and push to GitHub.
6. Zeabur will redeploy from the repository.
7. Test all eight required URLs again.

## 9. Update version

Recommended version examples:

```text
1.0.0 -> 1.0.1  Small translation correction
1.0.1 -> 1.1.0  Larger translation package update
```

Replace `AUTO` with the actual UTC timestamp when publishing an update, for example:

```json
{
  "version": "1.0.1",
  "updated_at": "2026-08-06T04:38:00Z"
}
```

For this Stage 2 initial package, `updated_at` remains `AUTO` exactly as requested.

## 10. Testing checklist

- [ ] `/translations/en.json` returns HTTP 200
- [ ] `/translations/ms.json` returns HTTP 200
- [ ] `/translations/zh.json` returns HTTP 200
- [ ] `/translations/vi.json` returns HTTP 200
- [ ] `/translations/th.json` returns HTTP 200
- [ ] `/translations/bn.json` returns HTTP 200
- [ ] `/version.json` returns HTTP 200
- [ ] `/health.txt` returns HTTP 200 and `OK`
- [ ] JSON response Content-Type is `application/json`
- [ ] Language JSON has Cache-Control
- [ ] `version.json` revalidates instead of using a long cache
- [ ] No database is connected
- [ ] No environment variables or sensitive credentials are stored

## 11. Stop point

Stage 2 ends after deployment and testing. Do not modify the HK8 website or start the Translation Loader until Stage 3.
