/**Nama: Muhammad Afwan Hafizh**/
/**NPM: 2306208855**/

___
Reflection

1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?
___

1. Unary RPC mengirimkan satu request dan menerima satu response (seperti pada ProcessPayment). Ini cocok untuk operasi sederhana seperti autentikasi atau validasi. Lalu, server streaming RPC mengirimkan satu request dan menerima aliran respons berkelanjutan (seperti pada GetTransactionHistory). Ini cocok untuk pengambilan data historis atau notifikasi berkelanjutan Lalu, bi-directional streaming RPC memungkinkan kedua pihak mengirim aliran pesan secara bersamaan (seperti pada Chat). Ini cocok untuk aplikasi realtime seperti obrolan, game multiplayer, atau service kolaborasi di mana komunikasi dua arah yang cepat diperlukan.

2. Implementasi service gRPC di Rust memerlukan beberapa pertimbangan keamanan penting, termasuk autentikasi untuk memverifikasi identitas client (misal, token JWT atau OAuth2), otorisasi untuk mengontrol akses ke service dan metode tertentu berdasarkan peran pengguna, dan enkripsi data melalui TLS untuk melindungi data dalam transit. Rust memberikan keuntungan keamanan default dengan sistem dan pengelolaan memori yang aman, tetapi developer tetap perlu menerapkan validasi input yang ketat, logging yang tepat untuk audit keamanan, dan perlindungan terhadap serangan umum seperti DDoS, atau man-in-the-middle attacks.

3. Beberapa tantangan utamanya yaitu mengelola concurrency dan sinkronisasi antara aliran pesan yang masuk dan keluar, menangani kegagalan koneksi dan pemulihan secara anggun, serta memastikan pemrosesan yang efisien dari pesan dalam jumlah besar. Selain itu, mengimplementasikan logika timeout yang tepat, buffering pesan, dan backpressure juga penting untuk menghindari kebocoran memori atau situasi kehabisan resource terutama ketika client mengirim pesan lebih cepat daripada kemampuan server untuk memprosesnya sehingga dapat mengakibatkan degradasi kinerja atau bahkan kegagalan sistem.

4. Penggunaan tokio_stream::wrappers::ReceiverStream untuk respons streaming dalam service gRPC Rust memberikan beberapa keuntungan penting, termasuk integrasi dengan ekosistem asinkron Tokio, manajemen backpressure default melalui saluran yang kapasitasnya terbatas, dan abstraksi untuk membuat dan mengelola aliran. Namun, pendekatan ini juga memiliki kekurangan, seperti overhead tambahan dari saluran komunikasi yang dapat memengaruhi kinerja pada beban tinggi, potensi kebocoran memori jika pengirim tidak dikelola dengan benar saat terjadi kesalahan, dan keterbatasan dalam kontrol aliran yang lebih granular yang mungkin diperlukan untuk kasus penggunaan yang sangat khusus.

5. Struktur kode dapat diorganisir dengan memisahkan service, implementasi, dan logika program menjadi modul terpisah. Implementasi trait service dapat dikembangkan di atas abstraksi yang mendukung pengujian dan simulasi, dengan dependency injection daripada diinstansiasi secara langsung. Kode yang digunakan bersama, seperti utilitas validasi, manajemen error, dan logika otorisasi, dapat diabstraksi ke dalam crate terpisah yang dapat digunakan kembali di beberapa service. Pendekatan tersebut meningkatkan development dengan mengurangi duplikasi, memungkinkan pengujian unit yang lebih baik, dan memudahkan perluasan fungsionalitas dengan meminimalkan perubahan pada kode yang ada.

6. Beberapa langkah tambahan yang diperlukan yaitu validasi input untuk memverifikasi ID pengguna, jumlah, dan detail pembayaran lainnya, integrasi dengan penyedia pembayaran atau gateway pihak ketiga, penerapan mekanisme rollback dan transaksi untuk menjaga integritas data, dan pencatatan audit yang komprehensif untuk kepatuhan dan pelacakan. Selain itu, logika bisnis yang lebih canggih perlu ditambahkan untuk menangani otorisasi pembayaran, deteksi penipuan, pengelolaan batas pengeluaran, dan mekanisme notifikasi untuk memberi tahu pengguna tentang status transaksi mereka melalui berbagai saluran seperti email atau SMS.

7. gRPC memberikan kontrak yang jelas antara service melalui file Proto sehingga memungkinkan development paralel dan menekankan pendekatan berbasis kontrak. Dibandingkan dengan REST, gRPC memberikan kinerja yang lebih baik dan penggunaan bandwidth yang lebih efisien berkat HTTP/2 dan Protocol Buffers sehingga mendukung streaming dua arah dan menghasilkan kode client dan server secara otomatis. Namun, adopsi gRPC juga memiliki kompleksitas tambahan dalam pengujian, pemantauan, dan dapat menimbulkan tantangan interoperabilitas dengan service web yang mengharuskan gateway API untuk penerjemahan format.

8. HTTP/2 memberikan beberapa keunggulan termasuk multiplexing request melalui satu koneksi yang mengurangi overhead TCP, kompresi header yang mengurangi overhead jaringan, dan prioritisasi aliran yang memungkinkan sumber daya penting diproses lebih dulu. Namun, HTTP/2 juga memiliki kekurangan seperti kompleksitas implementasi yang lebih tinggi, kurangnya dukungan proxy yang luas, dan tantangan debugging karena format biner. Sementara, WebSocket memberikan komunikasi full-duplex. WebSocket tidak memiliki fitur multiplexing HTTP/2 dan framework kontrak yang terstruktur seperti yang ditawarkan oleh gRPC dengan Protocol Buffers, meskipun WebSocket mungkin masih lebih cocok untuk beberapa aplikasi berbasis browser karena dukungan browser yang lebih baik.

9. REST API mengikuti paradigma request-response yang sinkron, yaitu client harus memulai setiap interaksi dan menunggu respons server sebelum melanjutkan. Ini cocok untuk operasi CRUD sederhana tetapi kurang optimal untuk pembaruan real-time. Sebaliknya, gRPC dengan streaming dua arahnya memungkinkan komunikasi asinkron berkelanjutan antara client dan server tanpa perlu polling berkelanjutan sehingga meningkatkan responsivitas untuk aplikasi real-time seperti obrolan atau pemantauan. Meskipun REST dapat meniru beberapa kemampuan ini dengan teknik seperti long polling atau WebSocket, gRPC menawarkan pendekatan yang lebih terintegrasi dan efisien dengan dukungan streaming bawaan dan penggunaan bandwidth yang lebih rendah.

10. Protocol Buffers memberikan keuntungan berupa validasi tipe yang ketat, ukuran payload yang lebih kecil, kinerja serialisasi/deserialisasi yang lebih cepat, dan development yang lebih aman melalui kontrak eksplisit yang mencegah banyak kesalahan runtime. Namun, pendekatan ini kurang fleksibel dibandingkan JSON karena memerlukan kompilasi ulang ketika skema berubah. Sementara, JSON lebih mudah di-debug, lebih fleksibel untuk evolusi API, dan memiliki dukungan yang lebih luas di berbagai platform dan bahasa. Namun, JSON juga rentan terhadap kesalahan runtime karena kurangnya penegakkan tipe dan menghasilkan payload yang lebih besar. Pilihan antara keduanya bergantung pada kebutuhan proyek, dengan gRPC yang lebih cocok untuk sistem internal berperforma tinggi dan REST/JSON untuk API publik yang memerlukan fleksibilitas dan aksesibilitas yang lebih luas.