<h1 align="center">✦ Planetary Analog Finder</h1>

<p align="center">
  <b>Where on Earth looks like Jezero crater, Mars?</b><br>
  NASA Space Apps Challenge 2026 · Team Proteus 2 · <a href="#team">Proteus Group</a>
</p>

<p align="center">
  <img alt="Python" src="https://img.shields.io/badge/python-3.10%2B-blue">
  <img alt="Streamlit" src="https://img.shields.io/badge/built%20with-Streamlit-ff4b4b">
  <img alt="License" src="https://img.shields.io/badge/license-MIT-green">
</p>

<p align="center">
  <img src="docs/screenshots/mars-globe.png" alt="Planetary Analog Finder: the Mars globe locked on Jezero crater" width="900">
</p>

We scanned every piece of land on Earth (60° S to 80° N) with open satellite data and kept the places that look like
**Jezero crater**, the ancient lake where NASA's Perseverance rover landed in February 2021. This repository holds the
interactive Streamlit app that presents the results: a 3D Earth globe and map of the candidate sites, the top 100 checked
for field access, a comparison tool, and a rotating Mars globe that locks onto Jezero.

<details>
<summary><b>🇫🇷 En bref</b></summary>

**Planetary Analog Finder** cherche sur Terre les lieux qui ressemblent au cratère **Jezero** (Mars) : climat, relief et
roches. Un modèle de score expert (pas de machine learning) calculé dans Google Earth Engine retient 4 553 sites de 25 km
sur 138 millions de km² de terres ; les 100 meilleurs sont ensuite contrôlés (accessibilité, zones disputées, aires
protégées, signal argileux). L'application Streamlit présente ces résultats avec une carte, un globe 3D de la Terre, un
comparateur de sites et un globe de Mars qui se verrouille sur Jezero. Le site recommandé est le n° 4, dans la Puna
argentine. Le rapport complet (en français) est dans [`data/Rapport_analogues_Jezero.pdf`](data/Rapport_analogues_Jezero.pdf).
</details>

## Key results

| | |
|---|---|
| Land analysed (60° S – 80° N) | 138.3 million km² |
| Kept after the three tiers | 734,600 km² (0.53 %) |
| Candidate sites (25 km cells) | **4,553** |
| Top 100: accessible / protected area / disputed territory | 84 / 6 / 10 |
| Recommended site | **#4, Puna (Argentina)**, −25.16, −68.16: grade A on climate, terrain and rock, a clay signal, 5.5 h from a city |

The model finds known analog regions on its own (Altiplano-Puna, Qaidam basin, Tibetan Plateau) without ever being told
where they are, and rejects the negative tests (sand sea, rainforest, France).

## The app

| Page | What you can do |
|---|---|
| **1 · The model** | Run the funnel stage by stage (climate → terrain → rock → 4,553 sites → top 100) on a flat map or a **3D globe**. Click a site to open its panel. Key numbers, method, validation and limits are explained below the map. |
| **2 · Top 100 sites** | Filter by field status, access time and novelty. Switch between *before / after* the field check (disputed borders, protected areas, travel time). Click a table row or a map point. |
| **3 · Compare sites** | Which site is best for which kind of experiment: landing and hazard mapping, rover driving, instruments and geology, astrobiology, thermal stress, crewed-mission simulation. Radar and ranking per experiment. |
| **4 · Jezero vs new sites** | The **Mars globe**, the Jezero fact sheet with sources, and a dimension-by-dimension comparison of Jezero with our new sites (*Jezero match* score). |

Handy features: stage buttons instead of sliders, a **site panel** (summary, analogy, data, uses) with previous / next
navigation, a **shortlist** you can download as CSV, a *Find a site* search in the sidebar, a *Compare with Jezero* shortcut
from any site, colour-blind-friendly status shapes (circle / diamond / square), and a Proteus Group look and feel.

### The Mars globe

A WebGL globe written for this project, with the Mars texture **built into the app** (it works offline):

- slow rotation, drag to rotate, scroll to zoom;
- **Locate Jezero** flies to the crater (18.38° N, 77.58° E), then locks on with a reticle, sonar rings, a target card and a
  NASA orbital image;
- relief shading, terminator, atmosphere glow, lat/lon grid;
- optional labels for regions (Olympus Mons, Valles Marineris, Hellas, Isidis...) and landing sites.

## How the model works

It is an **expert score**, not a machine-learning model: only a handful of recognised Mars analogs exist, far too few to
train on, and an explainable score is easier to defend. Three tiers act as a funnel; every parameter is turned into a score
between 0 and 1 with fixed bounds (so a score never depends on the area studied) and combined with a weighted geometric mean
(one bad criterion pulls the whole score down).

| Tier | Question | Data | Main parameters |
|---|---|---|---|
| 1 · Climate | Cold, dry and lifeless like Mars? | MODIS, TerraClimate, JRC | day–night swing (30 %), mean cold (20 %), vegetation NDVI (25 %), aridity (25 %) |
| 2 · Terrain | Does it look like a crater floor? | MERIT DEM 90 m | share of flat land (40 %), local relief 100–500 m (35 %), basin position (25 %) |
| 3 · Rock | Mafic rock like Jezero's floor? | ASTER GED 100 m | mafic index (band 12 / band 13) |

