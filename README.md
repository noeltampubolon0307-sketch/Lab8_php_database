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

<img width="574" height="360" alt="image" src="https://github.com/user-attachments/assets/16987919-22e3-4f0f-b8af-a7ee90941e3d" />


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

<img width="584" height="706" alt="image" src="https://github.com/user-attachments/assets/53793aed-5dbd-41d2-878c-970efd3564cd" />


<img width="840" height="305" alt="image" src="https://github.com/user-attachments/assets/9925775e-be24-41b0-9f02-4c2dfc63d92e" />


Hasil di browser (`http://localhost/Lab8_PHP_Database/`): ketiga data barang tampil di tabel **Data Barang**. Gambar belum muncul karena file gambarnya belum ada di folder `gambar/`.

<img width="1062" height="210" alt="image" src="https://github.com/user-attachments/assets/db59f760-2a4f-4545-9772-c25d97a49234" />


## Langkah 6 – Menambah Data (`tambah.php`)

File `tambah.php` berisi form input dan proses penyimpanan data.

Alur kerjanya:
1. Form memakai `method="post"` dan `enctype="multipart/form-data"` supaya bisa mengunggah file gambar.
2. Saat tombol **Simpan** ditekan (`isset($_POST['submit'])`), data dari form diambil lewat `$_POST` dan file gambar lewat `$_FILES`.
3. Jika upload tidak error, file dipindahkan ke folder `gambar/` dengan `move_uploaded_file()`.
4. Data disimpan ke database dengan query `INSERT INTO data_barang (...)`.
5. Setelah selesai, halaman diarahkan kembali ke `index.php` dengan `header("location: index.php")`.

Form memiliki isian Nama Barang, Kategori (pilihan Komputer, Elektronik, Hand Phone), Harga Jual, Harga Beli, Stok, dan File Gambar.

<img width="460" height="404" alt="image" src="https://github.com/user-attachments/assets/f0d6baeb-83b1-4bf0-ba23-2ba99b80bdc9" />


<img width="506" height="546" alt="image" src="https://github.com/user-attachments/assets/a5f5bc32-a968-453d-b950-e91ffe2febce" />



Contoh pengisian form: menambah barang **Rog Tuf Gaming**, kategori Komputer, harga 22000000, stok 5.

<img width="372" height="348" alt="image" src="https://github.com/user-attachments/assets/eec0960f-94c9-4d88-9efc-0beaf7ec151e" />


Setelah menekan **Simpan**, data baru muncul di tabel pada halaman `index.php`.
<img width="905" height="211" alt="image" src="https://github.com/user-attachments/assets/ba890563-261b-42dc-a181-3ac13053f45f" />



## Langkah 7 – Mengubah Data (`ubah.php`)

File `ubah.php` dipakai untuk mengedit data yang sudah ada.

Alur kerjanya:
1. Saat link **Ubah** diklik, `id` barang dikirim lewat URL (misalnya `ubah.php?id=4`).
2. Data barang dengan id tersebut diambil dengan `SELECT * FROM data_barang WHERE id_barang = ...`, lalu nilainya ditampilkan di form (`value="<?php echo $data['nama']; ?>"` dan seterusnya).
3. Fungsi `is_select()` dipakai agar pilihan kategori yang sesuai otomatis terpilih (`selected="selected"`).
4. ID barang dikirim lewat input `hidden`.
5. Saat **Simpan** ditekan, query `UPDATE data_barang SET ... WHERE id_barang = ...` dijalankan. Kolom gambar hanya diubah kalau ada file baru yang diunggah.
6. Setelah itu halaman diarahkan kembali ke `index.php`.

<img width="563" height="529" alt="image" src="https://github.com/user-attachments/assets/9b6c49f3-5434-4e91-b290-a333c922c667" />


<img width="659" height="481" alt="image" src="https://github.com/user-attachments/assets/17aa9a07-d47e-43da-9c49-6f9a1c13d098" />


<img width="669" height="353" alt="image" src="https://github.com/user-attachments/assets/cc8a42bf-2159-40ca-9de2-6f4212d80f29" />


Tampilan form **Ubah Barang** dengan data lama sudah terisi di setiap kolom:

<img width="468" height="425" alt="image" src="https://github.com/user-attachments/assets/d727930b-1553-4763-a23e-036b064b7391" />


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

<img width="966" height="299" alt="image" src="https://github.com/user-attachments/assets/91496408-5a9f-47f1-93e9-f6b6486c9d6b" />


Saat link **Hapus** diklik, muncul kotak konfirmasi "Yakin ingin menghapus data ini?". Pilih **Oke** untuk menghapus atau **Batal** untuk membatalkan.

<img width="975" height="251" alt="image" src="https://github.com/user-attachments/assets/e2c77619-437d-462d-a160-0b7d803e03a7" />


---

## Kesimpulan

Dari praktikum ini dapat dipelajari cara menghubungkan PHP dengan database MySQL memakai `mysqli`, serta membuat fitur CRUD (tambah, tampil, ubah, hapus) dalam satu aplikasi web sederhana. Praktikum ini juga melatih cara mengunggah file gambar dan membuat konfirmasi sebelum menghapus data.# Lab8_php_database
