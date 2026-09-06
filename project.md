# Gagasan Pemanfaatan AI untuk Klasifikasi dan Pengarahan Sampah

## Status dokumen

- Versi: 0.1
- Status: Draf konsep awal
- Fokus: Paper penelitian, proof of concept model PyTorch, dan video edukasi
- Implementasi lapangan: Di luar ruang lingkup penelitian saat ini

## 1. Ringkasan proyek

Proyek ini mengusulkan pemanfaatan kecerdasan buatan berbasis computer vision untuk membantu proses pemilahan sampah. Kamera atau foto digunakan sebagai masukan, kemudian model mengidentifikasi jenis material sampah dan memberikan rekomendasi awal mengenai jalur penanganannya.

Gagasan ini dilatarbelakangi oleh pengamatan bahwa proses pemilahan pada fasilitas pengolahan sampah menjadi furnitur dan energi masih banyak dilakukan secara manual. AI diusulkan sebagai sistem pendukung keputusan yang dapat menangani kasus rutin secara lebih terstruktur, sedangkan manusia tetap memeriksa kasus yang ambigu, berisiko, atau memiliki confidence rendah.

Penelitian ini tidak bertujuan membuktikan peningkatan efisiensi operasional di fasilitas nyata. Model yang dibuat berfungsi sebagai proof of concept dan dievaluasi menggunakan dataset citra serta metrik statistik.

## 2. Latar belakang

Pemilahan merupakan tahap penting karena material yang berbeda memerlukan penanganan yang berbeda. Sebagian plastik dapat dimanfaatkan kembali sebagai bahan produk seperti plastic lumber atau furnitur, sebagian sampah mudah terbakar dapat dipertimbangkan untuk pemulihan energi, dan material lain perlu didaur ulang melalui jalur berbeda, ditangani secara khusus, atau menjadi residu.

Dalam proses manual, petugas harus mengidentifikasi material dan menentukan jalur penanganannya satu per satu. Proses tersebut berpotensi menghadapi beberapa masalah:

- beban kerja berulang pada petugas;
- keputusan pemilahan yang kurang konsisten;
- risiko salah penanganan pada benda yang ambigu atau berbahaya;
- pencatatan hasil pemilahan yang belum terstruktur;
- ketergantungan pada pengetahuan dan pengalaman masing-masing petugas.

AI dapat membantu memberikan identifikasi dan rekomendasi awal. Namun, keputusan model tidak selalu benar dan tidak semua karakteristik penting—seperti kadar air, komposisi kimia, atau tingkat kontaminasi—dapat diketahui hanya dari gambar. Oleh karena itu, sistem dirancang dengan pendekatan human-in-the-loop.

## 3. Pernyataan masalah

Bagaimana model deep learning berbasis citra dapat digunakan sebagai proof of concept untuk:

1. mengklasifikasikan sampah berdasarkan jenis material;
2. menampilkan confidence score dari setiap prediksi;
3. memberikan rekomendasi awal jalur penanganan sampah;
4. menyerahkan kasus dengan confidence rendah atau risiko tinggi kepada manusia;
5. menggambarkan potensi pemanfaatan AI untuk membuat proses pemilahan lebih terstruktur?

## 4. Tujuan

### 4.1 Tujuan utama

Mengembangkan dan mengevaluasi proof of concept sistem klasifikasi citra sampah berbasis PyTorch yang dilengkapi rekomendasi jalur penanganan dan mekanisme human-in-the-loop.

### 4.2 Tujuan khusus

- Menentukan taksonomi kelas sampah yang relevan dengan konteks Indonesia dan dapat dipelajari dari citra.
- Melatih serta membandingkan model deep learning untuk klasifikasi sampah.
- Mengukur performa model menggunakan accuracy, precision, recall, F1-score, dan confusion matrix.
- Menampilkan kelas prediksi beserta confidence score.
- Menentukan mekanisme rujukan kepada manusia berdasarkan threshold confidence dan tingkat risiko.
- Memetakan hasil klasifikasi ke tiga kelompok rekomendasi: bahan furnitur, pemulihan energi, dan penanganan lain atau residu.
- Menjelaskan gagasan kepada masyarakat umum melalui video edukasi dengan bahasa nonteknis.

## 5. Pertanyaan penelitian

