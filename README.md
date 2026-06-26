# VIIRS_Night-Time-Light-Animation

Creating an end-to-end temporal night time light visualization with Google Earth Engine and Google Colab

# VIIRS Nighttime Lights Indonesia (2016–2025)

This project generates a 10-year animation of Indonesia's nighttime lights (2016–2025) using VIIRS satellite data from Google Earth Engine. The final outputs are an animated GIF and a 4K MP4 video showing monthly changes in artificial light intensity (cities, roads, industrial zones, etc.) across the entire Indonesian archipelago.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/YOUR_REPO/blob/main/01_Script/viirs_nightlights_indonesia_animation.ipynb)

---

## Repository Structure

```
root/
├── 01_Script/
│   └── viirs_nightlights_indonesia_animation.ipynb   # Main notebook
├── 02_Output/
│   ├── viirs_indonesia_2016_2025_V1.gif              # Animated GIF (~5.7 MB)
│   └── viirs_indonesia_2016_2025_4K_V1.mp4           # 4K MP4 video (~19.7 MB)
├── .gitattributes
└── README.md
```

The `01_Script` folder contains the single notebook that drives the entire workflow. All generated outputs — both the GIF and the MP4 — are saved to the `02_Output` folder. The `.gitattributes` file is configured to handle large binary files (GIF and MP4) correctly when pushing to GitHub via Git LFS.

---

## How the Notebook Works

The notebook runs sequentially through several stages. The first stage handles installation and authentication, where all required Python dependencies (`earthengine-api`, `geemap`, `Pillow`, `imageio`, `moviepy`, and others) are installed automatically, followed by a Google Earth Engine login via a browser popup. This step only needs to be completed once per Colab session.

After authentication, the notebook moves into parameter configuration: defining Indonesia's bounding box (94°–141.5°E, 11.5°S–6.5°N), downloading a Natural Earth shapefile for country border overlays, and setting visualization parameters such as the color palette, frame resolution, and the data year range. All of these values are centralized in a single cell so they can be adjusted without touching any other part of the code.

The core of the process is the data fetching stage, where the notebook sends 120 individual requests to the GEE server — one request per month from January 2016 through December 2025. Each request returns one frame as a satellite image thumbnail. This stage takes approximately 2–5 minutes depending on internet speed and GEE server response time.

Each successfully downloaded frame then goes through an annotation stage, where a month-year label, title, progress bar, color legend, and credit text are drawn programmatically onto the image using Pillow. If any frame fails to download, a black placeholder frame is used instead to ensure the animation always contains exactly 120 frames with no gaps.

Finally, all frames are assembled into two outputs: an animated GIF (1000×380 px, 300 ms per frame) and a 4K MP4 video (3840×1456 px, 4 fps, H.264 codec). Both files can be downloaded directly to your local computer from within Colab.

---

## How to Use in Google Colab

**Prerequisite:** You must have an active Google Earth Engine account. If you don't have one yet, register for free at [https://earthengine.google.com](https://earthengine.google.com). Approval typically takes a few minutes to a few hours.

**Step 1 — Open the notebook in Colab.** Click the *Open in Colab* badge at the top of this README, or open it manually by going to [https://colab.research.google.com](https://colab.research.google.com), selecting the **GitHub** tab, and entering this repository's URL.

**Step 2 — Run Cell 1 (Install & Authenticate).** This cell installs all required libraries and triggers a Google Earth Engine login popup. Follow the on-screen instructions to log in and paste the authentication code into the provided field. Once authenticated, replace `'your-project-id'` in the line `ee.Initialize(project='your-project-id')` with your own Google Cloud Project ID. This ID can be found in the top-right corner of [https://code.earthengine.google.com](https://code.earthengine.google.com) after logging in.

**Step 3 — Run all cells in order.** Use the menu **Runtime → Run all**, or run each cell one by one from top to bottom. Do not skip or reorder cells, as each one depends on the output of the previous.

**Step 4 — Wait for the frame download process (Cell 6).** This is the longest step, taking around 2–5 minutes for all 120 frames. A progress bar will display real-time status. Do not close or refresh the browser tab while this cell is running.

**Step 5 — Download the outputs.** Once all cells have finished, run Cell 10 to download the GIF and Cell 11 to download the MP4 to your local computer. A live animation preview is also available directly in Cell 9's output.

---

## Customization

All adjustable parameters are centralized in **Cell 3 (Parameter Configuration)**, so no other cells need to be modified. Commonly changed values include `START_YEAR` and `END_YEAR` to adjust the data range, `VIZ_PARAMS['max']` to control display brightness (a higher value reveals more detail in dimly lit areas), `FRAME_DURATION` to set the GIF playback speed (in milliseconds per frame), and `IMG_WIDTH` to change the GIF resolution (height is calculated automatically based on a geographically corrected aspect ratio).

---

##Libraries Used

- `earthengine-api` & `geemap` — access and download data from Google Earth Engine
- `Pillow` — image processing and per-frame annotation drawing
- `imageio` & `moviepy` — export frames to MP4 video using H.264 codec
- `geopandas` & `shapely` — read country border shapefiles and convert them to GEE Geometry
- `matplotlib` — image grid preview inside the notebook
- `tqdm` — progress bar during the frame download process

---

## Dataset

The data source is **NOAA/VIIRS/DNB/MONTHLY_V1/VCMSLCFG**, a monthly composite of nighttime radiance data from the VIIRS (Visible Infrared Imaging Radiometer Suite) sensor, jointly operated by NOAA and NASA. The band visualized is `avg_rad` (average radiance in nanoWatts/cm²/sr). This dataset is publicly available on Google Earth Engine and covers data from 2012 to the present.

---

## Credits

Created by **Mahmud Zakariyah** (2026). Data sourced from NOAA VIIRS/DNB via Google Earth Engine. Country border geometries use Natural Earth data (naturalearth.com).
