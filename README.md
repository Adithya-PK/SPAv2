# Student Performance Analyzer

A local, file-based academic performance analysis application for colleges to manage subject-wise examination uploads, analyze student performance, visualize results, generate reports, and export professional PDF/Excel reports.

> **Application:** Student Performance Analyzer  
> **College:** Easwari Engineering College  
> **Department:** Artificial Intelligence and Data Science  
> **Status:** Active development

---

## 1. Overview

The **Student Performance Analyzer (SPA)** is designed to help faculty analyze examination performance without manually calculating pass percentages, failures, attendance, borderline students, failure distributions, or student-level risk.

The complete workflow is:

```text
Academic Configuration
        ↓
Subject & Faculty Assignment
        ↓
Excel Upload
        ↓
Analysis Engine
        ↓
Dashboard
        ↓
Reports
        ↓
PDF / Excel Export
```

The application is intended to run locally on a faculty member's personal computer and does not require an external SQL database.

---

## 2. Objectives

The project aims to:

- Reduce manual academic result analysis.
- Read examination data directly from Excel files.
- Automatically calculate academic performance metrics.
- Track subject upload progress.
- Provide subject-wise and overall analysis.
- Identify failed, absent, borderline, and high-risk students.
- Generate reusable reports.
- Export professional PDF and Excel reports.
- Keep Dashboard, Upload, Settings, Reports, and Exports synchronized.
- Be simple to deploy and maintain on faculty computers.

---

## 3. Core Features

### Academic Structure

The application supports:

- Academic Year
- Year I, II, III, IV
- Semester 1–8
- Sections A, B, C, D
- Exam selection
- Subject configuration
- Faculty assignment

Hierarchy:

```text
Academic Year
 └── Year
      └── Semester
           └── Section
                └── Subject
                     └── Faculty
```

Example:

```text
2025-2026
 └── Year III
      ├── Semester 5
      │    ├── Section A
      │    ├── Section B
      │    ├── Section C
      │    └── Section D
      │
      └── Semester 6
           ├── Section A
           ├── Section B
           ├── Section C
           └── Section D
```

Each academic year is independent.

---

## 4. Subject and Faculty Architecture

A subject belongs to the **semester**.

Faculty belongs to the **section**.

This prevents duplicate subject creation.

For example, Semester 5 may contain:

| Subject Code | Subject Name |
|---|---|
| 231ADC601T | Data Analytics |
| 231ADC602T | Data Exploration and Visualization |
| 231CSE914T | Large Language Models |
| 231CSE913T | Recommender Systems |
| 231IBM0901T | Health Informatics |
| 231CSE918T | AI for Edge Computing |

These subjects automatically apply to Sections A, B, C, and D.

Faculty can then differ:

```text
Section A
Data Analytics → Faculty A
Data Exploration and Visualization → Faculty B

Section B
Data Analytics → Faculty C
Data Exploration and Visualization → Faculty D
```

Subject code and subject name remain the same. Only faculty changes.

---

## 5. Settings Page

Settings is the **single source of truth for academic configuration**.

### Step 1 — Semester Subjects

Select:

```text
Academic Year
Year
Semester
```

Then configure:

```text
Subject Code
Subject Name
```

Use one subject table.

### Step 2 — Section Faculty

Select:

```text
Section A / B / C / D
```

The semester's subjects appear automatically.

Only the Faculty column is editable.

Subject Code and Subject Name are read-only.

There must be no empty or duplicate faculty-assignment rows created after saving.

---

## 6. Data Storage

The application intentionally does not use:

- SQL
- SQLite
- PostgreSQL
- MySQL
- MongoDB
- Firebase
- Supabase
- Any external database

Configuration is stored locally using JSON.

A representative structure is:

```text
data/
├── academic_years/
│   └── 2025-2026/
│       ├── I/
│       │   ├── Semester1/
│       │   └── Semester2/
│       ├── II/
│       │   ├── Semester3/
│       │   └── Semester4/
│       ├── III/
│       │   ├── Semester5/
│       │   │   ├── subjects.json
│       │   │   ├── section_A.json
│       │   │   ├── section_B.json
│       │   │   ├── section_C.json
│       │   │   └── section_D.json
│       │   └── Semester6/
│       └── IV/
│           ├── Semester7/
│           └── Semester8/
│
├── uploads/
└── reports/
```

