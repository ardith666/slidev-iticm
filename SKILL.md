---
name: "slidev-iticm"
description: "Tambah Alur baku deck baru konsisten P04-P05"
---

# Slidev-ITICM

Hasilkan deck HTML tunggal interaktif ala P04 untuk MK ITICM mana pun.

## Alur baku deck baru

1. Audit caption noref via regex `lihat s`.
2. Definisi eksplisit di s-basis/s-metode (`X adalah ...`).
3. Footnote `lihat sX` tiap caption kecuali s0/kuis/drill, target 0 noref.
4. Kuis self-contained 3 soal tanpa `lihat sX`, tiap jawaban WAJIB di collapse `<details class="flow"><summary>Lihat Jawaban</summary>`, dilarang teks jawaban polos terlihat langsung.
5. Drill 3 uji kecil sebab-akibat-alasan, habis di kelas.
6. TM fokus pertemuan + backup verbatim RPS di `<!-- dev backup -->`.
7. Verify `grep -c '<section'`=N+1, `node --check`, lalu push skill.
8. Demo praktik kode = satu file PHP mandiri di folder deck (mis. `scheduling.php`), dipanggil dari slide via `<iframe src="…php">` — bukan chart.js raksasa di dalam deck.
9. Deck yang punya blok kode: Prism.js + tombol Salin (lihat section "## Prism.js + tombol Salin").

## Demo praktik PHP via iframe (pola P05 s25)

1. **File demo mandiri** — satu `demo.php` di folder deck: `declare(strict_types=1)`, data studi kasus tetap (bukan random) didefinisikan di awal + ditampilkan sebagai tabel, logika + HTML dalam satu file, dijalankan lewat `php -S 127.0.0.1:8000 -t .` (bukan `file://`). Data jangan diakses fungsi lewat `global` (lihat Pitfalls: inklusi dari controller).
2. **Struktur slide** — narasi lengkap **di atas** iframe (`<div class="narrow">` h2 border oranye + 2–3 paragraf: studi kasus, constraint, goal, g(n)/h(n)), lalu iframe `loading="lazy"` tinggi ~520px **wajib ditutup `</iframe>`** (tidak ditutup = slide aneh, konten melorot), baris catatan cara menjalankan, lalu dua tombol: **Unduh** (`demo.php?unduh=1`, endpoint unduh di header PHP pakai `readfile(__FILE__)` + `Content-Disposition` filename deskriptif `pXX-topik.php`) dan **Buka di tab baru** (`target="_blank"`).
3. **Grafik perbandingan di dalam demo, bukan di deck** — taruh di bawah kartu hasil sebagai SVG inline buatan PHP (tanpa library JS), label nilai per batang, skala logaritmik bila angka timpang (mis. 3390 vs 7); jangan tambah `<script>` chart.js besar di bawah iframe (lambat, deck jadi berat).
4. **Kartu hasil + rangkuman** — tiap kartu algoritma: KPI (ekspansi / penugasan / slot), tabel hasil, jejak pencarian di `<details>`, dan paragraf "kenapa eksplorasinya segitu" (`<p class="kenapa">`); paling bawah satu card rangkuman: tabel perbandingan + grafik + kesimpulan.
5. **Jebakan performa ruang keadaan** — penugasan berurutan (hanya MK berikutnya yang dipasang slot+ruang) supaya ruang keadaan kecil; `h(n)` di-cache (`static $cache`); string jejak jangan simpan frontier penuh (pakai helper potong 4 simpul + `… +N`) — kalau tidak, BFS OOM dan A* bisa 7 detik; target render < 1s via `php -l` + `php -r '$_GET=[]; include "demo.php";'` tanpa notice.
6. **Narasi slide vs isi demo** — paragraf di slide harus memuat angka/istilah yang sama persis dengan demo (banyak ekspansi, definisi g/h, constraint) supaya tidak ada klaim yang didemo tidak mendukung.

