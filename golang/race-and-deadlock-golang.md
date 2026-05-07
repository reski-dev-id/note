# GO RACE CONDITION & DEADLOCK CHEAT SHEET

---

# RACE CONDITION

## Apa Itu?

- Terjadi saat banyak goroutine mengakses data yang sama
- Secara bersamaan
- Tanpa synchronization yang aman

## Akibat

- Data corrupt
- Hasil random
- Data overwrite
- Bug sulit dilacak

---

# CONTOH RACE CONDITION

## Bad Example

```go
package main

import (
    "fmt"
    "time"
)

var counter int

func increment() {
    for i := 0; i < 1000; i++ {
        counter++
    }
}

func main() {
    go increment()
    go increment()

    time.Sleep(time.Second)

    fmt.Println(counter)
}
```

## Kenapa Race?

`counter++` bukan operasi tunggal.

Sebenarnya ada proses:

1. Read
2. Increment
3. Write

Kalau 2 goroutine jalan bersamaan:
- Bisa overwrite data

## Hasil

```text
1998
1987
2000
```

Bisa random.

---

# CASE YANG SERING TERJADI RACE CONDITION

## 1. Shared Counter

Multiple goroutine update counter bersamaan.

### Contoh

```go
visitorCount++
stock--
totalRequest++
```

---

## 2. Shared Map

Multiple goroutine read/write map.

### Contoh

```go
users[id] = user
```

### Error yang sering muncul

```text
fatal error: concurrent map writes
```

---

## 3. Shared Slice / Array

Multiple append bersamaan.

### Contoh

```go
results = append(results, data)
```

### Bisa menyebabkan

- Data hilang
- Memory corruption
- Duplicate data

---

# 3 CONTOH REAL CASE RACE CONDITION

## CASE 1 - HIT COUNTER API

```go
var totalRequest int

func handler() {
    totalRequest++
}
```

### Masalah

- Banyak request masuk bersamaan
- Counter jadi tidak akurat

---

## CASE 2 - ECOMMERCE STOCK

```go
var stock int = 5

func buy() {
    stock--
}
```

### Masalah

- 2 user checkout bersamaan
- Stock bisa minus
- Overselling

---

## CASE 3 - CACHE / SESSION MAP

```go
var sessions = map[string]string{}

func saveSession(id string, token string) {
    sessions[id] = token
}
```

### Masalah

- Multiple goroutine write map
- Bisa panic:

```text
concurrent map writes
```

---

# DETECT RACE CONDITION

Gunakan:

```bash
go run -race main.go
```

Atau:

```bash
go test -race
```

---

# FIX RACE CONDITION

## Menggunakan Mutex

```go
package main

import (
    "fmt"
    "sync"
)

var (
    counter int
    mu sync.Mutex
)

func increment() {
    for i := 0; i < 1000; i++ {
        mu.Lock()
        counter++
        mu.Unlock()
    }
}

func main() {
    var wg sync.WaitGroup

    wg.Add(2)

    go func() {
        defer wg.Done()
        increment()
    }()

    go func() {
        defer wg.Done()
        increment()
    }()

    wg.Wait()

    fmt.Println(counter)
}
```

---

# DEADLOCK

## Apa Itu?

- Goroutine saling menunggu
- Tidak ada yang bisa lanjut
- Program berhenti total

---

# CONTOH DEADLOCK CHANNEL

```go
package main

func main() {
    ch := make(chan int)

    ch <- 1
}
```

## Error

```text
fatal error: all goroutines are asleep - deadlock!
```

## Kenapa?

- Tidak ada receiver

---

# CONTOH DEADLOCK MUTEX

```go
package main

import "sync"

func main() {
    var mu sync.Mutex

    mu.Lock()
    mu.Lock()
}
```

## Kenapa?

- Mutex sudah terkunci
- Lock lagi sebelum unlock

---

# CONTOH DEADLOCK WAITGROUP

```go
package main

import (
    "sync"
)

func main() {
    var wg sync.WaitGroup

    wg.Add(1)

    go func() {
        wg.Wait()
    }()

    wg.Wait()
}
```

## Kenapa?

- Semua saling menunggu
- Tidak ada Done()

---

# PENYEBAB DEADLOCK

## Channel

- Tidak ada sender
- Tidak ada receiver

## Mutex

- Double lock
- Lupa unlock

## WaitGroup

- Add dan Done tidak balance

---

# FIX DEADLOCK

## Channel

Pastikan:
- Sender ada
- Receiver ada

## Mutex

Gunakan:

```go
mu.Lock()
defer mu.Unlock()
```

## WaitGroup

Pastikan:
- Semua Add punya Done

---

# PERBEDAAN

| Race Condition | Deadlock |
|---|---|
| Data rusak/random | Program berhenti |
| Program tetap jalan | Semua goroutine stuck |
| Masalah synchronization data | Masalah blocking |

---

# ANALOGI

## Race Condition

2 orang edit file bersamaan.

## Deadlock

2 orang saling tunggu buka pintu.

---

# BEST PRACTICE

1. Shared data -> gunakan mutex

2. Gunakan:

```bash
go run -race
```

3. Jangan lock tanpa unlock

4. Gunakan WaitGroup dengan benar

5. Hindari blocking tanpa receiver

---

# TOOLS YANG SERING DIPAKAI

---

