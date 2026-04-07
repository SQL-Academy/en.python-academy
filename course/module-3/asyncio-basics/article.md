# Asynchronous Programming in Python: asyncio Basics

In previous articles, we looked at multithreading (`threading`) and multiprocessing (`multiprocessing`) as ways to achieve concurrency in Python. Now, we will explore a completely different approach — **asynchronous programming** using the `asyncio` module.

`asyncio` allows writing single-threaded concurrent code using the concept of coroutines. This is particularly effective for I/O-bound tasks, where performance can be significantly higher than multithreaded solutions due to lower overhead.

## What is Asynchronous Programming?

Imagine your program is performing a task that requires accessing an external resource, like downloading a file from the internet. In a traditional (synchronous) approach, the program would simply wait until the file is fully downloaded before moving to the next line of code. If there are many such operations, the total execution time can become very long, as the processor spends most of its time idle, waiting.

**Asynchronous programming** offers a different approach. It allows the program to start an operation that requires waiting (an I/O operation) and, without waiting for it to complete, switch to executing other tasks. When the awaited operation finishes, the program can return to process its result.

In `asyncio`, this is achieved through **cooperative multitasking**:

-   Tasks (coroutines) explicitly signal when they are ready to yield control (usually when waiting for an I/O operation to complete).
-   The **Event Loop** is the "brain" of `asyncio`. It keeps track of all active tasks and waiting operations. When one task yields control, the event loop finds another task ready to run and transfers control to it.

**How does this increase performance for I/O-bound tasks?**

Performance gains in `asyncio` come not from parallel execution of computations on multiple CPU cores (as in `multiprocessing`), but from the **most efficient use of processor time**.
Instead of the processor idling while the program waits for a network response or file read, `asyncio` allows it to handle other tasks during that time. This means the program can juggle many I/O operations simultaneously, quickly switching between them as they become ready.

**Advantages:**

-   **High Responsiveness:** The application doesn't "freeze" during long I/O operations.
-   **Efficient Resource Usage:** Lower overhead compared to creating and managing many operating system threads for each I/O operation.
-   **Scalability:** Allows handling thousands of simultaneous connections (e.g., in web servers) with relatively few system resources.

**Important:** Asynchronous programming with `asyncio` on a single thread **will not speed up CPU-bound tasks** (tasks that heavily use the processor). If you have complex mathematical calculations, their parallel execution on multiple cores will require `multiprocessing`. `asyncio` shines specifically in tasks involving a lot of waiting.

**Key Concepts of `asyncio`:**

-   **Event Loop**: Manages the execution of asynchronous tasks.
-   **Coroutines**: Functions defined with `async def`, whose execution can be paused and resumed.

## Keywords: async and await

Starting with Python 3.5, special keywords were introduced for asynchronous programming:

-   `async def`: Used to define a coroutine (an asynchronous function).
    ```python-executable
    async def my_coroutine():
        # ... asynchronous code ...
        pass
    ```
-   `await`: Used inside a coroutine to wait for the result of another coroutine or an awaitable object (e.g., a `Future` or another coroutine). While `await` is waiting, the event loop can execute other tasks.
    ```python-executable
    async def main():
        result = await another_coroutine() # Pauses main until another_coroutine completes
        print(result)
    ```
    `await` can only be used inside a function defined with `async def`.

## Coroutines

A coroutine is the fundamental building block of `asyncio`. When you call a function defined with `async def`, it immediately returns a coroutine object, rather than executing the function body.

```python-executable
import asyncio

async def greet(name):
    print(f"Hello, {name}!")
    await asyncio.sleep(1) # Simulate I/O operation (non-blocking sleep)
    print(f"Bye, {name}!")

# Calling greet() returns a coroutine object but does not execute its code
coro_obj = greet("Alice")
print(type(coro_obj)) # <class 'coroutine'>

# To run the coroutine, an event loop is needed
# See example below using asyncio.run()
```

