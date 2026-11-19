# Praktikum Lab 8 – PHP & Database (CRUD Data Barang)

## Identitas Mahasiswa
 
| Keterangan      | Data                           |
| --------------- | --------------------           |
| **Nama**        | Noel Marihot Tampubolon        |
| **Kelas**       | I251D                          |
| **NIM**         | 312510419                      |
| **Mata Kuliah** | Pemrograman Web                |
| **Dosen**       | Agung Nugroho, S.Kom., M.Kom.  |

## Deskripsi

Praktikum ini membuat aplikasi web sederhana dengan **PHP** dan **MySQL/MariaDB** untuk mengelola data barang. Aplikasi mencakup operasi **CRUD**:

- **Create** – menambah barang (`tambah.php`)
- **Read** – menampilkan daftar barang (`index.php`)
- **Update** – mengubah data barang (`ubah.php`)
- **Delete** – menghapus data barang (`hapus.php`)

**Alat yang digunakan:** XAMPP v3.3.0 (Apache, MySQL/MariaDB 10.4.32, PHP 8.2.12), Visual Studio Code, dan Google Chrome.

## Struktur File

```
Lab8_PHP_Database/
├── koneksi.php   # koneksi ke database
├── index.php     # menampilkan data barang
├── tambah.php    # form + proses tambah data
├── ubah.php      # form + proses ubah data
├── hapus.php     # proses hapus data
├── style.css     # tampilan halaman
└── gambar/       # folder penyimpanan gambar yang diunggah
```

---

## Langkah 1 – Menjalankan Apache dan MySQL di XAMPP

Buka **XAMPP Control Panel**, lalu klik **Start** pada modul **Apache** dan **MySQL**. Kedua modul berhasil berjalan: Apache di port 80 dan 443, MySQL di port 3306. Log di bagian bawah menunjukkan status `running` untuk keduanya.

<img width="634" height="376" alt="image" src="https://github.com/user-attachments/assets/e1fd0edc-d684-49c9-8648-46bd31815766" />


## Langkah 2 – Membuat Database dan Tabel

Klik tombol **Shell** di XAMPP, lalu masuk ke MySQL dengan perintah `mysql -u root`. Setelah itu dijalankan perintah berikut:

```sql
CREATE DATABASE latihan1;
USE latihan1;

CREATE TABLE data_barang (
    id_barang  int(10) auto_increment PRIMARY KEY,
    kategori   varchar(30),
    nama       varchar(30),
    gambar     varchar(100),
    harga_beli decimal(10,0),
    harga_jual decimal(10,0),
    stok       int(4)
);

INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
VALUES ('Elektronik', 'HP Samsung Android', 'hp_samsung.jpg', 2000000, 2400000, 5),
       ('Elektronik', 'HP Xiaomi Android',  'hp_xiaomi.jpg',  1000000, 1400000, 5),
       ('Elektronik', 'HP OPPO Android',    'hp_oppo.jpg',    1800000, 2300000, 5);

SELECT * FROM data_barang;
```

Penjelasan:
- `CREATE DATABASE latihan1` membuat database baru bernama `latihan1`.
- `CREATE TABLE data_barang` membuat tabel dengan kolom `id_barang` (primary key yang bertambah otomatis), `kategori`, `nama`, `gambar`, `harga_beli`, `harga_jual`, dan `stok`.
- `INSERT INTO` mengisi 3 data awal.
- `SELECT * FROM data_barang` menampilkan isi tabel. Hasilnya 3 baris data.

<img width="560" height="520" alt="image" src="https://github.com/user-attachments/assets/43ec2cd0-500e-4a42-ba9b-b57be565bbc3" />


## Langkah 3 – Membuat Folder Project di htdocs

Buat folder project bernama **Lab8_PHP_Database** di dalam `C:\xampp\htdocs`. Semua file PHP disimpan di folder ini agar bisa diakses lewat Apache.

<img width="829" height="142" alt="image" src="https://github.com/user-attachments/assets/25132bf9-8add-40c3-b413-47c28cf16e56" />


