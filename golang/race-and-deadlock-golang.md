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

## Mutex

```go
sync.Mutex
```

## RWMutex

```go
sync.RWMutex
```

## Atomic

```go
sync/atomic
```

## WaitGroup

```go
sync.WaitGroup
```

## Channel

```go
chan int
```
