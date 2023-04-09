# Tugas Program Lab 3 Web

## Pertama Membuat koneksi.php

```php
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db = "latihan1";
$conn = mysqli_connect($host, $user, $pass, $db);
if ($conn == false) {
  echo "Koneksi ke server gagal.";
  die();
} #else echo "Koneksi berhasil";

```

<br>
- Kode di atas adalah script PHP yang melakukan koneksi ke server database MySQL menggunakan fungsi mysqli_connect(). Variabel $host berisi nama server database, $user berisi username untuk mengakses database, $pass berisi password untuk mengakses database, dan $db berisi nama database yang akan digunakan.

<br>

- ### contoh database yang dibuat

  <img src="output/database.png">

<br>
<br>

## Index.php

<img src="output/hasil.png">

<br>

- Dilakukan pemanggilan file koneksi.php yang berisi script untuk koneksi ke database. Selanjutnya, dilakukan query untuk menampilkan data barang dari tabel data_barang menggunakan mysqli_query.
- Terdapat tombol "Tambah Barang" yang mengarahkan ke halaman tambah.php untuk menambahkan data baru. Pada kolom Aksi, terdapat tombol "Edit" dan "Hapus" yang mengarahkan ke halaman ubah.php dan hapus.php masing-masing. Tombol "Hapus" memiliki konfirmasi agar pengguna memastikan sebelum menghapus data.

```php
<?php
// memanggil koneksi database
require_once("koneksi.php");


// query untuk menampilkan data barang
$sql = "SELECT * FROM data_barang";
$result = mysqli_query($conn, $sql);

$no = 1;
?>

```

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Data Barang</title>
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.0-beta1/dist/css/bootstrap.min.css"
    />
  </head>

  <body>
    <h1
      class="py-2 px-3 text-center"
      style="background-color: #1E90FF; color: white;"
    >
      Data Barang
    </h1>

    <div class="container">
      <div class="mt-4">
        <a href="tambah.php" class="btn btn-success btn-sm mb-4 float-end"
          >Tambah Barang</a
        >
        <table class="table table-sm table-bordered">
          <tr class="text-center fw-bold text-uppercase">
            <th>No</th>
            <th>Gambar</th>
            <th>Nama</th>
            <th>Kategori</th>
            <th>Harga Beli</th>
            <th>Harga Jual</th>
            <th>Stok</th>
            <th>Aksi</th>
          </tr>
          <?php while ($row = mysqli_fetch_assoc($result)) { ?>
          <tr>
            <td class="text-center">
              <?php echo $no;
                                              $no++ ?>
            </td>
            <td class="text-center">
              <img
                src="gambar/<?= $row['gambar'] ?>"
                alt="<?= $row['nama']; ?>"
                width="100px"
              />
            </td>
            <td><?php echo $row['nama']; ?></td>
            <td><?php echo $row['kategori']; ?></td>
            <td>
              Rp.
              <?php echo $row['harga_beli']; ?>
            </td>
            <td>
              Rp.
              <?php echo $row['harga_jual']; ?>
            </td>
            <td><?php echo $row['stok']; ?></td>
            <td class="text-center">
              <a
                href="ubah.php?id=<?php echo $row['id_barang']; ?>"
                class="btn btn-warning btn-sm mx-1"
                >Edit</a
              >
              <a
                href="hapus.php?id=<?php echo $row['id_barang']; ?>"
                class="btn btn-danger btn-sm mx-1"
                onclick="return confirm('Apakah Anda yakin ingin menghapus barang ini?')"
                >Hapus</a
              >
            </td>
          </tr>
          <?php } ?>
        </table>
      </div>
    </div>
  </body>