Saat folder dibuka di browser lewat `http://localhost/lab8_php_database/`, halaman masih kosong (*Index of*) karena belum ada file di dalamnya.

<img width="794" height="303" alt="image" src="https://github.com/user-attachments/assets/57bb378c-305a-4dd5-9bbd-f51c0d99e88f" />


## Langkah 4 – Membuat Koneksi Database (`koneksi.php`)

File `koneksi.php` berisi pengaturan koneksi ke database:

```php
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db   = "latihan1";

$conn = mysqli_connect($host, $user, $pass, $db);
if ($conn == false) {
    echo "Koneksi ke server gagal.";
    die();
} else echo "Koneksi berhasil";
?>
```

Penjelasan:
- `$host`, `$user`, `$pass`, `$db` menyimpan alamat server, username, password, dan nama database.
- `mysqli_connect()` membuka koneksi ke MySQL.
- Jika koneksi gagal, tampil pesan "Koneksi ke server gagal." dan program berhenti (`die()`). Jika berhasil, tampil "Koneksi berhasil".

<img width="454" height="288" alt="php n1" src="https://github.com/user-attachments/assets/85460628-fa98-40f1-a72a-cb897d8e4552" />


Hasil saat `koneksi.php` dibuka di browser: muncul tulisan **Koneksi berhasil**.

<img width="975" height="103" alt="image" src="https://github.com/user-attachments/assets/45c1e436-92d4-4916-879c-f1fcd10d1721" />


## Langkah 5 – Menampilkan Data (`index.php`)

File `index.php` mengambil seluruh data dari tabel `data_barang` lalu menampilkannya dalam tabel HTML.

Bagian utama kodenya:
- `include("koneksi.php");` memanggil file koneksi.
- `$sql = 'SELECT * FROM data_barang';` dan `mysqli_query($conn, $sql)` menjalankan query.
- `while ($row = mysqli_fetch_array($result))` mengulang setiap baris data dan mencetaknya ke dalam `<tr>` dan `<td>`.
- Kolom **Aksi** berisi link **Ubah** (`ubah.php?id=...`) dan **Hapus** (`hapus.php?id=...`). Link Hapus memakai `confirm()` JavaScript agar ada konfirmasi sebelum data dihapus.
- Jika tidak ada data, tampil tulisan "Belum ada data".
- Link **Tambah Barang** mengarah ke `tambah.php`.

<img width="712" height="707" alt="php n2" src="https://github.com/user-attachments/assets/3e1457ed-58e4-4201-915a-0db07bc85765" />


<img width="1024" height="527" alt="php n3" src="https://github.com/user-attachments/assets/4aba964c-e1c3-4f98-8a6e-c7da3480452d" />


Hasil di browser (`http://localhost/Lab8_PHP_Database/`): ketiga data barang tampil di tabel **Data Barang**. Gambar belum muncul karena file gambarnya belum ada di folder `gambar/`.

<img width="1522" height="383" alt="php n4" src="https://github.com/user-attachments/assets/54efaab9-b40d-4f96-8501-2f09d318a74a" />


## Langkah 6 – Menambah Data (`tambah.php`)

File `tambah.php` berisi form input dan proses penyimpanan data.

Alur kerjanya:
1. Form memakai `method="post"` dan `enctype="multipart/form-data"` supaya bisa mengunggah file gambar.
2. Saat tombol **Simpan** ditekan (`isset($_POST['submit'])`), data dari form diambil lewat `$_POST` dan file gambar lewat `$_FILES`.
3. Jika upload tidak error, file dipindahkan ke folder `gambar/` dengan `move_uploaded_file()`.
4. Data disimpan ke database dengan query `INSERT INTO data_barang (...)`.
5. Setelah selesai, halaman diarahkan kembali ke `index.php` dengan `header("location: index.php")`.

Form memiliki isian Nama Barang, Kategori (pilihan Komputer, Elektronik, Hand Phone), Harga Jual, Harga Beli, Stok, dan File Gambar.

<img width="898" height="707" alt="php n5" src="https://github.com/user-attachments/assets/e345d41c-38f7-4e91-bfed-209995097747" />