The exact project may contain additional files, but the architectural principle is the same: configuration and academic files are local and shared rather than duplicated in individual pages.

---

## 7. Single Source of Truth

The intended architecture is:

```text
Settings
   │
   ▼
Configuration JSON
   │
   ├──────────────► Dashboard
   ├──────────────► Upload
   ├──────────────► Reports
   └──────────────► Export
```

Only Settings modifies academic configuration.

Upload handles uploaded examination files.

Dashboard, Reports, and Export read the shared configuration and analysis results.

This prevents stale or conflicting subject/faculty data.

---

## 8. Technology Stack

### Frontend

- React
- TypeScript
- Vite
- React Router
- React Context for shared academic context
- Local persistence where required
- Responsive chart components

The frontend handles:

- Navigation
- Academic filters
- Settings
- Excel upload UI
- Dashboard
- Reports
- Charts
- Loading states
- Empty states
- Error states

### Backend

- Python
- FastAPI
- Uvicorn

The backend handles:

- REST APIs
- Configuration
- Excel upload processing
- Analysis
- Report generation
- Export generation
- Local file access

---

## 9. Why FastAPI Is Used

FastAPI provides the bridge between the React frontend and the local Python analysis system.

Example:

```text
React
  ↓ HTTP request
FastAPI
  ↓
JSON / Excel / Analysis Services
  ↓
FastAPI response
  ↓
React
```

For an Excel upload:

```text
Excel
  ↓
React Upload
  ↓
FastAPI
  ↓
Analysis Engine
  ↓
Calculated Metrics
  ↓
Dashboard / Reports
```

This separates the UI from file handling and analysis logic.

---

## 10. Analysis Engine

The Analysis Engine converts uploaded Excel data into academic metrics.

Typical calculations include:

- Total strength
- Attended
- Absent
- Passed
- Failed
- Pass percentage
- Fail percentage
- Average marks
- Highest marks
- Lowest marks
- Borderline students
- Failure count
- Failure distribution
- Student-level marks
- Subject-level statistics

The intended flow is:

```text
Excel
 ↓
Parser
 ↓
Normalized Student Data
 ↓
Analysis Engine
 ↓
Calculated Metrics
 ↓
Dashboard / Reports / Export
```

The same calculated values should be reused everywhere.

---

## 11. Excel Upload

Subjects are uploaded individually.

Example:

```text
6 configured subjects

Data Analytics                         ✓ Uploaded
Data Exploration and Visualization     ✓ Uploaded
Large Language Models                   ✗ Missing
Recommender Systems                    ✗ Missing
Health Informatics                     ✗ Missing
AI for Edge Computing                  ✗ Missing
```

Upload progress is based on configured subjects versus valid uploaded subject files.

For example:

```text
3 / 6 subjects
50%
```

The system supports partial uploads.

If only one subject is uploaded, that subject can be analyzed.

If three subjects are uploaded, partial overall analysis can be generated.

Missing subjects must not be interpreted as zero marks or zero students.

---

## 12. Academic Validation Rules

### UT

```text
Maximum = 25
Pass = 15
Borderline = 15–17
```

### CAT

```text
Maximum = 50
Pass = 33
Borderline = 33–35
```

### End Semester

```text
AB → Absent
U  → Fail
W  → Withdrawn
Other valid grade → Pass
```

These rules should be centralized so Dashboard, Reports, and Exports use identical calculations.

---

## 13. Dashboard

The Dashboard provides a section-level overview.

It displays:

- Academic Year
- Year
- Semester
- Section
- Exam
- Overall Pass %
- Overall Fail %
- Students Passed
- Students Failed
- Students Absent
- Borderline Students
- Upload Progress
- Subject Cards
- Subject Performance
- Failure Distribution
- Student Attention / Risk information

Subject cards display:

```text
Subject Name
Subject Code
Faculty
Upload Status
Open Analysis
```

Open Analysis should take the user directly to the corresponding Report 3 analysis.

