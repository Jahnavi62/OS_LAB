> # Program Statement
> ## To stimulate CPU Scheduling Algorithms like FCFS, SJF, RR and Priority using C

# Source Code
```c
#include <stdio.h>

struct Process {
    int pid;
    int burst_time;
    int arrival_time;
    int priority;
};

void calculate_avg_times(int wait_time[], int turnaround_time[], int n) {
    float total_wt = 0, total_tat = 0;
    for (int i = 0; i < n; i++) {
        total_wt += wait_time[i];
        total_tat += turnaround_time[i];
    }
    printf("\nTotal Waiting Time: %.2f", total_wt);
    printf("\nTotal Turnaround Time: %.2f", total_tat);
    printf("\nAverage Waiting Time: %.2f", total_wt / n);
    printf("\nAverage Turnaround Time: %.2f\n", total_tat / n);
}

void fcfs(struct Process p[], int n) {
    int wait_time[n], turnaround_time[n], completion_time[n];
    
    completion_time[0] = p[0].arrival_time + p[0].burst_time;
    wait_time[0] = 0;
    turnaround_time[0] = completion_time[0] - p[0].arrival_time;
    
    for (int i = 1; i < n; i++) {
        completion_time[i] = completion_time[i - 1] + p[i].burst_time;
        wait_time[i] = completion_time[i - 1] - p[i].arrival_time;
        turnaround_time[i] = wait_time[i] + p[i].burst_time;
    }
    
    
    printf("PID\tBT\tAT\tWT\tTAT\n");
    for (int i = 0; i < n; i++) {
        printf("%d\t%d\t%d\t%d\t%d\n", p[i].pid, p[i].burst_time, p[i].arrival_time, wait_time[i], turnaround_time[i]);
    }
    calculate_avg_times(wait_time, turnaround_time, n);
}

void sjf(struct Process p[], int n) {
    struct Process temp;
    int wait_time[n], turnaround_time[n], completion_time[n];
    
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (p[i].burst_time > p[j].burst_time) {
                temp = p[i];
                p[i] = p[j];
                p[j] = temp;
            }
        }
    }
    
    completion_time[0] = p[0].arrival_time + p[0].burst_time;
    wait_time[0] = 0;
    turnaround_time[0] = completion_time[0] - p[0].arrival_time;
    
    for (int i = 1; i < n; i++) {
        completion_time[i] = completion_time[i - 1] + p[i].burst_time;
        wait_time[i] = completion_time[i - 1] - p[i].arrival_time;
        turnaround_time[i] = wait_time[i] + p[i].burst_time;
    }
    
    printf("\nSJF Scheduling\n");
    printf("PID\tBT\tAT\tWT\tTAT\n");
    for (int i = 0; i < n; i++) {
        printf("%d\t%d\t%d\t%d\t%d\n", p[i].pid, p[i].burst_time, p[i].arrival_time, wait_time[i], turnaround_time[i]);
    }
    calculate_avg_times(wait_time, turnaround_time, n);
}

void priority_scheduling(struct Process p[], int n) {
    struct Process temp;
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (p[i].priority > p[j].priority) {
                temp = p[i];
                p[i] = p[j];
                p[j] = temp;
            }
        }
    }
    fcfs(p, n);
}

void round_robin(struct Process p[], int n, int quantum) {
    int remaining_bt[n], wait_time[n], turnaround_time[n], completion_time[n];
    for (int i = 0; i < n; i++) remaining_bt[i] = p[i].burst_time;
    int time = 0, done;
    
    printf("\nRound Robin Scheduling\n");
    printf("PID\tWT\tTAT\n");
    
    do {
        done = 1;
        for (int i = 0; i < n; i++) {
            if (remaining_bt[i] > 0) {
                done = 0;
                if (remaining_bt[i] > quantum) {
                    time += quantum;
                    remaining_bt[i] -= quantum;
                } else {
                    time += remaining_bt[i];
                    wait_time[i] = time - p[i].arrival_time - p[i].burst_time;
                    turnaround_time[i] = time - p[i].arrival_time;
                    printf("%d\t%d\t%d\n", p[i].pid, wait_time[i], turnaround_time[i]);
                    remaining_bt[i] = 0;
                }
            }
        }
    } while (!done);
    calculate_avg_times(wait_time, turnaround_time, n);
}

int main() {
    int n;
    printf("Enter number of processes: ");
    scanf("%d", &n);
    struct Process p[n];
    
    for (int i = 0; i < n; i++) {
        printf("Enter PID, Burst Time, Arrival Time, and Priority for Process %d: ", i + 1);
        scanf("%d %d %d %d", &p[i].pid, &p[i].burst_time, &p[i].arrival_time, &p[i].priority);
    }
    
    int choice, quantum;
    do {
        printf("Choose Scheduling Algorithm:\n1. FCFS\n2. SJF\n3. Priority\n4. Round Robin\n5. Exit\n");
        scanf("%d", &choice);
        
        switch (choice) {
            case 1:
		printf("\nFCFS Scheduling\n");
                fcfs(p, n);
                break;
            case 2:
                sjf(p, n);
                break;
            case 3:
		printf("\nPriority Sheduling \n");
                priority_scheduling(p, n);
                break;
            case 4:
                printf("Enter time quantum: ");
                scanf("%d", &quantum);
                round_robin(p, n, quantum);
                break;
            case 5:
                printf("Exiting program...\n");
                break;
            default:
                printf("Invalid choice\n");
        }
    } while (choice != 5);
    
    return 0;
}
```
> ## Interpretation of the C CPU Scheduling Simulation Program

