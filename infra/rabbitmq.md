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

# INTERVIEW QUESTION

## Q

Apa fungsi RabbitMQ?

## A

Message broker untuk asynchronous communication dan queue processing.

---

## Q

Apa beda producer dan consumer?

## A

Producer mengirim message.  
Consumer menerima dan memproses message.

---

## Q

Kenapa pakai RabbitMQ?

## A

Untuk background processing, decouple service, dan retry queue.

---

## Q

Apa itu ACK?

## A

Konfirmasi bahwa message berhasil diproses.

---

## Q

Apa itu DLQ?

## A

Dead Letter Queue untuk message gagal.
