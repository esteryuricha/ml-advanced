# Laporan Proyek Akhir Membuat Sistem Rekomendasi Drama Korea

## Project Overview

Proyek ini membuat sebuah sistem rekomendasi drama-drama korea yang bisa ditonton dan memiliki tingkat similarity dengan drama yang ingin ditonton oleh pengguna. Sistem rekomendasi ini menggunakan pendekatan berbasis konten dengan fitur, seperti sinopsis (synopsis), aktor (actor), sutradara (director), dan penulis naskah (screenwriter). Dataset yang digunakna dalam membangun sistem rekomendasi ini diunduh melalui Kaggle dan diproses dengan pendekatan _Content Based Filtering_ untuk mendapatkan drama-drama yang memiliki tingkat similaritas menggunakan _Cosine Similarity_.

## Business Understanding

### Problem Statements

- Bagaimana membangun sistem rekomendasi berbasis konten dengan mempertimbangkan fitur seperti synopsis, aktor, sutradara, dan penulis naskah?
- Bagaimana caranya memberikan rekomendasi Top 3 drama Korea yang relevan berdasarkan drama yang sudah ditonton sebelumnya oleh pengguna?

### Goals

- Mengembangkan sistem rekomendasi drama Korea berbasis fitur dengan bobot yang ditentukan sehingga dapat merekomendasikan Top 3 drama yang relevan.

### Solution Statement

Sistem rekomendasi akan dibangun menggunakan pendekatan _Content Based Filtering_. Setiap drama akan diwakili sebagai vektor yang berisi bobot fitur:

- **Actor** (bobot 3)
- **Director** (bobot 1)
- **Screenwriter** (bobot 6)
- **Synopsis** (bobot 1)

Pembobotan ini didasarkan bahwa sistem rekomendasi mampu memberikan rekomendasi drama dengan cerita yang mirip dengan drama yang ditonton sebelumnya sehingga dapat menggunakan **synopsis** dengan bobot terbesar.
Kemiripan antar-drama akan dihitung menggunakan _Cosine Similarity_.

## Data Understanding

### Informasi Dataset

| Informasi | Keterangan                                                                                          |
| --------- | --------------------------------------------------------------------------------------------------- |
| Link      | https://www.kaggle.com/datasets/chanoncharuchinda/ korean-drama-2015-23-actor-and-reviewmydramalist |
| Lisensi   | CC0: Public Domain                                                                                  |
| Size      | 23.61 MB                                                                                            |
| Files     | 4 files                                                                                             |

Terdapat 4 dataset yang digunakan dalam proyek ini yaitu:

- korean_drama.csv: berisi data drama yang ada dikumpulkan dari tahun 2015 - 2023 dan di-assign pada variabel `dramas` dengan jumlah data sebanyak 1752
- reviews.csv: berisi review drama-drama dari user beserta dengan rating-rating yang ada dan di-assign pada variabel `reviews` dengan jumlah data sebanyak 4562
- wiki_actors.csv: berisi daftar aktor yang bermain di drama-drama dan di-assign pada variabel `actors` dengan jumlah data sebanyak 3090
- recommendations.csv: berisi rekomendasi drama berdasarkan judul drama yang ditonton dan rekomendasi ini didapatkan dari komunitas MyDramaList dan di-assign pada variabel `recommends` dan nantinya akan digunakan sebagai _Ground Truth_ dengan jumlah data sebanyak 1753.

### Univariate Exploratory Data Analysis

Pada tahap ini, Analisis dan eksplorasi setiap variabel pada data dilakukan. Analisis dan Eksplorasi dilakukan pada kesemua data, yaitu _dramas_, _reviews_, _actors_, dan _recommendations_.

#### Drama Variables

Dengan menggunakan perintah `dramas.info()`, dapat dilihat bahwa dataset ini memiliki 17 kolom sebagai berikut:

