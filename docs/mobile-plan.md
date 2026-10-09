# Rencana App Mobile Teknisi

Status: Consume, History, Dashboard selesai (2026-10-09); tinggal uji di HP + publish. Find DIBATALKAN (user: cukup lewat Consume). Ubah dokumen ini kalau user memutuskan lain.

## Yang sudah ada
- App.Formulas: nfMe, nfIsAdmin, nfMyBl, nfMyAreas, nfMyAreaIds, nfLowStock, `nfHasAccess` (terdaftar DAN punya BL),
  palet clr*. StartScreen `=If(nfHasAccess, scr_m_consume, scr_m_noaccess)`.
- Komponen `cmpHeader` (input `Title`; baris kedua = nama user · BL) dan `cmpTabBar` (input `ActiveTab`: "Consume" /
  "History" / "Dashboard"). Gaya modern: bar putih sudut atas bulat + bayangan; per tab ikon di atas pill
  (`conTb<k>Ind`, biru muda saat aktif, tombol `btnTb<k>Ico`) dan label (`btnTb<k>`); keduanya Navigate.
- Tiap screen tab: `con<P>Root` (AutoLayout vertikal) -> `cmp<P>Header` (88) - `con<P>Body` (FillPortions 1, isi
  screen ditaruh DI SINI) - `cmp<P>TabBar` (72). Prefix: Cons, Hist, Dash. Body masih berisi teks placeholder
  `txt<P>Soon` (hapus saat screen dibangun).
- `scr_m_noaccess` (prefix Na): pesan beda untuk belum terdaftar / belum punya BL, daftar admin + tombol email, tombol
  "Check again" (Refresh dis_users, lalu ke Consume kalau akses sudah ada).
- `scr_m_history` (prefix Hist): search nomor transaksi + refresh, chip tipe (`locHistType`) dan periode (`locHistDays`,
  default 30 hari), daftar kartu `galHist` (nomor, tanggal, badge tipe, rute asal -> tujuan, "By" pembuat). Query:
  Filter/Sort delegable di dalam (periode, tipe, nomor), filter area BL (`area_form`/`area_to in nfMyAreaIds`) di luar.
  Tap kartu -> lembar detail `conHistDlg` (`locHistSel`, `locHistOpen`) berisi item (foto, nama, BPN, qty bertanda).
- `scr_m_dashboard` (prefix Dash) - HANYA transaksi (user: teknisi tidak perlu stok): tanggal + refresh, chip
  "My transactions" (default, 'Created By' = `nfMySysUserId`) / "All in my BL" (`locDashAll`), KPI Today / 7 / 30 hari
  dan Consume / Receive / Transfer (30 hari), daftar 10 transaksi terakhir (See all -> History). Query: If(locDashAll,
  Filter(..), Filter(.., 'Created By'.User = ..)) di server (If DI LUAR Filter agar delegable), area BL di lokal.
- `scr_m_consume` (gaya e-commerce): search pill + reset/refresh, chip kategori (`btnConsCatAll` + galeri horizontal
  `galConsCat`, state `locConsCat`), dropdown Area, grid 2 kolom `galCons` (latar kartu `conConsCard` ManualLayout tanpa
  anak, foto, badge stok di atas foto, nama, BPN, area, tombol Add -> stepper - / angka / +; badge = status stok, `txtConsLeft` = "Stock left n uom -> sisa setelah consume"). Pilihan qty di koleksi
  `colConsSel {stockId, amt}`. Bar keranjang mengambang `conConsCart` (muncul bila ada pilihan) -> lembar konfirmasi
  `conConsDlg` (`locConsConfirm`); `btnConsDlgOk` = logika kontrak (stok dibaca terbaru).

## Tujuan
Teknisi di lapangan bisa: mengambil (consume) barang dari stok area BL-nya, mencari barang, melihat riwayat, dan
melihat ringkasan stok. Fitur admin (Category, Item, Geounit/Location, Area, User, form Transaction Receive/Transfer)
TIDAK dibawa.

## Layout umum
- Form factor Phone, portrait (640 x 1136 desain). Satu kolom, tombol/area sentuh minimal 44 px tinggi.
- Tiap screen: header atas (judul + nama user/BL kecil) - konten scroll - **bottom tab bar** 4 tab:
  Consume | History | Dashboard. Tab bar dibuat sebagai komponen (mis. `cmpTabBar`, input `ActiveTab`) atau
  container yang sama di tiap screen; jangan pakai Sidebar desktop.
- Palet sama dengan desktop (navy `RGBA(0, 18, 107, 1)`, accent `RGBA(56, 96, 178, 1)`, bg `RGBA(244, 246, 250, 1)`),
  ditulis literal.

## Screen
| # | Screen | Isi | Referensi desktop |
| --- | --- | --- | --- |
| 0 | `scr_m_noaccess` | Muncul kalau user belum terdaftar di dis_users ATAU belum punya Sub Business Line: pesan + daftar admin (tombol email). | Register.pa.yaml |
| 1 | `scr_m_consume` (StartScreen bila punya akses) | Search + filter Category & Area (opsional, `Sort(nfMyAreas, Name)`). Daftar kartu 1 kolom: foto kecil, nama, BPN, badge stok, area, stepper qty (- / angka / +). Bar bawah tetap: "n item · m pcs" + tombol Consume. Logika simpan = kontrak di CLAUDE.md. | scr_consume.pa.yaml |
| ~~2~~ | ~~`scr_m_find`~~ (batal) | Search + filter Category & Area. Daftar kartu (nama, BPN, stok, area, status menipis). Tap kartu -> detail (overlay atau screen `scr_m_find_detail`): foto, deskripsi, data item, stok per area. | scr_find_item.pa.yaml |
| 3 | `scr_m_history` | Daftar transaksi area BL user (terbaru di atas), filter tipe & rentang tanggal sederhana. Tap -> detail item transaksi. Hanya baca. | scr_History.pa.yaml |
| 4 | `scr_m_dashboard` | KPI ringkas (total item, stok menipis, transaksi hari ini), daftar stok menipis, 5 transaksi terakhir. Semua dibatasi area BL user. | scr_dashboard.pa.yaml |

StartScreen: `If(nfHasAccess, scr_m_consume, scr_m_noaccess)`.

## Urutan kerja (satu screen per sesi)
1. ~~App.pa.yaml (Formulas RBAC + palet, StartScreen) + komponen tab bar + `scr_m_noaccess`~~ (selesai)
2. ~~`scr_m_consume`~~ (selesai)
3. ~~`scr_m_find`~~ (batal)
4. ~~`scr_m_history`~~ (selesai)
5. ~~`scr_m_dashboard`~~ (selesai)
6. Uji di HP (aplikasi Power Apps mobile), publish.

## Pertanyaan terbuka (tanya user saat mulai)
- ~~Konfirmasi sebelum Consume~~: dibuat (lembar konfirmasi); hapus kalau user tidak mau.
- ~~KPI dashboard~~: dipakai usulan (user setuju).
- Perlu scan barcode (BarcodeReader) untuk mencari item? Bisa ditambah nanti.
