To install and run OpenMP in C++ on Ubuntu, follow these steps:

---

### **1. Install GCC/G++ with OpenMP Support**
OpenMP is included with GCC and G++, so you only need to ensure they are installed:

```bash
sudo apt update
sudo apt install gcc g++
```

---

### **2. Verify GCC/G++ Installation**
Check the installed version of GCC/G++ to confirm the installation:

```bash
g++ --version
```

---

### **3. Write an OpenMP Program**
Create a simple OpenMP program, `hello_openmp.cpp`:

```cpp
#include <iostream>
#include <omp.h>

int main() {
    // Set the number of threads (optional)
    omp_set_num_threads(4);

    // Parallel region
    #pragma omp parallel
    {
        int thread_id = omp_get_thread_num();
        int total_threads = omp_get_num_threads();
        std::cout << "Hello from thread " << thread_id 
                  << " of " << total_threads << " threads." << std::endl;
    }

    return 0;
}
```

---

### **4. Compile the Program**
Use the `-fopenmp` flag when compiling the program:

```bash
g++ -fopenmp hello_openmp.cpp -o hello_openmp
```

---

### **5. Run the Program**
Execute the compiled binary:

```bash
./hello_openmp
```

---

### **6. Control the Number of Threads**
Set the `OMP_NUM_THREADS` environment variable to specify the number of threads:

```bash
export OMP_NUM_THREADS=8
./hello_openmp
```

---

### **Expected Output**
The program should output messages from multiple threads, for example:

```
Hello from thread 0 of 4 threads.
Hello from thread 1 of 4 threads.
Hello from thread 2 of 4 threads.
Hello from thread 3 of 4 threads.
```

Each thread prints its ID and the total number of threads.

Here's a guide and example code to demonstrate **loop-level parallelism** in C++ using OpenMP features like `reduction`, `private`, `firstprivate`, and `lastprivate`.

---

### **1. Reduction Example**

Reduction is used to aggregate values from multiple threads into a single variable.

```cpp
#include <iostream>
#include <omp.h>

int main() {
    int sum = 0;

    // Parallelize the loop with reduction
    #pragma omp parallel for reduction(+:sum)
    for (int i = 1; i <= 10; i++) {
        sum += i;  // Each thread calculates part of the sum
    }

    std::cout << "Sum of numbers from 1 to 10: " << sum << std::endl;
    return 0;
}
```

**Explanation**:
- `reduction(+:sum)` initializes `sum` to `0` for each thread and aggregates results using `+`.

---

### **2. Using `private`**

The `private` clause ensures each thread has its own copy of a variable.

```cpp
#include <iostream>
#include <omp.h>

int main() {
    int shared_var = 5;

    #pragma omp parallel for private(shared_var)
    for (int i = 0; i < 4; i++) {
        shared_var = i;  // Each thread gets its own copy of `shared_var`
        std::cout << "Thread " << omp_get_thread_num() << " shared_var: " << shared_var << std::endl;
    }

    return 0;
}
```

**Output**: 
Each thread will have its own copy of `shared_var`, unaffected by other threads.

---

### **3. Using `firstprivate`**

The `firstprivate` clause initializes each thread's private copy of a variable with the value of the variable from the main thread.

```cpp
#include <iostream>
#include <omp.h>

int main() {
    int init_value = 10;

    #pragma omp parallel for firstprivate(init_value)
    for (int i = 0; i < 4; i++) {
        std::cout << "Thread " << omp_get_thread_num() << " init_value: " << init_value << std::endl;
        init_value += i;  // Changes in `init_value` are local to the thread
    }

    return 0;
}
```

**Output**: 
Each thread starts with `init_value = 10`.

---

### **4. Using `lastprivate`**

The `lastprivate` clause ensures the value of a variable from the last iteration of the loop is preserved after the loop.

```cpp
#include <iostream>
#include <omp.h>

int main() {
    int last_var = 0;

    #pragma omp parallel for lastprivate(last_var)
    for (int i = 0; i < 4; i++) {
        last_var = i;  // Each thread updates `last_var` for its iteration
        std::cout << "Thread " << omp_get_thread_num() << " last_var: " << last_var << std::endl;
    }

    std::cout << "Value of last_var after loop: " << last_var << std::endl;
    return 0;
}
```

**Explanation**:
- The value of `last_var` from the last iteration (`i = 3`) is retained after the loop.

---

### **5. Combining All Clauses**
Here’s an example combining `reduction`, `private`, `firstprivate`, and `lastprivate`:

```cpp
#include <iostream>
#include <omp.h>

int main() {
    int sum = 0, shared_var = 5, init_value = 10, last_var = 0;

    #pragma omp parallel for reduction(+:sum) private(shared_var) firstprivate(init_value) lastprivate(last_var)
    for (int i = 1; i <= 5; i++) {
        sum += i;            // Aggregate sum using reduction
        shared_var = i * 2;  // Each thread gets its own copy of `shared_var`
        init_value += i;     // Start with `init_value` initialized from the main thread
        last_var = i;        // Update `last_var` with the iteration value
        std::cout << "Thread " << omp_get_thread_num()
                  << ": shared_var = " << shared_var
                  << ", init_value = " << init_value
                  << ", last_var = " << last_var << std::endl;
    }

    std::cout << "Sum: " << sum << std::endl;
    std::cout << "Final last_var: " << last_var << std::endl;

    return 0;
}
```

---

### Key Points:
- **`reduction`**: Aggregates a variable across threads.
- **`private`**: Each thread has its independent copy of the variable.
- **`firstprivate`**: Initializes each thread’s private variable with the value from the main thread.
- **`lastprivate`**: Preserves the value of the variable from the last iteration of the loop.

Compile and run these examples with the `-fopenmp` flag, as shown below:

```bash
g++ -fopenmp filename.cpp -o filename
./filename
```

These examples showcase how to control data sharing and dependencies in OpenMP effectively. Let me know if you need further clarification!