<img width="706" height="710" alt="php n6" src="https://github.com/user-attachments/assets/c5416b45-75fa-47a5-b0cd-f9b011aa6156" />

<img width="637" height="255" alt="php n7" src="https://github.com/user-attachments/assets/a5d348cf-5c42-484b-9c64-481eae692506" />




Contoh pengisian form: menambah barang **HP IPHONE 18 PRO MAX BURGUNDY 2TB**, kategori Hand Phone, harga 5000000, stok 8.

<img width="542" height="436" alt="PHP N8" src="https://github.com/user-attachments/assets/98cca001-9ca9-44d5-ac27-7a86cddca024" />


Setelah menekan **Simpan**, data baru muncul di tabel pada halaman `index.php`.
<img width="1535" height="361" alt="php n9" src="https://github.com/user-attachments/assets/7c11bd74-7406-402a-9ce2-1e8eb4885964" />



## Langkah 7 – Mengubah Data (`ubah.php`)

File `ubah.php` dipakai untuk mengedit data yang sudah ada.

Alur kerjanya:
1. Saat link **Ubah** diklik, `id` barang dikirim lewat URL (misalnya `ubah.php?id=4`).
2. Data barang dengan id tersebut diambil dengan `SELECT * FROM data_barang WHERE id_barang = ...`, lalu nilainya ditampilkan di form (`value="<?php echo $data['nama']; ?>"` dan seterusnya).
3. Fungsi `is_select()` dipakai agar pilihan kategori yang sesuai otomatis terpilih (`selected="selected"`).
4. ID barang dikirim lewat input `hidden`.
5. Saat **Simpan** ditekan, query `UPDATE data_barang SET ... WHERE id_barang = ...` dijalankan. Kolom gambar hanya diubah kalau ada file baru yang diunggah.
6. Setelah itu halaman diarahkan kembali ke `index.php`.

<img width="831" height="710" alt="php n10" src="https://github.com/user-attachments/assets/2702dfd0-f598-4f35-91bf-00ed31bfd788" />


<img width="1060" height="691" alt="php n11" src="https://github.com/user-attachments/assets/cfe9b102-0e58-4ab1-adef-da7ad41dab4e" />


<img width="904" height="444" alt="php n12" src="https://github.com/user-attachments/assets/d4077374-fb51-4f44-b948-6ccc2d020bf6" />


Tampilan form **Ubah Barang** dengan data lama sudah terisi di setiap kolom:

<img width="587" height="410" alt="yes " src="https://github.com/user-attachments/assets/97df6c82-02c4-4753-aab0-525ea068a594" />


## Langkah 8 – Menghapus Data (`hapus.php`)

File `hapus.php` menghapus data berdasarkan `id` yang dikirim lewat URL:

```php
<?php
include_once 'koneksi.php';
$id = $_GET['id'];
$sql = "DELETE FROM data_barang WHERE id_barang = '{$id}'";
$result = mysqli_query($conn, $sql);
header('location: index.php');
?>
```

Penjelasan:
- `$_GET['id']` mengambil id barang yang akan dihapus.
- `DELETE FROM data_barang WHERE id_barang = ...` menghapus baris tersebut.
- Setelah itu halaman kembali ke `index.php`.

<img width="542" height="166" alt="hapus php" src="https://github.com/user-attachments/assets/aa402c34-7c00-4aef-a4c2-eed2db155a96" />


Saat link **Hapus** diklik, muncul kotak konfirmasi "Yakin ingin menghapus data ini?". Pilih **Oke** untuk menghapus atau **Batal** untuk membatalkan.

<img width="1535" height="453" alt="edn php" src="https://github.com/user-attachments/assets/2fdc8477-0ac3-4bff-a6f8-3fdd08047434" />


---

## Kesimpulan

Dari praktikum ini dapat dipelajari cara menghubungkan PHP dengan database MySQL memakai `mysqli`, serta membuat fitur CRUD (tambah, tampil, ubah, hapus) dalam satu aplikasi web sederhana. Praktikum ini juga melatih cara mengunggah file gambar dan membuat konfirmasi sebelum menghapus data.# Lab8_php_database
