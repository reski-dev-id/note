# GO ROUTINE CHEAT SHEET

---

# GO ROUTINE

## Apa Itu Goroutine?

Goroutine adalah lightweight thread milik Go.

Digunakan untuk menjalankan function secara concurrent tanpa harus membuat thread manual seperti di bahasa lain.

Go runtime akan mengatur scheduling goroutine secara otomatis.

---

# CREATE GOROUTINE

## Basic Syntax

```go
go myFunction()
```

## Contoh

```go
package main

import (
    "fmt"
    "time"
)

func hello() {
    fmt.Println("hello from goroutine")
}

func main() {
    go hello()

    time.Sleep(time.Second)
}
```

---

# KARAKTERISTIK

- Sangat ringan
- Bisa membuat ribuan goroutine
- Dikelola oleh Go runtime
- Berjalan secara concurrent
- Tidak perlu manage thread manual

---

# CARA KERJA

## Normal Function

```text
main -> function A -> selesai -> function B
```

Semua berjalan berurutan.

---

## Goroutine

```text
main
 ├── goroutine A
 ├── goroutine B
 └── goroutine C
```

Semua bisa berjalan bersamaan.

---

# KEUNGGULAN

## 1. Lightweight

Goroutine sangat kecil dibanding thread OS.

```text
Thread OS   -> MB
Goroutine   -> KB
```

---

## 2. Mudah Digunakan

Cukup tambah keyword:

```go
go
```

---

## 3. High Concurrency

Bisa handle:
- ribuan request
- banyak background task
- worker pool

---

## 4. Scheduler Otomatis

Go runtime mengatur:
- scheduling
- stack
- memory
- execution

---

## 5. Cocok Untuk Backend System

Sangat cocok untuk:
- API
- microservice
- queue consumer
- realtime processing

---

# KELEMAHAN

## 1. Race Condition

Multiple goroutine akses data bersamaan.

```go
counter++
```

Bisa menghasilkan data tidak konsisten.

---

## 2. Deadlock

Goroutine saling menunggu.

```text
fatal error: all goroutines are asleep - deadlock
```

---

## 3. Memory Leak

Goroutine yang tidak selesai bisa menumpuk.

Contoh:
- channel tidak di-close
- infinite loop
- blocked forever

---

## 4. Debugging Lebih Sulit

Concurrent code lebih sulit dianalisa dibanding synchronous code.

---

## 5. Shared Memory Complexity

Harus hati-hati dengan:
- mutex
- channel
- synchronization

---

# CARA PENGGUNAAN

## Basic Goroutine

```go
package main

import (
    "fmt"
    "time"
)

func worker() {
    fmt.Println("working...")
}

func main() {
    go worker()

    time.Sleep(time.Second)
}
```

---

# PENJELASAN

- `go worker()` menjalankan function secara concurrent
- Main function tidak menunggu otomatis
- `time.Sleep()` digunakan agar program tidak langsung selesai

---

# MULTIPLE GOROUTINE

## Contoh

```go
package main

import (
    "fmt"
    "time"
)

func task(id int) {
    fmt.Println("task", id)
}

func main() {
    for i := 1; i <= 5; i++ {
        go task(i)
    }

    time.Sleep(time.Second)
}
```

---

# FLOW

```text
main
 ├── task 1
 ├── task 2
 ├── task 3
 ├── task 4
 └── task 5
```

---

# 5 USE CASE GOROUTINE

# 1. HTTP Request Parallel

## Contoh

```go
go fetchUser()
go fetchOrder()
go fetchPayment()
```

## Digunakan Untuk

- mempercepat API aggregation
- parallel external API call

---

# 2. Worker Pool

## Contoh

```go
go worker(jobs)
```

## Digunakan Untuk

- background processing
- email sender
- image processing

---

# 3. Kafka / RabbitMQ Consumer

## Contoh

```go
go consumeMessage()
```

## Digunakan Untuk

- async event processing
- queue consumer

---

# 4. Scheduled Background Task

## Contoh

```go
go cleanupExpiredSession()
```

## Digunakan Untuk

- cron process
- cleanup service
- monitoring

---

# 5. Realtime System

## Contoh

```go
go handleWebsocket()
```

## Digunakan Untuk

- websocket
- chat app
- realtime notification

---

# WAITGROUP

## Problem

Main goroutine bisa selesai lebih cepat.

---

## Solution

Gunakan `sync.WaitGroup`

---

## Contoh

```go
package main

import (
    "fmt"
    "sync"
)

func worker(wg *sync.WaitGroup) {
    defer wg.Done()

    fmt.Println("working")
}

func main() {
    var wg sync.WaitGroup

    wg.Add(1)

    go worker(&wg)

    wg.Wait()
}
```

---

# TIPS & TRIK

## 1. Jangan Buat Goroutine Tanpa Limit

Salah:

```go
for {
    go process()
}
```

Bisa menyebabkan:
- memory explosion
- CPU overload

Gunakan:
- worker pool
- semaphore
- buffered channel

---

## 2. Selalu Pakai WaitGroup Untuk Synchronization

Agar program tidak selesai terlalu cepat.

---

## 3. Gunakan Context Untuk Cancelation

```go
ctx, cancel := context.WithCancel(context.Background())
```

Cocok untuk:
- timeout
- graceful shutdown
- request cancellation

---

## 4. Hindari Shared Variable

Salah:

```go
counter++
```

Gunakan:
- mutex
- atomic
- channel

---

## 5. Gunakan Race Detector

```bash
go run -race main.go
```

Untuk mendeteksi:
- race condition
- unsafe concurrent access

---

# BEST PRACTICE

## Gunakan Goroutine Untuk

- IO operation
- API call
- background process
- async processing

---

## Jangan Gunakan Goroutine Untuk

- operasi sangat kecil
- CPU kecil dan cepat
- code yang tidak butuh concurrency

Karena goroutine juga punya overhead.

---

# ANALOGI

## Normal Function

1 kasir melayani:
- customer 1
- customer 2
- customer 3

Berurutan.

---

## Goroutine

Banyak kasir melayani customer secara bersamaan.

---

# COMMON ERROR

## Program Langsung Exit

```go
go hello()
```

Main selesai sebelum goroutine selesai.

---

## Fix

Gunakan:
- WaitGroup
- channel
- synchronization

---

# INTERVIEW NOTES

## Pertanyaan Umum

### Apa bedanya goroutine vs thread?

| Goroutine | Thread |
|---|---|
| Lightweight | Heavy |
| Managed by Go runtime | Managed by OS |
| KB memory | MB memory |
| Faster create | Slower create |

---

### Apa itu concurrency?

```text
Mengerjakan banyak task dalam waktu bersamaan
```

Tidak selalu parallel.

---

### Apa bedanya concurrency vs parallelism?

| Concurrency | Parallelism |
|---|---|
| Banyak task bergantian | Banyak task benar-benar bersamaan |
| Fokus task management | Fokus execution |
| Bisa 1 core | Butuh multiple core |
