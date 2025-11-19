
---

# 📘 **Notes: Introduction to Arrays in Java**

## ✅ **Why Arrays?**

* Variables hold **single values**, but many real-world problems require storing **multiple values together**.
* Example: Storing roll numbers or student objects in a college software.
* Using many separate variables (`student1`, `student2`, …) is **cumbersome** and **not dynamic**.
* Students may join/leave at runtime → normal variables cannot handle this well.

## 🧱 **What is an Array?**

* A **data structure** that stores **multiple items of the same type** in **contiguous memory**.
* Almost all languages support arrays.
* Java provides arrays as **objects**, unlike C/C++ where arrays are more like raw memory blocks.

---

# 🧩 **Arrays in Java**

* Arrays in Java are **objects** ➝ array variables store a **reference** to the array in heap.
* They have **members**, most importantly:

  * `length` → gives the size of the array.
* Much easier than C/C++ where you manually track size using `sizeof`.

---

# 🔢 **Indexing**

* Array items are accessed using **indexes**.
* Indexing starts from **0**.

  * Example: In `[10, 20, 30, 40]`

    * `a[0] = 10`
    * `a[1] = 20`
    * `a[2] = 30`
    * `a[3] = 40`

---

# ✏️ **Modifying Array Values**

You can modify an array value using its index:

```java
a[2] = 50;   // replaces 30 with 50
```

---

# ⚠️ **Bounds Checking**

* Java checks array bounds at runtime.
* Accessing an invalid index throws:

  ```
  ArrayIndexOutOfBoundsException
  ```
* C/C++ do **not** check bounds → can cause crashes or random values.

---

# 🛠️ **Ways to Create Arrays in Java**

### **1️⃣ Declaration + Memory Allocation + Later Initialization**

```java
int[] a = new int[3];
a[0] = 10;
a[1] = 20;
a[2] = 30;
```

### **2️⃣ Declaration + Allocation (together) + Assignment**

```java
int[] a = new int[3];
a[0] = 10;
a[1] = 20;
a[2] = 30;
```

(Same as above but shown as a single step)

### **3️⃣ Declaration + Initialization (most compact)**

```java
int[] a = {10, 20, 30};
```

* Easy but not commonly used in real applications because data often comes from user/file.

### **4️⃣ Using a Loop for Initialization**

```java
int[] a = new int[3];
for (int i = 0; i < a.length; i++) {
    a[i] = (i + 1) * 10; // 10, 20, 30
}
```

* Useful when:

  * Values follow a pattern.
  * Reading from user input.
  * Reading from files.

---

# 🧠 **Memory Model**

* Array variable → stored in **stack** (reference).
* Actual array → stored in **heap** (object).
* Java automatically runs **garbage collection** → no need to manually free memory.

---

# 💡 **Key Points Summary**

* Arrays store **multiple values of the same type** together.
* Java arrays are **objects** with a `length` property.
* Indexing starts from **0**.
* Accessing invalid index → **exception**.
* Four main ways to create arrays.
* Loops commonly used for reading/writing array values.
* Arrays are fixed-size once created.

---

If you want, I can also create:
📌 Short revision sheet
📌 Practice MCQs
📌 Java code examples
📌 Visual diagrams of array memory

Just tell me!