- _kdrama_id_: ID drama korea yang unik
- _drama_name_: nama drama korea
- _year_: tahun drama tayang
- _director_: sutradara drama yang berupa data list
- _screenwriter_: penulis naskah drama yang berupa data list
- _country_: negara drama ditayangkan yang hanya memiliki 1 value, yaitu "South Korea"
- _type_: tipe yang hanya memiliki 1 value, yaitu "Drama"
- _tot_eps_: total episode drama
- _duration_: durasi tayang tiap episode
- _start_dt_: tanggal pertama drama tayang
- _end_dt_: tanggal terakhir drama tayang
- _aired_on_: hari drama tayang
- _org_net_: jaringan channel yang menayangkan drama
- _content_rt_: content rating apakah ditonton untuk 15 tahun ke atas, ataupun belum diberikan rating
- _synopsis_: sinopsis singkat tentang drama
- _rank_: skor ranking drama
- _pop_: skor popularitas drama

Selain itu, melalui perintah `dramas.isnull().sum()` juga terlihat bahwa ada 6 kolom yang memiliki missing value, yaitu **director**, **screenwriter**, **duration**, **aired_on**, **org_net**, dan **synopsis**.

Terlihat bahwa total data adalah sebanyak 1752 baris dan hanya 5 kolom yang numerik, yaitu year, tot_eps, duration, rank, dan pop.

Untuk memastikan bahwa list drama merupakan drama-drama yang tayang di tahun 2015 - 2023, maka digunakan perintah `dramas.year.unique()` dan dikonfirmasi bahwa data drama berada di tahun 2015 - 2023.

Jika dilihat dari semua kolom yang ada, kolom **director**, **screenwriter**, **rank**, **pop** dan **synopsis** memiliki data yang bisa digunakan sebagai fitur bobot untuk model.

#### Actor Variables

Dataset ini dianalisis tiap variabelnya menggunakan `actors.info()` dengan kolom sebagai berikut:

- _actor_id_: ID aktor Korea Selatan
- _actor_name_: nama aktor Korea Selatan
- _drama_name_: drama yang pernah dibintangi
- _character_name_: nama karakter di drama yang dibintangi
- _role_: nama peran di drama yang dibintangi, apakah 'Main Role' atau 'Support Role' dan lainnya

kemudian dicek apakah ada kolom yang memiliki missing value menggunakan perintah `actors.isnull().sum()` dan didapatkan bahwa Data actors cukup bersih dan tidak ada missing values.

