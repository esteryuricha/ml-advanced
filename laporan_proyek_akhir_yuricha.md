# Laporan Proyek Akhir Membuat Sistem Rekomendasi Drama Korea

## Project Overview

### Latar Belakang

Di era digital saat ini, pengguna memiliki akses ke ribuan judul drama Korea melalui berbagai platform streaming. Saking banyaknya pilihan, membuat pengguna sering kali kebingungan sampai menjadi **paradox of choice**, di mana pengguna merasa kesulitan memilih drama yang sesuai dengan preferensi mereka. Netflix sebagai salah satu OTT yang sukses mencapai keunggulan kompetitif melalui recommender system dan membuat penggunanya tetap menggunakan layanan streaming tersebut. Oleh karena itu, sistem rekomendasi merupakan hal yang penting untuk saat ini di era pilihan yang terlampau banyak sehingga dapat membantu pengguna menemukan drama yang relevan berdasarkan preferensi mereka.

Sistem rekomendasi berbasis konten (content-based filtering) adalah salah satu metode yang populer karena mampu memberikan rekomendasi yang personal berdasarkan atribut drama, seperti **sinopsis**, **aktor**, **sutradara**, dan **penulis naskah**. Dengan menggunakan pendekatan ini, proyek ini bertujuan untuk mengembangkan sistem rekomendasi drama Korea yang relevan dan meningkatkan pengalaman pengguna.

### Tujuan Proyek

- **Membangun sistem rekomendasi drama Korea berbasis konten** yang dapat memberikan rekomendasi drama dengan tingkat similaritas tinggi berdasarkan drama yang ditonton sebelumnya.
- Memberikan **top-3 rekomendasi drama Korea** yang relevan kepada pengguna dengan memanfaatkan fitur berbobot seperti sinopsis, aktor, sutradara, dan penulis naskah.

---

## Business Understanding

### Problem Statements

1. Bagaimana membangun sistem rekomendasi berbasis konten dengan mempertimbangkan fitur seperti sinopsis, aktor, sutradara, dan penulis naskah?
2. Bagaimana memberikan rekomendasi top-3 drama Korea yang relevan berdasarkan drama yang ditonton sebelumnya oleh pengguna?

### Goals

1. Mengembangkan sistem rekomendasi drama Korea berbasis fitur dengan bobot yang ditentukan.
2. Menghasilkan rekomendasi top-3 drama yang relevan untuk pengguna.

### Solution Statement

Sistem rekomendasi dibangun menggunakan **Content-Based Filtering**. Setiap drama direpresentasikan sebagai vektor yang menggabungkan bobot fitur berikut:

- **Actor** (bobot: 0.3)
- **Director** (bobot: 0.1)
- **Screenwriter** (bobot: 0.6)
- **Synopsis** (bobot: 0.2)

Kemiripan antar-drama dihitung menggunakan **Cosine Similarity**, dengan drama yang memiliki nilai similaritas tertinggi direkomendasikan kepada pengguna.

---

## Data Understanding

### Informasi Dataset

| Informasi | Keterangan                                                                                          |
| --------- | --------------------------------------------------------------------------------------------------- |
| Link      | https://www.kaggle.com/datasets/chanoncharuchinda/ korean-drama-2015-23-actor-and-reviewmydramalist |
| Lisensi   | CC0: Public Domain                                                                                  |
| Size      | 23.61 MB                                                                                            |
| Files     | 4 files                                                                                             |

Terdapat 4 dataset yang digunakan dalam proyek ini yaitu:

| **Dataset**         | **Deskripsi**                                                                                                                                                                                                                                   |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| korean_drama.csv    | data drama yang ada dikumpulkan dari tahun 2015 - 2023 dan di-assign pada variabel `dramas` dengan jumlah data sebanyak 1752                                                                                                                    |
| reviews.csv         | review drama-drama dari user beserta dengan rating-rating yang ada dan di-assign pada variabel `reviews` dengan jumlah data sebanyak 4562                                                                                                       |
| wiki_actors.csv     | daftar aktor yang bermain di drama-drama dan di-assign pada variabel `actors` dengan jumlah data sebanyak 3090                                                                                                                                  |
| recommendations.csv | rekomendasi drama berdasarkan judul drama yang ditonton dan rekomendasi ini didapatkan dari komunitas MyDramaList dan di-assign pada variabel `recommends` dan nantinya akan digunakan sebagai _Ground Truth_ dengan jumlah data sebanyak 1753. |

---

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

---

## Data Preparation

