<!-- Profile README for github.com/TANISHX1 -->
<!-- Last updated: 2026-03-07 -->

<h1 align="center">Hi, I'm <a href="https://github.com/TANISHX1">Tanish Shivhare</a> 👋</h1>
<p align="center">
   Systems & OS  •  C on Linux  •  Networking  •  Concurrency  •  Python experiments
</p>

<p align="center">
  <img src="https://komarev.com/ghpvc/?username=TANISHX1&style=flat-square" alt="Profile views" />
  <img src="https://visitor-badge.laobi.icu/badge?page_id=TANISHX1.TANISHX1" alt="Visitor badge" />
  <a href="https://github.com/TANISHX1?tab=followers"><img src="https://img.shields.io/github/followers/TANISHX1?label=Followers&style=flat-square" alt="Followers" /></a>
</p>

---

## 🧑‍💻 About Me

I'm a systems-focused developer who loves building things close to the metal. My work lives at the intersection of **Operating Systems**, **Computer Networking**, and **low-level C programming on Linux**. I enjoy understanding *how* things work — from how the kernel schedules processes, to how a TCP packet travels between machines, to how a Huffman tree shrinks a file.

- 🔧 Most of my code is in **C on Linux** (POSIX environment)
- 🌐 Interested in **TCP/IP networking**, sockets, and multiplexing
- ⚙️ Deep interest in **OS internals**: scheduling, virtual memory, concurrency
- 🐍 Occasional **Python** experiments (image processing / ML)
- 🤝 Open to collaborating on systems, networking, or OS-style projects

---

## 🛠️ Tech Stack

### Languages
| Language | Proficiency | Primary Use |
|----------|------------|-------------|
| **C** | Advanced | Systems programming, OS internals, networking, algorithms |
| **C++** | Intermediate | General programs, data structures |
| **Python** | Intermediate | Scripting, ML experiments, image processing |
| **Bash / Shell** | Intermediate | Automation, scripting, Linux tooling |
| **JavaScript** | Basic | Web utilities (seat allocation system) |
| **HTML** | Basic | Project showcase pages |

### Core Technical Domains
| Domain | Key Concepts |
|--------|-------------|
| **Operating Systems** | Process scheduling (lottery, MLFQ), virtual memory (segmentation, paging), persistence (I/O), system calls |
| **Computer Networking** | TCP/IP sockets, `select()`-based I/O multiplexing, multi-client servers, cross-machine communication |
| **Concurrency & Parallelism** | POSIX threads (`pthreads`), mutexes, semaphores, condition variables, deadlock detection & prevention, race conditions |
| **Algorithms & Data Structures** | Huffman coding, linked lists (singly/doubly/circular), sorting, dynamic memory management |
| **Systems Programming** | UNIX utilities re-implementation (`cat`, `ls`, `grep`), file I/O, process management, memory management |
| **Machine Learning (Basic)** | Image classification, feature extraction using TensorFlow/OpenCV |

### Tools & Environment
| Category | Tools |
|----------|-------|
| **Compilers** | GCC, G++ |
| **Debuggers** | GDB, Valgrind, Helgrind (thread error detection) |
| **Build** | Make, GCC flags (`-Wall`, `-Werror`, `-pthread`) |
| **Version Control** | Git, GitHub |
| **Editors / IDE** | Vim, VS Code |
| **OS / Platform** | Linux (Fedora, Ubuntu, Arch), POSIX-compliant systems |
| **Python Libraries** | TensorFlow, OpenCV, Pillow, NumPy, Matplotlib |

---

## 📁 Projects — Deep Analysis

