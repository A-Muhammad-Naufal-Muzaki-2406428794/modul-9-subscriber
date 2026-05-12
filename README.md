# Module 09: Event-Driven Architecture - Subscriber

Repository ini berisi program subscriber untuk tutorial Event-Driven Architecture pada Module 09. Program subscriber bertugas mendengarkan queue `user_created` pada RabbitMQ, menerima event yang dikirim oleh publisher, lalu memproses event tersebut.

Dalam tutorial ini, subscriber tidak menerima data langsung dari publisher. Publisher mengirim event ke RabbitMQ sebagai message broker, kemudian subscriber mengambil event tersebut dari queue yang sama.

---

## Subscriber and Message Broker

### a. What is AMQP?

AMQP atau Advanced Message Queuing Protocol adalah protokol pada application layer yang digunakan untuk komunikasi berbasis message. Protokol ini umum digunakan pada sistem yang menggunakan message broker seperti RabbitMQ.

Dalam tutorial ini, AMQP digunakan agar publisher dan subscriber dapat berkomunikasi melalui RabbitMQ. Publisher mengirimkan event ke message broker, lalu subscriber menerima dan memproses event tersebut dari queue.

### b. What does `guest:guest@localhost:5672` mean?

String `guest:guest@localhost:5672` adalah bagian dari connection URI:

`amqp://guest:guest@localhost:5672`

Penjelasannya adalah sebagai berikut:

- `amqp://` menunjukkan bahwa koneksi menggunakan protokol AMQP.
- `guest` pertama adalah username default RabbitMQ.
- `guest` kedua adalah password default RabbitMQ.
- `localhost` berarti RabbitMQ berjalan di komputer lokal.
- `5672` adalah port default RabbitMQ untuk koneksi AMQP.

Karena publisher dan subscriber menggunakan connection URI yang sama, keduanya terhubung ke RabbitMQ yang sama. Subscriber kemudian mendengarkan queue `user_created` agar dapat menerima message yang dikirim oleh publisher.

---

## Simulation Slow Subscriber

Pada tahap ini, subscriber dibuat lebih lambat dengan mengaktifkan baris `thread::sleep(ten_millis);` pada file `src/main.rs`.

Baris tersebut membuat subscriber menunggu selama 1 detik setiap kali memproses satu message. Setelah itu, publisher dijalankan beberapa kali dengan cepat. Karena publisher mengirim message lebih cepat daripada subscriber memprosesnya, RabbitMQ akan menyimpan message yang belum diproses di dalam queue.

Ketika queue meningkat, artinya ada message yang sudah diterima oleh RabbitMQ tetapi belum selesai diproses oleh subscriber. Setelah subscriber memproses message satu per satu, jumlah queue akan turun kembali. Hal ini menunjukkan bahwa RabbitMQ dapat membantu menampung beban sementara ketika consumer lebih lambat daripada producer.

Gambar ada di repository *Publisher*.

---

## Reflection and Running at Least Three Subscribers

Pada eksperimen ini, saya menjalankan tiga subscriber secara bersamaan. Ketiga subscriber tersebut mendengarkan queue yang sama, yaitu `user_created`. Setelah itu, publisher dijalankan beberapa kali untuk mengirim banyak message ke RabbitMQ.

Hasilnya, message tidak hanya diproses oleh satu subscriber, tetapi dibagi ke beberapa subscriber yang sedang aktif. Dengan begitu, proses konsumsi message menjadi lebih cepat dibandingkan hanya menggunakan satu subscriber. Ini menunjukkan salah satu kelebihan event-driven architecture, yaitu sistem dapat meningkatkan kapasitas pemrosesan dengan menambahkan jumlah consumer.

![Three Subscriber](images/three.png)

---

## Bonus: Cloud Experiment

Selain menjalankan eksperimen secara lokal, saya juga menjalankan subscriber di cloud environment. Tujuannya adalah membuktikan bahwa arsitektur event-driven tetap berjalan ketika RabbitMQ, publisher, dan subscriber dijalankan di environment cloud.

### Cloud Subscriber

Subscriber berhasil dijalankan di cloud dan terhubung ke RabbitMQ melalui connection URI yang sama, yaitu `amqp://guest:guest@localhost:5672`.

![Cloud: Subscriber](images/cloud-subscriber.png)

### Cloud Sending and Processing Event

Pada eksperimen cloud, publisher dijalankan untuk mengirim 5 event ke RabbitMQ. Subscriber yang berjalan di cloud berhasil menerima dan memproses event tersebut.

![Cloud: Sending Processing](images/cloud-sending-processing.png)

### Cloud Slow Subscriber

Pada simulasi slow subscriber di cloud, baris `thread::sleep(ten_millis);` diaktifkan kembali. Ketika publisher dijalankan beberapa kali dengan cepat, RabbitMQ sempat menampung message di queue karena subscriber memproses message lebih lambat.

![Cloud: Slow](images/cloud-slow.png)

---

## Reflection

Dari tutorial ini, saya memahami bahwa subscriber memiliki peran sebagai consumer dalam event-driven architecture. Subscriber tidak perlu mengetahui kapan publisher akan mengirim data. Subscriber hanya perlu mendengarkan queue yang sesuai, kemudian memproses message ketika message tersedia.

Penggunaan RabbitMQ membuat komunikasi antara publisher dan subscriber menjadi lebih fleksibel. Jika subscriber sedang lambat, message tidak langsung hilang, tetapi dapat disimpan sementara di queue. Jika jumlah message meningkat, kita dapat menjalankan lebih banyak subscriber agar message dapat diproses lebih cepat.

Salah satu hal yang dapat ditingkatkan dari kode subscriber adalah penggunaan logging yang lebih jelas agar setiap subscriber dapat dibedakan ketika menjalankan banyak instance. Selain itu, variabel yang tidak digunakan seperti `now` dapat dihapus agar kode lebih bersih.