Tahap ini merupakan tahapn persiapan data sebelum data digunakan untuk proses selanjutnya. Setiap data yang telah melalui tahapan univariate exploratory data analysis akan melewati tahapan ini. Data dipersiapkan dan beberapa bagian dilakukan untuk mengatasi missing value baik dihilangkan ataupun diisi dengan data yang dapat digunakan dalam model development.

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

### Mengatasi missing value pada kolom director dan screenwriter

Dari sekian kolom yang memiliki missing value, ada 2 kolom yang cukup memberi pengaruh pada bagian sistem rekomendasi ini, dan saya tertarik untuk mencari tahu seberapa besar persentase missing value pada data drama ini sehingga dapat diputuskan bahwa 2 kolom ini perlu di-imutable atau diisi dengan value lain.
Nilai missing value pada kolom _director_ dan _screenwriter_ cukup kecil yaitu di bawah 20%. Sedangkan saya ingin tetap mempertahankan data ini agar tidak dibuang, maka missing value pada data _director_ dan _screenwriter_ akan diganti dengan **\*Unknown**.
Setelah itu, perlu diketahui juga missing value lainnya yang ada pada penggabungan data yang tersimpan pada _all_drama_ dan didapatkan output bahwa ada drama yang tidak memiliki data aktornya atau dengan kata lain ada data aktor yang tidak memiliki data di list drama sebanyak 14 drama. Terdapat 67 drama juga yang tidak memiliki synopsis, sehingga diputuskan bahwa data-data tersebut akan dihapus menggunakan perintah `all_drama.dropna()`.

### Mengatasi Missing Value pada Data

Jika diperhatikan, ada drama yang tidak memiliki data actor maupun review text. Data tersebut jumlahnya tidak terlalu significant, sehingga missing value ini akan dibuang saja menggunakan perintah `all_drama.dropna()`. Sehingga didapatkan hasil sebanyak 9569 data.

### Mengatasi missing value pada dataset Ground Truth

Ground Truth memiliki missing value yang harus dihapus terlebih dahulu agar tidak mengganggu jalankan proses evaluasi nantinya dan dibuat dictionary untuk mapping rekomendasi.

### Sorting data untuk mendapatkan fix data

Bagian ini diperlukan untuk membentuk dataframe yang tersorting secara ascending sehingga dapat dilihat data drama berurutan sesuai abjad berdasarkan nama drama-nya. Dari data ini terlihat dari nama drama yang duplikat, sehingga akan dihapus data yang duplikat berdasarkan nama drama dan didapatkan ada _835 data_ drama yang bisa diproses.

### Konversi data series ke dalam bentuk list

Selanjutnya adalah melakukan konversi data series menjadi list sehingga dapat digunakan dalam model development. Data series yang dimasukkan ke dalam list adalah _drama_name_, _actor_name_, _director_, _screenwriter_, _rank_, _overall_score_, dan _synopsis_. Dengan menggunakan perintah `drama_new.info()` didapatkan bahwa list berisi 835 baris dengan 7 kolom.

istem rekomendasi yang akan dibuat menggunakan pendekatan content based filtering. Di awal, kita akan melihat data assign dataframe dari _drama_new_ yang akan diberikan nama baru yaitu **data**

### TF-IDF Vectorizer

Pada tahap ini, sistem akan dibangun berdasarkan sinopsis dari tiap drama yang ada dengan tahapan sebagai berikut:

- Inisialisasi TfidVectorizer dengan perintah `tf = TfidfVectorizer`
- Menentukan bobot untuk masing-masing fitur yang akan dimasukkan dalam model, dimana _actor_ memiliki bobot 3, _director_ bobot 1, _screenwriter_ bobot 6, sisanya pada fitur _synopsis_ sebesar bobot 1. Tujuannya adalah dengan adanya bobot, prioritas rekomendasi akan lebih fokus ke synopsis terlebih dahulu baru ke aktor yang memerankan dramanya dst.
- Memproses data gabungan dengan `stop_words = 'english'` dan transform menggunakan kolom yang baru dibuat yaitu _combined_features_

Outputnya adalah menghasilkan matrix (1062, 9450) lalu dijadikan dalam bentuk matrix dengan menggunakan fungsi `.dense()`. Selanjutnya adalah melihat matriks drama_name dengan nilai bobot yang sudah dikombinasikan sehingga menghasilkan gambar sebagai berikut:

