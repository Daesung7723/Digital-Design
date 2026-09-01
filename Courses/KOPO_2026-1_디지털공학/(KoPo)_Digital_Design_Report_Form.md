# Digital Design — Sequential Logic Circuit Design Report

**Course**: Digital Design  
**Instructor**: Daesung Kim  
**Submission**: Convert to PDF and submit via email

---

## Submission Procedure

| Stage | Content | Deadline | Method |
|-------|---------|----------|--------|
| **Stage 1 (Draft)** | Design specification + Block diagram | **2026-06-14 (Sun)** | Submit via email |
| **Feedback** | Topic confirmation | **2026-06-17 (Tue)** | Reply via email |
| **Stage 2 (Final)** | Complete report | **Before final exam** | Convert to PDF and submit via email |

**Design Requirements**

- Number of states: 8 or fewer
- Flip-flop type: **JK Flip-Flop** (required)
- Circuit design tool: **Logisim Evolution** (required)

---

## Stage 1: Design Specification (Draft Submission)

### Cover Page

| Item | Content |
|------|---------|
| Report Title | |
| Student ID | |
| Name | |
| Submission Date | |

---

### 1. System Overview

Describe the operation of your sequential logic circuit in 2–3 sentences.

> (Example: This circuit is a 4-way intersection traffic light system that sequentially controls signals for the North-South (NS) and East-West (EW) directions. Each direction outputs red, green, and yellow signals that transition based on the current state.)

---

### 2. Block Diagram

Draw a block diagram including all elements listed below and insert it as an image.

**Required elements**:
- Input signals (name + meaning)
- Output signals (name + meaning)
- State block (combinational logic + flip-flops)
- Specify Mealy / Moore type

[Insert image]

---

### 3. Input/Output Variable Definitions

| Variable | Type | Description |
|----------|------|-------------|
| | Input | |
| | Output | |

*Add rows as needed. For systems with multiple outputs (e.g., R·Y·G signals per direction), list each signal separately.*

**Circuit type**: Mealy &nbsp;&nbsp; / &nbsp;&nbsp; Moore &nbsp;&nbsp; (circle one)

**Target number of states**: \_\_\_ (maximum 8)

---

> **For Stage 1, submit only up to this point via email.**  
> After receiving feedback and confirming your topic, proceed to Stage 2.

---

---

## Stage 2: Final Report

### 1. Design Specification (Final)

Revise and supplement your Stage 1 content based on the feedback received.  
(Include: cover page + system overview + final block diagram + I/O variable definitions)

---

### 2. State Diagram

Draw the state diagram and insert it as an image.

**Guidelines**:
- Represent each state as a circle (○)
- Label each transition arrow with input conditions and output
  - Mealy: *input / output* format on each arrow
  - Moore: input condition only on arrows; output shown inside state circle
- Clearly indicate the initial state

[Insert image]

---

### 3. State Table and JK Excitation Table

Fill in the current state, input, next state, output, and J·K input values for each flip-flop.

#### JK Excitation Table Reference

| Current Q | Next Q | J | K |
|:---------:|:------:|:---:|:---:|
| 0 | 0 | 0 | X |
| 0 | 1 | 1 | X |
| 1 | 0 | X | 1 |
| 1 | 1 | X | 0 |

#### State Table

| Current State | Input | Next State | Output | J₂ | K₂ | J₁ | K₁ | J₀ | K₀ |
|:-------------:|:-----:|:----------:|:------:|:--:|:--:|:--:|:--:|:--:|:--:|
| | | | | | | | | | |
| | | | | | | | | | |
| | | | | | | | | | |
| | | | | | | | | | |

*If using only 2 flip-flops, remove the J₂·K₂ columns. (2 FFs: up to 4 states / 3 FFs: up to 8 states)*

---

### 4. Karnaugh Map (Logisim Capture)

Use the Karnaugh map feature in Logisim Evolution to minimize each flip-flop input function and output function. Insert a captured image for each function separately.

**Required captures**: J₁, K₁, J₀, K₀, and each output function

[Insert captured images — one per function]

---

### 5. Logic Circuit Diagram (Logisim)

Insert a captured image of the final circuit designed in Logisim Evolution.

**Checklist before capture**:
- [ ] JK flip-flops used
- [ ] Input pins labeled with variable names
- [ ] Output pins labeled with variable names
- [ ] Entire circuit is visible and readable in a single capture

[Insert captured image]

---

### 6. Simulation Results (Logisim)

Specify an input sequence of your choice and capture the simulation results.

**Minimum 6 clock cycles required**

| Clock | Input | Expected State | Expected Output |
|:-----:|:-----:|:--------------:|:---------------:|
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |
| 5 | | | |
| 6 | | | |

[Insert simulation capture]

---

### 7. Discussion

Write 3–5 sentences for each of the following items.

**① Challenges encountered during the design process**

(Write here)

**② Possible improvements or additional feature suggestions**

(Write here)

---

---

## Rubric (Grading Criteria)

| Item | Evaluation Criteria | Points |
|------|---------------------|:------:|
| 1. Design Specification | Block diagram completeness, I/O variable definitions, Mealy/Moore distinction, clarity of operation description | 10 |
| 2. State Diagram | Appropriate number of states, completeness of transitions, notation accuracy | 20 |
| 3. State Table · JK Excitation Table | Transition accuracy, JK input value accuracy | 20 |
| 4. Karnaugh Map (Logisim capture) | Captures included per function, readability | 5 |
| 5. Logic Circuit Diagram (Logisim) | Circuit implementation accuracy, capture readability, labels present | 25 |
| 6. Simulation Results | Input sequence specified, state transition captured, results consistent | 15 |
| 7. Discussion | Depth and quality of content | 5 |
| **Total** | | **100** |
