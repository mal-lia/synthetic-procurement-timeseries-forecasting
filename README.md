# Synthetic Procurement Timeseries Forecasting
---
# Perbandingan Linear Regression dan SARIMA pada Dua Dataset Sintetis Bertema Procurement

Proyek ini membandingkan dua metode peramalan deret waktu, **Linear Regression** dan **SARIMA**, menggunakan dua dataset sintetis bulanan yang dibuat secara independen. Konteks procurement digunakan sebagai inspirasi skenario sintetis, bukan sebagai sumber data aktual.

> **Pernyataan data dan privasi:** seluruh dataset dan hasil yang disimpan di repository ini bersifat sintetis. Dataset V2 dibangkitkan dari parameter hipotetis yang ditulis di notebook. Proyek ini tidak memuat, menyalin, mengolah, mengkalibrasi, atau merekonstruksi transaksi, metadata, skema database, profil agregat, maupun data internal organisasi/perusahaan mana pun. Dataset ini bukan representasi faktual dari perusahaan atau tempat magang tertentu.

## Tujuan

1. Membandingkan performa Linear Regression dan SARIMA pada dua pola deret waktu sintetis.
2. Mengamati perbedaan tren, variasi, pola musiman, dan autokorelasi.
3. Mengevaluasi hasil peramalan menggunakan MAE, RMSE, dan MAPE pada data uji yang sama.
4. Menghasilkan contoh forecast 12 bulan sebagai demonstrasi.

## Dataset

| Dataset | Isi | Cara pembentukan |
|---|---|---|
| **V1 — Synthetic Independent** | 180 observasi bulanan, Januari 2009–Desember 2023 | Baseline sintetis independen yang dipertahankan sebagai pembanding awal. |
| **V2 — Procurement-Inspired Synthetic** | 180 observasi bulanan, Januari 2009–Desember 2023 | Dibangkitkan ulang secara independen dengan seed tetap dan asumsi ilustratif berupa tren, indeks musiman hipotetis, gangguan AR(1), noise, serta perubahan level skenario. |

Kedua dataset menggunakan satu variabel hitungan sintetis (`SyntheticEventCount`). Nilainya **bukan jumlah transaksi aktual**. Pola musiman dan perubahan level pada V2 hanyalah keputusan desain untuk membentuk skenario demonstrasi; bukan klaim mengenai pola procurement yang benar-benar terjadi.

## Metode

- **Prapemrosesan:** parsing periode bulanan dan pemeriksaan statistik deskriptif.
- **Stasioneritas:** Augmented Dickey-Fuller (ADF) dan KPSS pada level serta first difference.
- **Pembagian data:** 80% observasi awal untuk training dan 20% observasi akhir untuk testing, secara kronologis tanpa shuffle.
- **Linear Regression:** tren waktu linier dan dummy bulan untuk menangkap variasi musiman bulanan.
- **SARIMA:** pemilihan di antara kandidat order menggunakan AIC pada data training saja.
- **Evaluasi:** MAE, RMSE, dan MAPE pada periode testing yang sama.
- **Forecast tambahan:** 12 bulan setelah periode dataset, dengan model yang di-fit ulang menggunakan seluruh observasi.

## Ringkasan hasil eksperimen

Hasil berikut berasal dari konfigurasi notebook dan dataset sintetis yang disertakan. Angka dapat direproduksi dengan menjalankan notebook.

| Dataset | Model | MAE | RMSE | MAPE |
|---|---|---:|---:|---:|
| V1 — Independent | Linear Regression | **20.758** | **25.137** | **5.162%** |
| V1 — Independent | SARIMA | 26.096 | 31.039 | 6.054% |
| V2 — Procurement-inspired | Linear Regression | 11.370 | 14.486 | 2.751% |
| V2 — Procurement-inspired | SARIMA | **11.334** | **14.310** | **2.741%** |

Pada V1, Linear Regression menghasilkan error lebih rendah. Pada V2, SARIMA unggul tipis. Hasil ini hanya berlaku untuk dua seri sintetis ini; tidak dapat digeneralisasi menjadi klaim bahwa salah satu metode selalu lebih baik.

## Struktur repository

```text
synthetic-procurement-timeseries-forecasting/
├── README.md
├── .gitignore
├── requirements.txt
├── notebooks/
│   └── 01_perbandingan_linear_regression_sarima.ipynb
├── data/
│   ├── synthetic_v1_independent.csv
│   └── synthetic_v2_procurement_inspired.csv
└── results/
    ├── descriptive_summary_v1_v2.csv
    ├── model_comparison_lr_sarima_v1_v2.csv
    ├── stationarity_adf_kpss_v1_v2.csv
    └── forecast_12_months_v1_v2.csv
```

## Cara menjalankan

### Opsi A — JupyterLite

1. Buka JupyterLite yang digunakan untuk perkuliahan.
2. Upload file `notebooks/01_perbandingan_linear_regression_sarima.ipynb`.
3. Jalankan sel dari atas ke bawah (*Run All Cells*).
4. Notebook membangkitkan kedua dataset dan mengekspor CSV serta tabel hasil ke direktori kerja JupyterLite.
5. Notebook mencoba memasang `statsmodels` melalui `piplite` bila library belum tersedia. Ketersediaan instalasi bergantung pada konfigurasi deployment JupyterLite.

### Opsi B — Python lokal

Pastikan Python dan Jupyter Notebook/JupyterLab tersedia, lalu jalankan:

```bash
python -m pip install -r requirements.txt
jupyter notebook
```

Buka notebook di folder `notebooks/`, kemudian jalankan seluruh sel.

## Batasan

- Dataset merupakan data sintetis untuk demonstrasi metode, bukan data empiris.
- Parameter V2 dirancang secara hipotetis dan tidak ditaksir dari data organisasi/perusahaan.
- Linear Regression yang digunakan adalah baseline dengan tren linier dan dummy bulan; bentuk hubungan yang lebih kompleks tidak dimodelkan.
- Kandidat SARIMA dibatasi pada daftar yang ditentukan di notebook. Hasil pemilihan model dapat berubah jika rentang kandidat, periode data, atau parameter diubah.
- Forecast 12 bulan adalah ilustrasi pada seri sintetis, bukan proyeksi aktivitas organisasi nyata.

## Reproducibility

Seed V2 ditetapkan pada `20261110`. Dengan versi library dan konfigurasi yang sama, data V2 dapat dibangkitkan kembali secara konsisten. Notebook menyimpan dataset, ringkasan, hasil uji stasioneritas, hasil evaluasi model, dan forecast sebagai CSV.

## Lisensi

Belum ada lisensi open-source yang diberikan. Kode dan materi dalam repository ini dapat dilihat, tetapi hak penggunaan ulang tetap mengikuti ketentuan hak cipta pemilik repository sampai lisensi ditambahkan.
