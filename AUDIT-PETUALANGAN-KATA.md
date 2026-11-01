# Petualangan Kata — perbaikan kamera dan sensor tangan

1 November 2026. Sumber: `v1-petualangan-kata-main (1).zip`.

Perangkat tujuan berdasarkan keterangan dan gambar pengguna: **Hisense 75WM61FE, layar 75 inci, Android 13, RAM 16 GB, penyimpanan 256 GB**. Game dijalankan pada Android bawaan. Versi browser dan spesifikasi kamera belum diketahui. Informasi perangkat berasal dari lampiran pengguna, bukan pemeriksaan fisik panel.

## Hasil

Versi perbaikan mempertahankan mekanisme **tiga pukulan terpisah**, materi kelas 1–6, mode solo, kooperatif, duel, dan lomba bergiliran. Kamera HP sekarang mendahulukan kamera depan, dengan pilihan kamera belakang dan kamera tertentu dari daftar perangkat.

Pengolahan sensor dipisahkan dari animasi menggunakan Worker bila browser mendukungnya. Ini mengurangi gangguan pada antarmuka, tetapi tidak menjamin model selalu berjalan cepat pada setiap perangkat. Ukuran layar dan RAM saja tidak cukup untuk memprediksi kinerja: browser, pemrosesan CPU/GPU, kamera, pencahayaan, dan ukuran tangan dalam gambar juga berpengaruh.

## Temuan pada paket awal dan perbaikannya

| Temuan | Perbaikan |
|---|---|
| Permintaan kamera tidak menyertakan arah depan/belakang. | Kamera depan diminta terlebih dahulu. Jika batas resolusi ditolak, arah kamera tetap diprioritaskan; kamera USB tanpa informasi arah tetap mempunyai jalur cadangan. |
| `detectForVideo()` berjalan sinkron bersama loop permainan. | Worker memproses satu bingkai pada satu waktu; bingkai tidak ditumpuk dalam antrean. Browser tanpa dukungan yang diperlukan memakai jalur kompatibilitas. |
| Mode duel menaikkan jumlah tangan menjadi empat. | Satu tangan untuk solo/lomba, dua untuk duel/kooperatif. Gunakan satu tangan per pemain pada duel. |
| Resolusi kamera hanya berupa preferensi, sehingga kamera bisa memberi gambar lebih besar. | Masukan model benar-benar dibatasi hingga 640×480 dengan rasio gambar dipertahankan. |
| Penanda tangan digambar mengikuti seluruh ukuran arena, termasuk viewport 4K. | Lebar bitmap overlay dibatasi 1280 piksel. Koordinat target tetap menggunakan ukuran tampilan aktual agar posisi tidak bergeser. |
| Kecepatan pukulan dihitung berdasarkan waktu hasil diproses; latensi yang berubah dapat mengubah kecepatan terukur. | Kecepatan dihitung dari waktu pengambilan bingkai. Hasil kamera berulang tidak menghasilkan pukulan baru. |
| Riwayat posisi bisa menghubungkan dua pengamatan yang terlalu berjauhan waktunya. | Jeda antarsampel lebih dari 500 ms memulai riwayat baru, sehingga posisi baru tidak dianggap gerakan memukul dari posisi lama. Hasil berusia lebih dari 500 ms tidak dipakai. |
| Kamera dan model tidak mempunyai pembersihan terpadu saat kembali ke menu/hasil atau saat pemuatan dibatalkan. | Track kamera, Worker, dan model ditutup. Respons izin atau pemuatan yang terlambat tidak boleh menghidupkan kembali kamera lama ataupun menghentikan kamera pengganti. |
| Panduan lama masih menjelaskan pemuat `*-local.js`, tetapi README menyatakan berkas tersebut sengaja dihapus dan ZIP tidak memuatnya. | Pemuat yang merujuk berkas hilang dihapus. Panduan diselaraskan untuk HTTPS/localhost. Berkas WASM tanpa SIMD juga dilengkapi. Membuka HTML lewat `file://` menampilkan petunjuk hosting. |
| Panel petunjuk latihan bertumpuk dengan tombol bawah pada HP mendatar. | Area latihan disesuaikan; petunjuk dapat digulir di dalam panel, dan target serta tombol mulai tetap terlihat. |

Mode **Kompatibel (CPU)** tetap menjadi pilihan awal pada browser Android seperti versi sebelumnya. **Otomatis** mencoba GPU lalu CPU jika pembuatan model GPU gagal. Jika GPU berhasil dimuat tetapi lambat atau bermasalah saat berjalan, pengguna dapat memilih CPU dan memuat ulang. Versi ini tidak secara otomatis memilih mesin tercepat berdasarkan benchmark.

Pemetaan tangan tetap mengikuti video `object-fit:contain`, termasuk ruang kosong di kiri/kanan. Tombol **Cermin** menyelaraskan arah gerak; kamera belakang tidak dicerminkan secara default. Target yang telah terkena pukulan tetap berhenti seperti mekanisme permainan awal.

## Hasil pengujian

