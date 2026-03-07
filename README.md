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

I'm a systems-focused developer who loves building things close to the metal. My work lives at the intersection of **Operating Systems**, **Computer Networking**, and **low-level C programming on Linux**. I enjoy understanding *how* things work — from how the kernel schedules processes, to how a TCP packet travels across the network, to how a Huffman tree compresses a file, to how a `futex` syscall puts a thread to sleep.

- 🔧 Primary language is **C on Linux** (POSIX environment)
- 🌐 Builds **TCP/IP networked servers** with socket programming and I/O multiplexing
- ⚙️ Deep study of **OS internals**: scheduling, virtual memory, concurrency, I/O
- 🔒 Implements real **synchronization primitives** from scratch — including custom futex-based locks
- 🐍 Occasional **Python** experiments (image processing / ML)
- 🤝 Open to collaborating on systems, networking, or OS-style projects

---

## 🛠️ Comprehensive Tech Stack

### Languages
| Language | Level | Primary Use Cases |
|----------|-------|-------------------|
| **C** | Advanced | Systems programming, OS internals, TCP servers, compression algorithms, concurrent data structures |
| **C++** | Intermediate | OOP concepts, DBMS simulation, audio processing, graphics patterns |
| **Bash / Shell** | Intermediate | Linux automation, file ops, process management, conditional scripting |
| **Python** | Intermediate | ML/CV experiments, image classification with TensorFlow + OpenCV |
| **JavaScript** | Basic | Web-based seat allocation system |
| **HTML** | Basic | Project showcase static pages |

### Deep Technical Domains
| Domain | Specific Concepts Demonstrated in Code |
|--------|---------------------------------------|
| **Operating Systems** | CPU scheduling (lottery/proportional-share), virtual memory address translation (segmentation), I/O device models (polling/interrupt/DMA), UNIX system calls |
| **Computer Networking** | TCP socket lifecycle (`socket→bind→listen→accept`), `select()`-based I/O multiplexing, multi-client threaded servers, `INADDR_ANY` cross-machine binding, debug hex-dump of network buffers |
| **Concurrency & Synchronization** | POSIX `pthreads`, `pthread_mutex_t`, `sem_t`, `pthread_cond_t`, spin locks (flag/TAS), yield-based locks, **custom futex-based mutex** (`SYS_futex` + `_Atomic`), hand-over-hand (HOH) locking, scalable approximate counters, producer-consumer pattern, reader-writer problem |
| **Algorithms & Data Structures** | Huffman coding + min-heap priority queue, BST (insert/search/delete), stack, queue, circular queue, infix→postfix conversion, bubble/insertion/selection sort, Tower of Hanoi, linked list variants |
| **Systems Programming** | `fseek`/`fgetc`-based file reversal, `stat()` inode comparison, buffered I/O, UNIX utility re-implementation (cat/grep/ls), run-length encoding, bit-packing |
| **Computer Architecture** | Virtual address decoding (segment bits + offset bits), segmentation hardware model (base+bound), physical address translation, segmentation fault simulation |
| **Machine Learning (Basic)** | Image classification, feature extraction, pre-trained model inference via TensorFlow |
| **Linux Internals** | `futex` syscall (`FUTEX_WAIT`/`FUTEX_WAKE`), atomic operations (`_Atomic`, `atomic_fetch_or/add/sub/load`), `sysconf(_SC_NPROCESSORS_ONLN)`, `gettimeofday()` |

### Tools & Environment
| Category | Tools |
|----------|-------|
| **Compilers** | GCC (`-Wall -Werror -pthread`), G++ |
| **Debuggers** | GDB, Valgrind (memory), Helgrind (thread races) |
| **Build** | Make, manual GCC pipelines |
| **Version Control** | Git, GitHub |
| **Editors** | Vim, VS Code |
| **OS / Platform** | Linux — Fedora, Ubuntu, Arch (POSIX-compliant) |
| **Python Libraries** | TensorFlow, OpenCV, Pillow, NumPy, Matplotlib |
| **Syscalls Used** | `socket`, `bind`, `listen`, `accept`, `select`, `recv`/`send`, `read`/`write`, `open`/`fopen`, `stat`, `fseek`/`ftell`, `sbrk`, `SYS_futex` |

---

## 📁 Projects — Deep Code Analysis

---

### 1. 🖧 TCP Echo Server — `select()`-based (`Server`)
**Language:** C &nbsp;|&nbsp; **Field:** Computer Networking / Systems Programming  
**Domain:** TCP/IP, Socket Programming, I/O Multiplexing

**Architecture:**
The repo contains two variants: a **prototype** (simple single-client), and a full **echo server** using `select()`. The echo server uses a `header.h` + `wrapper.h` + `script.sh` structure. It maintains an `fd_set` for all active client connections, calls `select()` in an event loop to wait for any file descriptor to become readable, and echoes data back to the originating client.

