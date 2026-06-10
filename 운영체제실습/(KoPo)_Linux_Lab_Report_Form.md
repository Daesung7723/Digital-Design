# Operating System Lab — Linux Lab Report

**Course**: Operating System Lab  
**Instructor**: Daesung Kim  
**Submission**: Convert to PDF and submit via email  
**Deadline**:

---

## Cover Page

| Item | Content |
|------|---------|
| Student ID | |
| Name | |
| Submission Date | |

---

## Scenario Overview

This report is a step-by-step lab to build a **Student Grade Management System** in a Linux environment.  
The directory structure and files created in M1 will be reused throughout all subsequent missions.  
Complete each mission in order, capture the result screen, and insert it in the designated location.

**Target Directory Structure**

```
~/score_project/
├── data/
│   └── students.txt
├── results/
│   ├── sorted.txt
│   └── filtered.txt
├── scripts/
│   ├── analyze_scores.py
│   └── run_all.sh
└── backup/
```

---

## Mission 1 — Create Project Directory Structure

**Objective**: Create the directory structure shown above for use in subsequent missions.  
**Related Chapter**: Ch. 2·3

**Description** (briefly explain what you did)

&nbsp;

**Capture** — Output showing the entire directory structure you created.

[Insert captured image]

---

## Mission 2 — Create and Edit the Grade Data File

**Objective**: Use a text editor to create a student grade data file.  
**Related Chapter**: Ch. 4  
**Prerequisite**: M1 complete

**File location**: `~/score_project/data/students.txt`

**Requirements**:
- One student per line in `name score` format
- **15 students or more**

**Description**

&nbsp;

**Capture** — `cat data/students.txt` result (full content must be visible)

[Insert captured image]

---

## Mission 3 — Data Processing with Pipes and Redirection

**Objective**: Sort `students.txt` by score and save the result using pipes and redirection.  
**Related Chapter**: Ch. 5  
**Prerequisite**: M2 complete

**Requirements**:
- Sort students by score in descending order and save to `results/sorted.txt`
- Use a pipe to display only the top 5 students on screen
- Display the total number of students

**Description**

&nbsp;

**Capture** ① `cat results/sorted.txt` result

[Insert captured image]

**Capture** ② Top 5 students output using a pipe

[Insert captured image]

---

## Mission 4 — File and Directory Permission Settings

**Objective**: Set appropriate access permissions on files and directories.  
**Related Chapter**: Ch. 6  
**Prerequisite**: M1–M3 complete

**Requirements**:

| Target | Permission |
|--------|------------|
| `data/students.txt` | Owner read/write only (600) |
| `results/` directory | Owner rwx, group r-x (750) |
| `scripts/` directory | Owner rwx only (700) |

**Description**

&nbsp;

**Capture** — `ls -l ~/score_project/` and `ls -l ~/score_project/data/` results

[Insert captured image]

---

## Mission 5 — File Search and Text Processing

**Objective**: Extract data matching specific conditions and save to a file.  
**Related Chapter**: Ch. 7  
**Prerequisite**: M2 complete

**Requirements**:
- Extract students with a score of 70 or above and save to `results/filtered.txt` *(used in M8)*
- Search for students whose name contains a specific string
- Output the paths of all `.txt` files inside `score_project`

**Description**

&nbsp;

**Capture** ① `cat results/filtered.txt` result

[Insert captured image]

**Capture** ② Name search result

[Insert captured image]

**Capture** ③ `.txt` file search result

[Insert captured image]

---

## Mission 6 — Process Monitoring and Control

**Objective**: Check running processes and create, then terminate, a background process.  
**Related Chapter**: Ch. 8

**Requirements**:
- Check the list of currently running processes
- Start a background process
- Locate the background process and forcefully terminate it
- Confirm that the process has been terminated

**Description** (describe what you observed at each step)

&nbsp;

**Capture** ① Confirmation of background process after starting it

[Insert captured image]

**Capture** ② Confirmation after terminating the process

[Insert captured image]

---

## Mission 7 — Archive and Backup

**Objective**: Create a compressed archive backup of the project folder.  
**Related Chapter**: Ch. 10  
**Prerequisite**: M1–M5 complete

**Requirements**:
- Compress `data/` and `results/` directories and save the archive to `backup/`
- Verify the backup file size
- List the files included in the backup archive

**Description**

&nbsp;

**Capture** ① `ls -lh backup/` — backup file creation confirmed

[Insert captured image]

**Capture** ② File listing inside the backup archive

[Insert captured image]

---

## Mission 8 — Run the Python Script

**Objective**: Run the provided `analyze_scores.py` to output grade statistics from `filtered.txt`.  
**Prerequisite**: M5 complete (`results/filtered.txt` exists)

> Download `analyze_scores.py` from GitHub and save it to the `scripts/` folder.

**Provided Code** (`analyze_scores.py`)

```python
# analyze_scores.py
# Reads filtered.txt created in M5 and prints grade statistics.

import os

filepath = os.path.expanduser('~/score_project/results/filtered.txt')

names = []
scores = []

with open(filepath, 'r') as f:
    for line in f:
        parts = line.strip().split()
        if len(parts) == 2:
            names.append(parts[0])
            scores.append(int(parts[1]))

print("=== Grade Analysis Results ===")
print(f"Number of students : {len(scores)}")
print(f"Highest score      : {max(scores)}")
print(f"Lowest score       : {min(scores)}")
print(f"Average score      : {sum(scores) / len(scores):.1f}")
print()
print("--- Score List ---")
for i in range(len(names)):
    print(f"  {names[i]}: {scores[i]}")
```

**Description**

&nbsp;

**Capture** — `python3 scripts/analyze_scores.py` execution result

[Insert captured image]

---

## Mission 9 — Shell Script Integration and Automation

**Objective**: Automate M5 (filter) → M8 (Python analysis) → M7 (backup) in a single shell script.  
**Related Chapter**: Ch. 11  
**Prerequisite**: M5, M7, M8 complete

**File location**: `~/score_project/scripts/run_all.sh`

**Requirements**:
- Include `#!/bin/bash` on the first line
- Print a progress message at the start of each step
- **Step 1**: Filter students scoring 70 or above from `students.txt` → update `filtered.txt`
- **Step 2**: Call `analyze_scores.py`
- **Step 3**: Create a compressed backup with a filename that includes the current date and time

**Description** (explain the design decisions you made when writing the script)

&nbsp;

**Capture** ① Content of `run_all.sh` (`cat scripts/run_all.sh`)

[Insert captured image]

**Capture** ② Full execution result of `run_all.sh`

[Insert captured image]

---

---

## Rubric (Grading Criteria)

| Mission | Key Evaluation Criteria | Points |
|---------|------------------------|:------:|
| M1. Create Directory Structure | Completeness of folder structure, capture | 5 |
| M2. Create Data File | Format compliance, 15+ students, editor used | 10 |
| M3. Pipes & Redirection | Sorting accuracy, pipe usage | 10 |
| M4. Permission Settings | Correct permission values, ls -l verification | 10 |
| M5. Search & Text Processing | Accuracy of filtered.txt, use of search commands | 15 |
| M6. Process Monitoring | Background process creation and termination, captures | 10 |
| M7. Archive & Backup | Backup file created, file listing verified | 10 |
| M8. Run Python Script | Successful execution, correct output | 15 |
| M9. Shell Script Integration | 3-step integration implemented, script completeness | 15 |
| **Total** | | **100** |
