# Modul 3: Lapisan Aplikasi & Protokol Web (HTTP/1.1, HTTP/2, dan HTTP/3)

**A. Tujuan Pembelajaran**

1. Mahasiswa memahami siklus *Request-Response* pada arsitektur *Client-Server*.
2. Mahasiswa mampu mengidentifikasi dan membedakan komponen HTTP (*Methods, Status Codes, Headers, Body*).
3. Mahasiswa memahami evolusi protokol web (HTTP/1.1, HTTP/2, dan HTTP/3) serta kaitannya dengan Layer Transport (TCP vs UDP).
4. Mahasiswa mampu melakukan inspeksi dan manipulasi lalu lintas HTTP/API menggunakan *Browser Developer Tools* dan utilitas baris perintah (`curl`).

**B. Dasar Teori**
**1. Lapisan Aplikasi (Application Layer)**
Lapisan teratas pada OSI maupun TCP/IP ini adalah antarmuka langsung antara aplikasi perangkat lunak dengan jaringan. Tidak seperti lapisan di bawahnya yang mengurus pengiriman paket mentah, Lapisan Aplikasi berurusan dengan data berformat (seperti dokumen HTML, file JSON, atau gambar).

**2. Hypertext Transfer Protocol (HTTP)**
HTTP adalah protokol utama penyusun World Wide Web. HTTP bersifat *stateless*, artinya setiap permintaan (*request*) bersifat independen dan server tidak menyimpan memori dari permintaan sebelumnya tanpa bantuan fitur tambahan (seperti *Cookies* atau *Token*).
Siklus utama HTTP terdiri dari:

* **HTTP Methods (Kata Kerja):** Mendefinisikan aksi yang diinginkan klien.
* `GET` (mengambil data), `POST` (mengirim data baru), `PUT/PATCH` (memperbarui data), `DELETE` (menghapus data).


* **HTTP Status Codes:** Tiga digit angka dari server yang menandakan hasil *request*.
* `1xx` (Informasional), `2xx` (Sukses, misal: 200 OK), `3xx` (Redirection/Pengalihan), `4xx` (Client Error, misal: 404 Not Found, 401 Unauthorized), `5xx` (Server Error, misal: 500 Internal Server Error).


* **HTTP Headers:** Metadata yang dikirimkan bersama *request/response* (misal: `Content-Type: application/json` atau `User-Agent`).

**3. Evolusi HTTP**

* **HTTP/1.1:** Berjalan di atas TCP. Pesan berbentuk teks (*plaintext*). Memiliki kelemahan *Head-of-Line (HoL) Blocking*, di mana satu file lambat bisa menunda pengiriman file lainnya.
* **HTTP/2:** Berjalan di atas TCP. Menggunakan format biner dan mendukung *Multiplexing* (bisa mengirim banyak file gambar/teks secara bersamaan dalam satu koneksi TCP).
* **HTTP/3 (QUIC):** Berjalan di atas **UDP**. Dirancang untuk mengatasi masalah keandalan TCP pada jaringan modern (seperti saat berpindah dari Wi-Fi ke seluler). Tetap aman, cepat, dan lebih ringan.

**4. Domain Name System (DNS)**
Sebelum browser bisa membuat HTTP *request* ke server, browser harus tahu alamat IP server tersebut. DNS berfungsi sebagai "buku telepon" yang menerjemahkan nama domain (seperti `ugm.ac.id`) menjadi alamat IP logis (seperti `175.45.187.xx`).

**C. Alat dan Bahan**

* PC/Laptop (Windows / Linux / macOS)
* Koneksi Internet
* Web Browser modern (Google Chrome / Mozilla Firefox / Microsoft Edge)
* Terminal / Command Prompt / PowerShell
* Utilitas `curl` (Bawaan di hampir semua sistem operasi modern)

**D. Langkah-langkah Praktikum**

1. **Inspeksi HTTP melalui Browser DevTools:**
* Buka web browser. Tekan `F12` atau klik kanan > *Inspect* > masuk ke tab **Network**.
* Buka URL `[http://example.com](http://example.com)` atau `[https://ugm.ac.id](https://ugm.ac.id)`.
* Klik pada dokumen HTML pertama yang muncul di daftar Network. Amati panel sebelah kanan: perhatikan *Request URL*, *Request Method*, dan *Status Code*.
* Buka bagian **Headers** dan amati informasi *User-Agent* (yang mendeskripsikan browser Anda) serta *Content-Type*.


2. **Melihat Evolusi Protokol (HTTP/2 & HTTP/3):**
* Masih di tab Network, klik kanan pada *header* kolom daftar tabel jaringan, lalu centang opsi **Protocol**.
* Buka situs besar seperti `[https://youtube.com](https://youtube.com)` atau `[https://google.com](https://google.com)`.
* Amati pada kolom *Protocol*. Anda akan melihat nilai `h2` (HTTP/2) atau `h3` (HTTP/3 berbasis UDP).


3. **Membuat HTTP GET Request menggunakan `curl`:**
* Buka terminal, ketik perintah berikut: `curl -v [https://jsonplaceholder.typicode.com/users/1](https://jsonplaceholder.typicode.com/users/1)`
* Flag `-v` (verbose) akan menampilkan seluruh proses: mulai dari resolusi DNS, *TCP Handshake*, *TLS Handshake* (karena menggunakan HTTPS), hingga *Request Header* yang dikirim (ditandai `>`) dan *Response Header* dari server (ditandai `<`), lalu diakhiri dengan *Body* berupa data JSON.


4. **Membuat HTTP POST Request menggunakan `curl`:**
* Ketik perintah berikut untuk menyimulasikan pengiriman data (*Submit Form/API*):
`curl -v -X POST [https://jsonplaceholder.typicode.com/posts](https://jsonplaceholder.typicode.com/posts) -H "Content-Type: application/json" -d "{\"title\":\"TRPL Network\",\"body\":\"Modul 3\",\"userId\":1}"`
* Amati status code balasan (biasanya `201 Created` untuk request pembuatan data berhasil).



**E. Tugas/Laporan Praktikum**

1. **Analisis Status Code:** Menggunakan perintah `curl`, buatlah *request* sengaja ke URL (endpoint) yang tidak ada pada domain `[https://jsonplaceholder.typicode.com](https://jsonplaceholder.typicode.com)` (misalnya `/users/999` atau semacamnya). Lampirkan *screenshot* dan jelaskan HTTP Status Code apa yang dikembalikan oleh server dan apa artinya secara konseptual!
2. **Pemahaman Header (REST API):** Saat kita mengirimkan request dengan perintah `-H "Content-Type: application/json"`, apa fungsi utama dari pengiriman header tersebut? Jelaskan apa yang mungkin terjadi di sisi *Backend Server* jika kita salah menyertakan header ini (misal menggunakan `text/html`)!
3. **Studi Kasus Arsitektur (Menghubungkan Modul 2 & 3):** Anda telah melihat bahwa situs-situs modern dari Google atau Meta sudah banyak yang menggunakan protokol `h3` (HTTP/3). Berdasarkan pengetahuan Anda di Modul 2, jelaskan secara singkat mengapa perusahaan raksasa internet justru beralih dari protokol TCP (yang handal) ke UDP (melalui QUIC) untuk melayani web!

**F. Referensi**

* MDN Web Docs (Mozilla). *An overview of HTTP* (developer.mozilla.org/en-US/docs/Web/HTTP/Overview).
* RFC 9114: *HTTP/3* (datatracker.ietf.org).
