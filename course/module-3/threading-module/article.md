# Multithreading in Python: The threading Module

In the previous article, we discussed the general concepts of concurrency, parallelism, and asynchronicity. Now, let's delve into one of the classic ways to achieve concurrency in Python — **multithreading**, using the built-in `threading` module.

Multithreading allows your program to perform multiple tasks (threads) seemingly simultaneously within a single process. This is particularly useful for I/O-bound tasks, where the program often waits for external operations to complete.

## Introduction to the threading Module

The `threading` module provides a high-level interface for working with threads. Each thread represents a separate sequence of instructions that can execute independently of other threads in the same process.

It's important to remember that all threads within a single process share the same memory address space. This facilitates data exchange but also requires synchronization mechanisms to prevent conflicts.

## Creating and Starting Threads

The primary way to create a thread is to instantiate the `threading.Thread` class, passing it the target function (which the thread will execute) and its arguments.

```python-executable
import threading
import time

def worker(number, sleep_time):
    """Function that our thread will execute."""
    print(f"Thread {number}: starting work, will sleep for {sleep_time} sec.")
    time.sleep(sleep_time)
    print(f"Thread {number}: finishing work.")

# Create threads
thread1 = threading.Thread(target=worker, args=(1, 2)) # args are passed as a tuple
thread2 = threading.Thread(target=worker, args=(2, 1))

# Start threads
thread1.start()
thread2.start()

print("All threads started from the main thread.")

# Wait for threads to complete (optional, but often necessary)
thread1.join() # Main thread will wait until thread1 finishes
thread2.join() # Main thread will wait until thread2 finishes

print("All threads have finished their work.")
```

-   `target`: The function to be executed in the new thread.
-   `args`: A tuple of arguments for the target function.
-   `start()`: Starts the thread's execution.
-   `join()`: Blocks the calling thread until the thread whose `join()` method is called completes.

If you run this code, you'll see messages from the threads interleave, demonstrating their concurrent execution.

## The Global Interpreter Lock (GIL) and Its Impact

Before diving deeper into synchronization, it's crucial to mention the **Global Interpreter Lock (GIL)**. In CPython (the standard Python implementation), the GIL is a mutex that protects access to Python objects, preventing multiple threads within a single process from executing Python bytecode simultaneously.

**What this means in practice:**

-   **For CPU-bound tasks** (intensive computations), multithreading in Python within a single process will not yield performance gains on multi-core systems because only one thread can hold the GIL and execute Python code at any given time.
-   **For I/O-bound tasks** (waiting for network, disk), the GIL is released during blocking I/O operations. This allows other Python threads to run while one thread waits. Therefore, `threading` is effective for I/O-bound tasks.

Because of the GIL, if you need true parallelism for CPU-bound tasks, you should use the `multiprocessing` module (covered in the next article).

## Thread Synchronization

When multiple threads work with shared data, risks of race conditions and other issues arise. The `threading` module provides several synchronization primitives.

### 1. Locks (Lock and RLock)

The `Lock` object (mutex) is the simplest synchronization primitive. It has two states: "locked" and "unlocked". A thread can call the `acquire()` method to lock it. If the lock is already held by another thread, the calling thread will block until it is released using the `release()` method.

```python-executable
import threading
import time

shared_resource = 0
lock = threading.Lock()

def increment_shared_resource():
    global shared_resource
    for _ in range(100000):
        lock.acquire() # Acquire the lock
        try:
            shared_resource += 1
        finally:
            lock.release() # Release the lock in any case

threads = []
for i in range(5):
    thread = threading.Thread(target=increment_shared_resource)
    threads.append(thread)
    thread.start()

for thread in threads:
    thread.join()

print(f"Final value of shared_resource: {shared_resource}") # Expected: 500000
```

Using `try...finally` with `acquire()` and `release()` ensures the lock is released even if an exception occurs in the protected code block.

A `RLock` (re-entrant lock) allows the same thread to acquire the lock multiple times. `release()` must be called the same number of times `acquire()` was called. This is useful in situations involving recursive use of a protected resource by the same thread.

### 2. Events (Event)

An `Event` object is a simple mechanism for signaling between threads. One thread can wait for an event flag to be set, while another thread can set the flag.

-   `event.wait()`: Blocks the thread until the internal event flag becomes true.
-   `event.set()`: Sets the internal flag to `True`.
-   `event.clear()`: Resets the internal flag to `False`.
-   `event.is_set()`: Returns the current state of the flag.

```python-executable
import threading
import time

event = threading.Event()

def waiter():
    print("Waiter thread: waiting for event...")
    event.wait() # Blocks until the event is set
    print("Waiter thread: event received, continuing work!")

def setter():
    print("Setter thread: waiting a bit...")
    time.sleep(2)
    print("Setter thread: setting the event!")
    event.set()

t1 = threading.Thread(target=waiter)
t2 = threading.Thread(target=setter)

t1.start()
t2.start()

t1.join()
t2.join()
```

### 3. Semaphores (Semaphore and BoundedSemaphore)

A semaphore manages an internal counter which is decremented by each `acquire()` call and incremented by `release()`. If the counter is zero, `acquire()` blocks. This allows limiting the number of threads that can simultaneously access a resource.

A `BoundedSemaphore` additionally checks that the number of `release()` calls does not exceed the number of `acquire()` calls, preventing errors.

