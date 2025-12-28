---

# ✅ **Java Multithreading**

---

## 1. `join()` Method in Java

### What is `join()`?

* The `join()` method allows **one thread to wait for the completion of another thread**.
* When a thread calls `join()` on another thread:

  * The **calling thread goes into waiting state**.
  * It waits until the referenced thread **terminates**.

### Important Points

* If the referenced thread is **blocked**, `join()` keeps waiting → calling thread becomes **non‑responsive**.
* Java provides **overloaded versions** of `join()` with timeout:

  * `join(long millis)`
  * `join(long millis, int nanos)`
* `join()` throws `InterruptedException` if the waiting thread is interrupted.
* If the referenced thread:

  * **Cannot start**, or
  * **Has already terminated**
    → `join()` returns immediately.

### Example Code

```java
public class Main {

    public static void main(String[] args) throws InterruptedException {
        System.out.println("Main Thread starts");

        Thread thread = new Thread(() -> {
            System.out.println("User Thread starts");
        });

        thread.start();
        thread.join();

        System.out.println("User Thread ends");
        System.out.println("Main Thread Ends");
    }
}
```

### Output

```
Main Thread starts
User Thread starts
User Thread ends
Main Thread Ends
```

---

## 2. Parallel Programming using `Runnable` Interface

We calculate the **sum of numbers from 0 to 5000** using two threads.

### Expected Correct Sum

```
0 + 1 + 2 + ... + 5000 = 12,502,500
```

---

## Way 1: Using `Runnable` Classes

### Code

```java
import java.util.stream.IntStream;

public class Main {
    public static int[] numbers = IntStream.rangeClosed(0, 5000).toArray();
    public static int sum = 0;
    public static int total = IntStream.rangeClosed(0, 5000).sum();

    public static void main(String[] args) throws InterruptedException {
        Worker1Parallel worker1 = new Worker1Parallel(numbers);
        Worker2Parallel worker2 = new Worker2Parallel(numbers);

        Thread thread1 = new Thread(worker1);
        Thread thread2 = new Thread(worker2);

        thread1.start();
        thread2.start();

        thread1.join();
        thread2.join();

        System.out.println("Sum of 5000 integers in parallel is : " + sum);
        System.out.println("Sum of 5000 integers from IntStream sum is : " + total);
    }

    public static void add(int x) {
        sum += x;
    }
}

class Worker1Parallel implements Runnable {
    int array[], sum;

    public Worker1Parallel(int array[]) {
        this.array = array;
        sum = 0;
    }

    @Override
    public void run() {
        for (int i = 0; i < array.length / 2; i++) {
            sum += array[i];
        }
        Main.add(sum);
    }
}

class Worker2Parallel implements Runnable {
    int array[], sum;

    public Worker2Parallel(int array[]) {
        this.array = array;
        sum = 0;
    }

    @Override
    public void run() {
        for (int i = array.length / 2; i < array.length; i++) {
            sum += array[i];
        }
        Main.add(sum);
    }
}
```

---

## Way 2: Using Lambda Expressions

### Code

```java
import java.util.stream.IntStream;

public class Main {
    public static int[] numbers = IntStream.rangeClosed(0, 5000).toArray();
    public static int sum = 0;
    public static int total = IntStream.rangeClosed(0, 5000).sum();

    public static void main(String[] args) throws InterruptedException {
        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < numbers.length / 2; i++) {
                add(numbers[i]);
            }
        });

        Thread thread2 = new Thread(() -> {
            for (int i = numbers.length / 2; i < numbers.length; i++) {
                add(numbers[i]);
            }
        });

        thread1.start();
        thread2.start();

        thread1.join();
        thread2.join();

        System.out.println("Sum of 5000 integers in parallel is : " + sum);
        System.out.println("Sum of 5000 integers from IntStream sum is : " + total);
    }

    public static void add(int x) {
        sum += x;
    }
}
```

### Output (Wrong)

```
Sum of 5000 integers in parallel is : 10778041
Sum of 5000 integers from IntStream sum is : 12502500
```

---

## 3. The Problem: Race Condition & Lost Update

### Why is the result wrong?

* `sum += x` is **NOT atomic**.
* It internally does:

  1. Read `sum`
  2. Add `x`
  3. Write back to `sum`

When two threads execute this simultaneously:

* Both read the **same old value** of `sum`
* Both update it
* One update **overwrites** the other