</html>
```

<br>
<br>

## Form Tambah.php

  <img src="output/tambah.png">

  <br>

- Sintax ini merupakan sebuah form untuk menambahkan data barang ke dalam database. Pada saat form dikirimkan (submit), data yang dimasukkan oleh pengguna akan diolah terlebih dahulu dan kemudian dimasukkan ke dalam database.

```php
<?php
error_reporting(E_ALL);
include_once 'koneksi.php';
if (isset($_POST['submit'])) {
  $input = (object) $_POST;

  $nama = ucwords(strtolower($input->nama));
  $kategori = ucwords(strtolower($input->kategori));
  $harga_beli = $input->harga_beli;
  $harga_jual = $input->harga_jual;
  $stok = $input->stok;
  $file_gambar = $_FILES['file_gambar'];
  $gambar = NULL;

  if ($file_gambar['error'] == 0) {
      $nama_gambar = str_replace(' ', '_', $file_gambar['name']);
      $path = dirname(__FILE__) . '/gambar/' . $nama_gambar;

      if (move_uploaded_file($file_gambar['tmp_name'], $path)) {
          $gambar = $nama_gambar;
      }
  }

  $sql = 'INSERT INTO data_barang (nama, kategori,harga_jual, harga_beli,
stok, gambar) ';
  $sql .= "VALUE ('{$nama}', '{$kategori}','{$harga_jual}',
'{$harga_beli}', '{$stok}', '{$gambar}')";
  $result = mysqli_query($conn, $sql);
  header('location: index.php');
}
?>
```

```html
<!DOCTYPE html>
<html lang="en"></html>
<head>
  <meta charset="UTF-8" />
  <link href="style.css" rel="stylesheet" type="text/css" />
  <title>Tambah Barang</title>
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.0-beta1/dist/css/bootstrap.min.css"
  />
</head>
<body>
  <div class="container">
      <div class="row m-0">
          <div class="col-md-5 mx-auto">
              <div class="card mt-3">
                  <div class="card-header text-center">
                      <h1>Tambah Barang</h1>
                  </div>
                  <div class="card-body">
                      <form method="post" action="tambah.php" enctype="multipart/form-data">
                          <div class="mb-3">
                              <label>Nama Barang</label>
                              <input type="text" name="nama" />
                          </div>
                          <div class="mb-3">
                              <label>Kategori</label>
                              <select name="kategori">
                                  <option value="Komputer">Komputer</option>
                                  <option value="Elektronik">Elektronik</option>
                                  <option value="Hand Phone">HandPhone</option>
                              </select>
                          </div>
                          <div class="mb-3">
                              <label>Harga Jual</label>
                              <input type="text" name="harga_jual" />
                          </div>
                          <div class="mb-3">
                              <label>Harga Beli</label>
                              <input type="text" name="harga_beli" />
                          </div>
                          <div class="mb-3">
                              <label>Stok</label>
                              <input type="text" name="stok" />
                          </div>
                          <div class="mb-3">
                              <label>File Gambar</label>
                              <input type="file" name="file_gambar" />
                          </div>
                          <a href="index.php" class="btn btn-secondary">Kembali</a>
                          <button class="btn btn-success" name="submit" type="submit">
                              Tambah
                          </button>
                      </form>
                  </div>
              </div>
          </div>
      </div>
  </div>
  </div>
