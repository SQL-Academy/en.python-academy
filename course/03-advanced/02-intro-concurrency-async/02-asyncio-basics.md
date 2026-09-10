---
meta:
    title: "asyncio basics: async/await, coroutines, the event loop"
    description: "Basic asynchronous programming in Python: async and await keywords, coroutines, the event loop, tasks, and asyncio.gather."
---

# asyncio basics in Python

Three downloads from the intro article took a second and a half — and for almost all of it the CPU just waited for the server to answer. Threads and processes solved that by handing the switching to the operating system. **asyncio** takes a different route: a single thread with cooperative multitasking. While one request waits on the server, that same thread picks up the next, and the program itself marks the points where a task can be "parked" with the keyword `await`.

For I/O-bound workloads, asyncio gives the best ratio of throughput to resources: thousands of concurrent connections on a single thread with no OS-thread overhead.

## The event loop and the cooperative model

The heart of asyncio is the **event loop**. It keeps a list of tasks, runs one of them, and when a task hits `await something_slow()`, the task "yields control" and the loop switches to the next ready task. When `something_slow()` finishes, the original task becomes ready again.

![Illustration: in the centre an Event Loop, around it three tasks (Task 1 — running, Task 2 — awaiting I/O, Task 3 — ready); arrows between the tasks and event loop show how control is handed back and forth](https://python-academy.org/static/guidePage/asyncio-basics/event-loop-en.webp "The event loop hands control between tasks on every await")

Important: switching happens **only at `await`**. There are no interruptions in the middle of a computation. It's "cooperative" multitasking: tasks agree on when to yield. Consequence: if a task doesn't hit `await` (for example, it does heavy CPU work), the entire event loop is stuck.

## async and await

Python 3.5 introduced two keywords:

- `async def` defines a **coroutine** (an asynchronous function)
- `await` inside a coroutine: "wait for this operation, and let the event loop run other things while you wait"

```python
import asyncio

async def say_hello():
    print("Hello...")
    await asyncio.sleep(1)        # non-blocking, hands control back
    print("...world")
```

**Important detail**: calling `say_hello()` **doesn't run** the coroutine. It creates a coroutine object:

```python
coro = say_hello()
print(type(coro))     # <class 'coroutine'>
# the code inside hasn't run yet!
```

To run a coroutine, you need an event loop.

## asyncio.run: the entry point

`asyncio.run()` starts an event loop, runs the given coroutine, and closes the loop:

```python
import asyncio

async def main():
    print("Start")
    await asyncio.sleep(1)
    print("Done after 1 second")

asyncio.run(main())
```

`asyncio.run()` is the standard way to launch an async program from synchronous code. It's called **once** at the top level of your application.

## Sequential vs concurrent

Stacking `await` one after another runs coroutines **sequentially**, one at a time:

```python
import asyncio
import time

async def slow_task(name, delay):
    await asyncio.sleep(delay)
    print(f"{name} done in {delay}s")

async def main():
    start = time.time()
    await slow_task("A", 2)
    await slow_task("B", 1)
    await slow_task("C", 3)
    print(f"Total: {time.time() - start:.1f}s")    # ~6s

asyncio.run(main())
```

The three tasks could run in parallel (they only wait), but we forced them to take turns: `await` waits for the current one to finish. To run them **concurrently**, use `asyncio.gather()`:

```python
async def main():
    start = time.time()
    await asyncio.gather(
        slow_task("A", 2),
        slow_task("B", 1),
        slow_task("C", 3),
    )
    print(f"Total: {time.time() - start:.1f}s")    # ~3s

asyncio.run(main())
```

`gather()` runs the coroutines concurrently and returns a list of results. The total time equals the **longest** task, not the sum. That's the point of asyncio for I/O.

## Tasks: running coroutines in the background

Sometimes you want to start a coroutine "right now" without awaiting it, so it runs alongside the main logic. Use `asyncio.create_task()`:

```python
import asyncio

async def background_log():
    while True:
        print("heartbeat")
        await asyncio.sleep(1)

async def main():
    task = asyncio.create_task(background_log())
    await asyncio.sleep(3)      # do something else
    task.cancel()               # stop the background coroutine

asyncio.run(main())
```

`create_task()` schedules the coroutine for execution immediately. It returns a **Task** object with `cancel()`, `done()`, `result()`. A Task is essentially a coroutine the event loop has already started and is tracking: you can check its status, grab the result, or cancel it.

## Key rules

1. **Inside `async def`, every long wait goes through `await`**. A plain `time.sleep(1)` blocks the entire event loop. Use `await asyncio.sleep(1)`.
2. **Want them to run concurrently? `asyncio.gather()` or `asyncio.create_task()`**. Plain stacked `await` = sequential.
3. **CPU-bound in asyncio stalls everything**. Long computation? Move it to `run_in_executor` (next article) or `multiprocessing`.

## Understanding check

**What is the core idea of asyncio's concurrency model?**

1. True parallelism on multiple CPU cores — That describes multiprocessing. asyncio runs on a single thread and doesn't help CPU-bound work.

2. **Correct answer:** Cooperative multitasking: tasks explicitly hand control back to the event loop at await — Correct. Coroutines yield control at await, letting other tasks run while one waits for I/O. This is the fundamental principle of asyncio.

3. Automatic parallelization of any function without async/await — Not automatic. A function becomes a coroutine only via async def, and switching only happens at await.

4. Creating a system thread per task — The opposite — asyncio runs in a single thread. That's exactly its win for I/O: no thread-creation overhead.

The next article covers asyncio's advanced techniques: queues, coordination between coroutines, and (the most important one) how to run blocking code without killing the event loop.
