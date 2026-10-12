# JVM and Memory Troubleshooting Questions

## The application suddenly throws OutOfMemoryError. How would you debug it?

First identify which memory area failed from the error message: heap, metaspace, direct buffer, or inability to create a native thread. Capture the heap dump or native memory evidence before restarting if the system allows it, and compare it with a healthy baseline.

For a heap problem, inspect the largest retained object graphs and look for caches without bounds, accumulated collections, unexpectedly large results, or objects kept alive by listeners or static references. Check GC logs and memory trends to distinguish a leak from a legitimate load increase. A larger heap can buy time, but it does not explain why retained memory keeps growing.

If the message is “unable to create native thread,” check thread count, process limits, and whether executors are growing without bound. Fix the source and add a load or soak check around the identified retention or concurrency path.
