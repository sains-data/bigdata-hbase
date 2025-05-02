# Big Data Hive & HBase - Single Node Cluster with Docker

Praktikum ini membuat sebuah *single-node Hive dan HBase cluster* menggunakan Docker dengan integrasi MySQL untuk penyimpanan data Hive Metastore dan ZooKeeper untuk koordinasi HBase. Anda dapat melakukan percobaan atau pembelajaran Big Data lokal di Windows.

## Versi Komponen
- **Hadoop**: 3.4.1
- **Hive**: 4.0.1
- **Tez**: 0.10.4
- **HBase**: 2.5.11
- **ZooKeeper**: 3.8.4
- **MySQL**: 8.0 (untuk Hive Metastore)

## Prasyarat

Sebelum memulai, pastikan Anda telah menginstal dan menyiapkan hal-hal berikut:

1. **Install Docker Desktop**  
   Unduh dan instal dari [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop).

2. **Login atau Daftar Akun Docker**  
   Buka Docker Desktop dan login menggunakan akun Docker Anda. Jika belum punya, silakan daftar terlebih dahulu.

3. **Install WSL dan Ubuntu 22.04 di Windows**  
   Buka PowerShell (Run as Administrator) dan jalankan:
   ```bash
   wsl --install -d Ubuntu-24.04
   ```

