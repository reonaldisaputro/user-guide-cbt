# 🧪 Testing Guide — Weighted Scoring System

## Ringkasan Fitur

Sistem scoring baru dengan **bobot per section**:

- **Listening (Fahm al-Masmu)**: bobot 2.5 per soal
- **Structure (Fahm al-Kitabah)**: bobot 1.0 per soal
- **Reading (Fahm al-Maqru)**: bobot 1.0 per soal

**Contoh:** 20 soal listening + 50 soal biasa = max **100 point**

---

## 📋 Pre-requisite

Pastikan semua service berjalan:

```bash
# 1. BE-CBT (Backend)
cd /var/www/be-cbt
php artisan migrate        # Jalankan migration baru
php artisan serve          # atau via nginx/php-fpm

# 2. CBT-ADMIN (Admin Panel)
cd /var/www/cbt-admin
pnpm build && pnpm start   # atau npm run dev

# 3. FE-CBT (Participant Frontend)
cd /var/www/fe-cbt
npm run build && npm start # atau npm run dev
```

**Pastikan database sudah migrate:**

```bash
cd /var/www/be-cbt
php artisan migrate:status
# Harus ada: 2026_05_27_100000_add_score_weight_and_section_scores.php → Ran
```

---

## 🧪 Test Case 1: Setup Exam Package dengan Bobot

### Langkah 1: Login ke Admin Panel

1. Buka `https://admin-panel-url/login`
2. Login dengan akun admin

### Langkah 2: Buat/Edit Exam Package

1. Buka menu **Exam Packages**
2. Klik **"Buat exam package baru"** (atau edit existing)

### Langkah 3: Isi Bank Soal dengan Bobot

1. Scroll ke bagian **"Daftar Bank Soal"**
2. Tambah bank soal:
   | Bank | Jumlah Soal | Bobot |
   |------|-------------|-------|
   | Bank Listening (Fahm al-Masmu) | 20 | **2.5** |
   | Bank Structure (Fahm al-Kitabah) | 25 | **1.0** |
   | Bank Reading (Fahm al-Maqru) | 25 | **1.0** |

3. **Verifikasi UI:**
   - [ ] Kolom "Bobot" tampil di tabel bank mapping
   - [ ] Saat pilih bank Listening, default bobot = 2.5
   - [ ] Saat pilih bank lain, default bobot = 1.0
   - [ ] Bobot bisa diubah manual

4. Klik **"Create package"**

### Langkah 4: Verifikasi API Response

```bash
# Cek response dari backend
curl -H "Authorization: Bearer ADMIN_TOKEN" \
  https://be-cbt.miftadigital.cloud/api/admin/exam-packages/1

# Expected: banks array punya field "score_weight"
# {
#   "banks": [
#     { "question_bank_id": 1, "question_count": 20, "score_weight": 2.5, ... },
#     { "question_bank_id": 2, "question_count": 25, "score_weight": 1.0, ... }
#   ]
# }
```

### Langkah 5: Verifikasi Detail Page

1. Klik exam package yang baru dibuat
2. **Verifikasi:** di section "Banks", muncul info bobot:
   - `Bank Listening (20 soal, bobot 2.5)`
   - `Bank Structure (25 soal, bobot 1)`

---

## 🧪 Test Case 2: Buat Exam Session & Register Peserta

### Langkah 1: Buat Exam Session

1. Buka menu **Exam Sessions** → **New**
2. Pilih exam package yang sudah di-set bobot
3. Isi jadwal, durasi, dll
4. Publish session

### Langkah 2: Register Peserta

1. Buka detail exam session
2. Tambah peserta (manual assign atau auto-generate)
3. Approve payment/test approval untuk peserta

---

## 🧪 Test Case 3: Peserta Mengerjakan Ujian

### Langkah 1: Login sebagai Peserta

1. Buka `https://fe-cbt-url/login`
2. Login dengan akun peserta yang sudah di-register

### Langkah 2: Mulai Ujian

1. Di dashboard, klik session yang aktif
2. Ikuti instruksi → **Start Exam**

### Langkah 3: Jawab Beberapa Soal

Jawab dengan pattern ini (untuk verifikasi scoring):

