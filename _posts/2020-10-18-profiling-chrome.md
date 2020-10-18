---
layout: post
title:  "Profiling your C++ application: A DIY Trace Analysis on Chrome"
date:   2020-10-18 17:49:42 +0530
excerpt: "It just isn't convenient to calculate time difference each time I want to profile a block of code. I write a small profiler whose results can be visualized on Google Chrome."
---

In Python, this is what you'd *typically* do, to trace runtime of a block of code:

```python
# I wish Astrophysicists could use this to solve space-time continuum
import time

tstart = time.time()
# ...computation...
tstop = time.time()

print("Time elapsed: ", tstop - tstart)
```

This was to gain familiarity with ease of code. C++ follows on similar footsteps:

```cpp
#include <chrono>
#include <iostream>

int main(){

    auto startTimePoint = std::chrono::high_resolution_clock::now();
    /* ...computation... */
    auto endTimePoint = std::chrono::high_resolution_clock::now();

    /* convert gonzo numbers to microseconds */
    auto start = std::chrono::time_point_cast<std::chrono::microseconds>(startTimePoint).time_since_epoch().count();
    auto end = std::chrono::time_point_cast<std::chrono::microseconds>(endTimePoint).time_since_epoch().count();
    std::cout << "Time elapsed: " << (end - start) * 0.001 << "ms\n";
}
```

This is 4x the statements to profile a block of code between the comments. Imagine doing this with a lot of scopes and functions. This naive implementation is a lot of work.

This doesn't even allow you to **visualize** the time slice taken up by each block/function you profile. Pictures speak way better [insert image], by all means.  Can we do better? Yes.

Let's set two agendas here:
1. Get rid of these multiple statements to be written each time. Ideally, I should be able to tell the compiler, which scopes to profile, simple as that.
2. Visualize. Colors and bars are pretty.

### 1. Managing scope of blocks

One way to automate entry-exit of timing a function/scope would be, to really, exploit the concept of scopes.

As obvious it may sound, we'll use a class, a profiler class. We intend to use it in three easy steps:

1. Define your function/scope.
2. Instantiate a profiler object: Within the `{curly-brace-scope}` or within function scope.
3. The profiler object will time the computation, log the entry-exit time, destroy the object upon scope-exit.

For example, take this simple Matrix Multiplication profiling:

```cpp
/* ... */
template <typename T>
void MatrixMulCPU(size_t M, size_t N, size_t P,
				const std::vector<T> &a_host,
				const std::vector<T> &b_host,
				std::vector<T> &c_host) {

    /*
    This profiler call will log the runtime till the
    implicitly created timer is destroyed (as a consequence of end
    of function scope)
    */
    std::cout << "Starting CPU Profiling and computation...\n";
    PROFILE_FUNCTION();

    for (size_t i = 0; i < M; i++)
        for (size_t k = 0; k < N; k++)
            for (size_t j = 0; j < P; j++)
                c_host[i * M + j] += a_host[i * M + k] * b_host[k * N + j];

    std::cout << "End of CPU computation and auto-end of profiler scope.\n";
}

/* call MatrixMulCPU from anywhere */
```
 Output of the Profiler logged for MatrixMulCPU
```bash
Starting CPU Profiling and computation...
End of CPU computation and auto-end of profiler scope.
void __cdecl MatrixMulCPU(size_t, size_t, size_t, const std::vector<T> &
const std::vector<T> &, std::vector<T> &) [T = int]: 45671.3ms
```

Couple of good things about our upcoming Profiler:
1. It manages start and stop time automatically. Constructors and Destructors take charge.
2. It captures the function call type. You can end up having multiple overloads for a function. The profiler shows you 'which' variant of the function was called, in the signature, along with the return type.
3. It will add an entry to a logged JSON file, which will (eventually) help us visualize using chrome://tracing

#### Does this excite you?
Let's write a Timer class. We'll start easy.

```cpp
#include <chrono>
#include <iostream>

class Timer {
private:
    /* This will store the module/scope name */
    const char* func_name;
    /* A time_point datatype to store start time */
    std::chrono::time_point<std::chrono::high_resolution_clock> startTimePoint;

public:
    /* Constructor: Measures the time_point when the profiling begins.
    This happens when a new profiler object is instantiated. In OS terminology,
    this is also called RAII: Resource Acquisition Is Initialization
    */
    Timer(const char* name)
        : func_name(name)
    {
        startTimePoint = std::chrono::high_resolution_clock::now();
    }

    /* Destructor: Immediately called at the end-of-scope of the profiler object.
    The Stop() method prints the profiling results to console only (for now) */
    ~Timer() {
        Stop();
    }

    void Stop() {
        /* Measure the end time_point */
        auto endTimePoint = std::chrono::high_resolution_clock::now();

        /* Cast the gonzo numbers to microseconds */
        auto start = std::chrono::time_point_cast<std::chrono::microseconds>(startTimePoint).time_since_epoch().count();
        auto end = std::chrono::time_point_cast<std::chrono::microseconds>(endTimePoint).time_since_epoch().count();

        std::cout << func_name << ": " << (end - start) * 0.001 << "ms\n";
    }
};
```

This is a boilerplate. Let's see few examples of how this class can be used, and what problems does it solve so far.