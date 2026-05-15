# AML-CTD-Tools

Jupyter notebooks for downloading, processing, and visualising CTD cast data collected with an **AML-6 CTD** in Barkley Sound (Bamfield Marine Sciences Centre, British Columbia).  
Developed for use in **EOSC 473/573** (Methods in Oceanography, University of British Columbia).

---

## Notebooks

| Notebook | Purpose |
|----------|---------|
| [`CTD_Data_Download.ipynb`](CTD_Data_Download.ipynb) | Download raw cast files from the AML-6 over its onboard WiFi network |
| [`CTD_Data_Extraction.ipynb`](CTD_Data_Extraction.ipynb) | Load, trim, and visualise casts — station map via **cartopy** |
| [`CTD_Data_Extraction_mmap.ipynb`](CTD_Data_Extraction_mmap.ipynb) | Same as above with station map via **py_m_map** (a Python port of M-Map which produces excellent oceanographic maps) |
| [`CTD_Data_Extraction_Batch.ipynb`](CTD_Data_Extraction_Batch.ipynb) | Batch-process all cast files and export one CSV per day |

---

## Quickstart

### 1 · Download cast files

Open `CTD_Data_Download.ipynb`. Set `PLATFORM = 'mac'` or `PLATFORM = 'windows'` at the top of the configuration cell, then run all cells. The notebook will:

1. Connect to the AML-6 WiFi network (`AML_A60178`).
2. List available cast files on the instrument.
3. Download new files to `~/Documents/CTD Data/<YYYY-MM-DD>/`.
4. Sort casts chronologically and flag incomplete files.

### 2 · Process and visualise

Open `CTD_Data_Extraction.ipynb` (or the `_mmap` variant). Set `DATE_PATTERNS` in the **USER CONFIGURATION** cell to match the date(s) of your casts, then run all cells (`Kernel → Restart & Run All`). The notebook produces:

- Diagnostic trim plot (full record vs. downcast)
- Station map overlaid on multibeam bathymetry
- Multi-panel CTD profiles (temperature, salinity, DO, cDOM, pH, σ₀)
- Multi-variable overlay profile
- Dissolved oxygen concentration and percent saturation
- Apparent Oxygen Utilization (AOU)
- Temperature–Salinity (T-S) diagram with isopycnals

### 3 · Export to CSV (optional)

Open `CTD_Data_Extraction_Batch.ipynb` and run all cells to process every day folder in `CTD Data/` and write one CSV per day. Useful for downstream analysis in other tools.

---

## Dependencies

Install into a conda environment:

```bash
conda create -n ctd python=3.11
conda activate ctd
conda install -c conda-forge numpy pandas xarray matplotlib cmocean seaborn gsw netcdf4 cartopy jupyter
pip install paramiko   # Windows download notebook only
```

> **py_m_map** — the `_mmap` extraction notebook uses `py_m_map`, a Python port of R. Pawlowicz's MATLAB m_map toolbox. This package is under active development and will be made available as a separate repository soon; installation instructions will be added here when it is released.

---

## Repository layout

```
AML-CTD-Tools/
├── CTD_Data_Download.ipynb           # Cross-platform download
├── CTD_Data_Extraction.ipynb         # Analysis — cartopy maps
├── CTD_Data_Extraction_mmap.ipynb    # Analysis — py_m_map maps
├── CTD_Data_Extraction_Batch.ipynb   # Batch CSV export
├── barkley_sound_1_navd88_2016.nc    # Multibeam bathymetry (Barkley Sound, 2016)
└── archive/                          # Earlier notebook versions (not for student use)
```

---

## Data layout

The notebooks expect cast files to be organised as follows (created automatically by the download notebook):

```
~/Documents/CTD Data/
└── YYYY-MM-DD/
    ├── aml_log_YYYY-MM-DD_HH-MM-SS.aml
    └── ...
```

---

## Notes

- The AML-6 records dissolved oxygen in **µmol/L**. The notebooks use this unit by default; set `DO_UNITS = 'mL/L'` in the configuration cell to convert to the legacy mL/L unit.
- TEOS-10 quantities (Absolute Salinity, Conservative Temperature, σ₀) are computed using the [`gsw`](https://teos-10.github.io/GSW-Python/) package.
- On **macOS**, the download notebook uses `/usr/bin/expect` to drive `sftp` interactively. This is required because macOS Local Network privacy restrictions block Python's `socket` module and `nc` from reaching the instrument's local-network IP (172.18.x.x).
- The **browser** you use to run JupyterLab must also have Local Network access enabled. On macOS, go to **System Settings → Privacy & Security → Local Network** and ensure your browser is toggled on. If the toggle is missing, open any notebook cell and run a command — macOS will prompt you to grant access.
- On **Windows**, the download notebook uses `paramiko` for SFTP and `netsh` for WiFi management.
