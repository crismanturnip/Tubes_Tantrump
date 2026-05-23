# Rekomendasi Engine Kurayami

Dokumen ini berisi rekomendasi peningkatan untuk template bot **Kurayami** pada Robocode Tank Royale. Tujuannya bukan hanya membuat bot bisa jalan, tetapi membuatnya siap diadu melawan bot lain dengan strategi yang jelas, stabil, dan mudah ditingkatkan.

## Status Awal Bot

Bot awal memakai pola dasar template: maju 100, mundur 100, lalu menembak power 1. Event seperti `OnScannedBot`, `OnHitBot`, dan `OnHitWall` baru mencetak log, belum mengubah keputusan bot.

Masalah utama:

- Gerakan mudah ditebak karena hanya maju-mundur.
- Tembakan tidak diarahkan secara aktif ke target.
- Radar belum dikunci untuk mencari dan mempertahankan target.
- Bot belum memakai informasi energi, jarak, dinding, kecepatan musuh, atau posisi arena.
- Tidak ada mode strategi, misalnya menyerang, bertahan, kabur, atau duel akhir.

## Prioritas Pengembangan

### 1. Identitas Bot

Ubah metadata bot menjadi `Kurayami` agar mudah dikenali saat battle.

Checklist:

- Rename nama bot di file JSON.
- Sesuaikan nama class atau minimal nama project bila aturan tugas memperbolehkan.
- Gunakan warna konsisten untuk body, turret, radar, scan, dan bullet.
- Tambahkan versi strategi, misalnya `Kurayami v0.1 Tactical Baseline`.

### 2. Radar Lock

Radar adalah mata bot. Kurayami perlu terus mencari musuh, lalu mempertahankan scan pada target yang dipilih.

Rekomendasi:

- Di `Run()`, putar radar/gun terus-menerus untuk scanning.
- Di `OnScannedBot`, simpan data target: posisi X/Y, jarak, bearing, energi, dan waktu scan.
- Jika target masih valid, arahkan radar atau gun kembali ke posisi target.
- Jika target hilang terlalu lama, kembali ke mode sweeping.

Target minimal:

- Bot tidak menembak tanpa arah.
- Bot bisa terus mengikuti satu musuh sampai musuh mati atau menghilang.

### 3. Target Selection

Saat ada banyak bot, jangan asal menembak musuh pertama yang terlihat. Pilih target berdasarkan nilai ancaman dan peluang hit.

Prioritas target yang disarankan:

1. Musuh terdekat, karena peluang peluru mengenai target lebih tinggi.
2. Musuh dengan energi rendah, karena bisa menghasilkan kill bonus.
3. Musuh yang sedang mendekat atau sering menabrak.
4. Musuh yang pernah sering mengenai Kurayami.

Formula sederhana:

```text
score = jarakScore + energiLemahBonus + ancamanBonus
```

Target dengan score tertinggi menjadi target utama.

### 4. Firing Control

Skor besar datang dari damage peluru yang mengenai target, bukan dari peluru yang ditembak asal-asalan. Jangan selalu memakai `Fire(3)`.

Rekomendasi power peluru:

- Jarak dekat `< 150`: power 2.5 sampai 3.
- Jarak sedang `150-400`: power 1.5 sampai 2.
- Jarak jauh `> 400`: power 0.5 sampai 1.2.
- Energi sendiri `< 20`: power maksimal 1.
- Musuh hampir mati: gunakan power kecil yang cukup untuk membunuh.

Aturan aman:

- Tembak hanya jika `GunHeat == 0`.
- Tembak kuat hanya jika arah gun sudah dekat ke target.
- Hindari menghabiskan energi saat akurasi rendah.

### 5. Targeting

Naikkan kemampuan menembak dari direct targeting ke prediksi sederhana.

Tahap implementasi:

- Tahap 1: direct targeting dengan `GunBearingTo(e.X, e.Y)`.
- Tahap 2: linear targeting, prediksi posisi musuh dari perubahan posisi scan sebelumnya.
- Tahap 3: pilih direct atau linear berdasarkan jarak dan kestabilan gerak musuh.

Prinsip:

