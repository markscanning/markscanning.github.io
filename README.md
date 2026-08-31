# Progressive Value Reading Corpus

A Vue 3 web application for browsing and filtering the Progressive Value Reading corpus. The live site is published via GitHub Pages from the `webpage` branch.

## Requirements

| Tool | Version |
|------|---------|
| [Node.js](https://nodejs.org/) | 18 or later (20 LTS recommended) |
| npm | Comes with Node.js |

This project uses [Vite](https://vite.dev/) 6 and [Vue](https://vuejs.org/) 3. No other global tools are required.

## Getting started

Clone the repository and install dependencies:

```bash
git clone https://github.com/progressive-value-reading/progressive-value-reading.github.io.git
cd progressive-value-reading.github.io
npm install
```

Run the development server with hot reload:

```bash
npm run dev
```

Open the URL printed in the terminal (typically `http://localhost:5173`).

Other useful commands:

```bash
npm run build    # production build → dist/
npm run preview  # serve the production build locally
```

## Project structure

- `src/` — Vue source code (components, utilities, styles)
- `public/` — static assets copied as-is into the build (`images/`, `Case_Coding_Final.csv`, etc.)
- `dist/` — production build output (generated; not committed on `main`)

## Deploying to GitHub Pages

The published website is **not** served from `main`. GitHub Pages is configured to use the **`webpage`** branch, which contains only the built static files.

To update the live site after making changes on `main`:

1. Build the project:

   ```bash
   npm run build
   ```

2. Check out the `webpage` branch and copy the contents of `dist/` into the branch root (replacing the previous build):

   ```bash
   git checkout webpage
   cp -r dist/* .
   git add .
   git commit -m "Update site build"
   git push origin webpage
   ```

3. Switch back to your development branch when done:

   ```bash
   git checkout main
   ```

After the push, GitHub Pages will serve the updated files from `webpage`. It may take a minute or two for changes to appear.

## Branches

| Branch | Purpose |
|--------|---------|
| `main` | Source code and development |
| `webpage` | Built static site served by GitHub Pages |
