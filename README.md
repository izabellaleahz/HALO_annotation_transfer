# HALO Annotation Transfer to Visium Spots

This repository provides a utility function and example notebook to **align `.annotation` files exported from HALO AI** with **spatial coordinates in a Visium `AnnData` object**. This enables integration of histological annotations with spatial transcriptomics data.

## 🧠 What This Does

The core function `transfer_halo_annotations_to_adata`:

* Loads and parses `.annotation` files using `pyhaloxml`
* Applies optional flipping, rotation, and shifting to align annotations with Visium coordinates
* Projects HALO polygons onto buffered Visium spots
* Assigns a **dominant label** and **label proportions** to each Visium spot
* Optionally saves the updated `AnnData` and visualizes the result

---

## 📁 Repository Structure

```
.
├── data/
│   ├── C1-HT224P1-S1Fc2U1.annotations    # Example HALO annotation file
│   └── HT224P1.h5ad                       # Example Visium AnnData object
├── Halo_anntation_transfer_functions.ipynb # Jupyter notebook with transfer function
├── environment.yaml                        # Conda environment for reproducibility
└── README.md
```

---

## 🚀 Quickstart

### 1. Create Environment

#### Option A: Manual setup

```bash
conda create -n haloxml_env python=3.10
conda activate haloxml_env
conda install -c conda-forge shapely geopandas matplotlib
pip install pyhaloxml scanpy
```

#### Option B: With `environment.yaml`

```bash
conda env create -f environment.yaml
conda activate haloxml_env
```

### 2. Run the Notebook

Open the notebook:

```bash
jupyter lab Halo_anntation_transfer_functions.ipynb
```

The notebook contains:
- Data loading and preprocessing
- The `transfer_halo_annotations_to_adata` function
- Example usage with the provided data files

### 3. Example Usage

```python

# Example usage with the provided data
updated_adata = transfer_halo_annotations_to_adata(
    annotation_path="data/C1-HT224P1-S1Fc2U1.annotations",
    adata=adata,
    flip_x=True,
    flip_y=False,
    rotation=90,
    x_shift=300,
    y_shift=-500,
    align_to_visium=False,
    save_path=None,
    spot_diameter=55,
    plot=True
)
```

---

## 🧩 Function Reference

```python
transfer_halo_annotations_to_adata(
    annotation_path,
    adata,
    flip_x=False,
    flip_y=False,
    rotation=0,
    x_shift=0,
    y_shift=0,
    align_to_visium=True,
    save_path=None,
    spot_diameter=55,
    plot=True,
)
```

**Parameters:**

* `annotation_path` – path to the `.annotations` file
* `adata` – your `AnnData` object with spatial coordinates in `obs["X"]` and `obs["Y"]`
* `flip_x`, `flip_y`, `rotation`, `x_shift`, `y_shift` – optional transformations
* `align_to_visium` – if `True`, aligns coordinates to Visium layout automatically
* `spot_diameter` – assumed spot diameter in same units as annotations
* `plot` – whether to generate diagnostic overlay plots
* `save_path` – optional path to save updated `.h5ad` file

**Outputs:**

* Adds `.obs["halo_label"]`, `.obs["halo_label_frac"]`, and `.obs["halo_frac_<label>"]` to your `AnnData`

---

## 📊 Data Requirements

Your `AnnData` object should have:
- Spatial coordinates in `obs["X"]` and `obs["Y"]` (or `obsm["X_spatial"]`)
- Tissue spots filtered (e.g., `adata[adata.obs["in_tissue"] == 1]`)

---

## 🧪 Requirements

* Python 3.10+
* `geopandas`, `shapely`, `matplotlib`, `scanpy`, `pyhaloxml`

---

## 📁 Included Data

The repository includes example data files:
- `data/C1-HT224P1-S1Fc2U1.annotations` - HALO annotation file
- `data/HT224P1.h5ad` - Visium spatial transcriptomics data
