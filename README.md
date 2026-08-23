# Website Zona Kambuhan Kebakaran — Pulang Pisau

Sekarang bentuknya website satu halaman (scroll panjang), bukan cuma dashboard peta:
Beranda (cover) → Pendahuluan → Fakta Kunci → Peta Interaktif → Unduh Peta → Statistik → Mitigasi.

Nuansa: gelap, hitam-merah-oranye, terinspirasi warna api/bara — sengaja dibuat "mencekam" untuk menekankan urgensi isu.

## Yang perlu kamu taruh sendiri

| File | Taruh di | Wajib? |
|---|---|---|
| `cover.webp` (foto/ilustrasi karhutla untuk cover) | `assets/cover.webp` | Ya — tanpa ini, bagian Beranda cuma tampil warna hitam polos (tetap jalan, cuma kurang menarik) |
| `PulangPisau_Boundary.geojson` | `data/` | Ya |
| `PulangPisau_RecurrenceZone_Vector.geojson` | `data/` | Ya |
| `batas_desa.geojson` (batas administrasi desa) | `data/` | Opsional — kalau belum ada, toggle "Batas Desa" di peta cuma nggak akan nampilin apa-apa, tidak error |
| File `.tif` VERSI WEB (`..._web.tif`, resolusi 100m, ringan) | `data/raster/` | Wajib untuk website — `PulangPisau_FireFrequency_2019_2025_web.tif` + ke-7 file `PulangPisau_NBR_<tahun>_web.tif`. Ini yang di-upload ke GitHub / dimuat browser (lihat "Soal ukuran file" di bawah) |
| File `.tif` VERSI ASLI (resolusi 20m, besar) | `data/raster/` (atau simpan terpisah) | Opsional — cuma arsip untuk QGIS/analisis detail, TIDAK dipakai website, sebaiknya JANGAN diupload ke GitHub karena ukurannya besar |
| File layout peta siap cetak (PDF/PNG/JPG) | `data/peta/` | Opsional — lihat bagian "Unduh Peta" di bawah |

## Menambahkan peta layout ke bagian "Unduh Peta"

Karena nama file layoutnya belum ditentukan, bagian ini defaultnya kosong (menampilkan pesan "belum ditambahkan"). Begitu kamu sudah punya file layoutnya:

1. Taruh file-nya (PDF/PNG/JPG) di `data/peta/`.
2. Buka `index.html`, cari variabel `PETA_LAYOUTS` di bagian `<script>` (ada komentar CONTOH di situ).
3. Tambahkan satu baris per peta, formatnya:
   ```js
   { title: 'Nama Peta', desc: 'Deskripsi singkat.', filename: 'nama_file_kamu.pdf' },
   ```
4. Simpan, refresh browser — kartu unduhannya otomatis muncul.

## Soal ukuran file (.tif)
Script GEE sekarang menghasilkan **2 versi** tiap raster (NBR tahunan & frekuensi kebakaran):
- **Resolusi 20m** (nama file biasa) — detail tinggi, ukuran besar, buat arsip/QGIS.
- **Resolusi 100m** (nama file berakhiran `_web`) — ukurannya jauh lebih kecil (bedanya nyaris tidak kelihatan di skala peta kabupaten), ini yang dipakai `index.html` dan yang sebaiknya kamu upload ke GitHub.

Kalau upload ke GitHub lewat `git push` (bukan drag-drop di web, yang limitnya cuma 25MB), batas per file sekitar 100MB — versi `_web` harusnya jauh di bawah itu.

## Cara menjalankan

Buka `index.html` di browser. Kalau data tidak muncul sama sekali (bukan cuma pesan kuning "belum ketemu data"), jalankan local server dari folder ini:
```
python -m http.server 8000
```
lalu buka `http://localhost:8000`. (Browser modern memblokir `fetch()` file lokal langsung dari `file://`.)

## Struktur folder
```
webgis/
├── index.html
├── README.md
├── assets/
│   └── cover.webp                              <- taruh sendiri
└── data/
    ├── PulangPisau_Boundary.geojson
    ├── PulangPisau_RecurrenceZone_Vector.geojson
    ├── batas_desa.geojson                       <- opsional
    ├── raster/
    │   └── PulangPisau_FireFrequency_2019_2025.tif
    └── peta/
        └── (file layout peta siap cetak, nama menyusul)
```

## Ringkasan fitur per bagian
- **Beranda** — cover full-screen, judul besar, badge angka kunci (hotspot, periode, sumber data)
- **Pendahuluan** — narasi urgensi + siklus degradasi gambut (bisa dipakai ulang buat naskah presentasi)
- **Fakta Kunci** — 4 angka statistik nasional/regional sebagai konteks
- **Peta Interaktif** — 5 layer (Batas Kabupaten, Batas Desa, Zona Kambuhan, Frekuensi Kebakaran, NBR per Tahun) dengan legenda dan popup per poligon
- **NBR per Tahun** — slider 2019&ndash;2025, geser buat lihat perubahan kondisi vegetasi/gambut per tahun (perlu ke-7 file `PulangPisau_NBR_<tahun>.tif` di `data/raster/`, nama harus persis sesuai hasil export GEE). Otomatis mematikan layer Frekuensi supaya tidak menumpuk 2 raster berat sekaligus.
- **Unduh Peta** — kartu unduhan untuk layout peta siap cetak (data-driven, tinggal isi)
- **Statistik** — luas per kategori zona kambuhan (bar chart, dihitung otomatis dari GeoJSON)
- **Mitigasi** — rekomendasi aksi untuk tiga pihak: masyarakat, pemda/BRGM, dinas kesehatan
