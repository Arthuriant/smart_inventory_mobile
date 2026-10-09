# SLB Digital Inventory - Mobile (Teknisi)

Versi mobile (Phone) dari app desktop SLB Digital Inventory, khusus teknisi. User menulis dalam bahasa Indonesia:
jawab dalam bahasa Indonesia. Teks UI di app tetap bahasa Inggris (sama dengan desktop).

## Identitas
- Power Apps environment: `c2194d0b-89b3-ed3b-b65a-1db58a779659`
- App mobile: `fade1324-de08-4ffb-88a8-46aebc0dad43` (canvas, form factor Phone, coauthoring aktif)
- Login MCP `connect`: `login_hint` = `ARianto2@slb.com`
- URL Studio (mode edit):
  https://make.powerapps.com/e/c2194d0b-89b3-ed3b-b65a-1db58a779659/canvas?action=edit&form-factor=phone&app-id=%2Fproviders%2FMicrosoft.PowerApps%2Fapps%2Ffade1324-de08-4ffb-88a8-46aebc0dad43
- Repo: https://github.com/Arthuriant/smart_inventory_mobile (branch main, private)
- Folder ini: `app/` = HANYA file `.pa.yaml` (hasil sync/compile). Dokumen di root dan `docs/`.
- Rencana layar: `docs/mobile-plan.md`. Alur kerja harian + Log: `ALUR-KERJA.md`.

## Referensi desktop (HANYA BACA)
App desktop ada di `C:\Project\Powerapps` (app `87f60c80-e3c1-45bf-ba27-93eb0c079759`, repo
github.com/Arthuriant/smart_inventory). **Jangan pernah mengedit, commit, atau compile apa pun di sana**, dan jangan
`connect` ke app desktop dari sesi mobile. Boleh dibaca sebagai contoh:
- `app/App.pa.yaml` - App.Formulas (RBAC, palet warna, fxMenuItems)
- `app/scr_consume.pa.yaml` - logika Consume (btnConsSubmit)
- `app/scr_find_item.pa.yaml` - filter + overlay detail item
- `app/scr_History.pa.yaml` - riwayat transaksi
- `app/scr_dashboard.pa.yaml` - KPI, stok menipis, transaksi terbaru
- `app/canvas-app-shared.md` - bagian "KOREKSI WAJIB" (aturan hasil uji di Studio)
- `ALUR-KERJA.md` - Log desktop (riwayat masalah dan solusinya)

## Data (Dataverse, sudah ditambahkan di app mobile)
dis_areas, dis_category_items, dis_geounits, dis_histories, dis_item_v2S, dis_locations, dis_move_items, dis_stocks,
dis_subbls, dis_trx_details, dis_trx_headers, dis_users, Users.

Relasi penting:
- `dis_stocks`: qty per item per area (`dis_item_v2` lookup ke item, `dis_area` lookup ke area).
- `dis_item_v2S`: item (name, description, part_number = BPN, uom, min_qty, Image, category).
- `dis_areas`: area (Name); `elv_dis_subblid` -> dis_subbls (business line). Lokasi area ada di DUA lookup:
  `location` (lama, 24/27 terisi) dan `'elv_location (cr8a3_elv_location)'` (baru). Lokasi efektif =
  `Coalesce(location, 'elv_location (cr8a3_elv_location)')`. Lokasi CIB tidak punya geounit.
- `dis_trx_headers`: transaction_number (format `dd/mm/yy/0001`), type (`'type (dis_trx_headers)'.Consume` /
  Receive / Transfer), area_form (asal), area_to (tujuan), 'Created On'.
- `dis_trx_details`: header_id, PK, item, qty.
- `dis_users`: email, Name, role (`dis_role.administrator` = admin, lainnya user), `'Sub Business Line'` -> dis_subbls.

**User melarang mengubah data Dataverse.** Semua perbaikan lewat logika app saja.

