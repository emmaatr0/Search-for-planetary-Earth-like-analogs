# Roadmap: ideas to take the project further

## Globes
- **Real relief on Mars**: MOLA elevation as a displacement / bump map, and live NASA Mars Trek tiles (HiRISE / CTX) when the
  globe is zoomed on Jezero, with a smooth level-of-detail switch.
- **Perseverance traverse**: the rover track and sample sites over the crater, with a timeline slider (sols).
- **Mars and Earth side by side**: two synchronised globes, Jezero on one and the selected Earth analog on the other, same
  scale, with elevation profiles under them.
- **Scale and context**: a ruler and a "Paris for scale" ghost outline of the 45 km crater.
- **Guided tour / presenter mode**: one key steps through the pitch (funnel → top 100 → site #4 → Jezero) with captions and
  camera moves; full-screen mode for the jury.
- **Moon profile**: the same engine with a lunar texture and a lunar scoring profile (the challenge also covers the Moon).
- **Earth globe**: density heatmap instead of points at world zoom, arcs between an Earth site and Jezero, real day/night
  terminator, 3D terrain of the selected site.
- **Offline**: bundle three.js and a low-resolution Earth texture in the app so the Earth globe works without internet.

## Interaction
- **"What if" weights**: sliders for the climate / terrain / rock weights (and thresholds) that re-rank the sites live. The
  per-dimension cell scores are already in the CSV, so the geometric mean can be recomputed in the app.
- **Shareable links** to a site and a stage (`?site=4&stage=top100`), so a jury member can open exactly what you show.
- **Site brief**: a one-page PDF per site (scores, access, analogy, tests) for a field team.
- **EN / FR toggle** and keyboard navigation (previous / next site, stage buttons).

## Science
- **Sensitivity analysis** of weights and thresholds (tornado chart, rank stability), and uncertainty on the scores.
- **Multi-year averages** instead of a single year (2023).
- **Hyperspectral data (EMIT)** on the top 20 for carbonates and Fe/Mg clays.
- **A polar profile** to recover Haughton and the Dry Valleys.
- **Calibrate the clay signal** against known sites; stop defining the "new site" radii by hand (use mapped analog regions).

## Engineering
- Split `app.py` (4,500 lines, with embedded data) into a package: pages, globes (HTML / JS templates in their own files),
  data loading, theme.
- Keep a single source of truth for the data instead of CSV files plus an embedded copy; add a script that builds the app's
  data file from the Earth Engine exports.
- Add the Earth Engine scripts (`modele_final_v1`, `raffinement_argiles_top100`, `acces_top100`) and a notebook that
  reproduces the figures of the report.
- Lighter world map (one GeoJSON layer or vector tiles instead of 4,553 markers), better mobile layout, more tests (selection
  and filters), `ruff` + pre-commit, a Dockerfile, and one-click deployment on Streamlit Community Cloud.
