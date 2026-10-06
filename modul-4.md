# Modul 4: *Network Programming* & Sockets Dasar

**A. Tujuan Pembelajaran**

1. Mahasiswa memahami konsep *Network Programming* dan peran *Socket API* sebagai antarmuka standar antara aplikasi dan sistem operasi untuk komunikasi jaringan.
2. Mahasiswa mampu menjelaskan alur siklus hidup komunikasi berbasis TCP (*Connection-Oriented*) dari sisi *Server* dan *Client*.
3. Mahasiswa mampu mengimplementasikan program *TCP Server* dan *TCP Client* sederhana menggunakan bahasa pemrograman (misalnya: Python).
4. Mahasiswa mampu menganalisis interaksi pertukaran data (byte) tingkat rendah antar dua aplikasi yang berjalan di atas jaringan.

**B. Dasar Teori**
**1. Pengantar Network Programming dan Socket**
Jika Modul 2 membahas *bagaimana* paket data (TCP/UDP) berjalan di jaringan, *Network Programming* membahas *bagaimana cara kita menulis kode* agar aplikasi bisa mengirim dan menerima paket tersebut.
Untuk melakukan ini, sistem operasi menyediakan antarmuka pemrograman yang disebut **Socket API**. Secara sederhana, Socket adalah "pintu/colokan" di mana aplikasi menulis dan membaca data jaringan.
Sebuah Socket dibentuk dari kombinasi: **Alamat IP (Host) + Port (Layanan)**.

**2. Tipe Socket Utama**

* **Stream Sockets (TCP/SOCK_STREAM):** Digunakan untuk komunikasi TCP. Komunikasi ini handal, memastikan data tiba secara berurutan dan utuh tanpa duplikasi. Ini adalah tipe yang paling sering digunakan *developer* karena keandalannya.
* **Datagram Sockets (UDP/SOCK_DGRAM):** Digunakan untuk komunikasi UDP. Lebih sederhana dan cepat karena tidak ada proses pembuatan koneksi (tanpa *handshake*), tetapi data bisa hilang atau tiba tidak berurutan.

**3. Siklus Hidup Socket TCP (Client-Server Architecture)**
Dalam arsitektur *Client-Server*, program terbagi menjadi dua peran dengan siklus Socket yang berbeda:

* **Sisi Server (Pasif menunggu):**
1. `socket()`: Membuat instance socket baru.
2. `bind()`: Mengikat socket ke sebuah Alamat IP dan Port tertentu di mesin server.
3. `listen()`: Menandai socket sebagai mode pasif yang siap mendengarkan koneksi masuk.
4. `accept()`: Memblokir/menghentikan sementara eksekusi kode (secara asinkron) hingga ada permintaan koneksi dari klien. Setelah terhubung, ia akan membuat socket *baru* khusus untuk melayani klien tersebut.
5. `recv()` / `send()`: Menerima atau mengirim data.
6. `close()`: Menutup koneksi dan melepaskan resource.


* **Sisi Client (Aktif memulai):**
1. `socket()`: Membuat instance socket baru.
2. `connect()`: Memulai proses negosiasi koneksi (*3-way handshake*) ke IP dan Port tujuan (Server).
3. `send()` / `recv()`: Mengirim atau menerima data.
4. `close()`: Menutup koneksi.



**C. Alat dan Bahan**

* PC/Laptop (Windows / Linux / macOS)
* Python 3.x terinstal (Python sangat ideal karena memiliki modul `socket` bawaan yang mudah dipahami sintaksnya).
* Teks Editor / IDE (misal: Visual Studio Code, Sublime Text, atau Notepad).
* Terminal / Command Prompt / PowerShell.

**D. Langkah-langkah Praktikum**

1. **Persiapan Lingkungan:** Buat sebuah folder baru (misal: `praktikum_jaringan`). Di dalamnya, buat dua file kosong: `server.py` dan `client.py`.
2. **Menulis Kode Server (`server.py`):** Buka file `server.py` dan salin kode dasar berikut, lalu pelajari komentar pada setiap barisnya:
```python
import socket

HOST = '127.0.0.1'  # IP Localhost (loopback)
PORT = 65432        # Port non-privilege (di atas 1023)

# Membuat objek socket TCP (AF_INET = IPv4, SOCK_STREAM = TCP)
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((HOST, PORT)) # Mengikat ke IP dan Port
    s.listen()           # Mulai mendengarkan
    print(f"Server berjalan... menunggu koneksi di {HOST}:{PORT}")

    # Menerima koneksi masuk (conn = socket baru untuk klien ini, addr = IP klien)
    conn, addr = s.accept()
    with conn:
        print(f"Terkoneksi oleh {addr}")
        while True:
            # Menerima data maksimal 1024 bytes (Data yang diterima selalu dalam bentuk byte)
            data = conn.recv(1024) 
            if not data: # Jika tidak ada data (koneksi ditutup), keluar loop
                break
            print(f"Pesan diterima dari klien: {data.decode()}")
            # Mengirim kembali data ke klien (Echo)
            conn.sendall(data) 

```


3. **Menulis Kode Client (`client.py`):** Buka file `client.py` dan salin kode berikut:
```python
import socket

HOST = '127.0.0.1'  # IP tujuan (harus sama dengan server)
PORT = 65432        # Port tujuan (harus sama dengan server)

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect((HOST, PORT)) # Memulai negosiasi 3-way handshake
    s.sendall(b'Halo Server, ini adalah pesan percobaan dari Klien TRPL') # b'...' mengubah string menjadi byte

    # Menerima balasan
    data = s.recv(1024)

print(f"Menerima balasan dari server: {repr(data)}") # repr() agar format raw byte terlihat

```


4. **Menjalankan dan Menguji:**
* Buka dua jendela Terminal.
* Pada Terminal 1, jalankan server: `python server.py`. (Terminal akan seolah 'hang' karena sedang tertahan di fungsi `accept()`).
* Pada Terminal 2, jalankan klien: `python client.py`.
* Amati respon cepat yang muncul di kedua terminal (Server menerima koneksi dan pesan, Klien menerima pesan balasan).



**E. Tugas/Laporan Praktikum**

1. **Analisis Kode:** Pada kode klien, mengapa pesan dikirim dalam format byte (menggunakan notasi `b'Halo Server...'`) alih-alih tipe data *String* biasa? Apa peran fungsi `encode()` dan `decode()` dalam *Network Programming*?
2. **Pengembangan Aplikasi (Tugas Koding):** Kembangkan *source code* `server.py` dan `client.py` di atas menjadi aplikasi *terminal chat* dua arah secara bergantian (menggunakan perulangan / *loop*). Aplikasi tidak berhenti hingga salah satu pengguna mengetikkan pesan "exit".
*(Petunjuk: Anda bisa menggunakan fungsi bawaan `input()` pada Python untuk mengambil input dari keyboard).* Lampirkan *source code* baru beserta tangkapan layar (screenshot) saat kedua pihak saling berkirim pesan!
3. **Studi Kasus Konkurensi:** Kode server di atas hanya bisa melayani *satu* klien pada satu waktu. Jika klien kedua mencoba terhubung sementara klien pertama masih tersambung, klien kedua harus mengantri. Dalam pengembangan aplikasi nyata (seperti Web Server), jelaskan secara konseptual (tidak perlu kode) pendekatan/teknik apa yang bisa dilakukan agar satu server bisa menangani ribuan klien secara bersamaan (Konkuren)?

**F. Referensi**

* Python Software Foundation. *Socket Programming HOWTO* (docs.python.org/3/howto/sockets.html).
* Beej's Guide to Network Programming (beej.us/guide/bgnet/).
