# Program Statement: Simulate Paging Technique of memory management.
# Source Code:
```c
#include <stdio.h>
#include <stdlib.h>

int *page_table; // Dynamic array for page table
int total_pages, total_frames, page_size, memory_size;

// Function to initialize the page table
void initialize_page_table() {
    printf("\nPage Table (Page -> Frame Mapping):\n");
    for (int i = 0; i < total_pages; i++) {
        page_table[i] = rand() % total_frames; // Random frame allocation
        printf("Page %d -> Frame %d\n", i, page_table[i]);
    }
    printf("\n");
}

// Function to translate logical address to physical address
void translate_address(int logical_address) {
    int page_number = logical_address / page_size;  // Extract page number
    int offset = logical_address % page_size;       // Extract offset

    if (page_number >= total_pages) {
        printf("Invalid Logical Address: %d (Page %d out of bounds)\n", logical_address, page_number);
        return;
    }

    int frame_number = page_table[page_number];  // Get frame from page table
    int physical_address = (frame_number * page_size) + offset;

    printf("Logical Address: %d -> Page: %d, Offset: %d -> Physical Address: %d\n",
           logical_address, page_number, offset, physical_address);
}

int main() {
    // User input for memory size and page size
    printf("Enter total memory size (in KB): ");
    scanf("%d", &memory_size);
    printf("Enter page size (in KB): ");
    scanf("%d", &page_size);

    memory_size *= 1024; // Convert to bytes
    page_size *= 1024;   // Convert to bytes

    total_pages = memory_size / page_size;
    total_frames = total_pages; // Assuming 1:1 frame availability

    // Allocate memory for page table
    page_table = (int *)malloc(total_pages * sizeof(int));

    // Initialize page table with random frame mapping
    initialize_page_table();

    int num_addresses;
    printf("Enter number of logical addresses to translate: ");
    scanf("%d", &num_addresses);

    for (int i = 0; i < num_addresses; i++) {
        int logical_address;
        printf("Enter logical address %d: ", i + 1);
        scanf("%d", &logical_address);
        translate_address(logical_address);
    }

    free(page_table); // Free allocated memory
    return 0;
}
```
## Interpretation

#### 1. **Global Variables**
```c
int *page_table;
int total_pages, total_frames, page_size, memory_size;
```
- `page_table`: dynamically allocated array to store mapping of pages to frames.
- `total_pages`: number of pages in logical memory.
- `total_frames`: number of frames in physical memory (assumed equal to total pages).
- `page_size`, `memory_size`: sizes in bytes.

---

#### 2. **Function: initialize_page_table()**
```c
void initialize_page_table() {
    for (int i = 0; i < total_pages; i++) {
        page_table[i] = rand() % total_frames; // Random frame allocation
        printf("Page %d -> Frame %d\n", i, page_table[i]);
    }
}
```
- Randomly assigns frames to pages.
- Prints the page table showing the mapping: **Page → Frame**.

---

#### 3. **Function: translate_address(int logical_address)**
```c
void translate_address(int logical_address) {
    int page_number = logical_address / page_size;
    int offset = logical_address % page_size;

    if (page_number >= total_pages) {
        printf("Invalid Logical Address\n");
        return;
    }

    int frame_number = page_table[page_number];
    int physical_address = (frame_number * page_size) + offset;

    printf("Logical Address: %d -> Page: %d, Offset: %d -> Physical Address: %d\n",
           logical_address, page_number, offset, physical_address);
}
```
- Breaks logical address into:
  - `page_number`: logical page number.
  - `offset`: offset within the page.
- Checks if page number is valid.
- Uses the page table to find `frame_number`.
- Calculates and prints the **physical address**.

---

#### 4. **Inside main()**
```c
int main() {
    // Take inputs for memory size and page size
    // Convert KB to bytes
    // Calculate total pages and frames
    // Allocate page table

    initialize_page_table(); // Random page → frame mapping

    // Get number of logical addresses to convert
    // For each, input logical address and translate
    free(page_table); // Deallocate memory

    return 0;
}
```

- User enters total memory size and page size (in KB).
- Program calculates total pages and frames.
- Page table is dynamically allocated.
- Page table is initialized with random frame numbers.
- User is asked to enter logical addresses to convert.
- Logical address is translated and physical address is displayed.
- Memory is freed at the end.

---

###  **Sample Flow**

1. **Input**
```
Enter total memory size (in KB): 4
Enter page size (in KB): 1
```
Assuming 4 KB memory, 1 KB pages:
- Total pages = 4
- Total frames = 4

2. **Random Page Table (example)**
```
Page 0 -> Frame 2
Page 1 -> Frame 3
Page 2 -> Frame 0
Page 3 -> Frame 1
```

3. **Translate Logical Address**
```
Enter number of logical addresses to translate: 1
Enter logical address 1: 2500
Logical Address: 2500 -> Page: 2, Offset: 452 -> Physical Address: 452
```


# Output:

![Screenshot 2025-04-06 163929](https://github.com/user-attachments/assets/9606399d-fb68-4c52-84fe-5177a311677a)
