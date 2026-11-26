# Plank

Aplikasi web manajemen tugas berbasis papan Kanban, dibuat dengan Laravel sebagai **Tugas Pengganti UTS**.

## Deskripsi Aplikasi

Plank membantu pengguna (terutama mahasiswa yang mengerjakan tugas kelompok) mengatur pekerjaan lewat papan Kanban. Setiap pengguna bisa membuat board, membagi pekerjaan ke dalam kolom seperti *To Do*, *In Progress*, dan *Done*, lalu memindahkan kartu tugas sesuai perkembangannya.

**Permasalahan yang diselesaikan:** tugas kelompok sering berantakan karena pembagian kerja dan progresnya hanya tercatat di chat. Dengan Plank, semua tugas, penanggung jawab, prioritas, dan tenggat tercatat di satu tempat dan statusnya terlihat jelas.

## Fitur Utama

**1. Authentication**
- Register, login, dan logout (Laravel Starter Kit / Breeze).
- Semua halaman board hanya bisa diakses setelah login.

**2. Manajemen Board** (CRUD)
- Membuat, melihat, mengubah, dan menghapus board.
- Dashboard menampilkan daftar board milik user dan board yang dibagikan ke user.

**3. Anggota Board**
- Pemilik board dapat menambahkan atau mengeluarkan anggota (berdasarkan email user yang sudah terdaftar).
- Hak akses: hanya pemilik yang boleh mengubah/menghapus board dan mengelola anggota; anggota hanya bisa bekerja di dalam board (diatur dengan Policy).

**4. Manajemen Kolom** (CRUD)
- Menambah, mengubah nama, menghapus, dan mengatur urutan kolom dalam board.

**5. Manajemen Task / Kartu** (CRUD)
- Menambah, mengubah, dan menghapus task.
- Data task: judul, deskripsi, prioritas (low/medium/high), tenggat, penanggung jawab (salah satu anggota board), dan label.
- Memindahkan task antar kolom (tombol pindah kiri/kanan atau dropdown kolom; drag & drop opsional jika waktu cukup).

**6. Manajemen Label** (CRUD)
- Membuat label berwarna per board (contoh: Backend, Frontend, Urgent), lalu memasang beberapa label ke satu task.

**7. Fitur Tambahan (sederhana)**
- Pencarian task berdasarkan judul dan filter berdasarkan prioritas atau penanggung jawab.
- Penanda visual untuk task yang sudah lewat tenggat.

> Tidak ada fitur upload gambar/file.

## Pemetaan dengan Kriteria Tugas

| Kriteria | Penerapan di Plank |
|---|---|
| Authentication | Laravel Breeze (register, login, logout) |
| CRUD minimal 3-4 tabel | `boards`, `board_columns`, `tasks`, `labels` |
| Routing & Controller | Resource controller: `BoardController`, `BoardColumnController`, `TaskController`, `LabelController`, `BoardMemberController` |
| Model, Migration, Eloquent | Relasi one-to-many dan many-to-many (lihat bagian database) |
| Form & Validasi | `FormRequest` / `$request->validate()` di setiap form tambah dan ubah |
| Blade / View | Layout Blade dengan komponen dan partial, tampilan memakai Bootstrap |
| Keamanan dasar | `@csrf` pada semua form, `@method` untuk PUT/DELETE, mass assignment dijaga dengan `$fillable` |
| Authorization | `BoardPolicy` dan middleware `auth` |
| Database | MySQL, struktur ada di `plank.sql` |

## Rancangan Database

Struktur lengkap ada di file [`plank.sql`](plank.sql).

| Tabel | Fungsi |
|---|---|
| `users` | Data pengguna (bawaan authentication Laravel) |
| `boards` | Papan Kanban, dimiliki satu user (`owner_id`) |
| `board_members` | Anggota yang tergabung di board (pivot `users` dan `boards`) |
| `board_columns` | Kolom di dalam board |
| `tasks` | Kartu tugas di dalam kolom |
| `labels` | Label berwarna milik board |
| `label_task` | Pivot antara `labels` dan `tasks` |

Tabel bawaan Laravel (`password_reset_tokens`, `sessions`, dll.) juga ikut dibuat oleh migration.

**Relasi antartabel:**
- `User` 1 - N `Board` (pemilik board)
- `User` N - N `Board` lewat `board_members` (anggota board)
- `Board` 1 - N `BoardColumn`
- `BoardColumn` 1 - N `Task`
- `Board` 1 - N `Label`
- `Task` N - N `Label` lewat `label_task`
- `Task` N - 1 `User` sebagai pembuat (`created_by`) dan sebagai penanggung jawab (`assigned_to`)

## Teknologi yang Digunakan

- PHP 8.2+ dan Laravel 11/12
- MySQL
- Blade Template
- Bootstrap 5
- Laravel Breeze (authentication)
- Composer dan npm
- Git dan GitHub

## Cara Menjalankan Aplikasi

1. **Clone repository**
   ```bash
   git clone <link-repository-github>
   cd plank
   ```

2. **Install dependency**
   ```bash
   composer install
   npm install && npm run build
   ```

3. **Salin file environment dan buat app key**
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

4. **Buat database di MySQL**, misalnya dengan nama `plank`, lalu atur koneksi di file `.env`:
   ```env
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=plank
   DB_USERNAME=root
   DB_PASSWORD=
   ```

5. **Jalankan migration dan seeder**
   ```bash
   php artisan migrate --seed
   ```
   Alternatif: import file `plank.sql` lewat phpMyAdmin atau MySQL CLI (sudah termasuk data contoh).

6. **Jalankan aplikasi**
   ```bash
   php artisan serve
   ```
   Buka `http://127.0.0.1:8000`.

**Akun contoh (dari seeder):**

| Email | Password |
|---|---|
| satu@example.com | password |
| dua@example.com | password |

> File `.env` tidak di-commit ke GitHub. Gunakan `.env.example` sebagai acuan.

## Pembagian Tugas Anggota

| Anggota | Tugas |
|---|---|
| Anggota 1 | Setup project dan authentication, tabel Board dan Board Member, CRUD Board, Policy/authorization, routing, README |
| Anggota 2 | Tabel Kolom, Task, dan Label, CRUD Kolom/Task/Label, Blade/View, validasi, fitur pindah task dan pencarian |

*(Sesuaikan dengan pekerjaan yang benar-benar dikerjakan masing-masing.)*

## Anggota Kelompok

| Nama | NIM | Kelas |
|---|---|---|
| (isi nama anggota 1) | (NIM) | (kelas) |
| (isi nama anggota 2) | (NIM) | (kelas) |
