# Artifacts Submission for Manuscript 54 [MODELS22]

**Paper**: "Proving correctness for SQL implementations of OCL constraints"

## Table of Contents

- [Requirements](#requirements)
- [Experiments](#experiments)
    - [Experiment 1](#experiment-1)
    - [Experiment 2](#experiment-2)
    - [Experiment 3](#experiment-3)
    - [Experiment 4](#experiment-4)
    - [Experiment 5](#experiment-5)
    - [Experiment 6](#experiment-6)
    - [Experiment 7](#experiment-7)
- [How to run the artifacts](#how-to-run-the-artifacts)
    - [CVC4](#cvc4)
    - [Z3](#z3)

## Requirements
The following artifacts are MSFOL theories written in SMT-LIB2 language.
Therefore, the following SMT solvers are recommended to be installed in advance in order to replicate the experiments:
- [CVC4](https://github.com/CVC4/CVC4-archived)
- [Z3](https://github.com/Z3Prover/z3)

## Experiments
In this manuscript, we provide a number of examples to prove our model-based methodology of proving the correctness for SQL implementations of OCL constraints.
Each experiment consists of different MSFOL theory files for proving this correctness, namely:
- C1: is the MSFOL theory used to prove that the SQL implementation, when executed, contains only one row.
- C2: is the MSFOL theory used to prove that the SQL implementation is executed to `TRUE` _if_ the OCL constraint is evaluated to `true`.
- C3: is the MSFOL theory used to prove that the SQL implementation is executed to `TRUE` _only if_ the OCL constraint is evaluted to `true`. 

### Experiment 1:
<details>
  <summary>Click to expand!</summary>

    - There is no additional context.
    - No assumptions provided.
    - OCL expression: true
    - SQL statement : SELECT TRUE
</details>

### Experiment 2:
<details>
  <summary>Click to expand!</summary>
    
    - Context: 
        - `caller` is a `Lecturer`.
    - No assumptions provided.
    - OCL expression: caller.students->isEmpty()
    - SQL statement : SELECT NOT EXISTS (SELECT students 
                      FROM Enrolment 
                      WHERE lecturers = caller)
</details>

### Experiment 3:
<details>
  <summary>Click to expand!</summary>
    
    - Context: 
        - `self` is a `Student`.
    - No assumptions provided.
    - OCL expression: self.age >= 18
    - SQL statement : SELECT age >= 18 FROM Student 
                      WHERE Student_id = self
</details>

### Experiment 4:
<details>
  <summary>Click to expand!</summary>
    
    - There is no additional context.
    - Assumptions:
        - There is no student with undefined age.
        - There is no lecturer with undefined age.
    - OCL expression: Student.allInstances()->forAll(s|s.lecturers->forAll(l|l.age > s.age))
    - SQL statement : SELECT NOT EXISTS (SELECT 1 
                      FROM (SELECT s.age, e.lecturers 
                            FROM Student s 
                            JOIN Enrolment e 
                            ON e.students = s.Student_id) AS TEMP
                      JOIN Lecturer l 
                      WHERE TEMP.age >= l.age AND l.Lecturer_id = TEMP.lecturers)
</details>

### Experiment 5:
<details>
  <summary>Click to expand!</summary>
    
    - Context: 
        - `self` is a `Student`,
        - `user` is a `String`.
    - Assumptions:
        - There is no student with undefined age.
        - `user` is a non-null string.
    - OCL expression: self.name = user
    - SQL statement : SELECT (SELECT name 
                      FROM Student 
                      WHERE Student_id = self) = user
</details>

**Note**: For example 5, there is an additional theory `C4` to prove the corresponding subselect returns a single row (as described in the manusciprt).


### Experiment 6:
<details>
  <summary>Click to expand!</summary>
    
    - Context: 
        - `self` is a `Student`,
        - `user` is a `String`.
    - Assumptions:
        - There is no student with undefined age.
        - `user` is a non-null string.
    - OCL expression: self.name = user
    - SQL statement : SELECT name = user FROM Student WHERE Student_id = self
</details>

### Experiment 7:
<details>
  <summary>Click to expand!</summary>
    
    - Context: 
        - `self` is a `Student`,
        - `user` is a `String`.
    - No assumptions provided.
    - OCL expression: self.name = user 
    - SQL statement : SELECT CASE WHEN name IS NULL THEN user IS NULL
                            ELSE CASE WHEN user IS NULL THEN FALSE 
                                      ELSE name = user END END
                     FROM Student
                     WHERE Student_id = self 
</details>


## How to run the artifacts

### CVC4
For CVC4 users, the following command would prove the theory:
```
cvc4.exe -finite-model-find <THEORY_NAME>
```

### Z3
For Z3 users, the following command would prove the theory:
```
z3 <THEORY_NAME>
```
**Note**: There may be some warning being output by the solver but it does no harm so please ignore them.
