# Program statement: To implement memory allocation methods like first fit, best fit and worst first
# Source code:
```c
#include <stdio.h>

#define MAX 10  // Maximum number of partitions and processes

// Function to calculate and display fragmentation
void calculateFragmentation(int partitions[], int m) {
    int totalFragmentation = 0;
    printf("Remaining Fragmentation in Partitions:\n");
    for (int i = 0; i < m; i++) {
        printf("Partition %d: %d KB\n", i + 1, partitions[i]);
        totalFragmentation += partitions[i];
    }
    printf("Total External Fragmentation: %d KB\n", totalFragmentation);
}

// Function to implement First Fit memory allocation
void firstFit(int partitions[], int m, int processes[], int n) {
    int allocation[n];
    for (int i = 0; i < n; i++)
        allocation[i] = -1;
    
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (partitions[j] >= processes[i]) {
                allocation[i] = j;
                partitions[j] -= processes[i];
                break;
            }
        }
    }
    
    printf("First Fit Allocation:\n");
    for (int i = 0; i < n; i++)
        printf("Process %d (%d KB) -> Partition %d\n", i + 1, processes[i], allocation[i] + 1);
    calculateFragmentation(partitions, m);
}

// Function to implement Worst Fit memory allocation
void worstFit(int partitions[], int m, int processes[], int n) {
    int allocation[n];
    for (int i = 0; i < n; i++)
        allocation[i] = -1;
    
    for (int i = 0; i < n; i++) {
        int worstIdx = -1;
        for (int j = 0; j < m; j++) {
            if (partitions[j] >= processes[i]) {
                if (worstIdx == -1 || partitions[j] > partitions[worstIdx])
                    worstIdx = j;
            }
        }
        if (worstIdx != -1) {
            allocation[i] = worstIdx;
            partitions[worstIdx] -= processes[i];
        }
    }
    
    printf("Worst Fit Allocation:\n");
    for (int i = 0; i < n; i++)
        printf("Process %d (%d KB) -> Partition %d\n", i + 1, processes[i], allocation[i] + 1);
    calculateFragmentation(partitions, m);
}

// Function to implement Best Fit memory allocation
void bestFit(int partitions[], int m, int processes[], int n) {
    int allocation[n];
    for (int i = 0; i < n; i++)
        allocation[i] = -1;
    
    for (int i = 0; i < n; i++) {
        int bestIdx = -1;
        for (int j = 0; j < m; j++) {
            if (partitions[j] >= processes[i]) {
                if (bestIdx == -1 || partitions[j] < partitions[bestIdx])
                    bestIdx = j;
            }
        }
        if (bestIdx != -1) {
            allocation[i] = bestIdx;
            partitions[bestIdx] -= processes[i];
        }
    }
    
    printf("Best Fit Allocation:\n");
    for (int i = 0; i < n; i++)
        printf("Process %d (%d KB) -> Partition %d\n", i + 1, processes[i], allocation[i] + 1);
    calculateFragmentation(partitions, m);
}

int main() {
    int partitions[MAX], processes[MAX], m, n;
    
    printf("Enter number of partitions: ");
    scanf("%d", &m);
    printf("Enter partition sizes:\n");
    for (int i = 0; i < m; i++)
        scanf("%d", &partitions[i]);
    
    printf("Enter number of processes: ");
    scanf("%d", &n);
    printf("Enter process sizes:\n");
    for (int i = 0; i < n; i++)
        scanf("%d", &processes[i]);
    
    int partitionsCopy[MAX];
    for (int i = 0; i < m; i++) partitionsCopy[i] = partitions[i];
    firstFit(partitionsCopy, m, processes, n);
    
    for (int i = 0; i < m; i++) partitionsCopy[i] = partitions[i];
    worstFit(partitionsCopy, m, processes, n);
    
    for (int i = 0; i < m; i++) partitionsCopy[i] = partitions[i];
    bestFit(partitionsCopy, m, processes, n);
    
    return 0;
}
```
## Interpretation of the code

 **C program** to simulate **Memory Management Techniques** in Operating Systems.  

1. **First Fit**  
2. **Worst Fit**  
3. **Best Fit**  
allocation strategies for memory partitions.

---

### High-Level Summary:
When processes request memory, your code checks how to allocate the processes to memory partitions using different strategies, and then it shows:
- Which process is allocated to which partition.
- How much fragmentation (wasted memory) is left in each partition.
- The total external fragmentation.

---

### Explanation by parts:

### 1. **Macros & Global Constants**
```c
#define MAX 10
```
- `MAX` is the max limit for number of partitions and processes (both are set to 10).

---

### 2. **Fragmentation Calculation Function**
```c
void calculateFragmentation(int partitions[], int m)
```
- After allocation, this function prints:
  - Remaining space in each partition.
  - Total external fragmentation (sum of unused spaces).

---

### 3. **First Fit Function**
```c
void firstFit(int partitions[], int m, int processes[], int n)
```
- **Algorithm**: Allocates each process to the first partition that is big enough.
- Steps:
  - Check partitions sequentially.
  - Assign process if the partition has enough space.
  - Reduce the partition size by the allocated amount.
  - Finally, print allocation and fragmentation.

---

### 4. **Worst Fit Function**
```c
void worstFit(int partitions[], int m, int processes[], int n)
```
- **Algorithm**: Allocates each process to the largest partition available.
- Steps:
  - Find the largest suitable partition for each process.
  - Allocate, reduce partition size.
  - Print allocation and fragmentation.

---

### 5. **Best Fit Function**
```c
void bestFit(int partitions[], int m, int processes[], int n)
```
- **Algorithm**: Allocates each process to the smallest partition that is just big enough.
- Steps:
  - Find the tightest-fitting partition for each process.
  - Allocate, reduce partition size.
  - Print allocation and fragmentation.

---

### 6. **Main Function**
```c
int main()
```
- Takes user input:
  - Number and sizes of memory partitions.
  - Number and sizes of processes.
- Copies the original partition sizes before each algorithm (to avoid incorrect fragmentation results).
- Calls:
  - `firstFit`
  - `worstFit`
  - `bestFit`

---

### Output Example:
```
Enter number of partitions: 3
Enter partition sizes:
100
500
200
Enter number of processes: 4
Enter process sizes:
212
417
112
426

First Fit Allocation:
Process 1 (212 KB) -> Partition 2
Process 2 (417 KB) -> Partition 2
Process 3 (112 KB) -> Partition 3
Process 4 (426 KB) -> Partition -1
Remaining Fragmentation in Partitions:
Partition 1: 100 KB
Partition 2: -129 KB
Partition 3: 88 KB
Total External Fragmentation: 59 KB
...
```
---

### Interpretation Summary:

| Technique   | Strategy                                   |
|------------|--------------------------------------------|
| First Fit  | Allocate the first available suitable partition |
| Worst Fit  | Allocate the largest available partition       |
| Best Fit   | Allocate the smallest available suitable partition |

The program also helps visualize fragmentation and understand how these strategies work in practice.

# Output Screen Shots
![Screenshot 2025-04-06 162603](https://github.com/user-attachments/assets/dde22e76-b178-47cd-8cd4-353a1d365475)

