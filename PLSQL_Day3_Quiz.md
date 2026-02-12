# PL/SQL Cursors I – Day 3 Quiz

## Implicit Cursors, Explicit Cursors & Cursor Attributes

**Instructions:** Answer all 15 questions. Time: 15–20 minutes.

---

### Multiple Choice

**Q1.** What is a cursor?

- A) A variable that stores a single value
- B) A pointer to a result set returned by a SQL query
- C) A type of loop
- D) A table in the database

---

**Q2.** Which cursor is created automatically by Oracle for DML and SELECT INTO?

- A) Explicit cursor
- B) Named cursor
- C) Implicit cursor
- D) Dynamic cursor

---

**Q3.** What is the name of the implicit cursor in PL/SQL?

- A) IMPLICIT
- B) CURSOR
- C) SQL
- D) DEFAULT

---

**Q4.** `SQL%ROWCOUNT` returns:

- A) The total number of rows in a table
- B) The number of rows affected or returned by the last SQL statement
- C) The number of open cursors
- D) The current row number in a loop

---

**Q5.** For an implicit cursor, `SQL%ISOPEN` is always:

- A) TRUE
- B) FALSE
- C) NULL
- D) 0

---

**Q6.** What are the four stages of an explicit cursor lifecycle?

- A) CREATE, OPEN, FETCH, DESTROY
- B) DECLARE, OPEN, FETCH, CLOSE
- C) DEFINE, RUN, READ, END
- D) INIT, EXECUTE, LOOP, STOP

---

**Q7.** When should you check `cursor%NOTFOUND`?

- A) Before OPEN
- B) Before FETCH
- C) Immediately after FETCH
- D) After CLOSE

---

**Q8.** What does `EXIT WHEN cursor%NOTFOUND` do?

- A) Exits when a row is found
- B) Exits when no more rows are available
- C) Exits when the cursor is closed
- D) Exits when an error occurs

---

**Q9.** In a cursor FOR loop, who handles OPEN, FETCH, and CLOSE?

- A) The developer
- B) Oracle automatically
- C) The DBA
- D) No one; you must do it manually

---

**Q10.** Which attribute tells you how many rows have been fetched so far from an explicit cursor?

- A) %FOUND
- B) %NOTFOUND
- C) %ROWCOUNT
- D) %ISOPEN

---

### True or False

**Q11.** SELECT INTO can return multiple rows without raising an error.

- True / False

---

**Q12.** You must always CLOSE an explicit cursor when done to free resources.

- True / False

---

**Q13.** Implicit cursor attributes (SQL%FOUND, etc.) should be checked immediately after the SQL statement, before any other SQL runs.

- True / False

---

### Fill in the Blank / Short Answer

**Q14.** Complete the cursor loop pattern:

```sql
OPEN c_emp;
LOOP
    FETCH c_emp INTO v_id, v_name;
    EXIT WHEN _______________;
    -- Process row
END LOOP;
CLOSE c_emp;
```

---

**Q15.** List the four implicit cursor attributes and what each one indicates:

1. ******\_\_\_****** – ******\_\_\_******
2. ******\_\_\_****** – ******\_\_\_******
3. ******\_\_\_****** – ******\_\_\_******
4. ******\_\_\_****** – ******\_\_\_******

---

## Answer Key

| Q#  | Answer                                                                                                                                                                                 |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | B                                                                                                                                                                                      |
| 2   | C                                                                                                                                                                                      |
| 3   | C                                                                                                                                                                                      |
| 4   | B                                                                                                                                                                                      |
| 5   | B                                                                                                                                                                                      |
| 6   | B                                                                                                                                                                                      |
| 7   | C                                                                                                                                                                                      |
| 8   | B                                                                                                                                                                                      |
| 9   | B                                                                                                                                                                                      |
| 10  | C                                                                                                                                                                                      |
| 11  | False                                                                                                                                                                                  |
| 12  | True                                                                                                                                                                                   |
| 13  | True                                                                                                                                                                                   |
| 14  | `c_emp%NOTFOUND`                                                                                                                                                                       |
| 15  | 1. SQL%FOUND – TRUE if statement affected/returned rows 2. SQL%NOTFOUND – TRUE if no rows 3. SQL%ROWCOUNT – Number of rows affected/returned 4. SQL%ISOPEN – Always FALSE for implicit |

---

_Use this quiz to assess understanding at the end of Day 3._