All dashboard statistics must come from uploaded Excel analysis. No placeholder or hardcoded academic values should be used.

---

## 14. Dashboard Data Integrity

The same uploaded data must produce the same result everywhere.

For example, if the Analysis Engine calculates:

```text
Total Students = 64
Passed = 43
Failed = 19
Absent = 2
Pass % = 69.35%
```

those values should remain consistent in:

```text
Dashboard
Report 1
Report 2
Report 3
PDF
Excel
```

A difference between these views indicates a synchronization or calculation-source problem.

---

## 15. Shared Academic Filters

The selected context is shared between pages.

Filters:

```text
Academic Year
Year
Semester
Section
Exam
```

Example:

```text
2025-2026
Year III
Semester 6
Section A
UT 1
```

When navigating:

```text
Dashboard
 ↓
Upload
 ↓
Reports
 ↓
Settings
 ↓
Dashboard
```

the same context should remain selected.

The user should not repeatedly select the same filters.

A shared React Context plus local persistence can be used.

---

## 16. Dependent Filters

Filters must follow the academic hierarchy.

Example:

```text
Year III
```

should expose:

```text
Semester 5
Semester 6
```

Changing Year automatically updates Semester choices.

Invalid combinations must not be selectable.

The same context must be used consistently by Dashboard, Upload, Reports, and Settings.

---

## 17. Upload Page

After selecting:

```text
Academic Year
Year
Semester
Section
Exam
```

the Upload page displays only subjects configured for that semester.

Faculty comes from the selected section configuration.

Subject Name and Subject Code come from the semester configuration.

The Upload page must not maintain its own independent subject list.

---

## 18. Report 1 — Overall Section Analysis

Report 1 provides section-level analysis.

Columns:

| Faculty | Subject | Subject Code | Strength | Attended | Absent | Passed | Failed | Pass % | Fail % |
|---|---|---|---:|---:|---:|---:|---:|---:|---:|

Rows can expand to show:

- Failed Students
- Absent Students
- Borderline Students

Charts include:

- Subject Pass %
- Attendance
- Marks Distribution / Average Marks
- Pass vs Fail where appropriate

---

## 19. Report 2 — Failure Distribution

Students are grouped by number of failed subjects:

```text
All Pass
1 Failure
2 Failures
3 Failures
4 Failures
More than 4 Failures
```

Expandable categories show:

```text
Student Name
Register Number
Failed Subjects
```

Charts:

- Failure Distribution
- Overall Pass vs Fail

---

## 20. Report 3 — Subject Analysis

Report 3 provides detailed subject-level analysis.

Summary:

| Faculty | Subject | Subject Code | Strength | Attended | Absent | Passed | Failed | Pass % | Fail % |
|---|---|---|---:|---:|---:|---:|---:|---:|---:|

The subject selector displays **Subject Names**, for example:

```text
Data Analytics
Data Exploration and Visualization
AI for Edge Computing
```

Subject codes remain in the summary and exports.

The selected subject must correspond exactly to its summary row and detailed student data.

---

## 21. Report 3 Order

The intended analytical order is:

```text
Report Filters
        ↓
Subject Selection
        ↓
Subject Summary
        ↓
Charts
        ↓
Failed Students
        ↓
Absent Students
        ↓
Borderline Students
        ↓
Student Marks Table
```

Charts include:

- Pass vs Fail
- Marks Distribution
- Attendance
- Borderline Students where meaningful

Charts should be responsive and readable.

---

## 22. Student Attention / Risk

Students requiring attention should be sorted by severity:

```text
High Risk
     ↓
Multiple Failures
     ↓
Borderline
     ↓
Absent
     ↓
Low Risk
```

The highest-risk students should appear first.

---

## 23. Student Correlation

When multiple subject files are available, students are correlated using their register number.

Example:

```text
Register Number
Student Name
Data Analytics Marks
Data Exploration Marks
LLM Marks
Recommendation Systems Marks
Health Informatics Marks
Edge Computing Marks
```

This allows the system to determine:

- Number of failed subjects
- All-pass status
- Multiple-failure status
- Subject-wise marks
- Borderline status
- Overall student performance

