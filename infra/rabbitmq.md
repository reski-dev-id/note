# RABBITMQ CHEAT SHEET

---

# APA ITU RABBITMQ?

- Message broker
- Queue system
- Digunakan untuk komunikasi asynchronous antar service
- Producer kirim message
- Consumer menerima dan process message

## Website

```text
https://www.rabbitmq.com
```

---

# KENAPA PAKAI RABBITMQ?

- Decouple service
- Background processing
- Retry message
- Queue job
- Mengurangi beban synchronous request

---

# ANALOGI

## Tanpa RabbitMQ

```text
User -> API -> Email Service
```

Kalau email lambat:
- API ikut lambat

---

## Dengan RabbitMQ

```text
User -> API -> RabbitMQ -> Email Worker
```

API langsung response:
- Worker process di background

---

# USE CASE RABBITMQ

## 1. Email Queue

Contoh:
- Send OTP
- Welcome email
- Invoice email

---

## 2. Payment Processing

Contoh:
- Payment callback
- Fraud checking
- Invoice generation

---

## 3. Notification System

Contoh:
- Push notification
- SMS
- WhatsApp

---

## 4. Background Job

Contoh:
- Resize image
- Generate PDF
- Video processing

---

## 5. Microservices Communication

Contoh:
- Order service
- Payment service
- Inventory service

---

# CORE COMPONENT

## Producer

Yang mengirim message.

```text
Producer -> RabbitMQ
```

---

## Queue

Tempat penyimpanan message sementara.

```text
[ Queue ]
```

---

## Consumer

Yang menerima dan process message.

```text
RabbitMQ -> Consumer
```

---

## Exchange

Router message.

Menentukan message masuk ke queue mana.

---

## Routing Key

Identifier untuk routing message.

Contoh:

```text
order.created
payment.success
```

---

# FLOW RABBITMQ

```text
Producer
   ↓
Exchange
   ↓
Queue
   ↓
Consumer
```

---

# INSTALL RABBITMQ

## Docker

```bash
docker run -d --hostname rabbit --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management
```

---

# ACCESS DASHBOARD

```text
http://localhost:15672
```

Default login:

```text
username: guest
password: guest
```

---

# PORT RABBITMQ

| Port | Function |
|---|---|
| 5672 | AMQP connection |
| 15672 | Management dashboard |

---

# BASIC CONCEPT

## Queue

Tempat message menunggu.

Contoh:

```text
email_queue
payment_queue
```

---

## Message

Data yang dikirim.

Contoh:

```json
{
  "email": "user@gmail.com",
  "type": "welcome"
}
```

---

## Consumer

Worker yang membaca queue.

---

# CONTOH SIMPLE FLOW

## Producer

Kirim job:

```text
Send Welcome Email
```

Masuk ke:

```text
email_queue
```

---

## Consumer

Worker membaca queue:

```text
process email...
```

---

# EXCHANGE TYPE

## Direct Exchange

Routing exact match.

Contoh:

```text
routing key = payment.success
```

---

## Fanout Exchange

Broadcast ke semua queue.

Use Case:
- Notification
- Event broadcast

---

## Topic Exchange

Routing berdasarkan pattern.

Contoh:

```text
order.*
payment.*
```

---

## Headers Exchange

Routing berdasarkan header.

Jarang dipakai.

---

# ACKNOWLEDGEMENT (ACK)

## Apa Itu?

Consumer memberi tahu:
- Message berhasil diproses

---

## Kalau Tidak ACK?

Message bisa:
- Requeue
- Retry
- Masuk dead letter queue

---

# RETRY MECHANISM

Use Case:
- API external error
- Database timeout
- Payment gateway error

Flow:

```text
Consumer gagal
    ↓
Retry queue
    ↓
Consumer ulang
```

---

# DEAD LETTER QUEUE (DLQ)

## Apa Itu?

Queue khusus message gagal.

Use Case:
- Debugging
- Error monitoring
- Retry failed job

---

# DURABLE QUEUE

## Apa Itu?

Queue tetap ada walau RabbitMQ restart.

Contoh:

```text
durable = true
```

---

# PERSISTENT MESSAGE

## Apa Itu?

Message disimpan ke disk.

Contoh:
- Important payment event
- Financial transaction

---

# AUTO ACK VS MANUAL ACK

## Auto ACK

RabbitMQ anggap message sukses langsung.

