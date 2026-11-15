# GAS

Kumpulan skill Claude untuk belajar dan mengembangkan Google Apps Script, HTML, CSS, dan JavaScript.

## Isi repositori

| Folder | Fungsi | Sumber |
|---|---|---|
| `tutor-apps-script/` | Membimbing tahap demi tahap. Claude menjelaskan dan memberi petunjuk, bukan langsung menulis solusi lengkap. | Dibuat untuk repositori ini |
| `apps-script-coding-standards/` | Konvensi penamaan, immutability, error handling, dan struktur file untuk file `.gs` dan JavaScript browser. | Diadaptasi dari `coding-standards` ECC |
| `apps-script-security-review/` | Checklist keamanan untuk web app dan sidebar Apps Script: rahasia, validasi input, injeksi rumus di Sheets, dan pengaturan deployment. | Diadaptasi dari `security-review` ECC |

## Cara memasang di claude.ai

1. Zip setiap folder skill, dengan folder itu sebagai root ZIP. Isi ZIP harus berbentuk `nama-folder/SKILL.md`, bukan file langsung di root.
2. Buka claude.ai, lalu masuk ke **Customize > Skills**. Pada sebagian tampilan, menunya ada di **Settings > Capabilities**.
3. Klik **+ > Create skill > Upload a skill**, lalu pilih ZIP.
4. Nyalakan toggle skill tersebut.

Sumber pihak ketiga menyebut bahwa custom skill membutuhkan paket tertentu dan fitur code execution yang aktif. Jika unggahan gagal, periksa keduanya di pengaturan akunmu.

## Cara memakai di Claude Code

Salin folder skill ke `.claude/skills/` di dalam proyek untuk hanya proyek itu, atau ke `~/.claude/skills/` untuk semua proyek.

## Batasan

- Skill ini hanya memberi panduan kepada Claude. Skill tidak menjalankan Apps Script dan tidak mengakses akun Google-mu.
- Skill ini dibuat untuk belajar dan belum diuji secara menyeluruh di claude.ai.
- Checklist keamanan di sini tidak menggantikan peninjauan manual sebelum web app dibagikan ke publik.

## Atribusi dan lisensi

Dua skill, `apps-script-coding-standards` dan `apps-script-security-review`, diadaptasi dari [affaan-m/ECC](https://github.com/affaan-m/ECC), yang berlisensi MIT. Salinan lisensi asli ada di [`LICENSE-ECC`](LICENSE-ECC).

Bagian buatanku (README.md, tutor-apps-script/) berlisensi MIT, lihat [`LICENSE`](LICENSE).

## Catatan versi

Skill ini adalah salinan dari ECC pada saat dibuat. Perubahan di repositori asal tidak otomatis masuk. Untuk memperbaruinya, salin ulang dari sumbernya dan buat ulang ZIP-nya.