`final score = climate^0.35 × terrain^0.30 × rock^0.35`. The world is cut into 0.25° cells (about 25 km). A cell becomes a
site if at least 10 % of its land passes the three tiers; sites are ranked by the cell's mean score and get a profile such as
`Env A · Relief A · Compo B`. The **top 100** is then enriched with a Sentinel-2 clay ratio, travel time to the nearest city
(Oxford / MAP), disputed-territory and protected-area (WDPA) flags. Full details and validation: see the
[report](data/Rapport_analogues_Jezero.pdf) (French) and page 1 of the app.

## Quick start

```bash
git clone https://github.com/<your-account>/planetary-analog-finder.git
cd planetary-analog-finder
python -m venv .venv && source .venv/bin/activate     # Windows: .venv\Scripts\activate
pip install -r requirements.txt
streamlit run app.py
```

The app reads the results from `data/` (or from the folder named in the `PAF_DATA_DIR` environment variable) and ships an
embedded fallback copy, so it also starts without any file. An internet connection is needed for the 3D Earth globe
(three.js and map tiles), the base maps and the web font; **the Mars globe and the results work offline**.

Run the tests (every page, the stage buttons, the globe toggle, the site panel):

```bash
pip install pytest && pytest -q
```

## Data

| File | Content |
|---|---|
| `data/classement_final_monde.csv` | the 4,553 candidate sites, best first |
| `data/acces_top100V2_avecapprofondissement.csv` | the top 100 with clay signal and field-access columns |
| `data/Rapport_analogues_Jezero.pdf` | the full report (French) |

Column definitions: [`data/README.md`](data/README.md). The scores are computed in Google Earth Engine; the Earth Engine
scripts are not part of this repository yet (see the [roadmap](docs/ROADMAP.md)). To refresh the app, export new CSVs
with the same columns and drop them in `data/`.

## Project structure

```
planetary-analog-finder/
├── app.py                   # the Streamlit app (pages, site panel, Earth and Mars globes)
├── data/                    # results (CSV) and report (PDF)
├── docs/
│   ├── ROADMAP.md           # improvement ideas
│   └── screenshots/
├── tests/test_smoke.py      # every page loads, main interactions work
├── .streamlit/config.toml   # Proteus Group theme (dark, amber accent)
├── .github/workflows/ci.yml # compile + tests on every push
└── requirements.txt
```

## Limits

- Earth cannot reproduce Mars: gravity (0.38 g), pressure (≈ 6–7 mbar), radiation and the Martian thermal regime. The score
  measures resemblance on a few dimensions, never identity.
- Carbonates cannot be detected with ASTER at 100 m (hyperspectral data such as EMIT would be needed); the mafic index cannot
  tell a basalt from an andesite.
- The clay signal is a generic, uncalibrated proxy: a prioritisation index, not a proven detection.
- Polar deserts (Haughton, Dry Valleys) are missed because of the day–night swing criterion.
- Access is physical only: visas, permits and military zones are not covered.
- One year of data (2023), mixed resolutions (90 m to about 4 km), weights and thresholds chosen by the team (justified but
  not optimised, no sensitivity analysis yet).
- "New site" means *not near any of the 8 analog stations listed in the app*; the count depends on the area radii used.

## Use of AI

We used an AI assistant (Claude, by Anthropic) to help write and debug code (Google Earth Engine scripts and this Streamlit
app) and to draft explanatory text. The team chose the method, parameters and thresholds, ran all Earth Engine analyses,
checked results against test sites and published sources, and reviewed every change. No image or data in the app was
generated by AI.

## Team

**Team Proteus 2** (Paris), from Proteus Group, the tech association of our engineering school: Lilian JAOUEN (composition block:
ASTER and Sentinel-2 indices, test sites), Jaime SOUSA (environment block, model assembly, world scans, top-100
refinements), Thiya PAPUGE and Emma TROTIGNON (web app and integration of the Earth Engine results). Louis worked on the
terrain block, which the team took over on the day.

## Credits

- **Challenge**: [NASA Space Apps Challenge 2026](https://www.spaceappschallenge.org/2026/challenges/), *Identify Earth
  Locations that Analog the Permanent Moon Base Locations and Mars*.
- **Satellite data (Google Earth Engine)**: MODIS MOD11A2 / MOD13A2 (NASA), TerraClimate, JRC Global Surface Water, MERIT DEM,
  ASTER GED AG100 (NASA / METI), Sentinel-2 (ESA / Copernicus), Oxford MAP accessibility 2015, LSIB borders (US Department of
  State), WDPA protected areas (UNEP-WCMC).
- **Mars globe texture**: Viking Orbiter colour mosaic of Mars (NASA/JPL/USGS), taken from PyVista's open test data and
  lightly contrast-enhanced. Check its licence before any redistribution outside this project.
- **Jezero from orbit**: NASA/JPL-Caltech/MSSS/JHU-APL, [PIA23239](https://photojournal.jpl.nasa.gov/catalog/PIA23239).
- **Earth base maps**: Esri (World Dark Gray Base, World Imagery, World Topo Map). **Fonts**: Inter. **3D**: three.js (MIT).
- **Mars facts**: NASA / JPL mission results, with numbered sources on page 4 of the app.

## License

Code: [MIT](LICENSE). Third-party data and imagery keep their own licences (see Credits).