4. **Buka Terminal Ubuntu (WSL)**  
   Posisikan terminal di direktori `C:\`:
   ```bash
   cd /mnt/c
   ```

   Atau alternatif lain, gunakan terminal di Windows:
   ```bash
   cd C:\
   ```

## Langkah Instalasi

Setelah prasyarat selesai, ikuti langkah-langkah berikut:

5. **Clone Repo**
   ```bash
   git clone https://github.com/sains-data/bigdata-hbase.git 
   ```

6. **Download Dependencies** - jika belum memiliki file yang diperlukan:
   - [Hadoop 3.4.1](https://downloads.apache.org/hadoop/common/hadoop-3.4.1/hadoop-3.4.1.tar.gz)
   - [Hive 4.0.1](https://downloads.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz)  
   - [Tez 0.10.4](https://dlcdn.apache.org/tez/0.10.4/apache-tez-0.10.4-bin.tar.gz)
   - [HBase 2.5.11](https://archive.apache.org/dist/hbase/2.5.11/hbase-2.5.11-bin.tar.gz)
   - [ZooKeeper 3.8.4](https://archive.apache.org/dist/zookeeper/zookeeper-3.8.4/apache-zookeeper-3.8.4-bin.tar.gz)

7. **Buka folder di VS Code**  
   Buka folder `bigdata-hive-mysql` di VS Code, lalu ubah semua baris dengan format `CRLF` ke `LF`.  
   - Di VS Code: Klik `CRLF` di kanan bawah, lalu pilih `LF`.

8. **Arahkan Terminal ke Folder Repo**
   ```bash
   cd /mnt/c/bigdata-hive-hbase
   ```

   Atau pada terminal Windows:
   ```bash
   cd C:\bigdata-hive-hbase
   ```

9. **Salin file `hadoop-3.4.1.tar.gz` ke folder ini**  
   File ini *tidak* diunduh otomatis oleh Dockerfile.  
   - Unduh dari: [https://downloads.apache.org/hadoop/common/hadoop-3.4.1/hadoop-3.4.1.tar.gz](https://downloads.apache.org/hadoop/common/hadoop-3.4.1/hadoop-3.4.1.tar.gz)  
   - Pindahkan ke folder `bigdata-hive-mysql`

   > 💡 **Catatan:** Jika ingin Docker otomatis mengunduh file, bisa tambahkan perintah `curl` ke dalam `Dockerfile` seperti:
   ```Dockerfile
   RUN curl -O https://downloads.apache.org/hadoop/common/hadoop-3.4.1/hadoop-3.4.1.tar.gz
   ```

10. **Salin file `hive-4.0.1-bin.tar.gz` dan `tez-0.10.4-bin.tar.gz` ke folder ini**  
   File-file ini *tidak* diunduh otomatis oleh Dockerfile.  
   - Unduh dari:
     - [Apache Hive 4.0.1](https://downloads.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz)  
     - [Apache Tez 0.10.4](https://dlcdn.apache.org/tez/0.10.4/apache-tez-0.10.4-bin.tar.gz)

   - Pindahkan file-file tersebut ke folder `bigdata-hive-mysql`.

   > 💡 **Catatan:** Jika ingin Docker otomatis mengunduh file, bisa tambahkan perintah `wget` ke dalam `Dockerfile` seperti:
   ```Dockerfile
   RUN wget https://downloads.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz
   RUN wget https://downloads.apache.org/tez/tez-0.10.4/apache-tez-0.10.4-bin.tar.gz
   ```

   **Salin file hbase-2.5.11-bin.tar.gz dan file zookeeper-3.8.4-bin.tar.gz ke folder ini**

11. **Bangun Docker Image**
   ```bash
   bash build.sh
   ```

12. **Jalankan Container**
   ```bash
   bash start.sh
   ```

13. **Cek Apakah Container Jalan**
   ```bash
   docker ps -a
   ```

14. **Login ke Container**
   ```bash
   bash login.sh
   ```

15. **Cek Aktivitas Hive dan Port yang Tersedia**
   Cek port apakah sudah aktif untuk `hivemetastore` di port 10000 dan `hiveserver2` di port 10001 dengan perintah:
   ```bash
   netstat -nlpt
   ```

   Atau cek satu-satu dengan perintah:
   ```bash
   netstat -nlpt | grep 10000
   netstat -nlpt | grep 10001
   ```

   Jika belum muncul, tunggu beberapa menit dan cek kembali. Jika sudah muncul, lanjutkan ke langkah berikutnya.

16. **Koneksi ke Hive dengan Beeline - jika ingin memeriksa Hive Metastore**
   Setelah port 10001 aktif, lanjutkan dengan membuka koneksi Hive menggunakan Beeline:
   ```bash
   beeline -u jdbc:hive2://localhost:10001/ -n hive -p hive
   ```

   Di sini:
   - `-n` adalah username Hive (misalnya `hive`).
   - `-p` adalah password Hive (misalnya `hive`).

   Jika berhasil terkoneksi, Anda siap untuk menggunakan Hive.

17. **Akses GUI Hiveserver**
   Cek juga antarmuka web GUI Hiveserver di:  
   [http://localhost:10002](http://localhost:10002).

18. **Keluar dari Koneksi Hive**
   Setelah selesai, tekan `Ctrl + C` untuk keluar dari koneksi Beeline.

19. **Akses HBase Shell**:
   ```bash
   hbase shell
  ```
   Untuk mengakses **HBase Web UI**, Anda bisa membuka browser dan akses alamat berikut:
   ```
   http://localhost:16010
   ```
   Detail Akses GUI HBase:

   | Komponen HBase       | Port  | URL Akses                   |
   |-----------------------|-------|-----------------------------|
   | **HBase Master UI**   | 16010 | http://localhost:16010      |
   | **HBase REST API**    | 16030 | http://localhost:16030      |

20. **Keluar dari Container**
   Setelah keluar dari Beeline, ketik:
   ```bash
   exit
   ```

21. **Hentikan Container**
   Hentikan container dengan menjalankan:
   ```bash
   bash stop.sh
   ```

   Tunggu hingga container benar-benar berhenti.

## Port yang Digunakan

### Hadoop Ecosystem
| Port  | Service                  | URL (jika ada)              |
|-------|--------------------------|-----------------------------|
| 9000  | HDFS NameNode            | -                           |
| 9866  | HDFS DataNode            | -                           |
| 9870  | HDFS Web UI              | http://localhost:9870       |
| 8088  | YARN ResourceManager     | http://localhost:8088       |
| 8030  | YARN Scheduler           | -                           |
| 19888 | MapReduce JobHistory     | http://localhost:19888      |

### Hive
| Port  | Service          | URL                     |
|-------|------------------|-------------------------|
| 10000 | Hive Metastore   | -                       |
| 10001 | HiveServer2      | jdbc:hive2://localhost:10001 |
| 10002 | Hive Web UI      | http://localhost:10002  |

### HBase
| Port  | Service              | URL                     |
|-------|----------------------|-------------------------|
| 16000 | HBase Master         | -                       |
| 16010 | HBase Master Web UI  | http://localhost:16010  |
| 16020 | HBase RegionServer   | -                       |
| 16030 | HBase REST Server    | http://localhost:16030  |

### ZooKeeper
| Port  | Purpose                  |
|-------|--------------------------|
| 2181  | Client connections       |
| 2888  | Peer communication       |
| 3888  | Leader election          |

### Lainnya
| Port  | Service  |
|-------|----------|
| 3306  | MySQL    |

## Yang Bisa Dilakukan di HBase Web UI:

1. **Melihat status cluster**  
   - Versi HBase
   - Region servers yang aktif
   - Load average

2. **Memantau tabel**  
   - Daftar tabel yang ada
   - Region distribution
   - Request metrics

3. **Logs dan Configuration**  
   - Konfigurasi HBase
   - Log files

## Jika Tidak terbuka:
1. Pastikan container sudah running:
   ```bash
   docker ps | grep bigdata-hive-hbase
   ```

2. Cek log HBase di container:
   ```bash
   docker exec -it bigdata-hive-hbase tail -f /hbase/logs/hbase-*.log
   ```

3. Verifikasi port sudah terexpose:
   ```bash
   docker port bigdata-hive-hbase | grep 16010
   ```
   atau
   ```bash
   netstat -nlpt | grep 16010
   ```

4. Jika menggunakan **Windows/Mac**, pastikan:  
   - Docker Desktop sudah running  
   - Tidak ada konflik port di host (cek dengan `netstat -ano | findstr 16010` di Windows)

## Kontak

Untuk pertanyaan atau kontribusi, silakan buka *Issue* di repo ini atau hubungi Asisten Praktikum Analisis Big Data.