Partial uploads must be handled safely. Missing data must not become artificial zero marks.

---

## 24. Excel Export

Excel exports must use the same calculated data shown in the application.

Report 3 can contain a consolidated student table:

| Register Number | Student Name | Subject 1 Marks | Subject 2 Marks | Subject 3 Marks | Subject 4 Marks | Subject 5 Marks | Subject 6 Marks |
|---|---|---:|---:|---:|---:|---:|---:|

At the bottom:

```text
Total Students
Passed
Failed
Borderline
Absent
Overall Pass %
Overall Fail %
```

These values must be calculated from the same analysis source used by the dashboard and reports.

---

## 25. Excel Branding

Excel reports should contain:

```text
Easwari Engineering College
Bharathi Salai
Ramapuram
Chennai - 89

Department of Artificial Intelligence and Data Science

Student Performance Analyzer
```

The college logo should be:

- Correctly positioned
- Properly sized
- Proportionally scaled
- Not stretched
- Aligned with the report header
- Consistent across worksheets

---

## 26. PDF Export

PDF reports should include:

```text
College Logo

Easwari Engineering College
Bharathi Salai
Ramapuram
Chennai - 89

Department of Artificial Intelligence and Data Science

Student Performance Analyzer

Academic Year
Year
Semester
Section
Exam
```

PDF values must match Dashboard and Reports.

---

## 27. Data Visualization

Charts are intended to provide quick visual understanding before detailed student tables.

General structure:

```text
Summary
   ↓
Charts
   ↓
Detailed Data
```

Charts should:

- Use consistent colours
- Have clear labels
- Have readable legends
- Use appropriate axes
- Avoid unnecessary empty space
- Resize correctly on smaller screens
- Match the underlying table data

### Dashboard

Use appropriate visualizations for:

- Subject Performance
- Failure Distribution
- Pass vs Fail
- Upload Progress

### Report 1

Use:

- Subject Pass %
- Attendance
- Average / Marks Distribution

### Report 2

Use:

- Failure Distribution
- Overall Pass vs Fail

### Report 3

Use:

- Pass vs Fail
- Marks Distribution
- Attendance
- Borderline Students

---

## 28. Dashboard Layout

Where a table and chart appear side by side, they should be visually aligned.

For example:

```text
┌───────────────────────────────┬───────────────────┐
│ Subject Analysis Table        │ Failure           │
│                               │ Distribution      │
│                               │                   │
│                               │                   │
└───────────────────────────────┴───────────────────┘
```

The Failure Distribution chart should:

- Have an appropriate width
- Match the table's visual height
- Be vertically aligned
- Avoid excessive white space
- Remain responsive

---

## 29. Navigation and Context Preservation

Navigation should preserve the selected academic context.

Example:

```text
Upload
2025-2026 / Year III / Semester 6 / Section A / UT 1

        ↓

Dashboard

        ↓

Reports

        ↓

Upload
```

The same:

```text
Academic Year
Year
Semester
Section
Exam
```

should still be selected.

Changing the context should update all pages using that context.

---

## 30. Error Handling

The application should handle:

- Backend unavailable
- Failed API requests
- Invalid Excel files
- Empty Excel files
- Incorrect subject uploads
- Missing configuration
- Missing faculty
- Duplicate uploads
- Invalid academic combinations
- Corrupt JSON
- Partial uploads

Errors should be understandable to faculty users.

---

## 31. Failed Fetch Troubleshooting

The React frontend communicates with FastAPI.

If FastAPI is not running, or if the frontend points to the wrong port, the browser may show:

```text
Failed to fetch
```

Start the backend:

```bash
cd backend
uvicorn app.main:app --reload
```

If port 8000 is unavailable:

```bash
uvicorn app.main:app --reload --port 8001
```

Make sure the frontend API configuration points to the same port.

---

## 32. Running Locally

### Requirements

Install:

- Python 3.x
- Node.js
- npm

Verify:

```bash
python --version
node --version
npm --version
```

### Backend

