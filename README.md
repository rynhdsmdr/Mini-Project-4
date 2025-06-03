Nama    : Riyan Hadi Samudra
NPM     : 2308107010068

Link Presentasi : https://youtu.be/SgcjbcDH5Jw

Sistem Booking Lapangan Olahraga
1. Deskripsi Singkat Aplikasi
Sistem Booking Lapangan Olahraga adalah aplikasi web berbasis Laravel yang dirancang untuk memfasilitasi proses pemesanan lapangan olahraga secara online. Aplikasi ini memungkinkan pengguna terdaftar untuk mencari dan memesan lapangan yang tersedia, sementara administrator memiliki kontrol penuh atas pengelolaan data lapangan, kategori lapangan, dan semua transaksi booking. Fokus utama aplikasi adalah pada kemudahan penggunaan, validasi data yang ketat, dan manajemen booking yang efisien, dengan antarmuka yang bersih dan responsif.
2. Penjelasan Code (Struktur & Fungsionalitas)
Aplikasi ini dibangun menggunakan arsitektur Model-View-Controller (MVC) yang merupakan standar di Laravel.
A. Struktur Utama Code:
Routes (routes/web.php):
Berfungsi sebagai "peta" aplikasi, mendefinisikan URL (endpoint) dan mengarahkan permintaan HTTP ke controller yang sesuai.
Public Routes: Home page, Login, Register.
Authenticated User Routes: Dashboard, Profile management, Booking Lapangan (/fields/book), Booking Saya (/my-bookings).
Admin-Specific Routes: Dilindungi oleh AdminMiddleware, mencakup Manajemen Tipe Lapangan (/field-types), Manajemen Lapangan (/fields), dan Manajemen Semua Booking (/admin/bookings). Penggunaan Route::resource memudahkan pembuatan CRUD otomatis.
Controllers (app/Http/Controllers/):
Bertanggung jawab untuk menerima permintaan (request) dari pengguna, memproses logika bisnis, berinteraksi dengan model, dan mengembalikan respons (misalnya, menampilkan view).
FieldTypeController.php: Mengelola operasi CRUD (Create, Read, Update, Delete) untuk kategori lapangan (misalnya Futsal, Badminton, Basket). Hanya bisa diakses oleh admin. Melakukan validasi input untuk nama dan deskripsi.
FieldController.php: Mengelola operasi CRUD untuk detail lapangan individual (nama, deskripsi, harga per jam, tipe lapangan, gambar). Hanya bisa diakses oleh admin. Menangani upload dan penyimpanan gambar lapangan.
BookingController.php: Ini adalah controller paling kompleks:
Untuk Pengguna:
createBookingForm(): Menampilkan form untuk membuat booking baru.
storeBooking(): Memproses data booking dari form. Logika penting di sini adalah pengecekan ketersediaan lapangan. Aplikasi memeriksa apakah ada booking yang sudah ada pada lapangan dan waktu yang sama untuk mencegah double booking. Juga menghitung total_price berdasarkan price_per_hour lapangan dan durasi booking.
myBookings(): Menampilkan daftar booking yang telah dibuat oleh pengguna yang sedang login.
cancelBooking(): Memungkinkan pengguna membatalkan booking mereka jika statusnya masih pending atau confirmed.
Untuk Admin:
adminIndex(): Menampilkan semua booking dari seluruh pengguna.
adminShow(): Menampilkan detail spesifik dari sebuah booking.
confirmBooking(): Mengubah status booking dari pending menjadi confirmed.
adminCancelBooking(): Memungkinkan admin membatalkan booking apa pun.
markAsPaid(): Mengubah status pembayaran menjadi paid dan status booking menjadi completed.
Models (app/Models/):
Representasi tabel di database dan menyediakan antarmuka untuk berinterinteraksi dengan database. Mereka juga mendefinisikan relasi antar tabel.
User.php: Mewakili tabel users. Ditambahkan kolom role (admin/user) dan relasi hasMany ke Booking.
FieldType.php: Mewakili tabel field_types. Memiliki relasi hasMany ke Field.
Field.php: Mewakili tabel fields. Memiliki relasi belongsTo ke FieldType dan hasMany ke Booking.
Booking.php: Mewakili tabel bookings. Memiliki relasi belongsTo ke User dan belongsTo ke Field. Ini menyimpan detail booking seperti tanggal, waktu mulai/akhir, durasi, total harga, dan status (pending, confirmed, cancelled, completed, unpaid, paid).
Views (resources/views/):
File template Blade yang bertanggung jawab untuk menampilkan data ke pengguna. Menggunakan layout Breeze (layouts/app.blade.php) untuk konsistensi UI.
Tersusun dalam folder-folder sesuai modul: field_types, fields, bookings, dan admin/bookings.
Setiap folder berisi file seperti index.blade.php (daftar), create.blade.php (form tambah), edit.blade.php (form edit), dan show.blade.php (detail).
Middleware (app/Http/Middleware/AdminMiddleware.php):
AdminMiddleware: Middleware kustom yang dibuat untuk memeriksa apakah pengguna yang sedang login memiliki role 'admin'. Jika tidak, permintaan akan dialihkan atau dihentikan dengan status 403 (Unauthorized). Ini memastikan bahwa hanya admin yang bisa mengakses rute dan fungsionalitas administratif.
Database Migrations (database/migrations/):
File-file yang mendefinisikan struktur tabel database (skema). Digunakan untuk membuat, memodifikasi, dan menghapus tabel.
add_role_to_users_table: Menambahkan kolom role ke tabel users.
create_field_types_table: Membuat tabel field_types (id, name, description).
create_fields_table: Membuat tabel fields (id, field_type_id, name, description, price_per_hour, image).
create_bookings_table: Membuat tabel bookings (id, user_id, field_id, booking_date, start_time, end_time, duration_hours, total_price, status, payment_status).
Database Seeders (database/seeders/):
Digunakan untuk mengisi tabel database dengan data awal yang berguna untuk pengembangan atau pengujian.
UserSeeder: Membuat user 'admin' dan 'user' awal.
FieldTypeSeeder: Mengisi beberapa tipe lapangan awal (Futsal, Badminton, Basket).
FieldSeeder: Mengisi beberapa data lapangan awal yang terkait dengan tipe lapangan.
B. Validasi Form:
Setiap kali data dikirimkan melalui form (misalnya saat membuat/mengedit tipe lapangan, lapangan, atau booking), Laravel melakukan validasi sisi server menggunakan Request->validate().
Contoh validasi: required, string, max:255, numeric, min:0, unique, exists, date, after_or_equal:today, date_format:H:i, after:start_time, image, mimes, max:2048.
Pesan error validasi akan ditampilkan secara otomatis di bawah input form yang bermasalah.
3. User Interface (UI)
Aplikasi ini memanfaatkan Laravel Breeze sebagai fondasi UI dan sistem otentikasi, yang secara default mengintegrasikan Tailwind CSS untuk styling. Ini menghasilkan antarmuka yang bersih, modern, dan responsif.
A. Umum (untuk Semua Pengguna):
Navigasi Bar: Header navigasi yang responsif, menampilkan link ke Dashboard, Profile, dan link khusus peran (Booking Lapangan / Tipe Lapangan, dll.) setelah login.
Form Otentikasi: Halaman registrasi, login, dan lupa password yang standar dan mobile-friendly.
Dashboard: Halaman utama setelah login, menyambut pengguna.
B. Antarmuka Pengguna Biasa (role: user):
Booking Lapangan (/fields/book):
Halaman form sederhana untuk memilih lapangan, tanggal, waktu mulai, dan waktu selesai.
Dropdown untuk memilih lapangan yang tersedia, menampilkan nama lapangan, tipe, dan harga per jam.
Input tanggal dan waktu untuk detail booking.
Fitur Real-time: Perhitungan estimasi total harga secara otomatis menggunakan JavaScript saat pengguna memilih lapangan dan memasukkan waktu, memberikan feedback instan.
Tombol "Booking Sekarang" untuk mengirimkan permintaan.
Pesan sukses/error (misalnya jika lapangan sudah dipesan pada waktu yang dipilih) ditampilkan di bagian atas halaman.
Booking Saya (/my-bookings):
Tabel yang menampilkan semua booking yang telah dibuat oleh pengguna yang sedang login.
Kolom-kolom meliputi: Nama Lapangan, Tipe Lapangan, Tanggal Booking, Waktu (mulai-selesai dan durasi), Total Harga, Status Booking (Pending, Confirmed, Cancelled, Completed - dengan indikator warna), Status Pembayaran (Unpaid, Paid - dengan indikator warna).
Tombol "Batalkan" tersedia untuk booking yang masih pending atau confirmed, memungkinkan pembatalan diri.
Paginasi untuk daftar booking yang panjang.
C. Antarmuka Administrator (role: admin):
Navigasi Admin: Link tambahan di navigasi bar ke "Tipe Lapangan", "Daftar Lapangan", dan "Semua Booking (Admin)".
Manajemen Tipe Lapangan (/field-types):
Halaman Indeks: Tabel yang menampilkan daftar semua tipe lapangan (Nama, Deskripsi).
Tombol "Tambah Tipe Lapangan" untuk navigasi ke form pembuatan.
Aksi: Tombol "Edit" dan "Hapus" untuk setiap tipe lapangan. Konfirmasi penghapusan via JavaScript.
Halaman Create/Edit: Form dengan input untuk Nama Tipe Lapangan dan Deskripsi.
Manajemen Lapangan (/fields):
Halaman Indeks: Tabel yang menampilkan daftar semua lapangan (Nama Lapangan, Tipe, Harga/Jam, Gambar Lapangan). Gambar lapangan ditampilkan sebagai thumbnail.
Tombol "Tambah Lapangan Baru".
Aksi: Tombol "Edit" dan "Hapus".
Halaman Create/Edit: Form dengan input untuk Tipe Lapangan (dropdown), Nama Lapangan, Deskripsi, Harga per Jam, dan input file untuk Gambar Lapangan. Saat edit, gambar yang sudah ada akan ditampilkan.
Manajemen Semua Booking (/admin/bookings):
Halaman Indeks: Tabel komprehensif yang menampilkan semua booking dari seluruh pengguna.
Kolom-kolom meliputi: Nama User, Lapangan (Nama & Tipe), Tanggal & Waktu, Total Harga, Status Booking (Pending, Confirmed, Cancelled, Completed), Status Pembayaran (Unpaid, Paid).
Aksi: Tombol "Detail" untuk melihat rincian booking.
Halaman Detail Booking (/admin/bookings/{id}):
Menampilkan semua informasi terkait booking secara rinci (detail pengguna, detail lapangan, detail booking).
Aksi Admin: Tombol-tombol untuk:
"Konfirmasi Booking" (jika status pending).
"Batalkan Booking" (jika belum dibatalkan).
"Tandai Sudah Bayar" (jika status pembayaran unpaid).
Pesan sukses/error ditampilkan setelah setiap aksi admin.