1. Seberapa baik model deep learning dapat membedakan sepuluh kelas material sampah pada dataset penelitian?
2. Model atau arsitektur mana yang menghasilkan keseimbangan terbaik antara performa dan kebutuhan komputasi?
3. Bagaimana penggunaan confidence threshold memengaruhi jumlah prediksi otomatis dan jumlah kasus yang dirujuk kepada manusia?
4. Kesalahan klasifikasi apa yang paling sering terjadi dan apa implikasinya terhadap rekomendasi penanganan?
5. Bagaimana human-in-the-loop dapat digunakan untuk mengurangi risiko keputusan otomatis pada kasus ambigu atau berbahaya?

## 6. Ruang lingkup

### Termasuk dalam ruang lingkup

- Penggunaan dataset citra publik dan/atau foto tambahan yang dikumpulkan tim.
- Klasifikasi satu objek sampah dominan dalam satu gambar atau frame kamera.
- Pelatihan dan evaluasi model menggunakan PyTorch.
- Perbandingan baseline dengan satu atau beberapa model transfer learning.
- Simulasi rekomendasi jalur penanganan berdasarkan aturan yang telah ditentukan.
- Simulasi human-in-the-loop berdasarkan confidence score.
- Demonstrasi prediksi melalui unggahan foto dan/atau kamera.
- Penyusunan paper dan video edukasi.

### Tidak termasuk dalam ruang lingkup

- Pemasangan sistem di fasilitas pengolahan sampah nyata.
- Integrasi dengan conveyor, robot pemilah, atau mesin industri.
- Pengukuran langsung terhadap waktu kerja, biaya operasional, atau kapasitas fasilitas.
- Pengujian laboratorium terhadap jenis polimer, kadar air, nilai kalor, kontaminasi, atau kandungan bahan berbahaya.
- Keputusan otomatis final tanpa verifikasi manusia.
- Klaim bahwa sistem telah terbukti meningkatkan efisiensi fasilitas nyata.
- Deteksi banyak objek yang saling bertumpuk dalam satu frame pada versi awal.

## 7. Taksonomi kelas sampah

Model dirancang untuk memprediksi sepuluh kelas material. Kelas model dipisahkan dari jalur penanganan karena satu material dapat memiliki tujuan berbeda tergantung kondisi dan teknologi fasilitas.

| No. | Label model | Nama umum | Contoh |
| ---: | --- | --- | --- |
| 1 | `organic` | Sampah organik | Sisa makanan dan kulit buah |
| 2 | `wood_vegetation` | Kayu dan vegetasi | Kayu, ranting, dan daun |
| 3 | `paper_cardboard` | Kertas dan kardus | Kertas, koran, dan kardus |
| 4 | `rigid_plastic` | Plastik keras | Botol, jeriken, tutup, dan wadah plastik keras |
| 5 | `flexible_plastic` | Plastik fleksibel | Kantong plastik, sachet, dan plastik film |
| 6 | `textile_rubber_leather` | Tekstil, karet, dan kulit | Kain, sepatu, tas, dan potongan karet |
| 7 | `metal` | Logam | Kaleng, aluminium, dan potongan besi |
| 8 | `glass_ceramic` | Kaca dan keramik | Botol kaca, pecahan kaca, dan keramik |
| 9 | `battery_electronic` | Baterai dan elektronik | Baterai, kabel, ponsel, dan komponen elektronik |
| 10 | `mixed_residual` | Sampah campuran atau residu | Popok, pembalut, puntung rokok, dan benda multimaterial yang tidak dapat dikenali dengan aman |

Taksonomi ini mengadaptasi kelompok komposisi sampah yang digunakan oleh Sistem Informasi Pengelolaan Sampah Nasional (SIPSN), lalu memecah plastik menjadi plastik keras dan fleksibel serta memisahkan baterai dan elektronik untuk mendukung kebutuhan keselamatan dan rekomendasi proyek.

## 8. Rekomendasi jalur penanganan

Keluaran model tidak langsung menyatakan keputusan final. Kelas material diproses oleh aturan konseptual untuk menghasilkan salah satu rekomendasi berikut:

1. `FURNITURE_CANDIDATE` — berpotensi menjadi bahan furnitur atau plastic lumber;
2. `ENERGY_RECOVERY_CANDIDATE` — berpotensi digunakan dalam pemulihan energi apabila memenuhi persyaratan fasilitas;
3. `OTHER_HANDLING_OR_RESIDUAL` — memerlukan daur ulang lain, pengomposan, penanganan khusus, atau pembuangan residu.