**Code-level details:**
- `socket(AF_INET, SOCK_STREAM, 0)` → `setsockopt(SO_REUSEADDR)` → `bind` → `listen(SOMAXCONN)` — full server setup
- `FD_ZERO`, `FD_SET`, `select(maxfd+1, &rfds, NULL, NULL, &tv)` with a 10-second `struct timeval` timeout
- Iterates over `clients[]` array to find ready descriptors after `select()` returns
- `accept()` creates a new socket for each new connection
- Single-threaded — no locking needed; all I/O handled in one event loop
- A `script.sh` automates spawning multiple client connections for testing

**Significance:** Demonstrates the foundational approach to multiplexing — understanding why `select()` exists and how it compares to `poll()` and `epoll()`. Shows the classic I/O-event-driven server design pattern.

**Showcase:** [Live Demo](https://tanishx1.github.io/web-result/server-client/index.html)

---

### 2. 🌐 Cross-Machine Threaded TCP Server (`server_x`)
**Language:** C &nbsp;|&nbsp; **Field:** Computer Networking  
**Domain:** TCP/IP, Multi-threaded Servers, Mutex Synchronization, ANSI Terminal I/O

**Architecture:**
This is a hybrid `select()` + `pthreads` server that works **across different machines** on a real network. It combines an event loop (for accepting new connections) with a per-client `pthread` (for handling each client's data independently).

**Code-level details:**
- **Header (`header.h`):** Defines `cli_socket_info` struct (holds `sockaddr_in`, `pthread_mutex_t cli_lock`, `pthread_mutex_t cli_fill_lock`, `cli_id`, `client_online[]`), `u_ipt` (debug mode input), `threads_info` (active thread count + lock), `global_variables` (global mutex `g_lock`)
- **Server (`server.c`):**
  - `INADDR_ANY` binding — accepts connections from any network interface
  - `calloc(FD_SETSIZE, sizeof(int))` allocates client array for up to 1024 connections
  - `pthread_mutex_init` called for `thread_lock` and `g_lock` at startup
  - `clients[]` array initialized to `-1` (empty slots) for O(n) slot scanning
  - **Event loop:** `select()` blocks up to 10 seconds; on new connection, `accept()` + `malloc(cli_socket_info)` + `pthread_create(&thread[a], NULL, client_thread, client)`
  - **`client_thread()`:** Logs connect time with `ctime()`, `inet_ntop()` for IP string, scans `client_online[]` with `g_lock` mutex for empty slot, `recv()` loop, echoes back with ANSI color codes (`FG_BYELLOW`/`RESET`), `close(cli_id)` + decrements `thread_active` + `free(client)` on disconnect
  - **Debug modes:** 0=silent, 1=byte count log, 2=full hex dump with `printf("%02x ", c)` + printable char mapping
  - `setsockopt(SO_REUSEADDR)` prevents `bind` failure on restart
- **Client (`client.c`):** `socket + sockaddr_in setup`, takes `<ip> <port>` as CLI args

**Significance:** Production-quality server code: thread-safe client management, configurable debug levels, graceful disconnect handling, clean memory management per thread, ANSI terminal UX. Shows understanding of the `select+thread` hybrid concurrency model.

---

### 3. ⚙️ Concurrency & Synchronization Deep Study (`Concurrency`)
**Language:** C &nbsp;|&nbsp; **Field:** Operating Systems / Systems Programming  
**Domain:** POSIX Threads, Synchronization Primitives, Lock Implementation, Concurrent Data Structures

This is the most comprehensive repository — a structured, multi-folder deep dive into concurrent programming from first principles to advanced kernel-level locking.

**Folder Structure & Code Analysis:**

#### `locks/` — Lock Implementations from Scratch
- **`simple-flag.c`:** Custom spinlock using a plain `int flag`. Shows the problem: not atomic, leads to data races and indeterminate results. Race condition demonstrated with two threads incrementing a shared counter 100,000 times each.
- **`test-and-set.c`:** Implements `testandset(int *old_ptr, int new_value)` manually (non-atomic) to show the TAS concept. `_lock_t` struct with `int flag`. Lock spins with `while(testandset(&mutex->flag, 1) == 1)`. Notes explicitly: "not atomic, stucks when race condition occurs".
- **`yield.c`:** Lock that calls `sched_yield()` instead of spinning — trades CPU for cooperative multitasking in the lock wait path.

#### `custom_locks/` — Futex-Based Custom Mutex *(Most Advanced)*
- **`linux_futex.c`:** Implements a real **futex-based mutex** using `_Atomic int mutex`, `atomic_fetch_or`, `atomic_fetch_add`, `atomic_fetch_sub`, `atomic_load`, and `syscall(SYS_futex, ...)`.
  - **Bit layout:** bit 31 = lock bit (1=locked), bits 0–30 = waiter count
  - **`lock()` fast path:** `atomic_fetch_or(mutex, (1<<31))` — if bit 31 was clear → lock acquired immediately, no syscall
  - **`lock()` slow path:** increments waiter count → retries → if still locked → `syscall(SYS_futex, mutex, FUTEX_WAIT, v, ...)` to sleep in kernel queue
  - **`unlock()` fast path:** `atomic_fetch_add(mutex, 0x80000000)` clears bit 31; if result is 0 (no waiters) → returns immediately
  - **`unlock()` slow path:** `syscall(SYS_futex, mutex, FUTEX_WAKE, 1, ...)` to wake one sleeping thread
  - Demonstrates: userspace fast path (no syscall when uncontended), kernel-assisted blocking when contended — the exact same mechanism used by `glibc`'s `pthread_mutex_t`
- **`linux_futex2.c`:** A refined variant of the above

#### `semaphore/` — Semaphore Patterns
- **`binary semaphore.c`:** Binary semaphore as a mutual exclusion primitive
- **`simple_c_p.c`:** Multiple producer – multiple consumer using three semaphores: `full` (tracks slots with items), `empty` (tracks free slots), `glock` (mutual exclusion on buffer). 5 producers × 5 consumers on a `buffer[MAX]` with circular indexing. Demonstrates **bounded buffer / producer-consumer** classic problem.
- **`temaphore.c`:** Additional semaphore patterns

#### `condition variable/`
- **`condition_variable.c`:** Basic `pthread_cond_wait` / `pthread_cond_signal` usage
- **`multiple_p_c.c`:** Multiple producers and consumers using condition variables instead of semaphores
- **`rwe_cv.c`:** **Reader-writer problem** with condition variables — multiple readers can proceed concurrently; writers get exclusive access

#### `LOCK-BASED CONCURRENT DATA STRUCTURES/` — Thread-Safe Data Structures
- **`simple_counter.c`:** Counter with a single global mutex — correct but not scalable
- **`scalable_counter.c`:** **Approximate counter** (scalable slab counter) — a `_counter` struct with a global counter, global lock (`glock`), plus per-CPU local counters (`lvalue[CPU_NUM]`) and per-CPU local locks (`llock[CPU_NUM]`). `update()` increments local counter; only flushes to global when local hits `threshold`. `get()` returns only global value. Comment: *"such approach is used in high-concurrency environments — OS kernel, high-end servers, performance-critical things."* This is the actual approximation technique used in Linux kernel counters.
- **`HOH locking.c`:** **Hand-over-hand (coupling) locking** on a linked list — each node has its own `pthread_mutex_t lock`. `search()` locks `current`, then locks `next`, then unlocks `current` before advancing. Demonstrates fine-grained locking for higher concurrent throughput vs. one big list lock. Comment acknowledges potential deadlock risk.
- **`concurrent_linked_list.c`:** Concurrent linked list with global lock
- **`concurent_stack.c`:** Thread-safe stack with mutex

#### `event based concurrency/` — Event-Driven Model
- README explains event-based concurrency: single-threaded event loop, `select()`/`poll()`, avoiding locking by eliminating shared mutable state. Contrasts with thread-based concurrency.

**Significance:** This repository alone shows understanding from *spin locks* up to *futex internals* — the same primitives underlying `glibc`, Linux kernel, and high-performance runtimes. The scalable counter and HOH locking are graduate-level OS concepts.

---

### 4. 🧠 Segmentation Virtual Address Simulator (`segmentation-virtual-address-Simulator`)
**Language:** C &nbsp;|&nbsp; **Field:** Operating Systems / Computer Architecture  
**Domain:** Virtual Memory, MMU, Address Translation, Memory Segmentation

**Code-level details:**
- **`segment.h`** defines the `segment` struct: `char *segment_no` (2-bit binary string), `char *segment_name`, `char *offset` (14-bit binary string), `int bound`, `int base`. Constants: `BOUND=16384` (16 KB per segment), `OFFSET_BITS=14`, `SEGMENTS_BITS=2`.
- **`code.h`** provides `random_number()` (generates random 16-bit value), `binaryconv(int num, int bits)` (converts decimal to binary string), `binary_to_decimal(char *bin)` (binary string to int).
- **`segmentation.c`** (main): calls `OS()` to show OS memory layout, generates a 16-bit virtual address, allocates 4 `segment*` structs with `malloc`, calls `binary(seg, i)` to assign segment number in binary (00=CODE, 01=DATA, 10=HEAP, 11=STACK), calls `copy(seg, address)` to extract the 14 offset bits, sets `base` and `bound` via `Bound()` (each segment base = previous base + 16384).
- **`segment_search()`:** extracts `offset_val = binary_to_decimal(seg->offset)`, checks `offset_val >= seg->bound` → prints `SEGMENTATION FAULT`, else `strncmp(seg->segment_no, address, 2)` to match the virtual address's top 2 bits → prints segment info and computes `physical_address = seg->base + offset_val`.
- Supports configurable retry loop — generates and translates multiple addresses per run.
- All memory dynamically allocated with `malloc` + freed properly.

**Virtual Address Layout:**
```
16-bit virtual address:
┌──────────┬─────────────────────────────┐
│ bits[15:14]│         bits[13:0]           │
│  seg no   │            offset             │
│  (2 bits) │           (14 bits)           │
└──────────┴─────────────────────────────┘
     ↓                    ↓
  00=CODE           0 – 16383 bytes
  01=DATA           within segment
  10=HEAP
  11=STACK

Physical = segment.base + offset
```

**Significance:** Directly implements the memory segmentation model from OSTEP (Operating Systems: Three Easy Pieces). Demonstrates how virtual-to-physical translation works at the hardware level, including segmentation fault detection — foundational knowledge for OS, hypervisor, and embedded systems work.

---

### 5. 🎟️ Lottery CPU Scheduler Simulation (`lottery-scheduling-simulation`)
**Language:** C &nbsp;|&nbsp; **Field:** Operating Systems  
**Domain:** CPU Scheduling, Probabilistic Algorithms, Linked Lists, Dynamic Memory

**Code-level details (from `lottery_schedule.c`):**
- **Data structure:** `node_t` — singly linked list node: `char *job_name` (`strdup`-allocated), `int tickets`, `int wins`, `struct node *next`
- **`create_job(name, tickets)`:** `malloc` + `strdup` for job name, zero wins
- **`append_job(**head, name, tickets)`:** Appends to tail — O(n) traversal
- **`get_total_tickets(head)`:** Sums all tickets — O(n)
- **`lottery_schedule(head, print_flag)`:** `winner = rand() % total_tickets`; iterates list, accumulates `counter += node->tickets`; when `counter > winner` → that job wins, `wins++`. This is the classic proportional-share selection algorithm.
- **`shorting(**head)`:** In-place bubble sort on linked list nodes by `wins` count — O(n²) with a `do-while(flag)` loop
- **`print_stats(head)`:** `(wins * 100.0) / NUM_RUNS` percentage display
- **`free_job_list(head)`:** Full cleanup with `free(job_name)` + `free(node)`
- **`main()`:** Uses `goto restart` for re-run; `srand(time(NULL))`; job names auto-generated as `Job_A`, `Job_B`, `Job_C` via `snprintf(upd, sizeof(upd), "Job_%c", 65+i)`; `ticket_value = rand() % (10 * jobs)` for varied distribution
- **`#define NUM_RUNS 10`** — default scheduler time slices

**Significance:** Shows understanding that lottery scheduling is O(n) per decision (proportional to number of jobs) — and how real proportional-share schedulers trade algorithmic efficiency for fairness guarantees. Demonstrates clean linked list lifecycle management and user-interactive CLI design.

---

### 6. 🗜️ Huffman File Compressor / Decompressor (`File-Compression`)
**Language:** C &nbsp;|&nbsp; **Field:** Algorithms & Data Structures  
**Domain:** Lossless Compression, Trees, Min-Heap Priority Queue, Bit Manipulation, File I/O

**Code-level details (from `compression.c` — 20 KB, the largest single file in the portfolio):**

**Data structures:**
- `struct MinHeapNode` — tree node: `unsigned char data` (character), `unsigned freq` (frequency), `left`/`right` pointers
- `struct MinHeap` — priority queue: `unsigned size`, `unsigned capacity`, `MinHeapNode **array`

**Core algorithm functions:**
- `newNode(data, freq)` — `malloc` + init
- `createMinHeap(capacity)` — `malloc` for heap + pointer array
- `minHeapify(minHeap, idx)` — recursive heapify: finds `smallest` among `idx`, `2*idx+1`, `2*idx+2`; swaps and recurses
- `buildMinHeap(minHeap)` — runs `minHeapify` from `(n-1)/2` down to 0 (bottom-up build in O(n))
- `extractMin(minHeap)` — removes root, moves last element to root, calls `minHeapify(0)`
- `insertMinHeap(minHeap, node)` — bubble-up insertion in O(log n)
- `buildHuffmanTree(data[], freq[], size)` — the classic algorithm: repeatedly `extractMin` twice → create internal node with `data='$'`, `freq = left->freq + right->freq` → `insertMinHeap` until one node remains (the root)
- `storeCodes(root, arr[], top, huffmanCodes[])` — recursive DFS: `arr[top]=0` for left, `arr[top]=1` for right; at leaf: `malloc` + store binary string in `huffmanCodes[root->data]` (indexed by char value)
- `countFrequency(file, freq[], fileSize)` — `fseek(SEEK_END)/ftell` for size, then reads in 8 KB buffer chunks for efficiency; builds `freq[256]` array
- `compressFile()` — counts frequencies → builds Huffman tree → generates codes → writes header (freq table for reconstruction) → bit-packs encoded data
- `decompressFile()` — reads header → reconstructs Huffman tree → decodes bit stream → writes original bytes
- `validatePath()` — checks input file exists, output path is writable
- `getFileSize()` — uses `struct stat` / `stat()` syscall

**Significance:** A full, production-quality Huffman codec in a single C file (~500 lines). Demonstrates mastery of trees, heap data structures, recursive algorithms, bitwise operations, and buffered file I/O in C. This is the core of real compression tools like zlib/gzip.

---

### 7. 🧱 Data Structures Library (`Data-Structures`)
**Language:** C &nbsp;|&nbsp; **Field:** Computer Science Fundamentals  
**Domain:** Data Structures, Algorithms, Pointer Mechanics

**Actual contents (much broader than just linked lists):**

#### `linked_list.c/` — Linked List Variants
- Singly linked list: insert at head/tail, delete, traverse, `malloc`/`free`
- Doubly linked list: `prev` + `next` pointers, bidirectional traversal
- Circular linked list: tail→head link

#### `sorting/` — Sorting Algorithms
- `bubble_sort.c` — O(n²) comparison-swap, optimized with early-exit flag
- `insertion_sort.c` — O(n²) shift-based insertion
- `selection_sort.c` — O(n²) minimum-finding selection

#### `trees/` — Binary Search Trees
- `bst.c` — BST with `insert`, `inorder`, `search`
- `bst_2.c` — BST with `delete` node (all three cases: leaf, one child, two children using inorder successor)
- `simple_tree.c` — general binary tree with BFS/DFS traversals

#### `stack_Queue/` — Stack, Queue, Applications
- `stack.c` — array-based stack with `push`/`pop`/`peek`
- `simple_queue.c` — array-based queue with `enqueue`/`dequeue`
- `circular_queue.c` — circular buffer queue; avoids wasted space with modular indexing
- `infex_postfix.c` — **infix to postfix expression converter** using a stack — handles operator precedence and parentheses
- `toh.c` — **Tower of Hanoi** recursive solution

#### `c_string_manipulation/` — C String Operations
- String copy, reverse, length, search, tokenization

#### `ptr_arithmatic.c` — Pointer Arithmetic
- Demonstrates pointer increment, array decay, `sizeof` with pointer vs array, void pointer casting

**Significance:** Far more comprehensive than basic linked lists — this is a complete undergraduate-level DSA library covering trees, sorting, stack applications (expression evaluation), and classic recursion problems.

---

### 8. 🔧 UNIX Utilities Re-implementation (`unix-utilities`)
**Language:** C &nbsp;|&nbsp; **Field:** Systems Programming  
**Domain:** UNIX/Linux Internals, File I/O, String Processing

**Implemented utilities (each in its own folder):**

- **`wcat`** (`wcat.c`): Reads files passed as `argv[]` with `fopen()`/`fgets()`/`fclose()`; if `argc < 2` exits silently. Handles multiple files sequentially. Prints `"wcat: cannot open file"` on error.
- **`wgrep`** (`wgrep.c`): Searches for a pattern string in lines of files (or stdin); uses `strstr()` for substring match; handles both file arguments and stdin fallback.
- **`wls`** (`wls/`): Directory listing using `opendir()`/`readdir()`/`closedir()`.
- **`wft`** (`wft/`): File type detection using `stat()` — distinguishes regular files, directories, symlinks.
- **`wzip`** (`wzip/`): Run-length encoding (RLE) compression of files.
- **`wunzip`** (`wunzip/`): RLE decompression.
- **`File Handling/`**: Additional file I/O exercises.

All compiled with `-Wall -Werror` — zero tolerance for warnings.

**Significance:** Re-implementing `cat`, `grep`, `ls` from scratch forces understanding of the POSIX file API, argument parsing, and the UNIX philosophy of composable tools. These are classic OS course projects (OSTEP, Wisconsin CS537).

---

### 9. 🔄 File Reverser (`reverse-file`)
**Language:** C &nbsp;|&nbsp; **Field:** Systems Programming  
**Domain:** File I/O, `fseek`/`fgetc`, inode Comparison, CLI Argument Handling

**Code-level details (from `reverse.c`):**
- **Algorithm:** `fseek(fptr, 0, SEEK_END)` + `ftell(fptr)` to get file size. Then walks backwards byte-by-byte with `fseek(fptr, lr, SEEK_SET)` + `fgetc(fptr)`. When `'\n'` found, flushes accumulated characters in `buffer[]` in reverse order.
- **Three operating modes:**
  1. `argc == 1` (no args): reads stdin line by line into `lines[]` array using `fgets`, then prints from `lines[count-1]` down to `lines[0]`
  2. `argc == 2` (one file): reverses file to stdout
  3. `argc == 3` (two files): reverses input file to output file
- **Same-file detection:** `stat(argv[1], &stat1)` + `stat(argv[2], &stat2)`; compares `st_ino` (inode number) and `st_dev` (device ID) — proper POSIX way to detect if two paths resolve to the same physical file
- **Buffer overflow guard:** `if (idx >= BUFFER_SIZE-1) → BUFFER_FAULT error + exit(1)`
- **`decor.h`:** ANSI escape code macros for colored error output (`FG_RED`, `ITALIC`, `RESET`, `UNDERLINE`)
- **`reverse_linked_list.c`:** bonus — reversal of a doubly linked list in memory

**Significance:** Non-trivial file manipulation — backward byte walking, inode-based same-file detection, three operating modes, error handling matching production CLI tool standards. The `fseek`-backward technique is the same approach used in tools like `tail -r`.

---

### 10. 🖼️ I/O & Persistence Study (`presistance`)
**Language:** (Study/Notes) &nbsp;|&nbsp; **Field:** Operating Systems  
**Domain:** I/O Subsystem, Device Drivers, DMA, Interrupt Handling

**Actual content (from `input_output.md`):** A detailed technical study of the OS I/O stack:

- **Canonical Device Model:** Hardware interface (command registers, status registers, data registers/buffers) + internal implementation (chips, firmware, embedded processor)
- **Basic I/O Protocol:** 4 steps: poll until BUSY clears → write to DATA register → write COMMAND → poll until done
- **Polling inefficiency:** CPU busy-waits in a tight loop — wastes cycles
- **Interrupt-based I/O:** OS puts process to sleep, context-switches; device raises hardware interrupt → ISR → wake sleeping process. Two-phased hybrid: poll briefly first, then sleep
- **Interrupt coalescing:** Device waits before raising interrupt to batch multiple completions into one delivery
- **DMA (Direct Memory Access):** CPU programs DMA controller registers (source address, byte count, device target); DMA controller takes over bus mastering; moves data without CPU involvement per byte; raises interrupt on completion. Shows CPU freed to run other processes during transfer.
- **Port-Mapped I/O vs Memory-Mapped I/O:** Separate I/O address space with `IN`/`OUT` instructions vs. device registers in regular memory address space accessed with `LOAD`/`STORE`

**Significance:** Covers the bottom layer of the OS storage stack — how the kernel talks to hardware. This knowledge is required for device driver development and systems performance analysis.

---

### 11. 🎮 Hangman Game (`hangman`)
**Language:** C &nbsp;|&nbsp; **Field:** Application Development  
**Domain:** CLI Applications, String Matching, Random Selection, ASCII Art

**Code-level details (from `hamgman.c`):**
- **Data structure:** `struct word_hint { char word[50]; char hint[50]; }` — pairs word with its hint
- **Word list:** 6 hardcoded `word_hint` entries (dog, chair, sun, car, fish, cup) — `srand(time(NULL))` + `rand() % 6` for selection
- **State:** `bool guessedletter[26]` — tracks which letters have been tried; `char guessedword[MAX_WORD_LENGTH]` — revealed characters
- **`displayword()`:** Iterates word chars; if `guessedletter[tolower(word[i])-'a']` is true → prints char, else `_`
- **`draw(int tries)`:** `const char *hangman[]` array of 7 ASCII art lines; prints lines 0 through `tries`
- **Win condition:** `strcmp(secretword, guessedword) == 0`
- **Loss condition:** `tries >= MAX_TRIES` (6)
- Handles case insensitivity with `tolower()` throughout

---

### 12. 📜 Bash Scripting Collection (`Bash-scripting`)
**Language:** Bash / Shell &nbsp;|&nbsp; **Field:** Linux Automation / DevOps  
**Domain:** Shell Scripting, Linux System Administration

**Scripts included:**
| Script | Purpose |
|--------|---------|
| `array.bash` | Bash array declaration and iteration |
| `canditional.sh` | `if/elif/else` with numeric and string comparisons |
| `characcess.sh` | Character-level string access in Bash |
| `clargument.sh` | Parsing CLI arguments (`$1`, `$2`, `$#`, `$@`) |
| `dir_check.sh` | Directory existence check with `-d` flag |
| `evenodd.sh` | Even/odd number detection with `$(( n % 2 ))` |
| `file_exist.sh` | File existence and type checks (`-e`, `-f`, `-r`, `-w`) |
| `file_permit.sh` | Reading and checking file permissions |
| `forloop.sh` | `for` loop over ranges and arrays |
| `reading.sh` | `read` for interactive user input |
| `rootcheck.sh` | Root user detection via `$EUID` |
| `run_all.sh` | Batch runner that executes all other scripts |
| `sorting.sh` | Sorting a list of numbers in Bash |
| `switch.bash` | `case` statement pattern matching |
| `triangle.sh` | Nested loop pattern generation (triangle) |
| `whileloop.sh` | `while` loop with counter |

**Also includes:** A comprehensive Linux terminal commands cheat sheet covering file ops, permissions, system monitoring, package management (apt/dnf), networking, and scripting basics.

---

### 13. 🧰 C / C++ Programs Collection (`C-C_Plus_Plus-Programs`)
**Language:** C, C++ &nbsp;|&nbsp; **Field:** Multi-domain  
**Domain:** OOP, Graphics, Audio Processing, DBMS, Games

**Subfolders:**
- **`C++ Concepts/`:** OOP — classes, `this` pointer, method chaining, operator overloading; memory management — `new`/`delete`, dangling pointers, memory leaks; references vs pointers; numeric limits (`INT_MAX`, `DBL_EPSILON`)
- **`Graphics/`:** Terminal-based pattern generation — brick walls, grid patterns, star pyramids, ASCII art visualizations
- **`Hangman V2/`:** Improved hangman with extended word list, debug utilities, better UI
- **`dbms_in_c++/`:** A mini DBMS implementation in C++ — table/record management, basic query operations, demonstrating class design for data management
- **`Audio_processing/`:** Audio signal processing experiments in C/C++

---

### 14. 🖼️ Image Detector / Classifier (`image_detector`)
**Language:** Python &nbsp;|&nbsp; **Field:** Machine Learning / Computer Vision  
**Domain:** Image Classification, Feature Extraction, GUI Applications

**Tech stack:**
- **TensorFlow** — pre-trained model loading and inference
- **OpenCV** (`cv2`) — image reading, preprocessing, feature extraction
- **Pillow** — image format handling and GUI integration
- **NumPy** — array operations on image tensors
- **Matplotlib** — visualization of results

**What it does:** GUI application that loads an image, runs it through a classification model, extracts visual features, and displays the classification result with confidence scores.

---

### 15. 💺 Seat Allocation System (`seat-allocation-sys`)
**Language:** JavaScript / HTML &nbsp;|&nbsp; **Field:** Web Development  
**Domain:** Frontend Logic, Booking Algorithms

A browser-based seat allocation system with booking/cancellation logic — demonstrates frontend problem-solving outside the primary C/Linux context.

---

## 🗺️ Domain Map

```
Tanish Shivhare — Technical Profile
│
├── 🖥️  Operating Systems (Deepest Area)
│   ├── CPU Scheduling         → lottery-scheduling-simulation
│   ├── Virtual Memory         → segmentation-virtual-address-Simulator
│   ├── Concurrency            → Concurrency (locks/semaphore/cv/futex/scalable-counter/HOH)
│   ├── I/O Subsystem          → presistance (polling/interrupt/DMA/MMIO)
│   └── UNIX Internals         → unix-utilities (wcat/wgrep/wls/wzip)
│
├── 🌐  Computer Networking
│   ├── TCP Echo Server        → Server (select()-based, local machine)
│   └── Threaded TCP Server    → server_x (select()+pthread, cross-machine)
│
├── 📐  Algorithms & Data Structures
│   ├── Huffman Compression    → File-Compression (min-heap + bit-pack)
│   ├── BST / Tree             → Data-Structures/trees
│   ├── Stack / Queue          → Data-Structures/stack_Queue (infix-postfix, TOH)
│   ├── Linked Lists           → Data-Structures/linked_list.c + reverse-file
│   └── Sorting                → Data-Structures/sorting
│
├── 🔒  Low-Level Synchronization
│   ├── Spin locks (TAS/flag)  → Concurrency/locks
│   ├── Custom futex mutex     → Concurrency/custom_locks
│   ├── Semaphore patterns     → Concurrency/semaphore
│   ├── Condition variables    → Concurrency/condition variable
│   └── Concurrent DS          → Concurrency/LOCK-BASED CONCURRENT DATA STRUCTURES
│
├── 🐚  Systems / CLI / File I/O
│   ├── File reversal          → reverse-file (fseek-backward + inode check)
│   ├── Bash automation        → Bash-scripting (15+ scripts)
│   ├── C/C++ programs         → C-C_Plus_Plus-Programs (OOP/DBMS/audio/graphics)
│   └── Games                  → hangman + Hangman V2
│
└── 🐍  Python / Machine Learning
    └── Image Classification   → image_detector (TensorFlow + OpenCV)
```

---

## 🤖 Agentic AI Context

> This section provides structured, machine-readable context for AI agents, copilots, code assistants, and automated tools to accurately reason about this developer's skills, project history, and capabilities.

```yaml
developer:
  name: Tanish Shivhare
  handle: TANISHX1
  primary_language: C
  platform: Linux (POSIX / x86_64)
  experience_level: intermediate-to-advanced in systems and OS programming

core_strengths:
  - Low-level C systems programming on Linux (POSIX APIs)
  - POSIX concurrency — pthreads, mutex, semaphore, condition variables
  - Custom lock implementation — futex syscall, _Atomic operations, bit-manipulation locking
  - Advanced concurrent data structures — HOH locking, scalable approximate counters
  - TCP/IP socket programming — server lifecycle, select() multiplexing, threaded handlers
  - OS internals — virtual memory (segmentation), CPU scheduling (lottery), I/O subsystem
  - Classic algorithms — Huffman coding (min-heap), BST, sorting, stack applications
  - UNIX utility implementation — fopen/fseek/stat syscalls, argument parsing
  - Bash scripting — conditionals, loops, file ops, argument handling, sorting

notable_implementations:
  - futex_based_mutex: custom lock using SYS_futex + _Atomic; fast userspace path + kernel sleep
  - scalable_counter: per-CPU local counters + global flush — Linux kernel technique
  - hoh_locking: per-node mutex on linked list for fine-grained concurrent access
  - huffman_codec: full compressor + decompressor from scratch (min-heap, bit-packing)
  - segmentation_simulator: 16-bit VA → physical address translation with segfault detection
  - select_plus_pthread_server: hybrid event loop + per-client thread TCP server
  - producer_consumer: multi-producer multi-consumer with semaphores on bounded buffer

tech_stack:
  languages:
    primary: [C, Bash]
    secondary: [C++, Python]
    basic: [JavaScript, HTML]
  syscalls_used:
    - socket, bind, listen, accept, select, recv, send
    - read, write, open, fopen, fclose, fseek, ftell, fgetc, fgets
    - stat, lstat
    - SYS_futex (FUTEX_WAIT, FUTEX_WAKE)
    - sched_yield, sysconf
    - malloc, calloc, realloc, free, strdup
  libraries: [pthreads, semaphore.h, arpa/inet.h, linux/futex.h, stdatomic.h, termios.h]
  tools: [GCC, GDB, Valgrind, Helgrind, Make, Git, Vim, VS Code]
  python_libs: [TensorFlow, OpenCV, Pillow, NumPy, Matplotlib]
  compile_flags: [-Wall, -Werror, -pthread, -O2]

projects_by_domain:
  operating_systems:
    - name: segmentation-virtual-address-Simulator
      concepts: [virtual memory, address translation, segmentation, MMU, segfault]
      lang: C
    - name: lottery-scheduling-simulation
      concepts: [proportional-share scheduling, randomized algorithms, linked list]
      lang: C
    - name: Concurrency
      concepts: [pthreads, mutex, semaphore, condition_variable, futex, TAS spinlock,
                 HOH_locking, scalable_counter, producer_consumer, reader_writer]
      lang: C
    - name: presistance
      concepts: [canonical_device, polling, interrupts, DMA, MMIO, port_mapped_IO]
      lang: notes/C
    - name: unix-utilities
      concepts: [syscalls, file_IO, UNIX_internals, wcat, wgrep, wls, wzip]
      lang: C
  networking:
    - name: Server
      concepts: [TCP, select(), fd_set, echo_server, I/O_multiplexing]
      lang: C
    - name: server_x
      concepts: [TCP, select, pthread, INADDR_ANY, cross_machine, debug_modes, mutex]
      lang: C
  algorithms_dsa:
    - name: File-Compression
      concepts: [Huffman_coding, min_heap, priority_queue, bit_packing, lossless_compression]
      lang: C
    - name: Data-Structures
      concepts: [linked_list, BST, stack, queue, circular_queue, infix_postfix, tower_of_hanoi,
                 bubble_sort, insertion_sort, selection_sort, ptr_arithmetic]
      lang: C
    - name: reverse-file
      concepts: [fseek_backward, inode_comparison, stat(), CLI_modes, file_IO]
      lang: C
  machine_learning:
    - name: image_detector
      concepts: [image_classification, feature_extraction, TensorFlow, OpenCV, GUI]
      lang: Python
  cli_systems:
    - name: hangman
      concepts: [word_guessing, ASCII_art, string_matching, tolower, struct]
      lang: C
    - name: Bash-scripting
      concepts: [conditionals, loops, arrays, file_checks, sorting, arg_parsing, root_check]
      lang: Bash
    - name: C-C_Plus_Plus-Programs
      concepts: [OOP, classes, DBMS, audio_processing, graphics_patterns, memory_management]
      lang: [C, C++]
  web:
    - name: seat-allocation-sys
      concepts: [seat_booking, frontend_logic]
      lang: JavaScript

suitable_for_tasks:
  - Systems and OS-level C development on Linux
  - Network programming (TCP sockets, server architectures, multiplexing)
  - Concurrent and parallel programming (pthreads, synchronization primitives)
  - Custom lock/synchronization implementation (futex, spinlock, semaphore)
  - Algorithm implementation in C (trees, heaps, sorting, compression)
  - UNIX shell scripting and Linux automation
  - Low-level file I/O and system call usage
  - Basic ML/computer vision prototyping in Python

not_yet_experienced_in:
  - Linux kernel module / driver development
  - Large-scale distributed systems (Raft, Paxos, consensus)
  - Cloud infrastructure / CI-CD / DevOps pipelines
  - Backend web frameworks (Flask, FastAPI, Node.js)
  - Database internals (B-tree implementations, WAL)
  - Mobile or embedded (bare-metal) development

reading_influences:
  - OSTEP (Operating Systems: Three Easy Pieces) — Arpaci-Dusseau
  - POSIX programmer's manual
  - Linux man pages

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
