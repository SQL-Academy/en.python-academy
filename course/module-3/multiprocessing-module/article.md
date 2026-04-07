# Multiprocessing in Python: The multiprocessing Module

In the previous article, we explored multithreading with the `threading` module and found that due to the Global Interpreter Lock (GIL), it doesn't provide true parallelism for CPU-bound tasks in CPython. To truly utilize multiple processor cores for computationally intensive operations, Python offers the `multiprocessing` module.

The `multiprocessing` module allows creating and managing processes similarly to how the `threading` module manages threads. The key difference is that each process has its own Python interpreter and memory space, allowing them to run in parallel on different CPU cores, bypassing the GIL limitations.

## Introduction to the multiprocessing Module

The API of the `multiprocessing` module is largely similar to the `threading` API, making the transition between them relatively straightforward.

**Key advantages of `multiprocessing`:**

-   Achieves true parallelism for CPU-bound tasks.
-   Each process runs in its isolated memory space, reducing the risk of data conflicts compared to threads.

**Key disadvantages:**

-   Creating processes is more resource-intensive than creating threads.
-   Data exchange between processes is more complex and slower than between threads (requires serialization/deserialization and special Inter-Process Communication (IPC) mechanisms).

## Creating and Managing Processes

The `multiprocessing.Process` class is used to create new processes.

```python-executable
import multiprocessing
import os
import time

def worker_process(name):
    """Function executed in a separate process."""
    print(f"Process {name} (PID: {os.getpid()}): starting work.")
    # time.sleep(2) # Simulate delay (can be uncommented for clarity)
    print(f"Process {name} (PID: {os.getpid()}): finishing work.")

if __name__ == "__main__":  # Important for multiprocessing on some platforms
    print(f"Main process (PID: {os.getpid()})")

    # Create processes
    process1 = multiprocessing.Process(target=worker_process, args=("Worker-A",))
    process2 = multiprocessing.Process(target=worker_process, args=("Worker-B",))

    # Start processes
    process1.start()
    process2.start()

    print("All child processes started.")

    # Wait for processes to complete
    process1.join()
    process2.join()

    print("All child processes have finished their work.")
```

-   **`if __name__ == "__main__":`**: This check is crucial when using `multiprocessing` on platforms where child processes inherit (or import) the parent module (e.g., Windows). Without it, the top-level code of the module would execute in each child process, potentially leading to recursive process creation and errors.
-   `os.getpid()`: Returns the identifier of the current process.

## Exchanging Data Between Processes

Since processes have isolated memory, special mechanisms are required for data exchange.

### 1. Pipe

`multiprocessing.Pipe()` creates a pair of `Connection` objects representing the two ends of a pipe. By default, the pipe is duplex (two-way). Each `Connection` object has `send()` and `recv()` methods.

```python-executable
import multiprocessing
import time

def sender(conn):
    print("Sender: sending data.")
    conn.send("Hello from sender") # Single message
    conn.close()
    print("Sender: data sent and pipe closed.")

def receiver(conn):
    print("Receiver: waiting for data...")
    msg = conn.recv()
    print(f'Receiver: received "{msg}'")
    conn.close()
    print("Receiver: pipe closed.")

if __name__ == "__main__":
    parent_conn, child_conn = multiprocessing.Pipe()

    p_sender = multiprocessing.Process(target=sender, args=(parent_conn,))
    p_receiver = multiprocessing.Process(target=receiver, args=(child_conn,))

    p_sender.start()
    p_receiver.start()

    p_sender.join()
    p_receiver.join()
    print("Exchange via Pipe completed.")
```

`Pipe` is suitable for simple two-way communication between two processes.

### 2. Queue

`multiprocessing.Queue` is very similar to `queue.Queue` from the `queue` module but is designed for processes. It is thread- and process-safe.

```python-executable
import multiprocessing
import time
import random

def producer_proc(q):
    for i in range(3):
        item = f"Item-{i}"
        time.sleep(random.uniform(0.1, 0.2)) # Small random delay
        q.put(item)
        print(f"Producer: added {item} to queue.")
    q.put(None) # Sentinel value for the consumer

def consumer_proc(name, q):
    while True:
        item = q.get()
        if item is None:
            print(f"{name}: received None signal, exiting.")
            break
        print(f"{name}: processed {item}")
        time.sleep(random.uniform(0.1, 0.3))

if __name__ == "__main__":
    q = multiprocessing.Queue()

    p_prod = multiprocessing.Process(target=producer_proc, args=(q,))
    p_cons1 = multiprocessing.Process(target=consumer_proc, args=("Consumer-A", q))

    p_prod.start()
    p_cons1.start()

    p_prod.join()
    p_cons1.join()
    print("Exchange via Queue completed.")
```

### 3. Shared Memory (Value and Array)

`Value` and `Array` allow sharing simple data types (numbers, strings, arrays) between processes. Access must be synchronized using locks (`multiprocessing.Lock`) if multiple processes might modify them.

