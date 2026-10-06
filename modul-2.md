### Modul 2: Layer Transport: TCP vs UDP

**A. Tujuan Pembelajaran**

1. Mahasiswa memahami peran dan fungsi Layer Transport dalam menjembatani komunikasi antar aplikasi (*application-to-application communication*).
2. Mahasiswa mampu mengidentifikasi karakteristik, cara kerja, serta kelebihan dan kekurangan protokol TCP dan UDP.
3. Mahasiswa mampu menganalisis siklus *3-Way Handshake* pada TCP.
4. Mahasiswa mampu menggunakan *network analyzer* (seperti Wireshark) dan utilitas baris perintah (seperti Netcat) untuk menangkap, membedakan, dan menguji trafik lalu lintas TCP dan UDP.
5. Mahasiswa mampu menentukan pemilihan protokol TCP atau UDP yang tepat berdasarkan kebutuhan arsitektur perangkat lunak (contoh: *streaming* vs *database transaksi*).

**B. Dasar Teori**
**1. Peran Layer Transport dan Konsep Port**
Layer Transport (Layer 4 pada OSI atau Layer 3 pada TCP/IP) bertanggung jawab untuk pengiriman data logis antar proses/aplikasi yang berjalan pada host yang berbeda. Agar sistem operasi tahu data jaringan harus dikirim ke aplikasi perangkat lunak yang mana (misal: membedakan data untuk *browser* web atau aplikasi *email*), Layer Transport menggunakan pengalamatan yang disebut **Port Number** (berkisar dari 0 hingga 65535).
Kombinasi antara IP Address dan Port Number disebut sebagai *Socket*.

**2. Transmission Control Protocol (TCP)**
TCP adalah protokol yang berorientasi pada koneksi (*connection-oriented*) dan andal (*reliable*).

* **Keandalan:** TCP menjamin data sampai ke tujuan secara utuh dan berurutan. Jika ada paket yang hilang (Packet Loss), TCP akan memintanya kembali (*retransmission*).
* **3-Way Handshake:** Sebelum mengirim data aplikasi, TCP harus membangun koneksi fisik logis melalui tiga tahap:
1. **SYN:** Klien meminta koneksi ke server.
2. **SYN-ACK:** Server merespons dan menyetujui permintaan.
3. **ACK:** Klien mengonfirmasi persetujuan, lalu transmisi data bisa dimulai.


* **Overhead:** Karena mekanisme keandalan, urutan, dan kontrol kemacetan (*congestion control*), paket TCP lebih berat dan membutuhkan waktu lebih lama untuk dikirim.
* **Penggunaan (Use-Cases):** Aplikasi yang tidak menoleransi kehilangan data, seperti HTTP/HTTPS (Web), SMTP (Email), FTP (Transfer File), dan akses *Database*.

**3. User Datagram Protocol (UDP)**
UDP adalah protokol tanpa koneksi (*connectionless*) dan merupakan *best-effort delivery* (mengirim secepat mungkin tanpa jaminan).

* **Tidak Andal tapi Cepat:** UDP tidak melakukan *handshake*, tidak mengecek apakah paket hilang, dan tidak menyusun ulang paket yang datang secara acak.
* **Low Latency & Low Overhead:** Karena tidak ada mekanisme validasi dan *header* paketnya sangat kecil (hanya 8 byte dibanding TCP yang minimal 20 byte), pengiriman data melalui UDP sangat cepat.
* **Penggunaan (Use-Cases):** Aplikasi *real-time* yang mengutamakan kecepatan dan bisa menoleransi sedikit kehilangan data, seperti *Video Streaming*, *Online Gaming*, *Voice over IP* (VoIP/Zoom), dan DNS (*Domain Name System*).

**C. Alat dan Bahan**

* PC/Laptop (Windows / Linux / macOS)
* Koneksi Jaringan Lokal/Internet
* Aplikasi *Wireshark* (Bisa diunduh gratis di wireshark.org)
* Utilitas *Netcat* (Bawaan di Linux/macOS, atau menggunakan `ncat` untuk Windows)

**D. Langkah-langkah Praktikum**

1. **Persiapan Wireshark:**
* Buka aplikasi Wireshark dan pilih antarmuka jaringan (*network interface*) yang aktif (misal: Wi-Fi atau Ethernet).
* Klik dua kali untuk mulai merekam/menangkap lalu lintas jaringan (*capture packets*).


