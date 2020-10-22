---
layout: post
title:  "Profiling your C++ application: A DIY Trace Analysis on Chrome"
date:   2020-10-18 17:49:42 +0530
excerpt: "It just isn't convenient to calculate time difference each time I want to profile a block of code. I write a small profiler whose results can be visualized on Google Chrome."
---
As a beginner, this was pretty much how I "profiled" my C++ application in a scope:

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

This was 4x the statements to profile a block of code between the comments. This was intuitive, atleast to me. But as my compute regions grew, this naive method was getting outta hand.

Imagine doing this with a lot of scopes and functions. This naive implementation is a lot of work.

Let's set two agendas here:
1. Get rid of these multiple statements to be written each time. Ideally, I should be able to tell the compiler, which scopes to profile, simple as that.
2. Visualize. I mean, who hates graphical views? Plus, this tool would come in real-handy when you have *concurrent* executions to profile.

### 1. Quick-n-Easy Scope-based Profiler

One way to automate entry-exit of timing a function/scope would be, exploit the concept of scopes. If it's not obvious yet, we'll use a *class*.

Why? Constructors and Destructors, that's why.

To use our Quick-n-Easy Scope-based Profiler, we would:
1. Create a Timer object in the scope you want to profile. That's it.

Let's write a Timer class, shall we?

```cpp
// profiler.h
#pragma once
#include <chrono>
#include <iostream>

class Timer {
private:
    /* This will store the module/scope name */
    const char* func_name;
    /* A time_point datatype to store start time */
    std::chrono::time_point<std::chrono::high_resolution_clock> m_startTimePoint;

public:
    /* Constructor: Measures the time_point when the profiling begins.
    This happens when a new profiler object is instantiated. In OS terminology,
    this is also called RAII: Resource Acquisition Is Initialization
    */
    Timer(const char* name)
        : func_name(name)
    {
        m_startTimePoint = std::chrono::high_resolution_clock::now();
    }

    /* Destructor: Immediately called at the end-of-scope of the profiler object.
    The Stop() method prints the profiling results to console only (for now) */
    ~Timer() {
        Stop();
    }

    void Stop() {
        /* Measure the end time_point */
        auto m_endTimePoint = std::chrono::high_resolution_clock::now();

        /* Cast the gonzo numbers to microseconds */
        auto start = std::chrono::time_point_cast<std::chrono::microseconds>(m_startTimePoint).time_since_epoch().count();
        auto end = std::chrono::time_point_cast<std::chrono::microseconds>(m_endTimePoint).time_since_epoch().count();

        std::cout << func_name << ": " << (end - start) * 0.001 << "ms\n";
    }
};
```

Let's see a few ways how this Timer is useful. We'll write a small workload, matrix multiplication.


Here is an example of how our profiler *should* work, take this simple Matrix Multiplication profiling:

```cpp
// main.cpp
#include <vector>
#include <iostream>
#include "profiler.h"

template <typename T>
void MatrixMulCPU(size_t M, size_t N, size_t P,
    const std::vector<T>& a_host,
    const std::vector<T>& b_host,
    std::vector<T>& c_host) {

    // Constructor gets called
    Timer t("MatMul CPU fn body");

    for (size_t i = 0; i < M; i++) {
        for (size_t k = 0; k < N; k++)
            for (size_t j = 0; j < P; j++)
                c_host[i * M + j] += a_host[i * M + k] * b_host[k * N + j];
    }
    // Destructor gets called (end of scope)
}

int main() {
    Timer t("Main");

    size_t M = 64;
    size_t N = 64;
    size_t P = 64;

    Timer *t1 = new Timer("Init vectors");
    // Initialize all to 0.
    std::vector<float> a_host(M * N, 0);
    std::vector<float> b_host(N * P, 0);
    std::vector<float> c_host(M * P, 0);
    delete t1;

    MatrixMulCPU<float>(M, N, P, a_host, b_host, c_host);
    MatrixMulCPU<float>(M, N, P, a_host, b_host, c_host);
    return 0;
}
```
 Output of the Profiler logged for MatrixMulCPU
```bash
Init vectors: 0.112ms
MatMul CPU fn body: 1.827ms
MatMul CPU fn body: 1.642ms
Main: 4.422ms
```

Now we're talking. You can profile multiple times, without having to write those extra lines of start-stop.

But we still have some opens:
1. Every time you create a Timer, you need to supply the "name" of the operation/function. This consumes time.
2. You cannot "visualize" the flow here. The `Main: 4.22ms` includes runtimes of `MatMul` twice. How can you visualize the portion of time consumed within main? Sure, you can eyeball it. But in complex examples and projects you'll encounter, this gets very difficult very quickly.
