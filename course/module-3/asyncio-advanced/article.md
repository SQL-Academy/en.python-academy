# Advanced asyncio in Python: Key Tools

In the previous article, we familiarized ourselves with the basics of `asyncio`. Now, let's look at key advanced concepts often used when writing real-world asynchronous applications: asynchronous generators and context managers, queues for data exchange, basic synchronization primitives, and integration with blocking code.

## Asynchronous Generators (async for)

Similar to regular generators, **asynchronous generators** allow iterating over a sequence of data asynchronously without loading it all into memory. They are defined with `async def` and use `yield`. They are iterated over using `async for`.

```python-executable
import asyncio

async def async_number_generator(limit):
    for i in range(limit):
        await asyncio.sleep(0.5) # Simulate asynchronous operation
        yield i

async def main_gen():
    print("Starting iteration over async generator:")
    async for number in async_number_generator(5):
        print(f"Received number: {number}")

if __name__ == "__main__":
    # Ensure the example runs from the main thread
    # or use the appropriate asyncio startup method for your environment.
    try:
        asyncio.run(main_gen())
    except RuntimeError as e:
        # In some environments (like Jupyter), get_event_loop() might be needed
        if "cannot run current event loop" in str(e):
             print("asyncio.run() failed. Try a different method to run the event loop.")
        else:
             raise e

```

`async for` will wait (`await`) to receive each next element from the asynchronous generator.

## Asynchronous Context Managers (async with)

Context managers (`with`) are useful for managing resources. **Asynchronous context managers** extend this for asynchronous operations. They implement `__aenter__` and `__aexit__` methods (which can be `async`) and are used with `async with`.

```python-executable
import asyncio

class AsyncResource:
    def __init__(self, name):
        self.name = name

    async def __aenter__(self): # Asynchronous entry
        print(f"Resource '{self.name}': entering (acquiring resource...)")
        await asyncio.sleep(0.5) # Simulate asynchronous operation
        print(f"Resource '{self.name}': acquired.")
        return self

    async def __aexit__(self, exc_type, exc_val, exc_tb): # Asynchronous exit
        print(f"Resource '{self.name}': exiting (releasing resource...)")
        await asyncio.sleep(0.5) # Simulate asynchronous operation
        print(f"Resource '{self.name}': released.")
        if exc_type:
            print(f"An exception occurred: {exc_val}")
        # return True # If True, the exception will be suppressed

async def use_async_resource():
    async with AsyncResource("DB_Connection") as resource:
        print(f"Using resource '{resource.name}'...")
        await asyncio.sleep(1)
        print("Finished using resource.")

# The if __name__ == "__main__" block and try/except are similar to the previous example
if __name__ == "__main__":
    try:
        asyncio.run(use_async_resource())
    except RuntimeError as e:
        if "cannot run current event loop" in str(e):
             print("asyncio.run() failed. Try a different method to run the event loop.")
        else:
             raise e
```

## Working with Network Protocols and Streams

`asyncio` provides a low-level API for working with network data streams (TCP) via `StreamReader` and `StreamWriter`, obtainable through `asyncio.open_connection` and `asyncio.start_server`. This allows creating asynchronous TCP clients and servers for any protocol.

However, for standard protocols like HTTP/HTTPS, it's usually more convenient to use high-level libraries built on `asyncio`, such as:

-   **`aiohttp`**: A popular library for creating asynchronous HTTP clients and servers.
-   **`httpx`**: A modern HTTP client that supports both synchronous and asynchronous requests.

These libraries abstract the details of working with Streams, providing a simpler interface for web interactions.

## Asynchronous Queues (asyncio.Queue)

`asyncio.Queue` is the primary way to safely exchange data between different asynchronous tasks (`Task`) within the same event loop. The API is similar to `queue.Queue` but uses `await`.

-   `await queue.put(item)`: Add an item.
-   `await queue.get()`: Remove an item (waits if the queue is empty).
-   `queue.task_done()` / `await queue.join()`: For coordinating the completion of item processing.

