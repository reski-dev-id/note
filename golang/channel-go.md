# GO CHANNEL CHEAT SHEET

---

# UNBUFFERED CHANNEL

## Create

```go
ch := make(chan int)
```

## Karakteristik

- Tidak punya buffer
- Sender dan receiver harus siap bersamaan
- Cocok untuk synchronization dan signaling

## Block Conditions

- Sender block jika belum ada receiver
- Receiver block jika belum ada sender

## Flow

```text
Sender <--> Receiver
```

## Contoh Basic

```go
package main

import "fmt"

func main() {
    ch := make(chan int)

    go func() {
        ch <- 10
    }()

    value := <-ch

    fmt.Println(value)
}
```

## Penjelasan

- Goroutine mengirim angka 10
- Akan menunggu sampai ada receiver
- Main goroutine menerima data
- Program lanjut

---

## Contoh Deadlock

```go
package main

func main() {
    ch := make(chan int)

    ch <- 1
}
```

## Error

```text
fatal error: all goroutines are asleep - deadlock
```

## Kenapa?

- Tidak ada receiver
- Sender terus menunggu

---

## Use Case

- Signal selesai
- Sinkronisasi goroutine
- Notify antar process

```go
done := make(chan bool)
```

---

# BUFFERED CHANNEL

## Create

```go
ch := make(chan int, 2)
```

Angka `2` = kapasitas buffer

## Karakteristik

- Punya buffer/antrian internal
- Sender bisa kirim tanpa receiver langsung
- Cocok untuk queue dan worker pool

## Block Conditions

- Sender block jika buffer penuh
- Receiver block jika buffer kosong

## Flow

```text
Sender -> Buffer -> Receiver
```

---

## Contoh Basic

```go
package main

import "fmt"

func main() {
    ch := make(chan int, 2)

    ch <- 1
    ch <- 2

    fmt.Println(<-ch)
    fmt.Println(<-ch)
}
```

## Penjelasan

- Data masuk ke buffer
- Tidak langsung butuh receiver
- Receiver bisa baca nanti

---

## Contoh Buffer Full

```go
package main

func main() {
    ch := make(chan int, 2)

    ch <- 1
    ch <- 2
    ch <- 3
}
```

## Kenapa block?

- Buffer hanya muat 2 data
- Data ke-3 harus menunggu

---

## Use Case

- Worker pool
- Job queue
- Event pipeline
- Kafka consumer
- Background task

```go
jobs := make(chan Job, 100)
```

---

# PERBANDINGAN

| Unbuffered | Buffered |
|---|---|
| Tidak ada queue | Ada queue |
| Full synchronous | Semi asynchronous |
| Direct handoff | Ada temporary storage |
| Cocok untuk sync | Cocok untuk throughput tinggi |

---

# ANALOGI

## Unbuffered

- Telepon
- Pengirim & penerima harus aktif bersamaan

## Buffered

- Email inbox
- Pengirim bisa kirim dulu
- Penerima baca nanti

---

# RULE PENTING

1. Jangan lupa receiver  
   Kalau tidak -> deadlock

2. Buffered channel tetap bisa block  
   Kalau buffer penuh

3. Close channel hanya dari sender

4. Channel cocok untuk komunikasi data  
   Mutex cocok untuk shared memory kompleks

---

# CLOSE CHANNEL

## Close

```go
close(ch)
```

Digunakan untuk memberi tahu:

```text
"Tidak ada data lagi"
```

## Contoh

```go
v, ok := <-ch
```

- `ok = true`  -> masih ada data
- `ok = false` -> channel sudah close

---

# RANGE CHANNEL

## Contoh

```go
for v := range ch {
    fmt.Println(v)
}
```

Loop otomatis berhenti saat channel di-close
