# laravel
Tutorial Laravel untuk Pemula

## Instalasi, setup Database, Membuat API

Berikut adalah langkah-langkah untuk membuat API dengan metode POST, GET, dan PUT menggunakan Laravel bagi pemula:

### 1. **Install Laravel**
   - Pastikan Anda telah menginstal Composer terlebih dahulu. Jika belum, instal Composer [di sini](https://getcomposer.org/download/).
   - Buka terminal dan jalankan perintah berikut untuk menginstal Laravel:
     ```bash
     composer create-project --prefer-dist laravel/laravel nama_proyek
     ```

### 2. **Konfigurasi Database**
   - Buka file `.env` di root proyek Laravel Anda.
   - Atur detail koneksi database, seperti nama database, username, dan password.
   - Contoh:
     ```env
     DB_CONNECTION=mysql
     DB_HOST=127.0.0.1
     DB_PORT=3306
     DB_DATABASE=nama_database
     DB_USERNAME=root
     DB_PASSWORD=password
     ```

### 3. **Buat Migration dan Model**
   - Buat migration dan model untuk resource yang ingin Anda kelola melalui API, misalnya `Post`.
   - Jalankan perintah:
     ```bash
     php artisan make:model Post -m
     ```
   - Buka file migration di `database/migrations/` dan tambahkan kolom yang diperlukan, contoh:
     ```php
     public function up()
     {
         Schema::create('posts', function (Blueprint $table) {
             $table->id();
             $table->string('title');
             $table->text('content');
             $table->timestamps();
         });
     }
     ```
   - Jalankan migration untuk membuat tabel di database:
     ```bash
     php artisan migrate
     ```

### 4. **Buat Controller**
   - Buat controller untuk mengelola API:
     ```bash
     php artisan make:controller PostController --resource
     ```
   - Ini akan membuat `PostController` dengan metode CRUD (Create, Read, Update, Delete) dasar.

### 5. **Atur Route API**
   - Buka file `routes/api.php` dan tambahkan route untuk resource `Post`:
     ```php
     Route::apiResource('posts', PostController::class);
     ```
   - Route ini secara otomatis akan membuat route untuk GET, POST, PUT, dan DELETE.

### 6. **Implementasi Metode di Controller**
   - Buka `PostController.php` dan implementasikan logika CRUD. Contoh implementasi untuk metode `store` (POST), `index` (GET), dan `update` (PUT):

     **GET: Mengambil Data**
     ```php
     public function index()
     {
         return Post::all();
     }
     ```

     **POST: Menambahkan Data**
     ```php
     public function store(Request $request)
     {
         $post = Post::create($request->all());
         return response()->json($post, 201);
     }
     ```

     **PUT: Mengupdate Data**
     ```php
     public function update(Request $request, $id)
     {
         $post = Post::findOrFail($id);
         $post->update($request->all());
         return response()->json($post, 200);
     }
     ```

### 7. **Test API dengan Postman atau Curl**
   - Gunakan Postman atau Curl untuk mengetes API Anda. Misalnya, untuk membuat data baru:
     - Method: `POST`
     - URL: `http://localhost:8000/api/posts`
     - Body: `{"title": "Judul Baru", "content": "Konten baru"}`

### 8. **Jalankan Server**
   - Jalankan server Laravel menggunakan perintah:
     ```bash
     php artisan serve
     ```
   - API akan tersedia di `http://localhost:8000`.

### 9. **Optional: Menambah Middleware untuk Otentikasi**
   - Jika Anda ingin menambahkan otentikasi, Laravel memiliki paket `sanctum` yang sederhana untuk membuat API dengan token otentikasi.

Dengan mengikuti langkah-langkah di atas, Anda seharusnya bisa membuat API sederhana dengan Laravel. Semoga membantu!

## Otorisasi Authentikasi Token

Untuk mengimplementasikan otentikasi menggunakan PASETO (Platform-Agnostic Security Tokens) di Laravel, Anda dapat mengikuti langkah-langkah berikut:

### 1. **Install Laravel Passport**
   Laravel secara native mendukung Laravel Passport untuk otentikasi API berbasis token. Namun, untuk menggunakan PASETO, kita perlu menggunakan paket pihak ketiga.

   - Install paket PASETO menggunakan Composer:
     ```bash
     composer require sksoft/laravel-paseto
     ```

### 2. **Publikasikan Konfigurasi**
   - Publikasikan file konfigurasi untuk PASETO:
     ```bash
     php artisan vendor:publish --provider="SKsoft\LaravelPaseto\PasetoServiceProvider"
     ```

### 3. **Konfigurasi PASETO**
   - Buka file konfigurasi `config/paseto.php`.
   - Atur konfigurasi sesuai kebutuhan Anda. Misalnya, Anda bisa memilih untuk menggunakan symmetric key atau asymmetric key.

### 4. **Generate Keys**
   - Jika Anda menggunakan PASETO dengan kunci asymmetric, Anda perlu membuat kunci publik dan privat:
     ```bash
     php artisan paseto:keys
     ```
   - Kunci ini akan disimpan dalam `storage/app/paseto`.

### 5. **Integrasikan Middleware**
   - Buka file `app/Http/Kernel.php` dan tambahkan middleware PASETO:
     ```php
     protected $routeMiddleware = [
         'auth.paseto' => \SKsoft\LaravelPaseto\Http\Middleware\AuthenticateWithPaseto::class,
     ];
     ```

### 6. **Lindungi Route dengan PASETO**
   - Buka file `routes/api.php` dan tambahkan middleware untuk melindungi route yang memerlukan otentikasi:
     ```php
     Route::middleware('auth.paseto')->group(function () {
         Route::apiResource('posts', PostController::class);
     });
     ```

### 7. **Generate dan Verifikasi Token PASETO**
   - Untuk menghasilkan token PASETO, Anda dapat membuat endpoint khusus atau mengintegrasikan logika dalam proses login.
   - Contoh kode untuk menghasilkan token PASETO:
     ```php
     use Paseto\Keys\SymmetricKey;
     use Paseto\Builder;

     public function generateToken(Request $request)
     {
         $key = SymmetricKey::generate();
         $token = (new Builder())
             ->setKey($key)
             ->setIssuedAt()
             ->setExpiration((new \DateTimeImmutable())->modify('+1 hour'))
             ->setClaims(['user_id' => auth()->id()])
             ->toString();

         return response()->json(['token' => $token]);
     }
     ```
   - Simpan token ini di client-side dan kirimkan di header setiap permintaan sebagai `Authorization: Bearer {token}`.

### 8. **Verifikasi Token PASETO dalam Middleware**
   - Middleware `AuthenticateWithPaseto` akan memverifikasi token secara otomatis untuk memastikan keabsahan dan izin akses.

### 9. **Test API dengan PASETO**
   - Gunakan Postman atau Curl untuk mengirim permintaan dengan token PASETO di header.
   - Contoh dengan Curl:
     ```bash
     curl -X GET http://localhost:8000/api/posts -H "Authorization: Bearer {your_paseto_token}"
     ```

Dengan konfigurasi ini, Anda dapat menggunakan PASETO untuk otentikasi API di Laravel. PASETO menawarkan keamanan yang lebih baik dibandingkan JWT dalam beberapa aspek dan merupakan pilihan yang baik untuk aplikasi modern.