## Prism.js + tombol Salin (pola P04–P15)

Blok kode di deck WAJIB disorot sintaks + bisa disalin satu klik. Tanpa ini mahasiswa screenshot layar, bukan menyalin teks.

### 1. Markup

`<pre><code class="language-sql">…</code></pre>` — satu baris, isi escape `&lt; &gt;`.

| class | untuk |
|---|---|
| `language-sql` | SQL: SELECT/INSERT/UPDATE/DELETE/CREATE/ALTER/DROP/SHOW/EXPLAIN/GRANT/REVOKE/WITH/START TRANSACTION/BEGIN/COMMIT/ROLLBACK/TRUNCATE/CALL/DESCRIBE/USE/SET/ANALYZE/OPTIMIZE/SAVEPOINT |
| `language-php` | PHP: `<?php`, `$var =`, `// komentar` |
| `language-markup` | HTML: `&lt;form`, `&lt;input`, `&lt;button` (grammar `markup` sudah ikut CDN) |
| `language-none` | teks polos: output `mysql>`, daftar pustaka, path, hasil hitung, pseudocode `PROGRAM/DEKLARASI/ISI` |

Prism core **tidak** punya grammar `none` → blok dibiarkan polos (aman, tanpa error). Jangan asal tebak `language-sql` untuk blok teks.

