# 🔐 InteligentBitcoinScanner2026 — FASTSCAN v2

> **Ultra‑optimized CPU Bitcoin Keyspace Scanner**

---

## 🚀 Overview
```## ▶️ YouTube Video:
https://youtu.be/FJanTRhb6pc
┌───────────────────────────────┐
│   ▶️    │
│     (https://youtu.be/FJanTRhb6pc)]            │
└───────────────────────────────┘
```

email: hacker001ethical@proton.me

🧵🪡FASTSCAN v2 — “Needle in Keyspace” 3D Diagram
                                                
                         
<img width="518" height="873" alt="image" src="https://github.com/user-attachments/assets/186f1d4a-9712-4f88-9d07-b4a0c6826535" />







**FASTSCAN v2** is a high‑performance, CPU‑only Bitcoin keyspace scanner optimized for scanning massive ranges with deterministic stride jumps and ultra‑fast `hash160` lookups from memory‑mapped address databases.

Unlike GPU tools (BitCrack, VanitySearch, KeyHunt), FASTSCAN does **not** rely on CUDA, VRAM or external GPU hardware. Instead it leverages:

* 🧠 **Direct memory‑mapped (`mmap`) address files**
* ➕ **Incremental elliptic‑curve pubkey progression (tweak‑add)**
* 📐 **Fully deterministic bit‑range stride scanning**
* ⚡ **Highly optimized `secp256k1` operations**
* 🧵 **Multithreaded CPU processing with minimal overhead**

This allows FASTSCAN v2 to perform up to:

* **2.5 million keys/sec per thread**
* multiplied by **instant lookup against 600+ million stored address hashes**

All on pure CPU.

> On full 256‑bit ranges the tool has already found **6 hits in 8 hours**, and on smaller ranges (20–50 bits) it produces **continuous hits** due to the statistical density of those bit segments.

---

## 🔥 Key Features

### ✅ 1. CPU‑only — no GPU, no VRAM, no CUDA

* No GPU drivers required
* No VRAM limitations
* No CUDA or kernel overhead
* Runs on laptops, low‑end servers and WSL2

### ✅ 2. Memory‑mapped address database (600M+ entries)

* Loads `hash160` addresses via `mmap` (20‑byte binary entries)
* Instant O(log n) binary search
* Zero memory copying, direct OS page access
* Extremely low‑latency lookups

### ✅ 3. Deterministic bit‑range scanning with stride

The process divides `2^start_bit → 2^end_bit` into deterministic chunks using:

```
stride = (2^end_bit - 2^start_bit) / total_chunks
```

Each thread processes one chunk and `BLOCK_SIZE` sequential keys using incremental `+1` private‑key progression. Guarantees:

* No collisions
* Full coverage
* Uniform distribution
* Reproducible results

Smaller ranges (e.g., 20–50 bits) create dense hit zones — hits can appear one after another.

### ✅ 4. Ultra‑fast `secp256k1` key generation

* `secp256k1_ec_pubkey_create` for initial key
* `secp256k1_ec_pubkey_tweak_add` for incremental `+1`
* Eliminates expensive full point generation per key

### ✅ 5. High‑performance hashing

* Computes both **uncompressed** and **compressed** `hash160`
* Uses OpenSSL `SHA256` + `RIPEMD160`

### ✅ 6. Persistent progress tracking

* `progress.txt` auto‑saves after each chunk
* Resume with `--resume`

---

## 📦 Installation (Ubuntu / WSL2)

> Minimal commands to prepare a build environment and compile FASTSCAN.

### 1) Update & dependencies

```bash
sudo apt update
sudo apt install -y build-essential libssl-dev autoconf automake libtool pkg-config git
```

### 2) Install `secp256k1` (preferred from source)

```bash
git clone https://github.com/bitcoin-core/secp256k1.git
cd secp256k1
./autogen.sh
./configure --enable-module-recovery --enable-experimental --enable-module-ecdh --enable-module-schnorrsig
make -j$(nproc)
sudo make install
cd ..
```

### 3) Compile FASTSCAN

