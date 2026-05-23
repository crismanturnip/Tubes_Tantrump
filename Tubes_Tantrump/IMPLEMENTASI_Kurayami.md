# Panduan Implementasi Kurayami

File ini menjelaskan apa saja yang perlu kamu lakukan dari rekomendasi di `README.md`, lokasi file yang harus diedit, dan bagian mana yang sudah mulai saya implementasikan.

## 1. Ganti Metadata Bot

Lokasi:

```text
Kurayami/Kurayami.json
```

Yang diganti:

- `name`: nama bot di arena.
- `version`: versi strategi bot.
- `authors`: nama tim/pembuat.
- `description`: deskripsi singkat strategi.
- `gameTypes`: tipe battle yang didukung.
- `platform`: sesuaikan dengan target framework di `.csproj`.

Status implementasi:

- Sudah diganti menjadi `Kurayami`.
- Versi sudah dibuat `0.1 Tactical Baseline`.
- Deskripsi sudah disesuaikan dengan strategi kompetitif.
- `platform` sudah disamakan ke `.Net 9.0`.

## 2. Pastikan Kode Membaca File Metadata yang Benar

Lokasi:

```text
Kurayami/Kurayami.cs
```

Bagian yang penting:

```csharp
Kurayami() : base(BotInfo.FromFile("Kurayami.json")) { }
```

Status implementasi:

- Sudah diperbaiki. Kode sekarang mencari `Kurayami.json`, sesuai nama file metadata yang ada.

## 3. Ganti Identitas Visual Bot

Lokasi:

```text
Kurayami/Kurayami.cs
```

Method:

```csharp
ConfigureIdentity();
```

Yang bisa kamu ubah:

- `BodyColor`
- `TurretColor`
- `RadarColor`
- `GunColor`
- `BulletColor`
- `ScanColor`
- `TracksColor`

Status implementasi:

- Sudah dibuat tema warna khusus untuk Kurayami.

## 4. Aktifkan Radar Lock

Lokasi:

```text
Kurayami/Kurayami.cs
```

Method utama:

```csharp
SweepRadar();
LockRadar();
OnScannedBot(ScannedBotEvent e);
```

Cara kerjanya:

- Kalau belum ada target, radar berputar mencari musuh.
- Kalau target ditemukan, posisi target disimpan.
- Radar diarahkan kembali ke target agar scan tidak cepat hilang.

Status implementasi:

- Sudah dibuat radar sweeping dan radar lock sederhana.

## 5. Simpan Target Memory

Lokasi:

```text
Kurayami/Kurayami.cs
```

Class:

```csharp
TargetInfo
```

Data yang disimpan:

- ID target.
- Posisi target sekarang.
- Posisi target sebelumnya.
- Energi target.
- Arah dan kecepatan target.
- Jarak target.
- Turn terakhir target terlihat.

Status implementasi:

- Sudah dibuat target memory.
- Target otomatis dilupakan kalau terlalu lama tidak terlihat.

## 6. Pilih Target yang Lebih Bagus

Lokasi:

```text
Kurayami/Kurayami.cs
```

Method:

```csharp
ShouldSwitchTarget(ScannedBotEvent e);
TargetScore(double distance, double energy, int lastSeenTurn);
```

Logika saat ini:

- Musuh lebih dekat lebih diprioritaskan.
- Musuh dengan energi rendah mendapat bonus.
- Target lama yang tidak terlihat mendapat penalti.

Status implementasi:

- Sudah dibuat target selection sederhana.

## 7. Atur Power Tembakan

Lokasi:

```text
Kurayami/Kurayami.cs
```

Method:

```csharp
ChooseFirePower(double distance, double enemyEnergy);
```

Logika saat ini:

- Dekat: power lebih besar.
- Jauh: power lebih hemat.
- Energi sendiri rendah: power dibatasi.
- Musuh hampir mati: power cukup kecil saja.
- Akurasi rendah atau banyak peluru kena dinding: power diturunkan.

