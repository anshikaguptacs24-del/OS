
//FCFS #include <stdio.h>

typedef struct { int id; int arrival_time; int burst_time; int completion_time; int turnaround_time; int waiting_time; } Process;

int main() { int n; float total_wt = 0, total_tat = 0;

printf("--- FCFS Scheduling Algorithm ---\n");
printf("Enter the number of processes: ");
scanf("%d", &n);

Process p[n];
for (int i = 0; i < n; i++) {
    p[i].id = i + 1;
    printf("\nProcess P%d\n", p[i].id);
    printf("Arrival Time: ");
    scanf("%d", &p[i].arrival_time);
    printf("Burst Time: ");
    scanf("%d", &p[i].burst_time);
}

// Sort by Arrival Time
for (int i = 0; i < n - 1; i++) {
    for (int j = 0; j < n - i - 1; j++) {
        if (p[j].arrival_time > p[j+1].arrival_time) {
            Process temp = p[j];
            p[j] = p[j+1];
            p[j+1] = temp;
        }
    }
}

// Calculate Times
int current_time = 0;
for (int i = 0; i < n; i++) {
    if (current_time < p[i].arrival_time) {
        current_time = p[i].arrival_time; 
    }
    
    current_time += p[i].burst_time;
    p[i].completion_time = current_time;
    p[i].turnaround_time = p[i].completion_time - p[i].arrival_time;
    p[i].waiting_time = p[i].turnaround_time - p[i].burst_time;
    
    total_tat += p[i].turnaround_time;
    total_wt += p[i].waiting_time;
}
printf("\nPID\tAT\tBT\tCT\tTAT\tWT\n");
for (int i = 0; i < n; i++) {
    printf("P%d\t%d\t%d\t%d\t%d\t%d\n", p[i].id, p[i].arrival_time, 
           p[i].burst_time, p[i].completion_time, p[i].turnaround_time, p[i].waiting_time);
}
printf("\nAverage Turnaround Time: %.2f\n", total_tat / n);
printf("Average Waiting Time: %.2f\n", total_wt / n);

return 0;
}

// Preemptive SJF (SRTF) Algorithm

#include <stdio.h> #include <limits.h>

typedef struct { int id; int arrival_time; int burst_time; int remaining_time; int completion_time; int turnaround_time; int waiting_time; int is_completed; } Process;

int main() { int n; int current_time = 0; int completed = 0; float total_wt = 0, total_tat = 0;

printf("--- SJF (Preemptive / SRTF) Scheduling Algorithm ---\n");
printf("Enter the number of processes: ");
scanf("%d", &n);

Process p[n];

for (int i = 0; i < n; i++) {
    p[i].id = i + 1;
    printf("\nProcess P%d\n", p[i].id);
    printf("Arrival Time: ");
    scanf("%d", &p[i].arrival_time);
    printf("Burst Time: ");
    scanf("%d", &p[i].burst_time);
    p[i].remaining_time = p[i].burst_time;
    p[i].is_completed = 0;
}

while (completed != n) {
    int min_idx = -1;
    int min_rem_time = INT_MAX;

    for (int i = 0; i < n; i++) {
        if (p[i].arrival_time <= current_time && p[i].is_completed == 0) {
            if (p[i].remaining_time < min_rem_time) {
                min_rem_time = p[i].remaining_time;
                min_idx = i;
            }
            if (p[i].remaining_time == min_rem_time && min_idx != -1) {
                if (p[i].arrival_time < p[min_idx].arrival_time) {
                    min_idx = i;
                }
            }
        }
    }

    if (min_idx != -1) {
        p[min_idx].remaining_time--;
        current_time++;

        if (p[min_idx].remaining_time == 0) {
            p[min_idx].completion_time = current_time;
            p[min_idx].turnaround_time = p[min_idx].completion_time - p[min_idx].arrival_time;
            p[min_idx].waiting_time = p[min_idx].turnaround_time - p[min_idx].burst_time;
            p[min_idx].is_completed = 1;

            total_tat += p[min_idx].turnaround_time;
            total_wt += p[min_idx].waiting_time;
            completed++;
        }
    } else {
        current_time++;
    }
}

printf("\n--- Results (Preemptive / SRTF) ---\n");
printf("PID\tAT\tBT\tCT\tTAT\tWT\n");
for (int i = 0; i < n; i++) {
    printf("P%d\t%d\t%d\t%d\t%d\t%d\n", p[i].id, p[i].arrival_time, 
           p[i].burst_time, p[i].completion_time, p[i].turnaround_time, p[i].waiting_time);
}
printf("\nAverage Turnaround Time: %.2f\n", total_tat / n);
printf("Average Waiting Time: %.2f\n", total_wt / n);
//Sjf (non-preemtive) #include <stdio.h> #include <limits.h>

typedef struct { int id; int arrival_time; int burst_time; int completion_time; int turnaround_time; int waiting_time; int is_completed; } Process;

int main() { int n; int current_time = 0; int completed = 0; float total_wt = 0, total_tat = 0;

printf("--- SJF (Non-Preemptive) Scheduling Algorithm ---\n");
printf("Enter the number of processes: ");
scanf("%d", &n);

Process p[n];

// Input Process Details
for (int i = 0; i < n; i++) {
    p[i].id = i + 1;
    printf("\nProcess P%d\n", p[i].id);
    printf("Arrival Time: ");
    scanf("%d", &p[i].arrival_time);
    printf("Burst Time: ");
    scanf("%d", &p[i].burst_time);
    p[i].is_completed = 0; 
}

// Scheduling Logic
while (completed != n) {
    int min_idx = -1;
    int min_burst = INT_MAX;

    for (int i = 0; i < n; i++) {
        if (p[i].arrival_time <= current_time && p[i].is_completed == 0) {
            if (p[i].burst_time < min_burst) {
                min_burst = p[i].burst_time;
                min_idx = i;
            }
            // Tie-breaker: If burst times match, pick the one that arrived earlier
            if (p[i].burst_time == min_burst && min_idx != -1) {
                if (p[i].arrival_time < p[min_idx].arrival_time) {
                    min_idx = i;
                }
            }
        }
    }

    if (min_idx != -1) {
        current_time += p[min_idx].burst_time;
        p[min_idx].completion_time = current_time;
        p[min_idx].turnaround_time = p[min_idx].completion_time - p[min_idx].arrival_time;
        p[min_idx].waiting_time = p[min_idx].turnaround_time - p[min_idx].burst_time;
        p[min_idx].is_completed = 1;
        
        total_tat += p[min_idx].turnaround_time;
        total_wt += p[min_idx].waiting_time;
        completed++;
    } else {
        current_time++; // CPU is idle, wait for a process to arrive
    }
}

// Output Results
printf("\n--- Results (Non-Preemptive) ---\n");
printf("PID\tAT\tBT\tCT\tTAT\tWT\n");
for (int i = 0; i < n; i++) {
    printf("P%d\t%d\t%d\t%d\t%d\t%d\n", p[i].id, p[i].arrival_time, 
           p[i].burst_time, p[i].completion_time, p[i].turnaround_time, p[i].waiting_time);
}
printf("\nAverage Turnaround Time: %.2f\n", total_tat / n);
printf("Average Waiting Time: %.2f\n", total_wt / n);

return 0;
}

return 0;
}
