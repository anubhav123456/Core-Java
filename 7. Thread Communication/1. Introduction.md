
---

# Thread Communication in Java

## `wait()` and `notify()`

---

## 1. Why do we need Thread Communication?

When multiple threads are working on a **shared resource**, sometimes one thread must **pause execution** until another thread completes a specific task.

👉 This coordination between threads is called **Inter-Thread Communication**.

Java provides:

* `wait()`
* `notify()`
* `notifyAll()`

These methods help threads **communicate safely** using a shared lock.

---

## 2. Real-Life Analogy (Room & Parcel Example)

* There is **one room** containing parcels.
* Only **one person (thread)** can enter at a time.
* The **security guard (lock)** controls entry.
* If the room is occupied:

  * Others must **wait**
* When the person exits:

  * The security **notifies** waiting people
* Another person enters and repeats the process

➡️ This is exactly how `wait()` and `notify()` work with **synchronized blocks**.

---

## 3. Key Concepts

### 🔒 Lock

* A shared object used for synchronization
* Only one thread can hold the lock at a time

### ⏸ `wait()`

* Causes the **current thread to pause**
* Releases the lock
* Thread enters **WAITING state**

### 🔔 `notify()`

* Wakes up **one waiting thread**
* Does **not** release the lock immediately
* Lock is released only after exiting synchronized block

### 📢 `notifyAll()`

* Wakes **all waiting threads**
* Only one will acquire the lock at a time

---

## 4. Important Rules (Very Important for Interviews ❗)

1. `wait()` and `notify()` **must be called inside synchronized block**
2. They work on the **same lock object**
3. `wait()` releases the lock, `sleep()` does NOT
4. After `notify()`, execution continues **inside the same synchronized block**

---

## 5. Code Example: `wait()` and `notify()`

### Java Code

```java
public class Main {

    private static final Object lock = new Object();

    public static void main(String[] args) {

        Thread t1 = new Thread(() -> {
            try {
                one();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        Thread t2 = new Thread(() -> {
            try {
                two();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        t1.start();
        t2.start();
    }

    private static void one() throws InterruptedException {
        synchronized (lock) {
            System.out.println("Hello from method one...");
            lock.wait();
            System.out.println("Back again in method one.");
        }
    }

    private static void two() throws InterruptedException {
        synchronized (lock) {
            System.out.println("Hello from method two...");
            lock.notify();
            System.out.println("Hello from method two after notifying");
        }
    }
}
```

---

## 6. Output

```
Hello from method one...
Hello from method two...
Hello from method two after notifying
Back again in method one.
```

---

## 7. Step-by-Step Execution Flow

1. **Thread t1 starts**

   * Acquires lock
   * Prints: `Hello from method one...`
   * Calls `wait()`
   * Releases lock and enters WAITING state

2. **Thread t2 starts**

   * Acquires lock
   * Prints: `Hello from method two...`
   * Calls `notify()`
   * Prints: `Hello from method two after notifying`
   * Releases lock

3. **Thread t1 resumes**

   * Re-acquires lock
   * Prints: `Back again in method one.`

---

## 8. `wait()` vs `sleep()` (Very Common Question)

| Feature                     | `wait()`       | `sleep()`      |
| --------------------------- | -------------- | -------------- |
| Releases lock               | ✅ Yes          | ❌ No           |
| Used for communication      | ✅ Yes          | ❌ No           |
| Belongs to                  | `Object` class | `Thread` class |
| Requires synchronized block | ✅ Yes          | ❌ No           |

---

## 9. Variants of `wait()`

```java
lock.wait();        // waits indefinitely
lock.wait(1000);    // waits for 1 second
```

* Thread wakes up when:

  * `notify()` / `notifyAll()` is called
  * Timeout expires
  * Thread is interrupted

---

## 10. `notify()` vs `notifyAll()`

| Method        | Behavior                      |
| ------------- | ----------------------------- |
| `notify()`    | Wakes **one** waiting thread  |
| `notifyAll()` | Wakes **all** waiting threads |

👉 Used commonly in **Producer–Consumer problems**

---

## 11. When to Use `wait()` and `notify()`?

* Producer–Consumer problem
* Thread coordination
* Resource sharing
* Task dependency handling

---

## 12. Summary

* `wait()` pauses thread and releases lock
* `notify()` wakes waiting thread(s)
* Must be used with `synchronized`
* Enables **safe and efficient inter-thread communication**

---
