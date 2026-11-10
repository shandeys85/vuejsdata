# vuejsdata
tutorialvue
# Panduan Alur Logika & Konsep Vue 3: Rekapitulasi Data & Diagram

Dokumen ini dirancang sebagai panduan konsep dasar (*cheat sheet* logika) untuk membangun aplikasi web berbasis **Vue 3 (Composition API)** yang mencakup struktur komponen modular, rekapitulasi statistik, serta diagram visual.

---

## 1. Arsitektur Komponen (Struktur File Proyek)

Untuk membangun aplikasi yang rapi seperti struktur proyek *real-world*, bagi aplikasi menjadi komponen-komponen kecil dengan peran masing-masing:

* **`src/App.vue` (Komponen Utama / Parent)**
  * **Peran:** Menjadi *Single Source of Truth*. Menyimpan array data utama (*state*) dan merangkai seluruh sub-komponen.
* **`src/components/DataSummary.vue` (Komponen Statistik)**
  * **Peran:** Menampilkan kartu-kartu ringkasan angka (Total, Rata-rata, Jumlah Data).
* **`src/components/DataForm.vue` (Komponen Input Data)**
  * **Peran:** Mengelola formulir untuk menambah atau mengubah entri data baru.
* **`src/components/DataChart.vue` (Komponen Grafik)**
  * **Peran:** Memvisualisasikan data dalam bentuk diagram batang/garis menggunakan Chart.js.
* **`src/components/DataTable.vue` (Komponen Tabel)**
  * **Peran:** Menampilkan daftar rincian data dalam bentuk tabel HTML.

---

## 2. Peta Fungsi & Alur Logika per Fitur

### A. Fitur 1: Manajemen Data Reaktif (*State Management*)
* **Tujuan:** Menyimpan data agar ketika terjadi perubahan (tambah/hapus), seluruh tampilan layar ter-update otomatis.
* **Fungsi / Alat yang Digunakan:**
  1. **`ref([])` atau `reactive([])`**:
     * *Kapan digunakan?* Untuk mendefinisikan array utama penampung data di `App.vue`.
     * *Alasan:* Membuat array bersifat reaktif terhadap perubahan *DOM*.
  2. **`v-model`**:
     * *Kapan digunakan?* Dipasang pada elemen `<input>`, `<select>`, atau `<textarea>` di form.
     * *Alasan:* Menghubungkan nilai *input* HTML secara dua arah (*two-way data binding*) dengan variabel JavaScript.
  3. **Array Method `.push()`**:
     * *Kapan digunakan?* Saat tombol simpan/submit ditambahkan.
     * *Alasan:* Menambahkan objek data baru hasil *input* form ke dalam array utama `ref`.
  4. **Array Method `.filter()`**:
     * *Kapan digunakan?* Saat membuat fungsi hapus data berdasarkan ID atau indeks.
     * *Alasan:* Menghasilkan array baru yang sudah membuang item tertentu tanpa merusak struktur data lain.

---

### B. Fitur 2: Rekapitulasi & Statistik Data (*Aggregation*)
* **Tujuan:** Menghitung total, nilai rata-rata, dan ringkasan angka lainnya secara otomatis saat data bertambah/berkurang.
* **Fungsi / Alat yang Digunakan:**
  1. **`computed()`**:
     * *Kapan digunakan?* Membungkus seluruh kalkulasi statistik (misal: `totalHarga`, `rataRata`, `maxVal`).
     * *Alasan:* Fitur Vue yang efisien karena hanya melakukan kalkulasi ulang secara otomatis (*auto-compute*) **apabila data dasar di dalam `ref` mengalami perubahan**.
  2. **Array Method `.reduce()`**:
     * *Kapan digunakan?* Di dalam `computed()` untuk akumulasi penjumlahan (misal: menghitung Total Pendapatan).
     * *Logika:* Mengiterasi array dari item pertama hingga akhir sambil membawa nilai akumulator (`total + current.harga`).
  3. **Array Property `.length`**:
     * *Kapan digunakan?* Untuk menghitung total transaksi/jumlah baris data yang ada.
  4. **Operasi Pembagian (`/`)**:
     * *Kapan digunakan?* Untuk menghitung Rata-Rata = `Hasil Total .reduce()` dibagi `Hasil .length`.
     * *Catatan Logika:* Berikan proteksi kondisi `if (length === 0)` untuk menghindari *error* pembagian dengan angka nol.

---

### C. Fitur 3: Integrasi Diagram & Visualisasi (*Chart.js*)
* **Tujuan:** Menampilkan bentuk grafis (bar chart/pie chart) dari array data yang dimiliki.
* **Fungsi / Alat yang Digunakan:**
  1. **Komunikasi Data Props (`defineProps`)**:
     * *Kapan digunakan?* Di dalam komponen `DataChart.vue` untuk menerima array data yang dikirimkan dari `App.vue`.
  2. **Array Method `.map()`**:
     * *Kapan digunakan?* Memisah array data kompleks menjadi dua array tunggal:
       * Array 1 (Label Sumbu X): Mengambil daftar nama/kategori (`data.map(item => item.nama)`).
       * Array 2 (Nilai Sumbu Y): Mengambil daftar angka (`data.map(item => item.nilai)`).
  3. **`computed()` untuk Data Chart**:
     * *Kapan digunakan?* Untuk membungkus struktur objek konfigurasi Chart.js (memuat `labels` dan `datasets`).
     * *Alasan:* Agar grafik ikut tergambar ulang saat data utama bertambah.
  4. **Lifecycle Hook `onMounted()`**:
     * *Kapan digunakan?* Menjalankan inisialisasi awal pustaka diagram tepat setelah elemen `<canvas>` HTML terpasang di browser.

---

## 3. Alur Komunikasi Antar Komponen (*Data Flow*)

1. **State Terpusat:** Data disimpan di `App.vue`.
2. **Parent to Child (Props):** `App.vue` mengalirkan data ke `DataSummary.vue`, `DataTable.vue`, dan `DataChart.vue` menggunakan **Props**.
3. **Child to Parent (Events / Emit):** Ketika pengguna mengisi form di `DataForm.vue` atau menekan tombol hapus di `DataTable.vue`, kirimkan instruksi balik ke `App.vue` menggunakan **`defineEmits(['tambah-data', 'hapus-data'])`**.
4. **Reaktivitas Otomatis:** Begitu `App.vue` menerima perubahan data, seluruh komponen turunan yang memanfaatkan `computed()` atau `props` akan memperbarui tampilannya secara instan.

---

## 4. Rangkuman Pertanyaan Logika Saat Ujian

Sebelum menulis baris kode saat UTS, tanyakan pada diri sendiri:
* *"Di mana data ini harus disimpan?"* $\rightarrow$ Gunakan **`ref()`** di komponen induk.
* *"Apakah saya ingin menghitung sesuatu dari data yang ada?"* $\rightarrow$ Gunakan **`computed()`** dan method array seperti **`.reduce()`**.
* *"Apakah saya ingin memotong/memilah data tertentu?"* $\rightarrow$ Gunakan **`.filter()`** atau **`.map()`**.
* *"Apakah saya butuh inputan user terhubung ke variabel?"* $\rightarrow$ Pasang **`v-model`**.
* *"Apakah saya ingin mengambil label & nilai untuk grafik?"* $\rightarrow$ Gunakan **`.map()`** di dalam objek `computed` milik Chart.