## Aturan akses (salin dari desktop App.Formulas)
Akses area mengikuti Sub Business Line user. Admin JUGA hanya melihat area BL-nya di Consume/Find/History.
```
nfMe = LookUp(dis_users, email = User().Email);
nfIsAdmin = nfMe.role = dis_role.administrator;
nfMyBl = nfMe.'Sub Business Line';
nfMyAreas = Filter(dis_areas, elv_dis_subblid.dis_subbl = Coalesce(nfMyBl.dis_subbl, GUID("00000000-0000-0000-0000-000000000000")));
nfMyAreaIds = ForAll(nfMyAreas, ThisRecord.dis_area);
nfLowStock = Filter(AddColumns(dis_stocks, MinQty, dis_item_v2.min_qty), qty < MinQty);
```
- GUID nol dipakai supaya user tanpa BL tidak cocok dengan area yang tidak punya BL.
- Tidak ada "home area" default. User tanpa BL = tidak melihat stok apa pun (tampilkan pesan, jangan error).
- User yang belum terdaftar di dis_users (`IsBlank(nfMe.Name)`): desktop membuka screen Register (pesan "belum
  terdaftar" + daftar admin dengan tombol kirim email). Mobile: lihat docs/mobile-plan.md.

## Logika Consume (kontrak data, jangan diubah tanpa diminta)
Sama dengan desktop `btnConsSubmit.OnSelect`:
1. Validasi: ada baris dengan qty > 0, dan tidak ada qty > stok atau < 0.
2. Baris terpilih dikelompokkan per area asal stok (`dis_stocks.dis_area`); SATU header Consume per area:
   `Patch(dis_trx_headers, Defaults(..), {transaction_number, type: 'type (dis_trx_headers)'.Consume, area_form})`.
   Nomor transaksi: ambil transaksi terakhir (Sort 'Created On' desc), `Value(Right(x, 4)) + 1`, format
   `Text(Now(), "dd/mm/yy/") & Text(n, "0000")`.
3. Per baris: `Patch(dis_trx_details, Defaults(..), {header_id, PK: trxNo & "-" & RandBetween(1000,9999), item, qty})`
   lalu kurangi stok dengan membaca stok TERBARU: `LookUp(dis_stocks, dis_stock = id).qty - amt`.
4. Reset input, Notify sukses.
Area tidak wajib dipilih untuk Consume (filter area hanya untuk menyaring tampilan).

## Aturan wajib Canvas (dipelajari dari desktop, semua pernah menyebabkan bug)
1. **Galeri hitam**: JANGAN taruh rumus `ThisItem` di `Fill` GroupContainer di dalam galeri. Fill container baris =
   konstanta (`RGBA(0,0,0,0)` atau putih); highlight baris lewat `TemplateFill` galeri.
2. **Baris galeri**: jangan pakai ModernIcon (jadi lingkaran kosong/hitam); aksi baris = ModernButton
   `Layout: =ButtonLayout.IconOnly`, `Appearance: =ButtonAppearance.Subtle`. Hindari container bersarang dalam baris.
   Jangan pakai `Select(Parent)` di control dalam container baris.
3. **Warna**: tulis literal `RGBA(...)` di properti control; named formula `clr*` boleh ada tapi tidak dipakai.
4. **Jangan ganti tipe control** (Classic <-> Modern) lewat compile pada control yang sudah ada; buat control baru
   dengan nama baru, atau restyle di tempat.
5. **Jangan pindah parent container** (re-parent) lewat compile; buat container baru dengan nama baru.
6. **Filter Dataverse**: sisi kanan perbandingan harus konstanta. JANGAN tulis `nfIsAdmin Or x in nfMyAreaIds` di
   Filter (hasilnya kosong saat runtime); pakai `If(nfIsAdmin, true, x in nfMyAreaIds)`. `If(cond, Choices(..),
   Filter(Choices(..)))` merusak tipe record (display name hilang); pakai `Filter(Choices(..), If(...))`.
7. ModernText tidak punya `Tooltip`. Navigate di `OnVisible` ditolak compiler.
8. Formula satu baris yang mengandung `": "` (mis. `{Qty: x}`) harus diberi tanda kutip atau `|-`, kalau tidak
   YamlInvalidSyntax.
9. `CountIf(ds, true)` daripada `CountRows(ds)` (hindari warning nilai cache). Delegasi: `in` ke koleksi tidak
   delegable; aman selama dis_stocks < 500 baris.
10. Gejala "binding" Studio setelah compile (Fill hitam, dropdown form tidak bisa memilih) BUKAN kesalahan YAML:
    minta user ketik ulang rumus / hapus "Depends on" di Studio, lalu Save. Jangan ubah struktur YAML untuknya.

## Sync & compile (WAJIB)
- `sync_canvas` dulu sebelum mengedit, ke folder sementara baru di scratchpad (bukan `app/`), lalu bandingkan dengan
  `app/` (`diff -r --strip-trailing-cr`). Perubahan manual user di Studio disalin ke `app/` dulu.
- Studio menulis YAML tanpa properti default (FillPortions =1, Fill transparan, Wrap =true, Width =200, ...). Itu
  bukan perubahan; jangan timpa `app/` dengan hasil sync hanya karena itu.
- Compile: salin HANYA file `.pa.yaml` yang diubah ke folder sync sementara, `compile_canvas` (`directoryPath`) ke
  folder itu. Compile seluruh `app/` membuat Studio membangun ulang semua screen (bug binding muncul lagi).
- Compile yang GAGAL tetap mengirim file ke Studio: sync ulang, perbaiki, compile lagi.
- `connect` HTTP 422 = tidak ada tab Studio yang memegang mode edit; minta user buka Studio mode edit.
- Setelah compile, ingatkan user: tunggu Studio tampil (sering putih sebentar), **JANGAN refresh sebelum File > Save**
  (refresh membuang sesi yang belum disimpan), cek gejala binding, lalu File > Save.
- `_EditorState.pa.yaml` tidak di-commit (.gitignore).

## Penutup tiap tugas
Tambah satu entri di Log `ALUR-KERJA.md`, commit, push ke main. Jawab singkat.
