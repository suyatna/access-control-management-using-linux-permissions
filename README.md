# Access control management using Linux permissions

## 📌 Table of contents

1. [Project overview](#overview)
2. [Scenario context](#scenario)
3. [Checking file and directory permissions](#checking)
4. [Understanding the permission string](#understanding)
5. [Modifying file permissions](#modify)
6. [Managing permissions on hidden files](#managing)
7. [Securing directory access](#securing)
8. [Final summary](#summary)

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

<img width="640" height="175" alt="Screenshot 2026-01-20 at 02-36-58 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/f1f98f2a-7b88-4ae7-ba5c-715638473ea9" />

Perintah tersebut menampilkan daftar file secara lengkap beserta informasi izin, kepemilikan pengguna, grup, dan tipe objek. Opsi `-l` digunakan untuk melihat detail izin secara jelas. Output perintah kemudian ditelusuri satu per satu untuk menemukan potensi risiko, seperti file yang memiliki akses tulis bagi group atau other. Jenis akses ini tidak sesuai dengan kebijakan organisasi karena dapat membuka peluang perubahan data tanpa otorisasi.

Temuan dari pemeriksaan ini menjadi dasar untuk langkah berikutnya, yaitu memahami struktur permission string dan menentukan file atau direktori yang perlu disesuaikan izinnya. Penyesuaian dilakukan agar akses hanya diberikan kepada pengguna yang benar-benar berwenang.

---

## 🧩 Understanding the permission string <a name="understanding">

Hasil dari perintah `ls -l` atau `ls -la` menampilkan izin akses dalam bentuk string permission. Setiap karakter di dalam string tersebut memiliki makna khusus yang menjelaskan bagaimana sebuah file atau direktori dapat diakses oleh pemilik, grup, maupun pengguna lain.

Format string perizinan ini mengikuti pola tertentu yang digunakan untuk membaca tipe objek serta hak baca, tulis, dan eksekusi. Pemahaman struktur ini menjadi dasar penting untuk menilai apakah izin yang diterapkan sudah aman atau masih membuka celah risiko keamanan.

<img width="440" height="113" alt="Untitled Diagram drawio" src="https://github.com/user-attachments/assets/60e7020c-1284-476e-bd26-31e326281e4f" />

### a. Permission structure and symbol meaning

Tabel berikut menjelaskan simbol yang muncul pada string permission serta fungsinya dalam sistem Linux. Setiap simbol membantu memahami tipe objek dan hak akses yang diberikan dan pemahaman simbol-simbol ini menjadi langkah awal untuk menilai apakah izin akses sudah sesuai dengan prinsip keamanan.

|Simbol|Kategori|Arti|Penjelasan|
|---|---|---|---|
|`d`|Tipe objek|Direktori|Menunjukkan bahwa objek tersebut merupakan sebuah direktori|
|`-`|Tipe objek|File biasa|Menandakan objek berupa file non-direktori|
|`r`|Hak akses|Read|Memberikan izin membaca isi file atau melihat daftar isi direktori|
|`w`|Hak akses|Write|Memberikan izin mengubah isi file atau menghapus file di dalam direktori|
|`x`|Hak akses|Execute|Memberikan izin menjalankan file atau masuk ke dalam direktori|
|`-`|Hak akses|No permission|Menunjukkan bahwa hak akses tertentu tidak diberikan|

### b. Permission details based on command output

Tabel berikut merangkum hasil pembacaan permission pada beberapa file beserta implikasi keamanannya. Pembacaan detail tersebut membantu menentukan file mana yang perlu diperketat izinnya supaya akses hanya diberikan kepada pihak yang benar-benar berwenang.

|Nama objek|Tipe|Permission string|Penjelasan|
|---|---|---|---|
|`project_k.txt`|File|`-rw-rw-rw-`|Seluruh pengguna memiliki izin baca dan tulis. Konfigurasi ini berisiko tinggi karena siapa pun dapat mengubah isi file tanpa pembatasan.|
|`project_m.txt`|File|`-rw-r-----`|Pemilik dapat membaca dan menulis, grup hanya dapat membaca, pengguna lain tidak memiliki akses. Pola ini sudah mendekati prinsip least privilege.|
|`project_r.txt`|File|`-rw-rw-r--`|Grup masih memiliki izin tulis, sementara pengguna lain dapat membaca. Kondisi ini membuka peluang perubahan data oleh pihak yang tidak seharusnya.|
|`project_t.txt`|File|`-rw-rw-r--`|Izin grup untuk menulis masih aktif dan menimbulkan risiko serupa, karena file tetap dapat dimodifikasi di luar kontrol pemilik.|

Hasil pemeriksaan menunjukkan adanya file dan direktori dengan izin akses yang terlalu terbuka, terutama karena hak tulis masih diberikan kepada group dan other. Kondisi ini menimbulkan risiko perubahan data oleh pihak yang tidak berwenang. Temuan tersebut menjadi acuan untuk tahap berikutnya, yaitu menyesuaikan izin menggunakan perintah Linux agar akses dibatasi hanya untuk pengguna yang memiliki otoritas sesuai kebijakan keamanan organisasi.

---

## 🛠️ Modifying file permissions <a name="modify">

Identifikasi file dengan izin yang tidak sesuai kebijakan keamanan menjadi titik awal untuk melakukan perbaikan akses. Fokus perbaikan diarahkan pada pembatasan izin agar hanya pengguna yang berwenang yang memiliki hak akses. Kebijakan organisasi menegaskan bahwa tidak satu pun file diperbolehkan memberikan akses tulis kepada group maupun other.

Hasil pemeriksaan sebelumnya menunjukkan beberapa file masih memiliki izin tulis untuk group atau other. File-file inilah yang kemudian diprioritaskan untuk penyesuaian izin agar selaras dengan prinsip keamanan dan kontrol akses yang diterapkan organisasi.

### a. Securing files with open access

File `project_k.txt` memiliki izin `-rw-rw-rw-`, yang berarti seluruh pengguna dapat membaca dan menulis file tersebut. Konfigurasi ini tergolong berisiko karena membuka peluang perubahan data tanpa kontrol yang jelas. Akses tulis untuk group dan other dihapus menggunakan perintah berikut:

<img width="640" height="186" alt="Screenshot 2026-01-20 at 02-39-00 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/77e53c0c-6659-4aa1-8072-ad541f660ede" />

Perubahan ini membuat hanya pemilik file yang memiliki hak tulis, sementara group dan other dibatasi pada akses baca.

### b. Restricting group write access

File `project_r.txt` dan `project_t.txt` memiliki izin `-rw-rw-r--`, yang masih memperbolehkan group untuk menulis. Izin ini tidak sejalan dengan kebijakan keamanan karena memungkinkan modifikasi file oleh anggota group selain pemilik. Hak tulis pada group dihapus dengan perintah berikut:

<img width="640" height="344" alt="Screenshot 2026-01-20 at 02-41-13 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/0b5e4589-b64b-4f3c-8b95-ea39fa877366" />

Penyesuaian ini memastikan hanya pemilik file yang dapat melakukan perubahan, sementara group dan other tetap dapat membaca sesuai kebutuhan operasional.

### c. Preserving properly configured files and permission hardening results

File `project_m.txt` sudah memiliki izin `-rw-r-----`, di mana hak tulis hanya dimiliki oleh pemilik dan group hanya diberikan akses baca. Konfigurasi ini sesuai dengan kebijakan keamanan organisasi sehingga tidak memerlukan perubahan lebih lanjut.

Semua penyesuaian izin telah diterapkan, tidak ada lagi file proyek yang memberikan hak tulis kepada group atau other. Kondisi ini menurunkan risiko perubahan data tanpa otorisasi dan memperkuat kontrol akses di sistem berkas. Langkah hardening ini juga menjadi dasar sebelum mengamankan file tersembunyi dan direktori yang lebih sensitif.

---

## 🕵️ Managing permissions on hidden files <a name="managing">

File tersembunyi biasanya tidak terlihat dalam tampilan standar direktori, sehingga sering dianggap aman. Risiko tetap ada jika izin aksesnya tidak diatur dengan benar. Pemeriksaan dan pengelolaan izin pada file tersembunyi menjadi bagian penting dari pengamanan sistem.

Pada direktori proyek ini, file tersembunyi `.project_x.txt` memiliki pengaturan izin sebagai berikut:

<img width="640" height="234" alt="Screenshot 2026-01-20 at 02-41-53 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/fa08433e-0cbb-4d32-976c-21cb027d5320" />

Konfigurasi ini menunjukkan pemilik file memiliki akses baca dan tulis, sedangkan group hanya memiliki hak tulis tanpa akses baca. Kondisi ini tidak sesuai kebijakan keamanan organisasi karena memungkinkan file diubah tanpa bisa dicek isinya, sehingga berpotensi menimbulkan manipulasi data.

### a. Hidden file permission adjustment

Kebijakan organisasi mengatur file ini supaya:
- Hanya pemilik yang memiliki izin tulis
- Pemilik dan group dapat membaca file
- Other tidak memiliki akses sama sekali

Penyesuaian dilakukan menggunakan perintah:

<img width="640" height="454" alt="Screenshot 2026-01-20 at 02-45-11 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/fd7c4c7a-bf1d-4f9b-b2a1-8746d5122ab1" />

Perintah ini menghapus hak tulis group dan memastikan group hanya memiliki akses baca. Hak akses pemilik tetap utuh sesuai kebijakan.

### b. Hidden file security results

Perubahan yang diterapkan membuat file tersembunyi hanya dapat:
- Dibaca dan diubah oleh pemilik
- Dibaca oleh group
- Tidak diakses oleh other

Konfigurasi ini menjaga integritas file dan memastikan hanya pengguna berwenang yang dapat mengakses isinya, meskipun file tersembunyi. Langkah ini melengkapi penguatan kontrol akses sebelumnya sebelum dilanjutkan ke pengamanan direktori yang membatasi akses ke folder-folder sensitif.

---

## 📁 Securing directory access <a name="securing">

Pengaturan izin pada direktori sama pentingnya dengan pengamanan file dalam menjaga keamanan sistem. Izin ini menentukan siapa yang bisa melihat isi folder sekaligus siapa yang bisa masuk dan mengakses file di dalamnya. Kesalahan konfigurasi bisa membuka akses tidak sah ke seluruh struktur direktori. Direktori proyek bernama drafts digunakan untuk menyimpan dokumen kerja yang sensitif dan hanya boleh diakses oleh pemiliknya, yaitu pengguna `researcher2`.

Izin awal pada direktori ini adalah:

<img width="640" height="234" alt="Screenshot 2026-01-20 at 02-41-53 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/da6bab45-9cbd-40af-bce2-d764b7fd87b8" />

Pengaturan ini memberi izin kepada group untuk masuk ke direktori, tetapi mereka tidak bisa melihat isi atau mengubah file di dalamnya. Akses terbatas seperti ini masih belum sesuai dengan kebijakan organisasi, karena hanya pemilik yang seharusnya bisa mengakses direktori beserta isinya.

### a. Restricting directory access

Hanya pengguna `researcher2` yang seharusnya dapat mengakses direktori `drafts`. Izin execute untuk group dan other dihapus agar mereka tidak bisa masuk ke folder. Perintah yang digunakan untuk ini adalah:

<img width="640" height="245" alt="Screenshot 2026-01-20 at 02-48-24 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/b652d89b-1ef4-4cca-beb0-32d03b854ebd" />

### b. Directory hardening results

Direktori `drafts` kini hanya dapat diakses oleh pemilik, sedangkan group dan other tidak memiliki izin masuk atau melihat isinya. Konfigurasi ini melindungi dokumen sensitif dari akses tidak sah dan memperkuat kontrol akses berbasis peran di sistem. Langkah pengamanan direktori ini melengkapi pengaturan izin pada file dan file tersembunyi, sehingga seluruh struktur proyek kini sesuai dengan kebijakan keamanan organisasi.

---

## 🏁 Final summary <a name="summary">

Proyek ini mendokumentasikan pengalaman mengelola dan mengamankan izin file serta direktori menggunakan perintah Linux sebagai bagian dari praktik access control. Skenario yang digunakan menggambarkan lingkungan organisasi besar dengan tim riset internal, di mana saya meninjau izin yang sudah ada, menemukan akses yang tidak semestinya, lalu melakukan penyesuaian supaya pengguna berwenang hanya yang dapat mengakses sistem.

Proses berjalan dengan memeriksa file biasa, file tersembunyi, dan direktori yang bersifat sensitif agar selaras dengan kebijakan keamanan organisasi. Setiap perubahan dilakukan secara hati-hati melalui perintah Linux seperti `ls -l`, `ls -la`, dan `chmod`. Tujuannya jelas, yaitu menghapus akses tulis yang tidak sah dan membatasi akses direktori hanya untuk pemilik yang memiliki otoritas.

Hasil akhir menunjukkan bahwa kontrol akses berbasis permission file dapat diterapkan secara efektif untuk melindungi data sensitif dan mencegah penyalahgunaan akses. Pendekatan ini membantu memperkuat keamanan sistem secara menyeluruh. Proyek ini juga menegaskan pentingnya memahami permission string Linux agar praktik keamanan dapat dijalankan secara konsisten dan berkelanjutan.
