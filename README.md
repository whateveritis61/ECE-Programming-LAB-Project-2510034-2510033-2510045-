# ECE-Programming-LAB-Project-2510034-2510033-2510045-
The Student Result & Attendance Tracker is a menu-driven C programming project designed to manage and evaluate student academic information efficiently. The system allows users to add student information, record and evaluate their results, track attendance, view saved records, display all registered students, and search for students by name.


  Roll 2510034's Part(Handled the Structures, Data Type,GRading Logic and Github part)
  
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
    return 10; // above 80
}

// Assignment -> 10 if submitted, else 0
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

```


## *Output :* 
*Making A Student's Result  and Attendance Tracker*
<p align="center">
<img width="350" height="427" alt="image" src="https://github.com/user-attachments/assets/99df72ef-b288-4a3e-937c-1f9e95c76ae3" />


</p>

## *Discussion :*
<div align="justify">
  The Student Result & Attendance Tracker is a menu-driven C program developed to simplify the management of student academic records. The program allows users to add student information, enter and evaluate their results and attendance, view saved records, display all students, and search for a student by name.
</div>
