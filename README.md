# Craftland Photo Generator

Alat web satu file (HTML) yang mengubah foto menjadi gambar dari **kotak, lingkaran, dan segitiga** untuk Craftland. Hasilnya berupa teks padat yang dibagi per **200 karakter**, jadi bisa ditempel baris demi baris ke Craftland dan dibaca ulang oleh logika di dalam game.

Semuanya berjalan di browser. Foto tidak diunggah ke mana pun.

## Kenapa dibuat begini

### Kenapa memakai bentuk, bukan piksel?

Cara paling sederhana mengubah foto menjadi gambar adalah satu geometri untuk satu piksel. Masalahnya, Craftland membatasi jumlah geometri **maksimal 5000**. Dengan batas itu, gambar piksel hanya bisa sekitar 70 × 70 piksel (70 × 70 = 4900), terlalu kasar untuk dikenali.

Dengan kotak, lingkaran, dan segitiga yang bebas diatur posisi, ukuran, rotasi, dan warnanya, jatah geometri dipakai lebih hemat. Satu bentuk besar bisa menutupi area luas yang warnanya mirip, dan bentuk kecil hanya dipakai di bagian yang butuh detail. Hasilnya jauh lebih jelas untuk jumlah geometri yang sama. Slider dibatasi sampai 2500 bentuk, masih di bawah batas 5000.

### Kenapa datanya dipadatkan?

Teks hanya bisa dimasukkan ke Craftland **per 200 karakter**, jadi seluruh data harus ditempel baris demi baris. Makin panjang datanya, makin banyak baris yang harus disalin dan ditempel satu per satu, dan makin melelahkan.

Karena itu data tiap bentuk dipadatkan sekecil mungkin. Formatnya berkembang dari 17 karakter per bentuk (basis 36) menjadi **7 karakter** (satu karakter Unicode per angka, alfabet 1449 karakter, tipe dan rotasi digabung, warna 2 karakter):

| Jumlah bentuk | 17 karakter per bentuk | 7 karakter per bentuk |
|---|---|---|
| 300 (bawaan) | 5.100 karakter, 26 baris | 2.100 karakter, 11 baris |
| 2500 | 42.500 karakter, 213 baris | 17.500 karakter, 88 baris |

Imbal baliknya, alfabet 1449 karakter itu memang lebih berat: tabel di Craftland harus memuat 1449 entri, dan setiap karakternya harus sudah teruji diterima kolom input Craftland (itulah alasan ada daftar uji karakter di repo ini). Tetapi biaya itu hanya sekali di awal, sedangkan penghematannya terasa setiap kali menyalin hasil, jadi kompromi ini sepadan.

## Latar belakang

### Kenapa tidak memakai metode piksel?

Cara paling sederhana menampilkan gambar adalah satu geometri per piksel. Tetapi Craftland membatasi jumlah geometri maksimal **5000**. Dengan batas itu, gambar piksel hanya bisa sekitar 70 × 70 piksel (70 × 70 = 4900), terlalu kasar untuk dikenali.

Dengan bentuk bebas (kotak, lingkaran, segitiga) yang ukuran, posisi, rotasi, dan warnanya diatur satu per satu, jumlah geometri yang sama bisa dipakai jauh lebih efisien. Bentuk besar menutup area luas, lalu bentuk kecil ditambahkan di bagian yang butuh detail.

### Kenapa datanya dipadatkan?

Kolom input teks di Craftland hanya menerima **200 karakter sekali tempel**. Makin panjang hasilnya, makin banyak kali salin-tempel, dan itu melelahkan. Karena itu tiap bentuk dipadatkan sampai hanya **7 karakter**. Perbandingan untuk 2500 bentuk:

| Format | Karakter per bentuk | Total karakter | Jumlah baris (200 karakter) |
|---|---|---|---|
| Versi awal (Base36) | 17 | 42.500 | 213 |
| Sekarang (alfabet 1449) | 7 | 17.500 | 88 |

Harganya, alfabet 1449 karakter Unicode lebih berat: tabelnya lebih besar di Craftland dan tiap karakter harus dicari nomor urutnya. Tetapi itu sepadan, karena bagian yang paling merepotkan adalah salin-tempel, bukan pembacaannya.

## Fitur

- Memecah foto menjadi 20 sampai 2500 bentuk (bawaan 300).
- Pilihan bentuk: kotak, lingkaran, segitiga (boleh dipilih sebagian).
- Pemotong gambar (crop) dengan rasio bebas, 1:1, 4:3, 3:4, dan 16:9.
- Tiga tingkat ketelitian: Cepat, Seimbang (bawaan), Teliti.
- Dua tahap proses: membuat bentuk, lalu mengganti bentuk yang tidak terlihat.
- Pratinjau hasil langsung di kanvas 1294 × 1294.
- Tombol salin per baris dan salin semuanya.
- Pemeriksaan otomatis: hasil di-decode lagi dan dibandingkan dengan aslinya sebelum bisa disalin.

