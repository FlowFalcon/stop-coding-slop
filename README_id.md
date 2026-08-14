# Stop Coding Slop

Agent Skill untuk menghasilkan kode berbantuan AI yang fokus dan menyatu dengan pola repositori.

[English](README.md) · [Bahasa Indonesia](README_id.md)

`stop-coding-slop` membuat coding agent berhenti sejenak sebelum mengedit, menentukan arti “berhasil”, memilih solusi paling sederhana yang tetap lengkap, menjaga perubahan tetap surgical, membuang residu generatif, dan hanya melaporkan verifikasi yang benar-benar dilakukan.

Skill ini tidak bertujuan menyamarkan bahwa kode dibuat dengan bantuan AI. Tujuannya adalah meningkatkan kualitas rekayasa dari perubahan berbantuan AI.

## Apa itu AI coding slop?

AI coding slop adalah kode, test, komentar, atau laporan akhir yang sekilas terlihat masuk akal, tetapi tidak cukup berlandaskan kondisi repositori dan menambah pekerjaan review atau maintenance yang sebenarnya bisa dihindari.

Masalahnya bukan karena kode ditulis oleh LLM. Kode buatan AI bisa benar, langsung, dan mudah dirawat. Slop muncul ketika model lebih mengutamakan jawaban yang tampak lengkap daripada mengintegrasikan perilaku yang diminta ke sistem yang benar-benar ada.

Patch yang penuh slop biasanya memiliki satu atau beberapa ciri berikut:

- langsung menulis kode sebelum memahami perilaku yang sudah ada;
- mengarang import, API, konvensi framework, atau fakta repositori;
- membuat abstraksi untuk kebutuhan masa depan yang belum diminta;
- mengubah file lain hanya karena letaknya berdekatan;
- menambah retry, fallback, konfigurasi, atau cabang defensif tanpa kontrak saat ini;
- menduplikasi perilaku yang sudah ada atau memaksa reuse ketika semantiknya berbeda;
- memberi komentar yang hanya menceritakan ulang kode;
- meninggalkan placeholder, dead branch, contoh, atau implementasi alternatif;
- menangani happy path tetapi melupakan authorization, transaction, cleanup, skala, atau kegagalan;
- membuat test yang meniru detail implementasi tanpa membuktikan perilaku penting;
- mengklaim test berhasil meskipun tidak ada perintah test yang dijalankan.

Kodenya mungkin berhasil dikompilasi. Sebagiannya bahkan mungkin berjalan. Namun, patch itu tetap slop jika maintainer harus membuang mekanisme spekulatif, mencari ulang requirement sebenarnya, memperbaiki perilaku boundary, atau memverifikasi klaim yang tidak didukung sebelum perubahan dapat dipercaya.

## Sinyal kecil yang jika menumpuk menjadi masalah

| Sinyal | Bentuknya | Biaya bagi repositori |
|---|---|---|
| Langsung menerima jawaban pertama | Implementasi pertama yang terasa masuk akal langsung ditulis | Helper, caller, failure path, dan pemilik perilaku yang sudah ada terlewat |
| Integrasi hasil tebakan | Placeholder import, field request rekaan, asumsi API transaksi | Patch bisa gagal dikompilasi atau memakai boundary yang salah |
| Abstraksi terlalu dini | Service, factory, strategy, interface, atau option bag untuk satu use case | Permukaan review dan maintenance membesar tanpa manfaat saat ini |
| Minimalisme palsu | Patch sangat kecil tetapi menghilangkan validasi, rollback, cleanup, atau test | Sedikit baris menyembunyikan kontrak yang tidak lengkap |
| Cleanup melebar | Rename, formatting, dependency, atau refactor dicampur ke tugas | Reviewer sulit memisahkan perubahan perilaku dari noise |
| Reuse yang dipaksakan | Dua alur memakai helper yang sama walaupun validation, error, side effect, atau lifecycle berbeda | Perbedaan semantik tersembunyi di balik flag dan branching |
| Noise defensif | Retry, fallback, regex, telemetry, atau compatibility shim yang tidak diminta | Perilaku dan failure mode baru muncul tanpa requirement |
| Komentar naratif | `// Validasi input` tepat di atas pengecekan yang sudah jelas | Komentar mengulang sintaks, bukan menyimpan pengetahuan |
| Nama template | `data`, `item`, `processData`, `BaseManager`, `ResultHandler` | Makna domain hilang di balik istilah generik |
| Tidak memahami boundary | Buffer seluruh file, concurrency tak terbatas, cancellation diabaikan, byte disamakan dengan karakter | Kode lolos demo tetapi gagal pada kondisi nyata |
| Test dangkal | Hanya memeriksa jumlah row akhir padahal ordering atau atomicity adalah kontrak | Test hijau tidak membuktikan invariant yang penting |
| Handoff terlalu percaya diri | Menulis “semua test lulus” tanpa output perintah atau bukti repo | Reviewer menerima rasa aman palsu, bukan status verifikasi |