Risk:
- Message hilang kalau consumer crash

---

## Manual ACK

Consumer ACK setelah selesai process.

Lebih aman.

---

# PREFETCH

## Apa Itu?

Limit jumlah message per consumer.

Contoh:

```text
prefetch = 1
```

Artinya:
- Consumer ambil 1 message dulu
- Baru ambil berikutnya setelah selesai

---

# REAL CASE PRODUCTION

## E-commerce

- Order queue
- Payment processing
- Invoice generation

---

## Banking

- Transaction event
- Notification
- Audit log

---

## Social Media

- Notification
- Feed generation
- Media processing

---

## Video Platform

- Video encoding
- Thumbnail generation
- Upload processing

---

# KELEBIHAN RABBITMQ

- Reliable
- Mature
- Retry support
- Flexible routing
- Mudah dipakai

---

# KEKURANGAN RABBITMQ

- Lebih lambat dibanding Kafka
- Complex scaling
- Bukan untuk big data streaming massive

---

# RABBITMQ VS REDIS

## RabbitMQ

- Reliable queue
- Advanced routing
- ACK system
- Durable message

Cocok untuk:
- Critical job
- Payment
- Transaction

---

## Redis Queue

- Sangat cepat
- Simpler
- Lightweight

Cocok untuk:
- Simple queue
- Fast temporary task

---

# RABBITMQ VS KAFKA

## RabbitMQ

- Queue-based
- Complex routing
- Job processing
- Worker queue

---

## Kafka

- Event streaming
- Big data
- High throughput
- Log streaming

---

# BEST PRACTICE

1. Gunakan durable queue

2. Gunakan manual ACK

3. Pisahkan queue per domain

Contoh:

```text
email_queue
payment_queue
notification_queue
```

4. Gunakan DLQ untuk failed message

5. Monitor queue size

---

# MONITORING

Tools:

```text
RabbitMQ Management UI
Prometheus
Grafana
```

---

# GO CLIENT

Library:

```bash
github.com/rabbitmq/amqp091-go
```

---

# CONTOH PRODUCER GO

```go
body := "hello"

ch.Publish(
    "",
    "email_queue",
    false,
    false,
    amqp.Publishing{
        ContentType: "text/plain",
        Body: []byte(body),
    },
)
```

---

# CONTOH CONSUMER GO

```go
msgs, _ := ch.Consume(
    "email_queue",
    "",
    false,
    false,
    false,
    false,
    nil,
)

for msg := range msgs {
    fmt.Println(string(msg.Body))

    msg.Ack(false)
}
```

---

# ACKNOWLEDGEMENT (ACK)

## Apa Itu?

Consumer memberi tahu:
- Message berhasil diproses

---

## Flow ACK

```text
Consume → proses OK → ACK → hilang
```

```text
✔ di-consume
✔ di-ack
❌ hilang dari queue
```

---

## Contoh ACK

```go
msg.Ack(false)
```

---

## Kapan ACK?

Gunakan saat:
- Database sukses
- API external sukses
- File sukses diproses
- Semua logic selesai tanpa error

---

# NACK (NEGATIVE ACKNOWLEDGEMENT)

## Apa Itu?

Consumer memberi tahu:
- Message gagal diproses

RabbitMQ bisa:
- Retry
- Requeue
- Masuk DLQ

---

## Flow NACK

```text
✔ di-consume
✔ di-nack
✔ balik ke queue
```

---

## Contoh NACK

```go
msg.Nack(false, true)
```

Artinya:
- gagal process
- masukkan kembali ke queue

---

# FLOW PRODUKSI YANG BENAR

```go
try {
    proses job

    kalau sukses:
        ACK

    kalau gagal:
        NACK (requeue)
}
```

---

# DEAD LETTER QUEUE (DLQ)

## Apa Itu?

Queue khusus message gagal.

---

## Use Case

```text
retry > 3
    ↓
masuk DLQ
```

---

## Kenapa Penting?

Tanpa DLQ:
- Message gagal infinite retry

Dengan DLQ:
- Bisa debugging
- Bisa manual retry
- Tidak ganggu queue utama

---

# TTL (TIME TO LIVE)

## Apa Itu?

Message expire otomatis.

---

## Dipakai Untuk

- Delay job
- OTP expiration
- Cleanup message lama

---

## Contoh

```text
x-message-ttl = 60000
```

