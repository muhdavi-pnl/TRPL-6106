
# Modul 1: Pengantar Jaringan Komputer & Model OSI/TCP-IP

**A. Tujuan Pembelajaran**

1. Mahasiswa mampu menjelaskan elemen pembentuk komunikasi data dan topologi dasar jaringan komputer.
2. Mahasiswa mampu memahami dan membedakan arsitektur Model Referensi OSI (7 Layer) dan Model TCP/IP (4 Layer).
3. Mahasiswa memahami peran spesifik dari *Application, Transport, dan Network Layer* yang sangat relevan dalam pengembangan perangkat lunak.
4. Mahasiswa mampu menggunakan utilitas jaringan bawaan sistem operasi (CLI) untuk inspeksi konektivitas, pengalamatan, dan *routing* dasar berdasarkan konsep TCP/IP.

**B. Dasar Teori**

**1. Komunikasi Data dan Topologi Jaringan**
Jaringan komputer adalah kumpulan perangkat (node) yang saling terhubung untuk berbagi sumber daya dan informasi. Komunikasi data di dalam jaringan mensyaratkan adanya 4 elemen utama: *Sender* (pengirim), *Receiver* (penerima), *Transmission Medium* (media fisik seperti kabel tembaga, fiber optik, atau nirkabel), dan *Protocol* (aturan baku yang disepakati agar data bisa dipahami kedua belah pihak).
Berdasarkan tata letaknya (Topologi), jaringan umumnya menggunakan topologi *Star* (terpusat pada sebuah *switch/router*), *Tree* (hirarki), atau *Mesh* (saling terhubung penuh untuk keandalan tinggi).

**2. Model Referensi OSI (Open Systems Interconnection)**
Untuk menstandardisasi komunikasi antar berbagai vendor perangkat keras dan perangkat lunak, ISO menciptakan model OSI yang terdiri dari 7 lapisan (layer). Setiap layer memiliki fungsi spesifik dan melayani layer di atasnya:

* **Layer 7 - Application:** Antarmuka langsung bagi aplikasi perangkat lunak untuk mengakses jaringan. (Contoh protokol: HTTP untuk web, SMTP untuk email, FTP untuk transfer file). *Layer ini adalah ranah utama developer perangkat lunak.*
* **Layer 6 - Presentation:** Bertanggung jawab atas translasi format data, enkripsi, dekripsi, dan kompresi data sehingga bisa dipahami oleh Application layer.
* **Layer 5 - Session:** Membangun, menjaga, dan memutus sesi dialog komunikasi antar dua perangkat.
* **Layer 4 - Transport:** Mengatur pengiriman pesan *end-to-end*, segmentasi data, serta kendali aliran (*flow control*) dan koreksi kesalahan. Protokol utamanya adalah TCP (handal) dan UDP (cepat).
* **Layer 3 - Network:** Mengelola pengalamatan logis (IP Address) dan menentukan rute terbaik (*routing*) dari pengirim ke tujuan melewati berbagai jaringan.
* **Layer 2 - Data Link:** Mengatur pengalamatan fisik (MAC Address), deteksi *error* di tingkat perangkat keras, dan membungkus paket menjadi *frame* (misal: Ethernet, Wi-Fi).
* **Layer 1 - Physical:** Berkaitan dengan perangkat keras fisik, sinyal listrik, gelombang radio, atau cahaya untuk mentransmisikan bit-bit mentah.

**3. Model TCP/IP (Transmission Control Protocol/Internet Protocol)**
TCP/IP adalah model arsitektur praktis yang secara de facto menjadi fondasi Internet saat ini. Model ini menyederhanakan OSI menjadi 4 layer utama:

* **Application Layer** (Menggabungkan layer 5, 6, 7 dari OSI).
* **Transport Layer** (Setara layer 4 OSI).
* **Internet Layer** (Setara layer 3 OSI, di sinilah letak protokol IP bekerja).
* **Network Access Layer** (Menggabungkan layer 1 dan 2 OSI).

