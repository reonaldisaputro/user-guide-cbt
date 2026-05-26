# 🧪 Panduan Lengkap QA Testing — Sistem PMB PKU-MI

> **Untuk QA Pemula** — Panduan ini dibuat step-by-step agar kamu yang baru pertama kali jadi QA bisa langsung memahami dan menjalankan testing dengan benar.

---

## 📖 Daftar Isi

1. [QA itu Apa? (Pemula Wajib Baca)](#1-qa-itu-apa-pemula-wajib-baca)
2. [Kenalan dengan Sistem PMB](#2-kenalan-dengan-sistem-pmb)
3. [Persiapan Sebelum Testing](#3-persiapan-sebelum-testing)
4. [Metode Testing yang Digunakan](#4-metode-testing-yang-digunakan)
5. [Testing Portal Publik (Calon Mahasiswa)](#5-testing-portal-publik-calon-mahasiswa)
6. [Testing Dashboard Admin](#6-testing-dashboard-admin)
7. [Testing Backend API](#7-testing-backend-api)
8. [End-to-End Testing (Alur Lengkap)](#8-end-to-end-testing-alur-lengkap)
9. [State Machine Testing (Paling Penting)](#9-state-machine-testing-paling-penting)
10. [Template Laporan Bug](#10-template-laporan-bug)
11. [Regression Testing Checklist](#11-regression-testing-checklist)
12. [Tips & Best Practices untuk QA Pemula](#12-tips--best-practices-untuk-qa-pemula)

---

## 1. QA itu Apa? (Pemula Wajib Baca)

### Apa itu QA (Quality Assurance)?

QA adalah orang yang memastikan aplikasi/software **berfungsi dengan benar** sebelum dirilis ke pengguna. Bayangkan QA seperti "pengawas kualitas" di pabrik — QA memeriksa apakah produk (aplikasi) yang dibuat developer sudah sesuai dengan yang diinginkan user.

### Perbedaan QA dan Developer

| Developer              | QA (Kamu)                           |
| ---------------------- | ----------------------------------- |
| Membuat aplikasi       | Memastikan aplikasi tidak rusak     |
| Menulis kode program   | Menulis skenario testing            |
| Fokus "membangun"      | Fokus "memeriksa & mencari masalah" |
| Bisa saja terburu-buru | Harus teliti dan sabar              |

### Istilah-istilah yang Sering Dipakai

| Istilah             | Arti                                               | Contoh                                                             |
| ------------------- | -------------------------------------------------- | ------------------------------------------------------------------ |
| **Bug**             | Kesalahan/kutu di aplikasi                         | Tombol "Daftar" tidak bisa diklik                                  |
| **Test Case**       | Skenario uji yang harus dilakukan                  | "User mengisi email valid lalu klik login"                         |
| **Expected Result** | Hasil yang seharusnya terjadi                      | Muncul pesan "Login berhasil"                                      |
| **Actual Result**   | Hasil yang sebenarnya terjadi                      | Muncul pesan error                                                 |
| **Pass**            | Test case berhasil/lulus                           | Fitur berjalan sesuai harapan                                      |
| **Fail**            | Test case gagal                                    | Ada bug yang ditemukan                                             |
| **Regression**      | Test ulang fitur lama setelah ada perubahan        | Setelah fix bug login, cek apakah register masih normal            |
| **Reproduce**       | Mengulangi langkah bug agar developer bisa melihat | "Langkah 1: Buka halaman login. Langkah 2: ..."                    |
| **Environment**     | Tempat aplikasi diuji                              | Local (laptop), Staging (server testing), Production (server live) |

---

## 2. Kenalan dengan Sistem PMB

### Gambaran Umum

Sistem PMB PKU-MI terdiri dari **3 aplikasi**:

```
┌─────────────────────────────────────────────────────────┐
│  CALON MAHASISWA                                        │
│  ┌─────────────────┐                                    │
│  │ Portal Publik   │  ← Daftar, isi biodata, upload     │
│  │ (pmb-fe-next-js)│    dokumen, cek status             │
│  └────────┬────────┘                                    │
│           │                                             │
│           ▼                                             │
│  ┌─────────────────┐                                    │
│  │ Backend API     │  ← Menyimpan data, logika bisnis   │
│  │ (Laravel)       │                                    │
│  └────────┬────────┘                                    │
│           │                                             │
│           ▼                                             │
│  ┌─────────────────┐                                    │
│  │ Dashboard Admin │  ← Panitia verifikasi, wawancara,  │
│  │ (pmb-admin)     │    umumkan hasil                   │
│  └─────────────────┘                                    │
└─────────────────────────────────────────────────────────┘
```

### State Machine (Mesin Status Pendaftaran)

Ini adalah **inti sistem PMB**. Setiap pendaftar memiliki status yang berubah otomatis:

```
PROFILE_DRAFT → PROFILE_COMPLETE → PROFILE_CONFIRMED
                                          ↓
                           BERKAS_IN_PROGRESS → BERKAS_SUBMITTED
                                                        ↓
              BERKAS_REJECTED ← BERKAS_REVISION ↔ BERKAS_VERIFIED
                                                      ↓
                                                   SELEKSI
                                                      ↓
                                          DITERIMA ↔ DITOLAK
```

**Penjelasan singkat setiap status:**

| Status               | Arti                                         |
| -------------------- | -------------------------------------------- |
| `PROFILE_DRAFT`      | Biodata belum lengkap                        |
| `PROFILE_COMPLETE`   | Biodata lengkap tapi belum dikonfirmasi      |
| `PROFILE_CONFIRMED`  | Biodata sudah dikonfirmasi oleh user         |
| `BERKAS_IN_PROGRESS` | Sedang upload dokumen, belum lengkap         |
| `BERKAS_SUBMITTED`   | Semua dokumen sudah diupload                 |
| `BERKAS_VERIFIED`    | Admin sudah verifikasi semua dokumen (valid) |
| `BERKAS_REVISION`    | Ada dokumen ditolak, masih bisa revisi       |
| `BERKAS_REJECTED`    | Dokumen ditolak dan melewati deadline revisi |
| `SELEKSI`            | Sedang dalam proses wawancara                |
| `DITERIMA`           | Lolos seleksi                                |
| `DITOLAK`            | Tidak lolos seleksi                          |

### Dokumen yang Harus Diupload

**7 Dokumen Wajib:**

1. Pas Foto
2. KTP
3. Ijazah
4. Transkrip Nilai
5. CV
6. Surat Pernyataan Integritas
7. Bukti Pembayaran

**Dokumen Opsional:**

- Sertifikat Bahasa
- LOA (Letter of Acceptance)
- Surat Rekomendasi
- Surat Usulan

---

## 3. Persiapan Sebelum Testing

### 3.1 Siapkan Akun Test

Buat akun-akun berikut untuk testing (jangan pakai akun production/real):

| Akun              | Email Contoh              | Password Contoh | Kegunaan        |
| ----------------- | ------------------------- | --------------- | --------------- |
| Calon Mahasiswa 1 | test.mahasiswa1@gmail.com | Test1234!       | Jalur Regular   |
| Calon Mahasiswa 2 | test.mahasiswa2@gmail.com | Test1234!       | Jalur LOA       |
| Calon Mahasiswa 3 | test.mahasiswa3@gmail.com | Test1234!       | Test deadline   |
| Admin Panitia     | test.admin@pkumi.cloud    | Admin1234!      | Dashboard admin |

> **Catatan Penting**: Gunakan email yang benar-benar bisa dibuka (bisa pakai Gmail + alias seperti `nama+test1@gmail.com`) karena sistem mengirim email verifikasi.

### 3.2 Siapkan File Test

Siapkan file-file ini di folder khusus (misal: `D:\QA_Test_Files\`):

| File              | Ukuran | Format       | Kegunaan             |
| ----------------- | ------ | ------------ | -------------------- |
| Foto valid        | < 2MB  | .jpg         | Upload pas foto      |
| Foto oversize     | > 2MB  | .jpg         | Test batas ukuran    |
| KTP valid         | < 2MB  | .pdf / .jpg  | Upload KTP           |
| Ijazah valid      | < 2MB  | .pdf         | Upload ijazah        |
| Transkrip valid   | < 2MB  | .pdf         | Upload transkrip     |
| CV valid          | < 2MB  | .pdf         | Upload CV            |
| File salah format | < 2MB  | .exe / .zip  | Test validasi format |
| File corrupt      | -      | .pdf (rusak) | Test error handling  |

### 3.3 Siapkan Tools

| Tool                         | Fungsi                        | Cara Pakai                      |
| ---------------------------- | ----------------------------- | ------------------------------- |
| **Browser** (Chrome/Firefox) | Akses aplikasi                | Buka URL aplikasi               |
| **DevTools** (F12)           | Inspeksi element, lihat error | Tekan F12 → tab Console/Network |
| **Postman** (Opsional)       | Test API langsung             | Install aplikasi Postman        |
| **Notepad/Excel**            | Catat hasil testing           | Tulis test case dan status      |
| **Screenshots**              | Dokumentasi bug               | Snipping Tool / Win+Shift+S     |
| **Stopwatch/Timer**          | Cek countdown/timer           | Pakai HP atau website timer     |

### 3.4 Environment Testing

Tanya ke tim developer: aplikasi diuji di mana?

| Environment    | URL Contoh                      | Kegunaan                              |
| -------------- | ------------------------------- | ------------------------------------- |
| **Local**      | http://localhost:3000           | Developer coding di laptop            |
| **Staging**    | https://staging.pmb.pkumi.cloud | **QA testing di sini**                |
| **Production** | https://pmb.pkumionline.cloud   | Live / user asli (JANGAN DIUTAK-ATIK) |

> **Aturan emas QA**: Selalu test di **Staging** dulu, jangan pernah test langsung di Production.

---

## 4. Metode Testing yang Digunakan

### 4.1 Manual Testing (Utama)

QA menjalankan aplikasi secara manual seperti user biasa, lalu mencatat hasilnya.

### 4.2 Functional Testing

Memastikan setiap fitur berfungsi sesuai spesifikasi.

### 4.3 UI/UX Testing

Memastikan tampilan rapi, tombol mudah ditemukan, tidak ada typo.

### 4.4 Negative Testing

Sengaja memberikan input salah untuk melihat apakah aplikasi menangani error dengan baik.

Contoh Negative Testing:

- Upload file .exe (seharusnya ditolak)
- Isi email tanpa @ (seharusnya muncul pesan error)
- Upload foto 10MB (seharusnya muncul pesan "file terlalu besar")

### 4.5 Boundary Testing

Menguji batas-batas nilai.

Contoh:

- File persis 2MB (batas maksimal) → harus diterima
- File 2.1MB (melebihi batas) → harus ditolak
- Password 7 karakter (batas minimal 8) → harus ditolak
- Password 8 karakter (pas batas) → harus diterima

---

## 5. Testing Portal Publik (Calon Mahasiswa)

### 5.1 Halaman Homepage

| No  | Test Case                | Langkah                     | Expected Result                         | Status |
| --- | ------------------------ | --------------------------- | --------------------------------------- | ------ |
| 1   | Tampil informasi periode | Buka halaman utama          | Muncul periode PMB aktif dengan tanggal | ⬜     |
| 2   | Tampil program studi     | Scroll ke bawah             | Muncul daftar program studi (S2/S3)     | ⬜     |
| 3   | Tampil timeline          | Cari bagian timeline        | Muncul jadwal pendaftaran lengkap       | ⬜     |
| 4   | Tombol "Daftar Sekarang" | Klik tombol daftar          | Redirect ke halaman register            | ⬜     |
| 5   | Tampil FAQ               | Klik menu FAQ               | Muncul pertanyaan & jawaban             | ⬜     |
| 6   | Tampil kontak            | Klik menu Kontak            | Muncul informasi kontak panitia         | ⬜     |
| 7   | Tampil download          | Klik menu Download          | Muncul file yang bisa didownload        | ⬜     |
| 8   | Responsive mobile        | Buka di HP / resize browser | Tampilan tetap rapi                     | ⬜     |

### 5.2 Halaman Register (Pendaftaran Akun)

| No  | Test Case                   | Langkah                                            | Expected Result                                  | Status |
| --- | --------------------------- | -------------------------------------------------- | ------------------------------------------------ | ------ |
| 1   | Register dengan data valid  | Isi nama, email valid, password kuat → klik Daftar | Muncul pesan "Registrasi berhasil, cek email"    | ⬜     |
| 2   | Email sudah terdaftar       | Daftar dengan email yang sama                      | Muncul pesan "Email sudah terdaftar"             | ⬜     |
| 3   | Email tidak valid           | Isi email: "test@" atau "test"                     | Muncul pesan "Format email tidak valid"          | ⬜     |
| 4   | Password terlalu pendek     | Isi password: "abc"                                | Muncul pesan "Minimal 8 karakter"                | ⬜     |
| 5   | Password tanpa huruf besar  | Isi password: "password1!"                         | Muncul pesan "Harus ada huruf besar"             | ⬜     |
| 6   | Password tanpa angka        | Isi password: "Password!"                          | Muncul pesan "Harus ada angka"                   | ⬜     |
| 7   | Konfirmasi password beda    | Password: "Test1234!", Konfirmasi: "Test1234?"     | Muncul pesan "Password tidak cocok"              | ⬜     |
| 8   | Field kosong                | Biarkan semua kosong → klik Daftar                 | Muncul pesan "Field wajib diisi"                 | ⬜     |
| 9   | Register saat periode tutup | Coba daftar saat end_date sudah lewat              | Muncul pesan "Periode pendaftaran sudah ditutup" | ⬜     |
| 10  | Verifikasi email            | Buka email → klik link verifikasi                  | Status email terverifikasi, bisa login           | ⬜     |
| 11  | Resend verifikasi email     | Klik "Kirim ulang email verifikasi"                | Email baru dikirim                               | ⬜     |
| 12  | Register melebihi kuota     | Daftar saat jumlah peserta = max_participants      | Muncul pesan "Kuota pendaftaran penuh"           | ⬜     |

### 5.3 Halaman Login

| No  | Test Case               | Langkah                                                  | Expected Result                          | Status |
| --- | ----------------------- | -------------------------------------------------------- | ---------------------------------------- | ------ |
| 1   | Login dengan data valid | Isi email & password benar → Login                       | Redirect ke dashboard                    | ⬜     |
| 2   | Email belum verifikasi  | Login dengan email belum diverifikasi                    | Muncul pesan "Email belum diverifikasi"  | ⬜     |
| 3   | Password salah          | Isi password salah                                       | Muncul pesan "Email atau password salah" | ⬜     |
| 4   | Email tidak terdaftar   | Isi email random                                         | Muncul pesan "Email atau password salah" | ⬜     |
| 5   | Field kosong            | Biarkan kosong → Login                                   | Muncul pesan "Field wajib diisi"         | ⬜     |
| 6   | Remember Me             | Centang "Ingat saya" → Login → tutup browser → buka lagi | Masih tetap login                        | ⬜     |
| 7   | Tanpa Remember Me       | Tidak centang → Login → tutup browser → buka lagi        | Harus login ulang                        | ⬜     |
| 8   | Forgot password         | Klik "Lupa password" → isi email → submit                | Email reset password dikirim             | ⬜     |
| 9   | Reset password          | Klik link di email → isi password baru                   | Password berhasil diubah                 | ⬜     |

### 5.4 Halaman Dashboard Mahasiswa

| No  | Test Case                    | Langkah                           | Expected Result                                         | Status |
| --- | ---------------------------- | --------------------------------- | ------------------------------------------------------- | ------ |
| 1   | Tampil status pendaftaran    | Login → lihat dashboard           | Muncul status saat ini (misal: "Biodata Belum Lengkap") | ⬜     |
| 2   | Tampil progress bar          | Lihat dashboard                   | Muncul progress bar sesuai state                        | ⬜     |
| 3   | Countdown timer              | Lihat dashboard saat ada deadline | Countdown berjalan real-time                            | ⬜     |
| 4   | Navigasi ke Profile          | Klik "Lengkapi Biodata"           | Pindah ke halaman profile                               | ⬜     |
| 5   | Navigasi ke Form Pendaftaran | Klik "Upload Dokumen"             | Pindah ke halaman upload dokumen                        | ⬜     |
| 6   | Tampil notifikasi            | Lihat icon lonceng/notifikasi     | Muncul notifikasi jika ada update                       | ⬜     |
| 7   | Logout                       | Klik logout                       | Kembali ke halaman login, session habis                 | ⬜     |

### 5.5 Halaman Profile (Biodata)

| No  | Test Case                   | Langkah                                                                                                                                                               | Expected Result                                                  | Status |
| --- | --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------ |
| 1   | Isi semua field wajib       | Isi nama, tempat lahir, tanggal lahir, gender, no HP, alamat, provinsi, kota, pendidikan terakhir, jurusan, nama ayah, nama ibu, pekerjaan ortu, alamat ortu → Simpan | Data tersimpan, muncul pesan sukses                              | ⬜     |
| 2   | Field wajib kosong          | Biarkan nama kosong → Simpan                                                                                                                                          | Muncul pesan "Nama wajib diisi"                                  | ⬜     |
| 3   | Nomor HP tidak valid        | Isi no HP: "abc" atau "123"                                                                                                                                           | Muncul pesan "Nomor HP tidak valid"                              | ⬜     |
| 4   | Pilih provinsi & kota       | Pilih provinsi → pilih kota                                                                                                                                           | Dropdown kota menyesuaikan provinsi                              | ⬜     |
| 5   | Tanggal lahir di masa depan | Pilih tanggal: tahun 2030                                                                                                                                             | Muncul pesan "Tanggal tidak valid" atau tidak bisa dipilih       | ⬜     |
| 6   | Simpan dan lanjut nanti     | Isi sebagian → Simpan                                                                                                                                                 | Data tersimpan, status PROFILE_DRAFT                             | ⬜     |
| 7   | Konfirmasi profil           | Isi semua → klik "Konfirmasi Profil"                                                                                                                                  | Muncul modal konfirmasi → setuju → status PROFILE_CONFIRMED      | ⬜     |
| 8   | Edit setelah konfirmasi     | Coba edit data setelah dikonfirmasi                                                                                                                                   | Jika belum lewat end_date: bisa edit. Jika sudah lewat: terkunci | ⬜     |
| 9   | Profile locked              | Coba edit setelah deadline periode                                                                                                                                    | Tidak bisa edit, muncul pesan "Periode sudah ditutup"            | ⬜     |

### 5.6 Halaman Form Pendaftaran (Upload Dokumen)

| No  | Test Case                     | Langkah                                 | Expected Result                                      | Status |
| --- | ----------------------------- | --------------------------------------- | ---------------------------------------------------- | ------ |
| 1   | Pilih program studi           | Pilih Magister S2 PKU                   | Program studi tersimpan                              | ⬜     |
| 2   | Upload 7 dokumen wajib        | Upload satu per satu                    | Semua dokumen muncul di list dengan status "Pending" | ⬜     |
| 3   | Upload file > 2MB             | Upload foto 5MB                         | Muncul pesan "File maksimal 2MB"                     | ⬜     |
| 4   | Upload format salah           | Upload file .exe                        | Muncul pesan "Format file tidak didukung"            | ⬜     |
| 5   | Upload file corrupt           | Upload PDF yang rusak                   | Muncul pesan error atau tidak bisa di-preview        | ⬜     |
| 6   | Hapus dokumen                 | Klik icon hapus di salah satu dokumen   | Dokumen terhapus dari list                           | ⬜     |
| 7   | Ganti dokumen                 | Upload dokumen baru mengganti yang lama | Dokumen lama terganti, yang baru muncul              | ⬜     |
| 8   | Upload LOA (opsional)         | Upload file LOA                         | LOA muncul di list opsional                          | ⬜     |
| 9   | Status berkas setelah upload  | Upload semua dokumen wajib              | Status berubah ke BERKAS_SUBMITTED                   | ⬜     |
| 10  | Revisi dokumen (Masa Sanggah) | Admin tolak dokumen → mahasiswa login   | Muncul notifikasi, bisa upload ulang                 | ⬜     |
| 11  | Upload saat periode tutup     | Coba upload setelah end_date            | Terkunci, tidak bisa upload                          | ⬜     |

### 5.7 Halaman Download Undangan Wawancara

| No  | Test Case               | Langkah                        | Expected Result                      | Status |
| --- | ----------------------- | ------------------------------ | ------------------------------------ | ------ |
| 1   | Download undangan       | Status SELEKSI → klik download | PDF undangan berhasil diunduh        | ⬜     |
| 2   | Preview undangan        | Klik preview PDF               | PDF bisa dilihat di browser          | ⬜     |
| 3   | Tampil jadwal wawancara | Lihat dashboard                | Muncul tanggal, jam, link Zoom, room | ⬜     |
| 4   | RSVP wawancara          | Klik "Hadir" / "Tidak Hadir"   | Status RSVP tersimpan                | ⬜     |

### 5.8 Halaman Hasil Akhir (DITERIMA / DITOLAK)

| No  | Test Case              | Langkah                                    | Expected Result                            | Status |
| --- | ---------------------- | ------------------------------------------ | ------------------------------------------ | ------ |
| 1   | Tampil DITERIMA        | Status DITERIMA → buka dashboard           | Muncul ucapan selamat, tombol download LOA | ⬜     |
| 2   | Download LOA           | Klik "Download Surat Diterima"             | PDF LOA berhasil diunduh                   | ⬜     |
| 3   | Check kelayakan LOA    | Klik "Cek Kelayakan LOA"                   | Muncul status kelayakan                    | ⬜     |
| 4   | Tampil DITOLAK         | Status DITOLAK → buka dashboard            | Muncul pemberitahuan penolakan             | ⬜     |
| 5   | Countdown pengumuman   | Sebelum tanggal pengumuman                 | Muncul countdown timer                     | ⬜     |
| 6   | Pengumuman belum rilis | Coba akses sebelum final_announcement_date | Muncul pesan "Pengumuman belum tersedia"   | ⬜     |

---

## 6. Testing Dashboard Admin

### 6.1 Halaman Login Admin

| No  | Test Case                   | Langkah                               | Expected Result                                  | Status |
| --- | --------------------------- | ------------------------------------- | ------------------------------------------------ | ------ |
| 1   | Login admin valid           | Isi email admin & password → Login    | Redirect ke dashboard admin                      | ⬜     |
| 2   | Login dengan akun mahasiswa | Coba login pakai akun calon mahasiswa | Muncul pesan "Akses ditolak"                     | ⬜     |
| 3   | Password salah              | Isi password salah                    | Muncul pesan "Kredensial tidak valid"            | ⬜     |
| 4   | Session kadaluarsa          | Biarkan idle lama → coba navigasi     | Redirect ke login dengan pesan "Sesi kadaluarsa" | ⬜     |

### 6.2 Dashboard Analytics

| No  | Test Case                      | Langkah                         | Expected Result                              | Status |
| --- | ------------------------------ | ------------------------------- | -------------------------------------------- | ------ |
| 1   | Tampil total pendaftar         | Buka dashboard admin            | Muncul angka total registrant                | ⬜     |
| 2   | Tampil donut chart             | Lihat bagian state distribution | Chart menampilkan proporsi tiap status       | ⬜     |
| 3   | Tampil bar chart program studi | Lihat bagian program studi      | Chart menampilkan peminat tiap prodi         | ⬜     |
| 4   | Filter by periode              | Pilih periode PMB di dropdown   | Data berubah sesuai periode                  | ⬜     |
| 5   | Filter by program studi        | Pilih program studi             | Data berubah sesuai prodi                    | ⬜     |
| 6   | Filter by tanggal              | Pilih rentang tanggal           | Data berubah sesuai rentang                  | ⬜     |
| 7   | Tampil conversion funnel       | Lihat bagian funnel             | Muncur visualisasi dari daftar → diterima    | ⬜     |
| 8   | Tampil peta provinsi           | Lihat bagian distribusi         | Muncul peta/jsvectormap provinsi             | ⬜     |
| 9   | Tampil timeline trend          | Lihat bagian grafik garis       | Grafik menunjukkan tren pendaftaran per hari | ⬜     |

### 6.3 Manajemen Data Pendaftar

| No  | Test Case                 | Langkah                    | Expected Result                                 | Status |
| --- | ------------------------- | -------------------------- | ----------------------------------------------- | ------ |
| 1   | Lihat list belum diproses | Buka menu "Belum Diproses" | Muncul daftar pendaftar status awal             | ⬜     |
| 2   | Lihat list lulus berkas   | Buka menu "Lulus"          | Muncul daftar pendaftar BERKAS_VERIFIED         | ⬜     |
| 3   | Lihat list tidak lulus    | Buka menu "Tidak Lulus"    | Muncul daftar pendaftar BERKAS_REJECTED/DITOLAK | ⬜     |
| 4   | Detail pendaftar          | Klik salah satu nama       | Muncul halaman detail lengkap pendaftar         | ⬜     |
| 5   | Search pendaftar          | Ketik nama di search box   | Hasil filter sesuai nama                        | ⬜     |
| 6   | Filter by status          | Pilih filter status        | List berubah sesuai filter                      | ⬜     |
| 7   | Pagination                | Klik next/previous page    | Data berpindah halaman                          | ⬜     |
| 8   | Export Excel              | Klik "Export Excel"        | File Excel berhasil diunduh                     | ⬜     |
| 9   | Export PDF                | Klik "Export PDF"          | File PDF berhasil diunduh                       | ⬜     |

### 6.4 Verifikasi Dokumen (Attachments)

| No  | Test Case                     | Langkah                                           | Expected Result                                    | Status |
| --- | ----------------------------- | ------------------------------------------------- | -------------------------------------------------- | ------ |
| 1   | Approve dokumen               | Buka detail pendaftar → klik "Approve" di dokumen | Status dokumen jadi "Valid", muncul timestamp      | ⬜     |
| 2   | Reject dokumen dengan catatan | Klik "Reject" → isi alasan penolakan → Submit     | Status dokumen jadi "Invalid", muncul catatan      | ⬜     |
| 3   | Bulk reject semua dokumen     | Pilih pendaftar → klik "Tolak Semua Berkas"       | Semua dokumen jadi invalid, status BERKAS_REJECTED | ⬜     |
| 4   | Reset penolakan               | Klik "Reset Penolakan"                            | Status kembali ke BERKAS_REVISION                  | ⬜     |
| 5   | Preview dokumen               | Klik nama file dokumen                            | Muncul preview file (gambar/PDF)                   | ⬜     |
| 6   | Download dokumen              | Klik icon download                                | File berhasil diunduh                              | ⬜     |

### 6.5 Manajemen Wawancara

| No  | Test Case                  | Langkah                                                                           | Expected Result                            | Status |
| --- | -------------------------- | --------------------------------------------------------------------------------- | ------------------------------------------ | ------ |
| 1   | Buat konfigurasi wawancara | Buka "Manajemen Wawancara → Config" → klik "Tambah"                               | Form konfigurasi muncul                    | ⬜     |
| 2   | Isi data Zoom              | Isi link Zoom, Meeting ID, Passcode, tanggal, jam, peserta per ruang, total ruang | Data tersimpan                             | ⬜     |
| 3   | Generate nomor ujian       | Pilih pendaftar → klik "Generate Nomor Ujian"                                     | Nomor ujian muncul (format: YYYYMMXXX)     | ⬜     |
| 4   | Batch generate nomor ujian | Pilih multiple pendaftar → klik "Generate Batch"                                  | Semua pendaftar mendapat nomor ujian       | ⬜     |
| 5   | Assign ruang wawancara     | Pilih pendaftar → pilih ruang → Assign                                            | Pendaftar masuk ke jadwal ruang tertentu   | ✔      |
| 6   | Auto-assign ruang          | Klik "Auto Assign"                                                                | Sistem otomatis membagi pendaftar ke ruang | ⬜     |
| 7   | Kirim undangan email       | Klik "Kirim Undangan"                                                             | Email terkirim, PDF undangan ter-generate  | ⬜     |
| 8   | Retry email gagal          | Klik "Retry" di email yang gagal                                                  | Email dikirim ulang                        | ⬜     |
| 9   | Lihat jadwal per ruang     | Klik salah satu ruang                                                             | Muncul daftar peserta di ruang tersebut    | ⬜     |
| 10  | Reschedule peserta         | Pindahkan peserta dari ruang A ke ruang B                                         | Jadwal peserta berpindah                   | ⬜     |

### 6.6 Manajemen LOA (Letter of Acceptance)

| No  | Test Case              | Langkah                                  | Expected Result                                          | Status |
| --- | ---------------------- | ---------------------------------------- | -------------------------------------------------------- | ------ |
| 1   | Verifikasi LOA valid   | Buka detail LOA pendaftar → klik "Valid" | Status LOA valid, pendaftar auto-DITERIMA                | ⬜     |
| 2   | Verifikasi LOA invalid | Klik "Invalid" → beri catatan            | Status LOA invalid, pendaftar tetap di status sebelumnya | ⬜     |
| 3   | Download LOA admin     | Klik download file LOA                   | File berhasil diunduh                                    | ⬜     |
| 4   | Generate LOA resmi     | Klik "Generate LOA"                      | PDF resmi dengan nomor urut ter-generate                 | ⬜     |

### 6.7 Manajemen User Admin

| No  | Test Case         | Langkah                                | Expected Result                 | Status |
| --- | ----------------- | -------------------------------------- | ------------------------------- | ------ |
| 1   | Tambah admin baru | Klik "Tambah User" → isi data → Simpan | Admin baru tersimpan            | ⬜     |
| 2   | Edit admin        | Klik edit → ubah data → Simpan         | Data admin terupdate            | ⬜     |
| 3   | Nonaktifkan admin | Toggle "is_active" ke off              | Admin tidak bisa login          | ⬜     |
| 4   | Hapus admin       | Klik hapus → konfirmasi                | Admin terhapus                  | ⬜     |
| 5   | Role-based access | Login dengan admin non-superadmin      | Hanya menu tertentu yang muncul | ⬜     |

### 6.8 Manajemen Periode PMB

| No  | Test Case           | Langkah                                                                                                                                                                                    | Expected Result                              | Status |
| --- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------- | ------ |
| 1   | Tambah periode baru | Klik "Tambah Periode" → isi nama, tanggal mulai, tanggal selesai, deadline revisi, tanggal seleksi dokumen, tanggal pengumuman dokumen, tanggal wawancara, tanggal pengumuman akhir, kuota | Periode baru tersimpan                       | ⬜     |
| 2   | Edit periode        | Klik edit → ubah tanggal → Simpan                                                                                                                                                          | Periode terupdate                            | ⬜     |
| 3   | Aktifkan periode    | Toggle "Aktif"                                                                                                                                                                             | Periode menjadi periode yang aktif digunakan | ⬜     |
| 4   | Hapus periode       | Klik hapus → konfirmasi                                                                                                                                                                    | Periode terhapus                             | ⬜     |
| 5   | Validasi tanggal    | Isi tanggal mulai > tanggal selesai                                                                                                                                                        | Muncul pesan error validasi                  | ⬜     |

### 6.9 Manajemen Konten (FAQ, Download, Informasi, Kontak)

| No  | Test Case                   | Langkah                                      | Expected Result                     | Status |
| --- | --------------------------- | -------------------------------------------- | ----------------------------------- | ------ |
| 1   | Tambah FAQ                  | Isi pertanyaan & jawaban → Simpan            | FAQ muncul di portal publik         | ⬜     |
| 2   | Edit FAQ                    | Ubah jawaban → Simpan                        | FAQ terupdate                       | ⬜     |
| 3   | Tambah file download        | Upload file → beri judul → Simpan            | File muncul di menu download publik | ⬜     |
| 4   | Tambah informasi/pengumuman | Isi judul & konten → pilih kategori → Simpan | Pengumuman muncul di portal         | ⬜     |
| 5   | Edit kontak                 | Ubah nomor telepon/email panitia → Simpan    | Kontak terupdate di portal          | ⬜     |
| 6   | Rich text editor            | Ketik dengan format bold, list, link         | Format tersimpan dengan benar       | ⬜     |

---

## 7. Testing Backend API

> **Untuk QA yang sudah agak mahir** — bisa pakai Postman atau test langsung dari frontend.

### 7.1 API Autentikasi

| Endpoint             | Method | Test Case                      | Expected                        |
| -------------------- | ------ | ------------------------------ | ------------------------------- |
| `/api/auth/register` | POST   | Register dengan data valid     | 201 Created, token dikembalikan |
| `/api/auth/register` | POST   | Email duplikat                 | 422 Unprocessable, pesan error  |
| `/api/auth/login`    | POST   | Login valid                    | 200 OK, token + user data       |
| `/api/auth/login`    | POST   | Password salah                 | 401 Unauthorized                |
| `/api/auth/logout`   | POST   | Logout dengan token valid      | 200 OK, token dihapus           |
| `/api/auth/profile`  | GET    | Get profile dengan token valid | 200 OK, data user               |
| `/api/auth/profile`  | GET    | Tanpa token                    | 401 Unauthorized                |

### 7.2 API Student (Biodata)

| Endpoint                       | Method | Test Case              | Expected                             |
| ------------------------------ | ------ | ---------------------- | ------------------------------------ |
| `/api/student/upsert`          | POST   | Simpan biodata lengkap | 200 OK, data tersimpan               |
| `/api/student/upsert`          | POST   | Field wajib kosong     | 422 Validation Error                 |
| `/api/student/profile`         | GET    | Get profile            | 200 OK, data lengkap                 |
| `/api/student/confirm-profile` | POST   | Konfirmasi profil      | 200 OK, state jadi PROFILE_CONFIRMED |
| `/api/student/confirm-profile` | POST   | Profile belum lengkap  | 400 Bad Request                      |

### 7.3 API Attachments

| Endpoint                | Method | Test Case                   | Expected                    |
| ----------------------- | ------ | --------------------------- | --------------------------- |
| `/api/attachments`      | POST   | Upload file valid           | 201 Created, file tersimpan |
| `/api/attachments`      | POST   | File > 2MB                  | 413 Payload Too Large       |
| `/api/attachments`      | GET    | List attachments user       | 200 OK, array attachments   |
| `/api/attachments/{id}` | DELETE | Hapus attachment sendiri    | 200 OK, file terhapus       |
| `/api/attachments/{id}` | DELETE | Hapus attachment orang lain | 403 Forbidden               |

### 7.4 API Admin

| Endpoint                              | Method | Test Case                   | Expected               |
| ------------------------------------- | ------ | --------------------------- | ---------------------- |
| `/api/admin/students`                 | GET    | Get list dengan token admin | 200 OK, array students |
| `/api/admin/students`                 | GET    | Get dengan token mahasiswa  | 403 Forbidden          |
| `/api/admin/attachments/{id}/approve` | POST   | Approve dokumen             | 200 OK, status valid   |
| `/api/admin/attachments/{id}/reject`  | POST   | Reject dokumen              | 200 OK, status invalid |
| `/api/admin/interview-config`         | POST   | Buat konfigurasi wawancara  | 201 Created            |

---

## 8. End-to-End Testing (Alur Lengkap)

### Skenario 1: Jalur Regular (Lengkap dari Daftar sampai Diterima)

**Tujuan**: Memastikan alur pendaftaran normal berjalan mulus.

| Langkah | Aksi                                                         | Di Cek                                      |
| ------- | ------------------------------------------------------------ | ------------------------------------------- |
| 1       | Buka portal → klik Daftar → isi data valid → submit          | Email verifikasi terkirim                   |
| 2       | Buka email → klik link verifikasi                            | Status email terverifikasi                  |
| 3       | Login dengan akun baru                                       | Redirect ke dashboard                       |
| 4       | Klik "Lengkapi Biodata" → isi semua field → Simpan           | Data tersimpan                              |
| 5       | Klik "Konfirmasi Profil" → setuju                            | Status jadi PROFILE_CONFIRMED               |
| 6       | Klik "Upload Dokumen" → pilih prodi → upload 7 dokumen wajib | Semua dokumen muncul di list                |
| 7       | Tunggu admin verifikasi (atau minta developer bantu)         | -                                           |
| 8       | Admin approve semua dokumen                                  | Status jadi BERKAS_VERIFIED                 |
| 9       | Admin generate nomor ujian                                   | Nomor ujian muncul di dashboard             |
| 10      | Admin assign ke ruang wawancara                              | Jadwal muncul di dashboard mahasiswa        |
| 11      | Admin kirim undangan email                                   | Email masuk, PDF bisa di-download           |
| 12      | Mahasiswa download undangan wawancara                        | PDF berhasil diunduh                        |
| 13      | Admin set status "Diterima"                                  | Status jadi DITERIMA                        |
| 14      | Mahasiswa buka dashboard                                     | Muncul ucapan selamat + tombol download LOA |
| 15      | Mahasiswa download LOA                                       | PDF LOA berhasil diunduh                    |

### Skenario 2: Jalur LOA (Letter of Acceptance)

| Langkah | Aksi                                 | Di Cek                    |
| ------- | ------------------------------------ | ------------------------- |
| 1-5     | Sama seperti Jalur Regular           | Sama                      |
| 6       | Upload dokumen + upload LOA          | LOA muncul di list        |
| 7       | Admin verifikasi LOA sebagai "Valid" | Status auto jadi DITERIMA |
| 8       | Mahasiswa cek dashboard              | Muncul ucapan selamat     |

### Skenario 3: Jalur Dokumen Ditolak & Revisi

| Langkah | Aksi                                                       | Di Cek                                           |
| ------- | ---------------------------------------------------------- | ------------------------------------------------ |
| 1-6     | Sama seperti Jalur Regular                                 | Sama                                             |
| 7       | Admin reject dokumen KTP dengan catatan "Foto tidak jelas" | Status jadi BERKAS_REVISION                      |
| 8       | Mahasiswa login → cek dashboard                            | Muncul notifikasi dokumen ditolak                |
| 9       | Mahasiswa upload ulang KTP yang baru                       | Dokumen baru tersimpan                           |
| 10      | Admin approve KTP baru                                     | Status kembali ke BERKAS_SUBMITTED lalu VERIFIED |

### Skenario 4: Deadline Revisi Terlewat

| Langkah | Aksi                                                                         | Di Cek                                  |
| ------- | ---------------------------------------------------------------------------- | --------------------------------------- |
| 1-7     | Sama seperti Skenario 3                                                      | Sama                                    |
| 8       | Tunggu hingga melewati revision_deadline (atau minta developer ubah tanggal) | -                                       |
| 9       | Mahasiswa coba upload ulang                                                  | Tidak bisa, status jadi BERKAS_REJECTED |
| 10      | Admin coba approve/reject                                                    | Status sudah final (BERKAS_REJECTED)    |

---

## 9. State Machine Testing (Paling Penting)

Ini adalah **bagian paling kritis** dari sistem PMB. Kamu harus memastikan status berubah sesuai aturan.

### Aturan Transisi Status

| Dari Status          | Ke Status            | Trigger                       | Boleh?            |
| -------------------- | -------------------- | ----------------------------- | ----------------- |
| `PROFILE_DRAFT`      | `PROFILE_COMPLETE`   | Biodata lengkap               | ✅ Auto           |
| `PROFILE_COMPLETE`   | `PROFILE_CONFIRMED`  | User klik konfirmasi          | ✅ Manual         |
| `PROFILE_CONFIRMED`  | `BERKAS_IN_PROGRESS` | Mulai upload dokumen          | ✅ Auto           |
| `BERKAS_IN_PROGRESS` | `BERKAS_SUBMITTED`   | Semua dokumen wajib diupload  | ✅ Auto           |
| `BERKAS_SUBMITTED`   | `BERKAS_VERIFIED`    | Admin approve semua           | ✅ Manual         |
| `BERKAS_SUBMITTED`   | `BERKAS_REVISION`    | Admin reject beberapa         | ✅ Manual         |
| `BERKAS_REVISION`    | `BERKAS_VERIFIED`    | Revisi di-upload & di-approve | ✅ Manual         |
| `BERKAS_REVISION`    | `BERKAS_REJECTED`    | Lewat revision_deadline       | ✅ Auto           |
| `BERKAS_VERIFIED`    | `SELEKSI`            | Admin assign interview        | ✅ Manual         |
| `SELEKSI`            | `DITERIMA`           | Admin set passed              | ✅ Manual         |
| `SELEKSI`            | `DITOLAK`            | Admin set failed              | ✅ Manual         |
| `DITOLAK`            | `DITERIMA`           | Admin set passed (reversal)   | ✅ Manual (force) |
| `DITERIMA`           | `DITOLAK`            | Admin set failed (reversal)   | ✅ Manual (force) |

### Test Case State Machine

| No  | Skenario                         | Langkah                                           | Expected                                |
| --- | -------------------------------- | ------------------------------------------------- | --------------------------------------- |
| 1   | Auto-sync: biodata lengkap       | Isi semua field biodata → simpan                  | Status otomatis PROFILE_COMPLETE        |
| 2   | Auto-sync: biodata tidak lengkap | Isi sebagian biodata → simpan                     | Status tetap PROFILE_DRAFT              |
| 3   | Auto-sync: dokumen lengkap       | Upload 7 dokumen wajib                            | Status otomatis BERKAS_SUBMITTED        |
| 4   | Auto-sync: dokumen kurang        | Upload 6 dokumen                                  | Status tetap BERKAS_IN_PROGRESS         |
| 5   | Admin lock state                 | Admin ubah state secara manual                    | State tidak berubah otomatis lagi       |
| 6   | Force update state               | Admin paksa ubah state dengan flag force          | State berubah meski tidak sesuai aturan |
| 7   | Invalid transition               | Coba ubah dari PROFILE_DRAFT langsung ke DITERIMA | Ditolak, muncul error                   |

---

## 10. Template Laporan Bug

Saat menemukan bug, laporkan dengan format berikut:

```markdown
## 🐛 Bug Report

**ID Bug**: BUG-001
**Tanggal**: 2025-01-15
**Dilaporkan oleh**: [Nama QA]
**Environment**: Staging
**Browser**: Chrome 120.0
**Device**: Laptop Windows 11

### Deskripsi

[Penjelasan singkat apa yang salah]

### Langkah Reproduce

1. [Langkah 1]
2. [Langkah 2]
3. [Langkah 3]

### Expected Result

[Seharusnya apa yang terjadi]

### Actual Result

[Apa yang sebenarnya terjadi]

### Evidence

[Screenshot / Screen recording / Log error]

### Severity

- [ ] Critical (Sistem tidak bisa dipakai)
- [ ] High (Fitur utama tidak berfungsi)
- [ ] Medium (Fitur berfungsi tapi ada workaround)
- [ ] Low (UI/UX minor, typo)

### Priority

- [ ] Urgent (Perlu fix segera)
- [ ] High (Fix dalam 1-2 hari)
- [ ] Medium (Fix dalam 1 minggu)
- [ ] Low (Bisa ditunda)
```

### Contoh Laporan Bug yang Baik

```markdown
## 🐛 Bug Report

**ID Bug**: BUG-042
**Tanggal**: 2025-01-15
**Dilaporkan oleh**: QA Reza
**Environment**: Staging
**Browser**: Chrome 120.0
**Device**: Laptop Windows 11

### Deskripsi

Tombol "Konfirmasi Profil" tidak berfungsi setelah mengisi biodata lengkap.

### Langkah Reproduce

1. Login sebagai calon mahasiswa
2. Buka halaman Profile
3. Isi semua field wajib dengan data valid
4. Klik tombol "Simpan"
5. Klik tombol "Konfirmasi Profil"
6. Klik "Ya, Saya Yakin" di modal konfirmasi

### Expected Result

- Status pendaftaran berubah menjadi "PROFILE_CONFIRMED"
- Muncul notifikasi "Profil berhasil dikonfirmasi"
- Tombol edit profile menjadi disabled

### Actual Result

- Tidak ada response setelah klik "Ya, Saya Yakin"
- Modal tidak tertutup
- Status tetap "PROFILE_COMPLETE"
- Di Console DevTools muncul error: `TypeError: Cannot read properties of undefined (reading 'id')`

### Evidence

[Screenshot modal yang tidak tertutup]
[Log Console: `TypeError: Cannot read properties of undefined (reading 'id')`]

### Severity

- [x] High

### Priority

- [x] High
```

---

## 11. Regression Testing Checklist

Regression Testing = Test ulang fitur yang sudah berfungsi setelah developer melakukan perubahan (fix bug atau tambah fitur baru).

### Kapan Harus Regression?

- Setelah ada update/fix dari developer
- Setelah deploy ke staging
- Sebelum deploy ke production

### Checklist Regression (Jalankan Semua)

**Autentikasi:**

- [ ] Register akun baru
- [ ] Verifikasi email
- [ ] Login
- [ ] Logout
- [ ] Forgot password
- [ ] Reset password

**Portal Publik:**

- [ ] Homepage tampil normal
- [ ] Isi biodata lengkap
- [ ] Konfirmasi profil
- [ ] Upload 7 dokumen wajib
- [ ] Upload file > 2MB (harus ditolak)
- [ ] Lihat status di dashboard
- [ ] Download undangan wawancara
- [ ] Download LOA (jika diterima)

**Dashboard Admin:**

- [ ] Login admin
- [ ] Dashboard analytics tampil
- [ ] List pendaftar tampil
- [ ] Approve dokumen
- [ ] Reject dokumen
- [ ] Generate nomor ujian
- [ ] Assign ruang wawancara
- [ ] Kirim undangan email
- [ ] Set DITERIMA
- [ ] Set DITOLAK
- [ ] Export Excel

**API:**

- [ ] API login response normal
- [ ] API get profile normal
- [ ] API upload attachment normal
- [ ] API admin get students normal

---

## 12. Tips & Best Practices untuk QA Pemula

### 🎯 Mindset QA

1. **Jangan takut "merusak"** — Tugas QA memang mencari masalah, bukan membuat aplikasi terlihat bagus
2. **Berpikir seperti user** — Jangan hanya test "jalur bahagia", coba juga cara aneh/negatif
3. **Teliti** — Perhatikan typo, warna yang aneh, loading yang lama
4. **Dokumentasikan** — Screenshot setiap bug, catat langkah dengan jelas
5. **Komunikasi** — Jangan ragu tanya ke developer jika ada yang tidak dimengerti

### 📋 Tips Praktis

- **Test satu fitur sampai tuntas** sebelum pindah ke fitur lain
- **Pakai data test yang konsisten** — Jangan ganti-ganti email/password setiap test
- **Refresh browser** jika ada yang aneh, kadang cache menyebabkan masalah
- **Buka Console (F12)** saat testing untuk melihat error JavaScript
- **Test di berbagai ukuran layar** — Desktop, tablet, HP
- **Catat waktu response** — Jika loading > 5 detik, catat sebagai performance issue

### 🔧 Tools Gratis yang Bisa Digunakan

| Tool          | Fungsi               | Link             |
| ------------- | -------------------- | ---------------- |
| Lightshot     | Screenshot cepat     | app.prntscr.com  |
| OBS Studio    | Screen recording     | obsproject.com   |
| Postman       | API Testing          | postman.com      |
| Google Sheets | Test case management | spreadsheets.new |
| Notion        | Dokumentasi bug      | notion.so        |

### 📞 Kapan Harus Tanya Developer?

- Fitur belum jelas cara kerjanya
- Tidak yakin apakah suatu behavior adalah bug atau memang sengaja
- Butuh data test khusus
- Ingin memahami logika bisnis yang kompleks

---

## ✅ Quick Start untuk QA Baru

Hari 1-2: Baca panduan ini & kenalan dengan aplikasi
Hari 3-4: Test portal publik (register, login, profile, upload)
Hari 5-6: Test dashboard admin (verifikasi, wawancara, LOA)
Hari 7: End-to-end testing alur lengkap
Hari 8+: Regression testing setiap ada update

Selamat testing! 🧪✨
