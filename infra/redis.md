# REDIS CHEAT SHEET

---

# Apa Itu Redis?

- In-memory database
- Key-value store
- Sangat cepat
- Sering dipakai untuk cache, session, queue, dan pub/sub

## Website

```text
https://redis.io
```

---

# KENAPA REDIS CEPAT?

- Data disimpan di RAM
- Tidak perlu baca disk terus-menerus
- Single-threaded event loop
- Operasi sederhana dan ringan

---

# USE CASE REDIS

## 1. Cache

Simpan data sementara.

### Contoh

- Cache API response
- Cache query database
- Cache user profile

---

## 2. Session Store

Simpan login/session user.

### Contoh

- JWT blacklist
- Session login
- OTP temporary

---

## 3. Queue

Background job processing.

### Contoh

- Email queue
- Notification queue
- Payment processing

---

## 4. Rate Limiter

Batasi request user.

### Contoh

- Max 100 request per menit

---

## 5. Pub/Sub

Real-time messaging.

### Contoh

- Chat
- Notification
- Live update

---

# INSTALL REDIS

## Docker

```bash
docker run --name redis -p 6379:6379 redis
```

## Check Redis

```bash
redis-cli ping
```

## Result

```text
PONG
```

---

# BASIC COMMAND

## SET DATA

```bash
SET name reski
```

## GET DATA

```bash
GET name
```

## DELETE DATA

```bash
DEL name
```

## CHECK EXIST

```bash
EXISTS name
```

## EXPIRE KEY

```bash
EXPIRE name 60
```

## TTL KEY

```bash
TTL name
```

---

# STRING

## SET

```bash
SET username reski
```

## GET

```bash
GET username
```

## INCREMENT

```bash
INCR counter
```

## DECREMENT

```bash
DECR counter
```

---

# HASH

Mirip object/map.

## SET HASH

```bash
HSET user:1 name reski age 25
```

## GET HASH

```bash
HGET user:1 name
```

## GET ALL HASH

```bash
HGETALL user:1
```

---

# LIST

Mirip queue.

## PUSH DATA

```bash
LPUSH jobs job1
RPUSH jobs job2
```

## GET DATA

```bash
LPOP jobs
RPOP jobs
```

## GET ALL

```bash
LRANGE jobs 0 -1
```

---

# SET

Data unik, tidak duplicate.

## ADD

```bash
SADD tags golang redis kafka
```

## GET ALL

```bash
SMEMBERS tags
```

## CHECK EXIST

```bash
SISMEMBER tags redis
```

---

# SORTED SET

Data + score.

## ADD

```bash
ZADD ranking 100 reski
```

## GET RANK

```bash
ZRANGE ranking 0 -1 WITHSCORES
```

## Use Case

- Leaderboard
- Ranking
- Score system

---

# EXPIRE / TTL

## SET EXPIRE

```bash
SET otp 123456 EX 60
```

Artinya:
- Data otomatis hilang dalam 60 detik

## Check TTL

```bash
TTL otp
```

---

# REDIS CACHE FLOW

1. App cek Redis
2. Kalau ada -> return cache
3. Kalau tidak ada:
   - query database
   - simpan ke Redis
   - return response

---

# CONTOH CACHE API

## Tanpa Redis

- Semua request ke database

### Masalah

- Database berat
- Lambat

---

## Dengan Redis

- Data sering diambil dari memory
- Jauh lebih cepat

---

# REDIS SESSION FLOW

1. User login
2. Session disimpan di Redis
3. Request berikutnya baca Redis
4. Logout -> delete session

---

# REDIS QUEUE FLOW

## Producer

- Push job ke Redis

## Worker

- Ambil job dari Redis
- Process background

---

# PUB/SUB

## Publisher

```bash
PUBLISH chat "hello"
```

## Subscriber

```bash
SUBSCRIBE chat
```

## Use Case

- Chat realtime
- Notification
- Event system

---

# PERSISTENCE

Walau in-memory, Redis bisa simpan ke disk.

## Mode

- RDB snapshot
- AOF append only file

---

# KELEBIHAN REDIS

- Sangat cepat
- Simple
- Ringan
- Cocok untuk realtime
- Banyak dipakai production

---

# KEKURANGAN REDIS

- Data utama di RAM
- RAM mahal
- Tidak cocok untuk relational query kompleks

---

# KAPAN REDIS COCOK?

Gunakan Redis saat:
- Butuh response cepat
- Banyak repeated query
- Butuh cache
- Butuh queue
- Butuh realtime system

---

# KAPAN TIDAK COCOK?

Jangan jadikan Redis:
- Database relational utama
- Tempat query join kompleks
- Tempat simpan data besar permanen

---

# REDIS VS DATABASE

## Redis

- Super cepat
- In-memory
- Key-value
- Cache/realtime

## PostgreSQL/MySQL

- Persistent storage
- Relational query
- Source of truth

---

# TOOLS YANG SERING DIPAKAI

## Redis CLI

```bash
redis-cli
```

## Docker

```bash
redis:latest
```

## Go Redis Client

```bash
github.com/redis/go-redis/v9
```

## Laravel Redis

```bash
predis/predis
```

## Monitoring

```text
Redis Insight
```

---

# REAL CASE PRODUCTION

## 1. E-commerce

- Cache product
- Cart session
- Payment queue

---

## 2. Social Media

- Feed cache
- Notification
- Online status

---

## 3. Banking

- OTP
- Rate limiter
- Session management

---

## 4. Gaming

- Leaderboard
- Matchmaking
- Live ranking

---

# BEST PRACTICE

1. Jangan simpan data terlalu besar

2. Gunakan TTL untuk cache

3. Pisahkan cache dan persistent data

4. Monitor memory usage

5. Gunakan naming key yang rapi

## Contoh

```text
user:1
session:abc123
cache:products
```

---

# REDIS INTERVIEW QUESTION

## Q

Kenapa Redis cepat?

## A

Karena data disimpan di memory (RAM).

---

## Q

Redis cocok untuk apa?

## A

Cache, queue, session, realtime system.

---

## Q

Apa beda Redis dan MySQL?

## A

Redis untuk speed/cache.  
MySQL untuk persistent relational data.
