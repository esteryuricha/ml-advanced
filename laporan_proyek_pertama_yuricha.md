# Laporan Proyek: Prediksi Risiko Seseorang Mengidap Diabetes

## Domain Proyek

Diabetes merupakan salah satu penyakit tidak menular yang sampai di tahun 2024 menjadi penyakit mematikan nomor 6 di dunia dan sebagai penyakit penyebab kematian tertinggi ketiga di Indonesia. Berdasarkan data dari WHO, prevalensi diabetes terus meningkat dari tahun ke tahun diikuti dengan tren makanan minuman manis yang semakin banyak bermunculan dan mudah diakses oleh semua kalangan.

Pendeteksian dini terhadap risiko diabetes memungkinkan intervensi lebih awal melalui perubahan gaya hidup, pengelolaan pola makan, dan pengobatan yang tepat. Oleh karena itu, predictive analytics dapat menjadi solusi penting untuk membantu memprediksi risiko seseorang mengidap diabetes.

## Business Understanding

### Problem Statements

- Apa saja faktor utama yang memengaruhi kemungkinan seseorang mengidap diabetes?
- Bagaimana membangun model prediktif yang akurat untuk mendeteksi risiko diabetes berdasarkan data kesehatan individu?

### Goals

- Mengidentifikasi variabel-variabel penting yang berkontribusi terhadap risiko diabetes.
- Membangun model prediktif berbasis machine learning untuk memberikan indikasi dini risiko diabetes.
- Mendukung kampanye pencegahan dan edukasi kesehatan masyarakat berbasis data.

### Solution Statement

Solusi yang akan diterapkan adalah mengembangkan model prediksi menggunakan algoritma machine learning, seperti Logistic Regression, Random Forest, dan Gradient Boosting. Model akan dilatih dengan data historis yang mencakup faktor risiko diabetes, seperti kadar glukosa, tekanan darah, indeks massa tubuh (BMI), dan riwayat keluarga.

## Data Understanding

**Informasi Dataset**
Informasi | Keterangan
----------- |------------------
Link | https://www.kaggle.com/api/v1/datasets/download/akshaydattatraykhare/diabetes-dataset/
Lisensi | CC0: Public Domain
Size | 54.1 kB
Data | 768 rows

**Exploratory Data Analysis (EDA) - Deskripsi Variabel**
Berdasarkan informasi dari Kaggle, variabel-variabel pada Diabetes Dataset adalah sebagai berikut:

- _Pregnancies_: Jumlah kehamilan.
- _Glucose_: Konsentrasi glukosa plasma (mg/dL).
- _Blood Pressure_: Tekanan darah (mm Hg).
- _Skin Thickness_: Ketebalan kulit (mm).
- _Insulin_: Kadar insulin serum (µU/ml).
- _BMI_: Indeks massa tubuh (kg/m²).
- _Diabetes Pedigree Function_: Riwayat keluarga terkait diabetes.
- _Age_: Usia individu (tahun).
- _Outcome_: Status diabetes (1 = diabetes, 0 = tidak diabetes).

**Exploratory Data Analysis (EDA) - Univariate Analysis**
pada dataset yang ada, fitur yang bertipe numerik dengan tipe int64, yaitu Pregnancies, Glucose, BloodPressure, SkinThickness, Insulin, Age dan Outcome.Sedangkan fitur yang bertipe numerik dengan tipe float64, yaitu BMI dan DiabetesPedigreeFunction.
Berikut info diabetes dataset sebagai:

![univariat numerical fitur](https://github.com/esteryuricha/ml-advanced/blob/4b80f0a58b189f2552015f097989b12a2a5319ad/Screenshot%202024-11-19%20161410.png)

**Exploratory Data Analysis (EDA) - Multivariate Analysis**

## Data Preparation

1. **Pembersihan Data**

   - Menghapus data yang tidak valid atau noise.
   - Mengatasi missing value dengan imputasi, seperti mean untuk data numerik.

2. **Split Data**

   - Membagi dataset menjadi 80% data pelatihan dan 20% data pengujian.

3. **Feature Scaling**

   - Normalisasi variabel numerik seperti Glucose, Blood Pressure, dan BMI untuk memastikan skala seragam.

4. **Balancing Data**
   - Jika dataset tidak seimbang (diabetes vs tidak diabetes), metode oversampling seperti SMOTE akan diterapkan.

## Modeling

Model akan dikembangkan menggunakan beberapa algoritma berikut:

- **Logistic Regression**: Model baseline yang sederhana dan interpretatif.
- **Random Forest**: Untuk menangkap pola non-linear antar fitur.
- **Gradient Boosting**: Untuk meningkatkan akurasi melalui pendekatan boosting.

## Evaluation

Model akan dievaluasi menggunakan metrik berikut:

- **Accuracy**: Persentase prediksi yang benar.
- **Precision dan Recall**: Untuk menilai deteksi risiko diabetes pada kelas minoritas.
- **F1-Score**: Kombinasi antara precision dan recall.
- **ROC-AUC**: Menilai kemampuan model dalam membedakan antara individu dengan dan tanpa risiko diabetes.

## Kesimpulan

Proyek ini bertujuan untuk membantu mendeteksi risiko diabetes pada individu menggunakan pendekatan machine learning. Model yang dikembangkan diharapkan dapat memberikan prediksi akurat untuk mendukung intervensi dini dan meningkatkan kesadaran kesehatan masyarakat.

**Referensi**

- Dataset: Pima Indians Diabetes Dataset
- Artikel: [Using Machine Learning for Diabetes Prediction](https://www.example.com)
