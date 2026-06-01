# Advanced asyncio in Python

The previous article covered the basics of asyncio: `async def`, `await`, `gather`, Tasks. Here are three tools you'll reach for in real applications: queues between coroutines, synchronization, and (most importantly) running blocking code without stalling the event loop.

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

The cardinal rule of asyncio: **never call blocking functions directly**. `time.sleep(2)`, `requests.get()`, heavy math — anything blocking stalls the event loop and freezes every other coroutine.

But sometimes there's no avoiding it: an old synchronous library, a CPU-bound calculation. For that there's `loop.run_in_executor()`: run a blocking function in a **separate thread** (or process) while the event loop keeps going.

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

| | threading | multiprocessing | asyncio |
| --- | --- | --- | --- |
| CPU parallelism | no (GIL) | yes | no (1 thread) |
| I/O-bound | good | good but expensive | great |
| Overhead | low | high | minimal |
| Memory | shared | isolated | shared (1 thread) |
| Sharing data | variables + Lock / Queue | Queue, Pipe, Manager | variables / asyncio.Queue |
| Thousands of tasks | poor | very poor | excellent |

**Rule of thumb:**

-   Thousands of network connections, new projects → **asyncio**
-   I/O in legacy synchronous code without async libraries → **threading** or `ThreadPoolExecutor`
-   Heavy computation → **multiprocessing** or `ProcessPoolExecutor`
-   Real apps often mix all three: asyncio as the main layer + `run_in_executor` with a thread/process pool for blocking pieces.

## A few pitfalls

-   **CPU-bound in asyncio** kills the event loop. Use `run_in_executor` with a `ProcessPoolExecutor` for heavy math inside async code.
-   **Forgotten `await`**: `asyncio.sleep(1)` without `await` does nothing (it creates a coroutine that gets discarded). Modern IDEs highlight this.
-   **Mixing sync/async**: calling `requests.get()` (synchronous) inside asyncio stalls everything. Use `aiohttp` or `httpx` for async HTTP.
-   **`if __name__ == "__main__":`** on Windows and macOS is required for `multiprocessing`; otherwise child processes recursively spawn themselves.

## What's next?

That wraps up the concurrency module. The key rule: pick the tool by task type.

-   Thousands of network connections → **asyncio**
-   I/O in legacy code → **threading**
-   Math and data crunching → **multiprocessing**

In modern apps the main program tends to be asyncio, with CPU-heavy pieces offloaded into a process pool through `run_in_executor`.

---

**Which asyncio tool runs blocking code without stalling the event loop?**

