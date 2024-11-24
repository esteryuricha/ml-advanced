# Laporan Proyek: Prediksi Risiko Seseorang Mengidap Diabetes - Yuricha

Proyek ini membangun model prediktif untuk memprediksi risiko seseorang mengidap diabetes dari riwayat kesehatan yang ada dan menentukan faktor-faktor utama yang memengaruhi kemungkinan seseorang mengidap diabetes.

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

### Solution Statement

Solusi yang akan diterapkan adalah mengembangkan model prediksi menggunakan algoritma machine learning, seperti K-Nearest Neighbor, Random Forest, dan Adaptive Boosting. Model akan dilatih dengan data historis yang mencakup faktor risiko diabetes, seperti kadar glukosa, tekanan darah, indeks massa tubuh (BMI), dan riwayat keluarga.

## Data Understanding

**Informasi Dataset**
Informasi | Keterangan
----------- |------------------
Link | https://www.kaggle.com/api/v1/datasets/download/akshaydattatraykhare/diabetes-dataset/
Lisensi | CC0: Public Domain
Size | 54.1 kB
Data | 768 rows & 9 columns

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

**Kualitas Dataset**
Berdasarkan hasil `data.info()` dan `data.isnull().sum()` bahwa semua kolom terisi dan tidak ada missing value.

**Exploratory Data Analysis (EDA) - Univariate Analysis**
pada dataset yang ada, fitur yang bertipe numerik dengan tipe int64, yaitu _Pregnancies_, _Glucose_, _BloodPressure_, _SkinThickness_, _Insulin_, _Age_ dan _Outcome_.
Sedangkan fitur yang bertipe numerik dengan tipe float64, yaitu _BMI_ dan _DiabetesPedigreeFunction_.
Berikut grafik Univariate Analysis dari dataset diabetes sebagai berikut:

