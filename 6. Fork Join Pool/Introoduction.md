
---

# Fork–Join Framework in Java (Java 7+)

## Why Fork–Join?

With modern hardware having **multiple cores** (even mobile phones), applications must utilize **parallel processing** efficiently.
Traditional multithreading was complex and error-prone. To solve this, **Java 7 introduced the Fork–Join Framework**, which simplifies parallel execution using a **divide-and-conquer** approach.

---

## Core Concept

The Fork–Join framework:

* Breaks a **large task** into **smaller subtasks**
* Executes subtasks **in parallel**
* Combines (joins) their results into a final output

This approach is similar to **Divide & Conquer algorithms** like Merge Sort.

---

## Fork–Join Pseudocode

```java
Result solve(Problem problem) {
  if (problem is small)
    directly solve problem
  else {
    split problem into independent parts
    fork new subtasks
    join results
    combine results
  }
}
```

---

## Key Terminology

### Fork

* Splits a task into **smaller subtasks**
* Subtasks are queued for parallel execution

### Join

* Waits for the completion of forked subtasks
* Combines their results

---

## Work-Stealing Algorithm

Fork–Join uses **work-stealing**:

* Each worker thread has its own deque (double-ended queue)
* Idle threads **steal tasks** from busy threads
* Improves CPU utilization and load balancing

This is what makes Fork–Join more efficient than a traditional thread pool.

---

## ForkJoinPool

* Specialized implementation of `ExecutorService`
* Uses **work-stealing**
* Designed for **CPU-intensive tasks**
* Automatically manages splitting and scheduling

### ForkJoinPool Responsibilities

1. Split tasks
2. Execute subtasks in parallel
3. Join results automatically

---

## ForkJoinPool Creation

```java
ForkJoinPool pool = new ForkJoinPool();
```

Or specify parallelism:

```java
ForkJoinPool pool = new ForkJoinPool(4);
```

> **Parallelism** = Number of worker threads (usually equals available CPU cores)

---

## Types of Fork–Join Tasks

| Class              | Returns Value |
| ------------------ | ------------- |
| `RecursiveAction`  | ❌ No          |
| `RecursiveTask<T>` | ✅ Yes         |

---

## Example: Sum of Numbers Using RecursiveAction

### Problem

Calculate the sum of numbers from `0` to `5000` using Fork–Join.

---

## Code Example

```java
package com.modernjava.threading.forkjoin;

import java.util.List;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;
import java.util.stream.Collectors;
import java.util.stream.LongStream;

public class ForkJoinSum extends RecursiveAction {

    public static long total = 0;
    private static final int CALCULATE_SUM_THRESHOLD = 5;

    private List<Long> data;

    public ForkJoinSum(List<Long> data) {
        this.data = data;
    }

    @Override
    protected void compute() {
        if (data.size() <= CALCULATE_SUM_THRESHOLD) {
            long sum = computeSumDirectly();
            System.out.println("Sum of: " + data + " is: " + sum);
        } else {
            int mid = data.size() / 2;

            ForkJoinSum firstSubTask =
                    new ForkJoinSum(data.subList(0, mid));
            ForkJoinSum secondSubTask =
                    new ForkJoinSum(data.subList(mid, data.size()));

            firstSubTask.fork();      // fork first half
            secondSubTask.compute(); // compute second half
            firstSubTask.join();     // wait for first half
        }
    }

    private long computeSumDirectly() {
        long sum = 0;
        for (Long l : data) {
            sum += l;
        }
        total += sum;
        return sum;
    }

    public static void main(String[] args) {
        int end = 5000;

        List<Long> data =
                LongStream.rangeClosed(0, end)
                          .boxed()
                          .collect(Collectors.toList());

        ForkJoinPool pool = new ForkJoinPool();
        System.out.println("Pool Parallelism: " + pool.getParallelism());

        ForkJoinSum task = new ForkJoinSum(data);
        pool.invoke(task);

        System.out.println(
            "Total is: " + total +
            " and correct sum calculated using stream sum is: " +
            LongStream.rangeClosed(0, end).sum()
        );
    }
}
```

---

## Sample Output

```text
Pool Parallelism: 8
Sum of: [0, 1, 2, 3, 4] is: 10
Sum of: [5, 6, 7, 8, 9] is: 35
Sum of: [10, 11, 12, 13, 14] is: 60
...
Total is: 12502500 and correct sum calculated using stream sum is: 12502500
```

*(Exact ordering may vary due to parallel execution)*

---

## Important Notes & Best Practices

### ✅ Advantages

* Efficient CPU utilization
* Automatic load balancing
* Ideal for **recursive and CPU-bound tasks**

### ⚠️ Cautions

* Avoid shared mutable state (`static total` is unsafe in real apps)
* Use `RecursiveTask` for safer result aggregation
* Not suitable for I/O-heavy tasks

---

## When to Use Fork–Join?

✔ Large computational problems
✔ Recursive algorithms
✔ CPU-intensive workloads

❌ Simple parallel tasks
❌ I/O-bound operations

---
