# End-to-End-Machine-Learning-Pipeline-for-Insurance-Risk-Scoring

## Laporan Proyek: Prediksi Klasifikasi Risiko Nasabah (Data Prudential)

## Summary
Proyek ini bertujuan untuk mengotomatisasi penilaian risiko (*underwriting*) nasabah asuransi jiwa menggunakan dataset Prudential. Fokus utamanya adalah mengklasifikasikan calon nasabah ke dalam 8 tingkatan risiko (**Response 1-8**) dengan menggunakan algoritma Machine Learning yang robust dan evaluasi metrik yang sesuai dengan kaidah aktuaria.

---

## Tahapan 

### 1. Analisis Data Eksploratif (EDA)
Tahap ini dilakukan untuk memahami karakteristik data dan memvisualisasikan faktor risiko utama:
* **Visualisasi Distribusi Fisik**: Penggunaan *Boxplot* pada fitur kritis seperti `BMI`, `Weight`, dan `Height` untuk melihat sebaran nilai di setiap kelas risiko.
* **Matriks Korelasi**: Identifikasi hubungan antar variabel numerik guna mendeteksi multikolinearitas (terutama pada riwayat medis dan variabel fisik).

### 2. Rekayasa Fitur (Feature Engineering) Dasar
Optimasi data mentah menjadi informasi yang lebih bernilai bagi model:
* **Penanganan Data Hilang**: Imputasi menggunakan nilai *median* (ekuivalen dengan `na.roughfix` di R) untuk menjaga stabilitas data terhadap *outlier*.
* **Label Encoding**: Konversi variabel kategorikal menjadi numerik agar dapat diproses oleh algoritma.
* **Agregasi Medical Keywords**: Pembuatan fitur baru `medkeywords` (*Comorbidity Index*) yang menjumlahkan total riwayat penyakit nasabah sebagai indikator keparahan risiko medis.

### 3. Pemodelan XGBoost & Evaluasi QWK (Baseline)
Pembangunan model awal (*baseline*) menggunakan algoritma *Extreme Gradient Boosting*:
* **XGBoost Multi-class**: Konfigurasi model untuk menangani 8 kelas target (indeks 0-7).
* **Quadratic Weighted Kappa (QWK)**: Penggunaan QWK sebagai metrik utama karena sifat target yang ordinal. QWK memberikan penalti lebih besar pada kesalahan prediksi yang "jaraknya" jauh.

### 4. Identifikasi Fitur Kategori & Seleksi Fitur Utama
Optimasi dimensi data untuk meningkatkan efisiensi:
* **Importance Scoring**: Mengidentifikasi 30 fitur paling berpengaruh (*Top Features*) dari model baseline.
* **Filter Kardinalitas**: Mengeluarkan fitur kategori dengan tingkat variasi terlalu tinggi (seperti `Medical_History_2`) dari proses encoding untuk menghindari *overfitting*.

### 5. One-Hot Encoding & Pelatihan Ulang (Retraining)
Eksperimen peningkatan akurasi model:
* **Selective One-Hot Encoding (OHE)**: Mengubah fitur kategori terpilih menjadi variabel *binary* (0/1) untuk menghilangkan bias urutan pada data nominal.
* **Experimental Retraining**: Melatih ulang model dengan fitur hasil OHE untuk membandingkan peningkatan performa secara signifikan.

### 6. Validasi Silang (K-Fold Cross Validation)
Memastikan stabilitas dan generalisasi model menggunakan **5-Fold Cross Validation**:
* **Accuracy**: Keandalan tebakan secara total.
* **Balanced Accuracy**: Akurasi rata-rata per kelas (mendeteksi bias pada kelas mayoritas).
* **QWK**: Konsistensi prediksi terhadap urutan risiko asli.

---

## Hasil Akhir (Summary Results)

| Metrik | Hasil Rata-rata | Kesimpulan Aktuarial |
| :--- | :--- | :--- |
| **Accuracy** | **0.5689** | Model memiliki kemampuan prediksi moderat di atas tebakan acak. |
| **Balanced Accuracy** | **0.4918** | Terdapat indikasi *class imbalance*; model perlu optimasi pada kelas minoritas. |
| **QWK** | **0.5146** | Model sudah mampu menangkap tren urutan risiko secara konsisten. |

---

## Kesimpulan & Rekomendasi
* **Variabel Penentu**: `BMI`, `Medical_History_15`, dan `Age` adalah faktor risiko yang paling menentukan dalam model ini.
* **Kelayakan**: Model sangat layak digunakan sebagai **Alat Bantu Underwriting (Decision Support)** untuk mempercepat verifikasi nasabah di kelas risiko mayoritas.
* **Pengembangan Lanjut**: Disarankan melakukan *Hyperparameter Tuning* (Optuna/GridSearch) dan teknik penanganan *imbalance* (seperti SMOTE atau penyesuaian bobot kelas) untuk meningkatkan *Balanced Accuracy*.

---
*Laporan ini dihasilkan untuk kebutuhan analisis teknis aktuaria - Maret 2025*