![univariat numerical fitur](https://github.com/esteryuricha/ml-advanced/blob/main/images/histogram%20univariate.png?raw=true)

Beberapa hal yang dapat dianalisis dari hasil histogram yang ada, yaitu:

- **Pregnancies**: Distribusi skewed ke kanan. Sebagian besar individu memiliki jumlah kehamilan rendah (0-3).
- **Glucose**: Distribusi mendekati normal dengan puncak sekitar 100-150.
- **BloodPressure**: Distribusi normal, puncak di 60-80.
- **SkinThickness**: Skewed ke kanan, dengan beberapa nilai nol yang mencurigakan (missing value).
- **Insulin**: Skewed ke kanan. Banyak nilai mendekati nol yang kemungkinan juga missing value.
- **BMI**: Distribusi simetris dengan puncak di sekitar 30.
- **DiabetesPedigreeFunction**: Skewed ke kanan, menunjukkan kontribusi genetik terhadap diabetes.
- **Age**: Skewed ke kanan, didominasi individu berusia 20-50 tahun.
- **Outcome**: Dataset imbalanced, dengan lebih banyak individu tanpa diabetes (0).

**Exploratory Data Analysis (EDA) - Multivariate Analysis**
Multivariate EDA menunjukkan hubungan antara dua atau lebih variabel pada data. Berikut adalah relasi pasangan dalam dataset:

![multivariate analysis](https://github.com/esteryuricha/ml-advanced/blob/main/images/multivariate.png?raw=true)

Dari hasil analisis multivariate dapat terlihat bahwa hubungan :

- **Glucose** dengan **Outcome** memperlihatkan bahwa semakin tinggi kadar glukosa maka kemungkinan outcome menghasilkan 1 lebih tinggi.
- **BMI** dengan **Outcome** memperlihatkan bahwa BMI tinggi cenderung memiliki outcome diabetes.

## Data Preparation

Pada Data Preparation dilakukan beberapa tahapan sebagai berikut:

1. **Normalisasi Data Menggunakan MinMaxScaler**
   Karena Outcome sudah memiliki nilai 0 dan 1 maka Fitur Outcome akan direduksi sementara lalu akan dilakukan standarisasi data terhadap fitur lainnya agar nilai memiliki skala yang sama sehingga data yang ditampilkan dapat dilihat sebagai berikut:

   ![multivariate analysis](https://github.com/esteryuricha/ml-advanced/blob/8139ac47e2cd809e54c544c97e84838b6e89cf2a/images/Screenshot%202024-11-23%20100808.png?raw=true)

2. **Mencari Principal Component Analysis (PCA)**
   Jika dilihat dari pairplot yang ada, didapatkan bahwa _Pregnancies_, _Glucose_, _BloodPressure_, _SkinThickness_, _Insulin_, _BMI_, _DiabetesPedigreeFunction_, dan _Age_ menjadi PC Pertama. Nilai varian yang tinggi adalah _Age_ sebesar 0.68, _Pregnancies_ sebesar 0.65, dan _Glucose_ sebesar 0.24

   Berikut ini adalah hasil loading fitur PC Pertama dan PC Kedua:

   ![hasil loading fitur](https://github.com/esteryuricha/ml-advanced/blob/main/images/Screenshot%202024-11-23%20172630.png?raw=true)

3. **Split Data**
   Dataset akan dibagi menjadi 90% data training dan 10% data testing. Pembagian dataset menggunakan **train_test_split**. Sehingga nilai test_size adalah 0.1.

## Model Development

Model akan dikembangkan menggunakan beberapa algoritma berikut:

- **K-Nearest Neighbor**
  Model dilatih menggunakan nilai n_neighbors sebesar 12 dengan metrik jarak **Euclidean**. Jarak yang diambil adalah sebesar 12 dengan iterasi terus menerus sehingga didapatkan nilai MSE untuk data train dan data test. Algoritma ini cocok digunakan pada data yang ukurannya kecil dan pola non-linear seperti dataset ini.

- **Random Forest**
  Random Forest menggunakan model ensemble yang pada proyek ini diatur value parameter pada n_estimators sebanyak 100 dengan maksimal kedalaman setiap estimator adalah sebesar 5, sedangkan nilai random_state yang digunakan adalah sebesar 12 dan n-jobs diisikan nilai -1. Random Forest sering digunakan untuk mendapatkan stabilitas saat melakukan prediksi.

- **Boosting Algorithm**
  Model menggunakan nilai n_estimators sebesar 200 dengan learning_rate sebesar 0.05 dan random_state yang digunakan adalah 55. Boosting Algorithm yang digunakan adalah Adaptive Boosting yaitu AdaBoost. Iterasi yang dilakukan terus menerus sebanyak jumlah yang ditentukan, memungkinkan akurasi data yang semakin baik.

## Evaluation

Evaluasi awal dengan memeriksa nilai MSE dari tiap algoritma dan menghasilkan data sebagai berikut:

Tabel hasil Train MSE vs Test MSE:
| | Train MSE | Test MSE |
|--|-----------|----------|
|KNN|0.000152|0.000154|
|RF|0.00011|0.000117|
|Boosting|0.000146|0.000142|

Hasil menunjukkan bahwa RF memiliki nilai MSE paling rendah diantara kedua model lainnya. Sehingga, model terbaik untuk dataset ini adalah RF.

Lalu data diuji prediksinya dan menghasilkan data sebagai berikut:

```
      y_true	prediksi_KNN	prediksi_RF    prediksi_Boosting
229   0	      0.3	         0.3	         0.4
```

Model kemudian dihitung nilai koefisien determinasinya untuk mendapatkan seberapa baik model menjelaskan variansi dalam data target yaitu Outcome, dan didapatkan data sebagai berikut:

|          | Koefisien Determinasi |
| -------- | --------------------- |
| RF       | 0.4948855841865071    |
| Boosting | 0.3331561791383221    |
| KNN      | 0.3872930369532044    |

Dari hasil koefisien, dapat disimpulkan bahwa model yang terbaik adalah RF.

Kemudian, dilakukan evaluasi kembali dengan metrik berikut dengan menggunakan RandomForest (RF):

- **Accuracy**: Persentase prediksi yang benar.
  Nilai yang didapatkan adalah sebesar 0.84415

- **Precision dan Recall**: Untuk menilai deteksi risiko diabetes pada kelas minoritas.
  Nilai yang didapat untuk nilai precision adalah sebesar 0.83333 dan nilai re-call adalah sebesar 0.71428

- **F1-Score**: Kombinasi antara precision dan recall.
  Nilai yang didapatkan adalah sebesar 0.76923

## Kesimpulan

Faktor utama yang memengaruhi kemungkinan seseorang mengidap diabetes dapat dilihat dari hasil PCA dan dari PC Pertama, yaitu _Age_ (umur), _Pregnancies_ (kehamilan), dan _Glucose_ (kadar gula).

Model prediktif yang tepat digunakan adalah menggunakan Random Forest untuk mendeteksi risiko diabetes pada individu.

**Referensi**

- Dataset Diabetes dari Kaggle (https://www.kaggle.com/api/v1/datasets/download/akshaydattatraykhare/diabetes-dataset/)
- https://kemkes.go.id/id/pola-hidup-sehat-dan-deteksi-dini-bantu-kontrol-gula-darah-pada-penderita-diabetes
- studi kasus pertama predictive analiytics [machine learning terapan](https://www.dicoding.com/academies/319/tutorials/16989)