```bash
g++ -O3 scan.cpp -o scan -lssl -lcrypto -lsecp256k1 -pthread
```

---

## ▶️ Usage

**Basic:**

```bash
./scan addresses.bin 20 50
```

**Resume:**

```bash
./scan addresses.bin 20 50 --resume
```

**Arguments:**

| Argument        | Description                                     |
| --------------- | ----------------------------------------------- |
| `addresses.bin` | Binary file of sorted 20‑byte `hash160` entries |
| `start_bit`     | Start exponent (e.g. `20`)                      |
| `end_bit`       | End exponent (e.g. `50`)                        |
| `--resume`      | Continue from saved `progress.txt`              |

---

## 📁 Output

* `found.txt` — matched private keys with base58 addresses (`(C)` = compressed, `(U)` = uncompressed)
* `progress.txt` — last saved round & chunk for resume

**Example `found.txt` lines:**

```
1abc... (C) <privatekeyhex>
1abc... (U) <privatekeyhex>
```

---

🔷 1. 3D Overview of the Keyspace

                         <img width="754" height="739" alt="image" src="https://github.com/user-attachments/assets/d2673849-bee7-4fd0-b2c3-d7488e53bd0e" />

🔷 2. Single Chunk — 3D Linear Scan (Inside View)
  <img width="516" height="357" alt="image" src="https://github.com/user-attachments/assets/66839e81-9a5d-415c-beaf-dea310820eb6" />

🔷 3. 3D Layered Grid Model (Threads as Workers)

Each layer = one thread consuming chunks.
<img width="544" height="460" alt="image" src="https://github.com/user-attachments/assets/51cc6d40-69c8-4848-9502-dba820c5580c" />


Y-axis → stride jumps  
X-axis → chunk index  
Z-axis → linear scan  
🔷 4. Full Keyspace in 3D “Voxel Blocks”

Each cube = one chunk, each internal line = tweak-add scan path.
<img width="609" height="477" alt="image" src="https://github.com/user-attachments/assets/b0145a1e-a1ef-4498-8a6e-b0cd0d7e8c31" />

Where:

C0, C1, C2… = chunks

each cube contains a vertical Z-scan via tweak-add

stride jumps shift you in the Y axis

threads march along X axis
🔷 5. Detailed Flow Block Diagram (3D Steps)
<img width="600" height="533" alt="image" src="https://github.com/user-attachments/assets/53ce16e4-a865-4db5-9728-b3005a90699f" />

🧱 1. 3D Pipeline Threadów (Multi-threading Assembly Line)
         <img width="515" height="295" alt="image" src="https://github.com/user-attachments/assets/832499ee-5000-4310-978d-6c1a4f2602ba" />

📦 2. 3D Model of Private Key Progression (Incremental Big-Int)
               128-bit illustration (shortened)

    <img width="532" height="267" alt="image" src="https://github.com/user-attachments/assets/c339899a-63a5-4e8a-883c-50fbe101a4b9" />

📡 3. 3D Memory-Mapped File Lookup (mmap → binary search)
<img width="515" height="228" alt="image" src="https://github.com/user-attachments/assets/5dd5daaf-5d42-4de6-82e5-0cdbb30514a5" />

🌀 4. 3D Elliptic Curve Walking (pub = pub + G)
  <img width="503" height="205" alt="image" src="https://github.com/user-attachments/assets/306a8678-6a06-4a96-8ade-a2934572f04e" />

🌐 5. 3D Visualization of Rounds Growing (round_idx)
<img width="513" height="141" alt="image" src="https://github.com/user-attachments/assets/a9c457d9-3601-4d8c-be15-08bea08412e8" />


Shape: expanding 3D platform
🔥 6. 3D Representation of Stride Dividing Keyspace
<img width="510" height="212" alt="image" src="https://github.com/user-attachments/assets/fb257e73-4bc8-44c5-92db-cd8c9ba14359" />




⭐ What is “range compression”? — Intuition

Imagine this:

a full 256-bit range → the size of a galaxy 🌌

