# Modul 8 Pemrograman Lanjut : High-Level Networking
oleh **Brenda Po Lok Fahida**

<br>
<br>

> 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

Perbedaan utama antara ketiga metode RPC ini adalah pada cara komunikasi dilakukan antara client dan server. Unary, server streaming, dan bidirectional streaming masing-masing memiliki karakteristik yang cocok untuk skenario yang berbeda:

- Unary RPC adalah metode di mana client mengirim satu permintaan dan menerima satu respons dari server. Cocok untuk operasi sederhana seperti login, pengambilan data tunggal, atau validasi input.
- Server Streaming RPC adalah metode di mana client mengirim satu permintaan, dan server mengirimkan banyak respons secara bertahap. Cocok untuk pengambilan data dalam jumlah banyak seperti riwayat transaksi atau log sistem.
- Bi-directional Streaming RPC adalah metode di mana client dan server dapat saling mengirim dan menerima data secara bersamaan dan berkelanjutan. Cocok untuk aplikasi real-time seperti chat, collaborative tools, atau layanan streaming langsung.


> 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

Dalam mengembangkan layanan gRPC menggunakan Rust, beberapa pertimbangan keamanan yang penting meliputi perlindungan komunikasi, kontrol akses, dan validasi input. Penggunaan TLS wajib diterapkan untuk mengenkripsi data selama pengiriman agar tidak mudah disadap pihak ketiga. Dari sisi otentikasi, layanan dapat menggunakan token berbasis JWT atau OAuth2 untuk memastikan hanya client yang sah yang bisa mengakses. Selain itu, sistem otorisasi harus membatasi akses berdasarkan peran atau hak pengguna, mencegah penyalahgunaan layanan. Validasi input dan sanitasi data juga penting untuk menghindari serangan injeksi atau data corrupt. Tidak kalah penting adalah mempertimbangkan rate limiting atau throttling untuk mengurangi risiko serangan denial of service (DoS).


> 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Penanganan bidirectional streaming di Rust gRPC, khususnya untuk aplikasi seperti chat, menghadirkan tantangan teknis yang tidak bisa diabaikan. Salah satu tantangan utamanya adalah mengelola sink dan stream secara bersamaan dalam lingkungan asynchronous. Ini membutuhkan pemahaman mendalam mengenai concurrency dan ownership model di Rust. Selain itu, dalam aplikasi real-time yang melibatkan banyak user, kita perlu memastikan bahwa sistem dapat menskalakan beban secara efisien tanpa menyebabkan bottleneck atau deadlock. Tantangan lain termasuk menangani backpressure, menjaga kestabilan koneksi yang terus-menerus aktif, dan menyediakan mekanisme reconnect yang mulus saat koneksi terganggu. Proses debugging dan logging pada skenario streaming juga jauh lebih kompleks dibanding RPC biasa.


> 4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

Penggunaan ReceiverStream dalam konteks layanan gRPC di Rust menawarkan solusi praktis untuk menangani aliran data dari channel asynchronous. Pendekatan ini memungkinkan server untuk secara efisien mengirimkan data secara real-time, namun tentu saja disertai beberapa keterbatasan.

Kelebihan:

- Mudah diintegrasikan dengan tokio::sync::mpsc, sehingga cocok untuk pipeline berbasis event.
- Mendukung pengiriman data secara terus-menerus tanpa harus membuat koneksi baru.
- Sederhana dan efisien untuk skenario streaming ringan seperti notifikasi.

Kekurangan:

- Tidak ideal untuk skenario dengan volume data sangat besar atau kompleksitas pengelolaan aliran tinggi.
- Perlu perhatian terhadap ukuran buffer agar tidak terjadi bottleneck atau kehilangan data.
- Tidak memiliki built-in flow control canggih, sehingga harus diatur manual jika diperlukan.


> 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

Untuk mendukung keterpeliharaan dan skalabilitas jangka panjang, kode Rust gRPC sebaiknya disusun secara modular dan reusable. Salah satu pendekatan yang efektif adalah memisahkan business logic dari handler gRPC. Dengan cara ini, handler hanya bertugas menghubungkan permintaan client dengan logika bisnis yang dapat diuji secara independen. Selain itu, penggunaan trait untuk mendefinisikan antarmuka layanan memungkinkan pengujian unit lebih fleksibel karena dependensi dapat dimock dengan mudah. Struktur proyek juga dapat dibagi menjadi beberapa crate atau module, seperti auth, payment, dan proto, agar kode lebih terorganisasi. Pendekatan builder pattern atau konfigurasi berbasis file .toml juga dapat digunakan untuk membuat inisialisasi layanan menjadi lebih fleksibel dan dapat diatur tanpa perlu menyentuh logika utama aplikasi.


