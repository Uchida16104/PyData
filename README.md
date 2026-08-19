# PyData

PyData is a **single-file, browser-local data application**. The complete application UI and Python database logic live in `index.html`; no Python server, Node.js build step, API, or backend is required.

## Features

- SQLite database created and operated directly by **PyScript** in the browser.
- Real-time create, read, update, delete, search, and refresh operations.
- CSV import and CSV export handled by Python running in the browser.
- **HTMX 2.0.10** used for declarative event wiring and client-side custom-event integration.
- **Alpine.js 3.15.12** used for UI state and theme state.
- **Motion 13.0.0** used for data-row and interface animations.
- **Tailwind CSS Play CDN** used for styling without a build pipeline.
- Responsive table and editor UI.
- Input validation and HTML escaping before table rendering.
- Sample records are created automatically on startup.
- No application server or database server is required.

## Project structure

```text
PyData/
├── index.html
└── README.md
```

The runtime application is intentionally limited to `index.html`. `README.md` is documentation only.

## How the architecture works

```text
Browser
  │
  ├── Tailwind CSS ──────────────── UI styling
  ├── HTMX ─────────────────────── declarative browser events / custom events
  ├── Alpine.js ────────────────── UI state
  ├── Motion ────────────────────── interaction animations
  │
  └── PyScript
       │
       └── Python
            │
            └── sqlite3 (:memory:)
                 │
                 ├── CREATE / READ / UPDATE / DELETE
                 ├── search
                 ├── CSV import
                 └── CSV export
```

PyData uses SQLite's in-memory database (`:memory:`). This makes the project self-contained and avoids any backend. Because the database is intentionally in memory, a full page reload starts a new database and recreates the sample data.

## Requirements

A modern browser with JavaScript and WebAssembly support is required. No local Python installation is required to run PyData.

For local development, serve the directory over HTTP rather than opening the HTML file directly:

```bash
cd PyData
python3 -m http.server 8000
```

Then open:

```text
http://localhost:8000/
```

Using a local HTTP server is the safest way to avoid browser restrictions around module loading, WebAssembly, and file APIs.

## CSV format

PyData exports the following columns:

```csv
id,name,category,value,notes,created_at
```

For imports, at minimum these columns are required:

```text
name,category
```

`value` and `notes` are optional. Other columns are ignored by the importer. Imported data replaces the current table contents.

## Deployment

Because the runtime application consists of static files only, PyData can be deployed to any static hosting service that serves `index.html`, including services such as:

- Vercel
- GitHub Pages
- Netlify
- Cloudflare Pages
- Amazon S3 + CloudFront
- Any standard static web server

No serverless function is necessary.

## CDN dependencies

PyData intentionally uses versioned CDN URLs for the main JavaScript and PyScript dependencies:

- PyScript 2026.7.1
- HTMX 2.0.10
- Alpine.js 3.15.12
- Motion 13.0.0
- Tailwind CSS Play CDN

The project is designed for the **zero-build / single-file** constraint. For an offline or highly controlled production environment, vendor the CDN assets locally and update the `<script>` and `<link>` tags accordingly.

## Design notes

### PyScript and SQLite

The database is constructed in Python with `sqlite3.connect(":memory:")`. PyScript runs that Python through Pyodide in the browser. No server-side SQL connection exists.

### HTMX in a static application

HTMX is primarily known for HTTP-driven HTML updates. Since PyData intentionally has no backend, the application uses HTMX's event and `hx-on` capabilities for local browser events instead of making HTTP requests to a server. PyScript performs the database work and refreshes the table.

### Alpine.js

Alpine.js owns small pieces of UI state such as the light/dark preference. Database state remains in Python/SQLite to keep the requested architecture explicit.

### Motion

Motion animates the initial interface and newly rendered table rows. Database operations do not depend on the animation layer.

## Data and privacy

The database exists in the current page's memory. PyData does not send records to a PyData backend because there is no backend. CSV import reads the selected file locally in the browser, and CSV export creates the download locally in the browser.

## Limitations

- Reloading the page resets the in-memory SQLite database.
- CSV import intentionally replaces the current table contents.
- Tailwind's Play CDN is convenient for a no-build demo/application but is not equivalent to a precompiled Tailwind production build.
- The project depends on network access to the external CDN resources unless those resources are vendored locally.

## License

No license is specified by default. Add a license file or license section before publishing PyData as an open-source project.

## References

- PyScript documentation: https://docs.pyscript.net/
- Pyodide documentation: https://pyodide.org/
- HTMX documentation: https://htmx.org/docs/
- Alpine.js documentation: https://alpinejs.dev/
- Motion documentation: https://motion.dev/
- Tailwind CSS documentation: https://tailwindcss.com/docs
