# Modul 9: Event-Driven Architecture - Subscriber


**a. What is amqp?**
AMQP (Advanced Message Queuing Protocol) adalah sebuah standar protokol *open-source* pada *application layer* yang digunakan untuk *message-oriented middleware*. Protokol ini memungkinkan komunikasi dan pertukaran pesan (data/event) yang aman, andal, dan efisien antar sistem atau aplikasi yang berbeda (dalam hal ini, antara *publisher*, *message broker*, dan *subscriber*).

**b. What does it mean? `guest:guest@localhost:5672`**
String tersebut merupakan *Connection URI* (Uniform Resource Identifier) yang digunakan oleh aplikasi kita untuk terhubung ke *message broker* (RabbitMQ). Berikut adalah rinciannya:
* **`guest` pertama:** Merupakan *username* (kredensial login) default yang digunakan untuk mengakses RabbitMQ.
* **`guest` kedua:** Merupakan *password* default yang digunakan untuk otentikasi *username* tersebut.
* **`localhost:5672`:** Menunjukkan lokasi dan *port* di mana server RabbitMQ berjalan. `localhost` berarti server berjalan di komputer lokal kita sendiri, dan `5672` adalah *port* default yang didengarkan (listen) oleh RabbitMQ untuk menerima koneksi AMQP.