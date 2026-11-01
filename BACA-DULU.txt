# Petualangan Kata — versi perbaikan IFP dan HP

Untuk Hisense 75WM61FE dengan Android 13 dan browser yang menyediakan akses kamera.

1. Ekstrak ZIP. Unggah seluruh isi folder aplikasi ke hosting **HTTPS**, termasuk folder `aset/`. Letakkan `index.html` di direktori utama hosting. Muat ulang halaman setelah mengganti versi.
2. Tekan **Mulai Petualangan**, lalu izinkan kamera. Kamera depan didahulukan pada HP. Kamera bawaan/USB dapat dipilih dari daftar setelah izin diberikan.
3. Pada Android, pilihan awal adalah **Kompatibel (CPU)**. Tunggu sekitar 15 detik, lalu coba target latihan. Bandingkan dengan **Otomatis** jika masih lambat; pilih mode yang lebih lancar pada perangkat Anda.
4. Angkat telapak sampai kursor terlihat. Ayunkan ke kotak, tarik keluar, dan ulangi hingga **3 pukulan**. Pukul di udara, bukan pada layar. Tangan diam tidak menambah hitungan.
5. Tekan **Mulai Bermain • Layar Penuh**. Setiap jawaban kamera tetap memerlukan tiga pukulan. Klik/sentuh dan keyboard juga tersedia.
6. Gunakan **Cermin** bila arah tangan pada pratinjau tidak sesuai. Di HP tersedia pilihan **Depan / otomatis**, **Belakang**, dan kamera tertentu.

**Indikator sensor:** `deteksi/dtk` adalah frekuensi hasil sensor, `ms` adalah waktu pemrosesan rata-rata. Bandingkan kedua mode pada posisi dan pencahayaan yang sama. `Worker` menandakan pemrosesan terpisah; `Kompatibilitas` adalah jalur cadangan untuk browser yang belum mendukungnya.

Jika titik sering hilang, pastikan telapak utuh, cukup besar dalam gambar, dan terang. Jika gambar ada tetapi sensor berhenti, muat ulang sensor atau ganti mode. Setelah kembali ke menu/hasil, kamera ditutup; mulai latihan lagi atau tekan Hubungkan Kamera untuk mengaktifkannya.

Model dan pustaka sudah ada dalam paket. Membuka `index.html` langsung dengan `file://` tidak didukung versi ini; gunakan HTTPS atau localhost. HTTP melalui alamat IP jaringan biasa tidak menggantikan HTTPS untuk akses kamera. Jangan memindahkan HTML tanpa folder `aset/`.

Keyboard P1: Q/W/A/S atau 1/2/3/4. P2: I/O/K/L.

22 tes otomatis serta pengujian model asli dengan kamera simulasi telah lulus. Respons dan akurasi tangan pada Hisense/HP fisik masih perlu diuji. Lihat `AUDIT-PETUALANGAN-KATA.md` untuk hasil lengkap. Pengujian ulang logika: `node --test tests/*.test.cjs` jika Node.js tersedia.
