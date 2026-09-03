# ECE-Programming-LAB-Project-2510034-2510033-2510045-
The Student Result & Attendance Tracker is a menu-driven C programming project designed to manage and evaluate student academic information efficiently. The system allows users to add student information, record and evaluate their results, track attendance, view saved records, display all registered students, and search for students by name.


  Roll 2510034's Part(Handled the Structures, Data Type,GRading Logic and Github part)
  Roll 2510045's Part(Handling the file and persisting the logic)
  Roll 2510033's Part(Handling application driver & dynamic management)
  
## *Code :*
```C

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define NUM_COURSES 5
#define DEPT_NAME "ECE (Electrical and Computer Engineering)"

const char *course_names[NUM_COURSES] = {
    "Circuit and Systems", "C Programming", "Math", "English", "Physics"
};

/* ============================================================================
   PART 1: STRUCTURES, DATA TYPES & GRADING LOGIC
   ============================================================================ */

typedef enum { FAIL = 0, PASS = 1 } AcademicStatus;

union ResultDetail {
    float cgpa;
    char grade_letter[3];
};

struct Course {
    char   name[30];
    float  attendance_percent;
    float  attendance_marks;
    float  ct_marks;
    float  assignment_marks;
    float  final_marks;
    float  total;
    int    barred;
    float  grade_point;
    int    failed;
};

struct Student {
    char name[50];
    char dept[60];
    struct Course courses[NUM_COURSES];
    float cgpa;
    AcademicStatus status;
    union ResultDetail detail;
};


float get_attendance_marks(float att, int *barred) {
    if (att < 50)  { *barred = 1; return 0; }
    *barred = 0;
    if (att <= 50) return 5;
    if (att <= 60) return 6;
    if (att <= 70) return 7;
    if (att <= 80) return 8;
    return 10; 
}

if submitted, else 0
float get_assignment_marks(char submitted) {
    return (submitted == 'y' || submitted == 'Y') ? 10 : 0;
}

float get_grade_point(float total, int *failed) {
    if (total < 40) { *failed = 1; return 0.0; }
    *failed = 0;
    if (total <= 50) return 2.5;
    if (total <= 60) return 3.0;
    if (total <= 70) return 3.5;
    if (total <= 75) return 3.75;
    return 4.0; // above 75
}


void input_course(struct Course *c, const char *name) {
    strcpy(c->name, name);
    printf("\n-- %s --\n", name);

    printf("Attendance %% (0-100): ");
    scanf("%f", &c->attendance_percent);
    if (c->attendance_percent < 0) c->attendance_percent = 0;
    if (c->attendance_percent > 100) c->attendance_percent = 100;
    c->attendance_marks = get_attendance_marks(c->attendance_percent, &c->barred);

    printf("CT marks (0-20): ");
    scanf("%f", &c->ct_marks);
    if (c->ct_marks < 0) c->ct_marks = 0;
    if (c->ct_marks > 20) c->ct_marks = 20;

    char submitted;
    printf("Assignment submitted? (y/n): ");
    scanf(" %c", &submitted);
    c->assignment_marks = get_assignment_marks(submitted);

    if (c->barred) {
        printf("[BARRED] Attendance below 50%% - cannot sit for Semester Final.\n");
        c->final_marks = 0;
    } else {
        printf("Semester Final marks (0-60): ");
        scanf("%f", &c->final_marks);
        if (c->final_marks < 0) c->final_marks = 0;
        if (c->final_marks > 60) c->final_marks = 60;
    }

    c->total = c->attendance_marks + c->ct_marks + c->assignment_marks + c->final_marks;
    c->grade_point = get_grade_point(c->total, &c->failed);
}

// Averages the 5 course grade points into a CGPA and sets overall PASS/FAIL
void update_cgpa(struct Student *s) {
    float sum = 0;
    int any_failed = 0;

    for (int i = 0; i < NUM_COURSES; i++) {
        sum += s->courses[i].grade_point;
        if (s->courses[i].failed) any_failed = 1;
    }

    s->cgpa = sum / NUM_COURSES;
    s->status = any_failed ? FAIL : PASS;
}



/* ============================================================================
   PART 2: FILE HANDLING & PERSISTENCE LOGIC
   ============================================================================ */

void write_log(struct Student s) {
    FILE *f = fopen("student_log.txt", "a");
    if (!f) { printf("File write error!\n"); return; }

    fprintf(f, "Department: %s | Student: %s | CGPA: %.2f | Overall: %s\n",
            s.dept, s.name, s.cgpa, s.status == PASS ? "PASS" : "FAIL");

    for (int i = 0; i < NUM_COURSES; i++) {
        struct Course *c = &s.courses[i];
        fprintf(f, "   %-20s Total: %5.1f  GP: %.2f%s\n",
                c->name, c->total, c->grade_point, c->failed ? " (FAIL)" : "");
    }
    fprintf(f, "--------------------------------------------------------\n");
    fclose(f);
}

void read_log(void) {
    FILE *f = fopen("student_log.txt", "r");
    if (!f) { printf("No log file found yet.\n"); return; }
    char ch;
    while ((ch = fgetc(f)) != EOF) putchar(ch);
    fclose(f);
}

/* ============================================================================
   PART 3: APPLICATION DRIVER & DYNAMIC MANAGEMENT
   ============================================================================ */

// Recursive countdown for academic advisory warning
int warning_countdown(int sec) {
    if (sec <= 0) {
        printf("[0] NOTICE ISSUED! ECE Advisor meeting required.\n");
        return 0;
    }
    printf("[%d] Warning dispatched...\n", sec);
    return warning_countdown(sec - 1);
}

void view_all_students(struct Student *arr, int count) {
    if (count == 0) { printf("No students added yet.\n"); return; }
    for (int i = 0; i < count; i++)
        printf("%d. %-15s | Dept: %s | CGPA: %.2f | %s\n",
               i + 1, arr[i].name, arr[i].dept, arr[i].cgpa,
               arr[i].status == PASS ? "PASS" : "FAIL");
}

void search_student(struct Student *arr, int count) {
    if (count == 0) { printf("No students added yet.\n"); return; }
    char query[50];
    printf("Enter name to search: ");
    scanf("%49s", query);

    for (int i = 0; i < count; i++) {
        if (strcmp(arr[i].name, query) == 0) {
            printf("\nFound: %s | Dept: %s | CGPA: %.2f | %s\n",
                   arr[i].name, arr[i].dept, arr[i].cgpa,
                   arr[i].status == PASS ? "PASS" : "FAIL");
            for (int j = 0; j < NUM_COURSES; j++) {
                struct Course *c = &arr[i].courses[j];
                printf("   %-20s Total: %5.1f  GP: %.2f%s\n",
                       c->name, c->total, c->grade_point, c->failed ? " (FAIL)" : "");
            }
            return;
        }
    }
    printf("No student named '%s' found.\n", query);
}

int main(void) {
    int choice, count = 0, capacity = 2;
    struct Student *students = malloc(capacity * sizeof(struct Student));

    if (!students) { printf("Memory Allocation Error!\n"); return 1; }

    do {
        printf("\n=== DEPARTMENT OF ECE: STUDENT RESULT & ATTENDANCE TRACKER ===\n"
               "1. Add & Evaluate Student\n2. View Saved Log (file)\n"
               "3. View All Students\n4. Search by Name\n5. Exit\n"
               "Enter choice (1-5): ");

        if (scanf("%d", &choice) != 1) {
            printf("[ERROR] Invalid input. Exiting.\n");
            free(students);
            return 1;
        }

        switch (choice) {
            case 1: {
                int n;
                printf("\nHow many students to add? ");
                if (scanf("%d", &n) != 1 || n <= 0) {
                    printf("[ERROR] Enter a positive number.\n");
                    break;
                }

                // Expand capacity if needed
                if (capacity < count + n) {
                    int needed = count + n;
                    struct Student *tmp = realloc(students, needed * sizeof(struct Student));
                    if (!tmp) { printf("[ERROR] Realloc failed!\n"); return 1; }
                    students = tmp;
                    capacity = needed;
                }

                for (int k = 0; k < n; k++) {
                    struct Student *s = &students[count];
                    printf("\n>>> Student %d of %d\n", k + 1, n);
                    printf("Enter Student Name: ");
                    scanf("%49s", s->name);

                    // Auto-assign Department Name
                    strcpy(s->dept, DEPT_NAME);

                    for (int i = 0; i < NUM_COURSES; i++) {
                        input_course(&s->courses[i], course_names[i]);
                    }

                    update_cgpa(s);
                    s->detail.cgpa = s->cgpa;

                    if (s->status == FAIL) {
                        printf("\n[ALERT] %s (%s) has one or more failed courses. CGPA: %.2f\n",
                               s->name, s->dept, s->cgpa);
                        printf("Initiating Counseling Notice Countdown:\n");
                        warning_countdown(3);
                    } else {
                        printf("\n[OK] %s (%s) cleared all courses. CGPA: %.2f\n",
                               s->name, s->dept, s->cgpa);
                    }

                    write_log(*s);
                    count++;
                }
                printf("\nAdded %d student(s). Total tracked: %d\n", n, count);
                break;
            }
            case 2: read_log(); break;
            case 3: view_all_students(students, count); break;
            case 4: search_student(students, count); break;
            case 5: printf("Exiting ECE Portal. Stay Motivated!\n"); break;
            default: printf("Invalid choice! Enter 1-5.\n");
        }
    } while (choice != 5);

    free(students);
    return 0;
}

```

## *Output :* 
*Making A Student's Result  and Attendance Tracker*
<p align="center">
<img width="350" height="427" alt="image" src="https://github.com/user-attachments/assets/99df72ef-b288-4a3e-937c-1f9e95c76ae3" />
<img width="830" height="472" alt="image" src="https://github.com/user-attachments/assets/0ca3826f-72bd-4b6c-ab4f-8d0acaa45e10" />
<img width="842" height="461" alt="image" src="https://github.com/user-attachments/assets/33fbec5f-d9d5-4954-8525-4673f5246b01" />





</p>

## *Discussion :*
<div align="justify">
  The Student Result & Attendance Tracker is a menu-driven C program developed to simplify the management of student academic records. The program allows users to add student information, enter and evaluate their results and attendance, view saved records, display all students, and search for a student by name.
</div>
