# Tubes Tantrump - Robocode Tank Royale

Repository ini berisi 4 bot Robocode Tank Royale untuk Tugas Besar Strategi Algoritma. Semua bot dibuat dengan C# dan .NET. Setiap bot memakai strategi greedy, yaitu mengambil keputusan terbaik berdasarkan kondisi yang sedang terlihat pada saat itu.

## Daftar Bot

### 1. Kurayami

Lokasi:

```text
src/main-bot/Kurayami
```

Kurayami adalah bot utama. Bot ini memilih musuh yang paling menguntungkan untuk diserang berdasarkan jarak, energi musuh, dan apakah musuh masih baru terlihat oleh radar.

Cara kerja greedy Kurayami:

- Musuh yang lebih dekat lebih diprioritaskan.
- Musuh dengan energi rendah lebih diprioritaskan karena lebih mudah dikalahkan.
- Musuh yang sudah lama tidak terlihat akan diabaikan.
- Kekuatan tembakan disesuaikan dengan jarak dan energi.
- Bot bergerak menyamping agar tidak mudah ditembak.

Tujuannya adalah mendapatkan damage dari tembakan, mengejar bonus kill, dan tetap bertahan selama mungkin.

### 2. Satsugi

Lokasi:

```text
src/alternative-bots/Satsugi
```

Satsugi adalah bot yang bermain agresif. Bot ini berusaha mendekati musuh, menekan dari jarak dekat, lalu menembak dengan kekuatan besar jika posisi sudah menguntungkan.

Cara kerja greedy Satsugi:

- Musuh yang terlihat langsung dikejar.
- Jika jarak sudah dekat, bot menembak dengan power besar.
- Jika sangat dekat, bot bisa memberi tekanan dengan tabrakan.
- Jika dekat dinding, bot langsung bergerak ke tengah arena.
- Jika tembakan sering meleset, power tembakan dikurangi.

Tujuannya adalah mendapatkan skor besar dari damage tembakan jarak dekat dan tekanan agresif ke musuh.

### 3. Shinji

Lokasi:

```text
src/alternative-bots/Shinji
```

Shinji adalah bot yang lebih bertahan. Bot ini mengutamakan posisi aman terlebih dahulu, lalu menyerang balik saat jarak dengan musuh sudah ideal.

Cara kerja greedy Shinji:

- Jika dekat dinding, bot segera menjauh ke tengah arena.
- Jika musuh terlalu dekat, bot bergerak menjauh secara diagonal.
- Jika jarak sudah aman, bot bergerak menyamping sambil menembak.
- Jika musuh jauh, bot mendekat secara hati-hati.
- Power tembakan disesuaikan dengan jarak dan energi bot.

Tujuannya adalah bertahan lebih lama, menghindari damage tidak perlu, lalu mendapatkan skor dari serangan balik.

### 4. Tatsuya

Lokasi:

```text
src/alternative-bots/Tatsuya
```

Tatsuya fokus mengejar musuh dengan energi paling rendah. Strateginya adalah mengunci target yang lemah agar bisa dikalahkan lebih cepat.

Cara kerja greedy Tatsuya:

- Bot mencari musuh dengan energi paling rendah.
- Target yang sudah dikunci akan terus dikejar.
- Jika energi target rendah, bot menyerang lebih agresif.
- Jika target hilang terlalu lama, bot mencari target baru.
- Bot memakai prediksi sederhana untuk menembak posisi musuh.

Tujuannya adalah mendapatkan bonus kill dan mengurangi jumlah musuh secepat mungkin.

## Requirement

Yang dibutuhkan untuk menjalankan program:

- Java/JDK untuk menjalankan GUI Robocode.
- .NET SDK 9.0 atau versi yang mendukung `net9.0`.
- Robocode Tank Royale GUI 0.30.0.

File GUI sudah tersedia di root repository:

```text
robocode-tankroyale-gui-0.30.0.jar
```

Cek Java:

```powershell
java -version
```

Cek .NET:

```powershell
dotnet --info
```

## Cara Build

Jalankan command berikut dari root repository:

```powershell
dotnet build .\src\main-bot\Kurayami\Kurayami.csproj
dotnet build .\src\alternative-bots\Satsugi\Satsugi.csproj
dotnet build .\src\alternative-bots\Shinji\Shinji.csproj
dotnet build .\src\alternative-bots\Tatsuya\Tatsuya.csproj
```

Atau masuk ke folder bot, lalu jalankan:

```powershell
dotnet build
```

## Cara Menjalankan GUI

Dari root repository:

```powershell
java -jar .\robocode-tankroyale-gui-0.30.0.jar
```

Jika folder bot belum terbaca di GUI, tambahkan folder berikut:

```text
src/main-bot
src/alternative-bots
```

## Cara Menjalankan Bot

Jalankan GUI terlebih dahulu, lalu jalankan bot dari terminal lain.

Kurayami:

```powershell
cd .\src\main-bot\Kurayami
.\Kurayami.cmd
```

Satsugi:

```powershell
cd .\src\alternative-bots\Satsugi
.\Satsugi.cmd
```

Shinji:

```powershell
cd .\src\alternative-bots\Shinji
.\Shinji.cmd
```

Tatsuya:

```powershell
cd .\src\alternative-bots\Tatsuya
.\Tatsuya.cmd
```

Untuk Linux, gunakan file `.sh` pada folder masing-masing bot.

Contoh:

```bash
cd src/main-bot/Kurayami
./Kurayami.sh
```

## Struktur Repository

```text
.
+-- src/
|   +-- main-bot/
|   |   +-- Kurayami/
|   +-- alternative-bots/
|       +-- Satsugi/
|       +-- Shinji/
|       +-- Tatsuya/
+-- doc/
+-- config.properties
+-- games.properties
+-- server.properties
+-- robocode-tankroyale-gui-0.30.0.jar
+-- README.md
```

Folder `doc` digunakan untuk menyimpan laporan tugas besar dalam bentuk PDF.

## Author

- Crisman Turnip
- Erhan Kurniawan

