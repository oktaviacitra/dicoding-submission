# Laporan Proyek Machine Learning - Nama Anda

## Domain Proyek

Cuaca memainkan peran penting dalam kehidupan sehari-hari manusia. Prediksi cuaca yang akurat sangat penting untuk berbagai sektor, mulai dari pertanian, transportasi, hingga perencanaan bencana karena dapat memperkecil dampak yang terjadi akibat keadaan cuaca yang tidak menentu. Dalam beberapa dekade terakhir, kemajuan teknologi informasi dan kemampuan komputasi telah membuka peluang baru untuk meningkatkan akurasi prediksi cuaca melalui penggunaan machine learning.

[Sistem Pemantauan Cuaca Berdasarkan Kecepatan Angin, Suhu dan Kelembaban Udara Berbasis Internet of Things](https://openlibrarypublications.telkomuniversity.ac.id/index.php/engineering/article/download/20764/20289) 

## Business Understanding

### Problem Statements

Berdasarkan latar belakang di atas, rincian masalahnya adalah sebagai berikut:
- Apa algoritma yang sesuai untuk memprediksi cuaca yang berdampak signifikan pada sektor-sektor kritis seperti pertanian, transportasi, dan manajemen bencana?
- Bagaimana cara menentukan hasil prediksi dari algoritma-algoritma Machine Learning dapat dikatakan optimal?

### Goals

Untuk menangani rincian masalah di atas, maka perlu dibuat tujuan sebagai berikut:
- Membandingkan algoritma-algoritma klasifikasi untuk mendapatkan algoritma yang paling sesuai untuk sistem prediksi cuaca diantaranya Decision Tree, Random Forest, Support Vector Machine, K Nearest Neighbors, Gradient Boosting, Ada Boost, dan Extra Trees.
- Melakukan evaluasi terhadap metrik dari masing-masing algoritma.

### Solution statements
Solusi yang dapat dilakukan untuk memenuhi goals proyek ini diantaranya sebagai berikut:
- Membandingkan 7 algoritma sekaligus dalam bentuk tabel yang berisi metrik evaluasi.
- Melakukan hyperparameter tuning terhadap 1 algoritma yang memiliki nilai paling unggul di metrik evaluasi.

## Data Understanding
Dataset yang digunakan pada proyek kali ini dibuat oleh Nikhil Narayan yang di upload ke Kaggle pada Juni 2024. Sumber dataset: [Weather Type Classification](https://archive.ics.uci.edu/ml/datasets/Restaurant+%26+consumer+data](https://www.kaggle.com/datasets/nikhil7280/weather-type-classification)). Pada dataset ini terdiri dari 13200 baris dan 10 kolom data tanpa ada missing values.

Selanjutnya uraikanlah seluruh variabel atau fitur pada data. Sebagai contoh:  

### Variabel-variabel pada dataset adalah sebagai berikut:
| Nama | Jenis | Keterangan| Variabel |
| --- | ----- | ------ | ------ |
| Temperature | Numerik Kontinu | Suhu dalam derajat Celsius | Dependent |
| Humidity | Numerik Diskrit | Persentase kelembapan | Dependent |
| Wind Speed | Numerik Kontinu | Kecepatan angin dalam kilometer per jam | Dependent |
| Precipitation | Numerik Diskrit | Persentase curah hujan | Dependent |
| Cloud Cover | Kategorikal Ordinal | Kondisi awan | Dependent |
| Atmospheric Pressure | Numerik Kontinu | Tekanan atmosfer dalam hPa | Dependent |
| UV Index | Numerik Diskrit | Kekuatan radiasi ultraviolet | Dependent |
| Season | Kategorikal Nominal | Kondisi musim | Dependent |
| Visibility | Numerik Kontinu | Jarak maksimum di mana objek dapat terlihat dan dikenali dengan mata telanjang | Dependent |
| Location | Kategorikal Ordinal | Jenis lokasi | Dependent |
| Weather Type | Kategorikal Nominal | Jenis cuaca | Independent |

### Exploratory Data Analysis
### Univariate
Menggunakan 1.5xIQR rule, ditemukan 4 variabel mengandung outlier data, diantaranya:

<img width="813" alt="Screenshot 2024-07-20 at 23 56 26" src="https://github.com/user-attachments/assets/b1e5575d-2f8c-48b2-9f1e-1388f6bed241">

Weather type merupakan variabel yang menjadi target pada proyek ini. Proyek ini menggunakan balanced dataset sehingga hal ini membantu mencegah overfitting pada kelas mayoritas selama pengembangan model machine learning sebab model tidak akan terlalu terfokus pada kelas mayoritas dan mengabaikan kelas minoritas.

![weather type](https://github.com/user-attachments/assets/17097a91-5969-4250-8426-83c4129106d6)

### Multivariate

![correlation matrixx](https://github.com/user-attachments/assets/43d0566d-cbbf-4ca1-8efb-d1c9ece40735)

Berdasarkan heatmap diatas dapat diketahui bahwa

| Kolom | Korelasi | Skor | 
| --- | ----- | ------ | 
| Humidity - Precipitation (%) | Positif | 0.638631 |
| Wind Speed - UV Index | Tidak ada | -0.068147 |
| Humidity - Visibility (km) | Negatif | -0.479969 |

## Data Preparation
Berikut merupakan tahapan-tahapan dalam Data Preparation:
- Mengganti nilai data outliers menggunakan [KNN Imputer](https://scikit-learn.org/stable/modules/generated/sklearn.impute.KNNImputer.html)
  
  <img width="821" alt="Screenshot 2024-07-20 at 23 57 44" src="https://github.com/user-attachments/assets/4c8a81e3-d50b-4974-8b18-1783a7016196">

- Melakukan encoding terhadap variabel-variabel kategorikal

  | Kolom | Encoding | Alasan |
  | --- | ----- | ------ |
  | Location | Label | nilai perlu menunjukkan tingkat dataran |
  | Cloud cover | Label | nilai perlu menunjukkan tingkat kerapatan awan di langit |
  | Season | One hot | nilai menunjukkan tidak ada urutan khusus |
  
- Melakukan normalisasi data ke semua variabel menggunakan [Standar Scaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html).

  <img width="1522" alt="Screenshot 2024-07-21 at 00 04 58" src="https://github.com/user-attachments/assets/3f67f838-7198-4bc0-96af-8fdd8472c60c">

- Memisahkan data menjadi dua jenis menggunakan [Train Test Split](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html).

  | Jenis | Persentase | Jumlah Baris |
  | --- | ----- | ------ |
  | Train | 90% | 11880 |
  | Test | 10% | 1320 |

## Modeling
Setelah data siap diproses lebih lanjut, maka akan dilanjutkan pada memilih metode terbaik untuk dapat memprediksi cuaca seakurat mungkin.

| Nama | Kelebihan | Kekurangan |
| --- | ----- | ------ |
| Decision Tree | Mampu menangani hubungan non-linear antara fitur dan target | Struktur menjadi kompleks dan lambat untuk dibangun jika menangani data besar |
| Random Forest | Mengurangi varians dan meningkatkan generalisasi model dengan menggabungkan prediksi dari banyak pohon | Membutuhkan banyak memori karena menghasilkan banyak decision tree | 
| Support Vector Machine | Memiliki fleksibilitas dalam menangani data kompleks karena penggunaan kernel tricks | Kurang optimal pada dataset yang tidak seimbang karena cenderung fokus pada margin yang memaksimalkan kelas mayoritas | 
| K Nearest Neighbors	 | dapat menangani data multikelas tanpa perlu modifikasi khusus | mudah terpengaruh oleh data yang noisy dan outliers yang dapat mengurangi akurasi model |
| Gradient Boosting | Menghasilkan estimasi pentingnya fitur selama masa pembelajaran | Sensitif terhadap fitur-fitur yang tidak berkorelasi. |
| Ada Boost | Mudah beradaptasi dengan data baru dan berubah dari waktu ke waktu karena sifatnya yang iteratif | sensitif terhadap noise dan outliers karena mempengaruhi pemberian bobot yang tidak sesuai pada iterasi berikutnya| 
| Extra Trees | lebih cepat dalam pelatihan karena membagi node berdasarkan split points yang dipilih secara acak tanpa melakukan pencarian split optimal | kurang optimal pada dataset yang tidak seimbang tanpa penyesuaian tambahan | 

**Rubrik/Kriteria Tambahan (Opsional)**: 
- Menjelaskan kelebihan dan kekurangan dari setiap algoritma yang digunakan.
- Jika menggunakan satu algoritma pada solution statement, lakukan proses improvement terhadap model dengan hyperparameter tuning. **Jelaskan proses improvement yang dilakukan**.
- Jika menggunakan dua atau lebih algoritma pada solution statement, maka pilih model terbaik sebagai solusi. **Jelaskan mengapa memilih model tersebut sebagai model terbaik**.

## Evaluation
Pada bagian ini anda perlu menyebutkan metrik evaluasi yang digunakan. Lalu anda perlu menjelaskan hasil proyek berdasarkan metrik evaluasi yang digunakan.

Sebagai contoh, Anda memiih kasus klasifikasi dan menggunakan metrik **akurasi, precision, recall, dan F1 score**. Jelaskan mengenai beberapa hal berikut:
- Penjelasan mengenai metrik yang digunakan
- Menjelaskan hasil proyek berdasarkan metrik evaluasi

Ingatlah, metrik evaluasi yang digunakan harus sesuai dengan konteks data, problem statement, dan solusi yang diinginkan.

**Rubrik/Kriteria Tambahan (Opsional)**: 
- Menjelaskan formula metrik dan bagaimana metrik tersebut bekerja.

**---Ini adalah bagian akhir laporan---**

_Catatan:_
- _Anda dapat menambahkan gambar, kode, atau tabel ke dalam laporan jika diperlukan. Temukan caranya pada contoh dokumen markdown di situs editor [Dillinger](https://dillinger.io/), [Github Guides: Mastering markdown](https://guides.github.com/features/mastering-markdown/), atau sumber lain di internet. Semangat!_
- Jika terdapat penjelasan yang harus menyertakan code snippet, tuliskan dengan sewajarnya. Tidak perlu menuliskan keseluruhan kode project, cukup bagian yang ingin dijelaskan saja.

