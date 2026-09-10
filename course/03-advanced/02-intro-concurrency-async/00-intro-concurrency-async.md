---
meta:
    title: "Concurrency, parallelism and asynchrony in Python"
    description: "Three ways to run tasks seemingly at once: threads, processes, asyncio. What GIL is and which tool to pick when."
---

# Concurrency, parallelism and asynchrony in Python

Suppose our program needs to download 100 pages from different sites. Done one by one, each request waits 1-2 seconds for the server, and the total time is the sum of all those waits. Most of that time the CPU sits idle. The fix is obvious: while one request waits, send the others. That's **concurrent execution**.

Python has three tools for this: `threading`, `multiprocessing`, `asyncio`. This article is the map: which to use when.

First, let's see the problem for real. Three "downloads" one after another, each waiting half a second:

```python
import time

def download(url):
    time.sleep(0.5)          # simulating the wait for a server response
    return f"data from {url}"

start = time.time()
for url in ["site-1", "site-2", "site-3"]:
    print(download(url))
<output>
data from site-1
data from site-2
data from site-3
</output>
print(f"Took {time.time() - start:.1f} s")
<output>
Took 1.5 s
</output>
```

Three requests — a second and a half, and the CPU did nothing the whole time, just waited for responses. Next, three different ways to fit those same requests into half a second.

## Three concepts

People mix up three similar words when talking about concurrency. The distinction matters:

- **Concurrency**: tasks can switch among themselves, creating the illusion of simultaneous work. One barista at the counter takes an order, starts the milk heating, moves to the next customer, comes back to the milk. One worker, several tasks "in the air" at once.

- **Parallelism**: tasks run **physically at the same time** on different CPU cores. Several baristas, each making their own coffee. Requires multiple cores.

- **Asynchrony**: a code organization style where a task can be "deferred" while waiting (for example, for a server reply) without blocking the entire thread. It's a **way to achieve concurrency** on a single thread, without OS-level switching.

Concurrency is the goal; parallelism and asynchrony are two ways to achieve it.

![Illustration: three timelines — parallelism (two tasks at once on two cores), concurrency (one timeline with alternating tasks), asynchrony (one timeline where await pauses are filled by other tasks)](https://python-academy.org/static/guidePage/intro-concurrency-async/concurrency-models-en.webp "Three concurrency models: parallelism, task switching, asynchrony")

## I/O-bound vs CPU-bound: the key dichotomy

The choice of tool depends **only** on what your task is waiting for:

- **I/O-bound**: the CPU sits idle while an external resource responds. Network request, disk read, database reply. Asynchrony wins here: while one request waits, fire the next ones.

- **CPU-bound**: the CPU is genuinely busy with computation. Image compression, encryption, scientific computing. Real parallelism across cores is what helps.

The most common beginner mistake: reaching for `multiprocessing` to download pages, or `asyncio` to multiply matrices. That gives a slowdown, not a speedup.

## GIL: why threading doesn't help with CPU

CPython (the standard implementation) has a **Global Interpreter Lock (GIL)**, a global lock that allows only one thread to execute Python code at a time within a process. Even with 8 cores, threads take turns.

The consequence:

- For **CPU-bound** tasks, `threading` is useless: threads share a single core through the GIL. You need processes (`multiprocessing`); each has its own GIL and its own core.
- For **I/O-bound** tasks the GIL is **released** while waiting for network/disk. So `threading` works great for I/O, as does `asyncio` (but without the overhead of OS threads).

## Which tool when

| Task                                            | Tool                                                                  |
| ----------------------------------------------- | --------------------------------------------------------------------- |
| Many network requests, thousands of connections | **asyncio**                                                           |
| I/O in legacy code without async libraries      | **threading**                                                         |
| Heavy computation on multiple cores             | **multiprocessing**                                                   |
| Simple parallelization without diving in        | **concurrent.futures** (`ThreadPoolExecutor` / `ProcessPoolExecutor`) |

The next articles cover each in turn. First **threads and processes**, then **asyncio** (two articles: basics and advanced).

## Understanding check

**Which of the following is true about concurrency in Python?**

1. Parallelism and asynchrony are synonyms — They're different concepts. Parallelism is about physically simultaneous execution (needs multiple cores). Asynchrony is about switching tasks on a single thread while waiting for I/O.

2. **Correct answer:** Parallelism is physically simultaneous execution on multiple cores; asynchrony is task switching on a single thread during waits — Parallelism needs multicore hardware and helps CPU-bound work. Asynchrony works effectively on a single thread for I/O-bound work.

3. Asynchrony always requires a multicore CPU — The opposite: asynchrony works just fine on a single core, switching between tasks while they wait. Multicore is required for true parallelism.

4. Python's GIL makes threading useless for all kinds of tasks — The GIL only limits CPU-bound work. For I/O-bound work, threads are effective because the GIL is released while waiting for network/disk.

The next article covers **threads and processes**: the two classic approaches. A thread is a lightweight worker inside a single process (for I/O); a process is a separate program with its own memory (for CPU-bound).