Tidak ada satu pola yang otomatis membuktikan bahwa sebuah patch buruk. Skill ini menilai setiap penambahan berdasarkan kontrak saat ini dan bukti repositori, bukan sekadar memakai detektor gaya.

## Mengapa coding agent menghasilkan slop?

Coding agent sering diminta bekerja dengan konteks yang tidak lengkap. Model dapat mengisi kekosongan dengan pola umum, melanjutkan perubahan melewati scope, atau menghasilkan banyak penjelasan karena jawaban seperti itu tampak lengkap jika dilihat sendirian. Pekerjaan repositori berbeda: kebenaran bergantung pada kontrak lokal, abstraksi yang sudah ada, batas operasional, dan failure behavior yang tidak aman jika hanya ditebak dari contoh generik.

Solusinya bukan “tulis baris lebih sedikit” atau “buat seolah-olah ditulis manusia”. Solusinya adalah menginspeksi sebelum mengedit, membuat ketidakpastian terlihat, dan menghubungkan setiap konsep baru dengan requirement yang sedang berlaku.

## Empat prinsip operasional

### 1. Think before coding

Jangan langsung mengubah permintaan menjadi patch pertama yang terasa masuk akal. Inspeksi implementasi, caller, test, type, konfigurasi, dan konvensi di sekitarnya. Cari perilaku yang sudah ada sebelum menambah perilaku baru.

Ini adalah tahap pengumpulan bukti yang singkat, bukan alasan untuk analysis paralysis. Setelah bukti yang tersedia cukup untuk membentuk kontrak yang aman, lanjutkan implementasi.

### 2. Tentukan success criteria yang jelas

Sebelum memilih bentuk kode, tentukan:

- perilaku sukses yang dapat diamati;
- perilaku gagal yang bermakna;
- constraint authorization, compatibility, skala, transaction, dan lifecycle;
- test atau pemeriksaan yang akan membuktikan keberhasilan;
- kumpulan file terkecil yang memiliki perilaku tersebut.

Jika “berhasil” belum dapat dijelaskan melalui hasil yang bisa diamati, target implementasinya masih kabur.

### 3. Prefer simplicity

Pilih desain paling langsung yang memenuhi seluruh kontrak saat ini. Sederhana berarti lebih sedikit konsep dan indirection, bukan membabi buta mengurangi jumlah baris.

Loop langsung yang benar bisa lebih sederhana daripada pipeline reusable. Transaction, cleanup path, atau regression test yang diwajibkan bukan overengineering. Abstraksi yang hanya dibenarkan oleh kemungkinan fitur masa depan adalah overengineering.

### 4. Surgical changes

Ubah hanya file dan baris yang dibutuhkan oleh kontrak. Pertahankan nama publik dan struktur repositori kecuali requirement memang meminta perubahan. Setiap perluasan scope harus memiliki alasan konkret yang terhubung ke perilaku saat ini.

Surgical bukan berarti tidak lengkap. Jika route, domain function, dan regression test sama-sama memiliki bagian dari perilaku tersebut, mengubah ketiganya tetap merupakan patch yang fokus.

## Cara kerja skill

Instruksi dalam [SKILL.md](SKILL.md) mengarahkan agent melalui urutan berikut:

1. Inspeksi bukti repositori sebelum mengedit.
2. Bentuk change contract internal yang ringkas.
3. Pilih bentuk implementasi paling sederhana yang tetap lengkap.
4. Batasi perubahan pada file yang memiliki perilaku tersebut.
5. Hasilkan artefak yang diminta, jangan berhenti pada rencana.
6. Buang helper, branch, komentar, placeholder, dan prosa spekulatif.
7. Jalankan pemeriksaan yang sudah dipakai repositori dan laporkan hasil sebenarnya.

Skill ini juga mencegah minimalisme palsu. Authorization, validation, transaction, cleanup, backpressure, test, dan error propagation yang dibutuhkan harus tetap dipertahankan.

## Contoh kecil

Misalnya sebuah tugas meminta endpoint bulk preference yang harus mem-parse semua item sebelum menulis dan meng-commit seluruh update secara atomik.

Respons yang rawan slop dapat:

- membuat `PreferenceUpdateService`, schema layer, retry policy, dan object konfigurasi baru;
- melakukan parse dan write dalam loop yang sama sehingga data parsial tertinggal ketika item berikutnya invalid;
- menambah komentar untuk setiap statement yang sebenarnya sudah jelas;
- hanya menguji response `204`;
- menutup laporan dengan “semua test lulus” tanpa pernah menjalankan test.

