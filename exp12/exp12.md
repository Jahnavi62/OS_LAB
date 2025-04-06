# Program Statement: 
## A printing service in an office environment where multiple users send print jobs to multiple printers. Each print job requires exclusive access to both a printer and a network resource to fetch the document to be printer. Simulate a scenario where deadlocks can occur.
# Source Code:
```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

// Mutex locks for network resource and printer
pthread_mutex_t network_lock;
pthread_mutex_t printer_lock;

// Function for User A's print job
void* print_job_A(void* arg) {
    printf("User A is requesting the network resource...\n");
    pthread_mutex_lock(&network_lock);
    printf("User A acquired the network resource and is fetching the document.\n");
    sleep(100); // Simulating document fetching

    printf("User A is requesting the printer...\n");
    pthread_mutex_lock(&printer_lock); // Deadlock occurs here
    printf("User A acquired the printer and is printing the document.\n");
    sleep(200);

    printf("User A finished printing and is releasing resources.\n");
    pthread_mutex_unlock(&printer_lock);
    pthread_mutex_unlock(&network_lock);

    return NULL;
}

// Function for User B's print job
void* print_job_B(void* arg) {
    printf("User B is requesting the printer...\n");
    pthread_mutex_lock(&printer_lock);
    printf("User B acquired the printer and is printing the document.\n");
    sleep(100); // Simulating printing delay

    printf("User B is requesting the network resource...\n");
    pthread_mutex_lock(&network_lock); // Deadlock occurs here
    printf("User B acquired the network resource and is fetching the document.\n");
    sleep(200);

    printf("User B finished printing and is releasing resources.\n");
    pthread_mutex_unlock(&network_lock);
    pthread_mutex_unlock(&printer_lock);

    return NULL;
}

int main() {
    pthread_t user1, user2;

    // Initialize mutex locks
    pthread_mutex_init(&network_lock, NULL);
    pthread_mutex_init(&printer_lock, NULL);

    // Creating two threads for User A and User B
    pthread_create(&user1, NULL, print_job_A, NULL);
    sleep(1); // Force interleaving
    pthread_create(&user2, NULL, print_job_B, NULL);

    // Waiting for both threads to finish (which they never will due to deadlock)
    pthread_join(user1, NULL);
    pthread_join(user2, NULL);

    // Destroy mutex locks (unreachable due to deadlock)
    pthread_mutex_destroy(&network_lock);
    pthread_mutex_destroy(&printer_lock);

    return 0;
}
```


### **Interpretation of the Code:**

This program simulates a **deadlock situation** in a printing environment using multithreading in C and mutex locks.

There are:
- **Two users**:  
  - **User A**: Wants to fetch a document from the network first, then print it.  
  - **User B**: Wants to print first, then fetch the document from the network.  

- **Two resources**:
  - **Network Resource** (to fetch the document)
  - **Printer** (to print the document)

- **Two mutex locks** are used to synchronize access to these shared resources:
  - `network_lock`
  - `printer_lock`

---

### **Flow of the Program:**

1. **Initialization:**
   - Mutex locks for network and printer are initialized.

2. **Threads:**
   - Two threads are created:
     - **Thread 1:** `print_job_A()` — simulates User A's job.
     - **Thread 2:** `print_job_B()` — simulates User B's job.
   - A sleep of `1 second` is introduced to increase the chances of deadlock by forcing some interleaving.

3. **User A's Print Job:**
   - Acquires the **network resource first**.
   - Simulates fetching the document (`sleep(100)`).
   - Tries to acquire the **printer** (but if User B already has it, User A gets blocked here).

4. **User B's Print Job:**
   - Acquires the **printer first**.
   - Simulates printing delay (`sleep(100)`).
   - Tries to acquire the **network resource** (but if User A has it, User B gets blocked here).

---

### **Deadlock Explanation:**

- User A locks **network** → then tries to lock **printer**.
- User B locks **printer** → then tries to lock **network**.

Both threads are waiting for each other to release their respective resources — **leading to a deadlock**.  
Since neither will release until the other releases their lock, the program gets stuck forever.

The lines where deadlock happens:
```c
pthread_mutex_lock(&printer_lock); // User A stuck here if User B has the printer
pthread_mutex_lock(&network_lock); // User B stuck here if User A has the network
```

---

### **Output Behavior:**

When you run this program, you will see output similar to:
```
User A is requesting the network resource...
User A acquired the network resource and is fetching the document.
User B is requesting the printer...
User B acquired the printer and is printing the document.
User A is requesting the printer...
User B is requesting the network resource...
```
Then it stops. The program will freeze here — this is the deadlock!

---


# OUTPUT:
![Screenshot 2025-04-06 165533](https://github.com/user-attachments/assets/3ed22b96-518b-4faa-a11f-1e340725e337)
