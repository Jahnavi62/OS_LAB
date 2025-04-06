# Program Statement: A library management system requires that only one user can borrow or return a book at a time to maintain database consistency.
# Source Code:
```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

pthread_mutex_t lock; // Mutex for synchronization
int books_available = 10; // Example: Total books in the library

void *borrow_book(void *arg) {
    pthread_mutex_lock(&lock); // Lock the critical section
    printf("User %ld trying to borrow a book...\n", pthread_self());
    
    if (books_available > 0) {
        books_available--;
        printf("User %ld borrowed a book. Books left: %d\n", pthread_self(), books_available);
    } else {
        printf("User %ld: No books available to borrow!\n", pthread_self());
    }

    pthread_mutex_unlock(&lock); // Unlock the critical section
    return NULL;
}

void *return_book(void *arg) {
    pthread_mutex_lock(&lock); // Lock the critical section
    printf("User %ld trying to return a book...\n", pthread_self());

    books_available++;
    printf("User %ld returned a book. Books now: %d\n", pthread_self(), books_available);

    pthread_mutex_unlock(&lock); // Unlock the critical section
    return NULL;
}

int main() {
    pthread_t users[30];

    if (pthread_mutex_init(&lock, NULL) != 0) {
        printf("Mutex initialization failed!\n");
        return 1;
    }

    // Creating threads for borrowing and returning books
    for (int i = 0; i < 15; i++) {
        if (pthread_create(&users[i], NULL, borrow_book, NULL) != 0) {
            printf("Error creating borrow thread %d\n", i);
        }
    }
    for (int i = 15; i < 30; i++) {
        if (pthread_create(&users[i], NULL, return_book, NULL) != 0) {
            printf("Error creating return thread %d\n", i);
        }
    }

    // Wait for all threads to finish
    for (int i = 0; i < 6; i++) {
        pthread_join(users[i], NULL);
    }

    pthread_mutex_destroy(&lock); // Destroy mutex
    return 0;
}

## Interpretaion of the code

### **Explanation Step by Step:**

1. **Header Files:**
   - `#include <stdio.h>` → For input/output functions.
   - `#include <pthread.h>` → For using POSIX threads (pthreads).
   - `#include <unistd.h>` → For sleep and other miscellaneous functions (though sleep is not used here).

2. **Global Variables:**
   - `pthread_mutex_t lock;` → Declares a mutex lock for synchronizing access to the shared resource.
   - `int books_available = 10;` → The library starts with 10 books.

3. **Function: `borrow_book`**
   - When a user tries to borrow a book:
     - **Locks** the mutex to enter the critical section.
     - Checks if books are available.
       - If **yes**, decrements the count and prints a success message.
       - If **no**, prints that no books are available.
     - **Unlocks** the mutex after the operation.

4. **Function: `return_book`**
   - When a user returns a book:
     - **Locks** the mutex to enter the critical section.
     - Increments the `books_available`.
     - Prints a success message with the updated count.
     - **Unlocks** the mutex after the operation.

5. **Function: `main`**
   - Initializes the mutex.
   - Declares an array of `pthread_t` to hold up to 30 user threads.
   - **Creates 15 threads** for borrowing books.
   - **Creates 15 threads** for returning books.
   - Waits (joins) for the first 6 threads to finish execution. (⚠️ Note: There is a potential mistake here. Ideally, it should join all 30 threads to ensure complete execution.)
   - Destroys the mutex before exiting.

---

### **Key Points in the Code:**

- **Synchronization:**  
  Mutex ensures only one thread accesses the shared variable `books_available` at a time, preventing data inconsistency.
  
- **Threads:**
  - Threads are used to simulate multiple users borrowing and returning books simultaneously.
  - The thread IDs (`pthread_self()`) are printed to identify different users.

- **Mutex Handling:**
  - Always lock before accessing/modifying shared data.
  - Always unlock after operation completion to avoid deadlock.

- **Minor Issue in Code:**  
  In `main()`, you have created 30 threads but only joined the first 6:
  ```c
  for (int i = 0; i < 6; i++) {
      pthread_join(users[i], NULL);
  }
  ```
   Ideally, this should be:
  ```c
  for (int i = 0; i < 30; i++) {
      pthread_join(users[i], NULL);
  }
  ```
  Otherwise, the program might exit before all threads complete.

---

### **Output Example (Sample):**
```
User 139805809292032 trying to borrow a book...
User 139805809292032 borrowed a book. Books left: 9
User 139805800899328 trying to borrow a book...
User 139805800899328 borrowed a book. Books left: 8
...
User 139805809292032 trying to return a book...
User 139805809292032 returned a book. Books now: 11
```

(Actual output order may vary due to thread scheduling.)

---


# OUTPUT:

![Screenshot 2025-04-06 164851](https://github.com/user-attachments/assets/46a005a3-8856-4830-ac68-4a509865fa8c)

![Screenshot 2025-04-06 164918](https://github.com/user-attachments/assets/8a2e7562-8f90-491d-9f0a-215c139fd70f)

