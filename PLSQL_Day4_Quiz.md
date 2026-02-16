# PL/SQL Cursors II – Day 4 Quiz

## Cursor FOR Loops, Parameterized Cursors & REF Cursors

**Instructions:** Answer all 15 questions. Time: 15–20 minutes.

---

### Multiple Choice

**Q1.** In a cursor FOR loop, who handles OPEN, FETCH, and CLOSE?

- A) The developer must write them
- B) Oracle automatically
- C) The DBA
- D) They are not needed

---

**Q2.** In a cursor FOR loop, the loop variable (e.g., `emp_rec`) is:

- A) Read-write
- B) Read-only
- C) Optional
- D) A cursor

---

**Q3.** Which syntax uses an inline subquery in a cursor FOR loop?

- A) `FOR rec IN c_emp LOOP`
- B) `FOR rec IN (SELECT ... FROM table) LOOP`
- C) `FOR rec IN OPEN c_emp LOOP`
- D) `FOR rec IN cursor_name LOOP`

---

**Q4.** A parameterized cursor allows you to:

- A) Return multiple result sets
- B) Pass values into the cursor so one definition works for different scenarios
- C) Use dynamic SQL only
- D) Skip the OPEN step

---

**Q5.** How do you pass a parameter when opening a parameterized cursor?

- A) `OPEN c_emp PARAMETER 10`
- B) `OPEN c_emp(10)`
- C) `OPEN c_emp WITH 10`
- D) `OPEN c_emp SET 10`

---

**Q6.** What is SYS_REFCURSOR?

- A) A static cursor type
- B) A built-in weakly typed REF cursor type
- C) A table type
- D) A procedure

---

**Q7.** REF cursors differ from static cursors because:

- A) REF cursors cannot be closed
- B) The query can be determined at runtime
- C) REF cursors only work with one row
- D) They require no OPEN

---

**Q8.** Which statement associates a REF cursor with a query and executes it?

- A) FETCH FOR
- B) OPEN FOR
- C) EXECUTE FOR
- D) RUN FOR

---

**Q9.** When using dynamic SQL with OPEN FOR, you should use bind variables (USING) to:

- A) Make the query run faster
- B) Avoid SQL injection and improve performance
- C) Force recompilation
- D) Create temporary tables

---

**Q10.** In a cursor FOR loop with a parameterized cursor, where do you pass the parameter?

- A) In the DECLARE section
- B) When declaring the cursor
- C) In the FOR clause: `FOR rec IN c_emp(10) LOOP`
- D) After END LOOP

---

### True or False

**Q11.** A cursor FOR loop with an inline subquery does not require a separate CURSOR declaration in the DECLARE section.

- True / False

---

**Q12.** REF cursors must always be closed explicitly; Oracle does not close them automatically.

- True / False

---

**Q13.** You can use a cursor FOR loop with a REF cursor after opening it with OPEN FOR.

- True / False

---

### Fill in the Blank / Short Answer

**Q14.** Complete the parameterized cursor definition:

```sql
CURSOR c_emp(_______________) IS
    SELECT emp_name FROM employees WHERE department_id = p_dept_id;
```

---

**Q15.** List three benefits of using a cursor FOR loop instead of manual OPEN/FETCH/CLOSE:

1. ***
2. ***
3. ***
