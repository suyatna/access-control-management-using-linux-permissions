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

Studi kasus ini dibuat sebagai bagian portofolio cybersecurity dengan fokus pada pengelolaan akses dan izin file di Linux. Skenario menampilkan peran seorang profesional keamanan yang memastikan setiap pengguna hanya punya hak akses yang diperlukan. Pendekatan ini membantu mencegah pemberian izin berlebih yang bisa menimbulkan risiko. Proses pengecekan, analisis, dan perbaikan izin file serta direktori dilakukan langsung lewat command line Linux.

Pembahasan mengacu pada materi Google Cybersecurity Professional Certificate, khususnya course Tools of the Trade: Linux and SQL. Fokus pada penggunaan command line untuk membaca permission file, memahami struktur izin, dan menerapkan perubahan sesuai prinsip keamanan. Portofolio ini menunjukkan kemampuan teknis mengelola access control di Linux sekaligus menyajikan dokumentasi yang jelas dan mudah dipahami untuk kebutuhan profesional.

---

## 📝 Scenario context <a name="scenario">

Skenario ini menempatkan saya sebagai profesional keamanan di sebuah organisasi besar yang mendukung tim riset. Lingkungan kerja menyimpan banyak file dan direktori penting berisi data internal. Pengaturan hak akses menjadi kunci untuk menjaga sistem tetap aman dan teratur.

Peran saya fokus meninjau izin file dan direktori yang ada. Setiap akses dicek supaya sesuai dengan otoritas pengguna. Perhatian khusus diberikan pada izin yang terlalu longgar, akses tulis yang tidak semestinya, dan risiko keamanan akibat konfigurasi izin yang salah.

Hasil pemeriksaan menjadi dasar untuk menyesuaikan izin lewat perintah Linux. Akses yang tidak sah dihapus, dan kepemilikan file dibatasi hanya untuk pengguna yang berwenang. Pendekatan ini membantu menjaga kerahasiaan data, mencegah perubahan tanpa izin, dan memperkuat kontrol akses di sistem Linux.

---

## 🔍 Checking file and directory permissions <a name="checking">

Proses pengamanan sistem dimulai dengan meninjau izin pada setiap file dan direktori di folder proyek. Langkah ini membantu melihat siapa yang punya akses dan menilai apakah pengaturannya sudah sesuai kebijakan keamanan organisasi.

Seluruh file, termasuk yang tersembunyi, ditampilkan menggunakan perintah Linux berikut:

<img width="640" height="175" alt="Screenshot 2026-01-20 at 02-36-58 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/f1f98f2a-7b88-4ae7-ba5c-715638473ea9" />

Perintah menampilkan daftar file lengkap beserta informasi izin, pemilik, grup, dan tipe objek. Opsi `ls -l` dipakai untuk melihat detail izin dengan jelas. Output diperiksa satu per satu untuk menemukan potensi risiko, misalnya file yang memberi akses tulis untuk group atau other. Akses ini melanggar kebijakan organisasi karena memungkinkan perubahan data tanpa otorisasi.

Temuan dari pemeriksaan menjadi dasar untuk memahami struktur permission string dan menentukan file atau direktori yang perlu disesuaikan izinnya. Penyesuaian dilakukan supaya akses hanya diberikan kepada pengguna yang benar-benar berwenang.

---

## 🧩 Understanding the permission string <a name="understanding">

Hasil perintah `ls -l` atau `ls -la` menampilkan izin akses dalam bentuk string permission. Setiap karakter punya arti khusus yang menjelaskan bagaimana file atau direktori bisa diakses oleh pemilik, grup, atau pengguna lain.

Format string ini mengikuti pola tertentu untuk membaca tipe objek dan hak baca, tulis, serta eksekusi. Memahami struktur ini penting untuk menilai apakah izin yang diterapkan aman atau masih meninggalkan celah risiko.

<img width="440" height="113" alt="Untitled Diagram drawio" src="https://github.com/user-attachments/assets/60e7020c-1284-476e-bd26-31e326281e4f" />

### a. Permission structure and symbol meaning

Tabel berikut menjelaskan simbol di string permission dan fungsinya di Linux. Simbol ini membantu memahami tipe objek dan hak akses, jadi bisa menilai apakah izin sudah sesuai prinsip keamanan.

| Simbol | Kategori   | Arti          | Penjelasan                                             |
| ------ | ---------- | ------------- | ------------------------------------------------------ |
| `d`    | Tipe objek | Direktori     | Menandakan objek adalah direktori                      |
| `-`    | Tipe objek | File biasa    | Menandakan file non-direktori                          |
| `r`    | Hak akses  | Read          | Izin membaca isi file atau daftar isi direktori        |
| `w`    | Hak akses  | Write         | Izin mengubah file atau menghapus file dalam direktori |
| `x`    | Hak akses  | Execute       | Izin menjalankan file atau masuk ke direktori          |
| `-`    | Hak akses  | No permission | Hak akses tertentu tidak diberikan                     |

