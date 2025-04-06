# Program Statement: To simulate Page replacement algorithm like FIFO, LRU, LFU and Optimal Algorithm
# Source Code:
```c
#include <stdio.h>

#define MAX_FRAMES 10
#define MAX_PAGES 20

// Function to implement FIFO Page Replacement
void fifo(int pages[], int n, int frames) {
    int queue[MAX_FRAMES], front = 0, rear = 0, count = 0;
    int pageFaults = 0;

    for (int i = 0; i < n; i++) {
        int found = 0;
        for (int j = 0; j < count; j++) {
            if (queue[j] == pages[i]) {
                found = 1;
                break;
            }
        }
        if (!found) {
            if (count < frames) {
                queue[rear++] = pages[i];
                count++;
            } else {
                queue[front] = pages[i];
                front = (front + 1) % frames;
            }
            pageFaults++;
        }
    }
    printf("FIFO Page Faults: %d\n", pageFaults);
}

// Function to implement LRU Page Replacement
void lru(int pages[], int n, int frames) {
    int stack[MAX_FRAMES], count = 0;
    int pageFaults = 0;

    for (int i = 0; i < n; i++) {
        int found = 0, index = -1;
        for (int j = 0; j < count; j++) {
            if (stack[j] == pages[i]) {
                found = 1;
                index = j;
                break;
            }
        }
        if (!found) {
            if (count < frames) {
                stack[count++] = pages[i];
            } else {
                for (int k = 1; k < count; k++)
                    stack[k - 1] = stack[k];
                stack[count - 1] = pages[i];
            }
            pageFaults++;
        } else {
            for (int k = index; k < count - 1; k++)
                stack[k] = stack[k + 1];
            stack[count - 1] = pages[i];
        }
    }
    printf("LRU Page Faults: %d\n", pageFaults);
}

// Function to implement LFU Page Replacement
void lfu(int pages[], int n, int frames) {
    int frame[MAX_FRAMES], freq[MAX_FRAMES] = {0}, count = 0;
    int pageFaults = 0;

    for (int i = 0; i < n; i++) {
        int found = 0, index = -1;
        for (int j = 0; j < count; j++) {
            if (frame[j] == pages[i]) {
                found = 1;
                freq[j]++;
                break;
            }
        }
        if (!found) {
            if (count < frames) {
                frame[count] = pages[i];
                freq[count] = 1;
                count++;
            } else {
                int minIdx = 0;
                for (int k = 1; k < count; k++) {
                    if (freq[k] < freq[minIdx])
                        minIdx = k;
                }
                frame[minIdx] = pages[i];
                freq[minIdx] = 1;
            }
            pageFaults++;
        }
    }
    printf("LFU Page Faults: %d\n", pageFaults);
}

// Function to implement Optimal Page Replacement
void optimal(int pages[], int n, int frames) {
    int frame[MAX_FRAMES], count = 0;
    int pageFaults = 0;

    for (int i = 0; i < n; i++) {
        int found = 0;
        for (int j = 0; j < count; j++) {
            if (frame[j] == pages[i]) {
                found = 1;
                break;
            }
        }
        if (!found) {
            if (count < frames) {
                frame[count++] = pages[i];
            } else {
                int farthest = -1, replaceIdx = -1;
                for (int j = 0; j < frames; j++) {
                    int nextUse = n;
                    for (int k = i + 1; k < n; k++) {
                        if (frame[j] == pages[k]) {
                            nextUse = k;
                            break;
                        }
                    }
                    if (nextUse > farthest) {
                        farthest = nextUse;
                        replaceIdx = j;
                    }
                }
                frame[replaceIdx] = pages[i];
            }
            pageFaults++;
        }
    }
    printf("Optimal Page Faults: %d\n", pageFaults);
}

int main() {
    int pages[MAX_PAGES], n, frames;
    
    printf("Enter number of pages: ");
    scanf("%d", &n);
    printf("Enter page reference string:\n");
    for (int i = 0; i < n; i++)
        scanf("%d", &pages[i]);
    
    printf("Enter number of frames: ");
    scanf("%d", &frames);
    
    fifo(pages, n, frames);
    lru(pages, n, frames);
    lfu(pages, n, frames);
    optimal(pages, n, frames);
    
    return 0;
}
```
## interpretation 

#### 1. FIFO — First In First Out

- **Idea:** Replace the oldest page in memory.
- **Implementation:**
  - Uses a queue to keep track of the order pages were added.
  - If page is not found in the queue:
    - If there’s space, add it.
    - Else, remove the oldest page and add the new one.
  - Each miss (page fault) increments `pageFaults`.

**Prints:**  
`FIFO Page Faults: X`

---

#### 2. LRU — Least Recently Used

- **Idea:** Replace the page that has not been used for the longest time.
- **Implementation:**
  - Uses a stack-like approach.
  - If a page is accessed and is already in the stack:
    - Move it to the top (most recently used).
  - If not found:
    - If space is available, add it.
    - If full, remove the least recently used (bottom of stack) and add new page.
  - Each miss (page fault) increments `pageFaults`.

**Prints:**  
`LRU Page Faults: X`

---

#### 3. LFU — Least Frequently Used

- **Idea:** Replace the page with the lowest frequency of access.
- **Implementation:**
  - Maintains two arrays:
    - `frame[]` to store the current pages.
    - `freq[]` to store frequency counts of corresponding pages.
  - If a page is accessed:
    - If found, increment its frequency.
    - If not found:
      - If space is available, add it and set frequency to 1.
      - If full, find the page with the lowest frequency and replace it.
  - Each miss (page fault) increments `pageFaults`.

**Prints:**  
`LFU Page Faults: X`

---

#### 4. Optimal

- **Idea:** Replace the page that **will not be used for the longest time in future**.
- **Implementation:**
  - For each page request:
    - If found in memory, do nothing.
    - If not:
      - If space is available, add it.
      - Else, look ahead in the reference string:
        - For each page in frame, find the next time it will be used.
        - Replace the one with the farthest next use (or not used again).
  - Each miss (page fault) increments `pageFaults`.

**Prints:**  
`Optimal Page Faults: X`

---

### Main Function

- Takes user input:
  - Number of pages.
  - Page reference string.
  - Number of frames.
- Calls all four functions one after another.
- Prints the total page faults for each algorithm.

---

### Example Output
```
Enter number of pages: 12
Enter page reference string:
1 3 0 3 5 6 3 1 2 4 3 2
Enter number of frames: 3

FIFO Page Faults: 10
LRU Page Faults: 9
LFU Page Faults: 8
Optimal Page Faults: 7
```

# Output

![Screenshot 2025-04-06 163033](https://github.com/user-attachments/assets/dd11ea74-042d-4797-b07f-b6d3960dba0d)
