Strategi greedy yang dipakai Kurayami adalah memilih musuh yang paling menguntungkan untuk diserang saat itu juga.

Jadi setiap bot melihat musuh, bot akan menilai:

musuh mana yang paling dekat,
musuh mana yang energinya rendah,
musuh mana yang masih terlihat jelas oleh radar,
apakah posisi bot aman atau terlalu dekat dengan musuh,
apakah energi bot cukup untuk menembak.
Kalau musuh dekat dan energinya rendah, bot akan menganggap musuh itu sebagai target yang bagus. Kalau target sudah lama tidak terlihat, nilainya dikurangi karena kemungkinan posisinya sudah berubah.

Secara sederhana, Kurayami memakai greedy karena pada setiap turn bot mengambil keputusan terbaik berdasarkan kondisi saat itu, bukan menghitung semua kemungkinan sampai akhir permainan.

Contoh penjelasan untuk laporan:

Bot Kurayami menggunakan strategi greedy dengan memilih target yang paling menguntungkan pada setiap turn. Target dinilai berdasarkan jarak, energi musuh, dan seberapa baru target tersebut terdeteksi radar. Bot akan lebih memprioritaskan musuh yang dekat, memiliki energi rendah, dan masih terlihat jelas. Setelah target dipilih, bot menjaga jarak aman, menghindari dinding, dan menembak dengan kekuatan yang disesuaikan dengan jarak serta energi. Strategi ini bertujuan untuk memperoleh skor sebesar mungkin dari damage tembakan dan bertahan hidup lebih lama.

Kalau mau dibuat lebih singkat:

Kurayami memakai strategi greedy dengan cara memilih musuh terbaik untuk diserang pada kondisi saat itu. Bot memilih target berdasarkan jarak, energi musuh, dan informasi radar. Setelah itu bot bergerak untuk menjaga jarak aman dan menembak dengan kekuatan yang disesuaikan. Tujuannya adalah mendapatkan damage sebesar mungkin sambil tetap bertahan hidup.