Kita juga perlu mengetahui, berapa jumlah data aktor yang masuk ke list _actors_ (len`actors.actor_id.unique())` dan berapa jumlah drama yang masuk list berdasarkan nilai unique-nya` len(actors.drama_name.unique())`. Didapatkan informasi bahwa ada sejumlah 3090 data aktor di 1643 drama.

#### Review Variables

Dataset ini dianalisis tiap variabel-nya menggunakan perintah `reviews.info()` dan didapatkan informasi kolom sebagai berikut:

- _user_id_: ID user review
- _title_: nama drama yang di-review
- _story_score_: skor untuk cerita drama
- _acting_cast_score_: skor untuk akting pemain
- _music_score_: skor untuk musik dalam drama
- _rewatch_value_score_: skor untuk pengguna menonton ulang drama tersebut
- _overall_score_: skor drama secara keseluruhan
- _review_text_: review drama dalam bentuk kalimat
- _ep_watched_: jumlah episode yang ditonton
- _n_helpful_: jumlah pengguna yang merasa review ini membantu mereka

Data `reviews` hanya memiliki 1 kolom yang memiliki missing value, yaitu kolom _review_text_ sebanyak 6 baris.

Data reviews berkaitan erat dengan rating yang diberikan dengan beberapa kriteria yang ada, untuk mengetahui lebih lengkap tentang data yang direkam lebih rinci dapat menggunakan perintah `reviews.describe()`
Terlihat bahwa score yang diberikan dengan skala 0 - 10.

Kemudian diperlukan juga untuk mengetahui berapa user unique yang memberikan review dari total 10625 baris data yang ada menggunakan perintah `len(reviews.user_id.unique())` dan berapa jumlah drama yang diberikan review menggunakan perintah `len(reviews.title.unique())`
Dari data tersebut, didapatkan informasi bahwa ada sebanyak 4562 pengguna yang memberikan review dan 1279 drama yang di-review. Jika dilihat dari drama yang di-review, dapat disimpulkan bahwa ada drama yang tidak memiliki data review.

#### Recommendation Variables

Dataset ini digunakan sebagai Ground Truth dan jika diterapkan perintah `recommends.info()` akan didapatkan informasi sebagai berikut:

- _kdrama_name_: nama drama korea
- _recommendations_: rekomendasi drama dari komunitas (tidak dijelaskan rekomendasi yang diberikan berdasarkan apa)

Dataset ini memiliki 573 drama yang tidak memiliki rekomendasi dari komunitas, dan data ini nantinya yang akan diamputasi.

## Data Preprocessing

Tahap ini merupakan tahapn persiapan data sebelum data digunakan untuk proses selanjutnya. Setiap data yang telah melalui tahapan univariate exploratory data analysis akan melewati tahapan ini.

### Menggabung nama aktor berdasarkan drama ke dalam bentuk list

Dari data aktor, didapatkan fakta bahwa beberapa aktor tercatat bermain di drama yang sama dan akan dilakukan grouping berdasarkan drama_name untuk membuat actor name dalam bentuk list

### Menggabungkan data drama dan actors

Penggabungan ini diperlukan untuk mendapatkan data aktor siapa saja yang memainkan drama yang ada. Dengan begitu, kita dapat mendapatkan list nama aktor yang memainkan drama yang ada. Terdapat 3395 drama yang memiliki list lengkap nama aktor.

### Mengganti nama kolom title

Pada data reviews terdapat kolom `title` yang sebenarnya menyimpan nama drama dan hal ini perlu disamakan agar dapat diproses lebih lanjut dengan dataset lainnya yang menggunakan nama kolom _drama_name_.

### Menggabungkan data reviews dan dramas

Setelah itu, perlu digabungkan antara data reviews dan data dramas yang sebelumnya sudah digabungkan dengan data aktor dengan menggunakan `pd.merge()`. Penggabungan dilakukan berdasarkan kolom _drama_name_. Setelah penggabungan, hal yang harus dilakukan selanjutnya adalah mengamati apakah ada missing value dari penggabungan yang ada. Hasilnya kolom yang memiliki missing value sebagai berikut:

- director: 1444 data missing
- screenwriter: 1836 data missing
- aired_on: 282 data missing
- org_net: 845 data missing
- synopsis: 16 data missing

Melihat dari penggabungan data yang ada, didapatkan bahwa ada satu drama bisa saja memiliki beberapa review, sehingga rating yang ada dari tiap drama akan digabungkan berdasarkan _drama_name_ dan akan dijumlahkan menggunakan fungsi `.groupby('drama_name').sum()`

Setelah itu yang harus dilakukan, review yang sudah dijumlahkan ratingnya akan digabungkan dengan data drama dan terakhir adalah mendapatkan data yang sudah digabungkan ketiga dataframe ke dalam variabel _all_drama_.

## Data Preparation

Pada tahap ini, data dipersiapkan dan beberapa bagian dilakukan untuk mengatasi missing value baik dihilangkan ataupun diisi dengan data yang dapat digunakan dalam model development.

### Mengatasi missing value pada kolom director dan screenwriter

Dari sekian kolom yang memiliki missing value, ada 2 kolom yang cukup memberi pengaruh pada bagian sistem rekomendasi ini, dan saya tertarik untuk mencari tahu seberapa besar persentase missing value pada data drama ini sehingga dapat diputuskan bahwa 2 kolom ini perlu di-imutable atau diisi dengan value lain.
Nilai missing value pada kolom _director_ dan _screenwriter_ cukup kecil yaitu di bawah 20%. Sedangkan saya ingin tetap mempertahankan data ini agar tidak dibuang, maka missing value pada data _director_ dan _screenwriter_ akan diganti dengan **\*Unknown**.
Setelah itu, perlu diketahui juga missing value lainnya yang ada pada penggabungan data yang tersimpan pada _all_drama_ dan didapatkan output bahwa ada drama yang tidak memiliki data aktornya atau dengan kata lain ada data aktor yang tidak memiliki data di list drama sebanyak 14 drama. Terdapat 67 drama juga yang tidak memiliki synopsis, sehingga diputuskan bahwa data-data tersebut akan dihapus menggunakan perintah `all_drama.dropna()`.

### Mengatasi Missing Value pada Data

Jika diperhatikan, ada drama yang tidak memiliki data actor maupun review text. Data tersebut jumlahnya tidak terlalu significant, sehingga missing value ini akan dibuang saja menggunakan perintah `all_drama.dropna()`. Sehingga didapatkan hasil sebanyak 9569 data.

### Mengatasi missing value pada dataset Ground Truth

Ground Truth memiliki missing value yang harus dihapus terlebih dahulu agar tidak mengganggu jalankan proses evaluasi nantinya dan dibuat dictionary untuk mapping rekomendasi.

### Sorting data untuk mendapatkan fix data

Bagian ini diperlukan untuk membentuk dataframe yang tersorting secara ascending sehingga dapat dilihat data drama berurutan sesuai abjad berdasarkan nama drama-nya. Dari data ini terlihat dari nama drama yang duplikat, sehingga akan dihapus data yang duplikat berdasarkan nama drama dan didapatkan ada _1062 data_ drama yang bisa diproses.

### Konversi data series ke dalam bentuk list

Selanjutnya adalah melakukan konversi data series menjadi list sehingga dapat digunakan dalam model development. Data series yang dimasukkan ke dalam list adalah _drama_name_, _actor_name_, _director_, _screenwriter_, _rank_, _overall_score_, dan _synopsis_. Dengan menggunakan perintah `drama_new.info()` didapatkan bahwa list berisi 835 baris dengan 7 kolom.

## Modelling

Sistem rekomendasi yang akan dibuat menggunakan pendekatan content based filtering. Di awal, kita akan melihat data assign dataframe dari _drama_new_ yang akan diberikan nama baru yaitu **data**

### TF-IDF Vectorizer

Pada tahap ini, sistem akan dibangun berdasarkan sinopsis dari tiap drama yang ada dengan tahapan sebagai berikut:

- Inisialisasi TfidVectorizer dengan perintah `tf = TfidfVectorizer`
- Menentukan bobot untuk masing-masing fitur yang akan dimasukkan dalam model, dimana _actor_ memiliki bobot 3, _director_ bobot 1, _screenwriter_ bobot 6, sisanya pada fitur _synopsis_ sebesar bobot 1. Tujuannya adalah dengan adanya bobot, prioritas rekomendasi akan lebih fokus ke synopsis terlebih dahulu baru ke aktor yang memerankan dramanya dst.
- Memproses data gabungan dengan `stop_words = 'english'` dan transform menggunakan kolom yang baru dibuat yaitu _combined_features_

Outputnya adalah menghasilkan matrix (1062, 9450) lalu dijadikan dalam bentuk matrix dengan menggunakan fungsi `.dense()`. Selanjutnya adalah melihat matriks drama_name dengan nilai bobot yang sudah dikombinasikan sehingga menghasilkan gambar sebagai berikut:

![matrix tf-idf](https://github.com/esteryuricha/ml-advanced/blob/main/images/1_matrix_tf_idf.png?raw=true)

## Cosine Similarity

Setelah mendapatkan korelasi dari drama dan sinopsis, langkah selanjutnya adalah menghitung derajat kesamaan (similarity degree) antara drama satu dan drama lainnya dengan menggunakan teknik cosine similarity. Berikut merupakan hasil dari cosine similarity pada matrix tf-idf yang telah dibuat sebelumnya.

![cosine similarity](https://github.com/esteryuricha/ml-advanced/blob/main/images/2_cosine_sim.png?raw=true)

Lalu diambil 5 sampel terhadap 5 drama yang ada, dan muncullah data sebagai berikut:

![drama samples](https://github.com/esteryuricha/ml-advanced/blob/main/images/3_similarity_in_shape.png?raw=true)

Terlihat juga heatmap dari hasil similarity yang ada sebagai berikut:

![heatmap](https://github.com/esteryuricha/ml-advanced/blob/main/images/4_heatmap.png?raw=true)

## Modeling

### Pendekatan: Cosine Similarity

Cosine Similarity digunakan untuk menghitung kemiripan antar-vektor drama. Drama yang memiliki nilai kemiripan tertinggi akan direkomendasikan kepada pengguna.

- **Proses**:

  1. Menggabungkan atribut berbobot menjadi vektor tunggal.
  2. Menghitung Cosine Similarity antar-vektor.
  3. Menampilkan top-3 drama yang paling mirip dengan drama yang sudah ditonton pengguna.

- **Parameter yang Digunakan**:
  - Fitur berbobot (aktor, sutradara, penulis naskah, dan sinopsis).
  - Cosine Similarity sebagai metrik utama.

Dalam mempermudah dalam menampilkan rekomendasi drama, dibuat suatu function bernama recommend_drama yang nantinya akan menghasilkan rekomendasi drama beserta detil informasinya yang tersimpan pada dataframe.

#### Contoh Penggunaan

```python
recommend_drama(
    title="Hospital Playlist",
    cosine_sim=cosine_sim,
    data=drama_data,
    actor_weight=0.1,
    director_weight=0.1,
    screenwriter_weight=0.6,
    synopsis_weight=0.2
)
```

## Evaluasi

Evaluasi dilakukan dengan meminta user untuk menginputkan drama yang ingin dimasukkan ke dalam sistem rekomendasi. Pada case ini menggunakan contoh "hospital playlist". Dikarenakan nama drama pada dataframe menggunakan huruf kapital di setiap kata-nya, maka judul yang diinputkan oleh user akan diberikan fungsi `.title()` agar dapat menyesuaikan dengan data.

Data rinci mengenai drama "hospital playlist" adalah sebagai berikut:

![detil](https://github.com/esteryuricha/ml-advanced/blob/main/images/6_detil%20informasi.png?raw=true)

Contoh hasil rekomendasi 3 drama yang bisa ditonton berikutnya adalah:

![hasil rekomendasi](https://github.com/esteryuricha/ml-advanced/blob/main/images/5_top3_recommendation.png?raw=true)

dapat dilihat dari kedua tabel bahwa ada hal yang membuat menjadi rekomendasi teratas karena memiliki screenwriter yang sama dan juga ada aktor yang turut bermain di drama yang sama.

### Metrik Evaluasi

kemudian metrik evaluasi dengan mengambil nilai precision, recall, f1-score, dan accuracy. Dari data terlihat bahwa jika diinputkan dengan drama "Hospital Playlist" akan menghasilkan sebagai berikut:

- precision: 0.50
  Dari semua drama yang direkomendasikan, hanya 50% yang benar-benar relevan berdasarkan ground truth.
  Ini menunjukkan bahwa sistem menghasilkan beberapa rekomendasi yang tidak relevan (false positives).

- recall: 1.00
  Semua drama yang relevan dalam ground truth berhasil direkomendasikan oleh sistem.
  Ini menunjukkan bahwa sistem sangat baik dalam mencakup semua pilihan yang benar (no false negatives).

- F1-score: 0.67
  F1-score adalah harmoni antara precision dan recall. Dengan nilai 0.67, ini menunjukkan bahwa performa sistem masih cukup baik tetapi perlu meningkatkan precision untuk lebih akurat.

- Accuracy: 0.50
  Secara keseluruhan, hanya 50% dari semua drama (baik yang relevan maupun tidak relevan) yang diprediksi dengan benar.
  Ini menunjukkan bahwa separuh prediksi sistem tidak sesuai dengan ground truth.

### Hasil

**Relevansi**: Hanya 50% rata-rata precision hasil rekomendasi yang sesuai dengan rekomendasi dari komunitas MyDramaList. Hal ini dapat terjadi karena kurang cukupnya informasi mengenai rekomendasi dari MyDramaList berdasarkan apa. Jika dianalisis manual, genre dari drama adalah hal yang penting, tetapi pada dataset tidak memiliki data genre, sehingga kesulitan dalam membuat model yang tepat.

## Kesimpulan

- Sistem rekomendasi berbasis atribut memberikan hasil rekomendasi dapat memberikan tingkat similarity yang sesuai dengan dataset yang disediakan.
- Pendekatan berbasis konten dengan Cosine Similarity sangat cocok untuk dataset drama Korea dengan atribut deskriptif seperti synopsis, aktor, sutradara, dan penulis naskah.
- Ground Truth yang ada memberikan rekomendasi berdasarkan genre dan aktor sedangkan genre tidak terdaftar pada dataset sehingga sulit untuk mendapatkan data precision yang sesuai.

## Dampak Bisnis

Dampak bisnis yang diharapkan adalah menambah kepuasan pengguna dan dapat mencapai keunggulan kompetitif

## Referensi

- Dataset Diabetes dari Kaggle (https://www.kaggle.com/datasets/chanoncharuchinda/ korean-drama-2015-23-actor-and-reviewmydramalist)
- studi kasus sistem rekomendasi [machine learning terapan](https://www.dicoding.com/academies/319/tutorials/17069)
