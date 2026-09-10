---
meta:
    title: "Advanced asyncio: queues, synchronization, executor"
    description: "asyncio.Queue for sharing data between coroutines, asyncio.Lock for synchronization, and most importantly run_in_executor for blocking code without stalling the event loop."
---

# Advanced asyncio in Python

An async service was holding thousands of connections and then froze all at once, for everyone. The cause was one line: someone called a synchronous `requests.get()`, and for a second it blocked the single event-loop thread, and with it every other coroutine.

The asyncio basics from the previous article (`async def`, `await`, `gather`, Tasks) aren't enough here. For real applications you need three more tools: queues between coroutines, synchronization, and, above all, running blocking code without stalling the event loop.

## asyncio.Queue: passing data between coroutines

In asyncio all coroutines run in a single thread and can share state directly. But for the **producer-consumer** pattern, a queue is more comfortable:

```python
import asyncio

async def producer(q):
    for i in range(5):
        await q.put(f"item-{i}")
        await asyncio.sleep(0.1)
    await q.put(None)            # stop signal

async def consumer(q):
    while True:
        item = await q.get()
        if item is None:
            break
        print(f"Got {item}")

async def main():
    q = asyncio.Queue()
    await asyncio.gather(producer(q), consumer(q))

asyncio.run(main())
```

The API mirrors `queue.Queue`, but the methods are coroutines (`await q.put`, `await q.get`). The queue blocks on an empty `get()` or a full `put()` (if `maxsize` is set), but it doesn't block the thread; it yields to the event loop.

## asyncio.Lock: protecting shared state

In asyncio, switching only happens at `await`. If a critical section between two `await`s changes shared state, no other coroutine can sneak in. But if there's an `await` inside the critical section, another coroutine can.

```python
import asyncio

counter = 0
lock = asyncio.Lock()

async def increment():
    global counter
    async with lock:
        current = counter
        await asyncio.sleep(0.01)     # await INSIDE the critical section
        counter = current + 1

async def main():
    await asyncio.gather(*(increment() for _ in range(100)))
    print(counter)        # 100 — correct thanks to the lock

asyncio.run(main())
```

Without the lock several coroutines would read the same `current` value, and the total would come out below 100. With `async with lock:` only one coroutine can sit inside the critical section at a time.

In real asyncio code, locks are needed **rarely**, because most variables live inside a single coroutine. `Lock` is useful when several coroutines read/write a shared structure or resource — for example, a shared counter of active connections or a cache.

Besides `Lock` there's `asyncio.Event`, `asyncio.Semaphore`, `asyncio.Condition` (API mirrors `threading`, but operations go through `await`).

## Blocking code in asyncio: run_in_executor

Back to the outage from the start of the chapter. The rule broken there: **never call blocking functions directly in the event loop**. `time.sleep(2)`, `requests.get()`, heavy math — anything blocking stalls the whole loop.

But sometimes there's no avoiding it: an old synchronous library, a CPU-bound calculation. For that there's `loop.run_in_executor()`: run a blocking function in a **separate thread** (or process) while the event loop keeps going.

![Illustration: event loop with await blocking_task() on the left; arrow labeled run_in_executor leads to a Thread Pool Executor on the right where time.sleep(2) runs; below, other coroutines keep running; arrow returns the result back to the event loop](https://python-academy.org/static/guidePage/asyncio-advanced/run-in-executor-en.webp "run_in_executor offloads a blocking function into a thread pool so the event loop is not blocked")

```python
import asyncio
import time

def blocking_io():
    print("Blocking function: sleeping 2s")
    time.sleep(2)                    # synchronous sleep
    return "done"

async def main():
    loop = asyncio.get_running_loop()
    print("Submitting blocking task to executor")

    # None = default executor (ThreadPoolExecutor)
    future = loop.run_in_executor(None, blocking_io)

    # while the blocking task runs, the event loop is free
    await asyncio.sleep(1)
    print("Event loop is still working")

    result = await future
    print(f"Result: {result}")

asyncio.run(main())
```

`run_in_executor(None, func, *args)` submits `func(*args)` to the default `ThreadPoolExecutor` (the very same one we met in the threads-and-processes article) and returns a future you can `await`.

For CPU-bound work, pass a `ProcessPoolExecutor` as the first argument; the function will run in a separate process with its own GIL.

## async iteration and context managers

If an object produces data incrementally (over the network, say), it can be an **async iterator** iterable via `async for`:

```python
async for line in aiohttp_response:
    process(line)
```

If a resource needs to be opened and closed asynchronously (a DB connection), use an **async context manager** with `async with`:

```python
async with aiohttp.ClientSession() as session:
    async with session.get(url) as response:
        data = await response.json()
```

You'll rarely write these yourself; they're tools libraries (`aiohttp`, `asyncpg`, `aioredis`) expose. It's enough to know they exist and to recognize `async for` / `async with` in other people's code.

## Comparing the three approaches

|                    | threading                | multiprocessing      | asyncio                   |
| ------------------ | ------------------------ | -------------------- | ------------------------- |
| CPU parallelism    | no (GIL)                 | yes                  | no (1 thread)             |
| I/O-bound          | good                     | good but expensive   | great                     |
| Overhead           | low                      | high                 | minimal                   |
| Memory             | shared                   | isolated             | shared (1 thread)         |
| Sharing data       | variables + Lock / Queue | Queue, Pipe, Manager | variables / asyncio.Queue |
| Thousands of tasks | poor                     | very poor            | excellent                 |

**Rule of thumb:**

- Thousands of network connections, new projects → **asyncio**
- I/O in legacy synchronous code without async libraries → **threading** or `ThreadPoolExecutor`
- Heavy computation → **multiprocessing** or `ProcessPoolExecutor`
- Real apps often mix all three: asyncio as the main layer + `run_in_executor` with a thread/process pool for blocking pieces.

## A few pitfalls

- **CPU-bound in asyncio** kills the event loop. Use `run_in_executor` with a `ProcessPoolExecutor` for heavy math inside async code.
- **Forgotten `await`**: `asyncio.sleep(1)` without `await` does nothing (it creates a coroutine that gets discarded). Modern IDEs highlight this.
- **Mixing sync/async**: calling `requests.get()` (synchronous) inside asyncio stalls everything. Use `aiohttp` or `httpx` for async HTTP.
- **`if __name__ == "__main__":`** on Windows and macOS is required for `multiprocessing`; otherwise child processes recursively spawn themselves.

## Understanding check

**Which asyncio tool runs blocking code without stalling the event loop?**

1. asyncio.sleep() — asyncio.sleep() is a non-blocking pause; it's already async itself.

2. asyncio.gather() — gather() runs several coroutines concurrently. It doesn't help with blocking SYNCHRONOUS functions — they would still freeze the event loop.

3. **Correct answer:** loop.run_in_executor() — Correct. run_in_executor pushes a blocking function into a separate thread (or process), and the event loop keeps running other coroutines.

4. asyncio.create_task() — create_task() schedules a coroutine on the event loop. It doesn't help with SYNCHRONOUS blocking functions — they would still freeze the loop.

That wraps up the concurrency module. The map from the intro article and the matrix above already answer "which tool"; in practice the main program most often lives on asyncio, handing CPU-heavy pieces to a process pool through `run_in_executor`.