This is called **Lost Update Problem**.

---

## 4. Solution 1: Using `synchronized`

### Code

```java
import java.util.stream.IntStream;

public class Main {
    public static int[] numbers = IntStream.rangeClosed(0, 5000).toArray();
    public static int sum = 0;
    public static int total = IntStream.rangeClosed(0, 5000).sum();

    public static void main(String[] args) throws InterruptedException {
        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < numbers.length / 2; i++) {
                add(numbers[i]);
            }
        });

        Thread thread2 = new Thread(() -> {
            for (int i = numbers.length / 2; i < numbers.length; i++) {
                add(numbers[i]);
            }
        });

        thread1.start();
        thread2.start();

        thread1.join();
        thread2.join();

        System.out.println("Sum of 5000 integers in parallel is : " + sum);
        System.out.println("Sum of 5000 integers from IntStream sum is : " + total);
    }

    public static synchronized void add(int x) {
        sum += x;
    }
}
```

### Output (Correct)

```
Sum of 5000 integers in parallel is : 12502500
Sum of 5000 integers from IntStream sum is : 12502500
```

---

## 5. `volatile` Keyword in Java

### What problem does `volatile` solve?

* **Visibility problem**, not race condition.
* Threads may cache variables locally → stale values.
* `volatile` ensures:

  * Always read from **main memory (RAM)**
  * Always write to **main memory (RAM)**

### Important Rules

* `volatile` can be applied to **variables only**
* Methods & classes **cannot** be volatile
* Read/write of volatile variables is **atomic**
* `volatile` does **NOT make compound operations atomic**

---

## Volatile in Double‑Checked Locking (Singleton)

```java
public class Singleton {

    private static volatile Singleton instance;

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null)
                    instance = new Singleton();
            }
        }
        return instance;
    }
}
```

---

## Volatile Example: Visibility Issue

### Without `volatile`

```java
public class Main {
    public static boolean flag = false;

    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("The value of i from first thread " + i);
            }
            flag = true;
        });

        Thread thread2 = new Thread(() -> {
            int i = 1;
            while (!flag) {
                i++;
            }
            System.out.println("The value of i from second thread " + i);
        });

        thread1.start();
        thread2.start();
    }
}
```

### Output (Thread 2 may never finish)

```
The value of i from first thread 1
The value of i from first thread 2
The value of i from first thread 3
The value of i from first thread 4
The value of i from first thread 5
```

---

### With `volatile`

```java
public class Main {
    public static volatile boolean flag = false;

    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("The value of i from first thread " + i);
            }
            flag = true;
        });

        Thread thread2 = new Thread(() -> {
            int i = 1;
            while (!flag) {
                i++;
            }
            System.out.println("The value of i from second thread " + i);
        });

        thread1.start();
        thread2.start();
    }
}
```

### Output

```
The value of i from first thread 1
The value of i from first thread 2
The value of i from first thread 3
The value of i from first thread 4
The value of i from first thread 5
The value of i from second thread 6510501
```

---

## Volatile is NOT a Replacement for `synchronized`

### Example

```java
import java.util.stream.IntStream;

public class Main {
    public static int[] numbers = IntStream.rangeClosed(0, 5000).toArray();
    public static volatile int sum = 0;
    public static int total = IntStream.rangeClosed(0, 5000).sum();

    public static void main(String[] args) throws InterruptedException {
        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < numbers.length / 2; i++) {
                add(numbers[i]);
            }
        });

        Thread thread2 = new Thread(() -> {
            for (int i = numbers.length / 2; i < numbers.length; i++) {
                add(numbers[i]);
            }
        });

        thread1.start();
        thread2.start();

        thread1.join();
        thread2.join();

        System.out.println("Sum of 5000 integers in parallel is : " + sum);
        System.out.println("Sum of 5000 integers from IntStream sum is : " + total);
    }

    public static void add(int x) {
        sum += x;
    }
}
```

### Result

* Still **wrong output possible** due to race condition

---

## Final Summary

| Concept        | Solves Visibility | Solves Race Condition |
| -------------- | ----------------- | --------------------- |
| `volatile`     | ✅ Yes             | ❌ No                  |
| `synchronized` | ✅ Yes             | ✅ Yes                 |

### Rule of Thumb

* **One writer, many readers → `volatile`**
* **Multiple writers → `synchronized` / Locks / Atomic classes**

---