**22 tes otomatis lulus**. Cakupan: pemilihan kamera depan/belakang, kamera USB, penolakan izin, pembatalan pemuatan, penghentian kamera, batas resolusi, pemetaan video/cermin, bingkai kedaluwarsa, satu bingkai aktif, tiga ayunan, tangan diam, jitter kecil, bingkai berulang, jeda panjang, latensi berubah, tangan hilang, dorong-tarik, lintasan cepat, dan dua identitas tangan. Dua pemeriksaan tambahan memastikan identitas dan riwayat gerakan tidak hilang hanya karena kursor sempat kedaluwarsa di antara hasil sensor yang lambat; hasil kamera yang benar-benar tidak mendeteksi tangan tetap menghapus riwayat.

Pengujian integrasi Chrome desktop menggunakan **kamera simulasi** dan **model MediaPipe asli**:

- Model dan inferensi berhasil berjalan pada CPU/Worker, GPU/Worker, dan CPU/Kompatibilitas.
- Akses jaringan eksternal diblokir selama uji: model dan pustaka berhasil dimuat dari paket lokal yang disajikan melalui localhost.
- Input telapak sintetis melalui alur permainan menghasilkan satu pukulan, dua pukulan, lalu menjawab hanya pada pukulan ketiga.
- Pergantian kamera, penghentian track lama, tombol cermin, keyboard pemain kedua, jeda, hasil, dan kembali ke menu berhasil diuji.
- Saat model sengaja dibuat tidak tersedia, pemuatan berhenti dengan status kegagalan, kamera ditutup, tombol coba ulang aktif, dan keyboard tetap berfungsi.
- Saat izin kamera sengaja ditunda, kembali ke menu lalu memulai lagi tidak menghidupkan kamera lama. Permintaan baru tetap dapat diselesaikan.
- Tata letak latihan diperiksa pada 1280×720, 3840×2160, dan 844×390. Tangkapan layar desktop dan HP mendatar diperiksa secara visual. Pada viewport 4K, bitmap overlay tetap 1280 piksel lebar.

Pengujian ini memverifikasi fungsi kode dan eksekusi model, **bukan akurasi pelacakan tangan fisik atau benchmark Hisense**. IFP Hisense dan HP fisik belum diuji. Model membutuhkan gambar tangan yang cukup jelas; Worker tidak menambah detail pada kamera yang buram atau tangan yang terlalu kecil di gambar.

Untuk mengulang tes logika dari folder aplikasi dengan Node.js: `node --test tests/*.test.cjs`.

## Memasang dan mencoba pada IFP

1. Ekstrak paket dan unggah **isi folder aplikasi secara lengkap** ke hosting HTTPS. `index.html` berada di direktori utama; folder `aset/` harus ikut. Jangan hanya menyalin HTML atau membuka langsung dari ZIP.
2. Muat ulang halaman setelah mengganti berkas. Masuk latihan, izinkan kamera di browser dan izin aplikasi Android jika diminta, lalu pilih kamera bawaan/USB yang benar dari daftar.
3. Mulai dengan **Kompatibel (CPU)**. Tunggu sekitar 15 detik untuk pemanasan, lalu uji telapak selama 30 detik. Catat angka `deteksi/dtk` dan `ms`.
4. Ulangi pada **Otomatis** dengan kamera, posisi, dan pencahayaan sama. Gunakan yang membuat titik lebih lancar dan hitungan pukulan lebih konsisten pada panel tersebut. CPU tidak selalu lebih cepat daripada GPU.
5. Tampilkan seluruh telapak. Ayunkan ke target, tarik keluar, lalu ulangi sampai hitungan 1–2–3. Lakukan gerakan di udara. Tangan diam tidak seharusnya menambah hitungan.
6. Uji sepuluh rangkaian pukulan pada jarak yang biasa digunakan. Jika tangan sering hilang, coba lebih dekat atau perbaiki pencahayaan. Tidak ada jarak pasti yang dapat dijamin tanpa mengetahui kamera dan bidang pandangnya.
7. Di HP, pilih **Depan / otomatis**. Pastikan pratinjau memperlihatkan pemain. Uji kamera belakang dan kembali ke depan; gunakan pilihan perangkat tertentu bila arah kamera tidak dilaporkan dengan benar oleh browser.

`deteksi/dtk` menunjukkan frekuensi hasil sensor, bukan FPS kamera. `ms` menunjukkan waktu pemrosesan model rata-rata. `Worker` berarti pemrosesan terpisah; `Kompatibilitas` berarti jalur cadangan yang masih dapat membebani animasi. Untuk dukungan lanjutan, catat versi browser, nama kamera, jarak, dan indikator dari kedua mode.

Paket menyimpan model lokal sehingga CDN tidak diperlukan selama seluruh berkas tersedia. Jika sumber lokal gagal, pemuat masih mencoba sumber resmi daring sebagai cadangan. Ini bukan aplikasi offline terpasang; halaman dan berkas harus dapat diakses. Tidak ada hosting yang dipublikasikan atau diubah dalam pekerjaan ini.

## Rujukan

- [MediaPipe Hand Landmarker untuk Web](https://developers.google.com/edge/mediapipe/solutions/vision/hand_landmarker/web_js): deteksi sinkron dan penggunaan Worker.
- [MDN facingMode](https://developer.mozilla.org/en-US/docs/Web/API/MediaTrackConstraints/facingMode): pemilihan arah kamera.
- [MDN getUserMedia](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia): izin, batasan kamera, dan penghentian track.

MediaPipe Tasks Vision tetap versi 0.10.14; model `hand_landmarker` float16 versi 1. Sumber dan lisensi tersedia dalam folder `aset/sensor/`.
