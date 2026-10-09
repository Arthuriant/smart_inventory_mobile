# Rencana App Mobile Teknisi

Status: langkah 1 selesai (2026-10-09). Ubah dokumen ini kalau user memutuskan lain.

## Yang sudah ada
- App.Formulas: nfMe, nfIsAdmin, nfMyBl, nfMyAreas, nfMyAreaIds, nfLowStock, `nfHasAccess` (terdaftar DAN punya BL),
  palet clr*. StartScreen `=If(nfHasAccess, scr_m_consume, scr_m_noaccess)`.
- Komponen `cmpHeader` (input `Title`; baris kedua = nama user · BL) dan `cmpTabBar` (input `ActiveTab`: "Consume" /
  "Find" / "History" / "Dashboard"; 4 ModernButton, Navigate ke screen tab).
- Tiap screen tab: `con<P>Root` (AutoLayout vertikal) -> `cmp<P>Header` (88) - `con<P>Body` (FillPortions 1, isi
  screen ditaruh DI SINI) - `cmp<P>TabBar` (72). Prefix: Cons, Find, Hist, Dash. Body masih berisi teks placeholder
  `txt<P>Soon` (hapus saat screen dibangun).
- `scr_m_noaccess` (prefix Na): pesan beda untuk belum terdaftar / belum punya BL, daftar admin + tombol email, tombol
  "Check again" (Refresh dis_users, lalu ke Consume kalau akses sudah ada).
- Screen1 bawaan masih ada (tidak terpakai): hapus manual di Studio.

## Tujuan
Teknisi di lapangan bisa: mengambil (consume) barang dari stok area BL-nya, mencari barang, melihat riwayat, dan
melihat ringkasan stok. Fitur admin (Category, Item, Geounit/Location, Area, User, form Transaction Receive/Transfer)
TIDAK dibawa.

## Layout umum
- Form factor Phone, portrait (640 x 1136 desain). Satu kolom, tombol/area sentuh minimal 44 px tinggi.
- Tiap screen: header atas (judul + nama user/BL kecil) - konten scroll - **bottom tab bar** 4 tab:
  Consume | Find | History | Dashboard. Tab bar dibuat sebagai komponen (mis. `cmpTabBar`, input `ActiveTab`) atau
  container yang sama di tiap screen; jangan pakai Sidebar desktop.
- Palet sama dengan desktop (navy `RGBA(0, 18, 107, 1)`, accent `RGBA(56, 96, 178, 1)`, bg `RGBA(244, 246, 250, 1)`),
  ditulis literal.

## Screen
| # | Screen | Isi | Referensi desktop |
| --- | --- | --- | --- |
| 0 | `scr_m_noaccess` | Muncul kalau user belum terdaftar di dis_users ATAU belum punya Sub Business Line: pesan + daftar admin (tombol email). | Register.pa.yaml |
| 1 | `scr_m_consume` (StartScreen bila punya akses) | Search + filter Category & Area (opsional, `Sort(nfMyAreas, Name)`). Daftar kartu 1 kolom: foto kecil, nama, BPN, badge stok, area, stepper qty (- / angka / +). Bar bawah tetap: "n item · m pcs" + tombol Consume. Logika simpan = kontrak di CLAUDE.md. | scr_consume.pa.yaml |
| 2 | `scr_m_find` | Search + filter Category & Area. Daftar kartu (nama, BPN, stok, area, status menipis). Tap kartu -> detail (overlay atau screen `scr_m_find_detail`): foto, deskripsi, data item, stok per area. | scr_find_item.pa.yaml |
| 3 | `scr_m_history` | Daftar transaksi area BL user (terbaru di atas), filter tipe & rentang tanggal sederhana. Tap -> detail item transaksi. Hanya baca. | scr_History.pa.yaml |
| 4 | `scr_m_dashboard` | KPI ringkas (total item, stok menipis, transaksi hari ini), daftar stok menipis, 5 transaksi terakhir. Semua dibatasi area BL user. | scr_dashboard.pa.yaml |

StartScreen: `If(nfHasAccess, scr_m_consume, scr_m_noaccess)`.

## Urutan kerja (satu screen per sesi)
1. ~~App.pa.yaml (Formulas RBAC + palet, StartScreen) + komponen tab bar + `scr_m_noaccess`~~ (selesai)
2. `scr_m_consume`
3. `scr_m_find`
4. `scr_m_history`
5. `scr_m_dashboard`
6. Uji di HP (aplikasi Power Apps mobile), publish.

## Pertanyaan terbuka (tanya user saat mulai)
- Consume perlu konfirmasi sebelum simpan (dialog "Consume n item?")? Saran: ya.
- Dashboard untuk teknisi: KPI apa yang paling berguna?
- Perlu scan barcode (BarcodeReader) untuk mencari item? Bisa ditambah nanti.
