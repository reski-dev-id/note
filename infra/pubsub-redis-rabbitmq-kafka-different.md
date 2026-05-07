# PUB/SUB REDIS VS RABBITMQ VS KAFKA CHEAT SHEET

---

# APA ITU PUB/SUB?

Pub/Sub = Publish/Subscribe pattern.

Flow:

```text
Publisher -> Broker/System -> Subscriber
```

Publisher:
- Mengirim event/message

Subscriber:
- Menerima event/message

Tujuan:
- Decouple service
- Async communication
- Realtime event processing

---

# PERBEDAAN UTAMA

| Feature | Redis Pub/Sub | RabbitMQ | Kafka |
|---|---|---|---|
| Tipe | Realtime pub/sub | Message broker | Event streaming |
| Penyimpanan message | Tidak durable | Durable | Durable |
| Replay message | Tidak bisa | Terbatas | Bisa |
| Speed | Sangat cepat | Cepat | Sangat cepat |
| Complex routing | Tidak | Ya | Sedang |
| Big data streaming | Tidak cocok | Tidak ideal | Sangat cocok |
| Queue system | Simple | Sangat kuat | Event-based |
| Persistence | Tidak default | Ya | Ya |
| Scaling | Medium | Medium | Sangat scalable |
| Use case utama | Live realtime | Worker queue | Event streaming massive |

---

# REDIS PUB/SUB

## APA ITU?

Redis publish message langsung ke subscriber.

Flow:

```text
Publisher -> Redis Channel -> Subscriber
```

---

# KELEBIHAN REDIS PUB/SUB

- Sangat cepat
- Simple
- Lightweight
- Setup mudah
- Latency rendah

---

# KELEMAHAN REDIS PUB/SUB

- Tidak durable
- Message hilang kalau subscriber offline
- Tidak ada replay
- Tidak cocok big data
- Tidak cocok critical transaction

---

# KAPAN PAKAI REDIS PUB/SUB?

Gunakan saat:
- Realtime ringan
- Live notification
- Chat sederhana
- Low latency event

---

# 5 CONTOH REDIS PUB/SUB

1. Live chat sederhana

2. Live notification

3. Online user status

4. Multiplayer game event ringan

5. Live dashboard kecil

---

# CONTOH FLOW REDIS PUB/SUB

```text
User kirim chat
    ↓
Redis publish
    ↓
Subscriber terima realtime
```

---

# CONTOH COMMAND

## Publish

```bash
PUBLISH chat "hello"
```

## Subscribe

```bash
SUBSCRIBE chat
```

---

# RABBITMQ

## APA ITU?

RabbitMQ adalah message broker dengan queue system.

Flow:

```text
Producer -> Exchange -> Queue -> Consumer
```

---

# KELEBIHAN RABBITMQ

- Reliable
- Durable queue
- Retry support
- ACK support
- Complex routing

---

# KELEMAHAN RABBITMQ

- Lebih lambat dibanding Redis
- Scaling lebih kompleks
- Tidak ideal untuk massive event streaming

---

# KAPAN PAKAI RABBITMQ?

Gunakan saat:
- Background job
- Worker queue
- Critical processing
- Transaction queue

---

# 5 CONTOH RABBITMQ

1. Email queue

2. Payment processing

3. Invoice generation

4. Notification worker

5. Image/video processing

---

# CONTOH FLOW RABBITMQ

```text
Order dibuat
    ↓
RabbitMQ queue
    ↓
Payment worker process
```

---

# KEUNGGULAN UTAMA RABBITMQ

## ACK

Consumer konfirmasi:
- Message berhasil diproses

## Retry

Kalau gagal:
- Bisa retry otomatis

## DLQ

Message gagal:
- Masuk dead letter queue

---

# KAFKA

## APA ITU?

Kafka adalah distributed event streaming platform.

Flow:

```text
Producer -> Topic -> Consumer Group
```

---

# KELEBIHAN KAFKA

- High throughput
- Replay event
- Durable
- Massive scaling
- Cocok big data

---

# KELEMAHAN KAFKA

- Setup lebih kompleks
- Learning curve lebih tinggi
- Overkill untuk aplikasi kecil

---

# KAPAN PAKAI KAFKA?

Gunakan saat:
- Big data streaming
- Event streaming massive
- Analytics realtime
- Banyak service consume event sama

