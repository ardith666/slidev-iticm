---
name: "slidev-iticm"
description: "Buat deck kuliah ITICM HTML tunggal hash nav glosarium mermaid aman gaya P04 untuk semua MK"
---

# Slidev-ITICM

Hasilkan deck HTML tunggal interaktif ala P04 untuk MK ITICM mana pun.

## Langkah

1. **Bangun kerangka deck tunggal** — satu `index.html`, section `s0` cover + `s1-sN` isi (bagian isi diberi nomor 1..N), hash `#/N`, keyboard panah, progress bar, fullscreen, `?all=1` untuk cetak, tanpa swipe. Cover `s0` tanpa caption keterangan hitungan halaman: jangan tulis teks "Halaman judul tidak dihitung" atau "Isi dimulai halaman 1". Kerangka (CSS, nav bawah, `gloModal`, `jumpWidget`, satu blok `<script>` inline) identik antar MK: salin `index.html` MK sebelumnya lalu tukar konten, `N`, `GLO`, dan storage key — jangan tulis ulang kerangka. Deck utuh hanya kalau `grep -c '<section'` = N+1 DAN `<nav>`, `gloModal`, `jumpWidget`, serta blok `<script>` ada. Selesai saat pindah slide tanpa reload dan hash sinkron.
2. **Tulis narasi 3 paragraf per slide** — tiap paragraf 8-12 kalimat bahasa mahasiswa sopan formal, jelaskan alasan, 1 contoh dekat (kos/kantin/SIAKAD/ojol/KTP), 1 kartu Keypoint max 4 butir, 1 visual. Selesai saat semua slide indent 2em justify tone kampus tanpa kata takbaku (muter/boros/hang/sunyi/gampang/bikin/ngubek/bolong).
3. **Kunci angka domain** — catat total aturan, fakta awal, turunan, total, putaran fixed, kembar, maxIter, bug, batas TM dari RPS di kepala deck + cover stats. Selesai saat angka konsisten di narasi, keypoint, glosarium, chart.
4. **Pasang glosarium inline + jump** — satu file, array `GLO [istilah,kat,definisi]`, search substring, chips kategori, highlight `<mark>`, paginasi 12, klik kartu lompat ke slide + highlight kuning pulse orange, widget fixed kanan-bawah persisten sampai x. Selesai saat klik istilah pindah slide benar.
5. **Persist state** — simpan `cur+q/kat/page+jump+modal` ke localStorage `p04state_v1` (ganti nama per MK), hash menang atas storage, `?all=1` skip restore. Selesai saat reload kembali ke posisi semula.
6. **Visual aman** — kartu putih aksen orange `#e86c00` bg `#1a1a2e`, tabel jadi card putih header tint stripe, visual bawah center max 720 caption siswa, dev `<!-- -->`, split wide hanya tabel/graf/trace, layout safe-center, stack pin Tailwind CDN + GSAP + Mermaid + Chart.js lazy on-enter fallback `<pre>`, animasi `.45s` stagger fadeUp hover lift hormat `prefers-reduced-motion`. Selesai saat tidak ada tabel transparan.
7. **Caption per slide** — teks kecil bawah tiap slide pakai `<p class="caption">`, isi keterangan visual + sumber data, satu per slide. Wajib center: `.slide p{text-align:justify}` (specificity 0,1,1) mengalahkan `.caption{text-align:center}` (0,1,0), jadi tulis dua aturan penimpa: `.slide p.caption{text-align:center;text-indent:0}` dan `.slide .narrow>.caption{text-indent:0}`. Selesai saat caption center penuh dan tidak kena inden 2em.
8. **Nav + mobile tanpa swipe** — bottom `[⏮ Prev Next ⏭]` (`goTo(0)`/`goTo(N)`) + keyboard panah saja, dilarang swipe/touchstart/touchend agar zoom/scroll mobile bebas. Tengah `iticm.ac.id • Slide X/N` desktop hide HP, logo kanan `ml-auto` jadi tombol glosarium + badge, `@media 640px` rampingkan header/nav/bar logo `h-6`, kanan `mr-10px`. Selesai saat HP 4 tombol muat logo tidak nempel edge dan tidak ada handler sentuh.
9. **Mermaid aman 19 pola** — flowchart TD vertical-first HP, sequence nama partisipan 1 kata, pesan tanpa `+ ? =`, tanpa `Note over`, label pakai `dan`. Selesai saat 0 error `translate(undefined,NaN)`.
10. **JS lock** — jangan global-replace teks di dalam `<script>`, validasi `node --check` tiap edit, tanpa handler swipe. Selesai saat navigasi tombol + sync jalan tanpa SyntaxError.
11. **Kartu TM dari RPS** — salin teks resmi Tabel RPS (nama/sifat/isi/batas/nilai) ke slide tugas, tanpa menebak. Selesai saat dosen bisa audit ke RPS.
12. **Tombol PDF di header** — satu tombol `PDF` di header sebelah All/Full, `onclick="printPDF()"`. Fungsi `printPDF()`: `await renderAllMermaid()` dulu (lazy diagram wajib render), tambah class `all` sementara ke `body`, `window.print()`, lalu kembalikan class semula via `afterprint` + timeout cadangan. CSS `@media print`: sembunyikan `header,nav,#bar,#gloModal,#jumpWidget`, `body` bg putih, `.slide{display:block!important}` + `page-break-inside:avoid`, `pre,.mermaid,.card,.kp,.tblWrap,.chartWrap` `break-inside:avoid`. Selesai saat klik PDF cetak semua N+1 slide penuh, bukan 1 slide aktif.

## Pitfalls

- Deck separuh bisa terlihat penuh: `index.html` berisi section konten s0–s25 tapi nol `<script>` inline → tak ada nav/glosarium/jump, `goTo` mati dan deck tak bisa dipindah. Isi konten dulu lalu kerangka = setengah deck; jangan tandai selesai sebelum cek N+1 section + `<script>`/nav/gloModal/jumpWidget ada. **Pulihkan dengan cangkok kerangka, jangan tulis ulang.** Ambil dari `</section>` terakhir sampai akhir file pada `index.html` MK lengkap sebelumnya — batas itu memuat nav + `gloModal` + `jumpWidget` + satu `<script>` (CSS kedua deck sudah identik, jadi cangkokan membawa stylenya). Sisipkan sebelum `</body>` deck separuh, lalu tukar `p04state_v1`→`pXXstate_v1`, label `P04`→`PXX`, array `GLO` dan `GLO_CATS`. Buang fungsi chart P04 (`drawCh3/11/24` + `let charts={}`) **hanya bila** deck baru tak punya `<canvas id="ch*">`. Verifikasi: `grep -c '<section'` = N+1, `node --check` pada isi `<script>`, dan DOM key yang dipakai `sync()` (`label`, `prog`, `barFill`, `glo*`, `jw*`) ada.
- Caption tidak center meski `.caption` sudah `text-align:center` — penyebabnya `.slide p{text-align:justify}` lebih spesifik; perbaikannya `.slide p.caption{text-align:center;text-indent:0}`, bukan menambah `!important` acak.
- `+` di mermaid picu `<g> translate(undefined,NaN)` — ganti `dan`.
- `iticm.ac.id` kepotong `itic...` di HP — hide mid, kanan `ml-auto`.
- Swipe dilarang: jangan tambah `touchstart`/`touchend`; zoom/scroll mobile prioritas, navigasi hanya tombol + keyboard.
- `node_modules` di luar www, deny `src/`, CDN load-1 lambat wajar.
