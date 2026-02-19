# Setup and Installation Guide

## Prerequisites

- **Git** — for cloning the repository
- **A web browser** — Chrome, Firefox, Safari, or Edge (modern version)
- **A text editor** — VS Code, Sublime Text, or any editor of your choice
- **Vercel CLI** (optional) — only needed for deploying to Vercel

No Node.js, npm, or any package manager is required. There are no dependencies to install.

## Clone the Repository

```bash
git clone https://github.com/erin-heilman/cms-star-rating-analyzer.git
cd cms-star-rating-analyzer
```

## Running Locally

Since this is a single static HTML file, there are several ways to run it locally:

### Option 1: Open Directly in Browser

The simplest approach — just open the file:

```bash
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows
```

Or double-click `index.html` in Finder/Explorer.

### Option 2: Python HTTP Server (Recommended)

Running a local server is preferred because it more closely mimics production behavior:

```bash
# Python 3
python3 -m http.server 3847

# Python 2
python -m SimpleHTTPServer 3847
```

Then open http://localhost:3847 in your browser.

### Option 3: Node.js HTTP Server

If you have Node.js installed:

```bash
npx http-server -p 3847
```

Then open http://localhost:3847 in your browser.

### Option 4: VS Code Live Server

1. Install the **Live Server** extension in VS Code
2. Right-click `index.html` and select **Open with Live Server**
3. The browser will open automatically and reload on file changes

## Deploying to Vercel

### First-Time Setup

1. Install the Vercel CLI:
   ```bash
   npm install -g vercel
   ```

2. Log in to Vercel:
   ```bash
   vercel login
   ```

3. Link the project to your Vercel team:
   ```bash
   vercel link --scope your-team-name
   ```

4. Deploy to production:
   ```bash
   vercel --prod
   ```

### Subsequent Deployments

If the project is connected to GitHub (recommended), every push to `main` will auto-deploy to production.

For manual deployments:

```bash
# Preview deployment (creates a unique URL for testing)
vercel

# Production deployment
vercel --prod
```

### Current Deployment

| Environment | Team | URL |
|---|---|---|
| Production (2026) | Medisolv | https://cms-star-rating-analyzer-sandy.vercel.app |
| Production (2025) | eheilman-5631 | https://cms-star-rating-analyzer.vercel.app |

## Vercel Configuration

The project uses Vercel's default static file serving. No `vercel.json` configuration is needed — Vercel automatically detects the `index.html` and serves it as a static site.

If a `vercel.json` file is ever needed (e.g., for redirects or custom headers), create it in the project root:

```json
{
  "rewrites": [
    { "source": "/(.*)", "destination": "/index.html" }
  ]
}
```

## Testing with a Sample CSV

1. Start the application using any method above
2. Obtain a Hospital Specific Report (HSR) CSV file from CMS QualityNet
3. Upload the CSV using the file upload area or drag-and-drop
4. Verify the 4 tabs display correctly: Overview, Summary Scores, Individual Measures, Analysis & Insights

### What to Check

- Star rating displays correctly and matches the CSV
- Peer group assignment matches (`3 Measure Groups`, `4 Measure Groups`, or `5 Measure Groups`)
- Summary score matches the CSV's `Star_Rating_Results-HOSP_RESULT-summary_score` value
- All reported measures appear with z-scores and performance badges
- Quartile rankings show the hospital's position relative to national benchmarks
- Threshold ranges match the hospital's peer group
