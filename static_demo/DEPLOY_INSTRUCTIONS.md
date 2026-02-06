# How to Deploy Surya Sutra (Demo Version)

This folder contains a standalone, static version of the **Surya Sutra** dashboard designed for demonstration purposes. It does not require the Python backend to run.

## 🚀 Easy Deployment Options

### Option 1: GitHub Pages (Recommended)
1.  Upload this `static_demo` folder to a GitHub repository.
2.  Go to **Settings** > **Pages**.
3.  Select `main` branch and the folder (if possible, or repo root).
4.  Your site will be live at `yourusername.github.io/repo-name`.

### Option 2: Netlify / Vercel (Drag & Drop)
1.  Go to [Netlify Drop](https://app.netlify.com/drop).
2.  Drag and drop the `static_demo` folder onto the page.
3.  It will deploy instantly. Update the site name to something cool like `surya-sutra-demo.netlify.app`.

### Option 3: Local Demo
1.  Simply double-click `index.html`.
2.  It opens in your browser and works perfectly offline!

## ⚠️ Limitations
- This version uses a **simulation engine** within the browser instead of the real Python backend.
- The optimization logic is simplified for visual demonstration.
- Data export works, but is generated locally.
