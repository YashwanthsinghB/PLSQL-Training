# PL/SQL Procedures – Day 5 Quiz

## Creating Procedures, Parameters (IN, OUT, IN OUT)

**Instructions:** Answer all 15 questions. Time: 15–20 minutes.

---

### Multiple Choice

**Q1.** A stored procedure is:

- A) An anonymous block that runs once
- B) A named PL/SQL block stored in the database, callable by name
- C) A SQL query
- D) A table

---

**Q2.** What is the default parameter mode if you don't specify one?

- A) OUT
- B) IN OUT
- C) IN
- D) REF

---

**Q3.** An IN parameter:

- A) Returns a value to the caller
- B) Passes a value into the procedure; the procedure cannot change it
- C) Must be assigned a value inside the procedure
- D) Can only be used with literals

---

**Q4.** An OUT parameter:

- A) Cannot be modified inside the procedure
- B) Must receive a value assigned by the procedure before it ends
- C) Passes a value in only
- D) Uses the default value from the caller

---

**Q5.** An IN OUT parameter:

- A) Can only be read inside the procedure
- B) Passes a value in and returns a modified value out
- C) Cannot use variables from the caller
- D) Is the same as OUT

---

**Q6.** Which keyword recreates a procedure if it already exists?

- A) REPLACE
- B) OR REPLACE
- C) CREATE NEW
- D) RECREATE

---

**Q7.** Named notation is used when calling a procedure as:

- A) `proc_name(value1, value2)`
- B) `proc_name(p_param1 => value1, p_param2 => value2)`
- C) `proc_name(@value1, @value2)`
- D) `CALL proc_name(value1, value2)`

---

**Q8.** Parameters with default values must appear:

- A) First in the parameter list
- B) After parameters without defaults
- C) Only at the end
- D) Defaults are not allowed in procedures

---

**Q9.** How do you call a procedure from a PL/SQL block?

- A) EXECUTE procedure_name;
- B) CALL procedure_name;
- C) procedure_name; (as a statement)
- D) RUN procedure_name;

---

**Q10.** RAISE_APPLICATION_ERROR is used to:

- A) Ignore an error
- B) Raise a user-defined error with a custom message and error code
- C) Close a cursor
- D) Commit a transaction

---

### True or False

**Q11.** A procedure can have both IN and OUT parameters in the same parameter list.

- True / False

---

**Q12.** The caller must pass a variable (not a literal) to receive the value of an OUT parameter.

- True / False

---

**Q13.** In mixed notation, positional arguments must come before named arguments.

- True / False

---

### Fill in the Blank / Short Answer

**Q14.** Complete the procedure signature for a procedure that takes an employee ID (IN) and returns the name (OUT) and salary (OUT):

```sql
CREATE PROCEDURE get_emp(
    p_emp_id IN _____________,
    p_name   _____________ VARCHAR2,
    p_salary _____________ NUMBER
)
```

---

**Q15.** List the three parameter modes and briefly describe each:

1. ******\_\_\_****** – ******\_\_\_******
2. ******\_\_\_****** – ******\_\_\_******
3. ******\_\_\_****** – ******\_\_\_******

---

## Answer Key

| Q#  | Answer                                                                                                                  |
| --- | ----------------------------------------------------------------------------------------------------------------------- |
| 1   | B                                                                                                                       |
| 2   | C                                                                                                                       |
| 3   | B                                                                                                                       |
| 4   | B                                                                                                                       |
| 5   | B                                                                                                                       |
| 6   | B                                                                                                                       |
| 7   | B                                                                                                                       |
| 8   | B                                                                                                                       |
| 9   | C                                                                                                                       |
| 10  | B                                                                                                                       |
| 11  | True                                                                                                                    |
| 12  | True                                                                                                                    |
| 13  | True                                                                                                                    |
| 14  | `NUMBER`, `OUT`, `OUT`                                                                                                  |
| 15  | 1. IN – Pass value in, read-only 2. OUT – Return value, procedure assigns 3. IN OUT – Pass in and return modified value |

---

_Use this quiz to assess understanding at the end of Day 5._