**4. Pengalamatan Jaringan (Network Addressing)**
Untuk dapat saling berkomunikasi, setiap perangkat membutuhkan identitas logis, yang terdiri dari:

* **IP Address:** Alamat numerik (IPv4 atau IPv6) sebagai identitas perangkat di jaringan.
* **Subnet Mask:** Menentukan bagian mana dari IP Address yang merupakan identitas jaringan (*Network ID*) dan identitas perangkat (*Host ID*).
* **Default Gateway:** Alamat router yang berfungsi sebagai pintu keluar agar perangkat lokal bisa terhubung ke jaringan lain (seperti Internet).
* **DNS (Domain Name System):** Buku telepon internet yang menerjemahkan nama domain yang mudah diingat manusia (seperti `google.com`) menjadi IP Address yang dipahami komputer.

**5. Protokol Inspeksi Jaringan (ICMP)**
Utilitas baris perintah seperti `ping` dan `traceroute` memanfaatkan *Internet Control Message Protocol* (ICMP) yang berjalan pada *Network Layer* untuk mengecek ketersediaan host (konektivitas) dan memetakan jalur lintas (hop) yang dilalui sebuah paket dari sumber ke tujuan.

**C. Alat dan Bahan**

* PC/Laptop (Windows / Linux / macOS)
* Koneksi Internet
* Terminal / Command Prompt / PowerShell

**D. Langkah-langkah Praktikum**

1. **Inspeksi Konfigurasi IP (Network Access & Internet Layer):**
* Buka terminal, ketik perintah `ipconfig /all` (untuk Windows) atau `ip a` (untuk Linux/macOS).
* Identifikasi baris yang menampilkan *IPv4 Address*, *Subnet Mask*, dan *Default Gateway*.


2. **Resolusi DNS (Application & Internet Layer):**
* Ketik perintah `nslookup ugm.ac.id` untuk melihat proses sistem menerjemahkan nama domain institusi menjadi sebuah alamat IP (*IP Address*). Catat IP yang muncul.


3. **Uji Konektivitas End-to-End (ICMP di Network Layer):**
* Lakukan `ping google.com` dan amati balasan (*reply*).
* Perhatikan nilai `time=` (*latency* dalam milidetik) dan `TTL=` (*Time to Live*). Hentikan proses (Ctrl+C jika terus berjalan).


4. **Pemetaan Rute / Routing (Network Layer):**
* Jalankan perintah `tracert github.com` (Windows) atau `traceroute github.com` (Linux/macOS).
* Amati daftar perangkat (*router/hop*) yang dilewati paket data Anda dari jaringan lokal kampus/rumah hingga mencapai server GitHub.


5. **Pemantauan Koneksi Transport Layer:**
* Jalankan `netstat -an` (Windows/Linux) untuk melihat daftar koneksi jaringan (TCP/UDP) beserta *port* lokal yang saat ini sedang aktif atau dalam status *LISTENING* oleh aplikasi di komputer Anda.



**E. Tugas/Laporan Praktikum**

1. Jelaskan perbedaan mendasar antara model OSI dan TCP/IP menggunakan bahasa Anda sendiri, serta sebutkan mengapa developer web biasanya hanya fokus pada 3 layer teratas!
2. Lakukan perintah *traceroute* ke 3 alamat domain berbeda: 1 domain lokal Indonesia (misal: kampus Anda), dan 2 domain internasional (misal: netflix.com, github.com). Bandingkan hasil jumlah *hop* (titik lompatan) dan *latency* rata-ratanya, kemudian analisis mengapa hasilnya berbeda!
3. Berikan tangkapan layar (screenshot) dari setiap langkah praktikum dan berikan penjelasan pada setiap *output* terminal yang dihasilkan.

**F. Referensi**

* Kurose, J. F., & Ross, K. W. (2021). *Computer Networking: A Top-Down Approach* (8th Ed.). Pearson.
* Forouzan, B. A. (2012). *Data Communications and Networking* (5th Ed.). McGraw-Hill.