# crud-php-postgres
contoh CRUD PHP sederhana dengan postgres


## Install Postgres via Docker

1. Pastikan docker telah terinstall
2. Download image postgres sesuai versi yang dibutuhkan

    ```bash
    # versi terbaru
    docker pull postgres

    # versi 9.6
    docker pull postgres:9.6
    ```

    NB: bisa install lebih dari 1 versi image

## Jalankan Image Postgres

Untuk menjalankan image prosgres, ada 2 cara. Bisa langsung menggunakan perintah `docker`, atau bisa juga menggunakan perintah `docker-compose` (cara ini lebih direkomendasikan untuk kemudahan).

### 1. Buat Folder Data untuk Volume

```bash
mkdir -p data/db/postgres
```

### Menggunakan perintah `docker`

```bash
docker run -d \
  --name server-postgres-96 \
  -e POSTGRES_PASSWORD=123456 \
  -e PGDATA=/var/lib/postgresql/data/pgdata \
  -v "/$(pwd)/data/db/postgres":/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:9.6
```

Perintah di atas akan membuat container baru dengan nama **server-postgres-96**, mem-*forward* port `5432` yang ada di dalam container (docker) ke port `5432` yang ada di host.

Cek apakah docker sudah jalan dengan perintah:

```bash
docker ps
```

## Cara Masuk ke Dalam Container

Postgres yang kita jalankan sekarang berada di dalam container, sehingga kita tidak bisa mengaksesnya secara langsung dari komputer host. Yang harus kita lakukan adalah dengan masuk ke dalam container terlebih dahulu.

Perintahnya:

```bash
docker exec -ti server-postgres-96 bash
```

Selanjutnya kita sudah masuk ke dalam container **server-postgres-96** dan memanggil perintah `bash`.

## Membuat database

Setelah berhasil masuk ke dalam container postgres, kita bisa membuat database menggunakan perintah `psql`.

```bash
psql -U postgres
```

```psql
root@332ef4998d96:/# psql -U postgres
psql (9.6.24)
Type "help" for help.

postgres=# create database database_baru;
CREATE DATABASE
postgres=# \l
                                   List of databases
     Name      |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
---------------+----------+----------+------------+------------+-----------------------
 database_baru | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 postgres      | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0     | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres
 template1     | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres
(4 rows)
postgres=#
```


## Import Database Dari File di Luar Container

```bash
cat data.psql | docker exec -i server-postgres-96 psql -U postgres -d database_baru
```

## Tes PHP

Jalankan file `index.php` dengan web server, contoh:

```bash
php -S localhost:5000
```