| Section               | Soal        | Jawaban           | Expected Score      |
| --------------------- | ----------- | ----------------- | ------------------- |
| Listening (bobot 2.5) | 20 soal     | Benar 15, Salah 5 | 15 × 2.5 = **37.5** |
| Structure (bobot 1.0) | 25 soal     | Benar 20, Salah 5 | 20 × 1.0 = **20.0** |
| Reading (bobot 1.0)   | 25 soal     | Benar 18, Salah 7 | 18 × 1.0 = **18.0** |
| **Total**             | **70 soal** | **Benar 53**      | **75.5**            |

### Langkah 4: Submit Ujian

1. Klik **Submit Final**
2. Tunggu redirect ke `/exam/completed`

---

## 🧪 Test Case 4: Verifikasi Hasil di Participant Frontend

### Halaman `/exam/completed`

**Verifikasi UI:**

- [ ] Total Score menampilkan angka (bukan persentase)
- [ ] Muncul **Section Scores**:
  - Listening: `37.5`
  - Structure: `20.0`
  - Reading: `18.0`
- [ ] Benar/Salah/Tidak Dijawab tetap tampil

### Halaman `/exam/score?attempt_id=X`

**Verifikasi UI:**

- [ ] Total Score: `75.5`
- [ ] Section Scores muncul di atas breakdown Benar/Salah/Tidak Dijawab
- [ ] Listening (sky blue), Structure (violet), Reading (amber)

### Halaman `/exam/history`

- [ ] List hasil ujian menampilkan total score

---

## 🧪 Test Case 5: Verifikasi Hasil di Admin Panel

### Langkah 1: Buka Exam Results

1. Login admin → menu **Exam Results**
2. Cari result dari peserta yang baru ujian

### Langkah 2: List View

- [ ] Kolom "Nilai" menampilkan total score (bukan persentase)

### Langkah 3: Detail Result

1. Klik result tersebut
2. **Verifikasi:**
   - [ ] **Total Score** besar di atas: `75.5`
   - [ ] **Section Scores** muncul:
     - Listening (L): `37.5`
     - Structure (S): `20.0`
     - Reading (R): `18.0`
   - [ ] Answer Breakdown: Correct=53, Wrong=17, Unanswered=0
   - [ ] Metadata: Attempt ID, Published at

### Langkah 4: Export Excel (kalau ada)

1. Klik **Export** di halaman Results
2. Buka file Excel
3. **Verifikasi:** Total score sesuai (bukan selalu 0-100)

---

## 🧪 Test Case 6: Verifikasi Database (Backend)

### Langkah 1: Cek Exam Result

```bash
cd /var/www/be-cbt
php artisan tsql
```

```sql
-- Cek exam result terbaru
SELECT
  id,
  exam_attempt_id,
  total_score,
  listening_score,
  structure_score,
  reading_score,
  correct_count,
  wrong_count,
  unanswered_count
FROM exam_results
ORDER BY id DESC
LIMIT 1;

-- Expected:
-- total_score = 75.50
-- listening_score = 37.50
-- structure_score = 20.00
-- reading_score = 18.00
-- correct_count = 53
```

### Langkah 2: Cek Exam Attempt Questions (Snapshot)

```sql
-- Cek apakah score_weight tersimpan di snapshot
SELECT
  eat.id,
  eat.display_number,
  eat.score_weight,
  qb.section_type
FROM exam_attempt_questions eat
JOIN questions q ON eat.question_id = q.id
JOIN question_banks qb ON q.question_bank_id = qb.id
WHERE eat.exam_attempt_id = (SELECT exam_attempt_id FROM exam_results ORDER BY id DESC LIMIT 1)
ORDER BY eat.display_number
LIMIT 10;

-- Expected: score_weight = 2.5 untuk listening, 1.0 untuk lainnya
```

### Langkah 3: Cek Exam Answers (Score Value)

```sql
-- Cek score_value sesuai bobot
SELECT
  ea.id,
  ea.is_correct,
  ea.score_value,
  eat.score_weight
FROM exam_answers ea
JOIN exam_attempt_questions eat ON ea.exam_attempt_question_id = eat.id
WHERE ea.exam_attempt_id = (SELECT exam_attempt_id FROM exam_results ORDER BY id DESC LIMIT 1)
  AND ea.is_correct = 1
LIMIT 5;

-- Expected: score_value = score_weight (2.5 untuk listening, 1.0 untuk lainnya)
```

---

## 🧪 Test Case 7: Backward Compatibility

### Scenario: Exam Package Lama (tanpa bobot custom)

1. Cari exam package yang **belum di-edit** sejak deploy
2. Cek di database:

