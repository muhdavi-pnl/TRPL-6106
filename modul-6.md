# Modul 6: Komunikasi Real-Time (WebSocket & gRPC)

**A. Tujuan Pembelajaran**

1. Mahasiswa memahami keterbatasan protokol HTTP tradisional untuk kebutuhan aplikasi *real-time* (masalah *overhead* pada *Polling* dan *Long-Polling*).
2. Mahasiswa memahami cara kerja protokol WebSocket dalam menciptakan komunikasi dua arah penuh (*full-duplex*) di atas satu koneksi TCP.
3. Mahasiswa mampu mengimplementasikan *WebSocket Server* dan mengonsumsinya melalui antarmuka *Browser* (Client).
4. Mahasiswa memahami paradigma *Remote Procedure Call* (RPC) dan keunggulan gRPC.
5. Mahasiswa memahami perbedaan pengiriman data berbasis teks (JSON) pada REST dengan format biner (*Protocol Buffers/Protobuf*) pada gRPC.

**B. Dasar Teori**
**1. Keterbatasan HTTP untuk Real-Time**
HTTP dirancang secara *stateless* (klien meminta, server menjawab, koneksi ditutup). Jika kita ingin membuat aplikasi *live-chat* menggunakan HTTP REST, klien harus terus-menerus me-*request* ke server setiap detik ("Apakah ada pesan baru?"). Teknik ini disebut **Polling**. Polling sangat memboroskan *bandwidth* dan sumber daya server karena klien harus terus mengirimkan *HTTP Header* yang panjang berulang kali meskipun tidak ada data baru.