### 8.1 Matriks pemetaan awal

| Kelas material | Rekomendasi awal | Kondisi atau catatan |
| --- | --- | --- |
| `organic` | `OTHER_HANDLING_OR_RESIDUAL` | Lebih sesuai untuk pengomposan atau pengolahan biologis; bukan otomatis dibuang |
| `wood_vegetation` | `ENERGY_RECOVERY_CANDIDATE` | Hanya jika cukup kering dan diterima fasilitas |
| `paper_cardboard` | `ENERGY_RECOVERY_CANDIDATE` | Diprioritaskan untuk daur ulang jika bersih; energi dipertimbangkan jika tidak layak didaur ulang dan cukup kering |
| `rigid_plastic` | `FURNITURE_CANDIDATE` | Bergantung pada jenis polimer, kebersihan, dan spesifikasi fasilitas |
| `flexible_plastic` | `FURNITURE_CANDIDATE` atau `ENERGY_RECOVERY_CANDIDATE` | Bergantung pada teknologi furnitur dan fasilitas energi |
| `textile_rubber_leather` | `ENERGY_RECOVERY_CANDIDATE` | Hanya jika jenis material dan fasilitas mengizinkan |
| `metal` | `OTHER_HANDLING_OR_RESIDUAL` | Diarahkan ke daur ulang logam; bukan bahan bakar |
| `glass_ceramic` | `OTHER_HANDLING_OR_RESIDUAL` | Kaca dapat memiliki jalur daur ulang; bukan bahan bakar |
| `battery_electronic` | `OTHER_HANDLING_OR_RESIDUAL` | Wajib diperiksa manusia dan ditangani melalui jalur khusus |
| `mixed_residual` | `OTHER_HANDLING_OR_RESIDUAL` | Wajib diperiksa sebelum dinyatakan sebagai residu akhir |

Matriks ini merupakan aturan awal berbasis literatur, bukan prosedur resmi suatu fasilitas. Aturan perlu disesuaikan apabila tim memperoleh daftar bahan yang benar-benar diterima oleh fasilitas furnitur dan fasilitas energi yang menjadi inspirasi proyek.

## 9. Human-in-the-loop

### 9.1 Prinsip

AI digunakan untuk membantu manusia, bukan menggantikan keputusan manusia sepenuhnya. Model menangani kasus yang cukup jelas, sementara petugas meninjau kasus yang tidak pasti atau berisiko tinggi.

Status keputusan sistem terdiri dari:

- `AUTO_ACCEPTED`: confidence memenuhi threshold dan tidak terkena aturan risiko;
- `NEEDS_HUMAN_REVIEW`: confidence rendah, hasil ambigu, atau kelas memerlukan pemeriksaan khusus;
- `HUMAN_CONFIRMED`: petugas menyetujui prediksi model;
- `HUMAN_CORRECTED`: petugas mengganti kelas atau rekomendasi model.

`NEEDS_HUMAN_REVIEW` bukan kelas sampah ke-11. Status ini diberikan oleh lapisan pengambilan keputusan setelah model menghasilkan prediksi sepuluh kelas.

### 9.2 Aturan rujukan

Sebuah prediksi dirujuk kepada manusia jika salah satu kondisi berikut terpenuhi:

- confidence prediksi tertinggi berada di bawah threshold `tau`;
- selisih confidence antara dua kelas teratas terlalu kecil;
- model memprediksi `battery_electronic` atau `mixed_residual`;
- gambar buruk, objek tertutup, atau lebih dari satu objek dominan terlihat;
- objek berada di luar kelompok yang dikenal model;
- aturan keselamatan fasilitas mengharuskan pemeriksaan manusia.

Nilai `tau` tidak ditetapkan secara sembarangan. Beberapa threshold akan dibandingkan pada validation set untuk mencari keseimbangan antara cakupan otomatis, akurasi prediksi yang diterima, dan beban pemeriksaan manusia.

### 9.3 Alur keputusan

```text
Foto atau frame kamera
          |
          v
Klasifikasi 10 kelas + confidence score
          |
          v
Pemeriksaan threshold dan aturan risiko
          |
          +---- yakin dan aman ----> rekomendasi awal ----> AUTO_ACCEPTED
          |
          +---- ragu/berisiko -----> NEEDS_HUMAN_REVIEW
                                            |
                                            v
                                  petugas menentukan hasil
                                            |
                              +-------------+-------------+
                              |                           |
                              v                           v
                       HUMAN_CONFIRMED             HUMAN_CORRECTED
```