2. **Menangkap Trafik TCP & 3-Way Handshake:**
* Pada kolom filter di Wireshark, ketik `tcp` dan tekan Enter.
* Buka peramban web (*browser*) dan akses sebuah situs web non-HTTPS sederhana (jika ada), atau biarkan background aplikasi bekerja.
* Cari paket TCP yang memiliki label flag `[SYN]`, diikuti oleh paket balasan `[SYN, ACK]`, dan ditutup dengan `[ACK]`. (Anda bisa menghentikan sementara penangkapan paket agar lebih mudah mencari).


3. **Simulasi Komunikasi TCP dengan Netcat (Terminal):**
* Buka dua jendela Terminal/Command Prompt.
* Terminal 1 (Sebagai Server): Ketik `nc -l 8080` (Linux/Mac) atau `ncat -l -p 8080` (Windows) untuk membuka port TCP 8080.
* Terminal 2 (Sebagai Klien): Ketik `nc 127.0.0.1 8080` atau `ncat 127.0.0.1 8080`.
* Ketik teks di Terminal 2 lalu tekan Enter, amati bahwa pesan akan muncul secara *real-time* di Terminal 1. (Koneksi persisten terjalin). Matikan koneksi dengan menekan `Ctrl+C`.


4. **Menangkap Trafik UDP & DNS Query:**
* Mulai ulang tangkapan paket di Wireshark. Pada kolom filter, ketik `udp.port == 53`. (Port 53 adalah port standar DNS).
* Buka terminal, ketik perintah `nslookup kampus.edu` (ganti dengan domain kampus).
* Kembali ke Wireshark, perhatikan bahwa hanya ada paket *query* (permintaan) dan *response* (balasan) tanpa didahului oleh proses *handshake*.


5. **Simulasi Komunikasi UDP dengan Netcat:**
* Buka dua jendela Terminal.
* Terminal 1 (Sebagai Server UDP): Tambahkan flag `-u` (untuk UDP). Ketik `nc -u -l 8080` atau `ncat -u -l -p 8080`.
* Terminal 2 (Sebagai Klien UDP): Ketik `nc -u 127.0.0.1 8080` atau `ncat -u 127.0.0.1 8080`.
* Kirim pesan antar terminal. Coba matikan paksa Terminal 1, dan amati bahwa jika Anda mengetik di Terminal 2, aplikasi tidak akan menghasilkan pesan *error* karena tidak ada koneksi yang dijaga (*connectionless*).



**E. Tugas/Laporan Praktikum**

1. **Analisis Paket:** Lampirkan tangkapan layar (screenshot) dari Wireshark yang menunjukkan proses **3-Way Handshake TCP**! Berikan lingkaran atau tanda pada flag `[SYN]`, `[SYN, ACK]`, dan `[ACK]`, serta catat *port asal (source)* dan *port tujuan (destination)*-nya.
2. **Analisis Perbandingan:** Berdasarkan praktikum menggunakan perintah `Netcat` (TCP vs UDP), jelaskan fenomena apa yang terjadi jika koneksi server tiba-tiba terputus pada TCP dibandingkan pada UDP!
3. **Studi Kasus Rekayasa Perangkat Lunak:** Sebagai seorang *Software Engineer*, Anda diminta merancang arsitektur jaringan untuk dua fitur pada sebuah aplikasi:
* **Fitur A:** Sistem unggah (upload) dokumen kontrak legal dari sisi *client* ke server *cloud*.
* **Fitur B:** Sistem transmisi video CCTV *live-streaming* ke *dashboard browser*.
Berdasarkan karakteristik protokol Transport, tentukan apakah Anda akan menggunakan TCP atau UDP untuk masing-masing fitur tersebut! Berikan alasan teknis yang mendetail!



**F. Referensi**

* Kurose, J. F., & Ross, K. W. (2021). *Computer Networking: A Top-Down Approach* (8th Ed.). Pearson.
* Fall, K. R., & Stevens, W. R. (2011). *TCP/IP Illustrated, Volume 1: The Protocols* (2nd Ed.). Addison-Wesley.
* Dokumentasi Resmi Wireshark (wireshark.org/docs)