### 2. Head — 7 baris, sisipkan SEBELUM `<style>` deck

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/prismjs@1.29.0/themes/prism-tomorrow.min.css">
<script src="https://cdn.jsdelivr.net/npm/prismjs@1.29.0/components/prism-core.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs@1.29.0/components/prism-markup.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs@1.29.0/components/prism-clike.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs@1.29.0/components/prism-markup-templating.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs@1.29.0/components/prism-php.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs@1.29.0/components/prism-sql.min.js"></script>
```

`prism-php` butuh `prism-markup-templating` (isi `<?php ?>`), `prism-sql` butuh `prism-clike`. Urutan: core → markup → clike → markup-templating → php → sql.

### 3. CSS — sisipkan setelah `<style>` deck (tema Prism harus kalah spesifisitas)

```css
pre{position:relative}
pre code{background:none;padding:0;border-radius:0;font-size:inherit}
pre[class*=language-]{background:#2d2d3a;padding:14px;margin:12px auto;max-width:720px;border-radius:10px;font-size:.82rem;line-height:1.45}
pre code[class*=language-]{color:inherit}
.codeBtn{position:absolute;top:6px;right:6px;z-index:2;background:#1a1a2e;color:#c0c5d0;border:1px solid #4a4a60;border-radius:6px;font-size:.68rem;padding:2px 8px;cursor:pointer;opacity:0;transition:opacity .18s}
pre:hover .codeBtn,.codeBtn:focus{opacity:1}
.codeBtn:hover{background:#e86c00;border-color:#e86c00;color:#fff}
@media (hover:none){.codeBtn{opacity:1}}
@media print{.codeBtn{display:none!important}}
```

### 4. JS — sisipkan di awal `<script>` inline deck

```js
function kodeTeks(pre){var c=pre.querySelector('code');return c?c.textContent:pre.textContent;}
function salinLama(t,ok){var ta=document.createElement('textarea');ta.value=t;ta.setAttribute('readonly','');ta.style.position='fixed';ta.style.left='-9999px';document.body.appendChild(ta);ta.select();try{document.execCommand('copy');ok();}catch(e){}document.body.removeChild(ta);}
function salinTeks(t,btn){var ok=function(){btn.textContent='Tersalin';setTimeout(function(){btn.textContent='Salin';},1400);};if(navigator.clipboard&&navigator.clipboard.writeText){navigator.clipboard.writeText(t).then(ok,function(){salinLama(t,ok);});}else{salinLama(t,ok);}}
document.querySelectorAll('pre').forEach(function(pre){if(!pre.querySelector('code'))return;var b=document.createElement('button');b.className='codeBtn';b.type='button';b.textContent='Salin';b.setAttribute('aria-label','Salin kode');b.onclick=function(e){e.preventDefault();salinTeks(kodeTeks(pre),b);};pre.appendChild(b);});
```

### Jebakan Prism

- Tema Prism menimpa `<pre>`: `pre[class*=language-]{padding:1em;margin:.5em 0}` mengalahkan `pre{padding:14px;margin:12px auto}` deck (spesifisitas lebih tinggi) → wajib baris override `pre[class*=language-]{…}` di atas.
- `pre` wajib `position:relative`, tanpa itu tombol Salin melayang jauh (mengikuti presisi elemen terdekat).
- Tanpa `<code>` di dalam `<pre>`, tombol tidak muncul — script cari `pre.querySelector('code')`.
- `file://` tidak punya `navigator.clipboard` → fallback `document.execCommand('copy')` wajib ada.
- `@media print` sembunyikan tombol; `@media (hover:none)` tombol selalu terlihat (HP).
- Prism saat runtime menambah class `language-x` juga ke `<pre>` (bukan cuma `<code>`) — jangan hard-code class itu.
- Blok pseudocode putih (style `background:#fff` + border-kiri orange) + `language-none` → tema Prism bikin teks `#ccc` di atas putih = tidak terbaca. Wajib baris `pre code[class*=language-]{color:inherit}` (spesifisitas `0,1,2` > `code[class*=language-]` `0,1,1`).
- Deck tanpa blok kode apa pun (cek `grep -c '<code class="language-' index.html` = 0) tidak perlu CDN Prism sama sekali — P01–P03 + P16 di Basis Data, P04 + P08 di Algoritma.

## Langkah

1. **Bangun kerangka deck tunggal** — satu `index.html`, section `s0` cover + `s1-sN` isi (bagian isi diberi nomor 1..N), hash `#/N`, keyboard panah, progress bar, fullscreen, `?all=1` untuk cetak, tanpa swipe. Slide terakhir `sN` selalu Terima Kasih gaya cover mirror `s0` (`div.wide.cover`, logo, H1 Terima Kasih, chips bawa pulang, tombol Kembali/Ulangi), bukan narrow. Cover `s0` tanpa caption keterangan hitungan halaman: jangan tulis teks "Halaman judul tidak dihitung" atau "Isi dimulai halaman 1". Kerangka (CSS, nav bawah, `gloModal`, `jumpWidget`, satu blok `<script>` inline) identik antar MK: salin `index.html` MK sebelumnya lalu tukar konten, `N`, `GLO`, dan storage key — jangan tulis ulang kerangka. Deck utuh hanya kalau `grep -c '<section'` = N+1 DAN `<nav>`, `gloModal`, `jumpWidget`, serta blok `<script>` ada. Selesai saat pindah slide tanpa reload dan hash sinkron.
2. **Paparan ringkas, bukan buku** — 1 narasi pendek 1-2 kalimat + 1 kartu Inti max 3-5 butir (5-7 kata per butir) + 1 visual (mermaid ERD/tabel atau blok SQL atau blok `<pre>` pseudocode). Jangan 3 paragraf panjang 8-12 kalimat. Ikuti draft pptx saja, tanpa improvisasi luar draft. Judul cover lock sesuai plan, jangan ubah. Definisi eksplisit wajib: slide basis bedakan basis vs representasi dalam 1 kalimat (`basis adalah catatannya, representasi adalah cara tulisnya agar mesin bisa pakai`); slide metode awali dengan `X adalah ...` sebelum langkah (misal `forward chaining adalah cara menalar dari fakta ke kesimpulan, bottom-up`). Topik pseudocode: tiap slide contoh wajib blok `<pre>` `PROGRAM/DEKLARASI/ISI` keyword Indonesia `BACA TULIS JIKA SELAMA UNTUK AKHIR`, escape `&lt; &gt;`, style putih border-kiri orange; padanan Inggris hanya di caption. Selesai saat tiap slide 1 ide, bahasa formal tanpa kata takbaku (muter/boros/hang/sunyi/gampang/bikin/ngubek/bolong), dan tiap slide contoh memuat `PROGRAM`.
3. **Kunci angka domain** — catat total aturan, fakta awal, turunan, total, putaran fixed, kembar, maxIter, bug, batas TM dari RPS di kepala deck + cover stats. Selesai saat angka konsisten di narasi, keypoint, glosarium, chart.
4. **Pasang glosarium inline + jump** — satu file, array `GLO [istilah,kat,definisi]`, search substring, chips kategori, highlight `<mark>`, paginasi 12, klik kartu lompat ke slide + highlight kuning pulse orange, widget fixed kanan-bawah persisten sampai x. Grid `#gloGrid` selalu `grid grid-cols-2 gap-2` (HP dan desktop sama, hemat scroll). Mobile popup tetap popup kanan-kiri: `@media(max-width:640px){#gloModal.open{padding:12px}#gloPanel{max-height:88vh;border-radius:14px;height:auto;width:calc(100% - 24px)}#gloGrid{gap:6px}.gloCard{padding:6px 8px;font-size:12px}.gloCard .text-sm{font-size:12px}.gloCard .text-xs{font-size:11px}}`, jangan full-height/radius-0/padding-0. Jump highlight anti-race: `goTo` async + `await sync()`, `jumpGo` delay 80ms sebelum `scrollIntoView(center)`, CSS `.slide p,.slide h2,.slide li,.slide .card{scroll-margin-top:70px;scroll-margin-bottom:80px}`, mobile `.jumpHi{outline:4px;background:#ffe8c7;pulse 2s}`. Widget mobile ramping kanan: `@media(max-width:640px){#jumpWidget{left:auto;right:12px;width:240px;max-width:70vw;bottom:64px;border-radius:10px}...font 11-12px}`. Selesai saat klik istilah pindah slide benar, highlight center terlihat di HP, dan widget tidak menutup konten.
5. **Persist state** — simpan `cur+q/kat/page+jump+modal` ke localStorage `p04state_v1` (ganti nama per MK), hash menang atas storage, `?all=1` skip restore. Selesai saat reload kembali ke posisi semula.
6. **Visual aman + teks paragraf** — kartu putih aksen orange `#e86c00` bg `#1a1a2e`, tabel jadi card putih header tint stripe, visual bawah center max 720 caption siswa, dev `<!-- -->`, split wide hanya tabel/graf/trace, layout safe-center, stack pin Tailwind CDN + GSAP + Mermaid + Chart.js lazy on-enter fallback `<pre>`, animasi `.45s` stagger fadeUp hover lift hormat `prefers-reduced-motion`. Narasi `p.mt-3` wajib paragraf: `justify + indent 2em + hyphens auto`; center hanya `h2`, `.caption`, cover `s0` + penutup `sN` (`#s0 p/div, #sN p/div center`), dan wadah visual (`.tblWrap/.mermaid` center); teks wadah `.kp/.card/details` wajib kiri: `.kp{text-align:left}`, `.card{text-align:left}` + `.card p{text-align:left;text-indent:0}` + `details.card summary{text-align:left}`, lock `#s30 .kp/.card/details/summary left` agar tak ketarik `#s30 center`; kode dan daftar tetap kiri: `.slide pre{text-align:left}`, `.slide ul,.slide ol{text-align:left;display:inline-block}` agar struktur `PROGRAM` dan butir tidak rusak. Selesai saat tidak ada tabel transparan, narasi justify berindent, kp/card/kuis kiri, head/cover/visual center sedang pre/ul kiri.
7. **Caption per slide + footnote rujuk** — teks kecil bawah tiap visuals pakai `<p class="caption">`, isi keterangan visual + `lihat sX` ke definisi/trace/kode (misal `R4 lihat s15, R10 lihat s16`, `fixed point lihat s10`). Nol caption tanpa rujuk kecuali cover s0; audit via regex `lihat s`. Kuis tutup catatan + drill lab wajib self-contained jawab langsung tanpa `lihat sX`. Wajib center: `.slide p.caption{text-align:center;text-indent:0}` dan `.slide .narrow>.caption{text-indent:0}`. Selesai saat caption center penuh, tidak kena inden 2em, dan 0 noref di luar kuis/drill.
8. **Nav + mobile tanpa swipe** — bottom `[⏮ Prev Next ⏭]` (`goTo(0)`/`goTo(N)`) + keyboard panah saja, dilarang swipe/touchstart/touchend agar zoom/scroll mobile bebas. Tengah `iticm.ac.id • Slide X/N` desktop hide HP, logo kanan `ml-auto` jadi tombol glosarium + badge, `@media 640px` rampingkan header/nav/bar logo `h-6`, kanan `mr-10px`. Selesai saat HP 4 tombol muat logo tidak nempel edge dan tidak ada handler sentuh.
9. **Mermaid aman 19 pola + diagram DB** — flowchart TD vertical-first HP, sequence nama partisipan 1 kata, pesan tanpa `+ ? =`, tanpa `Note over`, label pakai `dan`. Untuk topik konversi/normalisasi tambah erDiagram / classDiagram skema tabel + flowchart alur 1NF-3NF + blok `CREATE TABLE` MySQL read-only (contoh baca, 0 eksekusi). Selesai saat 0 error `translate(undefined,NaN)`.
10. **JS lock** — jangan global-replace teks di dalam `<script>`, validasi `node --check` tiap edit, tanpa handler swipe. Selesai saat navigasi tombol + sync jalan tanpa SyntaxError.
11. **Kartu TM dari RPS, boleh fokus pertemuan** — default salin teks resmi Tabel RPS (nama/sifat/isi/batas/nilai) tanpa menebak. Bila RPS generik kabur dari fokus pertemuan (misal TM1 Sub-CPMK 2,3 campur logika+representasi), boleh rewrite fokus pertemuan (nama fokus, min 10 IF THEN id/if/then PHP, run sampai fixed point, lampir output) + backup verbatim RPS di `<!-- dev backup ... -->`. Selesai saat dosen bisa audit ke RPS via backup.
12. **Tombol PDF di header** — satu tombol `PDF` di header sebelah All/Full, `onclick="printPDF()"`. Fungsi `printPDF()`: `await renderAllMermaid()` dulu (lazy diagram wajib render), tambah class `all` sementara ke `body`, `window.print()`, lalu kembalikan class semula via `afterprint` + timeout cadangan. CSS `@media print`: sembunyikan `header,nav,#bar,#gloModal,#jumpWidget`, `body` bg putih, `.slide{display:block!important}` + `page-break-inside:avoid`, `pre,.mermaid,.card,.kp,.tblWrap,.chartWrap` `break-inside:avoid`. Selesai saat klik PDF cetak semua N+1 slide penuh, bukan 1 slide aktif.
13. **Kuis + drill ringan** — kuis 3 soal self-contained: beda fakta vs aturan, arah metode 1 kalimat, stop kapan 1 kalimat; tiap jawaban WAJIB di dalam collapse `<details class="flow"><summary>Lihat Jawaban</summary>` (dilarang `<p><b>Jawab:</b>` polos), tanpa `lihat sX` di slide kuis. Drill 3 uji kecil habis di kelas, tiap uji tulis sebab-akibat-alasan (tambah fakta=muncul turunan, kosongkan=stop langsung, reverse=total sama). Tanpa hafal nomor aturan, tanpa buka slide lain. Selesai saat 3 menit kuis + 10 menit drill cukup, dan audit `grep -c '<details'` di slide kuis = 3.
14. **Humanizer ID (blader/humanizer https://github.com/blader/humanizer)** — cek tiap narasi/keypoint/caption: kalimat tuntas subjek-predikat lengkap tanpa gantung; buang tell AI-ID (kontras "tidak hanya...tetapi", pembuka "penting untuk dicatat", "selain itu" beruntun, frasa zaman, penutup template, triad, inflasi, kata mendalam/komprehensif/holistik/optimalisasi/memfasilitasi). Istilah asing asli jangan terjemah (forward chaining, database, primary key tetap Inggris, padanan Indonesia hanya di glosarium/caption). Selesai saat 0 kalimat gantung + 0 istilah aneh.
15. **Demo praktik PHP via iframe** — ikuti section "## Demo praktik PHP via iframe" di bawah: satu file PHP mandiri + narasi di atas iframe + tombol unduh + grafik di dalam demo. Selesai saat demo jalan mandiri lewat `php -S`, unduh menghasilkan file yang sama, dan slide hanya berisi narasi + iframe + tombol.
16. **Blok kode: Prism.js + tombol Salin** — deck yang punya blok kode WAJIB pakai Prism.js + tombol Salin, ikuti section "## Prism.js + tombol Salin" di bawah. Markup `<pre><code class="language-sql">` / `language-php` / `language-markup` / `language-none`, 7 baris CDN Prism di head sebelum `<style>`, CSS override + JS tombol salin. Selesai saat `grep -c '<code class="language-' index.html` = jumlah blok kode, tiap `<pre>` punya `<button class="codeBtn">`, dan blok SQL/PHP/HTML benar-benar ter-sorot warna. Suntik massal deck yang sudah jadi: `slidev/_build/prism_patch.py` (idempoten, dek-tolerant, skip deck tanpa blok kode).

## Pitfalls

- Deck separuh bisa terlihat penuh: `index.html` berisi section konten s0–s25 tapi nol `<script>` inline → tak ada nav/glosarium/jump, `goTo` mati dan deck tak bisa dipindah. Isi konten dulu lalu kerangka = setengah deck; jangan tandai selesai sebelum cek N+1 section + `<script>`/nav/gloModal/jumpWidget ada. **Pulihkan dengan cangkok kerangka, jangan tulis ulang.** Ambil dari `</section>` terakhir sampai akhir file pada `index.html` MK lengkap sebelumnya — batas itu memuat nav + `gloModal` + `jumpWidget` + satu `<script>` (CSS kedua deck sudah identik, jadi cangkokan membawa stylenya). Sisipkan sebelum `</body>` deck separuh, lalu tukar `p04state_v1`→`pXXstate_v1`, label `P04`→`PXX`, array `GLO` dan `GLO_CATS`. Buang fungsi chart P04 (`drawCh3/11/24` + `let charts={}`) **hanya bila** deck baru tak punya `<canvas id="ch*">`. Verifikasi: `grep -c '<section'` = N+1, `node --check` pada isi `<script>`, dan DOM key yang dipakai `sync()` (`label`, `prog`, `barFill`, `glo*`, `jw*`) ada.
- Caption tidak center meski `.caption` sudah `text-align:center` — penyebabnya `.slide p.mt-3` justify lebih spesifik; tetap pertahankan penimpa `.slide p.caption{text-align:center;text-indent:0}`, bukan menambah `!important` acak.
- Istilah pakai dulu definisi belakangan: wajib footnote `lihat sX` di caption kemunculan, definisi eksplisit di s-basis/s-metode; kuis/drill tetap self-contained.
- `+` di mermaid picu `<g> translate(undefined,NaN)` — ganti `dan`.
- `iticm.ac.id` kepotong `itic...` di HP — hide mid, kanan `ml-auto`.
- Swipe dilarang: jangan tambah `touchstart`/`touchend`; zoom/scroll mobile prioritas, navigasi hanya tombol + keyboard.
- Glosarium mobile wajib popup kanan-kiri, bukan full: jangan `padding:0` / `height:100%` / `radius:0`; grid selalu 2 kolom padat 12px.
- Jump highlight mobile: `sync()` `scrollTo(0,0)` vs `scrollIntoView(center)` race — wajib `goTo` async + delay 80ms + `scroll-margin-top:70px`; outline mobile 4px `#ffe8c7` pulse 2s agar terlihat.
- Widget jump mobile wajib ramping kanan 240px agar tidak menutup highlight: `left:auto;right:12px;width:240px;max-width:70vw`, font 11-12px.
- Keypoint/bullet terlihat center padahal isi kiri — penyebabnya wadah `.kp{text-align:center}` + `ul{display:inline-block}`; wajib `.kp{text-align:left}`, `.card{text-align:left}` + `.card p{left,indent 0}` + `details.card summary{left}`, lock `#s30` inner kiri; jangan center-kan wadah teks bullet/kuis.
- Narasi wajib paragraf justify + indent 2em; center hanya h2/caption/cover s0/penutup sN/visual; `pre/ul/ol` tetap kiri agar `PROGRAM` terbaca.
- `node_modules` di luar www, deny `src/`, CDN load-1 lambat wajar.
- Slide demo praktik aneh/konten melorot — iframe `</iframe>` tidak ditutup atau narasi/iframe/tombol terpisah `</div>` tak seimbang; wajib tutup iframe dan cek `count('<div')==count('</div>')` per section sebelum selesai.
- Demo PHP berat (OOM / >5 detik) — ruang keadaan meledak (penugasan semua kombinasi sekaligus), `h(n)` dihitung ulang tiap scan, atau jejak menyimpan frontier penuh; perbaiki: penugasan berurutan, cache `static`, potong string jejak. Grafik perbandingan jangan chart.js terpisah di deck — SVG inline di dalam demo dengan skala log.
- Demo PHP fatal `TypeError: Argument 1 passed to … must be of the type string, bool given` saat diakses lewat `index.php?r=slide-stream` — controller `include` file di dalam method, jadi variabel top-level bersifat lokal sementara fungsi demo ber-`global` membaca `$GLOBALS` kosong: semua kandidat dianggap tidak valid, `jalur` kosong, `end($jalur)` = `false`. Perbaiki: setelah definisi data, salin `$GLOBALS['matakuliah']/$GLOBALS['ruang']/$GLOBALS['slot']` (atau jangan pakai `global`), dan bungkus `slotAkhir($r)` = `0` bila `jalur` kosong. Uji wajib dengan PHP 8.5 (phpBro: `bin/php/8.5.8/php`, bukan php 7 CLI homebrew): `php -r 'function s($f){ob_start();include $f;return ob_get_clean();} $_GET=[]; echo strlen(s("demo.php"));'`.
- Graf SVG terpotong di bawah — tinggi `viewBox` dihitung dari jumlah baris per kolom kedalaman, dan penghitung baris salah bila penanda awal `$row[$d]=0` tidak pernah di-increment (node pertama tiap kolom hilang dari hitungan; DFS bisa jadi `viewBox` 20px untuk isi 42px). Pakai `$rIdx = $row[$d] ?? 0; $row[$d] = $rIdx + 1;` dan tinggi `20 + maxRow*34 + (terpotong ? 14 : 4)`. Verifikasi: `viewBox H >= max(rectY + rectH) + 4` untuk tiap SVG.
- Perubahan demo tak terlihat setelah edit — stream mengirim `Cache-Control: private, max-age=3600`; minta hard refresh (Cmd+Shift+R) sebelum menyimpulkan masih bug.
- Blok kode tanpa Prism atau tombol Salin — deck tetap "jalan" tapi mahasiswa screenshot layar; atau `<pre>` polos tanpa `<code>` → tombol Salin tidak pernah muncul (script cari `pre.querySelector('code')`); atau tema Prism menimpa `pre` (margin/padding hilang) karena lupa baris override `pre[class*=language-]{…}`.