Respons yang menyatu dengan repositori akan:

- memakai parser, repository, audit helper, dan transaction boundary yang sudah ada;
- mem-parse seluruh input sebelum write pertama;
- melakukan write secara berurutan dalam satu transaksi;
- menambah test untuk success, ukuran invalid, atomicity ketika satu entry invalid, dan authentication;
- melaporkan pemeriksaan yang benar-benar dijalankan, atau mengatakan dengan jujur bahwa test belum dieksekusi.

Patch yang lebih baik belum tentu memiliki baris paling sedikit. Ia adalah patch terkecil yang membuktikan seluruh perilaku.

## Hal yang tidak dilakukan skill ini

- Tidak sengaja menyamarkan kode agar terlihat ditulis manusia.
- Tidak melarang abstraksi, komentar, validasi, atau defensive code jika kontrak memang membutuhkannya.
- Tidak menggantikan test repositori, static analysis, security review, atau penilaian maintainer.
- Tidak mendorong kompresi cerdik atau code golf.
- Tidak mengubah public API atau arsitektur secara diam-diam demi mengecilkan diff.
- Tidak mengubah kekurangan bukti menjadi detail implementasi hasil tebakan.

## Kapan skill ini digunakan?

Gunakan skill ketika:

- mengimplementasikan fitur atau bug fix dengan coding agent;
- me-review atau membersihkan diff berbantuan AI;
- melakukan refactor pada kode yang menumpuk layer spekulatif atau residu generatif;
- patch berisi helper generik, logika duplikat, cleanup melebar, atau komentar naratif;
- handoff membuat klaim verifikasi yang perlu diperiksa;
- membutuhkan pemeriksaan akhir agar perubahan sesuai pola repositori sebelum review.

## Instalasi

Clone repositori:

```bash
git clone https://github.com/FlowFalcon/stop-coding-slop.git
```

### Claude Code

Salin folder ke direktori skill personal atau project:

```bash
cp -R stop-coding-slop ~/.claude/skills/stop-coding-slop
```

Untuk instalasi lokal project, gunakan `.claude/skills/stop-coding-slop`.

### Codex

Salin folder ke direktori skills Codex yang dikonfigurasi:

```bash
cp -R stop-coding-slop ~/.codex/skills/stop-coding-slop
```

### Agent lain

Agent yang mendukung konvensi Agent Skills dapat memuat [SKILL.md](SKILL.md). Jika agent tidak memiliki skill discovery, masukkan isi file tersebut sebagai konteks tugas atau system instruction.

## Penggunaan

Panggil skill secara eksplisit jika agent mendukung named skill:

```text
Use $stop-coding-slop untuk mengimplementasikan perubahan ini. Inspeksi repo
terlebih dahulu, definisikan hasil yang dapat diamati, lalu hasilkan diff fokus
dengan laporan verifikasi yang jujur.
```

Skill juga dapat dipakai sebagai pemeriksaan akhir:

```text
Use $stop-coding-slop untuk me-review diff ini. Buang residu generatif tanpa
mengubah perilaku yang diwajibkan, lalu laporkan pemeriksaan yang benar-benar dijalankan.
```

## Evaluasi

[Suite evaluasi](evals/evals.json) bawaan membandingkan perilaku dengan dan tanpa skill pada tiga tugas TypeScript long-form:

- endpoint bulk preference yang atomik;
- importer event streaming dengan memory bound;
- alur pembatalan order yang transaksional.

Setiap tugas memiliki enam assertion yang menilai correctness, failure behavior, constraint transaction atau skala, regression test, scope patch, residu generatif, dan kejujuran handoff. Suite dibuat panjang agar perbedaan yang sulit terlihat pada prompt kode kecil menjadi lebih jelas.

## Struktur repositori

```text
stop-coding-slop/
├── agents/openai.yaml   # Metadata UI untuk agent
├── evals/evals.json     # Evaluasi long-form dengan/tanpa skill
├── SKILL.md             # Instruksi yang dimuat coding agent
├── README.md            # Dokumentasi bahasa Inggris
├── README_id.md         # Dokumentasi bahasa Indonesia
├── CHANGELOG.md
└── LICENSE
```

## Aturan desain

Tujuannya bukan menghasilkan kode yang hanya menghindari gaya AI yang mudah dikenali. Tujuannya adalah perubahan yang dapat dipahami, diverifikasi, di-debug, dan dikembangkan oleh maintainer karena mengikuti bukti repositori dan mengimplementasikan kontrak yang jelas.

## Author

FlowFalcon (Fathur)

## Lisensi

MIT. Lihat [LICENSE](LICENSE).
