# Perbandingan Linear Regression dan SARIMA pada Dua Dataset Sintetis Bertema Procurement

Proyek ini membandingkan dua metode peramalan deret waktu, **Linear Regression** dan **SARIMA**, terhadap tolok ukur dasar **Seasonal Naive** (\(s = 12\)) menggunakan dua dataset sintetis bulanan yang dibuat secara independen. Konteks *procurement* digunakan sebagai inspirasi skenario sintetis, bukan sebagai sumber data aktual.

> **Pernyataan data dan privasi:** seluruh dataset dan hasil yang disimpan di repository ini bersifat sintetis. Dataset V2 dibangkitkan dari parameter hipotetis yang ditulis di notebook. Proyek ini tidak memuat, menyalin, mengolah, mengkalibrasi, atau merekonstruksi transaksi, metadata, skema database, profil agregat, maupun data internal organisasi/perusahaan mana pun. Dataset ini bukan representasi faktual dari perusahaan atau tempat magang tertentu.

## Tujuan

1. Membandingkan performa **Linear Regression** dan **SARIMA** terhadap tolok ukur **Seasonal Naive** pada dua pola deret waktu sintetis.
2. Mengamati perbedaan tren, variasi, pola musiman, autokorelasi, serta diagnosis residual *white noise*.
3. Mengevaluasi hasil peramalan menggunakan **MAE**, **RMSE**, **MASE**, dan **MAPE** pada periode uji yang sama.
4. Menghasilkan contoh *forecast* 12 bulan ke depan dan visualisasi resolusi tinggi (300 DPI) untuk poster ilmiah.

## Dataset

| Dataset | Isi | Cara pembentukan |
|---|---|---|
| **V1 — Synthetic Independent** | 180 observasi bulanan, Januari 2009–Desember 2023 | *Baseline* sintetis independen yang dipertahankan sebagai pembanding awal. |
| **V2 — Procurement-Inspired Synthetic** | 180 observasi bulanan, Januari 2009–Desember 2023 | Dibangkitkan ulang secara independen dengan *seed* tetap dan asumsi ilustratif berupa tren, indeks musiman hipotetis, gangguan AR(1), *noise*, serta perubahan level skenario. |

Kedua dataset menggunakan satu variabel hitungan sintetis (`SyntheticEventCount`). Nilainya **bukan jumlah transaksi aktual**. Pola musiman dan perubahan level pada V2 hanyalah keputusan desain untuk membentuk skenario demonstrasi; bukan klaim mengenai pola *procurement* yang benar-benar terjadi.

## Metode

- **Prapemrosesan:** *parsing* periode bulanan dan pemeriksaan statistik deskriptif.
- **Stasioneritas:** uji Augmented Dickey-Fuller (ADF) dan KPSS pada level serta *first difference*.
- **Pembagian data:** 80% observasi awal (144 bulan, Januari 2009–Desember 2020) untuk *training* dan 20% observasi akhir (36 bulan, Januari 2021–Desember 2023) untuk *testing*, secara kronologis tanpa *shuffle*.
- **Tolok ukur (*Baseline*):** **Seasonal Naive** (\(s = 12\)) dengan mengulang pola 12 bulan terakhir dari data *training*.
- **Linear Regression:** model regresi linier dengan fitur indeks waktu linier dan variabel *dummy* 12 bulan untuk menangkap tren serta variasi musiman.
- **SARIMA:** pemilihan kandidat orde menggunakan AIC pada data *training* saja, diikuti diagnosis residual *white noise* menggunakan **Uji Ljung-Box** pada lag 12.
- **Evaluasi:** **MAE**, **RMSE**, **MASE** (dengan skala kesalahan naif musiman pada data *training*), dan **MAPE** pada periode *testing* yang sama.
- **Forecast tambahan:** 12 bulan setelah periode dataset, dengan model yang di-*fit* ulang menggunakan seluruh observasi.

## Ringkasan hasil eksperimen