### b. Permission details based on command output

Tabel berikut merangkum pembacaan permission beberapa file dan implikasi keamanannya. Detail ini membantu menentukan file mana yang perlu diperketat izinnya supaya hanya pihak berwenang yang bisa mengakses.

| Nama objek      | Tipe | Permission string | Penjelasan                                                                                                        |
| --------------- | ---- | ----------------- | ----------------------------------------------------------------------------------------------------------------- |
| `project_k.txt` | File | `-rw-rw-rw-`      | Semua pengguna bisa baca dan tulis. Risiko tinggi karena siapa pun bisa mengubah isi file.                        |
| `project_m.txt` | File | `-rw-r-----`      | Pemilik bisa baca dan tulis, grup hanya baca, pengguna lain tidak akses. Sudah mendekati prinsip least privilege. |
| `project_r.txt` | File | `-rw-rw-r--`      | Grup masih bisa tulis, pengguna lain bisa baca. Ada risiko data diubah oleh pihak yang tidak seharusnya.          |
| `project_t.txt` | File | `-rw-rw-r--`      | Izin tulis untuk grup masih aktif. File tetap bisa diubah di luar kontrol pemilik.                                |

Pemeriksaan menunjukkan beberapa file dan direktori punya izin terlalu terbuka, khususnya hak tulis untuk grup dan other. Kondisi ini berisiko ada perubahan data oleh pihak tidak berwenang. Temuan ini jadi dasar untuk menyesuaikan izin menggunakan perintah Linux, supaya akses hanya diberikan ke pengguna yang punya otoritas sesuai kebijakan keamanan organisasi.

---

## 🛠️ Modifying file permissions <a name="modify">

Identifikasi file dengan izin yang tidak sesuai kebijakan keamanan menjadi titik awal untuk memperbaiki akses. Fokus perbaikan diarahkan supaya hanya pengguna berwenang yang punya hak akses. Kebijakan organisasi menegaskan tidak satu pun file boleh memberi hak tulis ke group atau other.

Pemeriksaan sebelumnya menunjukkan beberapa file masih punya izin tulis untuk group atau other. File-file ini diprioritaskan untuk penyesuaian agar sesuai prinsip keamanan dan kontrol akses organisasi.

### a. Securing files with open access

File `project_k.txt` punya izin `-rw-rw-rw-`, artinya semua pengguna bisa baca dan tulis file. Konfigurasi ini berisiko karena siapa pun bisa mengubah isi file tanpa kontrol. Akses tulis untuk group dan other dihapus dengan perintah Linux:

<img width="640" height="186" alt="Screenshot 2026-01-20 at 02-39-00 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/77e53c0c-6659-4aa1-8072-ad541f660ede" />

Perubahan ini membuat hanya pemilik file yang bisa menulis, sementara group dan other hanya bisa membaca.

### b. Restricting group write access

File `project_r.txt` dan `project_t.txt` punya izin `-rw-rw-r--`, masih memberi group hak tulis. Kondisi ini bertentangan dengan kebijakan karena memungkinkan anggota group mengubah file selain pemilik. Hak tulis group dihapus dengan perintah berikut:

<img width="640" height="344" alt="Screenshot 2026-01-20 at 02-41-13 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/0b5e4589-b64b-4f3c-8b95-ea39fa877366" />

Penyesuaian ini memastikan hanya pemilik file yang bisa mengubah, group dan other tetap bisa baca sesuai kebutuhan.

### c. Preserving properly configured files and permission hardening results

File `project_m.txt` sudah punya izin `-rw-r-----`, tulis hanya untuk pemilik, grup hanya bisa baca. Konfigurasi ini sesuai kebijakan organisasi dan tidak perlu diubah.

Semua penyesuaian izin sudah diterapkan. Tidak ada file proyek yang memberi hak tulis ke group atau other. Kondisi ini menurunkan risiko perubahan data tanpa otorisasi dan memperkuat kontrol akses. Langkah hardening ini jadi dasar sebelum mengamankan file tersembunyi dan direktori sensitif.

---

## 🕵️ Managing permissions on hidden files <a name="managing">

File tersembunyi biasanya tidak terlihat di tampilan standar direktori, jadi sering dianggap aman. Risiko tetap ada kalau izin aksesnya tidak diatur dengan benar. Pemeriksaan dan pengelolaan izin file tersembunyi penting untuk menjaga keamanan sistem.

