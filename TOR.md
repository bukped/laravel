1. Instalasi Laravel: Install Laravel
2. Konfigurasi Database: Buat database dan koneksikan dengan aplikasi Laravel
3. Template Admin Menggunakan template buatan sendiri atau cari referensi 
4. CRUD User: Buat fungsi CRUD untuk user, serta tambahkan fungsi login ke dalam aplikasi
5. Model dan Migration: Buat model bernama Category beserta migration-nya, dengan field: id, name, is_publish (boolean), created_at, dan updated_at
6.Factory dan Seeder: Buat factory dan seeder untuk model Category, dan masukkan 100 data menggunakan Faker
7. List Data Kategori: Buat fitur list data kategori dengan pagination dan search
8. Form Create & Edit Category: 
⁃ Buat form untuk create dan edit category beserta validasinya 
⁃ Buat form request secara terpisah untuk validasi. 
⁃ Tampilkan pesan error jika ada kesalahan 
- Pastikan value input sebelumnya tidak hilang jika terjadi kesalahan validasi 
- Buat juga fungsi delete untuk category

9. Autentikasi: - Tambahkan autentikasi saat mengakses category Jika user tidak login, category tidak bisa diakses dan sistem akan menampilkan halaman login.
10. API Login: ⁃ Buat API login yang akan menghasilkan token untuk mengakses halaman CRUC kategori. Token dapat menggunakan Passport atau metode lain yang sesuai.
11. REST API untuk CRUD Category: 
- Buat REST API untuk menangani CRUD kategori 
⁃ Gunakan route default Laravel yang diawali dengan /api/

12. Autentikasi Token: 
- Pastikan semua fungsi CRUD kategori hanya dapat diakses jika user telah login 
-.Jika token tidak valid atau telah kedaluwarsa, tampilkan error autentikasi.
- Jika token berlaku dan aktif, user dapat mengakses kategori
