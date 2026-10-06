# Modul 5: Arsitektur API dan Komunikasi Berbasis Web (REST, Webhooks, & MQTT)

**A. Tujuan Pembelajaran**

1. Mahasiswa memahami konsep dasar API (*Application Programming Interface*) sebagai jembatan komunikasi antar aplikasi.
2. Mahasiswa mampu mendesain dan memetakan arsitektur RESTful API menggunakan metode HTTP untuk operasi CRUD (*Create, Read, Update, Delete*).
3. Mahasiswa memahami perbedaan paradigma komunikasi jaringan *Synchronous* (REST) dan *Asynchronous/Event-Driven* (Webhooks & MQTT).
4. Mahasiswa mampu melakukan pengujian (konsumsi) API menggunakan *API Client Tools* seperti Postman atau Insomnia.

**B. Dasar Teori**
**1. Apa itu API?**
API adalah sekumpulan aturan yang memungkinkan satu aplikasi perangkat lunak berbicara dengan aplikasi lainnya. Dalam konteks jaringan (*Web API*), API mengatur bagaimana sebuah klien (*frontend/mobile app*) meminta data ke server (*backend*) dan format balasan apa yang akan diterima (biasanya JSON atau XML).

**2. Arsitektur RESTful API**
REST (*Representational State Transfer*) adalah gaya arsitektur standar untuk merancang web API. REST menggunakan standar protokol HTTP (yang sudah dipelajari di Modul 3) dan memetakan operasi *database* CRUD ke dalam **HTTP Methods**:

* `POST` -> **C**reate (Membuat data baru)
* `GET` -> **R**ead (Membaca/mengambil data)
* `PUT / PATCH` -> **U**pdate (Memperbarui data)
* `DELETE` -> **D**elete (Menghapus data)
Sebuah REST API yang baik menggunakan konsep *Resource-based URL*, di mana URL merepresentasikan kata benda (contoh: `/api/users/123`), bukan kata kerja (hindari `/api/getUser/123`). REST bersifat *Synchronous* (klien mengirim *request* dan menunggu *response*).

**3. Webhooks (Komunikasi Push/Callback)**
Jika REST API mengharuskan klien terus-menerus bertanya kepada server "Apakah ada data baru?" (*Polling* - yang boros bandwidth/jaringan), Webhooks membalik logika tersebut. Webhooks adalah *HTTP POST request* otomatis yang dikirimkan oleh sebuah aplikasi (sumber) ke aplikasi lain (tujuan) ketika sebuah kejadian (*event*) tertentu terjadi. Ini sangat menghemat beban trafik jaringan.

**4. MQTT (Message Queuing Telemetry Transport)**
Berbeda dengan HTTP yang *Request-Response*, MQTT menggunakan arsitektur **Publish/Subscribe**.
Terdapat sebuah *Broker* (server pusat). *Publisher* (pengirim) mengirim pesan ke sebuah *Topic* tertentu di Broker. *Subscriber* (penerima) yang berlangganan pada *Topic* tersebut akan otomatis menerima pesan tersebut secara *real-time*. MQTT sangat ringan dan awalnya dirancang untuk jaringan dengan *bandwidth* rendah atau tidak stabil (sangat populer di sistem IoT dan arsitektur Mikroservis/Event-Driven).

**C. Alat dan Bahan**

* PC/Laptop
* Koneksi Internet
* Aplikasi API Client: Postman (postman.com) atau Insomnia
* Situs simulasi Webhook: `webhook.site`
* Situs klien web MQTT: `mqttx.app/web-client` (atau menggunakan aplikasi MQTTX)

**D. Langkah-langkah Praktikum**

1. **Pengujian REST API (CRUD) menggunakan Postman:**
* Buka aplikasi Postman. Buat *request* baru.
* **GET (Read):** Set *method* ke `GET`, masukkan URL `[https://reqres.in/api/users?page=2](https://reqres.in/api/users?page=2)`, lalu klik **Send**. Amati struktur JSON yang dikembalikan di panel *response*.
* **POST (Create):** Buat request baru, set *method* ke `POST`, URL `[https://reqres.in/api/users](https://reqres.in/api/users)`. Buka tab **Body**, pilih **raw** dan format **JSON**. Masukkan data `{"name": "morpheus", "job": "leader"}`. Klik **Send** dan amati status code `201 Created`.


2. **Simulasi Webhook (Komunikasi Berbasis Event):**
* Buka browser, kunjungi `[https://webhook.site/](https://webhook.site/)`. Situs ini akan otomatis men-generate sebuah *URL unik* untuk Anda. Biarkan tab ini terbuka (berfungsi sebagai *Listener/Server*).
* Buka Postman, set method ke `POST`, masukkan URL unik dari webhook.site tersebut.
* Kirim sembarang data JSON dari Postman. Kembali ke tab browser `webhook.site`, Anda akan melihat data langsung masuk secara instan tanpa perlu merefresh halaman (Push mekanis).


3. **Simulasi Pub/Sub menggunakan MQTT:**
* Buka `[https://mqttx.app/web-client/](https://mqttx.app/web-client/)` di dua tab browser yang berbeda (Tab 1 sebagai Subscriber, Tab 2 sebagai Publisher).
* Pada kedua Tab, buat koneksi (New Connection) menggunakan *Public Broker* default yang disediakan (misal: `broker.emqx.io`).
* Di **Tab 1**, buat *Subscription* baru dengan nama topik `trpl/network/kampus/suhu`.
* Di **Tab 2**, pada bagian *Publish*, masukkan topik yang sama: `trpl/network/kampus/suhu`, isi *payload* (pesan) berupa `{"suhu": 28, "status": "normal"}`, lalu tekan tombol kirim (simbol pesawat kertas).
* Lihat kembali **Tab 1**, pesan akan langsung diterima seketika berkat perantara broker tanpa mereka saling tahu IP address masing-masing.



**E. Tugas/Laporan Praktikum**

1. **Desain REST API:** Anda diminta membuat API untuk sebuah sistem perpustakaan kampus. Tuliskan desain URL (*Endpoint*) beserta HTTP Method yang tepat untuk 4 operasi berikut:
* Melihat daftar seluruh buku.
* Melihat detail satu buku berdasarkan ID.
* Menambahkan buku baru.
* Menghapus buku berdasarkan ID.


2. **Studi Kasus Arsitektur Jaringan (Webhooks vs Polling):** Saat Anda membangun aplikasi *e-commerce* dan mengintegrasikan *Payment Gateway* (seperti Midtrans/Xendit), mengapa menggunakan **Webhook** dari sistem *Payment Gateway* ke server Anda jauh lebih baik (dari segi beban jaringan dan efisiensi *server*) dibandingkan server Anda melakukan *HTTP GET* (Polling) setiap 10 detik untuk mengecek status pembayaran? Jelaskan!
3. **Analisis Protokol:** Apa keuntungan utama memisahkan pengirim (*Publisher*) dan penerima (*Subscriber*) dengan sebuah *Broker* pada protokol MQTT dibandingkan arsitektur *Client-Server* tradisional?

**F. Referensi**

* Fielding, R. T. (2000). *Architectural Styles and the Design of Network-based Software Architectures* (Disertasi Ph.D., UC Irvine).
* Dokumentasi Postman (learning.postman.com)
* Spesifikasi MQTT (mqtt.org)
