# Arus Publik

Pelacak transparansi anggaran Indonesia. Frontend React + Vite; database PostgreSQL melalui Supabase; deployment melalui Vercel.

## Development

```bash
npm install
npm run dev
npm run build
```

## Konfigurasi frontend

Atur environment variables berikut pada Vercel:

- `VITE_SUPABASE_URL`
- `VITE_SUPABASE_PUBLISHABLE_KEY`

Publishable key memang digunakan di browser; keamanan write access harus berasal dari Row Level Security (RLS), bukan dengan menyembunyikan key. Jangan pernah menaruh Supabase service-role key di frontend.

## Model data

- `regions`: hierarki wilayah dari negara sampai kecamatan.
- `government_entities`: kementerian, lembaga, pemda, dan entitas publik.
- `budget_accounts`: pohon akun APBN/APBD; kode akun internal ditandai di metadata dan tidak boleh dianggap sebagai kode rekening resmi.
- `data_sources` dan `source_documents`: asal data, URL dokumen, periode, status verifikasi dan publikasi.
- `budget_observations`: nilai anggaran/realisasi dengan mata uang, satuan, periode, dan status verifikasi.
- `raw_source_records` dan `ingestion_runs`: jejak impor dan payload sumber.
- `fiscal_flows`: relasi transfer antarinstansi/wilayah untuk mencegah penghitungan ganda.

Semua tabel mengaktifkan RLS. Akses publik hanya membaca data yang diizinkan; data mentah dan log impor tidak diberi policy baca publik.

## Snapshot data saat ini

- **APBN 2024:** ringkasan LKPP audited, 38 akun dan 66 observasi anggaran/realisasi.
- **APBD nasional 2024:** ringkasan SIKD DJPK, 39 akun dan 78 observasi anggaran/realisasi.
- **Wilayah:** 38 provinsi, 514 kabupaten/kota, dan 7.285 kecamatan.
- **Jejak sumber:** raw source records disimpan untuk angka anggaran dan impor wilayah.

APBN disimpan dalam triliun rupiah; snapshot APBD SIKD menggunakan satuan portal “M” (miliar rupiah). Jangan menjumlahkan akun induk dengan rincian anak karena keduanya dapat tumpang tindih. Transfer pusat-daerah juga perlu dicocokkan sebagai aliran dana, bukan dianggap dua pengeluaran akhir.

## Batasan yang diketahui

- Data APBN/APBD yang ditampilkan adalah snapshot tahun 2024, bukan feed otomatis.
- Snapshot APBD nasional merupakan ringkasan nasional, bukan rincian seluruh pemda atau transaksi.
- Daftar kabupaten/kota dan kecamatan diimpor dari dataset komunitas dengan kode bergaya BPS sebagai **staging**. Dokumennya berstatus `under_review`, `unverified`, dan tidak dipublikasikan; harus dicocokkan dengan keputusan wilayah administrasi Kemendagri terbaru.
- Data desa/kelurahan, APBD per pemda, sinkronisasi otomatis, dan laporan BUMN belum diimpor.

## Sumber utama

- [DJPK — SIKD](https://djpk.kemenkeu.go.id/portal/)
- [LKPP 2024 audited — DJPb](https://djpb.kemenkeu.go.id/portal/images/LKPP/LKPP-2024.pdf)
- [Kode wilayah Kemendagri](https://ditjenbinaadwil.kemendagri.go.id/berita/index/352)
- [Dataset wilayah komunitas untuk staging](https://github.com/izzulabadi/api-wilayah-indonesia-2026)
