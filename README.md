# GUI-Topsis

A small Tkinter desktop app that ranks lamp products for restocking using the TOPSIS multi-criteria decision method. Built as a decision support system (SPPK) for a lamp shop; the UI is in Indonesian.

You enter each product's lighting output, lifetime, unit price, warranty and monthly demand. The app scores every product and tells you which one to prioritize for restocking.

## How it works

The app implements TOPSIS (Technique for Order of Preference by Similarity to Ideal Solution) in `hitung_topsis()` in `GUI SPPK.py`:

1. Vector-normalize each criterion column.
2. Multiply by the criterion weight.
3. Pick the ideal-best and ideal-worst value per criterion (lowest price is best, highest is best for the rest).
4. Compute each product's Euclidean distance to both.
5. Score = distance to worst / (distance to best + distance to worst). Higher is better.

Criteria and weights are hardcoded at the top of the script:

| Criterion | Input label in the UI | Type | Weight |
|---|---|---|---|
| Pencahayaan | Pencahayaan (Lumens) | benefit | 5 |
| Life Time | Life Time (Jam / hours) | benefit | 4 |
| Harga Satuan | Harga Satuan (Rp) | cost | 4 |
| Garansi | Garansi (Tahun / years) | benefit | 3 |
| Permintaan | Permintaan (Unit/Bulan) | benefit | 4 |

To change criteria or weights, edit the `kriteria`, `costbenefit` and `bobot` lists in `GUI SPPK.py`. The input form and result table are not generated from these lists, so adding or removing a criterion also means editing the form and table code.

## Requirements

- Python 3. The project's IDE config targets Python 3.12; other 3.x versions are untested.
- Tkinter. It ships with the python.org installers for Windows and macOS. On Debian/Ubuntu install it with `sudo apt install python3-tk`.
- scikit-learn. The script runs `from sklearn import tree` at startup, so the import must succeed even though the name is not used for any calculation (it is immediately shadowed by the product table widget).
- Windows is the intended platform: the script calls `root.iconbitmap('logo.ico')`, which Tk supports on Windows and which typically fails on Linux and macOS. This was not tested on those systems.

There is no `requirements.txt` or other dependency manifest in the repository.

## Installation

```bash
git clone https://github.com/Menjadianjay/GUI-Topsis.git
cd GUI-Topsis
pip install scikit-learn
```

## Run

Run from the repository directory, because the icon is loaded with the relative path `logo.ico`. The filename contains a space, so quote it:

```bash
python "GUI SPPK.py"
```

A window titled "Rekomendasi Stok Barang Toko Lampu" should open with an input form on the left and an empty product table on the right.

## Usage

1. Fill in all six fields on the left (name plus five numeric criteria) and click **Tambah**. The product appears in the table and the form clears. Numeric fields accept digits and a single decimal point only. Enter the price as whole rupiah.
2. Repeat for every product you want to compare. Enter at least two products whose values differ (see Known limitations).
3. Click **Hitung Rekomendasi**. A results window lists products by rank with their TOPSIS score. The top-ranked product is labeled "Re-Stok ditingkatkan" (increase restock); all others are labeled "Re-Stok Normal".
4. **Ulangi** clears the data and restarts; **Exit** closes the app.

Example, computed by running the script's formulas on made-up data (not a screenshot of the app):

| Product | Lumens | Hours | Price (Rp) | Warranty (yr) | Demand/month | Score | Rank |
|---|---|---|---|---|---|---|---|
| Lampu B | 1200 | 20000 | 45000 | 3 | 25 | 0.5014 | 1 |
| Lampu A | 800 | 15000 | 25000 | 2 | 40 | 0.4989 | 2 |
| Lampu C | 600 | 10000 | 15000 | 1 | 60 | 0.4986 | 3 |

## Known limitations

These come from reading the code; they have not all been reproduced in the running app.

- **One product, or identical products, will crash the calculation.** With a single product (or all values equal) both distances are 0 and the score computation divides by zero.
- **"Ulangi" (restart) looks unreliable.** It opens a second Tk window via `baru()`, but that window's **Tambah** button writes to the original window's widgets, which were destroyed.
- **No persistence or export.** Products live only in memory for the session.
- **Criteria and weights are fixed in code**; there is no UI to change them.
- **Decimal prices display incorrectly.** The price display strips every non-digit character, so `1.5` is shown as `Rp 15` while the calculation uses `1.5`.
- **No tests, CI, or screenshots** exist in the repository.

## Project structure

```
GUI SPPK.py   # the whole application: input form, TOPSIS calculation, results window
logo.ico      # window icon
.idea/        # PyCharm project settings
```

## Contributing

There is no contribution guide. Issues and pull requests can be opened on the GitHub repository; the maintainer has not published review or support policies.

## License

No license file is present in the repository, so no license is granted by default. The repository owner needs to choose and add one.
