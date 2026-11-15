---
name: apps-script-coding-standards
description: Coding conventions for Google Apps Script (.gs server files) and plain browser JavaScript, HTML, and CSS in Sheets sidebars and HtmlService web apps. Use when writing, reviewing, or refactoring Apps Script code, naming functions or variables, structuring .gs and .html files, or handling google.script.run calls and their errors.
metadata:
  origin: Diadaptasi dari affaan-m/ECC skill coding-standards (lisensi MIT). Dipangkas untuk Apps Script dan JavaScript biasa.
---

# Apps Script Coding Standards

Konvensi dasar untuk kode Google Apps Script dan JavaScript browser biasa. Skill ini tidak membahas React, TypeScript, Node.js, atau framework lain.

## Kapan dipakai

- Menulis atau meninjau file .gs dan .html
- Memberi nama fungsi dan variabel
- Merapikan struktur proyek Apps Script
- Menangani error pada panggilan google.script.run

## Dua lingkungan, dua aturan

- File .gs berjalan di server Google. Di sana tidak ada DOM, window, atau localStorage, tetapi Sheets dan Drive bisa diakses langsung.
- JavaScript di file .html berjalan di browser. Ia tidak bisa mengakses Sheets secara langsung. Ia hanya memanggil fungsi .gs lewat google.script.run.
- Jangan mencampur logika dua lingkungan dalam satu fungsi.
- Cek dokumentasi runtime V8 Apps Script sebelum memakai async/await atau Promise di file .gs.

## Penamaan

Nama fungsi memakai kata kerja diikuti objek. Nama variabel menjelaskan isinya.

```javascript
// BAIK
function hitungTotalPenjualan(baris) { }
const jumlahBaris = baris.length;

// KURANG
function total(d) { }
const n = baris.length;
```

Identifier boleh memakai bahasa Indonesia atau Inggris, asalkan konsisten di seluruh proyek.

## Immutability

Gunakan `const` secara default. Buat salinan dengan spread alih-alih mengubah objek atau array asli.

```javascript
const dataBaru = { ...dataLama, status: 'selesai' };
const daftarBaru = [...daftar, itemBaru];

// HINDARI
dataLama.status = 'selesai';
daftar.push(itemBaru);
```

Aturan ini berlaku untuk data di memori JavaScript. Operasi pada sheet, seperti `setValue` atau `appendRow`, memang mengubah sheet.

## Error handling di file .gs

```javascript
function ambilData(idSpreadsheet) {
  try {
    const sheet = SpreadsheetApp.openById(idSpreadsheet).getActiveSheet();
    return sheet.getDataRange().getValues();
  } catch (error) {
    console.error('Gagal membuka spreadsheet:', error);
    throw new Error('Data tidak bisa dimuat. Periksa ID dan izin akses.');
  }
}
```

Detail teknis masuk ke log server. Pesan yang dikirim ke klien tetap singkat.

## google.script.run di browser

```html
<script>
  function tampilkanData(hasil) {
    document.getElementById('hasil').textContent = JSON.stringify(hasil);
  }

  function tampilkanError(error) {
    document.getElementById('pesan').textContent = error.message;
  }

  google.script.run
    .withSuccessHandler(tampilkanData)
    .withFailureHandler(tampilkanError)
    .ambilData(idSpreadsheet);
</script>
```

Selalu pasang `withFailureHandler`. Tanpa itu, kegagalan bisa terjadi tanpa pesan apa pun.

## Tampilan

- Gunakan `textContent` untuk teks dari server, bukan `innerHTML`.
- Letakkan CSS di `<style>` atau file terpisah, dan JavaScript di `<script>`.
- Hindari angka ajaib. Beri nama konstanta: `const MAKS_BARIS = 500;`

## Struktur file

- `Code.gs` berisi fungsi utama dan `onOpen()`.
- Pisahkan file .gs per tanggung jawab jika mulai panjang.
- Untuk memisahkan CSS atau JavaScript dari HTML utama, gunakan helper `include`:

```javascript
function include(namaFile) {
  return HtmlService.createHtmlOutputFromFile(namaFile).getContent();
}
```

Di file HTML utama, panggil `<?!= include('Styles'); ?>`. File utama harus dimuat dengan `HtmlService.createTemplateFromFile()` agar tag `<?!= ?>` diproses.

## Checklist

- [ ] Nama fungsi menjelaskan tindakan yang dilakukan
- [ ] `const` dipakai secara default
- [ ] Setiap pemanggilan google.script.run punya `withFailureHandler`
- [ ] Fungsi .gs yang menyentuh Sheets, Drive, atau layanan eksternal memakai try/catch
- [ ] `textContent` dipakai untuk data dari server
- [ ] Tidak ada angka ajaib tanpa nama
