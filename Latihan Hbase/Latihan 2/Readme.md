# Latihan Lanjutan HBase

## 1. Manipulasi Data Dasar

### Membuat tabel dengan spesifikasi kolom
```hbase
create 'customer', 
  {NAME => 'personal', VERSIONS => 3}, 
  {NAME => 'contact', VERSIONS => 1, BLOCKSIZE => '65536'},
  {NAME => 'history', TTL => '2592000'}  # TTL 30 hari dalam detik
```

### Memasukkan data dengan timestamp eksplisit
```hbase
put 'customer', 'cust001', 'personal:name', 'John Doe', 1640995200000  # 1 Jan 2022
put 'customer', 'cust001', 'contact:email', 'john@example.com'
put 'customer', 'cust001', 'contact:phone', '+1234567890'
```

### Membaca data dengan filter versi
```hbase
get 'customer', 'cust001', {COLUMN => 'personal:name', VERSIONS => 3}
```

## 2. Operasi Batch

### Menggunakan batch operations
```hbase
batch_start
put 'customer', 'cust002', 'personal:name', 'Jane Smith'
put 'customer', 'cust002', 'contact:email', 'jane@example.com'
put 'customer', 'cust003', 'personal:name', 'Bob Johnson'
batch_execute
```

### Increment counter value
```hbase
incr 'customer', 'cust001', 'stats:visits', 1
get_counter 'customer', 'cust001', 'stats:visits'
```

## 3. Filter dan Scan Lanjutan

### Scan dengan filter prefix row key
```hbase
scan 'customer', {ROWPREFIXFILTER => 'cust00'}
```

### Scan dengan filter kolom
```hbase
scan 'customer', {COLUMNS => ['personal:name', 'contact:email']}
```

### Filter nilai
```hbase
import org.apache.hadoop.hbase.filter.CompareFilter
import org.apache.hadoop.hbase.filter.SubstringComparator
scan 'customer', {FILTER => "ValueFilter(=, 'substring:example')"}
```

## 4. Manajemen Tabel

### Menambahkan family kolom baru
```hbase
disable 'customer'
alter 'customer', {NAME => 'preferences', VERSIONS => 1}
enable 'customer'
```

### Melakukan major compaction
```hbase
major_compact 'customer'
```

### Membuat snapshot
```hbase
snapshot 'customer', 'customer_backup_2023'
```

## Tips Penting Latihan:

1. Selalu periksa status cluster sebelum memulai:
   ```hbase
   status
   ```

2. Gunakan `describe` untuk melihat schema tabel:
   ```hbase
   describe 'customer'
   ```

3. Untuk operasi admin, gunakan:
   ```hbase
   hbase> whoami
   hbase> balance_switch true
   ```

4. Monitor performa dengan:
   ```bash
   hbase hbck
   hbase org.apache.hadoop.hbase.tool.Canary
   ```