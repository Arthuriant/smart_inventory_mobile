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
