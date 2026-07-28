# Latency Monitor & Logger (latency-monitor-logger)

A high-performance, low-overhead latency tracking and logging tool designed for High-Frequency Trading (HFT) systems and ultra-low-latency applications. It measures execution times and round-trip times (RTT) down to the nanosecond and CPU cycle level.

---

## 🚀 Why This Project?
In HFT and low-latency systems, standard benchmarking tools introduce too much overhead. `latency-monitor-logger` acts as the ultimate "stopwatch" to benchmark your core components (such as order books, memory pools, and feed handlers) with maximum precision and zero performance penalty.

---

## 🛠️ How It Works
1. **High-Precision Timestamping:** Captures the starting point using `std::chrono::high_resolution_clock` or hardware-level CPU cycle counters (`__rdtsc()`).
2. **Execution:** Executes the target block or transaction.
3. **Delta Calculation & Logging:** Computes the exact difference in nanoseconds or CPU cycles and writes the metrics efficiently to a file or standard output.

---

## 📦 Core Features
* **Ultra-Low Overhead:** Minimal impact on measured code performance.
* **CPU Cycle Precision:** Optional hardware-level measurement via `__rdtsc()`.
* **Flexible Output:** Fast logging to files or console for post-processing analysis.
* **Component Testing:** Perfectly integrates with other high-performance C++ trading infrastructure components.

---

## 💻 Code Structure & Usage Example

```cpp
#include <iostream>
#include <chrono>
#include <x86intrin.h> // For __rdtsc()

class LatencyTracker {
public:
    inline uint64_t start() {
        // Option A: Hardware CPU cycles
        return __rdtsc();
        
        // Option B: High resolution steady clock
        // return std::chrono::duration_cast<std::chrono::nanoseconds>(
        //            std::chrono::high_resolution_clock::now().time_since_epoch()
        //        ).count();
    }

    inline void stop(uint64_t start_time, const std::string& label) {
        uint64_t end_time = __rdtsc();
        uint64_t cycles = end_time - start_time;
        std::cout << "[Latency Log] " << label << " took " << cycles << " CPU cycles." << std::endl;
    }
};

int main() {
    LatencyTracker tracker;
    
    uint64_t t0 = tracker.start();
    
    // --- Measured Code Block (e.g., Order Matching / Memory Allocation) ---
    for(volatile int i = 0; i < 1000; ++i); 
    
    tracker.stop(t0, "Core Matching Engine Latency");
    return 0;
}

🔧 Building and Running
g++ -O3 -std=c++20 main.cpp -o latency_logger
./latency_logger

📈 License
This project is open-source and available under the MIT License.