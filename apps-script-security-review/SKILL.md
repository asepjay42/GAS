---
name: apps-script-security-review
description: Security checklist for Google Apps Script projects, covering secrets in PropertiesService, validating input from google.script.run, formula injection in Sheets, web app deployment access, and safe error messages. Use when adding a web app or sidebar, handling user input or sheet data, storing API keys, or preparing an Apps Script project for other users.
metadata:
  origin: Diadaptasi dari affaan-m/ECC skill security-review (lisensi MIT). Dipangkas untuk Apps Script.
---

# Apps Script Security Review

Pemeriksaan keamanan untuk proyek Apps Script: file .gs, sidebar HTML, dan web app. Skill ini tidak mencakup CSP, cookie, rate limit Express, Supabase, atau blockchain.

## Kapan dipakai

- Menambahkan web app atau sidebar
- Menerima input dari pengguna lewat formulir
- Menyimpan API key atau token
- Menulis data dari pengguna ke Sheets
- Men-deploy web app untuk orang lain

## 1. Rahasia

Jangan menulis kunci di file .gs atau .html. File HTML bisa dibaca siapa pun yang membuka web app.

```javascript
// HINDARI
const API_KEY = 'sk-abc123';

// BAIK
function ambilApiKey() {
  const kunci = PropertiesService.getScriptProperties().getProperty('API_KEY');
  if (!kunci) {
    throw new Error('API_KEY belum diatur di Script Properties');
  }
  return kunci;
}
```

Atur nilainya lewat menu Script Properties di Project Settings. Pastikan file yang berisi kunci tidak masuk ke repositori.

## 2. Validasi input di server

Fungsi yang dipanggil lewat google.script.run harus memvalidasi argumennya sendiri. Validasi di JavaScript browser hanya membantu pengguna. Ia tidak melindungi data.

```javascript
function simpanNama(nama) {
  if (typeof nama !== 'string') throw new Error('Input tidak valid');

  const bersih = nama.trim();
  if (bersih.length === 0 || bersih.length > 100) {
    throw new Error('Nama harus 1 sampai 100 karakter');
  }
  // lanjutkan dengan data yang sudah bersih
}
```

## 3. Injeksi rumus di Sheets

Teks dari pengguna yang diawali `=`, `+`, `-`, atau `@` bisa dijalankan sebagai rumus. Sebelum menulis ke sel, tambahkan apostrof di depan.

```javascript
function amankanTeks(teks) {
  return /^[=+\-@]/.test(teks) ? "'" + teks : teks;
}

sheet.appendRow([amankanTeks(inputPengguna)]);
```

## 4. Siapa yang menjalankan dan siapa yang bisa mengakses

Saat men-deploy web app, dua pengaturan menentukan risikonya:

- **Execute the app as:** jika memilih "Me", fungsi berjalan dengan izinmu. Siapa pun yang punya akses bisa membaca atau mengubah apa pun yang bisa kamu akses.
- **Who has access:** pilihan "Anyone" membuat web app bisa dipakai tanpa login. Gunakan hanya jika memang diperlukan.

Pilih opsi paling sempit yang masih membuat aplikasi berfungsi. Label opsi bisa berbeda antar versi editor, jadi periksa langsung di layar deployment.

## 5. Identitas pengguna

Jangan mengandalkan `Session.getActiveUser().getEmail()` untuk otorisasi sebelum kamu mengujinya dengan akun lain. Hasilnya bergantung pada pengaturan deployment.

## 6. Log dan pesan error

- Jangan mencatat kata sandi, token, atau data pribadi ke `console.log` atau `Logger.log`.
- Kirim pesan umum ke klien. Detail teknis cukup di log server.

```javascript
catch (error) {
  console.error('Gagal menyimpan:', error);
  throw new Error('Penyimpanan gagal. Coba lagi nanti.');
}
```

## 7. Data dari sheet di browser

Data yang berasal dari sel bisa berisi teks apa pun. Tampilkan dengan `textContent`, bukan `innerHTML`.

## Checklist sebelum membagikan proyek

- [ ] Tidak ada kunci atau token di file .gs maupun .html
- [ ] Setiap fungsi yang dipanggil google.script.run memvalidasi input
- [ ] Teks dari pengguna yang ditulis ke Sheets sudah diamankan dari injeksi rumus
- [ ] "Execute the app as" dan "Who has access" sudah dipilih seminimal mungkin
- [ ] Pesan error tidak membocorkan detail internal
- [ ] Identitas pengguna sudah diuji dengan akun lain
- [ ] Log tidak berisi data sensitif
