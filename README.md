# Modul 9


> a. What is amqp?

AMQP (Advanced Message Queuing Protocol) adalah protokol standar terbuka pada lapisan aplikasi yang dirancang untuk komunikasi antar-sistem berbasis pesan (message-oriented middleware), memungkinkan pengiriman pesan yang andal, terstruktur, dan aman dengan mendukung berbagai pola komunikasi seperti antrean pesan (message queue), publikasi/langganan (publish/subscribe), dan transaksi, serta diimplementasikan melalui broker pesan populer seperti RabbitMQ.

> b. What does it mean? guest:guest@localhost:5672 , what is the first guest, and what is the second guest, and what is localhost:5672 is for?

Pada URI amqp://guest:guest@localhost:5672, kata guest sebelum tanda : adalah nama pengguna (username) untuk autentikasi ke broker AMQP, sedangkan guest setelah tanda : adalah kata sandi (password) yang bersesuaian. Localhost menunjukkan bahwa broker berjalan di mesin lokal, dan 5672 adalah port default yang digunakan oleh protokol AMQP seperti RabbitMQ untuk koneksi non-SSL.

### Simulation Slow Subscriber 

![slow subscriber](images/slow-subscriber.png)

Pada grafik spike, terjadi lonjakan yang cukup tinggi. Hal tersebut dikarenakan setiap kali menjalankan `cargo run` publisher akan langsung mengirim 5 pesan ke broker, sedangkan subscriber yang lambat hanya memproses satu pesan per detik, ketika saya menjalankan publisher sebanyak 5 kali berturut-turut berarti telah mengantri 5 × 5 = 25 pesan, tetapi pada saat saya cek subscriber baru sempat mengkonsumsi sekitar 4 pesan, sehingga tersisa 21 pesan di dalam antrian. Dengan demikian, itulah yang menyebabkan jumlah queued messages membengkak sebelum akhirnya subscriber bisa mengejar dan memprosesnya satu per satu.