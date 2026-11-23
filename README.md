# Peta MBG Kepulauan Seribu

Peta interaktif dan data spasial Satuan Pelayanan Pemenuhan Gizi (SPPG) Program Makan Bergizi Gratis (MBG), pulau layanannya, dermaga, dan rute kapal ke daratan Jakarta di Kabupaten Administrasi Kepulauan Seribu, 2024–2026.

Pramono Pido, Departemen Geografi, Fakultas Matematika dan Ilmu Pengetahuan Alam, Universitas Indonesia.
Halaman statis (MapLibre GL JS), terbuka tanpa akun: https://prampido.github.io/peta-mbg-kepulauan-seribu/

DOI Zenodo (semua versi): akan diisi setelah rilis pertama.

Data ini disusun sebagai bahan esai mata kuliah Dinamika Spasial Sosial Ekonomi (SCGE801103), Departemen Geografi FMIPA UI.

## Isi repositori

| Folder / berkas | Isi |
|---|---|
| `index.html` | Peta interaktif (GitHub Pages) |
| `data/` | Data spasial GeoJSON (WGS 84, EPSG:4326) dan tabel CSV |
| `lib/` | MapLibre GL JS 4.7.1 (BSD-3-Clause) |
| `img/`, `qr/` | Logo FMIPA UI dan kode QR ke halaman peta |

## Data

| Berkas | Isi | Kolom utama |
|---|---|---|
| `data/pulau.geojson` | 110 pulau (poligon) | `pid`, `nama`, `kelurahan`, `kecamatan`, `kelas`, `kode_kelas`, `luas_ha`, `n_bangunan`, `luas_bangunan_m2`, `ntl_2023`, `ntl_2024`, `ntl_2025`, `lon_titik`, `lat_titik` |
| `data/pulau_statistik.csv` | Tabel atribut pulau yang sama, tanpa geometri | sama dengan di atas |
| `data/batas_admin.geojson` | Batas kabupaten, 2 kecamatan, 6 kelurahan (daratan pulau) | `osm_id`, `nama`, `tingkat`, `kecamatan`, `luas_km2` |
| `data/sppg.geojson`, `data/sppg.csv` | 7 SPPG (titik) | `nama`, `kelurahan`, `kecamatan`, `mulai`, `penerima`, `relawan`, `verifikasi`, `kode_verifikasi`, `sumber` |
| `data/dermaga.geojson` | 13 dermaga/pelabuhan (titik) | `nama`, `jenis`, `lokasi`, `sumber`, `daratan` |
| `data/rute_skematik.geojson` | 7 rute kapal (garis lurus antar-dermaga) | `rute`, `layanan`, `panjang_km` |
| `data/layanan_sppg.geojson` | 7 hubungan SPPG ke pulau layanan sekelurahan | `sppg`, `pulau_layanan`, `n_bangunan`, `jarak_km`, `status_radius`, `di_luar_6km` |
| `data/radius_6km.geojson` | Lingkaran 6 km dari tiap titik SPPG | `sppg`, `radius_km` |

Keterangan kolom:
- `kelas`: Pulau permukiman (≥50 bangunan), Bangunan terbatas (1–49), Fasilitas migas lepas pantai (Pulau Pabelokan), Tidak/hampir tidak berbangunan.
- `n_bangunan`, `luas_bangunan_m2`: jumlah dan luas jejak bangunan Google Open Buildings v3 dengan keyakinan ≥0,70 di dalam poligon pulau.
- `ntl_2023`–`ntl_2025`: rata-rata radiansi lampu malam VIIRS tahunan (nW/cm²/sr) di poligon pulau.
- `luas_ha`, `luas_km2`: luas dari geometri OpenStreetMap, bukan luas resmi.
- `kode_verifikasi`: `terverifikasi` (berita resmi Pemkab Kepulauan Seribu), `sebagian` (layanan MBG ada di situs Pemkab, unit dari direktori), `belum` (hanya direktori pihak ketiga).
- `jarak_km` di `layanan_sppg`: jarak dari titik SPPG ke tepi pulau layanan.

## Metode singkat