![matrix tf-idf](https://github.com/esteryuricha/ml-advanced/blob/main/images/1_matrix_tf_idf.png?raw=true)

---

## Modeling and Result

### Pendekatan Modeling

**1. Content-Based Filtering**

- Menggunakan cosine similarity untuk menghitung kemiripan antar-drama berdasarkan fitur:
  - **Synopsis** (bobot: 0.2)
  - **Actor** (bobot: 0.3)
  - **Director** (bobot: 0.1)
  - **Screenwriter** (bobot: 0.6)

**2. Top-N Recommendation**

- Drama dengan skor similarity tertinggi (kecuali drama input) akan direkomendasikan sebagai **Top-3**.
- Dalam mempermudah dalam menampilkan rekomendasi drama, dibuat suatu function bernama recommend_drama yang nantinya akan menghasilkan rekomendasi drama beserta detil informasinya yang tersimpan pada dataframe.

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

#### Contoh Detil Informasi Drama (Contoh: _Hospital Playlist_)

| **Nama Drama**    | **Overall Score** | **Actor**                                         | **Director**    | **Screenwriter** | **Synopsis**                                      |
| ----------------- | ----------------- | ------------------------------------------------- | --------------- | ---------------- | ------------------------------------------------- |
| Hospital Playlist | 6.5               | ['Jeon Mi Do', 'Jo Jung Suk', 'Jung Kyung Ho',..] | ['Shin Won Ho'] | ['Lee Woo Jung'] | The stories of people going through their days... |

#### Hasil Rekomendasi (Contoh: _Hospital Playlist_)

| **Nama Drama**               | **Overall Score** | **Actor**                                          | **Director**    | **Screenwriter** | **Synopsis**                                      |
| ---------------------------- | ----------------- | -------------------------------------------------- | --------------- | ---------------- | ------------------------------------------------- |
| _Hospital Playlist Season 2_ | 8.0               | ['Jeon Mi Do', 'Jo Jung Suk', 'Jung Kyung Ho',...] | ['Shin Won Ho'] | ['Lee Woo Jung'] | Everyday is extraordinary for five doctors and..  |
| _Reply 1988_                 | 9.5               | ['Go Kyung Pyo', 'Lee Dong Hwi', 'Lee Hye Ri',..]  | ['Shin Won Ho'] | ['Lee Woo Jung'] | Five childhood friends, who all live in the sa... |
| _Prison Playbook_            | 6.5               | ['Im Hwa Young', 'Jung Kyung Ho', 'Kim Kyung N...] | ['Shin Won Ho'] | ['Lee Woo Jung'] | Kim Je Hyuk, a famous baseball player, is arre... |

Terlihat bahwa hasil rekomendasi memprioritaskan Data **Screenwriter** dan **Actor** yang sama dan baru melihat dari **Synopsis** dan **Director**.

---

## Evaluation

### Metrik Evaluasi

Untuk mengevaluasi sistem rekomendasi, digunakan metrik berikut:

1. **Precision**: Proporsi rekomendasi yang relevan.
2. **Recall**: Proporsi item relevan yang berhasil direkomendasikan.
3. **F1-Score**: Rata-rata harmonis antara precision dan recall.
4. **Accuracy**: Proporsi rekomendasi yang benar.

#### Hasil Evaluasi

| **Metric** | **Score** |
| ---------- | --------- |
| Precision  | 0.50      |
| Recall     | 1.00      |
| F1-Score   | 0.67      |
| Accuracy   | 0.50      |

### Interpretasi Hasil

- Precision rendah (0.50) menunjukkan beberapa rekomendasi tidak relevan (false positives tinggi).
- Recall tinggi (1.00) menunjukkan semua drama relevan berhasil direkomendasikan.
- F1-Score cukup baik (0.67), tetapi precision perlu ditingkatkan.
- Accuracy rendah (0.50), menunjukkan sistem masih memiliki ruang untuk perbaikan.

---

## Kesimpulan

1. Sistem rekomendasi berbasis atribut memberikan rekomendasi yang relevan berdasarkan fitur deskriptif drama.
2. Pendekatan cosine similarity efektif untuk menghitung kemiripan antar-drama.
3. Ground truth MyDramaList cenderung didasarkan pada genre, tetapi dataset tidak memiliki informasi genre sehingga precision rendah.

---

## Saran Perbaikan

1. Menambahkan fitur genre ke dalam dataset untuk meningkatkan relevansi rekomendasi.
2. Menyesuaikan bobot fitur agar precision lebih tinggi tanpa mengorbankan recall.
3. Menggunakan teknik **hyperparameter tuning** untuk meningkatkan performa model.

---

## Referensi

- Referensi Latar Belakang: [Strategi Netflix](https://sj.eastasouth-institute.com/index.php/smb/article/view/61/43)
- Kaggle: [Korean Drama Dataset](https://www.kaggle.com/datasets/chanoncharuchinda/korean-drama-2015-23-actor-and-reviewmydramalist)
- Dicoding: [Studi Kasus Sistem Rekomendasi](https://www.dicoding.com/academies/319/tutorials/17069)