---

# 5 CONTOH KAFKA

1. Fraud detection realtime

2. Clickstream analytics

3. User activity tracking

4. IoT telemetry

5. Realtime recommendation engine

---

# CONTOH FLOW KAFKA

```text
User click aplikasi
    ↓
Kafka topic
    ↓
Analytics service baca
    ↓
Fraud service baca
    ↓
Recommendation service baca
```

---

# KEUNGGULAN UTAMA KAFKA

## Replay Event

Consumer bisa baca ulang message lama.

## Partition

Bisa parallel processing massive.

## Consumer Group

Banyak consumer bisa share workload.

---

# PERBEDAAN CARA KERJA

## REDIS PUB/SUB

Message langsung dikirim realtime.

Kalau subscriber offline:
- Message hilang

---

## RABBITMQ

Message masuk queue dulu.

Kalau consumer offline:
- Message tetap aman

---

## KAFKA

Message disimpan di topic log.

Consumer:
- Bisa baca sekarang
- Bisa replay nanti

---

# PERBEDAAN DURABILITY

| System | Durable? |
|---|---|
| Redis Pub/Sub | Tidak |
| RabbitMQ | Ya |
| Kafka | Ya |

---

# PERBEDAAN REPLAY MESSAGE

| System | Replay? |
|---|---|
| Redis Pub/Sub | Tidak |
| RabbitMQ | Terbatas |
| Kafka | Ya |

---

# PERBEDAAN SCALING

| System | Scaling |
|---|---|
| Redis Pub/Sub | Medium |
| RabbitMQ | Medium |
| Kafka | Sangat tinggi |

---

# PERBEDAAN LATENCY

| System | Latency |
|---|---|
| Redis Pub/Sub | Sangat rendah |
| RabbitMQ | Rendah |
| Kafka | Rendah |

---

# PERBEDAAN COMPLEXITY

| System | Complexity |
|---|---|
| Redis Pub/Sub | Paling simple |
| RabbitMQ | Medium |
| Kafka | Paling kompleks |

---

# REAL CASE PEMILIHAN

## Gunakan Redis Pub/Sub Jika:

- Butuh realtime ringan
- Tidak perlu persistence
- Tidak perlu replay
- Simple notification

Contoh:
- Chat kecil
- Online status

---

## Gunakan RabbitMQ Jika:

- Butuh reliable queue
- Butuh retry
- Butuh ACK
- Worker processing

Contoh:
- Payment
- Email queue
- Background worker

---

## Gunakan Kafka Jika:

- Big data
- Massive event streaming
- Realtime analytics
- Banyak consumer baca event sama

Contoh:
- Analytics platform
- Banking stream
- IoT platform

---

# ANALOGI

## Redis Pub/Sub

Walkie-talkie realtime.

Kalau tidak dengar sekarang:
- Message hilang

---

## RabbitMQ

Kurir paket.

Kalau penerima belum ada:
- Paket tetap disimpan

---

## Kafka

Log CCTV/event recorder.

Semua event direkam.
Bisa diputar ulang kapan saja.

---

# BEST PRACTICE

## Redis Pub/Sub

- Gunakan hanya untuk realtime ringan

---

## RabbitMQ

- Gunakan durable queue
- Gunakan manual ACK
- Gunakan DLQ

---

## Kafka

- Gunakan partition cukup
- Monitor consumer lag
- Gunakan schema event konsisten

---

# INTERVIEW QUESTION

## Q

Apa beda Redis Pub/Sub dan RabbitMQ?

## A

Redis Pub/Sub realtime dan lightweight tetapi tidak durable.
RabbitMQ reliable queue dengan ACK dan retry.

---

## Q

Apa beda RabbitMQ dan Kafka?

## A

RabbitMQ fokus queue dan worker processing.
Kafka fokus event streaming massive dan replay event.

---

## Q

Kenapa Kafka cocok untuk analytics?

## A

Karena scalable, replayable, dan high throughput.

---

## Q

Kapan Redis Pub/Sub cocok dipakai?

## A

Untuk realtime event ringan seperti chat dan live notification.

---

## Q

Kapan RabbitMQ lebih baik dibanding Kafka?

## A

Saat butuh reliable worker queue dan transaction processing.