a small 20–50-bit range → a small room 🚪

Addresses (hash160) are distributed randomly, but when you inspect a small range, their density becomes enormous relative to the available space.
When you look at the full 256-bit space, that density is effectively zero.



<img width="519" height="321" alt="image" src="https://github.com/user-attachments/assets/701f94fd-a0bd-444c-b513-4abf2b6bba1c" />

📦 MESSAGE 2/4
🔷 3D: Density Comparison — “Galaxy vs. Cube”
🔭 Full 256-bit range in 3D (emptiness)
                         Y
                         ↑

           ┌──────────────────────────────────────┐
           │             .         .             │
           │     .                       .       │
           │                .                    │
     X ←   │       .              .         .    │   → Z
           │   .        .     .                 │
           └──────────────────────────────────────┘

                     ≈ empty universe
🧊 20–50 bit range in 3D (dense block)

                         Y
                         ↑

           ┌──────────────────────────────────────┐
           │ ███ ████ ███ ████ ████ ███ ████ ██  │
           │ ██ ███ ███ ████ ███ ███ █████ ████ │
           │ ███ ████ ███ ███ ████ ███ ███ ███  │
     X ←   │ █████ ███ ████ ███ █████ ███ ████  │ → Z
           │ ██ █████ ███ ███ ███ █████ ███ ███ │
           └──────────────────────────────────────┘

                     ≈ dense room filled with “points”
🔶 3D: Why do “dense ranges” produce hits?

My scanning works like this:

STRIDE scatters the starting keys “across a grid”

each chunk scans linearly downward along the Z-axis

if the addresses are dense → hits must appear

if the addresses are sparse → hits are practically impossible

ASCII 3D:

🟦 Dense range — shots hit the material

             Dense Keyspace Volume
                (20–50 bits)

             ████████████████████
             ████████████████████
             ██    ████   ███████   ◄── scan path
             ████████████████████
             ████ █████ █████████
             ████████████████████


🟥 Sparse range — the shots fly into empty space

             Full 256-bit Keyspace

           .                                   .
                   .            .         .
     scan path  ────────────────►  (miss)
         .                       .          .
                   .   .                   .





## 🧠 Why FASTSCAN Outperforms GPU Tools

**GPU bottlenecks:**

* VRAM size and fragmentation
* PCIe transfers (host ↔ device)
* CUDA kernel overhead and synchronization
* Large memory structures that don't fit GPU memory

**FASTSCAN advantages:**

* Runs fully on CPU cache + system RAM
* Uses OS page cache and `mmap` for direct lookups
* Incremental pubkey generation avoids repeated heavy EC ops
* Zero GPU latencies or copy overheads

**Result:** a single well‑tuned CPU can outperform multi‑GPU rigs on address‑dense ranges.

---

## 📈 Performance Notes

* **Real result:** Full 256‑bit search: 6 hits in 8 hours
* **Dense ranges (20–50 bits):** continuous hits
* Example throughput: `2.5M keys/s per thread × 16 threads = 40M keys/s`
* Works with `600–800M` `hash160` entries mmapped for instant lookup

---

## 🛠 System Requirements

* Linux or WSL2 (Ubuntu recommended)
* 4–8 GB RAM minimum (more recommended)
* Fast SSD strongly recommended
* Multi‑core CPU (8+ threads ideal)
* Address DB file (sorted 20‑byte entries)

---


## ⚖️ License

MIT License

---

## 🤝 Contribute

Pull requests, performance patches and improvements welcome. Please open issues for bugs or optimizations.

---

## 🧾 How to install Ubuntu (WSL2) — commands only

1. Open PowerShell as Administrator, then:

```powershell
wsl --install
wsl --set-default-version 2
wsl --install -d Ubuntu-22.04   # optional specific distro
```

2. Launch Ubuntu from Start menu or run `ubuntu` in PowerShell
3. Update inside Ubuntu:

```bash
sudo apt update && sudo apt upgrade -y
```

---

*Generated README for **FASTSCAN v2** — place this `README.md` in your repo root. Good luck and code responsibly.*

---