## Cara pakai

1. Buka `index.html` di browser.
2. Pilih foto, lalu potong bagian yang diinginkan (opsional).
3. Atur jumlah bentuk, jenis bentuk, dan ketelitian, lalu tekan **Mulai**.
4. Tunggu sampai tahap 1 dan tahap 2 selesai. Tombol salin aktif setelah proses selesai.
5. Tekan **Salin baris berikutnya** dan tempel ke Craftland **berurutan dari baris 1**, tanpa spasi atau enter. Atau pakai **Salin semuanya**.

Semakin banyak bentuk dan semakin tinggi ketelitian, semakin rapi hasilnya, tetapi semakin lama prosesnya dan semakin panjang teksnya. Perkiraan waktu tampil di halaman.

## Format data

Setiap bentuk dikodekan menjadi **7 karakter**. Setiap karakter adalah satu angka, yaitu nomor urutnya di alfabet **1449 karakter Unicode** (nomor mulai dari 0).

| Posisi | Isi | Cara baca |
|---|---|---|
| 1 | Tipe + rotasi | `n = tipe × 360 + rotasi`. Tipe: 0 kotak, 1 segitiga, 2 lingkaran. Rotasi 0 sampai 359 |
| 2 | Warna, bagian 1 | dipakai di rumus warna di bawah |
| 3 | Warna, bagian 2 | dipakai di rumus warna di bawah |
| 4 | X | nomor urut − 647 |
| 5 | Y | nomor urut − 647 |
| 6 | Lebar (W) | nomor urut, 3 sampai 1294 |
| 7 | Tinggi (H) | nomor urut, 3 sampai 1294 |

Titik (0, 0) ada di tengah kanvas 1294 × 1294. X positif ke kanan dan Y positif ke atas. Bentuk pertama adalah latar yang menutupi seluruh gambar dengan warna rata-rata foto.

### Cara membaca (pseudokode)

```
n     = nomor urut karakter 1
tipe  = floor(n / 360)
rotasi = n mod 360

hasil = nomor urut karakter 2 × 1449 + nomor urut karakter 3
r = (floor(hasil / 128 / 128) mod 128) × 2
g = (floor(hasil / 128) mod 128) × 2
b = (hasil mod 128) × 2

x = nomor urut karakter 4 − 647
y = nomor urut karakter 5 − 647
w = nomor urut karakter 6
h = nomor urut karakter 7
```

Pengali di rumus `hasil` harus **1449**, sama dengan ukuran alfabet. Kalau dipakai angka lain, pasangan karakter berbeda bisa menghasilkan warna yang sama.

Warna disimpan dalam 128 level per kanal (kelipatan 2, 0 sampai 254). Generator sudah membulatkan warna ke level ini saat mencari bentuk, jadi pratinjau di layar sama persis dengan hasil yang dibaca Craftland.

Arah rotasi mengikuti rumus di kode. Kalau di Craftland terlihat terbalik, ubah tanda rotasi saat membaca.

### Cara Craftland membacanya

Di Craftland, semua baris yang sudah ditempel disambung menjadi satu string panjang. Karena satu baris 200 karakter tidak selalu pas kelipatan 7, sebuah bentuk bisa terpotong di ujung baris. Jadi sambungkan dulu semuanya, baru dibaca per 7 karakter.

Logika di Craftland lalu melakukan loop:

```
untuk i dari 0 sampai (panjang data / 7) - 1:
    potongan = 7 karakter mulai dari posisi i x 7
    cari nomor urut tiap karakter di alfabet
    decode: tipe, rotasi, r, g, b, x, y, w, h   (pseudokode di atas)
    buat geometri sesuai tipe, di posisi (x, y), berukuran w x h,
        berotasi dan berwarna sesuai data
```

Satu putaran loop menghasilkan satu geometri, dan loop berhenti saat data habis. Urutan pembuatan sama dengan urutan data: bentuk pertama adalah latar, bentuk sesudahnya menumpuk di atasnya. Pastikan di Craftland urutan tumpukannya mengikuti urutan pembuatan, kalau tidak gambar bisa tampak berbeda dari pratinjau.

### Alfabet

Alfabet ada di konstanta `AL` di dalam `index.html`, dan salinannya per 200 karakter ada di `craftland_alfabet_1449.txt` (baris 1 sampai 8, indeks 0 sampai 1448). Urutannya tetap dan tidak boleh diubah, karena nomor urut karakter adalah datanya. Bila tabel di Craftland berbeda dari alfabet ini, semua hasil akan terbaca salah.

Karakter yang dipilih hanya yang berada di BMP (satu unit per karakter), tanpa spasi, karakter kontrol, tanda gabung, dan karakter yang berubah kalau dinormalisasi.