</body>
</html>
```

<br>
<br>

## Form Ubah.php

<img src="output/ubah.png">

<br>

- Sintax ini merupakan sebuah form untuk mengubah data barang yang telah tersimpan dalam database. Pertama, dilakukan koneksi ke database menggunakan file koneksi.php. Kemudian dilakukan pengecekan apakah form telah di-submit menggunakan isset($\_POST['submit']). Jika ya, maka dilakukan proses update data barang dengan mengambil nilai-nilai form dan mengganti data lama di database dengan yang baru. Gambar barang yang diunggah akan diubah dengan menggunakan fungsi move_uploaded_file. Jika tidak ada gambar yang diunggah, maka gambar akan tetap menggunakan gambar lama.

  ```php
  /<?php
  error_reporting(E_ALL);
  include_once 'koneksi.php';
  if (isset($_POST['submit'])) {
    $id = $_POST['id'];
    $nama = $_POST['nama'];
    $kategori = $_POST['kategori'];
    $harga_jual = $_POST['harga_jual'];
    $harga_beli = $_POST['harga_beli'];
    $stok = $_POST['stok'];
    $file_gambar = $_FILES['file_gambar'];
    $gambar = null;
    if ($file_gambar['error'] == 0) {
        $filename = str_replace(' ', '_', $file_gambar['name']);
        $destination = dirname(__FILE__) . '/gambar/' . $filename;
        if (move_uploaded_file($file_gambar['tmp_name'], $destination)) {
            $gambar = 'gambar/' . $filename;;
        }
    }
    $sql = 'UPDATE data_barang SET ';
    $sql .= "nama = '{$nama}', kategori = '{$kategori}', ";
    $sql .= "harga_jual = '{$harga_jual}', harga_beli = '{$harga_beli}',
  stok = '{$stok}' ";
    if (!empty($gambar))
        $sql .= ", gambar = '{$gambar}' ";
    $sql .= "WHERE id_barang = '{$id}'";
    $result = mysqli_query($conn, $sql);
    header('location: index.php');
  }
  $id = $_GET['id'];
  $sql = "SELECT * FROM data_barang WHERE id_barang = '{$id}'";
  $result = mysqli_query($conn, $sql);
  if (!$result) die('Error: Data tidak tersedia');
  $data = mysqli_fetch_array($result);
  function is_select($var, $val)
  {
    if ($var == $val) return 'selected="selected"';
    return false;
  }
  ?>

  ```

  ```html
  <!DOCTYPE html>
  <html lang="en"></html>
  <head>
    <meta charset="UTF-8" />
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Ubah Barang</title>

    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.0-beta1/dist/css/bootstrap.min.css"
    />
  </head>
  </head>
  <body>
    <div class="container">
        <div class="row m-0">
            <div class="col-md-5 mx-auto">
                <div class="card mt-3">
                    <div class="card-header text-center">
                        <h1>Ubah Barang</h1>
                        <div class="main">
                            <form method="post" action="ubah.php" enctype="multipart/form-data">
                                <div class="mb-3">
                                    <label>Nama Barang</label>
                                    <input type="text" name="nama" value="<?php echo
                                                                            $data['nama']; ?>" />
                                </div>
                                <div class="mb-3">
                                    <label>Kategori</label>
                                    <select name="kategori">
                                        <option <?php echo is_select('Komputer', $data['kategori']); ?> value="Komputer">Komputer</option>
                                        <option <?php echo is_select('Komputer', $data['kategori']); ?> value="Elektronik">Elektronik</option>
                                        <option <?php echo is_select('Komputer', $data['kategori']); ?> value="Hand Phone">HandPhone</option>
                                    </select>
                                </div>
                                <div class="mb-3">
                                    <label>Harga Jual</label>
                                    <input type="text" name="harga_jual" value="<?php echo
                                                                                $data['harga_jual']; ?>" />
                                </div>
                                <div class="mb-3">
                                    <label>Harga Beli</label>
                                    <input type="text" name="harga_beli" value="<?php echo
                                                                                $data['harga_beli']; ?>" />
                                </div>
                                <div class="mb-3">
                                    <label>Stok</label>
                                    <input type="text" name="stok" value="<?php echo
                                                                            $data['stok']; ?>" />
                                </div>
                                <div class="mb-3">
                                    <label>File Gambar</label>
                                    <input type="file" name="file_gambar" />
                                </div>
                                <div class="submit">
                                    <input type="hidden" name="id" value="<?php echo
                                                                            $data['id_barang']; ?>" />
                                </div>
                                <a href="index.php" class="btn btn-secondary">Kembali</a>
                                <button class="btn btn-warning" name="submit" type="submit">
                                    Edit
                                </button>
                            </form>
                        </div>
                    </div>
            </body>
  </html>
  ```

<br>
<br>

## Form Hapus.php

  <img src="output/hapus.png">

<br>

- Sintas ini merupakan script PHP untuk menghapus data pada tabel "data_barang" berdasarkan ID tertentu yang diterima dari parameter GET. Koneksi ke database diatur dengan menggunakan file "koneksi.php". Kemudian, sebuah query SQL dijalankan dengan menggunakan fungsi "mysqli_query()" untuk menghapus data berdasarkan ID yang diterima. Setelah itu, pengguna akan dialihkan kembali ke halaman "index.php" dengan menggunakan fungsi "header()".

```php
<?php
include_once 'koneksi.php';
$id = $_GET['id'];
$sql = "DELETE FROM data_barang WHERE id_barang = '{$id}'";
$result = mysqli_query($conn, $sql);
header('location: index.php');
?>
```

<br>

# TERIMAKASIH
