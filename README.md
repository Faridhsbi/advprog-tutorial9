# Modul 9


> a. What is amqp?

AMQP (Advanced Message Queuing Protocol) adalah protokol standar terbuka pada lapisan aplikasi yang dirancang untuk komunikasi antar-sistem berbasis pesan (message-oriented middleware), memungkinkan pengiriman pesan yang andal, terstruktur, dan aman dengan mendukung berbagai pola komunikasi seperti antrean pesan (message queue), publikasi/langganan (publish/subscribe), dan transaksi, serta diimplementasikan melalui broker pesan populer seperti RabbitMQ.

> b. What does it mean? guest:guest@localhost:5672 , what is the first guest, and what is the second guest, and what is localhost:5672 is for?

Pada URI amqp://guest:guest@localhost:5672, kata guest sebelum tanda : adalah nama pengguna (username) untuk autentikasi ke broker AMQP, sedangkan guest setelah tanda : adalah kata sandi (password) yang bersesuaian. Localhost menunjukkan bahwa broker berjalan di mesin lokal, dan 5672 adalah port default yang digunakan oleh protokol AMQP seperti RabbitMQ untuk koneksi non-SSL.

### Simulation Slow Subscriber 

![slow subscriber](images/slow-subscriber.png)

Pada grafik spike, terjadi lonjakan yang cukup tinggi. Hal tersebut dikarenakan setiap kali menjalankan `cargo run` publisher akan langsung mengirim 5 pesan ke broker, sedangkan subscriber yang lambat hanya memproses satu pesan per detik, ketika saya menjalankan publisher sebanyak 5 kali berturut-turut berarti telah mengantri 5 × 5 = 25 pesan, tetapi pada saat saya cek subscriber baru sempat mengkonsumsi sekitar 4 pesan, sehingga tersisa 21 pesan di dalam antrian. Dengan demikian, itulah yang menyebabkan jumlah queued messages membengkak sebelum akhirnya subscriber bisa mengejar dan memprosesnya satu per satu.


### Reflection and Running at least three subscribers

![running 3 subs](images/running-3-subscriber.png)
![subscriber 1](images/subscriber-1.png)
![subscriber 3](images/subscriber-3.png)
![subscriber 2](images/subscriber-2.png)

Ketika saya menjalankan 10 cargo run secara berturut-turut dan tiga instance subscriber paralel, terlihat bahwa sekitar 50 pesan terbagi merata dengan masing-masing subscriber memproses sekitar 16-17 message, karena RabbitMQ mendistribusikan pesan secara round-robin dan setiap pesan diambil hanya sekali oleh salah satu subscriber lalu dihapus dari antrian. Akibatnya, spike pada grafik queued messages jauh lebih cepat mereda dibanding hanya dengan satu subscriber saja, karena antrian dikosongkan secara paralel oleh tiga konsumer yang lambat (1 detik per pesan). Pola ini mengonfirmasi bahwa menambah jumlah subscriber efektif mengatasi bottleneck di konsumer. Untuk membuat simulasi lebih realistis, selain delay di subscriber, kita bisa menambahkan delay pada sisi publisher atau menerapkan prefetch/QoS agar beban pengiriman dan penerimaan pesan lebih terkontrol.