> This C program simulates and compares several common CPU scheduling algorithms: FCFS, SJF, Priority, and Round Robin.  It aims to demonstrate how each algorithm affects process execution and performance by calculating and displaying waiting times and turnaround times.

> **Program Overview:**

> The program takes process information (PID, burst time, arrival time, priority) as input. The user selects a scheduling algorithm to simulate, and the program calculates and displays performance metrics for each process and their averages.

> **Code Structure and Functionality:**

> 1. **`struct Process`:**
> Defines the structure for process data: `pid`, `burst_time`, `arrival_time`, and `priority`.

> 2. **`compare_burst_time` & `compare_priority`:**
> Comparison functions for `qsort` (from `stdlib.h`) to sort processes by burst time (SJF) and priority (Priority scheduling).

> 3. **`calculate_avg_times`:**
> Calculates and *prints* total and average waiting time and turnaround time, including a header identifying the algorithm.  This function is called by each scheduling algorithm.

> 4. **`fcfs` (First-Come, First-Served):**
> Implements the FCFS algorithm.
> Calculates completion time, waiting time, and turnaround time.
> Handles idle time (when a process arrives after the previous one finishes).
> Calls `calculate_avg_times` to display results.

> 5. **`sjf` (Shortest Job First):**
> Implements SJF.
> Uses `qsort` with `compare_burst_time` to sort processes by burst time.
> Calculates completion, waiting, and turnaround times.
> Handles idle time.
> Calls `calculate_avg_times` to display results.

> 6. **`priority_scheduling`:**
> Implements priority scheduling.
> Uses `qsort` with `compare_priority` to sort processes by priority.
> Effectively uses FCFS on the sorted processes (highest priority first).
> Calls `fcfs` (because the logic is the same after sorting).

> 7. **`round_robin`:**
> Implements Round Robin.
> Uses a `quantum` (time slice).
> Tracks `remaining_bt` (remaining burst time).
> Calculates waiting and turnaround times.
> Handles preemption and queueing.
> Calls `calculate_avg_times` to display results.

> 8. **`main`:**
> Gets the number of processes.
> Takes process details as input.
> Presents a menu to choose a scheduling algorithm.
> Calls the chosen algorithm's function.
> Loops until the user exits.


> # HOW to execute Program
> Open the terminal
> 
> compile the program
>
> `gcc filename.c -o exec-name`
> 
> run the program
> 
> `./exec-name`
>
>  # Output of the program
> ![ Screen Shot 1](op1.png)
> ![ Screen Shot 2](op2.png)
> ![ Screen Shot 3](op3.png)
> ![ Screen Shot 4](op4.png)
>
> # **Conclusion:**
>
> This C program provides a valuable tool for understanding and comparing the behavior of several fundamental CPU scheduling algorithms: FCFS, SJF, Priority, and Round Robin. By simulating these algorithms with user-provided process data (PID, burst time, arrival time, and priority), the program calculates and displays key performance metrics, namely waiting time and turnaround time, for each process and their averages.  The clear output, including algorithm-specific headers, facilitates a direct comparison of how each algorithm impacts process execution.

> The program highlights the trade-offs inherent in different scheduling strategies.  FCFS, while simple, can suffer from the convoy effect. SJF, though optimal in terms of average waiting time (for non-preemptive scheduling), requires knowledge of burst times and can lead to starvation. Round Robin offers fairness and responsiveness, particularly for interactive systems, but its performance is sensitive to the chosen time quantum. Priority scheduling allows for prioritizing important tasks but must be carefully managed to avoid starvation or priority inversion.

> The program's improved features, such as the use of `qsort` for efficient sorting, the handling of idle time and negative wait times, and the simplified priority scheduling implementation, enhance its robustness and accuracy.  Ultimately, this simulation provides a practical way to explore the core concepts of CPU scheduling and appreciate the complexities involved in choosing the right algorithm for a given workload and system requirements.  It serves as a useful educational tool for learning about operating system principles.