```python-executable
import threading
import time
import random

MAX_CONNECTIONS = 3
semaphore = threading.BoundedSemaphore(MAX_CONNECTIONS)

def use_resource(thread_id):
    print(f"Thread {thread_id}: trying to acquire resource...")
    with semaphore: # Context manager automatically calls acquire/release
        print(f"Thread {thread_id}: resource acquired.")
        sleep_time = random.randint(1, 3)
        time.sleep(sleep_time)
        print(f"Thread {thread_id}: releasing resource after {sleep_time} sec.")

threads = []
for i in range(10):
    thread = threading.Thread(target=use_resource, args=(i,))
    threads.append(thread)
    thread.start()

for thread in threads:
    thread.join()
```

You will observe that no more than 3 threads use the resource concurrently.

### 4. Condition Variables (Condition)

A `Condition` object is used for more complex synchronization where threads need to wait for a specific condition to be met. A `Condition` is usually associated with a `Lock` or `RLock`.

-   `condition.acquire()` / `condition.release()`: Acquire/release the associated lock.
-   `condition.wait()`: Releases the associated lock and waits until another thread calls `notify()` or `notify_all()`. Then, it re-acquires the lock.
-   `condition.notify(n=1)`: Wakes up one (or `n`) thread(s) waiting on this condition.
-   `condition.notify_all()`: Wakes up all threads waiting on this condition.

```python-executable
import threading
import time
import random

condition = threading.Condition()
items = []

def producer():
    for i in range(5):
        time.sleep(random.uniform(0.1, 0.5))
        with condition: # Acquire the lock
            item = f"Item-{i}"
            items.append(item)
            print(f"Producer: added {item}, total {len(items)} items. Notifying.")
            condition.notify() # Notify one consumer

def consumer(name):
    while True:
        with condition:
            print(f"{name}: waiting for an item...")
            condition.wait() # Wait until the producer adds something and notifies
            if not items:
                print(f"{name}: list is empty, but I was woken up. Continuing to wait.")
                continue # Could be a spurious wakeup or another consumer got the item
            item = items.pop(0)
            print(f"{name}: consumed {item}, {len(items)} items left.")
            # Example exit condition: Stop if list is empty and only main + consumer threads are left
            if not items and threading.active_count() <= 3:
                 print(f"{name}: Exiting as list is empty and few threads remain.")
                 break
        time.sleep(random.uniform(0.2, 0.6)) # Simulate processing

# Start one producer and two consumers
producer_thread = threading.Thread(target=producer)
consumer_a_thread = threading.Thread(target=consumer, args=("Consumer-A",))
consumer_b_thread = threading.Thread(target=consumer, args=("Consumer-B",))

producer_thread.start()
consumer_a_thread.start()
consumer_b_thread.start()

# Wait for producer to finish
producer_thread.join()

# Wait for consumers to finish (they will exit based on the condition inside)
# We need a mechanism for consumers to exit gracefully.
# For simplicity, we might rely on the exit condition check inside, or add a sentinel value.
# To ensure they eventually exit if the producer finishes and the list becomes empty:
with condition:
    print("Main: Notifying all consumers to check exit condition after producer finished.")
    condition.notify_all() # Wake up consumers so they can check the exit condition

consumer_a_thread.join()
consumer_b_thread.join()

print("Main: All threads finished.")

```

This example demonstrates the classic producer-consumer problem.

## Queues for Inter-Thread Communication (queue.Queue)

The `queue` module provides thread-safe queue classes (`Queue`, `LifoQueue`, `PriorityQueue`), which are the preferred way to exchange data between threads. They encapsulate all the necessary locking logic.

-   `q.put(item)`: Adds an item to the queue. Blocks if the queue is full (for bounded queues).
-   `q.get()`: Removes and returns an item from the queue. Blocks if the queue is empty.
-   `q.task_done()`: Signals that a formerly enqueued task is complete (used with `q.join()`).
-   `q.join()`: Blocks until all items in the queue have been gotten and processed (i.e., `task_done()` called for each item).

```python-executable
import threading
import queue
import time
import random

q = queue.Queue()

def producer_q():
    for i in range(10):
        item = f"Item-{i}"
        time.sleep(random.uniform(0.1, 0.3))
        q.put(item)
        print(f"Producer: added {item} to queue (size: {q.qsize()})")
    # Add sentinel values (None) to signal consumers to stop
    for _ in range(2): # Assuming 2 consumers
        q.put(None)

def consumer_q(name):
    while True:
        item = q.get() # Blocks if queue is empty
        if item is None: # Check for sentinel value
            print(f"{name}: received None, exiting.")
            q.task_done() # Important to call task_done even for None
            break
        print(f"{name}: processing {item}")
        time.sleep(random.uniform(0.2, 0.5)) # Simulate work
        q.task_done() # Signal that processing for this item is done
        print(f"{name}: finished {item}")

# Start one producer and two consumers
producer_th = threading.Thread(target=producer_q)
consumer_th_a = threading.Thread(target=consumer_q, args=("Consumer-A",))
consumer_th_b = threading.Thread(target=consumer_q, args=("Consumer-B",))

producer_th.start()
consumer_th_a.start()
consumer_th_b.start()

producer_th.join() # Wait for producer to finish putting items
q.join() # Wait for all items to be processed by consumers

print("Main: All queue tasks completed.")
```

Using `queue.Queue` often leads to cleaner and less error-prone code compared to manual lock management for producer-consumer scenarios.

## Conclusion

The `threading` module provides essential tools for building concurrent applications in Python, especially for I/O-bound tasks. Understanding thread creation, the impact of the GIL, and various synchronization primitives (`Lock`, `Event`, `Semaphore`, `Condition`) and communication mechanisms (`queue.Queue`) is key to writing effective multithreaded code.

In the next article, we will explore the `multiprocessing` module, which allows bypassing the GIL for true CPU parallelism.

---

**What is the primary limitation of the `threading` module in CPython for CPU-bound tasks?**
