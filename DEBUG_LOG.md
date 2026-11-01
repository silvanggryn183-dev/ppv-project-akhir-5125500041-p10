DEBUG LOG - KATALOG KARYA P10

1. BUG G1: LOGO NAVBAR TIDAK TAMPIL
Langkah Reproduksi: Membuka "index.html" di browser, lalu melihat navbar. Logo muncul sebagai ikon gambar rusak.
Hipotesis:
- File "logo.svg" rusak. GUGUR, karena saat dibuka langsung lewat "assets/logo.svg" gambarnya tampil normal.
- Path "src" pada tag img salah. TERBUKTI.
Bukti DevTools: Tab Network menunjukkan "logo.svg" berstatus "404 (Not Found)". Request menuju folder "img/", padahal file ada di folder "assets/".
Perbaikan (index.html:13): Mengubah src="img/logo.svg" menjadi src="assets/logo.svg".
Uji Ulang: Logo tampil dan status "logo.svg" di Network menjadi "200".
Hash Commit: 833a5b4

2. BUG G2: KARTU TERSUSUN SATU KOLOM DI LAYAR LEBAR
Langkah Reproduksi: Membuka halaman di lebar layar lebih dari 992 px. Kartu memanjang satu kolom ke bawah.
Hipotesis:
- CSS Bootstrap gagal dimuat. GUGUR, karena navbar, input, dan tombol tampil dengan gaya Bootstrap, dan "bootstrap.min.css" berstatus "200".
- Wadah kartu tidak punya class "row". TERBUKTI.
Bukti DevTools: Tab Elements menunjukkan div "galeri" hanya punya class "g-3", tanpa class "row" dan tanpa label flex. Tab Computed menunjukkan "display: block".
Perbaikan (index.html:33): Mengubah class="g-3" menjadi class="row g-3" pada div "galeri".
Uji Ulang: Di lebar 993 px kartu tampil 3 kolom, dan "display" galeri berubah menjadi "flex".
Hash Commit: 75080da

3. BUG G3: FILTER KATEGORI TIDAK BERFUNGSI
Langkah Reproduksi: Memilih "Video" pada filter kategori. Daftar kartu tidak berubah dan Console menampilkan error merah.
Hipotesis:
- Nilai kategori di "data.js" berbeda dengan "value" pada option. GUGUR, karena tulisannya sama persis ("Animasi", "Video", "Desain").
- Selector di "main.js" salah. TERBUKTI.
Bukti DevTools: Tab Console menampilkan "Uncaught TypeError: Cannot read properties of null (reading 'addEventListener')" di "main.js:54". Saat dites, document.querySelector("#filter-kategori") menghasilkan "null", sedangkan document.querySelector("#filterKategori") menghasilkan elemen select.
Perbaikan (main.js:54): Mengganti document.querySelector("#filter-kategori").addEventListener(...) dengan filterKategori.addEventListener("change", tampil). Variabel "filterKategori" sudah dibuat di bagian atas file.
Uji Ulang: Error di Console hilang. Memilih "Video" menampilkan 2 kartu, memilih "Semua kategori" kembali ke 6 kartu.
Hash Commit: ca44e9e

4. BUG G4: TEKS "MENAMPILKAN ... KARYA" TIDAK BERUBAH
Langkah Reproduksi: Mengetik "Video" di kotak cari. Kartu tinggal 2, tetapi teks tetap "Menampilkan 6 dari 6 karya".
Hipotesis:
- Fungsi "saring()" mengembalikan hasil yang salah. GUGUR, karena kartu yang tampil benar dan "hasil.length" bernilai 2.
- Teks jumlah memakai "KARYA.length" dua kali. TERBUKTI.
Bukti DevTools: Memasang breakpoint di "main.js:38" (tab Sources), lalu mengetik "Video". Di Console, "hasil.length" bernilai 2 dan "KARYA.length" bernilai 6.
Perbaikan (main.js:38): Mengubah angka pertama dari "KARYA.length" menjadi "hasil.length", sehingga menjadi "Menampilkan ${hasil.length} dari ${KARYA.length} karya".
Uji Ulang: Mengetik "Video" menampilkan teks "Menampilkan 2 dari 6 karya".
Hash Commit: d09cd8a

5. BUG G5: TOMBOL DETAIL MATI SETELAH MENCARI
Langkah Reproduksi: Klik Detail saat halaman baru dibuka, kotak informasi muncul. Lalu mengetik "Animasi" dan klik Detail, kotak informasi tidak muncul.
Hipotesis:
- Klik Detail menyebabkan error di handler. GUGUR, karena Console tidak menampilkan error baru saat tombol diklik.
- Tombol hasil render ulang tidak punya event listener. TERBUKTI.
Bukti DevTools: Tab Elements, lalu Event Listeners pada tombol Detail. Sebelum mencari, bagian "click" terisi dari "main.js". Setelah mencari, bagian "click" kosong.
Penyebab: Fungsi "tampil()" memanggil "replaceChildren" sehingga semua tombol dibuat ulang, sedangkan "pasangDetail()" hanya dipanggil satu kali di akhir file.
Perbaikan: Menambahkan "pasangDetail();" di dalam fungsi "tampil()" (main.js:39), dan menghapus pemanggilan "pasangDetail();" yang berdiri sendiri di bagian bawah file (main.js:52) agar listener tidak terpasang dobel.
Uji Ulang: Setelah mengetik "Animasi" dan klik Detail, kotak informasi muncul sesuai karya yang diklik. Setelah ganti filter, tombol Detail juga tetap berfungsi.
Hash Commit: ff94356

6. BUG G6: PENCARIAN HURUF KECIL TIDAK MENEMUKAN KARYA
Langkah Reproduksi: Mengetik "animasi" (huruf kecil) di kotak cari. Hasilnya "Menampilkan 0 dari 6 karya".
Hipotesis:
- "trim()" memotong kata yang diketik. GUGUR, karena saat diketik "Animasi" (A besar) karya tetap ditemukan.
- "includes()" membedakan huruf besar dan kecil (case-sensitive). TERBUKTI.
Bukti DevTools: Di Console, "Animasi Logo Himpunan".includes("animasi") menghasilkan "false", sedangkan "Animasi Logo Himpunan".toLowerCase().includes("animasi") menghasilkan "true".
Perbaikan (main.js:14): Mengubah k.judul.includes(kata) menjadi k.judul.toLowerCase().includes(kata.toLowerCase()).
Uji Ulang: Mengetik "animasi" menampilkan 2 kartu. Mengetik "VIDEO" dan "Animasi" juga ditemukan.
Hash Commit: d700b32