### 1. 🖧 TCP Multi-Client Server (`Server`)
- **Language:** C  
- **Field:** Computer Networking / Systems Programming  
- **Domain:** TCP/IP, Socket Programming, I/O Multiplexing  
- **What it does:** A TCP-based server that manages multiple simultaneous client connections on the **same machine** using `select()` for non-blocking I/O multiplexing. Handles real-time messaging and connection lifecycle management.  
- **Key Concepts:** `socket()`, `bind()`, `listen()`, `accept()`, `select()` syscall, file descriptor sets, connection management  
- **Significance:** Demonstrates understanding of the classic *C10K problem* approach — serving many clients without spawning a thread per connection.  
- **Showcase:** [Live Demo](https://tanishx1.github.io/web-result/server-client/index.html)

---

### 2. 🌐 Cross-Machine TCP Server (`server_x`)
- **Language:** C  
- **Field:** Computer Networking  
- **Domain:** TCP/IP, Distributed Systems (basic), Socket Programming  
- **What it does:** An evolution of the `Server` project — this variant works **across different machines** on a network, not just localhost.  
- **Key Concepts:** Network addressing, IP binding, port configuration, cross-host TCP communication  
- **Significance:** Bridges the gap between local socket demos and real networked applications.

---

### 3. ⚙️ Concurrency & Synchronization (`Concurrency`)
- **Language:** C  
- **Field:** Operating Systems / Systems Programming  
- **Domain:** Concurrency, POSIX Threads, Synchronization Primitives  
- **What it does:** A comprehensive study of concurrent programming in UNIX/Linux using POSIX APIs. Covers multiprocessing and multithreading patterns with a focus on correctness (race-free, deadlock-free code).  
- **Key Concepts Demonstrated:**
  - **Mutex locks** — protecting critical sections (atomicity violations)
  - **Condition variables** — signaling between threads (order violations)
  - **Semaphores** — counting synchronization
  - **Deadlock** — conditions (Mutual Exclusion, Hold & Wait, No Preemption, Circular Wait), prevention strategies, avoidance via scheduling
  - **Livelock** — `pthread_mutex_trylock()` pitfalls and mitigations
- **Significance:** Demonstrates deep understanding of OS-level concurrency bugs that affect production systems (databases, kernels, runtimes).

---

### 4. 🧠 Segmentation Virtual Address Simulator (`segmentation-virtual-address-Simulator`)
- **Language:** C  
- **Field:** Operating Systems / Computer Architecture  
- **Domain:** Virtual Memory, Memory Management Unit (MMU), Address Translation  
- **What it does:** Simulates how a CPU's MMU translates a 16-bit **virtual address** to a **physical address** using segmentation. Models four segments (CODE, DATA, HEAP, STACK), each 16 KB, stored contiguously in physical memory.  
- **Key Concepts:** Segment table, segment number extraction, offset calculation, bounds checking, physical address = base + offset  
- **Workflow:**
  1. Generate random 16-bit virtual address
  2. Decode segment number and offset from address bits
  3. Perform bounds check against segment size
  4. Compute physical address using segment base
- **Significance:** Hands-on implementation of OS memory management concepts typically only seen in textbooks (OSTEP-style). Valuable for understanding how modern OSes and hypervisors handle memory isolation.

---

### 5. 🎟️ Lottery Scheduling Simulation (`lottery-scheduling-simulation`)
- **Language:** C  
- **Field:** Operating Systems  
- **Domain:** CPU Scheduling Algorithms, Probabilistic Systems  
- **What it does:** Simulates a **lottery-based CPU scheduler** where each job receives a random number of tickets and the scheduler draws a winning ticket per time slice to decide which job runs next.  
- **Key Concepts:** Proportional-share scheduling, randomized algorithms, linked list for job tracking, dynamic memory, statistics collection  
- **Features:** Configurable runs, optional per-run output, win percentage stats, re-run without restart, full `free()` memory cleanup  
- **Significance:** Demonstrates understanding of probabilistic scheduling (used in research OSes and some real-time systems) and contrasts with deterministic schedulers like Round Robin or MLFQ.

---

### 6. 🗜️ Huffman File Compressor (`File-Compression`)
- **Language:** C  
- **Field:** Algorithms & Data Structures / Systems Programming  
- **Domain:** Lossless Data Compression, Trees, Bit Manipulation  
- **What it does:** A CLI-based lossless text file compressor and decompressor using **Huffman coding** — builds a frequency table, constructs a min-heap priority tree, generates variable-length binary codes, and writes bit-packed output.  
- **Key Concepts:** Huffman tree construction, priority queue (min-heap), bit-packing, file header encoding for tree reconstruction, entropy coding  
- **Workflow:**
  - *Compress:* Count char frequencies → Build Huffman tree → Generate codes → Bit-pack → Write `.huff` file  
  - *Decompress:* Read header → Rebuild tree → Decode bits → Restore original file  
- **Significance:** Implements the core algorithm behind ZIP/GZIP compression. Demonstrates mastery of trees, bit operations, and file I/O in C.  
- **Showcase:** [Sample Output](https://tanishx1.github.io/web-result/file%20compression/index.html)

---

### 7. 🧱 Data Structures Library (`Data-Structures`)
- **Language:** C  
- **Field:** Computer Science Fundamentals  
- **Domain:** Data Structures, Algorithms  
- **What it does:** Implements classic data structures from scratch in C: singly linked lists, doubly linked lists, and circular linked lists — with full CRUD operations.  
- **Key Concepts:** Pointer manipulation, dynamic memory allocation (`malloc`/`free`), traversal, insertion/deletion at head/tail/middle  
- **Significance:** Foundation work demonstrating strong understanding of memory layout and pointer semantics — crucial for systems programming.

---

### 8. 🔧 UNIX Utilities Re-implementation (`unix-utilities`)
- **Language:** C  
- **Field:** Systems Programming / Operating Systems  
- **Domain:** UNIX/Linux Internals, File I/O, Process Model  
- **What it does:** Reimplements core UNIX command-line utilities (`cat`, `ls`, `grep`, etc.) from scratch in C, compiled with strict flags (`-Wall -Werror`).  
- **Key Concepts:** `open()`/`read()`/`write()` syscalls, `stat()`, directory traversal, `getopt()`, string parsing  
- **Significance:** Deep dive into how standard UNIX tools actually work under the hood — a classic OS course exercise demonstrating systems thinking.

---

### 9. 🖼️ Image Detector / Classifier (`image_detector`)
- **Language:** Python  
- **Field:** Machine Learning / Computer Vision  
- **Domain:** Image Classification, Feature Extraction, GUI Applications  
- **What it does:** A Python GUI application that loads images, extracts visual features, and classifies them into categories using a pre-trained deep learning model.  
- **Tech Stack:** TensorFlow (model inference), OpenCV (image processing), Pillow (image loading), NumPy (array ops), Matplotlib (visualization)  
- **Significance:** Shows cross-domain capability — stepping outside C/systems work into ML/CV, demonstrating adaptability.

---

### 10. 🎮 Hangman Game (`hangman`)
- **Language:** C  
- **Field:** Application Development  
- **Domain:** CLI Applications, String Processing  
- **What it does:** A word-guessing game built entirely in C, running on the terminal. Manages game state, input handling, and win/loss conditions.  
- **Significance:** Demonstrates C string handling, input/output, and building interactive CLI applications.

---

### 11. 📜 Bash Scripting Collection (`Bash-scripting`)
- **Language:** Bash / Shell  
- **Field:** DevOps / Automation  
- **Domain:** Shell Scripting, Linux Automation  
- **What it does:** A collection of shell scripts solving automation and utility problems on Linux.  
- **Significance:** Demonstrates ability to automate Linux workflows — essential for systems and DevOps work.

---

### 12. 💾 I/O Persistence Simulation (`presistance`)
- **Language:** C  
- **Field:** Operating Systems  
- **Domain:** I/O Subsystem, Storage, OS Persistence Layer  
- **What it does:** Simulates how an OS interacts with an I/O device — models the concept of persistence (how data survives power loss via storage devices).  
- **Significance:** Covers the OS persistence layer (file systems, I/O devices) — a lesser-explored but critical domain.

---

### 13. 💺 Seat Allocation System (`seat-allocation-sys`)
- **Language:** JavaScript  
- **Field:** Web Development  
- **Domain:** Frontend Logic, Algorithms  
- **What it does:** A web-based seat allocation system with booking logic implemented in JavaScript.  
- **Significance:** Demonstrates frontend problem-solving and willingness to work outside the primary C/Linux comfort zone.

---

## 🗺️ Domain Map

```
TANISHX1's Technical Profile
│
├── 🖥️  Operating Systems
│   ├── CPU Scheduling        → lottery-scheduling-simulation
│   ├── Virtual Memory        → segmentation-virtual-address-Simulator
│   ├── Concurrency           → Concurrency
│   ├── Persistence / I/O     → presistance
│   └── UNIX Internals        → unix-utilities
│
├── 🌐  Computer Networking
│   ├── TCP Server (local)    → Server
│   └── TCP Server (network)  → server_x
│
├── 📐  Algorithms & DSA
│   ├── Huffman Compression   → File-Compression
│   └── Linked Lists          → Data-Structures
│
├── 🐚  Systems / CLI
│   ├── Bash Automation       → Bash-scripting
│   ├── C/C++ Programs        → C-C_Plus_Plus-Programs
│   ├── File Manipulation     → reverse-file
│   └── Games                 → hangman
│
└── 🐍  Python / ML
    └── Image Classification  → image_detector
```

---

## 🤖 Agentic AI Context

> This section provides structured context for AI agents, copilots, and automated tools to accurately reason about this developer's profile.

```yaml
developer:
  name: Tanish Shivhare
  handle: TANISHX1
  primary_language: C
  platform: Linux (POSIX)
  experience_level: intermediate-to-advanced in systems programming

core_strengths:
  - Low-level systems programming in C on Linux
  - POSIX concurrency: pthreads, mutex, semaphore, condition variables
  - TCP/IP socket programming and I/O multiplexing with select()
  - OS internals: virtual memory (segmentation), CPU scheduling (lottery), I/O persistence
  - Classic algorithms: Huffman coding, linked lists, sorting
  - UNIX utility implementation from scratch

tech_stack:
  languages:
    primary: [C, Bash]
    secondary: [C++, Python]
    basic: [JavaScript, HTML]
  tools: [GCC, GDB, Valgrind, Helgrind, Make, Git, Vim, VS Code]
  python_libs: [TensorFlow, OpenCV, Pillow, NumPy, Matplotlib]

projects_by_domain:
  operating_systems:
    - segmentation-virtual-address-Simulator  # virtual memory, address translation
    - lottery-scheduling-simulation           # probabilistic CPU scheduling
    - Concurrency                             # pthreads, mutex, deadlock
    - presistance                             # I/O device simulation
    - unix-utilities                          # syscalls, UNIX internals
  networking:
    - Server                                  # select()-based multi-client TCP server
    - server_x                                # cross-machine TCP server
  algorithms_dsa:
    - File-Compression                        # Huffman coding, bit manipulation
    - Data-Structures                         # linked lists in C
  machine_learning:
    - image_detector                          # TensorFlow image classifier (Python)
  cli_systems:
    - hangman                                 # C terminal game
    - Bash-scripting                          # Linux automation scripts
    - reverse-file                            # file I/O in C
  web:
    - seat-allocation-sys                     # JavaScript seat booking
    - web-result                              # HTML project showcase

suitable_for_tasks:
  - Systems and OS-level C development
  - Network programming (TCP sockets, multiplexing)
  - Concurrent/parallel programming (pthreads, synchronization)
  - Algorithm implementation in C
  - Linux shell scripting and automation
  - Basic ML/computer vision prototyping in Python

not_yet_experienced_in:
  - Kernel module development
  - Distributed systems at scale
  - Cloud infrastructure / DevOps pipelines
  - Backend web frameworks
  - Mobile development

contact:
  github: https://github.com/TANISHX1
  linkedin: https://www.linkedin.com/in/tanish-shivhare-71b576222
  email: Tanishshivhare2@gmail.com
```

---

<!-- Top Languages with Percentage Bar -->
<p align="center">
  <img src="https://github-readme-stats.zohan.tech/api/top-langs/?username=TANISHX1&layout=compact&theme=tokyonight&hide_border=true&langs_count=8&exclude_repo=seat-allocation-sys" alt="Top Languages" />
  <img height="190" src="https://github-profile-summary-cards.vercel.app/api/cards/stats?username=TANISHX1&theme=tokyonight" alt="Stats" />
</p>

---

## 📬 Contact / Collaborate

[![GitHub](https://img.shields.io/badge/GitHub-TANISHX1-181717?style=flat-square&logo=github)](https://github.com/TANISHX1)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-tanish--shivhare--71b576222-0A66C2?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/tanish-shivhare-71b576222)
[![Email](https://img.shields.io/badge/Email-Tanishshivhare2%40gmail.com-EA4335?style=flat-square&logo=gmail&logoColor=white)](mailto:Tanishshivhare2@gmail.com)
