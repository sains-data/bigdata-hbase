# Latihan HBase dengan Integrasi HDFS

Dengan latihan ini, Anda akan memahami:
1. Operasi dasar HBase
2. Integrasi dengan HDFS
3. Teknik optimasi
4. Pola desain tabel yang baik

## 1. Persiapan Environment

Pastikan semua service sudah berjalan:
```bash
jps
```
Harus muncul proses:
- NameNode (HDFS)
- HMaster (HBase)
- HRegionServer
- ZooKeeper

## 2. Latihan Dasar HBase

### Masuk ke HBase Shell
```bash
hbase shell
```
cek status hbase apakah sudah aktif

```hbase
status
```
output akan seperti:
```
1 active master, 0 backup masters, 1 servers, 0 dead, 2.0000 average load
```

### Membuat Tabel
```hbase
create 'latihan', 'cf1', 'cf2'
```

### Melihat Daftar Tabel
```hbase
list
```

### Memasukkan Data
```hbase
put 'latihan', 'row1', 'cf1:nama', 'John'
put 'latihan', 'row1', 'cf1:umur', '25'
put 'latihan', 'row2', 'cf2:kota', 'Jakarta'
```

### Membaca Data
```hbase
scan 'latihan'
get 'latihan', 'row1'
```

keluar dari hbase maka ketik exit pada hbase command line

selanjutnya masuk ke root bash

## 3. Integrasi HBase-HDFS

### Cek Data HBase di HDFS
```bash
hdfs dfs -ls /hbase
hdfs dfs -ls /hbase/data/default/latihan
```

### Export Tabel ke HDFS
masuk lagi ke hbase shell
```bash
hbase shell
```
lalu satu persatu eksekusi perintah berikut:

```hbase
disable 'latihan'
snapshot 'latihan', 'latihan_snapshot'
clone_snapshot 'latihan_snapshot', 'latihan_backup'
enable 'latihan'
```
keluar dulu dari hbase, lalu periksa:

### Cek Snapshot di HDFS
```bash
hdfs dfs -ls /hbase/.hbase-snapshot
```

## 4. Latihan Lanjutan

masuk kembali dengan `hbase shell`

### Membuat Tabel dengan Pre-splitting
```hbase
create 'latihan_besar', {NAME => 'cf', VERSIONS => 3}, {NUMREGIONS => 10, SPLITALGO => 'HexStringSplit'}
```

keluar lagi hbase shell, lalu ke root bash

### Bulk Load dari HDFS
1. Buat file data di HDFS:
```bash
echo "row1,cf1:name,John" > data.csv
echo "row2,cf1:name,Jane" >> data.csv
hdfs dfs -put data.csv /tmp/
```

2. Konversi ke HFile:
```bash
hbase org.apache.hadoop.hbase.mapreduce.ImportTsv \
-Dimporttsv.separator=, \
-Dimporttsv.columns=HBASE_ROW_KEY,cf1:name \
latihan_besar \
/tmp/data.csv
```

3. Lakukan bulk load:
```bash
hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmp/data.csv latihan_besar
```

## 5. Monitoring

### Cek UI HBase
Akses: http://localhost:16010

masuk ke hbase shell dahulu lalu periksa:

### Cek Region Server
```hbase
status 'detailed'
```

### Cek Kompaksi
```hbase
major_compact 'latihan'
```

## 6. Cleanup

### Menghapus Tabel
```hbase
disable 'latihan'
drop 'latihan'
```

kembali ke root bash

### Hapus Data di HDFS
```bash
hdfs dfs -rm -r /hbase/data/default/latihan
```

## Contoh Kasus: Sistem User Profile

1. Buat tabel:
```hbase
create 'user_profiles', 
  {NAME => 'basic', VERSIONS => 3}, 
  {NAME => 'preferences', VERSIONS => 1}, 
  {NAME => 'history', VERSIONS => 10, TTL => 2592000}
```

2. Insert data:
```hbase
put 'user_profiles', 'user1', 'basic:name', 'Alice'
put 'user_profiles', 'user1', 'basic:email', 'alice@example.com'
put 'user_profiles', 'user1', 'preferences:theme', 'dark'
put 'user_profiles', 'user1', 'history:login', '2023-01-01T12:00:00Z'
```

3. Query data:
```hbase
get 'user_profiles', 'user1', {COLUMN => 'basic:name'}
scan 'user_profiles', {COLUMNS => ['basic:name', 'preferences:theme']}
```

## Tips Penting:

1. **Desain Row Key**:
   - Gunakan composite key jika perlu: `region|userid|timestamp`
   - Hindari sequential keys untuk menghindari hotspotting

2. **Optimasi Performance**:
   ```hbase
   alter 'latihan', {NAME => 'cf1', BLOCKSIZE => '65536', BLOOMFILTER => 'ROW'}
   ```

3. **Backup ke HDFS**:
```bash
hbase org.apache.hadoop.hbase.mapreduce.Export \
latihan \
/hbase/backup/latihan_$(date +%F)
```

4. **Restore dari HDFS**:
```bash
hbase org.apache.hadoop.hbase.mapreduce.Import \
latihan_restored \
/hbase/backup/latihan_2023-01-01
```