```sql
SELECT score_weight FROM exam_package_banks WHERE exam_package_id = ID_LAMA;
-- Expected: 1.00 (default)
```

3. Buat session dari package lama → peserta ujian → submit
4. **Verifikasi:**
   - [ ] Scoring tetap berjalan (total = jumlah benar × 1.0)
   - [ ] Tidak ada error

---

## 🧪 Test Case 8: Edge Cases

### Case 8a: Semua Jawaban Benar (Max Score)

| Section   | Soal   | Bobot | Benar  | Score     |
| --------- | ------ | ----- | ------ | --------- |
| Listening | 20     | 2.5   | 20     | 50.0      |
| Structure | 25     | 1.0   | 25     | 25.0      |
| Reading   | 25     | 1.0   | 25     | 25.0      |
| **Total** | **70** |       | **70** | **100.0** |

**Verifikasi:** Total Score = **100.0**

### Case 8b: Semua Jawaban Salah (Min Score)

**Verifikasi:** Total Score = **0.0**, semua section score = **0**

### Case 8c: Tidak Menjawab Semua (Unanswered)

**Verifikasi:** Total Score = **0.0**, Unanswered = total soal

### Case 8d: Hanya 1 Bank Soal (misal hanya Listening)

1. Buat exam package dengan 1 bank (Listening, 10 soal, bobot 2.5)
2. Peserta ujian
3. **Verifikasi:**
   - [ ] `structure_score` = null (tidak tampil)
   - [ ] `reading_score` = null (tidak tampil)
   - [ ] Hanya `listening_score` dan `total_score` yang tampil

---

## 🧪 Test Case 9: API Endpoint Test (Backend)

```bash
# 1. Login admin untuk dapat token
ADMIN_TOKEN=$(curl -s -X POST https://be-cbt.miftadigital.cloud/api/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@example.com","password":"password"}' \
  | jq -r '.data.token')

# 2. Cek exam package detail (harus ada score_weight)
curl -s -H "Authorization: Bearer $ADMIN_TOKEN" \
  https://be-cbt.miftadigital.cloud/api/admin/exam-packages/1 \
  | jq '.data.banks[] | {id, question_count, score_weight}'

# 3. Cek result list (harus ada section scores)
curl -s -H "Authorization: Bearer $ADMIN_TOKEN" \
  https://be-cbt.miftadigital.cloud/api/admin/results \
  | jq '.data.items[0] | {total_score, listening_score, structure_score, reading_score}'

# 4. Cek user result (participant API)
USER_TOKEN=$(curl -s -X POST https://be-cbt.miftadigital.cloud/api/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"password"}' \
  | jq -r '.data.token')

curl -s -H "Authorization: Bearer $USER_TOKEN" \
  https://be-cbt.miftadigital.cloud/api/my/results \
  | jq '.data[0] | {total_score, listening_score, structure_score, reading_score}'
```

---

## ❌ Troubleshooting

| Masalah                              | Kemungkinan Penyebab                | Solusi                                  |
| ------------------------------------ | ----------------------------------- | --------------------------------------- |
| `score_weight` tidak tersimpan       | Migration belum jalan               | `php artisan migrate`                   |
| Section scores tidak muncul di FE    | API tidak return section scores     | Cek `ExamResultResource.php`            |
| Total score masih percentage (0-100) | `ScoringService` belum update       | Cek `app/Services/ScoringService.php`   |
| Admin form tidak ada kolom bobot     | `BankMappingEditor` belum update    | Clear browser cache, rebuild cbt-admin  |
| Score tidak sesuai hitungan          | `score_weight` di snapshot = 1.00   | Exam package harus di-edit & save ulang |
| `listening_score` = null di DB       | Tidak ada soal listening di package | Cek bank mapping di exam package        |

---

## ✅ Checklist Akhir

- [ ] Migration berhasil jalan
- [ ] Admin bisa set bobot per bank soal
- [ ] Bobot default listening = 2.5, lainnya = 1.0
- [ ] Snapshot (exam_attempt_questions) menyimpan score_weight
- [ ] Exam answers menyimpan score_value sesuai bobot
- [ ] Exam results menyimpan total_score + section scores
- [ ] Participant frontend tampilkan section scores
- [ ] Admin panel tampilkan section scores
- [ ] Backward compatibility: package lama tetap jalan (bobot 1.0)
- [ ] Edge case: 1 bank soal saja tetap jalan

---

_Last Updated: 2026-05-27_