**2. Protokol WebSocket (ws:// dan wss://)**
WebSocket (RFC 6455) menyelesaikan masalah di atas dengan menyediakan koneksi **Full-Duplex** (dua arah simultan) dan **Persistent** (koneksi ditahan agar terus terbuka).

* **Proses Handshake:** WebSocket dimulai dari HTTP biasa. Klien mengirimkan *HTTP request* dengan *header* `Upgrade: websocket`. Jika server mendukung, koneksi HTTP tersebut "ditingkatkan" (di-*upgrade*) menjadi koneksi WebSocket, dan *overhead header* HTTP dihilangkan. Server dan klien kini bisa saling melempar data (teks/biner) kapan saja secara instan.

**3. gRPC dan Protocol Buffers**
gRPC adalah *framework* komunikasi antar-layanan (berbasis RPC) berkinerja tinggi *open-source* buatan Google. Berbeda dengan REST API yang mengekspos URL (kata benda), RPC berfokus pada pemanggilan fungsi (kata kerja) seolah-olah fungsi tersebut berada di komputer lokal.

* **Protocol Buffers (Protobuf):** Jika REST umumnya menggunakan JSON (berbasis teks, besar, dan lambat di-parsing), gRPC menggunakan Protobuf. Protobuf adalah format biner yang dikompresi sangat padat dan memiliki aturan tipe data yang ketat (*strictly-typed*).
* **HTTP/2:** gRPC secara *native* berjalan di atas HTTP/2, memungkinkannya melakukan proses *multiplexing* dan *streaming* data dengan latensi sangat rendah. Arsitektur ini adalah standar emas komunikasi internal antar-Mikroservis (*Backend-to-Backend*).

**C. Alat dan Bahan**

* PC/Laptop (Windows / Linux / macOS)
* Python 3.x (Untuk Server WebSocket dan gRPC)
* Web Browser (Chrome/Firefox untuk Klien WebSocket)
* Library Python: `websockets`, `asyncio`, `grpcio`, `grpcio-tools`

**D. Langkah-langkah Praktikum**
**Bagian 1: Implementasi WebSocket**

1. **Instalasi Library:** Buka terminal, jalankan `pip install websockets`.
2. **Membuat WebSocket Server (`ws_server.py`):**
```python
import asyncio
import websockets

async def echo(websocket, path):
    print("Klien baru terhubung!")
    async for message in websocket:
        print(f"Pesan diterima: {message}")
        # Kirim balasan instan ke klien
        await websocket.send(f"Server membalas: {message}")

# Berjalan di localhost port 8765
start_server = websockets.serve(echo, "localhost", 8765)
print("WebSocket Server berjalan di ws://localhost:8765")

asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()

```


3. **Membuat WebSocket Client (`index.html`):**
Buat file HTML sederhana untuk berinteraksi langsung melalui browser tanpa *reload*.
```html
<!DOCTYPE html>
<html>
<body>
    <h2>WebSocket Client TRPL</h2>
    <input type="text" id="pesan" placeholder="Ketik pesan...">
    <button onclick="kirimPesan()">Kirim</button>
    <ul id="log"></ul>

    <script>
        // Membuka koneksi ke WebSocket Server
        const ws = new WebSocket("ws://localhost:8765");

        ws.onopen = () => console.log("Terhubung ke Server!");

        // Mendengarkan pesan masuk dari server
        ws.onmessage = (event) => {
            let li = document.createElement("li");
            li.textContent = event.data;
            document.getElementById("log").appendChild(li);
        };

        function kirimPesan() {
            let input = document.getElementById("pesan");
            ws.send(input.value); // Kirim tanpa me-reload halaman!
            input.value = "";
        }
    </script>
</body>
</html>

```


4. **Uji Coba:** Jalankan `python ws_server.py` di terminal. Kemudian klik ganda file `index.html` agar terbuka di *browser*. Ketik pesan dan klik Kirim. Amati hasilnya di terminal server dan layar *browser*.

**Bagian 2: Pengenalan Protocol Buffers (gRPC)**

1. Buat file definisi struktur data bernama `pesan.proto`:
```protobuf
syntax = "proto3";

// Format ini jauh lebih ketat dari JSON
message ProfilMahasiswa {
  int32 id = 1;
  string nama = 2;
  bool status_aktif = 3;
}

```


2. Buka terminal, kompilasi *file proto* tersebut menjadi kode Python agar siap digunakan (Simulasi *strict-typing* jaringan):
`python -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. pesan.proto`
3. Amati bahwa sistem secara otomatis men-*generate* file Python (`pesan_pb2.py`) yang berisi kelas dan metode untuk proses komunikasi data secara biner.

**E. Tugas/Laporan Praktikum**

1. **Analisis HTTP Upgrade:** Pada praktikum Bagian 1, buka tab *Network* di *Developer Tools browser* (F12), dan klik koneksi WebSocket-nya. Lampirkan *screenshot* dari **Request Headers** dan cari baris `Connection: Upgrade` dan `Upgrade: websocket`. Jelaskan makna dari pertukaran *header* tersebut berdasarkan Dasar Teori!
2. **Pengembangan WebSocket (Tugas Logika):** Kode `ws_server.py` di atas bersifat *Echo* (hanya membalas ke klien yang mengirim). Modifikasilah kode *server* tersebut agar menjadi sebuah **Broadcast Chat Room** (jika Klien A mengirim pesan, maka Klien A, Klien B, dan Klien C yang sedang terhubung semuanya akan menerima pesan tersebut).
*(Petunjuk: Anda perlu menyimpan daftar koneksi/klien yang aktif ke dalam sebuah struktur data tipe Set `set()` pada Python).*
3. **Perbandingan Arsitektur:** Sebagai *Software Engineer*, jika Anda diminta merancang sistem (1) **Aplikasi Ojek Online (Live Tracking Maps)** dan (2) **Sistem Validasi Login antar layanan Microservice**, tentukan mana yang sebaiknya menggunakan gRPC dan mana yang menggunakan WebSocket, lalu berikan alasan rasionalisasinya!

**F. Referensi**

* MDN Web Docs: *The WebSocket API* (developer.mozilla.org)
* gRPC Documentation: *Introduction to gRPC* (grpc.io/docs/what-is-grpc/core-concepts/)
* RFC 6455: *The WebSocket Protocol*

---

Modul 6 ini menjembatani gap (kesenjangan) antara konsep jaringan konvensional dengan *stack* teknologi yang benar-benar digunakan dalam industri pengembangan perangkat lunak terdistribusi saat ini.