> **Status uji:** 1400 karakter pertama alfabet (baris 1 sampai 7) sudah lolos di Craftland. 49 karakter terakhir (baris 8) belum dikonfirmasi.

## Cara Craftland membaca data

1. Semua baris ditempel **berurutan** dan digabung menjadi satu teks.
2. Craftland melakukan loop pada teks itu, **7 karakter sekali jalan**.
3. Dari tiap 7 karakter, Craftland menghitung data bentuk memakai rumus di atas, lalu membuat **satu geometri** dari data itu.

```
untuk i dari 0 sampai panjang teks, tambah 7 setiap putaran:
    ambil 7 karakter mulai dari i
    cari nomor urut tiap karakter di alfabet
    hitung tipe, rotasi, warna (r, g, b), x, y, lebar, tinggi
    buat 1 geometry:
        bentuk   = tipe
        posisi   = (x, y)
        ukuran   = (lebar, tinggi)
        rotasi   = rotasi
        warna    = (r, g, b)
```

Beberapa hal yang perlu diperhatikan:

- Jumlah bentuk = panjang teks ÷ 7. Panjang teks harus kelipatan 7.
- Satu baris berisi 200 karakter dan 200 bukan kelipatan 7, jadi satu bentuk bisa terbelah di antara dua baris. Itu tidak masalah selama semua baris ditempel berurutan dan dibaca setelah digabung.
- Bentuk pertama adalah latar yang menutupi seluruh gambar. Bentuk dibuat sesuai urutan data, dan generator mengasumsikan bentuk yang dibuat belakangan berada di lapisan paling atas.

## Cara kerja

Pendekatan serakah (*greedy*) dengan perbaikan lokal (*hill climbing*):

1. Latar dibuat dengan warna rata-rata foto.
2. Tiap langkah mencoba banyak bentuk acak (tipe, posisi, ukuran, rotasi). Warna tiap kandidat dihitung dari rata-rata area foto yang tertutup, lalu dibulatkan ke 128 level.
3. Kandidat terbaik diperbaiki dengan menggeser posisi, ukuran, dan rotasinya sedikit demi sedikit selama selisih ke foto asli terus mengecil.
4. Bentuk terbaik ditambahkan di lapisan paling atas. Ukuran bentuk makin kecil seiring bertambahnya langkah, dari bentuk besar ke detail halus.
5. Pada tahap 2, bentuk yang hampir seluruhnya tertutup bentuk di atasnya (sisa terlihat tiga piksel grid atau kurang) ditandai. Pengganti dibuat di bagian yang masih paling jauh dari foto asli. Bentuk lama baru dibuang di akhir supaya daftar salin tidak menyusut di tengah proses. Pengecekan diulang maksimal 6 putaran.

Ketelitian mengatur seberapa teliti pencarian di tiap langkah:

| Pilihan | Kandidat acak | Diperbaiki | Usaha perbaikan |
|---|---|---|---|
| Cepat | 80 | 6 | 60 |
| Seimbang | 160 | 8 | 110 |
| Teliti | 300 | 10 | 180 |

## Batasan

- Tiap bentuk hanya satu warna rata, jadi foto yang halus tetap tampak bergaya poligon.
- Perhitungan memakai grid 647 × 647 (setengah resolusi kanvas), sehingga detail yang sangat halus sulit tertangkap.
- Pengecekan bentuk tersembunyi juga memakai grid itu, jadi bentuk yang hanya menyisakan garis lebih tipis dari satu piksel grid bisa dianggap tersembunyi.
- Kadang pencarian tidak menemukan pengganti yang cocok pada tahap 2, sehingga jumlah bentuk akhir bisa sedikit di bawah jumlah yang diminta.

## Kebutuhan

- Browser modern (memakai selector CSS `:has()`: Chrome 105+, Safari 15.4+, Firefox 121+).
- Koneksi internet hanya untuk font Google Fonts. Tanpa internet, tampilan memakai font bawaan perangkat.

## Struktur repo

```
.
├── index.html                      # aplikasinya (satu file)
├── craftland_alfabet_1449.txt      # alfabet 1449 karakter, per 200 karakter
└── README.md
```

## Menjalankan lewat GitHub Pages

1. Pastikan file aplikasinya bernama `index.html` di akar repo.
2. Buka **Settings → Pages**.
3. Pada **Build and deployment**, pilih **Deploy from a branch**, lalu pilih branch `main` dan folder `/ (root)`.
4. Simpan. Alamat situs akan muncul di halaman yang sama, biasanya `https://<username>.github.io/<nama-repo>/`.

## Pemeriksaan internal

Saat halaman dibuka, skrip menjalankan pengujian format (ribuan pemeriksaan: bolak-balik semua nomor urut alfabet, nilai ekstrem, penolakan data tidak valid, dan 500 objek acak). Peringatan hanya muncul di halaman kalau ada yang gagal.
