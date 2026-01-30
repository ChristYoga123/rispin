# Dokumentasi Proyek Pak Tony

Dokumentasi ini berisi panduan setup awal setelah clone repository dan alur kerja pengembangan (development workflow) yang disepakati.

## 1. Tutorial Setup (Setelah Clone dari Git)

Jika anda baru saja melakukan `clone` dari repository, lakukan langkah-langkah berikut untuk mengaktifkan project di komputer lokal:

```bash
# 1. Clone Repository (Belum ada project)
# Ganti URL_REPOSITORY dengan link repository anda
git clone https://github.com/natanetpro/rispin.git

# 2. Masuk ke folder project
cd rispin

# 3. Install dependencies (penting!)
# Ini akan mengunduh semua library CodeIgniter 4 yang dibutuhkan
composer install

# 4. Setup file Environment
# Copy file 'env' menjadi '.env'
cp env .env

# 5. Konfigurasi .env (Opsional)
# Buka file .env dan sesuaikan database atau baseURL jika perlu:
# database.default.hostname = localhost
# database.default.database = nama_database
# database.default.username = root
# database.default.password =
# CI_ENVIRONMENT = development

# 6. Setup Database (Migrate & Seed)
# Jalankan migrasi untuk membuat tabel
php spark migrate

# Jalankan seeder untuk mengisi data awal (PENTING!)
php spark db:seed UserSeeder
php spark db:seed MenuSeeder
```

### Workflow Git Sehari-hari

```bash
# 1. Ambil update terbaru dari server sebelum mulai kerja
git pull origin main

# ... Lakukan coding ...

# 2. Cek file apa saja yang berubah
git status

# 3. Simpan perubahan ke staging
git add .

# 4. Buat Commit dengan pesan jelas
git commit -m "Menambahkan fitur X"

# 5. Kirim ke server
git push origin main
```

---

## 2. Tutorial Pengerjaan Fitur (Workflow CI4)

Sesuai request, penamaan file mengikuti standar berikut:

- **Model**: Akhiran `Model` (contoh: `BarangModel.php`)
- **Controller**: Akhiran `Controller` (contoh: `BarangController.php`)

Urutan pengerjaan: **Model -> Controller -> View -> Route**.

### Tahap 1: Membuat Model (Database)

Model bertugas berinteraksi dengan database.

1.  **Buat Model via Terminal:**
    Gunakan flag `--suffix` agar otomatis ada imbuhan "Model".

    ```bash
    php spark make:model Barang --suffix
    # Hasil file: app/Models/BarangModel.php
    ```

2.  **Konfigurasi Model (`app/Models/BarangModel.php`):**

    ```php
    <?php namespace App\Models;

    use CodeIgniter\Model;

    class BarangModel extends Model
    {
        protected $table      = 'barang';
        protected $primaryKey = 'id';

        // Kolom yang boleh diinput
        protected $allowedFields = ['nama_barang', 'harga', 'stok'];
    }
    ```

### Tahap 2: Membuat Controller (Logika)

Controller berfungsi sebagai penghubung. Kita akan menggunakan nama dengan akhiran "Controller".

1.  **Buat Controller via Terminal:**
    Tulis nama lengkap dengan "Controller".

    ```bash
    php spark make:controller BarangController
    # Hasil file: app/Controllers/BarangController.php
    ```

2.  **Isi Logic di Controller (`app/Controllers/BarangController.php`):**

    ```php
    <?php namespace App\Controllers;

    use App\Models\BarangModel;

    class BarangController extends BaseController
    {
        public function index()
        {
            // 1. Panggil Model
            $model = new BarangModel();

            // 2. Ambil data
            $data['barang_list'] = $model->findAll();

            // 3. Kirim ke View
            return view('barang/index', $data);
        }
    }
    ```

### Tahap 3: Membuat View (Tampilan)

1.  **Buat File:** `app/Views/barang/index.php`
2.  **Isi File:**
    ```php
    <h1>Daftar Barang</h1>
    <table>
        <?php foreach ($barang_list as $b): ?>
        <tr>
            <td><?= $b['nama_barang'] ?></td>
        </tr>
        <?php endforeach; ?>
    </table>
    ```

### Tahap 4: Mendaftarkan Route (URL)

Agar bisa diakses di browser, daftarkan route ke `BarangController`.

1.  **Buka file:** `app/Config/Routes.php`
2.  **Edit Route:**

    ```php
    // $routes->get('url', 'NamaController::Method');

    // Perhatikan nama controller harus sesuai nama file (BarangController)
    $routes->get('/barang', 'BarangController::index');
    ```

### Tahap Tambahan: Database Seeding (Isi Data Awal)

Seeder digunakan untuk mengisi database dengan data awal atau data dummy.

1.  **Buat Seeder:**

    ```bash
    php spark make:seeder BarangSeeder
    # Hasil file: app/Database/Seeds/BarangSeeder.php
    ```

2.  **Isi Data (`app/Database/Seeds/BarangSeeder.php`):**

    ```php
    <?php namespace App\Database\Seeds;

    use CodeIgniter\Database\Seeder;

    class BarangSeeder extends Seeder
    {
        public function run()
        {
            $data = [
                [
                    'nama_barang' => 'Laptop ASUS',
                    'harga'       => 15000000,
                    'stok'        => 5
                ],
                [
                    'nama_barang' => 'Mouse Logitech',
                    'harga'       => 150000,
                    'stok'        => 20
                ]
            ];

            // Insert data ke tabel 'barang'
            // Bisa pakai Query Builder atau Model
            $this->db->table('barang')->insertBatch($data);
        }
    }
    ```

3.  **Jalankan Seeder:**
    ```bash
    php spark db:seed BarangSeeder
    ```

---

## 3. Menjalankan Project

```bash
php spark serve
```

Akses: `http://localhost:8080/barang`