```bash
cd backend
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

### Frontend

Open another terminal:

```bash
cd frontend
npm install
npm run dev
```

Then open the Vite URL shown in the terminal.

---

## 33. Windows Deployment

The application is intended to be deployable on a faculty member's Windows PC.

Helper scripts may include:

```text
run_project.bat
start_backend.bat
start_frontend.bat
```

Before distributing the application, verify that these scripts use portable paths and do not depend on the developer's personal machine.

The intended experience is:

```text
Start Project
      ↓
Backend starts
      ↓
Frontend starts
      ↓
Browser opens
      ↓
Faculty uses application
```

---

## 34. Why No Database?

For the current scope, an external database is unnecessary.

The project mainly needs:

- Academic configuration
- Subject configuration
- Faculty assignments
- Uploaded Excel files
- Generated reports

These can be handled with:

```text
JSON
+
Local folders
+
Excel files
```

Advantages:

- Simple deployment
- Easy backup
- Easy debugging
- Easy copying to another PC
- No database server
- No database credentials
- No cloud dependency

A database can be considered later if the project becomes a multi-user cloud application.

---

## 35. Backup

Important local data includes:

```text
data/
uploads/
reports/
```

These folders should be backed up regularly.

Possible destinations:

- External drive
- College server
- OneDrive
- Google Drive
- Another secure backup location

Git should not be treated as the backup location for student academic data.

---

## 36. Git and Version Control

Check the current state:

```bash
git status
```

Add changes:

```bash
git add .
```

Commit:

```bash
git commit -m "Update Student Performance Analyzer"
```

Push:

```bash
git push origin master
```

If the repository uses `main`:

```bash
git push origin main
```

Check the current branch:

```bash
git branch
```

Check the remote:

```bash
git remote -v
```

---

## 37. Files That Should Not Be Committed

Avoid committing:

```text
.venv/
node_modules/
__pycache__/
*.pyc
.env
large uploaded Excel files
temporary generated files
machine-specific files
```

Keep real student academic files out of a public GitHub repository unless properly authorized.

---

## 38. Recommended Architecture

```text
                 ┌──────────────────────┐
                 │       React UI       │
                 │                      │
                 │ Dashboard            │
                 │ Upload               │
                 │ Reports              │
                 │ Settings             │
                 └──────────┬───────────┘
                            │
                         HTTP API
                            │
                            ▼
                 ┌──────────────────────┐
                 │       FastAPI        │
                 │                      │
                 │ API Routes           │
                 │ Config Service       │
                 │ Upload Handling      │
                 │ Analysis Engine      │
                 │ Export Service       │
                 └──────────┬───────────┘
                            │
              ┌─────────────┼─────────────┐
              │             │             │
              ▼             ▼             ▼
        Configuration   Excel/Analysis   Export
           JSON            Engine        Service
              │             │             │
              ▼             ▼             ▼
          Local Data      Metrics       PDF / Excel
```

---

## 39. Architectural Rules

### Rule 1 — Settings owns configuration

Only Settings modifies:

- Subject lists
- Subject codes
- Subject names
- Faculty assignments

### Rule 2 — Upload owns uploaded files

Uploaded examination files are handled by the upload workflow.

### Rule 3 — Analysis Engine owns calculations

Academic calculations should not be duplicated across pages.

### Rule 4 — Dashboard is read-only

Dashboard displays calculated data.

### Rule 5 — Reports are read-only

Reports display calculated data.

### Rule 6 — Export uses analysis data

PDF and Excel should export the same values displayed in the application.

### Rule 7 — Shared configuration

No page should maintain a separate subject or faculty configuration.

---

## 40. Example End-to-End Flow

Suppose the faculty selects:

```text
Academic Year: 2025-2026
Year: III
Semester: 5
Section: A
Exam: UT 1
```

Settings contains:

```text
231ADC601T → Data Analytics → Faculty A
231ADC602T → Data Exploration and Visualization → Faculty B
231CSE914T → Large Language Models → Faculty C
```

The faculty uploads Data Analytics.

The system performs:

```text
Excel Upload
     ↓
Validate file
     ↓
Identify students
     ↓
Normalize records
     ↓
Analysis Engine
     ↓
Calculate metrics
     ↓
Store/retain upload and analysis information
     ↓