Pada direktori proyek ini, file `.project_x.txt` punya izin seperti ini:

<img width="640" height="234" alt="Screenshot 2026-01-20 at 02-41-53 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/fa08433e-0cbb-4d32-976c-21cb027d5320" />

Pemilik file punya hak baca dan tulis, sementara group cuma punya tulis tanpa baca. Kondisi ini tidak sesuai kebijakan karena memungkinkan file diubah tanpa bisa dicek isinya, jadi berpotensi memicu manipulasi data.

### a. Hidden file permission adjustment

Kebijakan organisasi menetapkan file ini supaya:
- Pemilik bisa baca dan tulis
- Group hanya bisa baca
- Other tidak punya akses sama sekali

Perubahan dilakukan dengan perintah Linux:

<img width="640" height="454" alt="Screenshot 2026-01-20 at 02-45-11 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/fd7c4c7a-bf1d-4f9b-b2a1-8746d5122ab1" />

Perintah ini menghapus hak tulis group dan memastikan group hanya bisa baca. Pemilik tetap punya hak tulis dan baca sesuai kebijakan.

### b. Hidden file security results

Setelah penyesuaian, file tersembunyi hanya bisa:
- Dibaca dan diubah oleh pemilik
- Dibaca oleh group
- Tidak diakses oleh other

Konfigurasi ini menjaga integritas file dan memastikan hanya pengguna berwenang yang bisa mengakses isinya, walaupun file tersembunyi. Langkah ini melengkapi penguatan kontrol akses sebelumnya sebelum mengamankan direktori yang berisi folder sensitif.

---

## 📁 Securing directory access <a name="securing">

Pengaturan izin pada direktori sama pentingnya dengan file untuk menjaga keamanan sistem. Izin ini menentukan siapa yang bisa masuk ke folder sekaligus mengakses isi di dalamnya. Konfigurasi yang salah bisa membuat orang yang tidak berwenang bisa melihat atau mengubah seluruh isi direktori. Direktori proyek `drafts` menyimpan dokumen sensitif dan hanya boleh diakses oleh pemilik, pengguna `researcher2`.

Izin awal direktori terlihat seperti ini:

<img width="640" height="234" alt="Screenshot 2026-01-20 at 02-41-53 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/da6bab45-9cbd-40af-bce2-d764b7fd87b8" />

Group masih punya izin masuk ke direktori, tapi tidak bisa melihat isi atau mengubah file. Konfigurasi ini belum sepenuhnya sesuai kebijakan, karena seharusnya hanya pemilik yang bisa mengakses direktori dan isinya.

### a. Restricting directory access

Hanya `researcher2` yang boleh masuk ke `drafts`. Izin execute untuk group dan other dihapus supaya mereka tidak bisa membuka folder. Perintah yang digunakan:

<img width="640" height="245" alt="Screenshot 2026-01-20 at 02-48-24 Activity Manage authorization Google Skills" src="https://github.com/user-attachments/assets/b652d89b-1ef4-4cca-beb0-32d03b854ebd" />

### b. Directory hardening results

Sekarang direktori `drafts` hanya bisa diakses oleh pemilik. Group dan other tidak punya izin masuk atau melihat isinya. Konfigurasi ini menjaga dokumen sensitif tetap aman dan memperkuat kontrol akses berbasis peran. Langkah ini melengkapi pengaturan file dan file tersembunyi, membuat seluruh struktur proyek sudah sesuai kebijakan keamanan organisasi.

---

## 📝 Final summary <a name="summary">

Proyek ini mendokumentasikan pengalaman saya mengelola dan mengamankan izin file serta direktori pakai perintah Linux sebagai latihan access control. Skenario menggambarkan lingkungan organisasi besar dengan tim riset internal, di mana saya meninjau izin yang ada, menemukan akses yang tidak seharusnya, lalu menyesuaikannya supaya hanya pengguna berwenang yang bisa mengakses sistem.

Proses dilakukan dengan memeriksa file biasa, file tersembunyi, dan direktori sensitif supaya sesuai kebijakan keamanan organisasi. Setiap perubahan dijalankan dengan hati-hati pakai perintah Linux seperti `ls -l`, `ls -la`, dan `chmod`. Tujuannya menghapus akses tulis yang tidak sah dan membatasi akses direktori hanya untuk pemilik yang berwenang.

Hasil akhirnya menunjukkan kontrol akses berbasis permission file bisa diterapkan efektif untuk melindungi data sensitif dan mencegah penyalahgunaan akses. Pendekatan ini memperkuat keamanan sistem secara menyeluruh. Proyek ini juga menekankan pentingnya memahami permission string Linux supaya praktik keamanan dijalankan konsisten dan berkelanjutan.
