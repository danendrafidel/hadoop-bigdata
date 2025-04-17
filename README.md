# Hadoop-Bigdata

## Danendra Fidel Khansa_5027231063_Big Data and Lake House (A)

## - **Persiapan Apache Hadoop :**

- `docker pull silicoflare/hadoop:amd`

  ![Hadoop Screenshot](<img/hadoop%20(1).png>)

- `docker tag silicoflare/hadoop:amd hadoop`
  ![Hadoop Screenshot](<img/hadoop%20(2).png>)
- `docker run -it -p 9870:9870 -p 8088:8088 -p 9864:9864 --name hadoop-container hadoop`

- `init`
  ![Hadoop Screenshot](<img/hadoop%20(3).png>)

- `source ~/.bashrc`

## - **Apache Hadoop (HDFS)**

    - Buat direktori movielens di HDFS.
    - Upload file u.data ke direktori tersebut.
    - Tampilkan 10 baris pertama dari file.
    - Hitung ukuran file di HDFS.

Pengerjaan :

1. `apt install unzip`
2. `wget https://files.grouplens.org/datasets/movielens/ml-100k.zip`
3. `unzip ml-100k.zip`
   ![Hadoop Screenshot](<img/hadoop%20(4).png>)

4. `hdfs dfs -mkdir /movielens`
   ![Hdfs Screenshot](<img/hdfs%20(1).png>)
5. `hdfs dfs -put /root/ml-100k/u.data /movielens/`
   ![Hdfs Screenshot](<img/hdfs%20(2).png>)
6. `hdfs dfs -cat /movielens/u.data | head -n 10`
   ![Hdfs Screenshot](<img/hdfs%20(3).png>)
7. `hdfs dfs -du -h /movielens/u.data`
   ![Hdfs Screenshot](<img/hdfs%20(4).png>)
   ![Hdfs Screenshot](<img/hdfs%20(5).png>)

## - **Apache Pig**

    - Load file u.data ke Pig.
    - Hitung rata-rata rating per item_id (film).
    - Ambil hanya film yang memiliki rating rata-rata ≥ 4.0.
    - Simpan hasil akhir ke output/film_favorit.

Pengerjaan :

1. `pig`
   ![Pig Screenshot](<img/pig%20(1).png>)
2. Hitung rata-rata rating per item_id (film).

```sql
raw_data = LOAD '/movielens/u.data' USING PigStorage('\t')
           AS (user_id:int, item_id:int, rating:int, timestamp:long);
```

![Pig Screenshot](<img/pig%20(2).png>)

3. Hitung rata-rata rating per item_id (film).

```sql
grouped = GROUP raw_data BY item_id;

avg_rating = FOREACH grouped GENERATE
             group AS item_id,
             AVG(raw_data.rating) AS avg_rating;
```

![Pig Screenshot](<img/pig%20(3).png>)

4. Ambil hanya film yang memiliki rating rata-rata ≥ 4.0.

```sql
fav_movies = FILTER avg_rating BY avg_rating >= 4.0;
```

![Pig Screenshot](<img/pig%20(4).png>)

5. Simpan hasil akhir ke output/film_favorit.

```sql
STORE fav_movies INTO '/output/film_favorit' USING PigStorage('\t');

```

![Pig Screenshot](<img/pig%20(5).png>)

6. Tampilkan Hasilnya `quit`
   ![Pig Screenshot](<img/pig%20(6).png>)

   `hdfs dfs -rm -r /output/film_favorit`
   ![Pig Screenshot](<img/pig%20(7).png>)

## - **Apache Hive**

    - Buat database movielens.
    - Buat tabel ratings (user_id INT, item_id INT, rating INT, timestamp BIGINT).
    - Load data dari file u.data.
    - Hitung rata-rata rating setiap film.
    - Ambil 10 film dengan rata-rata rating tertinggi.

Pengerjaan :

1.  Ikuti langkaah dibawah jika ada error

    `rm -rf metastore_db` -> `rm -f derby.log` -> `schematool -dbType derby -initSchema`

2.  `hive --service metastore` -> `hive`
    ![Hive Screenshot](<img/hive%20(1).png>)
    ![Hive Screenshot](<img/hive%20(2).png>)

3.  Buat database movielens.

```sql
CREATE DATABASE movielens;
USE movielens;
```

![Hive Screenshot](<img/hive%20(3).png>)

4. Buat tabel ratings (user_id INT, item_id INT, rating INT, timestamp BIGINT).

```sql
CREATE TABLE ratings (
  user_id INT,
  item_id INT,
  rating INT,
  timestamp BIGINT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t';
```

![Hive Screenshot](<img/hive%20(4).png>)

5. Load data dari file u.data.

```sql
LOAD DATA INPATH '/movielens/u.data' INTO TABLE ratings;
```

![Hive Screenshot](<img/hive%20(5).png>)

6. Hitung rata-rata rating setiap film.

```sql
SELECT item_id, AVG(rating) AS avg_rating
FROM ratings
GROUP BY item_id;
```

![Hive Screenshot](<img/hive%20(6).png>)

7. Ambil 10 film dengan rata-rata rating tertinggi.

```sql
SELECT item_id, AVG(rating) AS avg_rating
FROM ratings
GROUP BY item_id
ORDER BY avg_rating DESC
LIMIT 10;
```

![Hive Screenshot](<img/hive%20(7).png>)
