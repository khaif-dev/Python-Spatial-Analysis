# Walkability: Urban Accessibility Analysis

**Urban accessibility analysis measuring network-distance connectivity to everyday essential services in Kenya's major cities.**

---

## Overview

This project analyzes how well residents of Kenyan cities are connected to essential services:  education, healthcare, retail, and social life using **network-distance** measurement rather than Euclidean distance. The core methodology builds connectivity graphs via the Waxman model applied to real road networks, revealing which services are genuinely reachable within a 2 km walking/riding radius.

### Cities Analyzed

| City | County | Region |
|------|--------|--------|
| Mombasa | Mombasa County | Coast |
| Kisumu | Kisumu County | Nyanza |
| Nakuru | Nakuru County | Rift Valley |

---

## Features

- **POI Extraction** — Pulls points of interest from OpenStreetMap across four categories:
  - **Education**: schools, universities (`amenity=school`, `amenity=university`)
  - **Daily Needs**: supermarkets, convenience stores (`shop=supermarket`, `shop=convenience`)
  - **Social Life**: restaurants, cafes (`amenity=restaurant`, `amenity=cafe`)
  - **Health Services**: hospitals, clinics, pharmacies (`amenity=hospital`, `amenity=clinic`, `amenity=pharmacy`)
- **Network-Distance Connectivity Graphs** — Constructs Waxman graphs connecting POIs within 2 km of each other, with distances measured along actual road networks via `city2graph`
- **Interactive & Static Visualization** — Produces dark-themed connectivity maps with color-coded service layers using matplotlib and supports Folium-based interactive maps

---

## Methodology

### Waxman Graph Construction

The connectivity graph is built using the **Waxman random graph model**, adapted for spatial networks:

- **`r0 = 2000`** — Maximum connection radius (2 km network distance)
- **`beta = 0.5`** — Controls how sharply connection probability decreases with distance
- **Distance metric**: `network` — Distances computed along real street segments (not Euclidean)

Two POIs are connected in the graph only if they are both within the city boundary and genuinely reachable within 2 km via the road network. This produces one connectivity graph per service category per city.

### Workflow

1. **Geocode** each city and fetch its road network from OpenStreetMap (via OSMnx)
2. **Extract POIs** from OSM using tag-based queries (4 categories × 2000 m radius)
3. **Build Waxman graphs** using `city2graph.waxman_graph()` with network distances
4. **Visualize** connectivity edges overlaid on city boundaries with color-coded layers

---

## Project Structure

```
walkability/
├── Urban_accessibility_analysis.ipynb   # Main analysis notebook
├── README.md                            # This file
├── requirements.txt                     # Python dependencies
├── .gitignore                           # Ignored files/cache
│
├── assets/                              # Visualization outputs
│   ├── mombasa.png
│   ├── kisumu.png
│   └── nakuru.png
```

---

## Requirements

All dependencies are listed in `requirements.txt`:

```
geopandas
osmnx
folium
contextily
matplotlib
shapely
city2graph
```

Install with:

```bash
pip install -r requirements.txt
```

---

## Usage

### Running the Analysis

Open and run the Jupyter notebook:

```bash
jupyter notebook Urban_accessibility_analysis.ipynb
```

The notebook is self-contained and performs the following in order:

1. **Imports** — Loads all required libraries
2. **POI Counting** — Queries OSM for each service category in each city and prints counts
3. **Graph Construction** — Fetches road networks, extracts POIs, and builds Waxman connectivity graphs
4. **Visualization** — Renders dark-themed connectivity maps with a legend and city boundary overlay

### Key Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| `radius` | `2000` | Network-distance radius in meters |
| `beta` | `0.5` | Waxman decay parameter |
| `network_type` | `drive` | OSMnx network type for road data |
| `CRS` | `EPSG:32737` | UZA Zone 37S (Kenya projection) |

### Modifying Cities

Edit the `cities` list in the notebook to analyze different locations:

```python
cities = ['Mombasa, Kenya', 'Nakuru, Kenya', 'Kisumu, Kenya']
```

### Modifying POI Categories

Edit the `poi_queries` dictionary to change which OSM tags are queried:

```python
poi_queries = {
    'education': {'amenity': ['school', 'university']},
    'daily_needs': {'shop': ['supermarket', 'convenience']},
    'social_life': {'amenity': ['restaurant']},
    'health_services': {'amenity': ['hospital', 'clinic', 'pharmacy']}
}
```

---


## Data Sources

| Source | Usage |
|--------|-------|
| [OpenStreetMap](https://www.openstreetmap.org) | Road networks and POI data |
| [OSMnx](https://osmnx.readthedocs.io) | Data fetching and network analysis |

---

## Caching

OSMnx caches downloaded OSM data as JSON files in the `cache/` directory. This avoids re-downloading large datasets on repeated runs. The `cache/` directory is listed in `.gitignore` and should not be committed to version control.




