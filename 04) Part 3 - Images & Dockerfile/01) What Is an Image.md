
---

# 📘 PART 3 — Images & Dockerfile (Most Important)

We start with **understanding**, not commands.

---

## Chapter 8: **What Is an Image (Deep Understanding)**

This chapter is *critical*.
If you truly understand images, **everything else in Docker becomes easy**.

---

## 1️⃣ Image vs Container (Once and for All)

### Image = Blueprint 📘

### Container = Running Instance 🏃

Think like this:

| Concept   | Real-world analogy     |
| --------- | ---------------------- |
| Image     | PDF file               |
| Container | Opened PDF in a reader |
| Image     | Class                  |
| Container | Object                 |
| Image     | Cake recipe            |
| Container | Baked cake             |

👉 You **cannot change the recipe** by eating the cake.

---

## 2️⃣ What an Image Actually Is

A Docker image is:

> **A stack of read-only filesystem layers**

Nothing more.

```
Image
├── Layer 1 (Base OS)
├── Layer 2 (Libraries)
├── Layer 3 (App files)
└── Layer 4 (Metadata)
```

⚠️ **Important**:
Images do **NOT** run.
Images do **NOT** change.

---

## 3️⃣ Read-Only Layers (Core Concept)

Every image layer is:

* Immutable (cannot be modified)
* Read-only
* Content-addressed (hash-based)

If Docker allowed image mutation:

* Caching would break
* Sharing layers would be unsafe
* Builds would be non-reproducible

So Docker enforces:

> **Images are immutable by design**

---

## 4️⃣ Image Filesystem vs Container Filesystem

When you run a container:

```
Container filesystem =
    Image layers (read-only)
  + Writable layer (container-only)
```

Visual:

```
┌─────────────────────────┐
│ Writable Layer          │  ← changes go here
├─────────────────────────┤
│ Image Layer 3           │
├─────────────────────────┤
│ Image Layer 2           │
├─────────────────────────┤
│ Image Layer 1           │
└─────────────────────────┘
```

---

### Example

```bash
docker run -it ubuntu bash
```

Inside container:

```bash
touch hello.txt
```

What happens?

* `hello.txt` goes into **writable layer**
* Image remains untouched

If container is deleted → `hello.txt` is gone ❌

---

## 5️⃣ Why Images Are Immutable (Very Important)

Immutability gives Docker its **superpowers**:

### 🔁 Layer Reuse

* Multiple images can share the same base layers

### 🚀 Speed

* Cached layers = instant builds

### 📦 Consistency

* “Works on my machine” problem solved

### 🔐 Safety

* No container can corrupt an image

---

## 6️⃣ Copy-on-Write (CoW) — How Changes Happen

If a container tries to modify an existing image file:

1. Docker **copies** the file to writable layer
2. Modifies the copy
3. Image stays unchanged

This is called **Copy-on-Write**.

---

## 7️⃣ Image Metadata (Not Files!)

Some image instructions do **not** add files:

Examples:

* `CMD`
* `ENTRYPOINT`
* `ENV`
* `WORKDIR`
* `EXPOSE`

They change **metadata**, not filesystem.

➡️ This will matter a LOT in Chapter 10 (zero-byte layers).

---

## 8️⃣ Containers Die, Images Live

| Thing                    | Lifetime    |
| ------------------------ | ----------- |
| Container                | Short-lived |
| Image                    | Long-lived  |
| Container writable layer | Temporary   |
| Image layers             | Permanent   |

That’s why:

* Containers are disposable
* Images are reusable

---

## 9️⃣ Mental Model (Memorize This)

> **Image = frozen filesystem + instructions**
> **Container = image + writable layer + running process**

If you remember only this sentence, Docker will make sense.

---

## Chapter 8 Summary

* Images are **read-only layered filesystems**
* Images are **immutable**
* Containers add **one writable layer**
* All changes happen in writable layer
* Copy-on-write protects image layers
* Metadata ≠ filesystem

---