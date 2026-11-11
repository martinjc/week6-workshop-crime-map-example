# South Wales Crime Map Visualisation

An interactive web mapping project using [Leaflet.js](https://leafletjs.com/) as part of [CompJ workshops](https://github.com/CompJCDF/workshops26) to show different ideas for visualising point-based geographic data. These maps visualise a year's worth of street-level crime data across South Wales (September 2025 – September 2026). 

The maps were all created by an LLM to illustrate how simple visualisations can be created with simple prompting. As such the code has not been reviewed for correctness or quality and is not considered as exemplar code. 

The project is structured as five progressive versions, evolving from a simple, unadorned density heatmap into a fully interactive tool with category filtering and location breakdowns.

---

## The Five Map Versions

### 1. `index.html` — Baseline Plain Heatmap
* **Goal**: A simple, unadorned visualisation of the raw crime dataset.
* **Features**:
  * Full-viewport Leaflet map centered over South Wales (`[51.62, -3.55]`, zoom level `10`).
  * Minimalist UI with no extra controls or widgets.
  * Plots every crime record as a small (`2px`), semi-transparent (`fillOpacity: 0.08`) red dot.
  * Overlapping dots naturally accumulate into a density heatmap showing high-frequency crime areas.
  * Backed by Leaflet's HTML5 Canvas renderer (`L.canvas`) to render >136,000 points smoothly.

---

### 2. `scaled.html` — Dynamic Zoom-Scaled Dots
* **Goal**: Maintain visual legibility at both regional and street scales.
* **Additional Functionality**:
  * Inherits the base map and layout from `index.html`.
  * Introduces dynamic zoom scaling via an exponential scaling formula:
    $$\text{radius} = \max\left(1.5,\, 2 \times 1.35^{(\text{zoom} - 10)}\right)$$
  * At wide view (zoom 10), dots remain compact (`2px`) for regional hotspot analysis.
  * As you zoom in toward neighborhood and street level (up to zoom 18), dots scale smoothly up to `~22px` to clearly reveal local cluster patterns.
  * Redraws efficiently on `zoomend` with zero tile lag.

---

### 3. `coloured.html` — Crime Type Categorisation
* **Goal**: Differentiate crime types across the region through colour coding.
* **Additional Functionality**:
  * Built on `scaled.html` (retains dynamic zoom scaling).
  * Parses column 8 (`Crime type`) for all records.
  * Assigns distinct colours to all 14 crime categories in the dataset:
    * **Violence and sexual offences**: Rose Red (`#e11d48`)
    * **Anti-social behaviour**: Blue (`#2563eb`)
    * **Public order**: Amber (`#d97706`)
    * **Shoplifting**: Purple (`#9333ea`)
    * **Criminal damage and arson**: Orange (`#ea580c`)
    * **Vehicle crime**: Cyan (`#0891b2`)
    * **Other theft**: Green (`#16a34a`)
    * **Drugs**: Indigo (`#4f46e5`)
    * **Burglary**: Pink (`#db2777`)
    * **Other crime**: Slate Grey (`#64748b`)
    * **Possession of weapons**: Dark Red (`#b91c1c`)
    * **Robbery**: Violet (`#7c3aed`)
    * **Bicycle theft**: Teal (`#0d9488`)
    * **Theft from the person**: Gold (`#ca8a04`)
  * Overlapping dots of different categories blend visually to reveal the dominant crime profile of each neighborhood.

---

### 4. `interactive.html` — Legend & Hover Pop-ups
* **Goal**: Provide analytical context on hover and a clear color reference.
* **Additional Functionality**:
  * Built on `coloured.html` (retains zoom scaling and colour coding).
  * **Floating Legend**:
    * Positioned in the top-right corner.
    * Displays each category with its colour dot, category label, and total regional count sorted by volume.
  * **2D Spatial Grid Indexing**:
    * Groups the 136,600+ records into ~14,500 unique geographic snap coordinates.
  * **Interactive Hover Pop-up**:
    * Hovering over any dot immediately reveals a pop-up at that location.
    * Displays the location name (e.g. *"On or near High Street"*), the total number of recorded crimes, and a sorted breakdown of every crime category that occurred there.

---

### 5. `filtered.html` — Interactive Category Filtering
* **Goal**: Give the user full control to isolate and inspect individual crime types.
* **Additional Functionality**:
  * Built on `interactive.html` (retains zoom scaling, colour coding, legend, and hover pop-ups).
  * **Click-to-Toggle Categories**:
    * Click any category in the legend to toggle its visibility on the map on or off.
    * Disabled categories display a dimmed label with strikethrough styling and a hollow dot indicator.
  * **Quick Filter Buttons**:
    * Added **"All"** and **"None"** buttons to the legend header to easily isolate a single crime type with two clicks (e.g., click *"None"*, then click *"Burglary"*).
  * **Instant Canvas Updates**:
    * Dynamically sets inactive marker radii to `0` and triggers an immediate canvas redraw in `< 20ms`.
  * **Dynamic Count & Synchronised Pop-ups**:
    * Legend displays live visibility totals (e.g. *"52,529 / 136,540 shown"*).
    * Hover pop-ups automatically update to only show and count the currently active crime categories.

---

## Comparison Summary

| Version | File | Dot Sizing | Colour Scheme | Legend | Hover Pop-up | Category Filtering |
|---|---|---|---|---|---|---|
| **1. Baseline** | [`index.html`](index.html) | Fixed (2px) | Single Red | No | No | No |
| **2. Scaled** | [`scaled.html`](scaled.html) | Zoom-Scaled | Single Red | No | No | No |
| **3. Coloured** | [`coloured.html`](coloured.html) | Zoom-Scaled | 14 Crime Colours | No | No | No |
| **4. Interactive** | [`interactive.html`](interactive.html) | Zoom-Scaled | 14 Crime Colours | Yes | Yes (Location Breakdown) | No |
| **5. Filtered** | [`filtered.html`](filtered.html) | Zoom-Scaled | 14 Crime Colours | Yes | Yes (Synchronised) | Yes (Click-to-Toggle + All/None) |

---

## Viewing the Visualisations online:

* **Version 1**: [Baseline](https://martinjc.github.io/week6-workshop-crime-map-example/)
* **Version 2**: [Scaled](https://martinjc.github.io/week6-workshop-crime-map-example/scaled.html)
* **Version 3**: [Coloured](https://martinjc.github.io/week6-workshop-crime-map-example/coloured.html)
* **Version 4**: [Interactive](https://martinjc.github.io/week6-workshop-crime-map-example/interactive.html)
* **Version 5**: [Filtered](https://martinjc.github.io/week6-workshop-crime-map-example/filtered.html)


## Running the Visualisations Locally

Because the web pages load the raw CSV files via `fetch()`, run a local HTTP server from the project directory:

```bash
# Start server on port 8080
python3 -m http.server 8080
```

Then open any of the following URLs in your web browser:

* **Version 1**: [http://localhost:8080/index.html](http://localhost:8080/index.html)
* **Version 2**: [http://localhost:8080/scaled.html](http://localhost:8080/scaled.html)
* **Version 3**: [http://localhost:8080/coloured.html](http://localhost:8080/coloured.html)
* **Version 4**: [http://localhost:8080/interactive.html](http://localhost:8080/interactive.html)
* **Version 5**: [http://localhost:8080/filtered.html](http://localhost:8080/filtered.html)

---

## Technical Notes
* **Libraries**: Uses vanilla Leaflet.js 1.9.4 with OpenStreetMap tile layers.
* **Performance**: With ~140,000 records, standard SVG/DOM markers cause browser lockups. All versions employ Leaflet's `preferCanvas: true` and an explicit `L.canvas()` layer.
* **Fast Parsing**: Custom sequential character index parsing extracts coordinates and categories without the overhead of heavy CSV parsing libraries.
* **Spatial Grid**: In versions 4 and 5, spatial bucketing prevents iterating over all 14,500 unique locations on every cursor movement, keeping hover checks under 0.1ms.

---

## Data
Contains data released under an [Open Government Licence 3.0](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/)