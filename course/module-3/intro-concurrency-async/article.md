# Concurrency, parallelism and asynchrony in Python

Suppose our program needs to download 100 pages from different sites. Done one by one, each request waits 1-2 seconds for the server, and the total time is the sum of all those waits. Most of that time the CPU sits idle. The fix is obvious: while one request waits, send the others. That's **concurrent execution**.

Python has three tools for this: `threading`, `multiprocessing`, `asyncio`. This article is the map: which to use when.

## Three concepts

People mix up three similar words when talking about concurrency. The distinction matters:

-   **Concurrency**: tasks can switch among themselves, creating the illusion of simultaneous work. One barista at the counter takes an order, starts the milk heating, moves to the next customer, comes back to the milk. One worker, several tasks "in the air" at once.

-   **Parallelism**: tasks run **physically at the same time** on different CPU cores. Several baristas, each making their own coffee. Requires multiple cores.

-   **Asynchrony**: a code organization style where a task can be "deferred" while waiting (for example, for a server reply) without blocking the entire thread. It's a **way to achieve concurrency** on a single thread, without OS-level switching.

Concurrency is the goal; parallelism and asynchrony are two ways to achieve it.

## I/O-bound vs CPU-bound: the key dichotomy

The choice of tool depends **only** on what your task is waiting for:

-   **I/O-bound**: the CPU sits idle while an external resource responds. Network request, disk read, database reply. Asynchrony wins here: while one request waits, fire the next ones.

-   **CPU-bound**: the CPU is genuinely busy with computation. Image compression, encryption, scientific computing. Real parallelism across cores is what helps.

The most common beginner mistake: reaching for `multiprocessing` to download pages, or `asyncio` to multiply matrices. That gives a slowdown, not a speedup.

## GIL: why threading doesn't help with CPU

CPython (the standard implementation) has a **Global Interpreter Lock (GIL)**, a global lock that allows only one thread to execute Python code at a time within a process. Even with 8 cores, threads take turns.

The consequence:

-   For **CPU-bound** tasks, `threading` is useless: threads share a single core through the GIL. You need processes (`multiprocessing`); each has its own GIL and its own core.
-   For **I/O-bound** tasks the GIL is **released** while waiting for network/disk. So `threading` works great for I/O, as does `asyncio` (but without the overhead of OS threads).

## Which tool when

| Task | Tool |
| --- | --- |
| Many network requests, thousands of connections | **asyncio** |
| I/O in legacy code without async libraries | **threading** |
| Heavy computation on multiple cores | **multiprocessing** |
| Simple parallelization without diving in | **concurrent.futures** (`ThreadPoolExecutor` / `ProcessPoolExecutor`) |

The next articles cover each in turn. First **threads and processes**, then **asyncio** (two articles: basics and advanced).

## What's next?

The next article covers **threads and processes**: the two classic approaches. A thread is a lightweight worker inside a single process (for I/O); a process is a separate program with its own memory (for CPU-bound).

---

**Which statement most accurately describes the difference between parallelism and asynchrony?**