## Event Loop

The event loop is the dispatcher that manages all asynchronous tasks. It decides which coroutine should run next, and when to pause and resume them.

You rarely need to interact with the event loop directly if you use modern `asyncio` constructs.

## The asyncio.run() Function

`asyncio.run(coroutine)` is a high-level function (added in Python 3.7+) that simplifies running asynchronous code. It handles creating the event loop, running the passed coroutine in it, and managing its lifecycle.

```python-executable
import asyncio
import time

async def say_after(delay, what):
    await asyncio.sleep(delay) # Non-blocking sleep
    print(what)

async def main_program():
    start_time = time.time()
    print(f"Started execution at {time.strftime('%X')}")

    await say_after(1, "Hello")
    await say_after(2, "World")

    print(f"Finished in {time.time() - start_time:.2f} sec.")

if __name__ == "__main__":
    asyncio.run(main_program())
```

Note that `asyncio.sleep()` does not block the entire thread but allows the event loop to run other tasks if available. In this example, because `await` precedes each `say_after` call, they execute sequentially, but the `sleep` operation itself is asynchronous.

## Tasks (asyncio.Task)

For coroutines to run concurrently (i.e., be able to switch while one is waiting), they need to be wrapped in **Tasks** using `asyncio.create_task()` (Python 3.7+) or `asyncio.ensure_future()` (older way).

A Task is an object that manages the independent execution of a coroutine within the event loop.

```python-executable
import asyncio
import time

async def worker_task(name, delay):
    print(f"Task {name}: starting, will wait {delay} sec.")
    await asyncio.sleep(delay)
    print(f"Task {name}: finished.")
    return f"Result from {name}"

async def main_concurrent():
    start_time = time.time()
    print(f"Starting concurrent tasks at {time.strftime('%X')}")

    # Create tasks for concurrent execution
    task1 = asyncio.create_task(worker_task("A", 2))
    task2 = asyncio.create_task(worker_task("B", 1))
    task3 = asyncio.create_task(worker_task("C", 3))

    # Wait for all tasks to complete concurrently
    # (A more convenient way to wait for multiple tasks is asyncio.gather())
    results = await asyncio.gather(task1, task2, task3)

    print(f"\nAll tasks finished in {time.time() - start_time:.2f} sec.")
    print(f"Results: {results}")

if __name__ == "__main__":
    asyncio.run(main_concurrent())
```

In this example, `worker_task("A", 2)`, `worker_task("B", 1)`, and `worker_task("C", 3)` will start running concurrently. The total execution time will be close to the maximum delay (3 seconds), not the sum of all delays.

`asyncio.gather(*tasks)` allows running multiple tasks concurrently and waiting for all of them, returning a list of their results.

## Future Objects

A `Future` is a special low-level object that represents the eventual result of an asynchronous operation. Coroutines usually don't interact with `Future` objects directly, but `Task` is a subclass of `Future`.

-   A `Future` can be awaited (`await future_obj`).
-   A `Future` can have its result set (`future_obj.set_result()`) or an exception set (`future_obj.set_exception()`).

In most application-level asynchronous programming, you will work with coroutines and tasks, while `Future` objects are used by libraries under the hood.

## Simple Asynchronous Example: "Hello, World"

```python-executable
import asyncio

async def hello_world_async():
    print("Async Hello...")
    await asyncio.sleep(0.1) # Simulate a small async operation
    print("...World!")

if __name__ == "__main__":
    asyncio.run(hello_world_async())
```

## What's Next?

We have covered the very basics of `asyncio`: the `async`/`await` keywords, coroutines, the event loop, tasks, and the `asyncio.run()` function. This forms the foundation for writing efficient I/O-bound code.

In the next article, we will delve into more advanced `asyncio` features, such as asynchronous generators, context managers, working with streams, and synchronization primitives for asynchronous code.

---

**What is the core feature of asynchronous programming with `asyncio`?**