1. Pulau: 110 bagian poligon batas Kabupaten Kepulauan Seribu di OpenStreetMap (relasi 5802442), dipecah dengan `ST_DUMP` di BigQuery (`bigquery-public-data.geo_openstreetmap`).
2. Bangunan dan lampu malam: statistik zonal di Google Earth Engine dari `GOOGLE/Research/open-buildings/v3/polygons` dan `NOAA/VIIRS/DNB/ANNUAL_V22` (pita `average_masked`).
3. SPPG: lima lokasi dari berita resmi di situs Pemkab Kepulauan Seribu; Tidung dan Pari dari direktori pihak ketiga. Titik diletakkan di pusat pulau.
4. Dermaga dari OpenStreetMap; rute digambar sebagai garis lurus antar-dermaga.
5. Pulau layanan: pulau permukiman lain di kelurahan yang sama dengan SPPG. Radius 6 km mengacu pada juknis Badan Gizi Nasional.
6. Pengolahan di CARTO (BigQuery) dan Python; peta interaktif dengan MapLibre GL JS.

## Batasan

- Titik SPPG adalah pusat pulau (perkiraan), bukan alamat dapur.
- SPPG Tidung dan Pari belum terverifikasi di Radar MBG Badan Gizi Nasional.
- Rute bersifat skematik dan belum dicocokkan dengan jadwal kapal Dinas Perhubungan DKI Jakarta.
- Hubungan SPPG ke pulau layanan adalah asumsi satu kelurahan.
- Luas total pulau di OSM 11,27 km², lebih besar dari angka BPS 8,69 km² (Kabupaten Kepulauan Seribu Dalam Angka 2025). Untuk luas resmi, pakai BPS.
- Piksel VIIRS ±500 m terlalu kasar untuk pulau kecil. Gunakan sebagai indikasi tren, bukan ukuran ekonomi pulau.

## Sumber data

- Elvidge, C. D., Zhizhin, M., Ghosh, T., Hsu, F.-C., & Taneja, J. (2021). Annual time series of global VIIRS nighttime lights derived from monthly averages: 2012 to 2019. *Remote Sensing, 13*(5), Article 922. https://doi.org/10.3390/rs13050922
- Gorelick, N., Hancher, M., Dixon, M., Ilyushchenko, S., Thau, D., & Moore, R. (2017). Google Earth Engine: Planetary-scale geospatial analysis for everyone. *Remote Sensing of Environment, 202*, 18–27. https://doi.org/10.1016/j.rse.2017.06.031
- OpenStreetMap contributors. (2026). *OpenStreetMap* [Data set]. OpenStreetMap Foundation. https://www.openstreetmap.org
- Sirko, W., Kashubin, S., Ritter, M., Annkah, A., Bouchareb, Y. S. E., Dauphin, Y., Keysers, D., Neumann, M., Cisse, M., & Quinn, J. (2021). *Continental-scale building detection from high resolution satellite imagery* (arXiv:2107.12283). arXiv. https://doi.org/10.48550/arXiv.2107.12283
- Situs resmi Pemerintah Kabupaten Administrasi Kepulauan Seribu (pulauseribu.jakarta.go.id), berita 27 Mei 2025, 19 Agustus 2025, 15 September 2025, 22 Januari 2026, dan 11 Februari 2026. Tautan lengkap ada di kolom `sumber` dan di daftar pustaka esai.

## Cara mengutip

Pido, P. P. S. (2026). *Peta interaktif jaringan pasok dan SPPG Program Makan Bergizi Gratis di Kabupaten Administrasi Kepulauan Seribu* (Versi pram-v1.0.0) [Data set]. Zenodo. DOI akan diisi setelah rilis.

## Lisensi

- `data/`: Open Database License (ODbL) 1.0, karena diturunkan dari OpenStreetMap. Atribusi: © kontributor OpenStreetMap; Google Open Buildings v3 (CC BY 4.0); VIIRS VNL V2.2, Earth Observation Group, Payne Institute (CC BY 4.0).
- Teks dan `index.html`: CC BY 4.0.
- `lib/`: MapLibre GL JS, BSD-3-Clause (lihat `lib/LICENSE.txt`).

Kredit peta interaktif: peta dasar © kontributor OpenStreetMap (ubin standar openstreetmap.org); citra © Esri, Maxar, Earthstar Geographics.