Status implementasi:

- Sudah dibuat adaptive fire power.

## 8. Arahkan Tembakan dengan Prediksi

Lokasi:

```text
Kurayami/Kurayami.cs
```

Method:

```csharp
AimAndFire();
PredictTargetPosition(double firePower);
AimTolerance(double distance);
```

Logika saat ini:

- Target dekat ditembak langsung.
- Target jauh diprediksi berdasarkan arah dan speed terakhir.
- Bot hanya menembak jika gun cukup mengarah ke target dan `GunHeat == 0`.

Status implementasi:

- Sudah dibuat direct targeting dan linear targeting sederhana.

## 9. Buat Movement Sulit Ditebak

Lokasi:

```text
Kurayami/Kurayami.cs
```

Method:

```csharp
MoveByState();
ChangeMoveDirection();
```

Logika saat ini:

- Jika ada target, bot bergerak lateral terhadap target.
- Jika terlalu dekat, bot menjauh.
- Jika terlalu jauh, bot sedikit mendekat.
- Arah gerak berubah periodik dan saat terkena peluru.

Status implementasi:

- Sudah mengganti pola maju-mundur menjadi lateral movement.

## 10. Hindari Dinding

Lokasi:

```text
Kurayami/Kurayami.cs
```

Method:

```csharp
NearWall();
TurnAwayFromWall();
OnHitWall(HitWallEvent e);
```

Logika saat ini:

- Bot menjaga margin dari dinding.
- Jika dekat dinding, bot diarahkan ke tengah arena.
- Jika menabrak dinding, arah gerak dibalik.

Status implementasi:

- Sudah dibuat wall avoidance dasar.

## 11. Reaksi Saat Kena Peluru atau Nabrak Bot

Lokasi:

```text
Kurayami/Kurayami.cs
```

Event:

```csharp
OnHitByBullet(HitByBulletEvent e);
OnHitBot(HitBotEvent e);
```

Logika saat ini:

- Saat kena peluru, bot langsung mengganti arah gerak.
- Saat menabrak bot, bot mencoba menembak jika target sangat dekat dan membalik arah.

Status implementasi:

- Sudah dibuat reaksi dasar untuk situasi berbahaya.

## 12. Catat Akurasi Tembakan

Lokasi:

```text
Kurayami/Kurayami.cs
```

Field:

```csharp
shotsFired;
shotsHit;
wallMisses;
```

Event:

```csharp
OnBulletHit(BulletHitBotEvent e);
OnBulletHitWall(BulletHitWallEvent e);
```

Status implementasi:

- Sudah dibuat pencatatan hit dan miss sederhana.
- Data ini dipakai untuk menurunkan bullet power jika akurasi buruk.

## 13. Langkah Testing

Build bot:

```bash
cd Kurayami
dotnet build
```

Lawan uji yang disarankan:

```text
sample-bots-csharp-0.30.0/TrackFire
sample-bots-csharp-0.30.0/Walls
sample-bots-csharp-0.30.0/SpinBot
sample-bots-csharp-0.30.0/VelocityBot
sample-bots-csharp-0.30.0/Target
```

Checklist saat battle:

- Apakah Kurayami masih sering menabrak dinding?
- Apakah radar tetap menemukan target?
- Apakah bot tetap bergerak saat ditembak?
- Apakah energi habis karena terlalu sering menembak?
- Apakah performa 1v1 dan melee berbeda jauh?

## 14. Pengembangan Berikutnya

Setelah versi ini jalan, urutan upgrade yang paling masuk akal:

1. Tuning `PreferredMinDistance` dan `PreferredMaxDistance`.
2. Tuning `ChooseFirePower`.
3. Tambahkan mode khusus 1v1 dan mode melee.
4. Tambahkan statistik per musuh.
5. Tambahkan movement yang lebih random saat musuh sering mengenai kita.
