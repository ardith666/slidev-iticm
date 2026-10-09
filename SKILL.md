---
name: "slidev-iticm"
description: "Buat deck kuliah ITICM HTML tunggal hash nav glosarium mermaid aman gaya P04 untuk semua MK"
---

# Slidev-ITICM

Hasilkan deck HTML tunggal interaktif ala P04 untuk MK ITICM mana pun.

## Langkah

1. **Bangun kerangka deck tunggal** — satu `index.html`, section `s0` cover tidak dihitung + `s1-sN` isi, hash `#/N`, keyboard panah, swipe, progress bar, fullscreen, `?all=1` untuk cetak. Selesai saat pindah slide tanpa reload dan hash sinkron.
2. **Tulis narasi 3 paragraf per slide** — tiap paragraf 8-12 kalimat bahasa mahasiswa sopan formal, jelaskan alasan, 1 contoh dekat (kos/kantin/SIAKAD/ojol/KTP), 1 kartu Keypoint max 4 butir, 1 visual. Selesai saat semua slide indent 2em justify tone kampus tanpa kata takbaku (muter/boros/hang/sunyi/gampang/bikin/ngubek/bolong).
3. **Kunci angka domain** — catat total aturan, fakta awal, turunan, total, putaran fixed, kembar, maxIter, bug, batas TM dari RPS di kepala deck + cover stats. Selesai saat angka konsisten di narasi, keypoint, glosarium, chart.
4. **Pasang glosarium inline + jump** — satu file, array `GLO [istilah,kat,definisi]`, search substring, chips kategori, highlight `<mark>`, paginasi 12, klik kartu lompat ke slide + highlight kuning pulse orange, widget fixed kanan-bawah persisten sampai x. Selesai saat klik istilah pindah slide benar.
5. **Persist state** — simpan `cur+q/kat/page+jump+modal` ke localStorage `p04state_v1` (ganti nama per MK), hash menang atas storage, `?all=1` skip restore. Selesai saat reload kembali ke posisi semula.
6. **Visual aman** — kartu putih aksen orange `#e86c00` bg `#1a1a2e`, tabel jadi card putih header tint stripe, visual bawah center max 720 caption siswa, dev `<!-- -->`, split wide hanya tabel/graf/trace, layout safe-center, stack pin Tailwind CDN + GSAP + Mermaid + Chart.js lazy on-enter fallback `<pre>`, animasi `.45s` stagger fadeUp hover lift hormat `prefers-reduced-motion`. Selesai saat tidak ada tabel transparan.
7. **Nav + mobile** — bottom `[⏮ Prev Next ⏭]` (`goTo(0)`/`goTo(N)`), tengah `iticm.ac.id • Slide X/N` desktop hide HP, logo kanan `ml-auto` jadi tombol glosarium + badge, `@media 640px` rampingkan header/nav/bar logo `h-6`, kanan `mr-10px`. Selesai saat HP 4 tombol muat logo tidak nempel edge.
8. **Mermaid aman 19 pola** — flowchart TD vertical-first HP, sequence nama partisipan 1 kata, pesan tanpa `+ ? =`, tanpa `Note over`, label pakai `dan`. Selesai saat 0 error `translate(undefined,NaN)`.
9. **JS lock** — jangan global-replace teks di dalam `<script>`, kunci `changedTouches`, validasi `node --check` tiap edit. Selesai saat swipe + sync jalan tanpa SyntaxError.
10. **Kartu TM dari RPS** — salin teks resmi Tabel RPS (nama/sifat/isi/batas/nilai) ke slide tugas, tanpa menebak. Selesai saat dosen bisa audit ke RPS.

## Pitfalls

- Replace `hang` merusak `changedTouches` jadi `ctidak meresponsedTouches` — fatal, lindungi `<script>`.
- `+` di mermaid picu `<g> translate(undefined,NaN)` — ganti `dan`.
- `iticm.ac.id` kepotong `itic...` di HP — hide mid, kanan `ml-auto`.
- `node_modules` di luar www, deny `src/`, CDN load-1 lambat wajar.
