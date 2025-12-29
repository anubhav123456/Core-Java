
---

# 📘 Atomic Variables in Java (Complete Notes)

## 🔹 What are Atomic Variables?

Atomic variables are special variables provided by Java in the
`java.util.concurrent.atomic` package.

They allow **lock-free 🔓, thread-safe 🛡️ operations** on **single variables** in a multithreaded environment.

👉 The word *atomic* means:

> An operation that is **indivisible** — it either happens completely or not at all.

---

## 🔹 Why Do We Need Atomic Variables?

### ❌ Problem with `count++` in Multithreading

`count++` is **NOT atomic**.
It internally does **3 steps**:

1. Read value
2. Increment
3. Write back

So when multiple threads run `count++`, **race conditions** occur.

---

### ❌ Using `synchronized` for Simple Operations

```java
synchronized(lock) {
    count++;
}
```

✔ Correct
❌ But **heavy and costly** for such a small operation

Issues:

* Lock acquisition & release overhead 🐢
* Thread blocking
* Context switching

---

## ✅ Solution: Atomic Variables

Atomic variables:

* Avoid explicit locking
* Use **CAS (Compare-And-Swap)** internally
* Are faster for simple operations like:

  * increment
  * decrement
  * update
  * compare-and-set

---

## 🔹 Common Atomic Classes

| Class                | Purpose                 |
| -------------------- | ----------------------- |
| `AtomicInteger`      | Atomic int operations   |
| `AtomicLong`         | Atomic long operations  |
| `AtomicBoolean`      | Atomic boolean          |
| `AtomicReference<T>` | Atomic object reference |

---

## 🔹 When Should You Use Atomic Variables?

✅ Use Atomic Variables when:

* You need **simple operations** (+, −, update)
* Variable is **shared among multiple threads**
* You want **lock-free performance**
* Logic is limited to **single-variable updates**

❌ Do NOT use when:

* Multiple variables must be updated together
* Complex business logic is involved
  👉 Use `synchronized` or `Lock` instead

---

## 🔹 AtomicInteger Example

### 📌 Code

```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicCounterExample {

    // Atomic counter initialized to 0
    private AtomicInteger counter = new AtomicInteger(0);

    // Atomic increment operation
    public void increment() {
        int newValue = counter.incrementAndGet();
        System.out.println(
            Thread.currentThread().getName() +
            " incremented counter to " + newValue
        );
    }

    public int getCounter() {
        return counter.get();
    }

    public static void main(String[] args) throws InterruptedException {

        AtomicCounterExample example = new AtomicCounterExample();
        int numberOfThreads = 10;
        int incrementsPerThread = 100;

        Thread[] threads = new Thread[numberOfThreads];

        for (int i = 0; i < numberOfThreads; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < incrementsPerThread; j++) {
                    example.increment();
                }
            }, "Thread-" + (i + 1));

            threads[i].start();
        }

        // Wait for all threads
        for (Thread t : threads) {
            t.join();
        }

        System.out.println("Final counter value: " + example.getCounter());
    }
}
```

---

### 📌 Output (Sample)

```
Thread-1 incremented counter to 1
Thread-4 incremented counter to 2
Thread-2 incremented counter to 3
...
Thread-10 incremented counter to 999
Thread-7 incremented counter to 1000
Final counter value: 1000
```

✔ Order may vary
✔ **Final value is always correct (1000)**

---

## 🔹 What Just Happened Internally?

* 10 threads × 100 increments = **1000**
* Method is **not synchronized**
* Multiple threads enter `increment()` concurrently
* `incrementAndGet()` executes **atomically**
* No race condition
* No locks
* No performance bottleneck

✨ **This is the beauty of atomic variables**

---

## 🔹 Atomic vs Volatile vs Synchronized

| Feature       | volatile | Atomic              | synchronized |
| ------------- | -------- | ------------------- | ------------ |
| Visibility    | ✅        | ✅                   | ✅            |
| Atomicity     | ❌        | ✅ (single variable) | ✅            |
| Lock-free     | ✅        | ✅                   | ❌            |
| Performance   | High     | High                | Lower        |
| Complex logic | ❌        | ❌                   | ✅            |

---

## 🔹 Key Interview Points 🚀

* `volatile` → **visibility only**
* `count++` → **not atomic**
* `AtomicInteger` → **lock-free atomic operations**
* Uses **CAS (Compare-And-Swap)**
* Best for **simple shared counters**
* Faster than `synchronized` for single-variable updates

---

## 🎯 Final Conclusion

* **Thread synchronization is essential** for correct concurrent programs
* Use:

  * `synchronized` → complex logic
  * `volatile` → visibility
  * **Atomic variables → simple, high-performance updates**
* Too much synchronization → performance issues
* Too little synchronization → subtle bugs ⚠️

⚖️ **Finding the right balance is the key to scalable concurrent programming**

---
