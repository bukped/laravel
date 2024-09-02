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

## Model dan Migration

Dalam Laravel, **Model** dan **Migration** adalah dua konsep fundamental yang membantu Anda mengelola dan bekerja dengan database secara efisien.

### 1. **Model**

**Model** adalah representasi dari sebuah tabel di database. Model adalah bagian dari Eloquent ORM (Object-Relational Mapping) di Laravel yang memungkinkan Anda untuk berinteraksi dengan database menggunakan objek dan metode yang lebih alami daripada menulis SQL secara langsung.

- **Fungsi Utama Model:**
  - **Merepresentasikan Tabel:** Setiap model biasanya terhubung dengan satu tabel dalam database. Misalnya, jika Anda memiliki tabel `posts`, Anda mungkin memiliki model `Post`.
  - **Mengelola Data:** Model digunakan untuk mengelola data di tabel tersebut, seperti menyimpan, memperbarui, menghapus, dan mengambil data.
  - **Relasi Antar Tabel:** Model juga bisa digunakan untuk mendefinisikan relasi antara tabel, seperti one-to-one, one-to-many, dan many-to-many.

- **Contoh:**
  Misalnya, berikut adalah model `Post`:
  ```php
  namespace App\Models;

  use Illuminate\Database\Eloquent\Model;

  class Post extends Model
  {
      protected $fillable = ['title', 'content'];
  }
  ```
  Di sini, model `Post` merepresentasikan tabel `posts` di database, dan atribut `fillable` menentukan kolom mana yang dapat diisi secara massal (mass assignable).

### 2. **Migration**

**Migration** adalah cara untuk mengelola skema database Anda dalam kode. Migration memungkinkan Anda untuk membuat, mengubah, dan menghapus tabel dan kolom di database menggunakan kode PHP, bukan SQL mentah.

- **Fungsi Utama Migration:**
  - **Mengelola Skema Database:** Migration adalah seperti "versi kontrol" untuk skema database. Ini membantu Anda mengubah struktur tabel tanpa harus menulis SQL secara langsung.
  - **Portabilitas:** Migration membuat pengelolaan skema database menjadi lebih portable. Anda bisa menjalankan migration yang sama di berbagai lingkungan (misalnya, lokal, staging, production) dengan perintah yang sama.
  - **Rollback:** Migration juga mendukung rollback, artinya Anda bisa membatalkan perubahan yang dilakukan oleh migration tertentu.

- **Contoh:**
  Misalnya, berikut adalah migration untuk membuat tabel `posts`:
  ```php
  use Illuminate\Database\Migrations\Migration;
  use Illuminate\Database\Schema\Blueprint;
  use Illuminate\Support\Facades\Schema;

  class CreatePostsTable extends Migration
  {
      public function up()
      {
          Schema::create('posts', function (Blueprint $table) {
              $table->id();
              $table->string('title');
              $table->text('content');
              $table->timestamps();
          });
      }

      public function down()
      {
          Schema::dropIfExists('posts');
      }
  }
  ```
  - **`up` Method:** Metode ini mendefinisikan apa yang terjadi ketika migration dijalankan. Dalam contoh ini, tabel `posts` dibuat dengan kolom `id`, `title`, `content`, dan `timestamps`.
  - **`down` Method:** Metode ini mendefinisikan apa yang terjadi ketika migration dibatalkan (rolled back). Di sini, tabel `posts` akan dihapus.

- **Menjalankan Migration:**
  Untuk menjalankan migration dan membuat tabel `posts`, Anda bisa menggunakan perintah:
  ```bash
  php artisan migrate
  ```
  Perintah ini akan menerapkan semua migration yang belum dijalankan.

### **Hubungan Model dan Migration**

- **Model** mengelola data di dalam tabel yang direpresentasikannya.
- **Migration** mengelola struktur tabel tersebut (misalnya, membuat tabel atau menambahkan kolom baru).

