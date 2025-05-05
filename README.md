Pemrograman Lanjut (Advanced Programming) 2024/2025 Genap

- Nama : Nashwa Ghania
- NPM : 2306241770
- Kelas : Pemrograman Lanjut - A

### Reflection:

1. **Perbedaan utama antara unary, server streaming, dan bidirectional streaming RPC**

   - **Unary RPC** adalah metode satu request satu response, cocok untuk operasi sederhana seperti validasi data atau pembayaran dimana klien hanya perlu satu response dari server.
   - **Server streaming** memungkinkan klien mengirim satu request, lalu server mengirim banyak response secara bertahap, cocok untuk kasus seperti menampilkan histori transaksi atau log data.
   - **Bidirectional streaming** memungkinkan klien dan server saling bertukar pesan secara asinkron dan terus-menerus, sangat ideal untuk aplikasi yang memerlukan komunikasi real-time seperti layanan chat.

2. **Pertimbangan keamanan dalam implementasi gRPC di Rust**

   - **Autentikasi**: memastikan identitas pengguna yang valid, misalnya dengan menggunakan token OAuth2 atau TLS client certificates.
   - **Otorisasi**: mengontrol hak akses setiap pengguna terhadap layanan, biasanya melalui middleware atau validasi token.
   - **Enkripsi data**: gRPC secara default menggunakan HTTP/2 yang mendukung TLS sehingga komunikasi data terenkripsi saat transit. Namun, konfigurasi TLS harus diatur dengan benar termasuk pengelolaan sertifikat.

3. **Tantangan dalam penanganan bidirectional streaming**

   Salah satu tantangan utama adalah menjaga sinkronisasi antara aliran masuk dan keluar. Dalam aplikasi chat, kita perlu memastikan bahwa pesan dari klien diproses tanpa delay, sekaligus server dapat mengirim balasan tepat waktu. Masalah umum termasuk penanganan error saat salah satu pihak memutuskan koneksi, kehabisan buffer pada channel, dan pengendalian flow (backpressure). Selain itu, jika tidak hati-hati, tokio::spawn bisa menyebabkan race condition atau memory leak bila alirannya tidak dikelola dengan baik.

4. **Kelebihan dan kekurangan penggunaan ReceiverStream dalam layanan streaming Rust gRPC**

   - **Kelebihan**: integrasi yang sederhana dengan channel asinkron, cocok untuk mengalirkan data secara reaktif, mudah digunakan bersama tokio::spawn.
   - **Kekurangan**: karena menggunakan channel mpsc, ukuran buffer tetap terbatas, dan jika pengiriman terlalu cepat tanpa penerimaan yang seimbang, bisa menyebabkan deadlock atau dropped messages.

5. **Bagaimana cara agar kode Rust gRPC lebih modular dan mudah dipelihara**

   Kode sebaiknya dipisahkan berdasarkan tanggung jawab yaitu setiap service dipecah ke file/module tersendiri (payment.rs, transaction.rs, chat.rs). Interface utama (main.rs) hanya bertanggung jawab untuk inisialisasi server. Penambahan fitur baru pun cukup menambah file dan mendaftarkannya ke dalam server, tanpa mengubah kode yang sudah ada.

6. **Untuk menangani logika pembayaran yang kompleks di MyPaymentService**

   Untuk menangani logika pembayaran yang lebih kompleks, perlu ada validasi data pengguna, pengecekan saldo, penanganan transaksi yang gagal, pencatatan ke dalam database, dan sistem retry. Kita juga bisa menambahkan ID transaksi unik, dan log aktivitas. Di sisi keamanan, kita perlu menerapkan otentikasi pengguna.

7. **Dampak adopsi gRPC pada arsitektur sistem terdistribusi**

   Mengadopsi gRPC membuat sistem lebih efisien untuk komunikasi service-to-service, terutama dalam arsitektur microservices. Karena menggunakan Protocol Buffers, data menjadi ringan dan cepat diproses. Namun, interoperabilitas dengan sistem lain membutuhkan support terhadap Protobuf dan HTTP/2. Bila sistem hanya mendukung REST/JSON, perlu ada adaptor atau gateway.

8. **Keunggulan dan kelemahan HTTP/2 dibanding HTTP/1.1 atau HTTP/1.1 + WebSocket**

   HTTP/2 mendukung multiplexing, jadi banyak request bisa dikirim sekaligus dalam satu koneksi, tidak seperti HTTP/1.1 yang harus menunggu giliran. Ini mengurangi latensi. Dibanding WebSocket, HTTP/2 lebih terstruktur dan tetap memakai model request-response meskipun mendukung stream. Namun, HTTP/2 lebih kompleks untuk ditangani, dan tidak semua tools/developer sudah terbiasa dengannya. WebSocket lebih fleksibel untuk komunikasi real-time bebas struktur, tapi tidak seketat gRPC dalam definisi schema.

9. **Perbandingan REST API dengan gRPC bidirectional streaming**

   - REST menggunakan model request-response statis yang cocok untuk operasi yang tidak memerlukan komunikasi berkelanjutan.
   - gRPC dengan streaming memungkinkan komunikasi real-time, membuatnya lebih responsif untuk aplikasi seperti notifikasi, live updates, atau chat.

10. **Implikasi pendekatan berbasis skema (gRPC) dibanding JSON yang tidak berskema (REST)**

    - gRPC dengan Protocol Buffers memberikan validasi dan kontrak eksplisit antara klien dan server, membuat integrasi lebih terstruktur.
    - JSON lebih fleksibel dan mudah dibaca manusia, tetapi rawan kesalahan karena tidak memiliki tipe data yang ketat dan tidak menjamin konsistensi struktur data.