Hasil berikut berasal dari konfigurasi notebook dan dataset sintetis yang disertakan. Angka dapat direproduksi sepenuhnya dengan menjalankan notebook.

| Dataset | Model | MAE | RMSE | MASE | MAPE (%) |
|---|---|---:|---:|---:|---:|
| V1 — Independent | **Linear Regression** | **20.758** | **25.137** | **0.818** | **5.162%** |
| V1 — Independent | Seasonal Naive (*Baseline*) | 23.778 | 28.231 | 0.937 | 5.652% |
| V1 — Independent | SARIMA(0,1,1)(0,1,1)₁₂ | 26.096 | 31.039 | 1.028 | 6.054% |
| V2 — Procurement-inspired | **SARIMA(1,1,1)(0,1,1)₁₂** | **11.334** | **14.310** | **0.607** | **2.741%** |
| V2 — Procurement-inspired | Linear Regression | 11.370 | 14.486 | 0.609 | 2.751% |
| V2 — Procurement-inspired | Seasonal Naive (*Baseline*) | 21.778 | 26.281 | 1.167 | 5.270% |

Pada V1, **Linear Regression** menghasilkan kesalahan terendah (MASE = 0,818), sedangkan SARIMA belum mengungguli *Seasonal Naive*. Pada V2, **SARIMA** unggul sangat tipis (MASE = 0,607) atas **Linear Regression** (MASE = 0,609), dan keduanya memangkas kesalahan hampir separuh dibandingkan *Seasonal Naive*. Uji *Ljung-Box* pada lag 12 menunjukkan residual SARIMA belum sepenuhnya mencapai *white noise* (\(p = 0,0038\) pada V1 dan \(p < 0,0001\) pada V2). Hasil ini hanya berlaku untuk dua seri sintetis ini dan tidak dapat digeneralisasi untuk seluruh kasus peramalan.

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
    ├── adw_model_comparison_complete_v1_v2.csv
    ├── adw_sarima_ljungbox_v1_v2.csv
    ├── stationarity_adf_kpss_v1_v2.csv
    ├── forecast_12_months_v1_v2.csv
    ├── poster_fig1_data_aktual_300dpi.png
    ├── poster_fig2_prediksi_v1_300dpi.png
    └── poster_fig3_prediksi_v2_300dpi.png
```

## Cara menjalankan

### Opsi A — JupyterLite

1. Buka JupyterLite yang digunakan untuk perkuliahan.
2. Upload file `notebooks/01_perbandingan_linear_regression_sarima.ipynb`.
3. Jalankan sel dari atas ke bawah (*Run All Cells*).
4. Notebook membangkitkan kedua dataset dan mengekspor CSV serta grafik 300 DPI ke direktori kerja JupyterLite.
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
- Evaluasi menggunakan satu pembagian kronologis tunggal (80:20) tanpa validasi bergulir (*rolling-origin*).
- Linear Regression yang digunakan adalah model berbasis tren linier dan *dummy* bulan; hubungan nonlinier atau peubah eksogen belum dimodelkan.
- Kandidat SARIMA dibatasi pada daftar yang ditentukan di notebook sehingga residual masih menyisakan autokorelasi pada lag 12.
- *Forecast* 12 bulan adalah ilustrasi pada seri sintetis, bukan proyeksi aktivitas organisasi nyata.

## Reproducibility

*Seed* V2 ditetapkan pada `20261110`. Dengan versi *library* dan konfigurasi yang sama, data V2 dapat dibangkitkan kembali secara konsisten. Notebook menyimpan dataset, ringkasan deskriptif, hasil uji stasioneritas, evaluasi model lengkap (dengan MASE dan *Seasonal Naive*), uji Ljung-Box, *forecast* 12 bulan, serta grafik resolusi tinggi 300 DPI ke folder `results/`.

## Lisensi

Belum ada lisensi open-source yang diberikan. Kode dan materi dalam repository ini dapat dilihat, tetapi hak penggunaan ulang tetap mengikuti ketentuan hak cipta pemilik repository sampai lisensi ditambahkan.