Dashboard
Reports
Exports
```

When another subject is uploaded, the available analysis updates using the newly available data.

---

## 41. Data Consistency Principle

The most important technical requirement is:

> The same uploaded data must produce the same result everywhere.

Every value should be traceable to:

```text
Academic Year
+
Year
+
Semester
+
Section
+
Exam
+
Subject
+
Uploaded File
```

This prevents:

- Random values appearing in another semester
- Hidden subject rows
- Wrong faculty assignments
- Stale dashboard values
- Incorrect report data
- Mismatched exports
- Data from one semester appearing in another

Missing configuration must result in an empty state, not fabricated values.

---

## 42. Privacy

Student academic information is sensitive.

Therefore:

- Do not upload real student Excel files to public GitHub repositories.
- Do not commit passwords or API keys.
- Do not commit `.env` files containing secrets.
- Keep academic files on authorized computers.
- Use secure backups.
- If cloud deployment is introduced later, implement authentication and authorization.

---

## 43. Future Enhancements

### Machine Learning Failure Prediction

A future ML module can predict failure risk using:

- Previous marks
- Attendance
- Internal assessment performance
- Previous failures
- Subject performance patterns

Possible output:

```text
Student
 ↓
Risk Score
 ↓
Low / Medium / High Risk
```

### Cloud Deployment

If the application eventually needs simultaneous access by multiple faculty members:

```text
React
+
FastAPI
+
Authentication
+
Cloud Database
+
Object Storage
```

can be introduced.

### Authentication

Potential roles:

```text
Admin
HOD
Faculty
```

### Historical Analytics

Future versions could analyze:

- Semester trends
- Subject difficulty
- Student progress
- Failure trends
- Faculty-level trends

---

## 44. Release Checklist

### Configuration

- [ ] Academic Year works
- [ ] Year works
- [ ] Semester dependency works
- [ ] Sections A-D work
- [ ] Subject creation works
- [ ] Faculty assignment works
- [ ] No empty duplicate rows appear
- [ ] Subject code/name are shared across sections
- [ ] Faculty can differ by section

### Upload

- [ ] Correct subjects appear
- [ ] Correct faculty appears
- [ ] Excel uploads correctly
- [ ] Upload progress is correct
- [ ] Partial uploads work
- [ ] Invalid files show useful errors

### Dashboard

- [ ] No hardcoded academic values
- [ ] Statistics match Analysis Engine
- [ ] Subject cards are correct
- [ ] Failure Distribution aligns correctly
- [ ] Upload progress is correct
- [ ] Risk ordering is correct

### Reports

- [ ] Report 1 works
- [ ] Report 2 works
- [ ] Report 3 works
- [ ] Subject selection works
- [ ] Subject selector shows names
- [ ] Charts match tables
- [ ] Student details match uploaded files

### Export

- [ ] PDF values match dashboard
- [ ] Excel values match dashboard
- [ ] College logo is aligned
- [ ] College information is correct
- [ ] Consolidated student table is correct

### Navigation

- [ ] Filters persist
- [ ] Dashboard → Reports works
- [ ] Dashboard → Upload works
- [ ] Upload → Dashboard works
- [ ] Settings changes appear everywhere
- [ ] No stale configuration remains

---

## 45. Summary

Student Performance Analyzer is a local academic analytics platform built around a simple architecture:

```text
Settings
   ↓
JSON Configuration
   ↓
Excel Upload
   ↓
Analysis Engine
   ↓
Dashboard
   ↓
Reports
   ↓
PDF / Excel
```

The central principle is **data consistency**.

A subject, subject code, faculty assignment, uploaded Excel file, analysis result, dashboard value, report value, and exported value must all refer to the same academic context:

```text
Academic Year
+
Year
+
Semester
+
Section
+
Exam
+
Subject
```

The project deliberately avoids an external database for the current deployment model, making it practical for individual faculty PCs while retaining structured configuration, reusable analysis, professional reporting, and local persistence.

---

## Project Information

**Student Performance Analyzer**

**Easwari Engineering College**  
Bharathi Salai, Ramapuram  
Chennai - 89  

**Department of Artificial Intelligence and Data Science**