Cara Instalasi Aplikasi Sistem Booking Lapangan Olahraga
Ikuti langkah-langkah berikut secara berurutan:
Prasyarat:
Pastikan Anda memiliki:
PHP (versi 8.1 atau lebih baru)
Composer
Node.js & NPM (atau Yarn)
MySQL Server (atau database lain yang kompatibel)
Langkah 1: Kloning Repositori atau Pindah ke Direktori Proyek
Jika Anda belum memiliki folder proyek di komputer Anda (misalnya Anda mendapatkannya dari GitHub atau ZIP file), asumsikan proyeknya bernama sports-booking-app.
Jika Anda baru memulai proyek (atau mengunduh ZIP):
Ekstrak file ZIP atau kloning repositori GitHub ke lokasi yang Anda inginkan (misal C:\xampp\htdocs\sports-booking-app atau /var/www/html/sports-booking-app).
Buka Terminal atau Command Prompt Anda.
Navigasi ke direktori proyek Anda:
cd /path/to/your/sports-booking-app
Use code with caution.
Bash
(Ganti /path/to/your/sports-booking-app dengan path aktual proyek Anda)
Langkah 2: Instalasi Dependensi PHP
Laravel membutuhkan banyak library PHP yang dikelola oleh Composer.
Di Terminal/Command Prompt yang sudah berada di direktori proyek:
composer install
Use code with caution.
Bash
Ini akan mengunduh semua dependensi PHP yang diperlukan dan menyimpannya di folder vendor/. Proses ini mungkin memakan waktu beberapa menit tergantung koneksi internet Anda.
Langkah 3: Konfigurasi Environment (File .env)
File .env berisi konfigurasi spesifik untuk lingkungan Anda (database, kunci aplikasi, dll.).
Salin file .env.example menjadi .env:
Untuk Linux/macOS:
cp .env.example .env
Use code with caution.
Bash
Untuk Windows:
copy .env.example .env
Use code with caution.
Bash
Buka file .env yang baru saja Anda buat (menggunakan editor teks seperti VS Code, Sublime Text, Notepad++).
Konfigurasi Database: Sesuaikan detail koneksi database Anda.
Buat database baru di MySQL. Gunakan phpMyAdmin, MySQL Workbench, atau klien MySQL lainnya. Contoh: sports_booking_db.
Kemudian, edit baris-baris berikut di .env:
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=sports_booking_db  # Ganti dengan nama database yang Anda buat
DB_USERNAME=root               # Ganti dengan username database Anda (umumnya 'root' untuk lokal)
DB_PASSWORD=                   # Ganti dengan password database Anda (kosongkan jika tidak ada)
Use code with caution.
Dotenv
Hasilkan Kunci Aplikasi (App Key): Ini sangat penting untuk keamanan aplikasi.
php artisan key:generate
Use code with caution.
Bash
Perintah ini akan secara otomatis mengisi nilai APP_KEY di file .env Anda.
Langkah 4: Instalasi Dependensi JavaScript
Aplikasi ini menggunakan Tailwind CSS yang memerlukan Node.js dan NPM untuk kompilasinya.
Di Terminal/Command Prompt yang sudah berada di direktori proyek:
npm install
Use code with caution.
Bash
Ini akan mengunduh semua dependensi JavaScript (seperti Tailwind CSS) ke dalam folder node_modules/.
Langkah 5: Kompilasi Aset Frontend (CSS & JavaScript)
Setelah dependensi diinstal, Anda perlu mengkompilasi aset frontend.
Untuk pengembangan (development):
npm run dev
Use code with caution.
Bash
Jika Anda ingin mengkompilasi untuk produksi (minify kode, dsb.): npm run build
Jika Anda ingin agar aset otomatis terkompilasi setiap kali Anda membuat perubahan pada file-file sumber (.js, .css): npm run watch (ini sangat berguna saat pengembangan).
Langkah 6: Jalankan Migrasi Database
Langkah ini akan membuat semua tabel database yang diperlukan berdasarkan file migrasi Laravel.
Di Terminal/Command Prompt yang sudah berada di direktori proyek:
php artisan migrate
Use code with caution.
Bash
Pastikan database yang Anda tentukan di .env sudah ada dan kredensialnya benar. Jika berhasil, Anda akan melihat pesan seperti "Migrating: ..." dan "Migrated: ...".
Langkah 7: Isi Database dengan Data Awal (Seeding)
Ini akan mengisi tabel dengan data contoh (admin user, user biasa, tipe lapangan, lapangan).
Di Terminal/Command Prompt yang sudah berada di direktori proyek:
php artisan db:seed
Use code with caution.
Bash
Anda akan mendapatkan user admin dengan email admin@example.com dan password password, serta user biasa dengan email user@example.com dan password password.
Langkah 8: Buat Symbolic Link untuk Storage
Untuk menampilkan gambar lapangan yang diunggah, Anda perlu membuat symbolic link dari public/storage ke storage/app/public.
Di Terminal/Command Prompt yang sudah berada di direktori proyek:
php artisan storage:link
Use code with caution.
Bash
Langkah 9: Jalankan Server Pengembangan Laravel
Sekarang aplikasi Anda siap dijalankan.
Di Terminal/Command Prompt yang sudah berada di direktori proyek:
php artisan serve
Use code with caution.
Bash
Ini akan memulai server pengembangan Laravel. Anda akan melihat pesan seperti Laravel development server started: http://127.0.0.1:8000.
Langkah 10: Akses Aplikasi di Browser
Buka browser web Anda.
Kunjungi URL yang ditampilkan di terminal (biasanya http://127.0.0.1:8000).
Verifikasi:
Anda akan melihat halaman selamat datang Laravel.
Coba registrasi akun baru (ini akan menjadi user biasa).
Coba login dengan akun admin:
Email: admin@example.com
Password: password
Setelah login sebagai admin, pastikan Anda dapat melihat dan mengakses menu "Tipe Lapangan", "Daftar Lapangan", dan "Semua Booking (Admin)".
Coba login dengan akun user biasa:
Email: user@example.com
Password: password
Setelah login sebagai user, pastikan Anda dapat melihat dan mengakses menu "Booking Lapangan" dan "Booking Saya".