Artinya:
- expire setelah 60 detik

---

# DURABILITY

## Apa Itu?

Message tetap aman walau RabbitMQ restart.

---

## Durable Queue

```text
durable = true
```

---

## Persistent Message

```text
delivery_mode = 2
```

---

## Kenapa Penting?

Kalau tidak durable:
- restart RabbitMQ
- message hilang

---

# READY VS UNACKED

## READY

Message:
- belum diambil worker

---

## UNACKED

Message:
- sudah diambil worker
- tapi belum ACK

---

## Flow

```text
Queue (Ready)
   ↓ consume
Worker
   ↓
Unacked
   ↓
ACK → hilang
NACK → balik / DLQ
```

---

# BAHAYA UNACKED NUMPUK

Kalau Unacked banyak:
- worker stuck
- queue macet
- memory naik
- throughput turun

---

# REQUEUE LOOP (BAHAYA)

## Flow Buruk

```text
NACK(true)
    ↓
masuk lagi
    ↓
error lagi
    ↓
NACK lagi
```

Infinite loop.

---

# SOLUSI REQUEUE LOOP

- Retry limit
- DLQ
- Backoff delay

---

# IDEMPOTENCY (WAJIB)

## Kenapa Penting?

RabbitMQ:
- at least once delivery

Artinya:
- message bisa duplicate

---

# SOLUSI IDEMPOTENCY

Gunakan:
- job_id
- transaction_id
- Redis check

---

## Contoh

```text
job sudah pernah diproses?
    ya -> skip
    tidak -> process
```

---

# SCALING WORKER

## Apa Itu?

Tambah banyak worker:
- process lebih paralel

---

# Tapi Harus:

✔ idempotent  
✔ prefetch  
✔ retry limit  
✔ DLQ

---

# PREFETCH

## Apa Itu?

Jumlah maksimal message yang boleh diambil worker sebelum ACK.

---

## Contoh

```text
prefetch = 1
```

Artinya:
- worker ambil 1 job
- selesaikan dulu
- baru ambil berikutnya

---

# TANPA PREFETCH

## Masalah

1 worker bisa ambil:
- banyak message sekaligus

Akibat:
- semua jadi Unacked
- worker stuck
- queue tidak fair

---

# TUJUAN PREFETCH

## 1. Hindari Race Condition

1 worker:
- tidak ambil terlalu banyak job

---

## 2. Hindari Unacked Numpuk

Job:
- tidak nyangkut di worker

---

## 3. Fair Distribution

Kalau banyak worker:
- job dibagi rata

---

# BOTTLENECK YANG SERING TERJADI

❌ Prefetch tidak diset  
→ Unacked numpuk

❌ Tidak idempotent  
→ duplicate chaos

❌ 1 queue untuk semua job  
→ saling block

❌ Worker lambat  
→ Ready numpuk

---

# ACK VS NACK TABLE

| Command | Scope | Hasil | Risiko |
|---|---|---|---|
| `Ack(false)` | 1 msg | selesai, hapus | aman |
| `Ack(true)` | batch | semua dihapus | over-ack |
| `Nack(false, true)` | 1 msg | retry | loop kalau salah logic |
| `Nack(false, false)` | 1 msg | drop / DLQ | kehilangan job |
| `Nack(true, true)` | batch | semua retry | duplicate mass |
| `Nack(true, false)` | batch | semua drop | data loss mass |

---

# YANG DISARANKAN

## Sukses

```go
msg.Ack(false)
```

---

## Retry

```go
msg.Nack(false, true)
```

---

## Drop / DLQ

```go
msg.Nack(false, false)
```

---

# YANG BERBAHAYA

```go
msg.Ack(true)

msg.Nack(true, true)

msg.Nack(true, false)
```

---

# KENAPA BERBAHAYA?

Karena:
- bukan 1 message
- tapi seluruh batch

---

# RULE PENTING

```text
false = aman
true  = bahaya
```

---

# BEST PRACTICE TAMBAHAN

1. Gunakan manual ACK

2. Gunakan retry limit

3. Gunakan DLQ

4. Gunakan prefetch

5. Gunakan idempotency

6. Pisahkan queue per domain

Contoh:

```text
payment_queue
email_queue
notification_queue
```

---

# KALAU TIDAK?

Cepat atau lambat:
- duplicate
- stuck worker
- infinite retry
- data loss
- queue bottleneck