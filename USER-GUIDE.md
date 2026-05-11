# CBT Admin Panel — User Guide

> **Versi:** 1.0  
> **Proyek:** Istiqlal Language Center (ILC) — Computer Based Test (CBT)  
> **Tanggal:** 11 Mei 2026  
> **Target Pembaca:** Admin, Finance Review, Academic Admin

---

## Daftar Isi

1. [Pengenalan](#1-pengenalan)
2. [Login & Autentikasi](#2-login--autentikasi)
3. [Navigasi Dashboard](#3-navigasi-dashboard)
4. [Manajemen User](#4-manajemen-user)
5. [Review Pembayaran](#5-review-pembayaran)
6. [Test Approvals](#6-test-approvals)
7. [Bank Soal](#7-bank-soal)
8. [Soal (Questions)](#8-soal-questions)
9. [Paket Ujian](#9-paket-ujian)
10. [Sesi Ujian](#10-sesi-ujian)
11. [Monitoring Ujian](#11-monitoring-ujian)
12. [Pelanggaran Ujian](#12-pelanggaran-ujian)
13. [Hasil Ujian & Export](#13-hasil-ujian--export)
14. [Activity Logs](#14-activity-logs)
15. [Analytics](#15-analytics)
16. [Pengaturan Global](#16-pengaturan-global)
17. [FAQ & Troubleshooting](#17-faq--troubleshooting)

---

## 1. Pengenalan

CBT Admin Panel adalah dashboard manajemen untuk sistem ujian berbasis komputer (Computer Based Test) milik **Istiqlal Language Center (ILC)**. Panel ini digunakan oleh tim admin untuk mengelola seluruh aspek ujian, mulai dari verifikasi akun peserta, review pembayaran, pengelolaan soal, pembuatan sesi ujian, monitoring real-time, hingga export hasil ujian.

### Peran Admin

| Peran | Tanggung Jawab Utama |
|-------|---------------------|
| **Admin Account** | Verifikasi akun user, manajemen user |
| **Finance Review** | Review dan approve/reject bukti pembayaran |
| **Academic Admin** | Kelola bank soal, soal, paket ujian, sesi ujian |
| **Super Admin** | Akses penuh ke semua fitur |

### Teknologi

- **Frontend:** Next.js 15 + TypeScript + Tailwind CSS
- **Backend API:** Laravel 11 (REST API dengan Sanctum Auth)
- **Charting:** Recharts
- **Rich Text Editor:** Tiptap / CKEditor

---

## 2. Login & Autentikasi

### 2.1 Akses Login

Buka halaman login melalui URL admin panel. Anda akan melihat tampilan login dengan dua kolom:

- **Kolom kiri:** Form login
- **Kolom kanan:** Informasi fitur panel (Payment proof, Question bank, Exam session, Result export)

### 2.2 Cara Login

1. Masukkan **Email** dan **Password** akun admin Anda
2. Klik tombol **Sign In**
3. Sistem akan memvalidasi kredensial dan memeriksa apakah akun memiliki role **Admin**
4. Jika berhasil, Anda akan diarahkan ke halaman Dashboard

### 2.3 Logout

- Klik menu **Logout** di bagian bawah sidebar navigasi
- Session token akan dihapus dan Anda akan kembali ke halaman login

### 2.4 Keamanan

- Token autentikasi disimpan di **HTTP-only cookie** (`cbt_admin_token`)
- Setiap request ke API memerlukan Bearer token
- Session akan expire jika token tidak valid

---

## 3. Navigasi Dashboard

### 3.1 Layout Panel

Panel admin memiliki layout dua kolom:

```
┌─────────────────────────────────────────────────────────┐
│  [LOGO ILC]  CBT Admin          Admin Workspace  [AD] │
│  Istiqlal Language Center                               │
├──────────┬──────────────────────────────────────────────┤
│          │                                              │
│  Sidebar │              Main Content Area               │
│  Nav     │                                              │
│          │                                              │
│  • Dashboard                                          │
│  • Payments                                           │
│  • Approvals                                          │
│  • Users                                              │
│  • Question Banks                                     │
│  • Questions                                          │
│  • Exam Packages                                      │
│  • Exam Sessions                                      │
│  • Exam Monitoring                                    │
│  • Exam Violations                                    │
│  • Exam Results                                       │
│  • Activity Logs                                      │
│  • Analytics                                          │
│  • Settings                                           │
│                                                       │
│  ─────────────────────                                │
│  Logout                                               │
│          │                                              │
└──────────┴──────────────────────────────────────────────┘
```

### 3.2 Menu Navigasi (Sidebar)

| Menu | Ikon | Deskripsi |
|------|------|-----------|
| **Dashboard** | LayoutDashboard | Ringkasan data pending dan statistik |
| **Payments** | ReceiptText | Review bukti pembayaran |
| **Approvals** | BadgeCheck | Kelola test approval |
| **Users** | Users | Manajemen akun peserta |
| **Question Banks** | BookOpenCheck | Kelola bank soal |
| **Questions** | ListChecks | Kelola soal per bank |
| **Exam Packages** | PackageCheck | Buat dan edit paket ujian |
| **Exam Sessions** | CalendarClock | Jadwal dan sesi ujian |
| **Exam Monitoring** | MonitorCheck | Pantau ujian real-time |
| **Exam Violations** | ShieldAlert | Log pelanggaran peserta |
| **Exam Results** | ChartNoAxesCombined | Hasil ujian dan export |
| **Activity Logs** | ScrollText | Audit trail aktivitas |
| **Analytics** | BarChart3 | Grafik dan statistik hasil |
| **Settings** | Settings | Pengaturan global ujian |

### 3.3 Header Area

- **Kiri:** Label halaman aktif (misal: "Admin Workspace — Dashboard")
- **Kanan:** Avatar admin dengan inisial "AD" dan nama "Admin CBT"
- **Mobile:** Menu navigasi horizontal di bawah header

---

## 4. Manajemen User

### 4.1 Akses

Menu: **Users** → `/admin/users`

### 4.2 Statistik

| Statistik | Deskripsi |
|-----------|-----------|
| **Pending** | Akun menunggu verifikasi |
| **Active** | Akun sudah aktif, siap login |
| **Rejected** | Akun ditolak |

### 4.3 Filter

- **Account Status:** `pending_verification` | `active` | `rejected`
- **Search:** Cari berdasarkan nama atau email

### 4.4 Tabel User

| Kolom | Keterangan |
|-------|-----------|
| Nama | Nama lengkap peserta |
| Email | Alamat email |
| Institusi | Asal institusi/sekolah |
| Status | Badge warna: kuning (pending), hijau (active), merah (rejected) |
| Aksi | Tombol detail/edit |

### 4.5 Verifikasi Akun

1. Buka halaman **Users**
2. Filter status ke **Pending Verification**
3. Klik nama user untuk melihat detail
4. Pada halaman detail, Anda dapat:
   - **Approve** → Akun menjadi aktif
   - **Reject** → Akum ditolak (perlu alasan)
   - **Reset Password** → Reset password user

### 4.6 Detail User

Halaman detail menampilkan:
- Informasi akun (nama, email, status, role)
- Riwayat pembayaran
- Riwayat test approval
- Riwayat sesi ujian

---

## 5. Review Pembayaran

### 5.1 Akses

Menu: **Payments** → `/admin/payment-proofs`

### 5.2 Statistik

| Statistik | Deskripsi |
|-----------|-----------|
| **Pending** | Bukti pembayaran dalam antrean review |
| **Approved** | Bukti sudah di-approve bulan ini |
| **Rejected** | Bukti ditolak (butuh alasan) |

### 5.3 Filter

- **Status:** `pending_review` | `approved` | `rejected`
- **User ID:** Filter berdasarkan user tertentu

### 5.4 Tabel Pembayaran

| Kolom | Keterangan |
|-------|-----------|
| User | Nama peserta |
| File | Nama file bukti pembayaran |
| Uploaded | Tanggal upload |
| Status | Badge: kuning (pending), hijau (approved), merah (rejected) |
| Aksi | Tombol review |

### 5.5 Review Bukti Pembayaran

1. Buka halaman **Payments**
2. Filter status ke **Pending Review**
3. Klik nama file untuk melihat preview
4. Pada halaman preview, Anda dapat:
   - **Preview gambar** → Lihat bukti pembayaran
   - **Approve** → Pembayaran diterima, approval tes akan dibuat
   - **Reject** → Pembayaran ditolak (wajib isi alasan)

### 5.6 Alur Pembayaran → Approval

```
User upload bukti → Admin review → Approve → Test Approval dibuat → User bisa ikut ujian
                              ↓
                        Reject → User upload ulang
```

---

## 6. Test Approvals

### 6.1 Akses

Menu: **Approvals** → `/admin/test-approvals`

### 6.2 Konsep

Sistem menggunakan konsep **"One Payment, One Test"** — setiap pembayaran yang di-approve akan menghasilkan satu test approval yang bisa digunakan peserta untuk mengikuti satu sesi ujian.

### 6.3 Statistik

| Statistik | Deskripsi |
|-----------|-----------|
| **Available** | Approval tersedia, belum dipakai |
| **Consumed** | Approval sudah digunakan untuk ujian |
| **Expired** | Approval sudah tidak berlaku |

### 6.4 Filter

- **Status:** `available` | `consumed` | `expired`
- **User ID:** Filter berdasarkan user

### 6.5 Tabel Approval

| Kolom | Keterangan |
|-------|-----------|
| Kode | Kode approval unik |
| User | Nama peserta pemilik |
| Payment | ID pembayaran terkait |
| Status | Badge: hijau (available), abu (consumed), merah (expired) |
| Aksi | Tombol detail |

### 6.6 Assign Manual

Admin dapat secara manual memberikan test approval ke user:
1. Buka halaman **Approvals**
2. Klik tombol **Assign Manual**
3. Pilih user dan tentukan jumlah approval
4. Klik **Assign**

---

## 7. Bank Soal

### 7.1 Akses

Menu: **Question Banks** → `/admin/question-banks`

### 7.2 Statistik

| Statistik | Deskripsi |
|-----------|-----------|
| **Banks** | Total bank soal |
| **Active** | Bank soal yang aktif/ready |
| **Questions** | Total jumlah soal di semua bank |

### 7.3 Tabel Bank Soal

| Kolom | Keterangan |
|-------|-----------|
| Code | Kode unik bank soal |
| Title | Judul bank soal |
| Description | Deskripsi |
| Status | Badge: hijau (active), abu (inactive) |
| Aksi | Edit / Delete |

### 7.4 Membuat Bank Soal Baru

1. Klik tombol **New Question Bank**
2. Isi form:
   - **Code** — Kode unik (wajib, tidak bisa diubah setelah dibuat)
   - **Title** — Judul bank soal
   - **Description** — Deskripsi opsional
   - **Active** — Centang untuk mengaktifkan
3. Klik **Create**

### 7.5 Edit Bank Soal

1. Klik tombol **Edit** pada baris bank soal
2. Ubah field yang diinginkan (Code tidak bisa diubah)
3. Klik **Save changes**

> **Catatan:** Bank soal yang sudah digunakan dalam paket ujian tidak bisa dihapus.

---

## 8. Soal (Questions)

### 8.1 Akses

Menu: **Questions** → `/admin/questions`

### 8.2 Statistik

| Statistik | Deskripsi |
|-----------|-----------|
| **Text** | Soal pilihan ganda teks biasa |
| **Audio** | Soal dengan audio (limited plays) |
| **Media** | Soal dengan gambar atau mixed |

### 8.3 Filter

- **Question Bank:** Filter berdasarkan bank soal
- **Section Type:** `listening` | `structure` | `reading`
- **Question Type:** `multiple_choice_text` | `multiple_choice_image` | `multiple_choice_audio` | `multiple_choice_mixed`
- **Search:** Cari berdasarkan stem soal

### 8.4 Tabel Soal

| Kolom | Keterangan |
|-------|-----------|
| Stem | Isi pertanyaan (HTML di-strip) |
| Section | Listening / Structure / Reading |
| Type | Tipe soal dengan badge warna |
| Options | Jumlah pilihan jawaban |
| Aksi | Edit / Delete |

### 8.5 Membuat Soal Baru

1. Klik tombol **New Question**
2. Isi form soal:

#### Informasi Dasar
| Field | Keterangan |
|-------|-----------|
| Question Bank | Pilih bank soal |
| Section Type | Listening / Structure / Reading |
| Question Type | Text / Image / Audio / Mixed |
| Difficulty | Easy / Medium / Hard |

#### Konten Soal
| Field | Keterangan |
|-------|-----------|
| Stem (Pertanyaan) | Rich text editor, bisa format HTML |
| Explanation (Pembahasan) | Rich text editor |
| Audio Max Play Count | Maksimal pemutaran audio (untuk soal audio) |

#### Media (opsional)
| Field | Keterangan |
|-------|-----------|
| Image | Upload gambar (max 2MB) |
| Audio | Upload file audio (max 2MB) |

#### Pilihan Jawaban
- Minimal 2 opsi, maksimal 5 opsi
- Setiap opsi menggunakan rich text editor
- **Centang** satu opsi sebagai jawaban benar

3. Klik **Create question**

### 8.6 Import Soal

1. Buka halaman **Questions**
2. Klik tombol **Import**
3. Upload file Excel/CSV dengan format yang sesuai
4. Sistem akan memvalidasi dan mengimpor soal secara batch

### 8.7 Tips Soal Audio

- Soal audio memiliki batasan pemutaran (Audio Max Play Count)
- Peserta tidak bisa mengulang audio melebihi batas
- Audio akan diputar otomatis saat soal ditampilkan

---

## 9. Paket Ujian

### 9.1 Akses

Menu: **Exam Packages** → `/admin/exam-packages`

### 9.2 Statistik

| Statistik | Deskripsi |
|-----------|-----------|
| **Packages** | Total paket ujian |
| **Active** | Paket yang siap digunakan |
| **Inactive** | Paket yang tidak aktif |

### 9.3 Tabel Paket Ujian

| Kolom | Keterangan |
|-------|-----------|
| Code | Kode unik paket |
| Title | Judul paket |
| Duration | Durasi ujian (menit) |
| Banks | Mapping bank soal per section |
| Aksi | Edit / Delete |

### 9.4 Membuat Paket Ujian Baru

1. Klik tombol **New Exam Package**
2. Isi form:

#### Informasi Package
| Field | Keterangan | Default |
|-------|-----------|---------|
| Code | Kode unik (tidak bisa diubah) | — |
| Title | Judul paket | — |
| Description | Deskripsi opsional | — |
| Duration (menit) | Durasi ujian | 120 |
| Max Tab Switch | Batas pindah tab browser | 3 |
| Max Fullscreen Exit | Batas keluar fullscreen | 3 |

#### Opsi Keamanan
| Opsi | Keterangan | Default |
|------|-----------|---------|
| Shuffle Questions | Acak urutan soal | ✓ |
| Shuffle Options | Acak urutan pilihan jawaban | ✓ |
| Active | Paket aktif/tidak | ✓ |

#### Bank Mapping
Pilih bank soal yang akan digunakan per section:

| Field | Keterangan |
|-------|-----------|
| Question Bank | Pilih dari daftar bank soal |
| Section Type | Listening / Structure / Reading |
| Question Count | Jumlah soal yang diambil dari bank |
| Sort Order | Urutan section |

3. Klik **Create package**

### 9.5 Contoh Mapping

```
Package: TOEFL Simulation A
├── Section 1: Listening — Bank: LISTENING_A — 50 soal
├── Section 2: Structure — Bank: STRUCTURE_A — 40 soal
└── Section 3: Reading — Bank: READING_A — 50 soal
Total: 140 soal, 120 menit
```

---

## 10. Sesi Ujian

### 10.1 Akses

Menu: **Exam Sessions** → `/admin/exam-sessions`

### 10.2 Statistik

| Statistik | Deskripsi |
|-----------|-----------|
| **Published** | Sesi sedang berjalan/dibuka |
| **Draft** | Sesi belum dipublish |
| **Closed** | Sesi sudah selesai/ditutup |

### 10.3 Filter

- **Status:** `published` | `draft` | `closed`
- **Date:** Filter berdasarkan tanggal

### 10.4 Tabel Sesi Ujian

| Kolom | Keterangan |
|-------|-----------|
| Code | Kode unik sesi |
| Title | Judul sesi |
| Waktu | Tanggal dan jam sesi |
| Status | Badge: hijau (published), kuning (draft), abu (closed) |
| Aksi | Edit / Publish / Close / Detail |

### 10.5 Membuat Sesi Ujian Baru

1. Klik tombol **New Exam Session**
2. Isi form:

#### Informasi Sesi
| Field | Keterangan |
|-------|-----------|
| Code | Kode unik sesi |
| Title | Judul sesi |
| Exam Package | Pilih paket ujian |
| Access Code | Kode akses opsional untuk peserta |

#### Jadwal
| Field | Keterangan |
|-------|-----------|
| Session Date | Tanggal pelaksanaan |
| Start Time | Jam mulai |
| End Time | Jam selesai |

#### Pengaturan
| Field | Keterangan | Default |
|-------|-----------|---------|
| Duration (menit) | Durasi ujian | 120 |
| Max Participants | Kuota maksimal peserta | — |
| Show Result to User | Tampilkan hasil ke peserta | ✓ |
| Auto Generate Enabled | Generate hasil otomatis | — |

3. Klik **Create session**

### 10.6 Status Sesi dan Transisi

```
Draft → Publish → Running → Close → Finished
         ↓
      Cancel
```

| Status | Keterangan | Aksi yang Tersedia |
|--------|-----------|-------------------|
| **Draft** | Sesi belum aktif | Publish, Edit, Delete |
| **Published** | Sesi dibuka, peserta bisa daftar | Close, Monitor |
| **Closed** | Sesi ditutup, tidak bisa daftar | Finish |
| **Finished** | Sesi selesai, hasil bisa dilihat | — |
| **Cancelled** | Sesi dibatalkan | — |

### 10.7 Publish Sesi

1. Buka detail sesi dengan status **Draft**
2. Klik tombol **Publish**
3. Sesi akan dibuka dan peserta bisa mendaftar

### 10.8 Menutup Sesi

1. Buka detail sesi dengan status **Published**
2. Klik tombol **Close**
3. Sesi ditutup, peserta tidak bisa mendaftar lagi

### 10.9 Peserta Sesi

Pada halaman detail sesi, tab **Participants** menampilkan:
- Daftar peserta yang terdaftar
- Status pendaftaran (registered, approved, rejected)
- Tombol untuk approve/reject pendaftaran manual

---

## 11. Monitoring Ujian

### 11.1 Akses

Menu: **Exam Monitoring** → `/admin/monitoring`

### 11.2 Statistik

| Statistik | Deskripsi |
|-----------|-----------|
| **Running** | Attempt yang sedang berjalan (heartbeat aktif) |
| **Submitted** | Attempt yang sudah disubmit hari ini |
| **Violations** | Total pelanggaran yang perlu diaudit |

### 11.3 Filter

- **Session ID:** Filter berdasarkan sesi
- **Status:** `in_progress` | `submitted` | `auto_submitted` | `expired`

### 11.4 Tabel Monitoring

| Kolom | Keterangan |
|-------|-----------|
| Peserta | Nama user |
| Session | Kode sesi |
| Progress | Soal ke-X dari Y |
| Status | Badge: hijau (in_progress), biru (submitted), kuning (auto_submitted), merah (expired) |
| Aksi | Tombol detail |

### 11.5 Detail Monitoring (Live)

Klik tombol **Detail** untuk melihat informasi real-time:

#### Live Status Bar
- Indikator **Live** dengan animasi pulse hijau
- **Countdown timer** yang berjalan real-time
- Tombol **Refresh** untuk update manual

#### Progress
- Bar progress dengan persentase
- Soal ke-X dari Y total soal

#### Info Grid
| Info | Keterangan |
|------|-----------|
| Participant | Nama dan email peserta |
| Status | Status attempt saat ini |
| Remaining | Waktu tersisa (live countdown) |
| Violations | Jumlah pelanggaran (merah jika > 0) |

#### Session Info
| Info | Keterangan |
|------|-----------|
| Session | Kode dan judul sesi |
| Package | Nama paket ujian |
| Started at | Waktu mulai |
| Ends at | Waktu selesai |
| Last activity | Aktivitas terakhir |
| Submitted at | Waktu submit (jika sudah) |

#### Technical Info
| Info | Keterangan |
|------|-----------|
| IP Address | Alamat IP peserta |
| User Agent | Browser dan device |

#### Auto-Refresh
- Data di-refresh otomatis setiap **30 detik** untuk attempt yang aktif
- Countdown timer berjalan setiap detik

### 11.6 Related Links

Dari halaman detail monitoring, Anda bisa langsung menuju:
- **View violations** → Lihat pelanggaran attempt ini
- **View results** → Lihat hasil ujian sesi ini

---

## 12. Pelanggaran Ujian

### 12.1 Akses

Menu: **Exam Violations** → `/admin/violations`

### 12.2 Statistik

| Statistik | Deskripsi |
|-----------|-----------|
| **Violations** | Total pelanggaran |
| **High** | Pelanggaran severity tinggi |
| **Medium** | Pelanggaran severity sedang |

### 12.3 Filter

- **Attempt ID:** Filter berdasarkan attempt
- **Type:** Jenis pelanggaran

### 12.4 Tabel Pelanggaran

| Kolom | Keterangan |
|-------|-----------|
| Attempt | ID attempt |
| Type | Jenis pelanggaran |
| Severity | Badge: merah (high), kuning (medium), biru (low) |
| Action | Tindakan yang diambil sistem |
| Waktu | Timestamp pelanggaran |
| Aksi | Tombol detail |

### 12.5 Jenis Pelanggaran

| Jenis | Deskripsi | Severity |
|-------|-----------|----------|
| **Tab Switch** | Peserta pindah tab/browser | Medium/High |
| **Fullscreen Exit** | Peserta keluar dari fullscreen | Medium/High |
| **Copy Paste** | Peserta mencoba copy-paste | High |
| **Right Click** | Peserta klik kanan | Medium |
| **Dev Tools** | Developer tools terbuka | High |
| **Multiple Login** | Login dari device lain | High |

### 12.6 Auto-Submit

Jika pelanggaran melebihi batas yang ditentukan:
- Sistem akan **auto-submit** attempt
- Status berubah menjadi `auto_submitted`
- Hasil tetap tersimpan dan bisa dinilai

---

## 13. Hasil Ujian & Export

### 13.1 Akses

Menu: **Exam Results** → `/admin/results`

### 13.2 Statistik

| Statistik | Deskripsi |
|-----------|-----------|
| **Results** | Total hasil ujian hari ini |
| **Average** | Rata-rata skor total |
| **Hidden** | Hasil yang belum dipublish |

### 13.3 Filter

- **Session ID:** Filter berdasarkan sesi

### 13.4 Tabel Hasil

| Kolom | Keterangan |
|-------|-----------|
| Peserta | Nama user |
| Session | Kode sesi |
| Score | Total skor |
| Status | Badge: hijau (published), kuning (hidden) |
| Aksi | Detail / Publish |

### 13.5 Detail Hasil

Halaman detail menampilkan:
- **Total Score** — Skor keseluruhan
- **Listening Score** — Skor section listening
- **Structure Score** — Skor section structure
- **Reading Score** — Skor section reading
- **Correct Count** — Jumlah jawaban benar
- **Wrong Count** — Jumlah jawaban salah
- **Unanswered Count** — Jumlah tidak dijawab
- **Published At** — Waktu hasil dipublish

### 13.6 Publish Hasil

1. Buka halaman **Results**
2. Filter status ke **Hidden**
3. Klik tombol **Publish** pada hasil yang ingin ditampilkan
4. Peserta akan bisa melihat hasilnya di dashboard

### 13.7 Export Excel

1. Buka halaman **Results**
2. Klik tombol **Export Excel**
3. Pilih filter (opsional):
   - Sesi tertentu
   - Rentang tanggal
4. File Excel akan di-generate dan di-download

### 13.8 Format Export

File Excel berisi kolom:
- Nama Peserta
- Email
- Sesi
- Paket
- Total Score
- Listening Score
- Structure Score
- Reading Score
- Benar / Salah / Kosong
- Status Publish
- Tanggal Submit

---

## 14. Activity Logs

### 14.1 Akses

Menu: **Activity Logs** → `/admin/activity-logs`

### 14.2 Statistik

| Statistik | Deskripsi |
|-----------|-----------|
| **Logs** | Total log aktivitas |
| **Created** | Log create hari ini |
| **Updated** | Log update hari ini |

### 14.3 Filter

- **Log Name:** Nama log
- **Event:** `created` | `updated` | `deleted`
- **Causer ID:** User yang melakukan aksi
- **Subject Type:** Tipe entitas yang terkena aksi
- **Subject ID:** ID entitas

### 14.4 Tabel Activity Logs

| Kolom | Keterangan |
|-------|-----------|
| Causer | Nama user/system |
| Event | Created / Updated / Deleted |
| Subject | Tipe entitas |
| Description | Deskripsi aktivitas |
| Waktu | Timestamp |
| Aksi | Tombol detail |

### 14.5 Gunakan Activity Logs untuk

- Audit perubahan data
- Melacak siapa yang melakukan aksi tertentu
- Troubleshooting masalah data

---

## 15. Analytics

### 15.1 Akses

Menu: **Analytics** → `/admin/analytics`

### 15.2 Tab Analytics

| Tab | Deskripsi |
|-----|-----------|
| **Session** | Analisis per sesi ujian |
| **Package** | Analisis per paket ujian |
| **Section** | Analisis per section (Listening/Structure/Reading) |

### 15.3 Grafik

Grafik batang menampilkan **Average Score** per kategori yang dipilih:
- Warna batang berbeda per item
- Tooltip menampilkan nilai dan jumlah peserta

### 15.4 Tabel Detail

| Kolom | Keterangan |
|-------|-----------|
| Session/Package/Section | Nama kategori |
| Participants | Jumlah peserta |
| Avg Score | Rata-rata skor |
| Listening | Rata-rata skor listening |
| Structure | Rata-rata skor structure |
| Reading | Rata-rata skor reading |
| Highest | Skor tertinggi |
| Lowest | Skor terendah |
| Pass | Jumlah lulus |
| Fail | Jumlah tidak lulus |

### 15.5 Interpretasi Data

- **Avg Score tinggi + Pass banyak** → Sesi/paket sesuai level peserta
- **Avg Score rendah + Fail banyak** → Sesi/paket terlalu sulit
- **Section dengan skor terendah** → Area yang perlu perhatian lebih

---

## 16. Pengaturan Global

### 16.1 Akses

Menu: **Settings** → `/admin/settings`

### 16.2 Default Numerik

| Pengaturan | Deskripsi | Default |
|-----------|-----------|---------|
| **Durasi Default (menit)** | Durasi ujian default | 120 |
| **Max Tab Switch Default** | Batas pindah tab default | 3 |
| **Max Fullscreen Exit Default** | Batas keluar fullscreen default | 3 |

### 16.3 Default Boolean

| Pengaturan | Deskripsi | Default |
|-----------|-----------|---------|
| **Shuffle Questions Default** | Acak soal default | ✓ |
| **Shuffle Options Default** | Acak pilihan jawaban default | ✓ |
| **Show Result to User Default** | Tampilkan hasil ke user default | ✓ |
| **Auto Generate Enabled Default** | Generate hasil otomatis default | — |
| **Auto Submit on Violation Limit** | Auto-submit saat pelanggaran melebihi batas | ✓ |

### 16.4 Cara Mengubah Pengaturan

1. Buka halaman **Settings**
2. Ubah nilai yang diinginkan
3. Klik **Simpan Pengaturan**
4. Nilai default akan diterapkan saat membuat package/session baru

> **Catatan:** Perubahan pengaturan tidak mempengaruhi package/session yang sudah dibuat sebelumnya.

---

## 17. FAQ & Troubleshooting

### Q: Dashboard tidak bisa dimuat?
**A:** Periksa koneksi ke backend API. Pastikan token autentikasi masih valid. Coba logout dan login kembali.

### Q: Preview bukti pembayaran tidak muncul?
**A:** Pastikan file masih tersedia di storage. Cek apakah API media accessible.

### Q: Soal audio tidak bisa di-upload?
**A:** Pastikan ukuran file tidak melebihi **2MB**. Format yang didukung: MP3, WAV.

### Q: Sesi tidak bisa di-publish?
**A:** Pastikan:
- Exam package sudah dipilih
- Tanggal dan waktu sesi valid
- Paket ujian masih aktif

### Q: Peserta tidak bisa mendaftar ke sesi?
**A:** Periksa:
- Status sesi harus **Published**
- Kuota peserta belum penuh
- Peserta memiliki test approval yang available
- Akun peserta sudah aktif

### Q: Hasil ujian tidak muncul di analytics?
**A:** Analytics hanya menampilkan data setelah ada hasil ujian yang di-submit. Pastikan peserta sudah menyelesaikan ujian.

### Q: Export Excel gagal?
**A:** Coba dengan jumlah data yang lebih kecil (filter sesi tertentu). Jika masih gagal, hubungi tim teknis.

### Q: Monitoring tidak update real-time?
**A:** Auto-refresh berjalan setiap 30 detik. Klik tombol **Refresh** untuk update manual. Jika data tidak update, mungkin attempt sudah selesai.

### Q: Bagaimana cara reset password user?
**A:** Buka halaman **Users** → Klik detail user → Klik tombol **Reset Password**. Password baru akan di-generate otomatis.

### Q: Apa yang terjadi jika peserta keluar dari fullscreen?
**A:** Sistem akan mencatat sebagai pelanggaran. Jika melebihi batas **Max Fullscreen Exit**, attempt akan di-auto-submit.

### Q: Bisakah saya edit soal yang sudah digunakan dalam ujian?
**A:** Bisa, tapi perubahan tidak akan mempengaruhi attempt yang sudah berjalan. Hanya attempt baru yang akan menggunakan versi soal terbaru.

---

## Lampiran

### A. Status Badge Warna

| Warna | Status | Arti |
|-------|--------|------|
| 🟢 Hijau | Success, Active, Approved, Published, Completed | Positif / Selesai |
| 🟡 Kuning | Warning, Pending, Draft, Hidden | Perhatian / Menunggu |
| 🔴 Merah | Danger, Rejected, Failed, Expired, Cancelled | Negatif / Error |
| 🔵 Biru | Info, Submitted, Consumed | Informasi |
| ⚪ Abu | Neutral, Inactive, Closed | Netral |

### B. Alur Kerja Utama

```
1. User Register → Status: Pending Verification
2. Admin verifikasi akun → Status: Active
3. User upload bukti pembayaran → Status: Pending Review
4. Finance review & approve → Test Approval dibuat (Available)
5. Admin buat Exam Package + Exam Session → Status: Draft
6. Admin publish session → Status: Published
7. User daftar ke session menggunakan Approval
8. User ikut ujian → Attempt: In Progress
9. User submit / Auto-submit → Attempt: Submitted
10. Admin publish hasil → Status: Published
11. User lihat hasil di dashboard
```

### C. Kontak Dukungan

Jika mengalami masalah teknis yang tidak bisa diselesaikan, hubungi tim pengembang dengan menyertakan:
- Screenshot error
- Langkah-langkah yang dilakukan
- Browser dan versi yang digunakan
- Waktu kejadian

---

*Dokumen ini akan diperbarui secara berkala sesuai dengan perkembangan fitur aplikasi.*