```python-executable
import multiprocessing

def worker_value(num, lock):
    for _ in range(500):
        with lock:
            num.value += 1

def worker_array(arr, index, lock):
    with lock:
        arr[index] -= index * 0.5 # Example operation on an array element

if __name__ == "__main__":
    lock = multiprocessing.Lock()
    shared_num = multiprocessing.Value('i', 0) # 'i' - integer type
    shared_array = multiprocessing.Array('d', [10.0, 20.0, 30.0]) # 'd' - double type

    processes = []
    # Processes for Value
    for _ in range(2):
        p = multiprocessing.Process(target=worker_value, args=(shared_num, lock))
        processes.append(p)
        p.start()

    # Processes for Array
    for i in range(len(shared_array)):
        p = multiprocessing.Process(target=worker_array, args=(shared_array, i, lock))
        processes.append(p)
        p.start()

    for p in processes:
        p.join()

    print(f"Shared number: {shared_num.value}") # Expected: 1000
    print(f"Shared array: {list(shared_array)}")
```

Data types for `Value` and `Array` are specified using type codes, similar to the `array` module.

### 4. Managers

Managers provide a way to create shared objects that can be more complex than simple `Value` or `Array`. A manager starts a server process that manages these objects, and other processes receive proxy objects to access them.

Lists, dictionaries, namespaces, locks, queues, etc., are supported.

```python-executable
import multiprocessing

def worker_manager_dict(shared_dict, key, value):
    shared_dict[key] = value
    print(f"Process {key}: set {key}={value}")

def worker_manager_list(shared_list, value):
    shared_list.append(value)
    print(f"Process added {value} to list")

if __name__ == "__main__":
    with multiprocessing.Manager() as manager:
        shared_dict = manager.dict()
        shared_list = manager.list()

        processes = []
        # Demonstration for dictionary
        for i in range(2):
            p = multiprocessing.Process(target=worker_manager_dict, args=(shared_dict, f"key{i}", i*10))
            processes.append(p)
            p.start()

        # Demonstration for list
        for i in range(2):
            p = multiprocessing.Process(target=worker_manager_list, args=(shared_list, f"item_{i}"))
            processes.append(p)
            p.start()

        for p in processes:
            p.join()

        print(f"Shared dictionary: {dict(shared_dict)}")
        print(f"Shared list: {list(shared_list)}")
```

Managers are more flexible but also slower compared to `Value`/`Array` due to IPC overhead.

## Process Pools (Pool)

The `multiprocessing.Pool` class provides a convenient way to distribute tasks among multiple worker processes.

-   `pool.map(func, iterable)`: Applies `func` to each element of `iterable` and returns a list of results. Blocks until all tasks are complete.
-   `pool.apply_async(func, args)`: Executes `func(*args)` asynchronously. Returns an `AsyncResult` object, from which `get()` can be called to retrieve the result (blocking call).
-   `pool.close()`: Prevents any more tasks from being submitted to the pool.
-   `pool.join()`: Waits for the worker processes to exit.

```python-executable
import multiprocessing
import time

def square(x):
    time.sleep(0.1) # Simulate computation
    return x * x

if __name__ == "__main__":
    # Use context manager for automatic close() and join()
    with multiprocessing.Pool(processes=2) as pool:
        numbers = list(range(5))

        # Example with map
        print("Using pool.map():")
        results_map = pool.map(square, numbers)
        print(f"Results (map): {results_map}")

        # Example with apply_async
        print("\nUsing pool.apply_async():")
        async_results = [pool.apply_async(square, (num,)) for num in numbers]
        results_apply_async = [res.get(timeout=1) for res in async_results]
        print(f"Results (apply_async): {results_apply_async}")

    print("Work with pool completed.")
```

## Comparison: threading vs multiprocessing

| Aspect              | `threading`                           | `multiprocessing`                                 |
| ------------------- | ------------------------------------- | ------------------------------------------------- |
| **CPU Parallelism** | Limited by GIL (no for CPU-bound)     | True parallelism (bypasses GIL)                   |
| **I/O-bound tasks** | Efficient (GIL released)              | Also efficient, but higher overhead               |
| **Memory**          | Shared memory                         | Isolated memory per process                       |
| **Data Exchange**   | Simple (via shared vars + sync)       | More complex (Pipe, Queue, Value, Array, Manager) |
| **Overhead**        | Low (thread creation cheaper)         | High (process creation costlier)                  |
| **Fault Tolerance** | Thread crash can crash entire process | Process crash usually doesn't affect others       |

**When to Choose:**

-   **`threading`**: For I/O-bound tasks where low overhead and simple data exchange are important, and the GIL is not a bottleneck.
-   **`multiprocessing`**: For CPU-bound tasks requiring intensive computations that can be parallelized across multiple cores.

## What's Next?

We have learned how the `multiprocessing` module enables true parallelism in Python by bypassing the GIL limitations. In the next article, we will move to a completely different approach to concurrency — asynchronous programming using `asyncio`.

---

**What is the main advantage of the `multiprocessing` module over `threading` for CPU-bound tasks?**
