# Alur Kerja Harian - Mobile

Semua pekerjaan di terminal: jalankan `claude` dari `C:\Project\PowerappsMobile`. Aturan lengkap ada di CLAUDE.md
(otomatis terbaca).

## Prompt (salin, ganti bagian dalam kurung)
```
Baca ALUR-KERJA.md (Log) dan docs/mobile-plan.md lalu kerjakan:
[tulis permintaan, mis. "bangun screen Consume (langkah 2)"]
```

## Persiapan tiap sesi
1. Buka Studio app mobile dalam mode edit di SATU tab (URL di CLAUDE.md). `git pull`.
2. Claude: connect -> sync ke folder sementara -> bandingkan dengan app/ -> edit -> compile hanya file yang berubah.
3. Setelah compile: tunggu Studio tampil, JANGAN refresh, cek gejala binding, File > Save, uji di HP kalau perlu.

---

## Log (entri terbaru di bawah)

Format: `- [tanggal] file yang diubah | ringkasan | status: belum compile / compile OK / compile gagal: ... | cek manual: ...`

- [2026-10-09] CLAUDE.md, docs/mobile-plan.md, ALUR-KERJA.md, app/ (sync awal) | Setup project mobile: app Phone
  kosong (Screen1) dengan 13 data source Dataverse sudah ditambahkan user; konteks dari project desktop disalin ke
  CLAUDE.md. | status: belum compile | cek manual: -
- [2026-10-09] App.pa.yaml, Components/cmpHeader + cmpTabBar, scr_m_noaccess, scr_m_consume/find/history/dashboard
  (kerangka) | Langkah 1: App.Formulas RBAC + nfHasAccess + palet, StartScreen; komponen header & bottom tab bar;
  screen noaccess (pesan, daftar admin + email, Check again); 4 screen tab berisi header/body placeholder/tab bar supaya
  Navigate bisa dicompile. | status: compile OK (sync ulang: hanya beda properti default) | cek manual: Save di Studio,
  hapus Screen1, cek tab bar (warna aktif, ikon), uji Check again.
