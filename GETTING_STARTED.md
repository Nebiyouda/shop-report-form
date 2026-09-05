# ENG360 Getting Started

ENG360 is a browser-based aviation engineering suite. The current release includes:

- Shop Report Builder
- Asset Release Comment Generator

The application is static HTML, CSS, and JavaScript. It runs locally without a build step and is deployable through GitHub Pages.

## Run Locally

From the repository root, start a static server:

```powershell
python -m http.server 8000
```

Open:

```text
http://localhost:8000/
```

The main dashboard is in `index.html`.

## Main Source Files

```text
index.html                              ENG360 dashboard and application shell
apps/shop-report-form/index.html        Shop Report Builder
apps/asset-release/index.html           Data, Template, and Comment routes
```

## Deploy to GitHub Pages

1. Commit changes to the configured default branch.
2. Push the branch to GitHub.
3. Wait for GitHub Pages to publish the latest commit.
4. Open the repository Pages URL.

The application uses relative paths for embedded applications, so the dashboard and child applications must remain in the same repository structure.

## External Browser Libraries

The application loads these browser libraries from CDNs:

- Tailwind CSS CDN for dashboard and form utility styling
- SheetJS for `.xlsx`, `.xls`, and `.csv` ingestion
- html2canvas for PDF rendering
- jsPDF for PDF export

An internet connection is required when the browser has not cached these libraries.

## Current Authentication Status

Authentication is currently a client-side demo flow. It accepts any non-empty email and password in the ENG360 shell. Supabase authentication has not yet been connected.

Do not use the current demo login as production security. Supabase integration should be completed before handling protected production data.

## Data Privacy Boundary

Asset data is processed in the browser only:

- Uploaded Excel and CSV files are read with the browser File API.
- SheetJS parses the first worksheet locally.
- P/N and serial lookup runs locally.
- Date calculations and comment generation run locally.
- The uploaded source data is not sent to an ENG360 server or Supabase.
- Uploaded source rows are held in page memory and are cleared by refresh or page close.
- Saved templates are stored in the browser `localStorage`.

The CDN requests for the front-end libraries are separate from the asset data processing. Do not treat browser-only processing as a replacement for server-side access control on shared or unmanaged devices.
