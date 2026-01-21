# Kenya Hiking Spot Analysis

A comprehensive geospatial analysis project for exploring and mapping hiking destinations across Kenya. This project combines web scraping, data cleaning, geocoding, and spatial analysis to create a structured dataset of Kenyan hiking spots with geographic coordinates.

## Project Overview

This repository contains an end-to-end pipeline for collecting, cleaning, and geocoding hiking trail data from Kenya. The project transforms raw web scraped data into a structured, spatially-indexed dataset that can be used for:

- Geographic analysis of hiking trails
- Route planning and trail recommendations
- Elevation profile studies
- Regional hiking distribution analysis
- Tourist and outdoor adventure research

## Project Structure

```
Hiking Spot/
├── scrapping.ipynb              # Web scraping notebook
├── geocoding.ipynb              # Geocoding and coordinate assignment
├── raw_hikes.csv               # Initial raw scraped data
├── norm_hikes.csv              # Cleaned and normalized data
├── norm_geocoded_hikes.csv     # Data with coordinates added
├── clean_geocoded.csv          # Final clean geocoded dataset
└── README.md                    # This file
```

## Technology Stack

- **Python 3.x**
- **Data Processing**: Pandas, NumPy
- **Web Scraping**: BeautifulSoup, Requests
- **Geocoding**: GeoPy (Nominatim), OpenStreetMap
- **Jupyter Notebooks** for interactive analysis and documentation
- **Excel** further cleaning

## Data Pipeline

### Stage 1: Web Scraping (`scrapping.ipynb`)

The scraping notebook extracts hiking trail data from hiking adventure websites by:

1. **Crawling multiple pages** (25+ pages) of hiking listings
2. **Extracting trail information** including:
   - Trail name and location
   - Difficulty level and ratings
   - Elevation data (min/max elevation, elevation gain/loss)
   - Distance and duration
   - Starting point and routes
   - Features and attractions
   - Park fees
3. **Filtering data** to include only Kenya-based trails
4. **Handling malformed data** and inconsistencies in source HTML

**Output**: `hikes_raw.csv`

### Stage 2: Data Cleaning & Normalization

The scraping notebook performs extensive data cleaning:

1. **Filtering**: Removes non-Kenya trails and unreliable data
2. **Column harmonization**: Consolidates similar columns
   - Multiple elevation columns → single standardized fields
   - Various distance formats → `total_distance`
   - Duration variations → `total_time`
   - Multiple location fields → `starting_point` and region fields
3. **Column deduplication**: Removes redundant metadata and timing columns
4. **Standardization**:
   - Normalizes column names (lowercase, spaces instead of underscores)
   - Cleans location strings (removes trailing punctuation)
   - Splits location data into `place_name` and `area` (county)

**Output**: `norm_hikes.csv`

### Stage 3: Geocoding (`geocoding.ipynb`)

The geocoding notebook converts location names into geographic coordinates:

1. **Geocoding Engine**: Uses OpenStreetMap Nominatim API
2. **Processing**:
   - Queries each hiking trail's starting point
   - Restricts results to Kenya using country codes
   - Extracts latitude, longitude, and full OSM address
   - Implements rate limiting (1 second between requests) to respect API limits
3. **Error Handling**: Gracefully handles timeouts and unavailable services
4. **Failure Tracking**: Identifies trails that couldn't be geocoded

**Output**: `norm_geocoded.csv`

### Stage   4: Further Cleaning in excel

The following was done:

1. **Geocoding**: Used Google maps to geocode the non geocoded data
2. **Processing**:
   - Dropped empty and unnecessary columns
   - normalized difficulty, duration, elevations and distance columns 
   - arranged by ascending name 
   - removed duplicated in name and routes

**Output**: `clean_geocoded.csv`

## Data Fields

The final  dataset includes the following fields:

| Field | Description |
|-------|-------------|
| `place_name` | Name of the hiking trail/peak |
| `elevation_max` | Maximum elevation (meters) |
| `elevation_min` | Starting elevation (meters) |
| `elevation_gain` | Total elevation gain (meters) |
| `total_distance` | Trail distance (km) |
| `total_time` | Duration to complete (hours/days) |
| `starting_point` | Trailhead or starting location |
| `routes` | Available hiking routes |
| `features_attractions` | Notable features and attractions |

## Usage

### Running the Pipeline

1. **Web Scraping**:
   ```
   Open and run scrapping.ipynb in Jupyter
   - Extracts data from hiking websites
   - Generates norm_hikes.csv
   ```

2. **Geocoding**:
   ```
   Open and run geocoding.ipynb in Jupyter
   - Reads norm_hikes.csv
   - Geocodes starting points to coordinates
   - Generates geocoded CSV files
   ```

### Loading Data

```python
import pandas as pd

# Load the final geocoded dataset
df = pd.read_csv('clean_geocoded.csv')

# Access geographic data
print(df[['place_name', 'area', 'lat', 'lon', 'elevation_max']].head())
```

### Filtering by Region

```python
# Get hikes in a specific county
central_hikes = df[df['area'] == 'Central Region']

# Find challenging hikes by elevation gain
challenging = df[df['elevation_gain'] > 1000]
```

## Key Challenges & Solutions

| Challenge | Solution |
|-----------|----------|
| Inconsistent data formatting across sources | Column harmonization and normalization |
| Multiple columns with same information | Coalesce function to merge similar fields |
| Trailing punctuation and whitespace | String cleaning and regex operations |
| Geocoding failures | Error handling and timeout management |
| Rate limiting on geocoding API | Implemented 1-second delays between requests |
| Location names outside Kenya | Country code filtering in geocoding queries |

## Data Quality

- **Total trails scraped**: 100+
- **Kenya-only trails**: Filtered to Kenya-based locations only
- **Geocoding success rate**: Tracks failed geocodes in `failed` variable
- **Data normalization**: 100+ source columns consolidated to ~15 standardized fields

## Geospatial Applications

This dataset can be used for:

1. **Mapping**: Visualize trails on interactive maps (Folium, Leaflet)
2. **Clustering**: Identify hiking regions and clusters
3. **Spatial Analysis**: Proximity analysis, accessibility studies
4. **Route Optimization**: Calculate distances between trailheads
5. **Elevation Profiles**: Analyze terrain difficulty
6. **Tourist Planning**: Regional hiking recommendations

## Potential Improvements

- Integrate additional geocoding services (MapBox) as fallback
- Add trail difficulty ratings and user reviews
- Implement caching to avoid re-geocoding
- Create visualization dashboard
- Add weather and seasonal data
- Integrate trail photos and detailed descriptions
- Add accessibility and permit requirements

## Dependencies

To run this project, install the required packages:

```bash
pip install pandas numpy beautifulsoup4 requests geopy
```

## Notes

- The project uses data from hiking adventure website and all data rights belong to the site owner
- The project uses OpenStreetMap's free Nominatim service, which has rate limits
- Web scraping respects robots.txt and includes proper user-agent headers
- Geocoding requests are throttled to avoid API overload
- Some older trail information may no longer be accurate

## Author
   Immaculate Khaoma (khaif-dev)

## License

This project and its data are provided for research and educational purposes.

## Contributing

To contribute improvements:

1. Update the data sources if URLs have changed
2. Enhance data cleaning logic for better accuracy
3. Add additional geocoding services
4. Improve visualization and analysis notebooks
5. Add new geospatial analysis features
