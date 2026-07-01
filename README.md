🎓 Student Performance Dashboard

A Power BI dashboard that tracks academic performance, attendance, and behavior for 1,000 students across classes and sections.

OVERVIEW

This dashboard gives a single view of how students are doing — academically, in attendance, and in behavior — with the ability to slice everything by Date, Class, Section, Subject, and Term.

Key metrics on the dashboard:
- Total Students: 1,000
- Average Attendance: 90.05%
- Average Score: 49.87%

DATA SOURCES

The dashboard is built from four flat tables:

| File              | Rows     | Description                                                |
|-------------------|----------|--------------------------------------------------------------|
| Students.xlsx     | ~1,000   | Student roster — ID, Name, Gender, Class, Section           |
| Attendance.xlsx   | ~100,000 | Daily attendance records — StudentID, Date, Status, Reason   |
| Scores.xlsx       | ~30,000  | Exam results — StudentID, Subject, ExamType, Score, MaxScore, Term |
| Behavior.xlsx     | ~6,500   | Behavior logs — StudentID, Date, BehaviorType, Notes         |

TABLE SCHEMAS

Students
- StudentID (Text) — Primary key (e.g. S0001)
- Name (Text)
- Gender (Text) — Male / Female
- Class (Number) — Grade level (1–12)
- Section (Text) — A, B, C, ...

Attendance
- StudentID (Text) — Foreign key → Students
- Date (Date)
- Status (Text) — Present / Absent / etc.
- Reason (Text) — Optional

Scores
- StudentID (Text) — Foreign key → Students
- Subject (Text) — Math, Science, English, History, Geography
- ExamType (Text) — e.g. Mid Term, Final
- Score (Number) — Points earned
- MaxScore (Number) — Points possible
- Term (Text) — Term 1, Term 2, Term 3

Behavior
- StudentID (Text) — Foreign key → Students
- Date (Date)
- BehaviorType (Text) — Disruptive, Late, Helpful, Participative, Absent without notice
- Notes (Text) — Free-text comment

DATA MODEL

All three transactional tables (Attendance, Scores, Behavior) relate to Students via StudentID in a one-to-many star schema:

Students (1 row per student)
   |
   |— 1:many —> Attendance
   |— 1:many —> Scores
   |— 1:many —> Behavior

A Date table (or the Date columns in Attendance/Behavior/Term in Scores) drives the filter panel and the Term trend visual.

DASHBOARD PAGES / VISUALS

- KPI cards: Total Students, Average Attendance %, Average Score %
- Avg Score by Subject and Class (matrix): Heat-mapped table of average score per subject, broken out by class
- Behavior Count per Type (donut): Share of each BehaviorType (Disruptive, Late, Helpful, Participative, Absent without notice)
- Performance Trend by Term (line): Average score trend across Term 1, Term 2, Term 3
- Total Students by Gender (bar): Male vs Female headcount
- Student by Score (table): Student list with a score-status indicator (color-coded)
- Filters: Date, Class, Section, Subjects, Term

KEY DAX MEASURES

Total Students = DISTINCTCOUNT(Students[StudentID])

Average Attendance % =
DIVIDE(
    CALCULATE(COUNTROWS(Attendance), Attendance[Status] = "Present"),
    COUNTROWS(Attendance)
)

Average Score % =
DIVIDE(
    SUM(Scores[Score]),
    SUM(Scores[MaxScore])
)

Avg Score by Subject =
AVERAGEX(Scores, DIVIDE(Scores[Score], Scores[MaxScore]))

Behavior Count = COUNTROWS(Behavior)

Note: If a "count students" measure returns unexpected results (e.g., inflated counts), make sure you're using DISTINCTCOUNT(Students[StudentID]) rather than COUNTROWS(Attendance) or similar row-level counts from a many-side table — a common cause of duplicated totals in a star schema like this one.

HOW TO USE

1. Open the .pbix file in Power BI Desktop.
2. Use the slicers on the right (Date, Class, Section, Subjects, Term) to filter the entire dashboard.
3. Hover over any visual for tooltips with exact values.
4. Click a data point (e.g., a class row or a behavior slice) to cross-filter the rest of the page.

REFRESHING DATA

To refresh with new data:
1. Replace Students.xlsx, Attendance.xlsx, Scores.xlsx, or Behavior.xlsx in the source folder (keep the same column headers).
2. In Power BI Desktop, go to Home → Refresh.
3. Verify relationships under Model view still map correctly on StudentID.

TECH STACK

- Power BI Desktop — data modeling, DAX, visualization
- Excel (.xlsx) — source data files
- Star schema — Students (dimension) + Attendance / Scores / Behavior (fact tables)

---
Project by Subhash — part of an ongoing Data Analysis / Power BI portfolio.