# MUTEX

## Package

```go
sync.Mutex
```

## Fungsi

- Mengunci akses data
- Hanya 1 goroutine boleh akses data dalam satu waktu

## Kapan Dipakai?

Gunakan saat:
- Banyak goroutine write/read shared variable
- Critical section
- Shared counter
- Shared map
- Shared struct

## Cocok Untuk

- Counter
- Stock
- Balance
- Session cache
- Shared memory

---

## Contoh Benar

```go
package main

import (
    "fmt"
    "sync"
)

var (
    counter int
    mu sync.Mutex
)

func increment(wg *sync.WaitGroup) {
    defer wg.Done()

    mu.Lock()
    counter++
    mu.Unlock()
}

func main() {
    var wg sync.WaitGroup

    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go increment(&wg)
    }

    wg.Wait()

    fmt.Println(counter)
}
```

## Kenapa Benar?

- Hanya 1 goroutine boleh update counter
- Tidak ada race condition

---

# RWMUTEX

## Package

```go
sync.RWMutex
```

## Fungsi

- Pisahkan read lock dan write lock
- Banyak reader boleh bersamaan
- Writer tetap exclusive

## Kapan Dipakai?

Gunakan saat:
- Read jauh lebih banyak daripada write
- Cache system
- Config data
- In-memory data store

## Cocok Untuk

- Cache
- Config
- Session store
- Read-heavy system

---

## Contoh Benar

```go
package main

import (
    "fmt"
    "sync"
)

var (
    data = map[string]string{
        "name": "reski",
    }

    mu sync.RWMutex
)

func read() {
    mu.RLock()
    fmt.Println(data["name"])
    mu.RUnlock()
}

func write() {
    mu.Lock()
    data["name"] = "golang"
    mu.Unlock()
}

func main() {
    go read()
    go read()
    go write()
}
```

## Kenapa Pakai RWMutex?

- Reader bisa paralel
- Lebih cepat dibanding Mutex biasa untuk read-heavy workload

---

# ATOMIC

## Package

```go
sync/atomic
```

## Fungsi

- Operasi low-level thread-safe
- Tanpa mutex
- Sangat cepat

## Kapan Dipakai?

Gunakan saat:
- Counter sederhana
- Increment/decrement
- Flag/status
- Statistik ringan

## Cocok Untuk

- Hit counter
- Request counter
- Boolean flag
- Metrics

## Tidak Cocok Untuk

- Logic kompleks
- Multiple variable transaction
- Shared object besar

---

## Contoh Benar

```go
package main

import (
    "fmt"
    "sync"
    "sync/atomic"
)

var counter int64

func increment(wg *sync.WaitGroup) {
    defer wg.Done()

    atomic.AddInt64(&counter, 1)
}

func main() {
    var wg sync.WaitGroup

    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go increment(&wg)
    }

    wg.Wait()

    fmt.Println(counter)
}
```

## Kenapa Atomic?

- Lebih ringan dibanding mutex
- Cocok untuk operasi sederhana

---

# WAITGROUP

## Package

```go
sync.WaitGroup
```

## Fungsi

- Menunggu semua goroutine selesai

## Kapan Dipakai?

Gunakan saat:
- Multiple goroutine paralel
- Batch processing
- Worker selesai semua dulu
- Parallel API call

## Cocok Untuk

- Fan-out goroutine
- Concurrent processing
- Background task synchronization

---

## Contoh Benar

```go
package main

import (
    "fmt"
    "sync"
)

func worker(id int, wg *sync.WaitGroup) {
    defer wg.Done()

    fmt.Println("worker", id)
}

func main() {
    var wg sync.WaitGroup

    for i := 1; i <= 3; i++ {
        wg.Add(1)

        go worker(i, &wg)
    }

    wg.Wait()

    fmt.Println("semua selesai")
}
```

## Kenapa Pakai WaitGroup?

- Main goroutine menunggu semua worker selesai
- Tanpa ini program bisa exit terlalu cepat

---

# CHANNEL

## Package

```go
chan int
```

## Fungsi

- Komunikasi antar goroutine
- Transfer data safely

## Kapan Dipakai?

Gunakan saat:
- Worker queue
- Pipeline
- Event streaming
- Signaling
- Async communication

## Cocok Untuk

- Job queue
- Kafka consumer
- Event processing
- Producer-consumer

---

## Contoh Benar

```go
package main

import "fmt"

func worker(ch chan string) {
    ch <- "job selesai"
}

func main() {
    ch := make(chan string)

    go worker(ch)

    result := <-ch

    fmt.Println(result)
}
```

## Kenapa Pakai Channel?

- Aman untuk komunikasi data
- Tidak perlu shared memory langsung

---

# PERBANDINGAN CEPAT

| Tool | Cocok Untuk |
|---|---|
| Mutex | Shared data write |
| RWMutex | Read-heavy shared data |
| Atomic | Counter sederhana |
| WaitGroup | Tunggu goroutine selesai |
| Channel | Komunikasi antar goroutine |

---

# RULE UMUM

## Mutex
- Jangan lupa Unlock()

## RWMutex
- Gunakan jika read lebih banyak dari write

## Atomic
- Hanya untuk operasi sederhana

## WaitGroup
- Semua Add() wajib punya Done()

## Channel
- Jangan kirim tanpa receiver
- Hindari deadlock