### 9.4 Umpan balik manusia

Keputusan manusia dapat dicatat sebagai data koreksi. Data tersebut tidak langsung dimasukkan kembali ke model. Data harus diperiksa kualitas labelnya terlebih dahulu sebelum digunakan dalam pelatihan ulang pada pengembangan berikutnya.

## 10. Konsep sistem

### 10.1 Masukan

- Foto yang diunggah pengguna; atau
- frame kamera dengan satu objek sampah dominan.

### 10.2 Pemrosesan

1. Gambar diambil dari foto atau kamera.
2. Gambar menjalani preprocessing yang sama seperti saat pelatihan.
3. Model menghasilkan skor untuk sepuluh kelas.
4. Sistem memilih kelas dengan skor tertinggi dan menampilkan confidence score.
5. Lapisan keputusan memeriksa threshold dan aturan risiko.
6. Sistem menampilkan rekomendasi awal atau meminta pemeriksaan manusia.

### 10.3 Keluaran

Contoh prediksi otomatis:

```text
Prediksi    : Rigid Plastic
Confidence  : 91%
Rekomendasi : Kandidat bahan furnitur
Status      : AUTO_ACCEPTED
```

Contoh prediksi yang membutuhkan manusia:

```text
Prediksi    : Flexible Plastic
Confidence  : 54%
Rekomendasi : Belum ditentukan
Status      : NEEDS_HUMAN_REVIEW
```

## 11. Rencana model dan eksperimen

### 11.1 Taksonomi Keluarga Arsitektur & Eksperimen Model
Untuk mendapatkan perbandingan komprehensif, evaluasi dilakukan dengan memilih minimal satu representasi dari 6 keluarga arsitektur berikut (total 6–10 model kandidat):

1. **Pure Scratch Baseline**: Custom CNN / ResNet-18 (from scratch, tanpa pretrain weights).
2. **Classic Deep CNN**: ResNet-50 / DenseNet-121 (Pretrained ImageNet).
3. **Lightweight Edge CNN**: MobileNetV3 / MobileNetV4 / EfficientNetV2-S.
4. **Modern CNN**: ConvNeXt-Tiny.
5. **Pure Vision Transformer**: Swin-T / ViT-Small.
6. **Self-Supervised Backbone**: DINOv2-Small (via `timm` / PyTorch Hub).

### 11.2 Protokol Eksperimen & Strategi Ensemble
1. **Auditing Data & Quality Check Awal (Pre-Screening)**:
   - Sebelum melatih seluruh variasi model, latih **1 model terbaik awal** (misal ConvNeXt-Tiny / ResNet-50) untuk melakukan *dataset error audit* (mendeteksi label noise, gambar korup, duplikasi yang lolos, atau *outlier visual* pada dataset).
   - Setelah dataset terverifikasi bersih, lanjutkan ke tahap benchmarking penuh.
2. **Benchmarking & Initial Training**:
   - Seluruh model (6–10 kandidat) dilatih selama **15 epoch per model** untuk mencari baseline perbandingan performa terbaik secara adil (*fair comparison*).
3. **Seleksi Model & Soft-Voting Ensemble**:
   - Pilih **3 model terbaik yang berasal dari keluarga arsitektur berbeda** (*cross-family diversity*, misal: 1 Modern CNN + 1 Vision Transformer + 1 Self-Supervised/Edge CNN).
   - Gabungkan probabilitas prediksi ketiganya menggunakan strategi **Soft Voting (Probability Averaging)** untuk meningkatkan generalisasi dan ketahanan prediksi pada skenario *Human-in-the-Loop*.

### 11.3 Pembagian data
Dataset dibagi secara terpisah menjadi:
- **Training set (70% - 10.500 gambar)**: untuk melatih bobot model (didukung augmentasi data).
- **Validation set (15% - 2.250 gambar)**: untuk pemilihan best checkpoint, hyperparameter tuning, dan kalibrasi confidence threshold $\tau$.
- **Test set (15% - 2.250 gambar)**: untuk evaluasi akhir independen (Akurasi, F1-Score, Confusion Matrix, dan Selective Accuracy).

Pemisahan telah di-cap seimbang sempurna 1.500 gambar per kelas (1:1) dan dialokasikan ke direktori `dataset_split/`.

### 11.4 Metrik klasifikasi