> 6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Untuk menangani proses pembayaran yang lebih kompleks di dalam MyPaymentService, beberapa langkah lanjutan perlu ditambahkan. Validasi data transaksi harus dilakukan dengan ketat, termasuk pengecekan double payment, format input, dan sumber dana. Layanan juga perlu terintegrasi dengan gateway pembayaran eksternal yang memiliki API dan protokol yang berbeda-beda, sehingga memerlukan adapter atau wrapper tersendiri. Selain itu, sistem harus mendukung idempotency untuk mencegah pengulangan transaksi akibat gangguan koneksi. Jika terjadi kegagalan di tengah proses, mekanisme rollback dan kompensasi perlu disiapkan agar data tetap konsisten. Logging yang lengkap dan sistem audit trail juga penting agar transaksi bisa ditelusuri dan diverifikasi di kemudian hari.


> 7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

Adopsi gRPC sebagai protokol komunikasi dalam sistem terdistribusi secara signifikan mempengaruhi arsitektur dan cara layanan saling berinteraksi. Karena gRPC mendukung berbagai bahasa pemrograman secara native, ini mendorong interoperabilitas lintas platform tanpa perlu membangun konversi manual seperti pada REST. Arsitektur berbasis schema yang konsisten melalui Protocol Buffers juga membantu koordinasi antar tim dalam organisasi besar. Namun demikian, adopsi ini menuntut standar yang ketat dalam pengelolaan schema dan versi API. Selain itu, tidak semua sistem atau alat pengembang mendukung gRPC secara langsung, sehingga integrasi dengan sistem REST atau frontend web kadang memerlukan tambahan lapisan seperti grpc-web atau reverse proxy.


> 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

Penggunaan HTTP/2 sebagai protokol dasar gRPC membawa peningkatan performa yang signifikan dibandingkan HTTP/1.1 dan WebSocket. HTTP/2 mendukung multiplexing, yaitu mengirim banyak permintaan sekaligus melalui satu koneksi tanpa blocking, serta melakukan header compression untuk mengurangi ukuran permintaan. Ini sangat menguntungkan dalam aplikasi yang banyak berkomunikasi dalam waktu singkat. 

Namun, terdapat kelemahan terutama dalam hal kompatibilitas dengan infrastruktur jaringan lama seperti firewall dan load balancer yang tidak mendukung HTTP/2. Selain itu, debugging dan inspeksi lalu lintas HTTP/2 bisa lebih sulit dibanding HTTP/1.1. Sementara WebSocket cocok untuk real-time, tapi tidak memiliki struktur seketat gRPC dan rentan kesalahan implementasi.


> 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

Model permintaan-respons pada REST API hanya mendukung komunikasi satu arah, yaitu client mengirim permintaan dan server merespons satu kali. Model ini cocok untuk banyak aplikasi web dan layanan CRUD, tetapi kurang ideal dalam komunikasi real-time. Sebaliknya, gRPC mendukung bidirectional streaming di mana client dan server dapat terus berkomunikasi tanpa harus memulai koneksi baru untuk setiap pertukaran data. Ini memberikan keunggulan signifikan dalam hal latensi rendah dan responsivitas, sangat sesuai untuk aplikasi seperti chat, pelacakan posisi, atau layanan streaming data. Oleh karena itu, dari sisi pengalaman pengguna dan efisiensi komunikasi, gRPC jauh lebih unggul untuk aplikasi yang membutuhkan interaksi waktu nyata.


> 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

Pendekatan schema-based seperti yang digunakan gRPC dengan Protocol Buffers menawarkan kejelasan dan efisiensi tinggi dalam serialisasi data. Dengan skema yang telah ditentukan sebelumnya, komunikasi antar layanan menjadi lebih konsisten dan mudah diverifikasi. Ini sangat menguntungkan dalam sistem berskala besar atau di mana kontrak data sangat penting. Namun, kekakuan skema ini juga bisa menjadi beban dalam pengembangan cepat, karena setiap perubahan pada struktur data memerlukan regenerasi kode dan sinkronisasi ulang ke semua pihak yang menggunakan. Sebaliknya, JSON dalam REST API lebih fleksibel dan dapat diubah dengan cepat tanpa memerlukan banyak tooling tambahan, tetapi kurang aman terhadap kesalahan struktural dan lebih berat secara ukuran data.