# Dashboard IRIS — Irigasi Rawa Kotawaringin Timur

Prototipe internal Dinas Pekerjaan Umum Kabupaten Kotawaringin Timur, Bidang Sumber Daya Air (SDA), untuk memantau jaringan irigasi rawa (Daerah Irigasi Rawa / DIR) secara digital.

## Struktur repo

```
/
├── index.html                   # Halaman dashboard (HTML + CSS + JS dalam satu file)
├── DIR_DASHBOARD_WGS84.geojson  # Geometri saluran irigasi (sumber tunggal, format WGS84)
└── README.md                    # Berkas ini
```

GitHub Pages disetel untuk melayani situs langsung dari branch `main`, folder root — jadi begitu dua berkas di atas berada di root repo, situs otomatis bisa diakses di `https://<username>.github.io/<nama-repo>/`.

## Arsitektur data

```
Google Sheets (database)
   │
   │  Apps Script (Code.gs, di-deploy sebagai Web App)
   ▼
JSON (kartu ringkasan, monitoring, analytics, aduan/usulan)
   │
   ▼
index.html  ──fetch──►  digabung di browser dengan
                          DIR_DASHBOARD_WGS84.geojson (geometri saluran)
```

- **Geometri saluran** (bentuk & panjang tiap segmen) selalu diambil dari `DIR_DASHBOARD_WGS84.geojson` — file ini tidak berubah kecuali ada pembaruan digitalisasi peta dari QGIS.
- **Data yang sering berubah** (kondisi saluran, progres kegiatan, sinkronisasi data, aduan warga) diambil live dari Google Sheets lewat Apps Script Web App.

## Status saat ini

`index.html` sudah bisa langsung ditampilkan di GitHub Pages walau Apps Script **belum** di-deploy — dashboard akan otomatis memakai data contoh (fallback) untuk bagian yang belum live, dan menampilkan label "(data contoh, Apps Script belum tersambung)" di header. Geometri peta & tabel DIR tetap 100% data asli karena diambil langsung dari GeoJSON.

## Cara menyambungkan Apps Script (sekali jalan)

1. Buka Google Sheets database (bukan file `.xlsx` hasil export — harus Sheets asli).
2. **Extensions → Apps Script**.
3. Hapus isi `Code.gs` bawaan, tempel isi `Code.gs` dari paket ini.
4. **Deploy → New deployment** → pilih tipe **Web app**.
   - Execute as: **Me**
   - Who has access: **Anyone**
5. Salin URL Web App yang muncul.
6. Buka `index.html`, cari baris:
   ```js
   const APPS_SCRIPT_URL = 'GANTI_DENGAN_URL_WEB_APP_SETELAH_DEPLOY';
   ```
   Ganti nilainya dengan URL dari langkah 5, lalu commit/push ulang `index.html`. Tidak ada bagian lain yang perlu diubah.
7. Setiap kali `Code.gs` diedit ulang di kemudian hari: **Deploy → Manage deployments → Edit (pensil) → New version → Deploy**, supaya URL tetap sama tapi kode terbaru yang aktif.

## Update data geometri saluran

Kalau ada revisi digitalisasi peta dari QGIS:
1. Export ulang sebagai GeoJSON, format WGS84 (EPSG:4326).
2. Ganti isi `DIR_DASHBOARD_WGS84.geojson` di repo (nama file harus tetap sama, karena `index.html` mengacu ke nama ini secara langsung).
3. Tidak perlu mengubah `index.html` maupun `Code.gs`.

## Privasi

Nama, kontak (WhatsApp/telepon), dan email pelapor pada Form Responses **sengaja tidak pernah dikirim** ke `index.html` — proses penyaringan dilakukan di `Code.gs` sebelum data keluar sebagai JSON publik.

## Data pilot

Dashboard ini menampilkan 6 dari 82 DIR terdaftar di Kotawaringin Timur, sebagai tahap awal (pilot project) sebelum digitalisasi diperluas ke seluruh DIR.