- Accuracy
- Precision per kelas dan macro average
- Recall per kelas dan macro average
- F1-score per kelas dan macro average
- Confusion matrix
- Waktu inferensi per gambar
- Ukuran model

Macro average diprioritaskan bersama metrik per kelas agar performa pada kelas berjumlah sedikit tidak tertutup oleh kelas yang dominan.

### 11.4 Metrik human-in-the-loop

- **Automatic coverage:** persentase sampel yang diterima tanpa pemeriksaan manusia.
- **Referral rate:** persentase sampel yang dirujuk kepada manusia.
- **Selective accuracy:** akurasi pada sampel yang diterima otomatis.
- **Error referral rate:** proporsi kesalahan model yang berhasil dialihkan ke pemeriksaan manusia.
- Perbandingan beberapa nilai confidence threshold.

Confidence dari softmax diperlakukan sebagai confidence score, bukan kepastian bahwa prediksi benar. Kalibrasi confidence dapat ditambahkan jika waktu dan data memungkinkan.

## 12. Kriteria dataset

Dataset publik akan dipilih setelah taksonomi kelas disepakati. Dataset yang digunakan sebaiknya:

- memiliki lisensi dan sumber yang jelas;
- menyediakan gambar yang cukup untuk setiap kelas;
- memiliki label yang dapat dipetakan ke sepuluh kelas proyek;
- tidak hanya berisi latar belakang studio yang terlalu bersih;
- memiliki variasi cahaya, sudut, bentuk, kondisi, dan latar;
- memungkinkan pemisahan training, validation, dan test tanpa kebocoran data;
- mendokumentasikan asal serta proses pelabelan data.

Jika tidak ada satu dataset yang mencakup seluruh kelas, beberapa dataset dapat digabungkan dengan hati-hati. Perbedaan latar atau gaya pengambilan gambar antardataset perlu diperiksa agar model tidak hanya belajar mengenali sumber dataset.

## 13. Skenario demonstrasi

1. Pengguna mengunggah foto atau mengarahkan satu objek ke kamera.
2. Sistem menampilkan kelas material dan confidence score.
3. Jika confidence memenuhi threshold, sistem menampilkan rekomendasi awal.
4. Jika confidence rendah atau kelas berisiko, sistem menampilkan permintaan pemeriksaan.
5. Petugas dapat menyetujui atau mengoreksi hasil.
6. Hasil keputusan ditampilkan dan, pada simulasi, dicatat sebagai riwayat.

Demonstrasi ini menunjukkan bagaimana AI dapat mendukung alur kerja. Demonstrasi bukan replika penuh fasilitas industri.

## 14. Kontribusi yang diharapkan

- Taksonomi sepuluh kelas material yang relevan dengan gagasan pengolahan sampah.
- Proof of concept klasifikasi citra sampah berbasis PyTorch.
- Lapisan rekomendasi yang memisahkan identifikasi material dari keputusan penanganan.
- Mekanisme human-in-the-loop berbasis confidence dan aturan risiko.
- Evaluasi statistik model dan analisis kesalahan per kelas.
- Materi edukasi yang menjelaskan peran AI secara mudah dipahami masyarakat.

## 15. Batasan dan risiko

- Jenis polimer plastik tidak selalu dapat dikenali hanya dari tampilan visual.
- Kondisi seperti kadar air, kandungan klorin, nilai kalor, dan kontaminasi kimia tidak dapat dipastikan melalui gambar biasa.
- Confidence tinggi tidak menjamin prediksi benar.
- Dataset publik mungkin tidak menggambarkan kondisi sampah Indonesia atau fasilitas nyata.
- Latar belakang gambar dapat menjadi shortcut yang dipelajari model.
- Beberapa kelas memiliki tampilan yang saling menyerupai atau terdiri dari campuran material.
- Pemetaan jalur penanganan berbeda antarwilayah dan fasilitas.
- Model klasifikasi satu objek tidak langsung sesuai untuk tumpukan sampah dengan banyak objek.
- Koreksi manusia juga dapat mengandung kesalahan dan tetap memerlukan kontrol kualitas.

## 16. Prinsip keselamatan dan komunikasi

