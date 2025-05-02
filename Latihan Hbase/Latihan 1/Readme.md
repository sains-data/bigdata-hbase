# Latihan Dasar Hbase

## **Tujuan:**

1. Memahami konfigurasi dan setup HBase di atas Hadoop.
2. Mampu melakukan operasi dasar di HBase seperti membuat tabel, memasukkan data, mengambil data, dan menghapus data.
3. Menggunakan shell HBase untuk operasi CRUD (Create, Read, Update, Delete).


## 📁 **Persiapan Sistem**

Sebelum mulai, pastikan:

* Hadoop sudah berjalan
* HBase sudah dikonfigurasi dan berjalan
* Zookeeper aktif (`jps` menampilkan `QuorumPeerMain`)


## 🧩 **Latihan 1: Menjalankan HBase Shell**

### Langkah:

```bash
hbase shell
```

Jika berhasil, kamu akan masuk ke prompt `hbase(main):001:0>`.


## 🧩 **Latihan 2: Membuat Tabel**

### Tujuan:

Membuat tabel `mahasiswa` dengan kolom `identitas` dan `nilai`.

### Perintah:

```hbase
create 'mahasiswa', 'identitas', 'nilai'
```


## 🧩 **Latihan 3: Menambahkan Data**

### Tambahkan beberapa data mahasiswa ke dalam tabel.

```hbase
put 'mahasiswa', 'mhs001', 'identitas:nama', 'Budi'
put 'mahasiswa', 'mhs001', 'identitas:nim', '12345678'
put 'mahasiswa', 'mhs001', 'nilai:matematika', '85'
put 'mahasiswa', 'mhs001', 'nilai:bahasa', '90'

put 'mahasiswa', 'mhs002', 'identitas:nama', 'Siti'
put 'mahasiswa', 'mhs002', 'identitas:nim', '87654321'
put 'mahasiswa', 'mhs002', 'nilai:matematika', '95'
put 'mahasiswa', 'mhs002', 'nilai:bahasa', '88'
```


## 🧩 **Latihan 4: Membaca Data**

### Ambil semua data dari tabel:

```hbase
scan 'mahasiswa'
```

### Ambil data satu baris:

```hbase
get 'mahasiswa', 'mhs001'
```

### Ambil kolom tertentu:

```hbase
get 'mahasiswa', 'mhs001', 'nilai:matematika'
```


## 🧩 **Latihan 5: Update Data**

```hbase
put 'mahasiswa', 'mhs001', 'nilai:matematika', '92'
```

---

## 🧩 **Latihan 6: Hapus Data**

### Hapus kolom:

```hbase
delete 'mahasiswa', 'mhs001', 'nilai:bahasa'
```

### Hapus baris:

```hbase
deleteall 'mahasiswa', 'mhs002'
```

## 🧩 **Latihan 7: Hapus Tabel**

```hbase
disable 'mahasiswa'
drop 'mahasiswa'
```


## 📝 **Tugas Tambahan**

1. Buat tabel `produk` dengan kolom `info` dan `harga`.
2. Tambahkan minimal 3 produk.
3. Ambil data produk berdasarkan ID.
4. Update harga salah satu produk.
5. Hapus salah satu produk.