```python-executable
import asyncio
import random

async def producer_async(q, n_items):
    for i in range(n_items):
        item = f"AsyncItem-{i}"
        await asyncio.sleep(random.uniform(0.1, 0.3))
        await q.put(item)
        print(f"Producer: added {item} (queue: {q.qsize()})")

async def consumer_async(name, q):
    while True:
        item = await q.get()
        print(f"Consumer {name}: got {item}")
        await asyncio.sleep(random.uniform(0.2, 0.5)) # Simulate processing
        q.task_done()
        print(f"Consumer {name}: processed {item}")

async def main_queue():
    q = asyncio.Queue(maxsize=3) # Queue with a size limit

    # Start producers and consumers as tasks
    producers = [asyncio.create_task(producer_async(q, 5)) for _ in range(2)]
    consumers = [asyncio.create_task(consumer_async(f"C-{i}", q)) for i in range(3)]

    # Wait for producers to add all items
    await asyncio.gather(*producers)
    print("-- Producers finished --")

    # Wait for consumers to process all items in the queue
    await q.join()
    print("-- All items processed --")

    # Gracefully stop consumers (since they are in an infinite loop)
    for c in consumers:
        c.cancel()

    # Allow tasks to handle cancellation
    await asyncio.gather(*consumers, return_exceptions=True)
    print("-- Consumers stopped --")

# The if __name__ == "__main__" block and try/except are similar to previous examples
if __name__ == "__main__":
    try:
        asyncio.run(main_queue())
    except RuntimeError as e:
        if "cannot run current event loop" in str(e):
             print("asyncio.run() failed. Try a different method to run the event loop.")
        else:
             raise e
```

## Synchronization Primitives in asyncio

To coordinate coroutines and protect shared resources, `asyncio` provides analogues to the primitives in `threading`, but adapted for asynchronicity (they yield control to the event loop instead of blocking the thread).

The most basic is **`asyncio.Lock`**. It ensures that only one coroutine can execute code within an `async with lock_async:` block. It's used to protect critical sections.

```python-executable
import asyncio

shared_counter_async = 0
lock_async = asyncio.Lock()

async def increment_async(n_times):
    global shared_counter_async
    for _ in range(n_times):
        async with lock_async: # Acquire the lock
            # Critical section: only one coroutine can be here at a time
            current_val = shared_counter_async
            await asyncio.sleep(0.001) # Simulate work within the section
            shared_counter_async = current_val + 1
        # Lock is automatically released upon exiting async with

async def main_lock_example():
    tasks = [increment_async(1000) for _ in range(5)] # Reduced count for speed
    await asyncio.gather(*tasks)
    print(f"Final counter (expected 5000): {shared_counter_async}")

# The if __name__ == "__main__" block and try/except are similar to previous examples
if __name__ == "__main__":
    try:
        asyncio.run(main_lock_example())
    except RuntimeError as e:
        if "cannot run current event loop" in str(e):
             print("asyncio.run() failed. Try a different method to run the event loop.")
        else:
             raise e
```

Other primitives like `asyncio.Event` (for signaling between coroutines), `asyncio.Semaphore` (to limit concurrent access to a resource), and `asyncio.Condition` (for more complex synchronization) are also available but used less frequently than `Lock` and `Queue`.

## Running Blocking Code in asyncio

What if you need to call a function that blocks the thread (e.g., a legacy library or a CPU-bound calculation) from asynchronous code? Calling it directly will block the entire event loop. The solution is `loop.run_in_executor()`.

This function allows executing a blocking function in a separate thread (using `ThreadPoolExecutor` by default) or process (`ProcessPoolExecutor`), without stopping the `asyncio` event loop.

```python-executable
import asyncio
import time
import concurrent.futures
import threading # Added import

def blocking_io_operation(duration):
    print(f"[Thread {threading.current_thread().name}] Blocking operation: starting, sleeping {duration} sec...")
    time.sleep(duration) # Regular, blocking sleep
    print(f"[Thread {threading.current_thread().name}] Blocking operation: finished.")
    return f"Result from {duration} sec."

async def main_blocking():
    loop = asyncio.get_running_loop() # Get the current event loop

    print("Starting blocking operation in executor...")

    # Run blocking_io_operation in the default ThreadPoolExecutor
    # The first argument None means use the default executor
    result_future = loop.run_in_executor(None, blocking_io_operation, 2)

    # While the blocking operation runs in another thread,
    # asynchronous code can continue:
    print("Async code runs CONCURRENTLY with the blocking operation...")
    await asyncio.sleep(1)
    print("Async code is still running...")

    # Wait for the result from the executor
    result = await result_future
    print(f"Received result from executor: {result}")

# The if __name__ == "__main__" block and try/except are similar to previous examples
if __name__ == "__main__":
    try:
        asyncio.run(main_blocking())
    except RuntimeError as e:
        if "cannot run current event loop" in str(e):
             print("asyncio.run() failed. Try a different method to run the event loop.")
        else:
             raise e
```

## What's Next?

We have reviewed key advanced `asyncio` tools: `async for`, `async with`, `asyncio.Queue`, `asyncio.Lock`, and `run_in_executor`. They form the basis for building most real-world asynchronous applications in Python.

In the final article, we will compare all the concurrency approaches discussed (`threading`, `multiprocessing`, `asyncio`), discuss `concurrent.futures`, and review general best practices.

---

**Which asyncio function is used to safely execute blocking code without stopping the event loop?**
