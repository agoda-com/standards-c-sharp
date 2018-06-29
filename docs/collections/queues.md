## Usage of queues

When you need certain queueing behavior:

- First In First Out (FIFO): `Queue<T>` or `ConcurrentQueue<T>` for thread-safe access
- Last In First Out (LIFO): `Stack<T>` or `ConcurrentStack<T>` for thread-safe access