- Sistem harus menyebut hasil sebagai rekomendasi awal, bukan keputusan final.
- Material berbahaya atau tidak dikenal harus diarahkan kepada manusia.
- Sistem tidak boleh mendorong masyarakat membakar sampah sendiri.
- Video harus menjelaskan bahwa pemulihan energi dilakukan oleh fasilitas yang memenuhi persyaratan lingkungan dan keselamatan.
- Hasil penelitian tidak boleh digunakan untuk mengklaim peningkatan efisiensi aktual tanpa pengujian lapangan.
- Bahasa yang disarankan adalah “berpotensi membantu” atau “dapat mendukung”, bukan “terbukti meningkatkan”.

## 17. Luaran proyek

### 17.1 Paper

Paper memuat:

1. latar belakang dan masalah pemilahan manual;
2. studi terkait klasifikasi sampah dan pengolahan sampah;
3. rancangan taksonomi dan alur human-in-the-loop;
4. dataset, preprocessing, model, dan desain eksperimen;
5. hasil statistik serta analisis kesalahan;
6. simulasi rekomendasi jalur penanganan;
7. keterbatasan dan peluang implementasi lanjutan.

### 17.2 Proof of concept

- Model PyTorch terlatih.
- Script atau notebook pelatihan dan evaluasi.
- Inferensi melalui foto dan, jika memungkinkan, kamera.
- Tampilan kelas, confidence, rekomendasi, dan status pemeriksaan manusia.

### 17.3 Video edukasi

Video menjelaskan:

- masalah sampah dan pemilahan manual;
- cara sederhana AI mengenali gambar;
- sepuluh kelas sampah yang digunakan;
- tiga kelompok rekomendasi penanganan;
- alasan manusia tetap terlibat;
- manfaat potensial dan keterbatasan sistem.

Pesan utama video:

> AI dapat membantu mengenali dan mengarahkan sampah secara lebih terstruktur, tetapi keputusan penting tetap membutuhkan manusia dan aturan fasilitas yang sesuai.

## 18. Tahapan pekerjaan

1. Memfinalkan definisi dan contoh setiap kelas.
2. Mencari serta mengaudit dataset publik.
3. Membuat aturan pemetaan kelas ke jalur rekomendasi.
4. Menyiapkan preprocessing dan pembagian data.
5. Melatih baseline dan model transfer learning.
6. Mengevaluasi performa serta pola kesalahan.
7. Memilih dan menguji confidence threshold.
8. Membuat simulasi human-in-the-loop.
9. Menyusun paper berdasarkan hasil eksperimen.
10. Membuat video edukasi berdasarkan gagasan dan hasil penelitian.

## 19. Pertanyaan terbuka

- Apa nama dan jenis teknologi fasilitas furnitur serta fasilitas energi yang menjadi inspirasi proyek?
- Material apa yang benar-benar diterima dan ditolak oleh masing-masing fasilitas?
- Apakah dataset publik dapat mendukung seluruh sepuluh kelas dengan label yang konsisten?
- Apakah kelas `textile_rubber_leather` perlu dipisah setelah dataset ditemukan?
- Apakah demonstrasi kamera hanya menangani satu objek atau perlu dikembangkan menjadi object detection multiobjek?
- Confidence threshold berapa yang memberikan keseimbangan terbaik antara automatic coverage dan selective accuracy?
- Apakah koreksi manusia hanya disimulasikan atau akan disimpan sebagai data untuk pengembangan berikutnya?

## 20. Referensi awal

1. Kementerian Lingkungan Hidup dan Kehutanan. [Sistem Informasi Pengelolaan Sampah Nasional: Komposisi Sampah](https://sipsn.menlhk.go.id/sipsn/public/data/komposisi).
2. Pemerintah Republik Indonesia. [Peraturan Presiden Nomor 109 Tahun 2025 tentang Penanganan Sampah Perkotaan Melalui Pengolahan Sampah Menjadi Energi Terbarukan Berbasis Teknologi Ramah Lingkungan](https://peraturan.bpk.go.id/Details/334718).
3. European Commission. [Refuse Derived Fuel, Current Practice and Perspectives](https://ec.europa.eu/environment/pdf/waste/studies/rdf.pdf).
4. ASTM International. [ASTM D7568: Standard Specification for Polyethylene-Based Structural-Grade Plastic Lumber for Outdoor Applications](https://store.astm.org/d7568-17.html).
5. United States Environmental Protection Agency. [Decision Maker's Guide to Recycling Plastics](https://nepis.epa.gov/Exe/ZyPURL.cgi?Dockey=50000PQU.TXT).
6. UCI Machine Learning Repository. [RealWaste Data Set](https://archive.ics.uci.edu/dataset/908/realwaste).