- Untuk target dekat, direct targeting sering cukup.
- Untuk target bergerak stabil, linear targeting lebih kuat.
- Untuk target zig-zag, kurangi bullet power dan fokus survival.

### 6. Movement Anti-Predictable

Gerakan Kurayami harus sulit ditebak. Pola maju-mundur konstan akan mudah dibaca bot lain.

Rekomendasi:

- Gunakan gerakan lateral terhadap target, bukan maju lurus ke musuh.
- Ubah arah secara periodik atau saat terkena peluru.
- Jaga jarak ideal sekitar 250-450 dari target.
- Hindari diam terlalu lama.
- Hindari menempel dinding kecuali memakai strategi wall movement yang disengaja.

Trigger perubahan arah:

- Terkena peluru.
- Terlalu dekat dengan dinding.
- Musuh terlalu dekat.
- Energi turun cepat.
- Sudah bergerak satu arah terlalu lama.

### 7. Wall Avoidance

Bot yang sering menabrak dinding kehilangan tempo dan menjadi sasaran mudah.

Rekomendasi:

- Buat margin aman dari dinding, misalnya 80-120 pixel.
- Jika posisi mendekati dinding, putar menjauh sebelum menabrak.
- Pada `OnHitWall`, langsung balik arah dan ubah sudut gerak.

Target minimal:

- Kurayami tidak terus mengulang tabrakan di dinding yang sama.

### 8. Energy Management

Energi adalah darah sekaligus amunisi. Kurayami perlu mengatur kapan agresif dan kapan hemat.

Mode energi:

- `Aggressive`: energi tinggi, target dekat, peluang hit besar.
- `Balanced`: energi sedang, gunakan power 1-2.
- `Survival`: energi rendah, fokus bergerak, tembak hanya saat yakin.
- `Finisher`: musuh energi rendah, pakai power secukupnya untuk kill.

Aturan penting:

- Jangan menembak power besar jika energi sendiri rendah.
- Jangan menembak jauh dengan power besar.
- Saat unggul energi, tetap bergerak dan tekan target.
- Saat kalah energi, perbanyak perubahan arah.

### 9. Event Handling

Event jangan hanya dipakai untuk log. Setiap event harus mengubah keputusan bot.

Implementasi yang disarankan:

- `OnScannedBot`: update target, aim, fire, dan adjust movement.
- `OnHitByBullet`: ubah arah dan tingkatkan evasive movement.
- `OnHitWall`: keluar dari dinding dengan arah baru.
- `OnHitBot`: jika musuh di depan dan dekat, tembak atau mundur.
- `OnBulletHitBot`: catat akurasi dan target yang berhasil terkena.
- `OnBulletHitWall`: kurangi kepercayaan pada strategi tembak jarak jauh.
- `OnBotDeath`: reset target jika target utama mati.

### 10. State Machine

Gunakan state machine agar strategi tidak berantakan di dalam satu loop besar.

State yang disarankan:

- `Search`: belum ada target, radar/gun sweeping.
- `Engage`: target terkunci, bergerak lateral, menembak terukur.
- `Evade`: terkena peluru atau posisi buruk, ubah arah.
- `WallRecover`: terlalu dekat dinding atau baru menabrak.
- `Finish`: target energi rendah, cari kill aman.

Contoh transisi:

```text
Search -> Engage      jika musuh terlihat
Engage -> Evade       jika terkena peluru
Engage -> WallRecover jika dekat dinding
Engage -> Finish      jika energi musuh rendah
Finish -> Search      jika target mati/hilang
```

### 11. Data Memory

Kurayami akan lebih kuat jika menyimpan memori sederhana selama ronde.

Data yang perlu disimpan:

- Posisi target terakhir.
- Energi target terakhir.
- Perubahan posisi target.
- Waktu target terakhir terlihat.
- Jumlah peluru ditembak.
- Jumlah hit.
- Bot yang paling sering memberi damage.

Gunakan data ini untuk:

- Menghitung akurasi.
- Mengubah bullet power.
- Memilih target prioritas.
- Mendeteksi musuh yang sering bergerak lurus.

### 12. Battle Testing