Kedua konsep ini bekerja sama untuk memungkinkan Anda mengelola database secara efisien dalam Laravel. Migration memastikan bahwa skema database Anda selalu up-to-date dan bisa diubah dengan mudah, sementara Model memungkinkan Anda berinteraksi dengan data di database tersebut menggunakan kode PHP yang bersih dan intuitif.


## Interaksi dengan Basis Data
Di Laravel, interaksi dengan database sangat mudah dilakukan menggunakan Eloquent ORM (Object-Relational Mapping). Berikut adalah cara-cara untuk melakukan operasi dasar seperti mendapatkan satu baris, mendapatkan semua baris, memperbarui satu baris, dan menghapus satu baris:

### 1. **Mendapatkan Satu Baris dengan Query**

Untuk mendapatkan satu baris dari tabel, Anda bisa menggunakan metode `find`, `first`, atau `where`.

- **Menggunakan `find`:**
  ```php
  $post = Post::find(1);
  ```
  Di sini, `1` adalah `id` dari baris yang ingin Anda dapatkan.

- **Menggunakan `first` dengan Kondisi:**
  ```php
  $post = Post::where('title', 'Judul Postingan')->first();
  ```
  Ini akan mengembalikan baris pertama yang cocok dengan kondisi yang diberikan.

- **Menggunakan `firstOrFail`:**
  ```php
  $post = Post::where('title', 'Judul Postingan')->firstOrFail();
  ```
  Jika tidak ada baris yang cocok, ini akan menampilkan `ModelNotFoundException`.

### 2. **Mendapatkan Seluruh Baris**

Untuk mendapatkan semua baris dari tabel, Anda bisa menggunakan metode `all` atau `get`.

- **Menggunakan `all`:**
  ```php
  $posts = Post::all();
  ```

- **Menggunakan `get` dengan Kondisi (opsional):**
  ```php
  $posts = Post::where('published', true)->get();
  ```
  Ini akan mengembalikan semua baris yang memenuhi kondisi tertentu.

### 3. **Memperbarui Satu Baris**

Untuk memperbarui satu baris, Anda bisa menggunakan metode `update` atau melalui instance model.

- **Menggunakan Instance Model:**
  ```php
  $post = Post::find(1);
  $post->title = 'Judul Baru';
  $post->content = 'Konten baru';
  $post->save();
  ```

- **Menggunakan `update` pada Query Builder:**
  ```php
  Post::where('id', 1)->update(['title' => 'Judul Baru', 'content' => 'Konten baru']);
  ```

### 4. **Menghapus Satu Baris**

Untuk menghapus satu baris, Anda bisa menggunakan metode `delete`.

- **Menggunakan Instance Model:**
  ```php
  $post = Post::find(1);
  $post->delete();
  ```

- **Menggunakan `delete` pada Query Builder:**
  ```php
  Post::where('id', 1)->delete();
  ```

### Contoh Lengkap di Controller

Misalkan kita ingin mengimplementasikan semua operasi ini dalam sebuah controller:

```php
use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    // Mendapatkan satu row
    public function show($id)
    {
        $post = Post::findOrFail($id);
        return response()->json($post);
    }

    // Mendapatkan semua row
    public function index()
    {
        $posts = Post::all();
        return response()->json($posts);
    }

    // Update satu row
    public function update(Request $request, $id)
    {
        $post = Post::findOrFail($id);
        $post->update($request->all());
        return response()->json($post);
    }

    // Delete satu row
    public function destroy($id)
    {
        $post = Post::findOrFail($id);
        $post->delete();
        return response()->json(['message' => 'Post deleted successfully']);
    }
}
```

Dengan cara ini, Anda dapat melakukan operasi dasar pada database di Laravel menggunakan Eloquent ORM, yang membuat pengelolaan data lebih mudah dan efisien.

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

## Factory dan Seeder