- [2026-10-09] scr_m_consume.pa.yaml | Langkah 2: Consume mobile - filter (search, Category, Area, reset, refresh),
  kartu stok dengan stepper (koleksi colConsSel), bar bawah ringkasan + Clear + Consume, lembar konfirmasi berisi daftar
  item; simpan = logika kontrak desktop. Compile pertama gagal (YamlInvalidSyntax: UpdateContext({x: y}) satu baris,
  aturan #8) -> pakai |-. | status: compile OK; App Checker: 1 peringatan ForAllWithMutation (pola kontrak, dibiarkan)
  | cek manual: Save; uji stepper +/-, ketik qty, qty > stok, filter lalu Consume, cek header per area & stok berkurang.
- [2026-10-09] scr_m_consume.pa.yaml (compile ulang), app/Screen1 dihapus | User melihat semua screen "under
  construction": sesi Studio putus (401) sebelum Consume langkah 2 di-Save, jadi Studio memuat versi tersimpan langkah
  1. Find/History/Dashboard memang masih kerangka (langkah 3-5). Connect ulang, kirim ulang scr_m_consume. Screen1
  sudah dihapus user di Studio -> dihapus juga dari app/. | status: compile OK, sync ulang berisi Consume lengkap |
  cek manual: File > Save SEGERA setelah Studio tampil, jangan refresh sebelumnya.
- [2026-10-09] scr_m_consume.pa.yaml, CLAUDE.md (aturan #11) | Restyle Consume gaya e-commerce (permintaan user, tetap
  ringan): search pill, chip kategori (ganti dropdown Category), dropdown Area 1 baris, grid kartu 2 kolom dengan foto
  besar + badge stok + tombol Add yang berubah jadi stepper, bar keranjang navy mengambang, lembar konfirmasi dengan
  handle + thumbnail. Logika simpan tidak berubah. Masalah: compile pertama menaruh kontrol baru di akhir parent (chip di
  bawah daftar, latar kartu menimpa isi) -> compile kedua file yang sama memperbaiki urutan. | status: compile OK, urutan
  terverifikasi lewat sync | cek manual: Save; cek chip aktif, kartu (foto terlihat), Add -> stepper, bar keranjang.
- [2026-10-09] scr_m_consume, scr_m_noaccess, Components/cmpTabBar, CLAUDE.md (aturan #12) | User: layout Consume
  rusak (SS1.png: baris chip/Area melar, galeri kartu kecil, bar keranjang jadi blok navy besar, indikator tab tebal).
  Sebab: GroupContainer di AutoLayout default FillPortions 1, jadi container bertinggi tetap ikut membagi ruang. Fix:
  FillPortions =0 pada conConsSearchRow/ChipRow/DdRow/Cart/Sheet/DlgBtns, conNaBrand, conTb*Ind. | status: compile
  OK, terverifikasi lewat sync | cek manual: Save, screenshot ulang Consume.
- [2026-10-09] scr_m_consume.pa.yaml | Tombol Consume di bar keranjang (btnConsCartGo) terlihat gelap: BasePaletteColor
  putih dirender abu-abu. Ganti ke biru terang RGBA(0, 120, 212, 1) + teks putih. | status: compile OK | cek manual:
  Save, cek kontras tombol di bar navy.
- [2026-10-09] scr_m_consume, Components/cmpTabBar, scr_m_find (dihapus), scr_m_history | (1) Kartu Consume: badge =
  status (In stock/Low stock/Out of stock), teks baru txtConsLeft "Stock left n uom -> sisa setelah consume", kartu 316.
  (2) Find dibatalkan user: tab Find dihapus (tab bar 3 tab), screen dihapus (hapus file dari folder compile + baris
  _EditorState = screen terhapus di Studio). (3) History mobile: search nomor, chip tipe & periode (default 30 hari),
  kartu transaksi, lembar detail item; dibatasi area BL user (desktop tidak memfilter area). App Checker: pakai
  AllItemsCount untuk teks jumlah (Visible galeri tetap IsEmpty(AllItems)). | status: compile OK (2x untuk urutan
  txtConsLeft), sync terverifikasi | cek manual: Save; History: chip, buka detail, nama "By", transaksi area lain tidak
  tampil.
- [2026-10-09] scr_m_dashboard.pa.yaml | Langkah 5: Dashboard mobile - tanggal + refresh, 4 KPI kartu (Stock items, Low
  stock, Out of stock, Consumed today), daftar stok menipis (foto, qty/min) dan 5 transaksi terakhir (See all ->
  History); semua dibatasi area BL user, tanpa koleksi. | status: compile OK, urutan terverifikasi, App Checker tidak ada
  isu baru | cek manual: Save; cocokkan angka KPI dengan data, uji See all.
- [2026-10-09] App.pa.yaml (nfMySysUserId), scr_m_dashboard.pa.yaml, CLAUDE.md | Dashboard diubah jadi HANYA transaksi
  (user: untuk teknisi stok tidak perlu): chip My transactions / All in my BL, KPI Today/7/30 hari + Consume/Receive/
  Transfer 30 hari, 10 transaksi terakhir. Compile pertama "gagal" karena 7 delegation warning dari If(locDashAll, true,
  ...) di dalam Filter -> If dipindah ke luar Filter. | status: compile OK, urutan terverifikasi | cek manual: Save;
  bandingkan "My transactions" dengan transaksi yang kamu buat sendiri.
- [2026-10-09] Components/cmpTabBar.pa.yaml | Tab bar dimodernkan: bar putih sudut atas bulat + DropShadow.Semilight
  (tanpa border), ikon di atas label, tab aktif = ikon filled di atas pill biru muda (conTb*Ind dipakai ulang jadi pill,
  ikon baru btnTb*Ico) + label navy bold. Studio membuang AlignInContainer Stretch di label -> tambah Width =Parent.Width.
  | status: compile OK, urutan terverifikasi | cek manual: Save; cek ketiga tab di HP.