Jangan hanya test melawan satu bot. Kurayami harus diuji melawan beberapa tipe lawan.

Lawan uji dari sample bots:

- `TrackFire`: menguji movement karena dia tracking dan menembak.
- `Walls`: menguji kemampuan melawan bot yang bergerak di tepi arena.
- `SpinBot`: menguji stabilitas melawan gerakan melingkar.
- `VelocityBot`: menguji kemampuan menghadapi perubahan speed.
- `Target`: menguji akurasi tembakan ke bot pasif.

Checklist pengujian:

- Jalankan battle minimal 10 ronde per lawan.
- Catat ranking, damage, survival, dan pola mati.
- Uji 1v1 dan multi-bot.
- Jika kalah, lihat apakah penyebabnya aim, movement, wall hit, atau energi habis.

### 13. Roadmap Implementasi

#### Versi 0.1 - Tactical Baseline

- Rename bot menjadi Kurayami.
- Tambahkan warna dan identitas.
- Implementasi direct targeting.
- Tembak berdasarkan jarak.
- Perbaiki `OnHitWall` dan `OnHitByBullet`.

#### Versi 0.2 - Survival Core

- Tambahkan wall avoidance.
- Tambahkan random/lateral movement.
- Tambahkan state `Search`, `Engage`, dan `Evade`.
- Simpan target utama.

#### Versi 0.3 - Smart Gun

- Tambahkan linear targeting.
- Tambahkan target selection.
- Tambahkan akurasi sederhana.
- Sesuaikan bullet power berdasarkan performa.

#### Versi 0.4 - Competition Ready

- Tuning jarak ideal.
- Tuning fire power.
- Test 1v1 dan multi-bot.
- Buat catatan hasil battle.
- Hapus log berlebihan agar bot tidak lambat.

## Strategi Utama yang Direkomendasikan

Untuk kompetisi melawan bot buatan orang lain, strategi paling aman untuk Kurayami adalah:

```text
Lateral movement + radar lock + adaptive bullet power + simple target memory
```

Strategi ini lebih fleksibel daripada bot yang hanya diam menembak, hanya keliling dinding, atau hanya spin. Kurayami tidak harus langsung punya AI rumit; yang penting keputusan dasarnya benar dan sulit ditebak.

## Struktur Kode yang Disarankan

Pisahkan logika menjadi method kecil:

```csharp
UpdateTarget(e);
ChooseFirePower(distance, enemyEnergy);
AimAtTarget();
MoveAgainstTarget();
AvoidWalls();
SwitchState();
```

Dengan struktur ini, strategi bisa dituning tanpa membuat `Run()` dan event handler menjadi terlalu penuh.

## Definisi Bot Bagus

Kurayami dianggap bagus jika:

- Jarang menabrak dinding.
- Tidak diam saat sedang ditembak.
- Menembak hanya saat punya peluang mengenai target.
- Bisa bertahan sampai akhir ronde.
- Bisa mengalahkan minimal beberapa sample bot secara konsisten.
- Tetap stabil di battle ramai, bukan hanya kuat di 1v1.

## Cara Build Game Engine

1. Masuk ke directory `tank-royale-0.30.0`.

   ```bash
   cd tank-royale-0.30.0
   ```

2. Clean dan build `gui-app` dengan Gradle.

   ```bash
   ./gradlew :gui-app:clean
   ./gradlew :gui-app:build
   ```

3. Jalankan executable JAR dari directory `gui-app/build/libs`.

   ```bash
   java -jar ./gui-app/build/libs/robocode-tankroyale-gui-0.30.0.jar
   ```

## Catatan Akhir Engine

Fokus awal Kurayami sebaiknya bukan membuat strategi paling rumit, tetapi membuat strategi yang konsisten: lihat musuh, pilih target, bergerak sulit ditebak, tembak saat masuk akal, dan hemat energi saat kondisi buruk. Bot yang konsisten biasanya lebih berbahaya daripada bot yang punya banyak fitur tetapi keputusannya saling bertabrakan.


cara ganti hotspot jdk -> $env:JAVA_HOME="C:\Program Files\Eclipse Adoptium\jdk-17.0.19.10-hotspot" (agar jadi jdk 11)