Dalam Laravel, **Factory** dan **Seeder** adalah alat yang sangat berguna untuk membuat dan mengisi data di database selama pengembangan atau pengujian. Mereka membantu Anda dengan cepat membuat data sampel atau mengisi tabel dengan data yang diperlukan untuk menjalankan aplikasi Anda.

### 1. **Factory**

**Factory** adalah sebuah fitur yang memungkinkan Anda membuat objek model dengan data contoh (dummy data) untuk keperluan pengujian atau pengisian database selama pengembangan. Factory ini sangat berguna ketika Anda perlu mengisi database dengan sejumlah besar data untuk menguji fitur atau melakukan pengembangan.

- **Cara Kerja:**
  Factory mendefinisikan bagaimana data model harus dihasilkan. Anda bisa menentukan nilai default untuk setiap kolom di model Anda, dan Laravel akan membuatkan data palsu (fake) menggunakan library Faker.

- **Contoh Factory:**
  Misalnya, berikut adalah contoh factory untuk model `Post`:
  ```php
  use App\Models\Post;
  use Illuminate\Database\Eloquent\Factories\Factory;
  use Faker\Generator as Faker;

  class PostFactory extends Factory
  {
      protected $model = Post::class;

      public function definition()
      {
          return [
              'title' => $this->faker->sentence,
              'content' => $this->faker->paragraph,
          ];
      }
  }
  ```
  Di sini, `definition()` mendefinisikan bagaimana data contoh untuk model `Post` harus dibuat. `faker->sentence` akan menghasilkan kalimat acak untuk kolom `title`, dan `faker->paragraph` akan menghasilkan paragraf acak untuk kolom `content`.

- **Menggunakan Factory:**
  Anda bisa menggunakan factory untuk membuat satu atau beberapa instance model:
  ```php
  // Membuat satu instance
  $post = Post::factory()->create();

  // Membuat beberapa instance
  $posts = Post::factory()->count(10)->create();
  ```

### 2. **Seeder**

**Seeder** adalah fitur yang digunakan untuk mengisi tabel database dengan data awal (seeding). Seeder sangat berguna untuk mengisi tabel dengan data statis yang diperlukan untuk aplikasi Anda, seperti kategori default, peran pengguna, atau data uji lainnya.

- **Cara Kerja:**
  Seeder berfungsi dengan menjalankan kode yang mengisi database Anda dengan data. Anda bisa menggunakan seeder untuk mengisi database dengan data yang dihasilkan oleh factory atau data yang Anda definisikan sendiri.

- **Contoh Seeder:**
  Misalnya, berikut adalah contoh seeder untuk mengisi tabel `posts` dengan data:
  ```php
  use Illuminate\Database\Seeder;
  use App\Models\Post;

  class PostSeeder extends Seeder
  {
      public function run()
      {
          Post::factory()->count(50)->create();
      }
  }
  ```
  Di sini, seeder `PostSeeder` menggunakan factory `PostFactory` untuk membuat 50 data acak dalam tabel `posts`.

- **Menjalankan Seeder:**
  Untuk menjalankan seeder dan mengisi database Anda dengan data yang ditentukan, Anda bisa menggunakan perintah:
  ```bash
  php artisan db:seed --class=PostSeeder
  ```
  Atau Anda bisa menjalankan semua seeder yang terdaftar dalam file `DatabaseSeeder.php` dengan perintah:
  ```bash
  php artisan db:seed
  ```

### **Hubungan antara Factory dan Seeder**

- **Factory** digunakan untuk membuat data palsu untuk model, seringkali untuk tujuan pengujian atau pengembangan.
- **Seeder** menggunakan Factory (atau metode lain) untuk mengisi tabel di database dengan data awal atau data contoh.

Keduanya sangat membantu dalam proses pengembangan, terutama ketika Anda perlu membuat data dalam jumlah besar untuk menguji bagaimana aplikasi Anda berfungsi dengan data yang beragam.
