# Prediksi State of Charge (SOC) Baterai Kendaraan Listrik

Proyek Data Mining -- Semester 6

---

## Deskripsi Proyek

Proyek ini bertujuan untuk memprediksi **State of Charge (SOC)** baterai Lithium-Ion pada kendaraan listrik menggunakan pendekatan data mining. SOC merupakan parameter kritis dalam Battery Management System (BMS) yang merepresentasikan persentase energi tersisa pada baterai, namun tidak dapat diukur secara langsung oleh sensor.

Kami menggunakan dataset eksperimen baterai **LG 18650HG2** dari McMaster University, di mana profil daya dihitung untuk satu sel baterai pada kendaraan listrik kompak. Data mencakup pengujian pada 6 kondisi suhu (-20°C hingga 40°C) dengan berbagai drive cycle standar otomotif (UDDS, HWFET, LA92, US06) serta skenario mixed driving.

Pendekatan yang digunakan meliputi eksplorasi data, simulasi penanganan missing value dan class imbalance, serta eksperimen feature selection dan dimensionality reduction terhadap model klasifikasi SOC.

---

## Dataset

Dataset berasal dari eksperimen yang dilakukan oleh Dr. Phillip Kollmeyer di McMaster University, Kanada.

**[Download Dataset](https://data.mendeley.com/datasets/b5mj79w5w9/2)**

| Aspek | Detail |
|---|---|
| Sel Baterai | LG HG2 18650 (3Ah, 3.7V nominal, Li-ion) |
| Aplikasi | Kendaraan listrik kompak |
| Kondisi Suhu | -20°C, -10°C, 0°C, 10°C, 25°C, 40°C |
| Drive Cycle | UDDS, HWFET, LA92, US06, Mixed1-8 |
| Variabel Terukur | Voltage, Current, Temperature, Capacity, WhAccu |
| Format | CSV |

---

## Struktur Proyek

```
Battery/
  Data/                                    # Folder dataset (6 subfolder per suhu)
  images/
    week2/                                 # Visualisasi Data Understanding
    week3/                                 # Visualisasi Data Preparation
    week4&5/                               # Visualisasi Feature Selection
  Business_Understanding.ipynb             # Definisi masalah dan tujuan
  Data_Understanding.ipynb                 # Eksplorasi dan analisis data
  Data_Preparation.ipynb                   # Missing value & class imbalance
  Feature_Selection.ipynb                  # Feature selection & dimensionality reduction
```

---

## Tahapan Proyek

### Business Understanding

Menentukan masalah, kriteria keberhasilan, dan faktor-faktor yang mempengaruhi prediksi SOC pada kendaraan listrik.

- **Problem:** Estimasi SOC baterai kendaraan listrik dari data operasional (Voltage, Current, Temperature)
- **Target:** MAE <= 2%, R2 >= 0.98
- **Faktor utama:** Tegangan terminal, arus beban, suhu baterai, dan suhu lingkungan

### Data Understanding

Eksplorasi awal terhadap dataset, termasuk identifikasi struktur data, statistik deskriptif, dan visualisasi.

- Identifikasi 14 kolom asli dalam dataset
- Perhitungan SOC menggunakan metode Coulomb Counting dari kolom Capacity
- Analisis korelasi antar variabel
- Visualisasi distribusi, time series, scatter plot, heatmap korelasi, dan boxplot
- Kesimpulan: dataset bersih, tidak ada missing value

### Data Preparation

Demonstrasi teknik penanganan missing value dan class imbalance melalui simulasi.

**Missing Value Imputation:**

| Metode | Pendekatan | Kesesuaian untuk Time Series |
|---|---|---|
| Mean Imputation | Isi dengan rata-rata kolom | Rendah |
| Median Imputation | Isi dengan median kolom | Rendah |
| KNN Imputation (k=5) | Isi berdasarkan tetangga terdekat | Sedang |
| Interpolasi Linear | Isi berdasarkan data sebelum dan sesudah | Tinggi |

Hasil: Interpolasi Linear memberikan MAE terkecil, sesuai dengan karakteristik data time series baterai.

**Class Imbalance Handling:**

| Metode | Cara Kerja | Risiko Overfitting |
|---|---|---|
| Random Oversampling | Duplikasi data dari kelas minoritas | Tinggi |
| Synthetic Generation | Interpolasi antar data point | Rendah |

### Feature Selection dan Dimensionality Reduction

Eksperimen perbandingan 6 skenario terhadap performa model klasifikasi SOC (5 kelas).

**Fitur yang digunakan (13 fitur):**

Fitur asli: Voltage, Current, Temperature, Ambient_Temp

Fitur hasil rekayasa: Power, dV, dI, dT, V_roll_mean, I_roll_mean, V_roll_std, I_roll_std, V_I_ratio

> Catatan: Capacity dan WhAccu tidak dimasukkan sebagai fitur untuk menghindari data leakage, karena SOC dihitung langsung dari Capacity.

**Skenario Eksperimen:**

| Skenario | Metode | Deskripsi |
|---|---|---|
| S1 | Baseline | Seluruh 13 fitur tanpa seleksi |
| S2 | Filter Method | ANOVA F-test + Mutual Information, top 8 fitur |
| S3 | Wrapper Method | Recursive Feature Elimination (RFE), 8 fitur |
| S4 | Embedded Method | RF + XGBoost Feature Importance, top 8 fitur |
| S5 | PCA | Principal Component Analysis, 95% explained variance |
| S6 | LDA | Linear Discriminant Analysis, 4 komponen |

Model yang digunakan: **Random Forest** dan **XGBoost**

Evaluasi: Accuracy, Precision, Recall, F1-Score, Confusion Matrix

---

## Teknologi

- Python 3.13
- Pandas, NumPy
- Matplotlib, Seaborn
- Scikit-learn
- XGBoost
- Jupyter Notebook

---

## Cara Menjalankan

1. Clone repositori ini
2. Download dataset dan letakkan di folder `Data/`
3. Install dependencies:
   ```
   pip install pandas numpy matplotlib seaborn scikit-learn xgboost jupytext
   ```
4. Buka notebook secara berurutan:
   - `Business_Understanding.ipynb`
   - `Data_Understanding.ipynb`
   - `Data_Preparation.ipynb`
   - `Feature_Selection.ipynb`

---

## Referensi

- Kollmeyer, P., Skells, M. (2020). LG 18650HG2 Li-ion Battery Data. McMaster University, Hamilton, Ontario, Canada.
- CRISP-DM Methodology -- Cross Industry Standard Process for Data Mining
