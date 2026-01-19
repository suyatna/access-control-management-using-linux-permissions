# Access control management using Linux permissions

## 📌 Table of contents

1. [Project overview](#overview)
2. [Scenario context](#scenario)
3. [Checking file and directory permissions](#checking)
4. [Understanding the permission string](#understanding)
5. [Modifying file permissions](#modify)
6. [Managing permissions on hidden files](#managing)
7. [Securing directory access](#securing)
8. [Conclusion](#conclusion)

---

## 🧠 Project overview <a name="overview">

Studi ini disusun sebagai bagian dari portofolio cybersecurity dengan fokus pada pengelolaan akses dan izin file di sistem Linux. Skenario yang digunakan menggambarkan peran seorang profesional keamanan yang memastikan setiap pengguna hanya memiliki hak akses yang memang dibutuhkan. Pendekatan ini membantu mencegah pemberian izin berlebih yang berpotensi menimbulkan risiko. Proses pengecekan, analisis, hingga perbaikan izin file dan direktori dilakukan secara langsung melalui perintah Linux.

Pembahasan berdasarkan materi dari program Google Cybersecurity Professional Certificate, khususnya course Tools of the Trade: Linux and SQL. Fokus diarahkan pada penggunaan command line untuk membaca permission file, memahami struktur izin, dan menerapkan perubahan sesuai prinsip keamanan. Portofolio ini menunjukkan kemampuan teknis dalam mengelola access control di Linux sekaligus menyajikan dokumentasi yang relevan dan mudah dipahami untuk kebutuhan profesional.

---

## 📝 Scenario context <a name="scenario">

Skenario ini menempatkan saya sebagai profesional keamanan di sebuah organisasi berskala besar yang mendukung aktivitas tim riset. Lingkungan kerja tersebut menyimpan banyak file dan direktori penting berisi data internal. Pengaturan hak akses menjadi bagian penting untuk menjaga sistem tetap aman dan tertata.

Peran utama saya berfokus pada peninjauan izin file dan direktori yang sudah ada. Setiap akses diperiksa agar sesuai dengan otoritas pengguna. Perhatian khusus diberikan pada izin yang terlalu longgar, akses tulis yang tidak semestinya, serta risiko keamanan akibat konfigurasi perizinan yang keliru.

Hasil pemeriksaan digunakan sebagai dasar untuk melakukan penyesuaian izin melalui perintah Linux. Akses yang tidak sah dihapus dan kepemilikan file dibatasi hanya untuk pengguna yang berwenang. Pendekatan ini membantu menjaga kerahasiaan data, mencegah perubahan tanpa izin, dan memperkuat kontrol akses pada sistem berbasis Linux.

---

## 🔍 Checking file and directory permissions <a name="checking">

Proses pengamanan sistem dimulai dengan meninjau izin yang diterapkan pada setiap file dan direktori di dalam folder proyek. Langkah ini membantu memahami siapa saja yang memiliki akses serta menilai apakah pengaturannya sudah sesuai dengan kebijakan keamanan organisasi.

Seluruh file dan direktori, termasuk file tersembunyi, ditampilkan menggunakan perintah Linux berikut:

<img width="640" height="165" alt="Screenshot 2026-01-19 at 01-57-49 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/ae5acb43-aa63-4a72-91a2-0788de81e2a0" />

Perintah tersebut menampilkan daftar file secara lengkap beserta informasi izin, kepemilikan pengguna, grup, dan tipe objek. Opsi -l digunakan untuk melihat detail izin secara jelas. Output perintah kemudian ditelusuri satu per satu untuk menemukan potensi risiko, seperti file yang memiliki akses tulis bagi group atau other. Jenis akses ini tidak sesuai dengan kebijakan organisasi karena dapat membuka peluang perubahan data tanpa otorisasi.

Temuan dari pemeriksaan ini menjadi dasar untuk langkah berikutnya, yaitu memahami struktur permission string dan menentukan file atau direktori yang perlu disesuaikan izinnya. Penyesuaian dilakukan agar akses hanya diberikan kepada pengguna yang benar-benar berwenang.

---

## 🧩 Understanding the permission string <a name="understanding">

Hasil dari perintah ls -l atau ls -la menampilkan izin akses dalam bentuk string permission. Setiap karakter di dalam string tersebut memiliki makna khusus yang menjelaskan bagaimana sebuah file atau direktori dapat diakses oleh pemilik, grup, maupun pengguna lain.

Format string perizinan ini mengikuti pola tertentu yang digunakan untuk membaca tipe objek serta hak baca, tulis, dan eksekusi. Pemahaman struktur ini menjadi dasar penting untuk menilai apakah izin yang diterapkan sudah aman atau masih membuka celah risiko keamanan.

<img width="584" height="319" alt="Frame 1(2)" src="https://github.com/user-attachments/assets/4d0c51e6-e575-4053-815a-d99444d314d9" />

### a. Permission structure and symbol meaning

Tabel berikut menjelaskan simbol yang muncul pada string permission serta fungsinya dalam sistem Linux. Setiap simbol membantu memahami tipe objek dan hak akses yang diberikan.

|Simbol|Kategori|Arti|Penjelasan|
|---|---|---|---|
|d|Tipe objek|Direktori|Menunjukkan bahwa objek tersebut merupakan sebuah direktori|
|-|Tipe objek|File biasa|Menandakan objek berupa file non-direktori|
|r|Hak akses|Read|Memberikan izin membaca isi file atau melihat daftar isi direktori|
|w|Hak akses|Write|Memberikan izin mengubah isi file atau menghapus file di dalam direktori|
|x|Hak akses|Execute|Memberikan izin menjalankan file atau masuk ke dalam direktori|
|-|Hak akses|No permission|Menunjukkan bahwa hak akses tertentu tidak diberikan|

Pemahaman simbol-simbol ini menjadi langkah awal untuk menilai apakah izin akses sudah sesuai dengan prinsip keamanan.

### b. Permission details based on command output

Tabel berikut merangkum hasil pembacaan permission pada beberapa file beserta implikasi keamanannya.

|Nama objek|Tipe|Permission string|Penjelasan|
|---|---|---|---|
|project_k.txt|File|-rw-rw-rw-|Seluruh pengguna memiliki izin baca dan tulis. Konfigurasi ini berisiko tinggi karena siapa pun dapat mengubah isi file tanpa pembatasan.|
|project_m.txt|File|-rw-r-----|Pemilik dapat membaca dan menulis, grup hanya dapat membaca, pengguna lain tidak memiliki akses. Pola ini sudah mendekati prinsip least privilege.|
|project_r.txt|File|-rw-rw-r--|Grup masih memiliki izin tulis, sementara pengguna lain dapat membaca. Kondisi ini membuka peluang perubahan data oleh pihak yang tidak seharusnya.|
|project_t.txt|File|-rw-rw-r--|Izin grup untuk menulis masih aktif dan menimbulkan risiko serupa, karena file tetap dapat dimodifikasi di luar kontrol pemilik.|

Pembacaan detail ini membantu menentukan file mana yang perlu diperketat izinnya agar akses hanya diberikan kepada pihak yang benar-benar berwenang.


