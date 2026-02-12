# PL/SQL Cursors I – Day 3

## Implicit Cursors, Explicit Cursors & Cursor Attributes

**Training Material for Freshers**

---

## INTRODUCTION: WHAT IS A CURSOR?

A **cursor** is a pointer or handle to a result set returned by a SQL query. When you run a SELECT that returns multiple rows, the cursor lets you process those rows one at a time inside PL/SQL.

- **Implicit cursor** – Created and managed automatically by Oracle for DML and single-row SELECT
- **Explicit cursor** – Declared and controlled by you for multi-row queries

Without cursors, you can only fetch one row at a time (SELECT INTO). Cursors let you work with many rows in a loop.

---

## LEARNING OBJECTIVES

- Master PL/SQL Cursors I in PL/SQL
- Write efficient procedural code
- Implement proper error handling
- Build reusable code components
- Apply PL/SQL best practices

---

## TOPICS COVERED

### Hour 1: Implicit and Explicit Cursors Introduction (50 min)

---

#### 1) What is a Cursor?

**Theory**

When a SQL statement runs, Oracle allocates a **work area** in memory to hold the query result. A **cursor** is the name of that work area—it gives you access to the rows.

| Cursor Type  | Who Creates It       | Use When                                                 |
| ------------ | -------------------- | -------------------------------------------------------- |
| **Implicit** | Oracle automatically | DML (INSERT, UPDATE, DELETE) or SELECT INTO (single row) |
| **Explicit** | You (in DECLARE)     | SELECT returning multiple rows                           |

**Why use explicit cursors?**

- SELECT INTO returns exactly one row. For 0 or many rows, it raises NO_DATA_FOUND or TOO_MANY_ROWS.
- To process multiple rows, you need an explicit cursor and a loop (FETCH one row at a time).

---

#### 2) Implicit Cursors and Attributes

**Theory**

Oracle creates an **implicit cursor** named **SQL** for every DML statement and single-row SELECT INTO. You don't declare it; it's always available right after the statement runs.

**Implicit cursor attributes** (use the prefix `SQL%`):

| Attribute      | Type    | Description                                                      |
| -------------- | ------- | ---------------------------------------------------------------- |
| `SQL%FOUND`    | BOOLEAN | TRUE if the last statement affected or returned at least one row |
| `SQL%NOTFOUND` | BOOLEAN | TRUE if the last statement affected or returned no rows          |
| `SQL%ROWCOUNT` | NUMBER  | Number of rows affected or returned by the last statement        |
| `SQL%ISOPEN`   | BOOLEAN | Always FALSE for implicit cursors (closed immediately after use) |

**Important:** Check these attributes **immediately** after the SQL statement. The next SQL statement will overwrite them.

**Example 1 – Implicit cursor with SELECT INTO:**

```sql
DECLARE
    v_name   VARCHAR2(100);
    v_salary NUMBER;
BEGIN
    SELECT emp_name, salary INTO v_name, v_salary
    FROM employees WHERE emp_id = 101;

    IF SQL%FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Found: ' || v_name || ', ' || v_salary);
    END IF;
    DBMS_OUTPUT.PUT_LINE('Rows returned: ' || SQL%ROWCOUNT);
END;
/
```

**Example 2 – Implicit cursor with UPDATE/DELETE:**

```sql
DECLARE
    v_rows_updated NUMBER;
BEGIN
    UPDATE employees SET salary = salary * 1.1 WHERE department_id = 10;
    v_rows_updated := SQL%ROWCOUNT;

    IF SQL%FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Updated ' || v_rows_updated || ' rows');
    ELSIF SQL%NOTFOUND THEN
        DBMS_OUTPUT.PUT_LINE('No rows updated');
    END IF;
    COMMIT;
END;
/
```

**Example 3 – Handling INSERT:**

```sql
BEGIN
    INSERT INTO employees (emp_id, emp_name, salary) VALUES (999, 'New Emp', 50000);
    IF SQL%ROWCOUNT > 0 THEN
        DBMS_OUTPUT.PUT_LINE('Inserted ' || SQL%ROWCOUNT || ' row(s)');
    END IF;
    COMMIT;
END;
/
```

---

#### 3) Explicit Cursor Lifecycle

**Theory**

An explicit cursor has four stages:

| Step       | Action                      | Purpose                          |
| ---------- | --------------------------- | -------------------------------- |
| 1. DECLARE | Define cursor with a SELECT | Associate query with cursor name |
| 2. OPEN    | Execute the query           | Fill result set in memory        |
| 3. FETCH   | Retrieve rows one by one    | Get each row into variables      |
| 4. CLOSE   | Release resources           | Free memory when done            |

**Flow:**

```
DECLARE cursor  →  OPEN cursor  →  LOOP: FETCH into vars  →  EXIT WHEN %NOTFOUND
                                       ↓
                               Process row
                                       ↓
                               (loop back to FETCH)
                                       ↓
                               CLOSE cursor
```

**Basic syntax:**

```sql
DECLARE
    CURSOR c_emp IS SELECT emp_id, emp_name, salary FROM employees;
    v_id     employees.emp_id%TYPE;
    v_name   employees.emp_name%TYPE;
    v_sal    employees.salary%TYPE;
BEGIN
    OPEN c_emp;
    LOOP
        FETCH c_emp INTO v_id, v_name, v_sal;
        EXIT WHEN c_emp%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_id || ' - ' || v_name || ' - ' || v_sal);
    END LOOP;
    CLOSE c_emp;
END;
/
```

---

#### Hands-on: Basic Exercises

- Write a block that updates a table and prints SQL%ROWCOUNT
- Declare an explicit cursor, OPEN, FETCH in a loop, CLOSE
- Print each employee name from a table using a cursor

---

### Hour 2: Advanced Implicit and Explicit Cursors (60 min)

---

#### 4) DECLARE, OPEN, FETCH, CLOSE – In Detail

**DECLARE**

Define the cursor with a SELECT. No INTO clause here—that comes in FETCH.

```sql
CURSOR c_name IS
    SELECT col1, col2 FROM table_name WHERE condition;
```

**OPEN**

Executes the query and prepares the result set. Rows are not fetched yet.

```sql
OPEN c_name;
```

**FETCH**

Retrieves one row from the result set into variables. Each FETCH gets the next row.

```sql
FETCH c_name INTO var1, var2;
```

**CLOSE**

Releases the cursor and frees memory. Always close when done. If you don't, you may hit "maximum open cursors" limits.

```sql
CLOSE c_name;
```

**Example – Complete lifecycle:**

```sql
DECLARE
    CURSOR c_dept IS SELECT department_id, department_name FROM departments;
    v_dept_id   departments.department_id%TYPE;
    v_dept_name departments.department_name%TYPE;
BEGIN
    OPEN c_dept;
    LOOP
        FETCH c_dept INTO v_dept_id, v_dept_name;
        EXIT WHEN c_dept%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_dept_id || ': ' || v_dept_name);
    END LOOP;
    CLOSE c_dept;
END;
/
```

---

#### 5) Cursor Attributes: %FOUND, %NOTFOUND, %ROWCOUNT, %ISOPEN

**Theory**

Explicit cursors have their own attributes. Use the cursor name as prefix (e.g., `c_emp%FOUND`).

| Attribute         | Type    | Description                            |
| ----------------- | ------- | -------------------------------------- |
| `cursor%FOUND`    | BOOLEAN | TRUE if the last FETCH returned a row  |
| `cursor%NOTFOUND` | BOOLEAN | TRUE if the last FETCH returned no row |
| `cursor%ROWCOUNT` | NUMBER  | Number of rows fetched so far          |
| `cursor%ISOPEN`   | BOOLEAN | TRUE if cursor is open                 |

**When to check:**

- After each **FETCH**: use `%NOTFOUND` to exit the loop when no more rows
- After **OPEN**: `%ISOPEN` is TRUE
- After **CLOSE**: `%ISOPEN` is FALSE
- `%ROWCOUNT` increases with each successful FETCH

**Example – Using all attributes:**

```sql
DECLARE
    CURSOR c_emp IS SELECT emp_name FROM employees WHERE department_id = 10;
    v_name employees.emp_name%TYPE;
BEGIN
    IF NOT c_emp%ISOPEN THEN
        OPEN c_emp;
    END IF;

    LOOP
        FETCH c_emp INTO v_name;
        EXIT WHEN c_emp%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE('Row ' || c_emp%ROWCOUNT || ': ' || v_name);
    END LOOP;

    DBMS_OUTPUT.PUT_LINE('Total fetched: ' || c_emp%ROWCOUNT);
    CLOSE c_emp;
    DBMS_OUTPUT.PUT_LINE('Cursor open? ' || CASE WHEN c_emp%ISOPEN THEN 'Yes' ELSE 'No' END);
END;
/
```

---

#### 6) Processing Multiple Rows – EXIT WHEN cursor%NOTFOUND

**Theory**

The standard pattern for processing all rows:

1. OPEN the cursor
2. Loop: FETCH → check %NOTFOUND → if true, EXIT → else process row
3. CLOSE the cursor

**Critical:** Always check `%NOTFOUND` **immediately after FETCH**. If you process the row before checking, you might process garbage/null data on the last (failed) fetch.

**Correct order:**

```sql
FETCH c_emp INTO v_id, v_name;
EXIT WHEN c_emp%NOTFOUND;   -- Check BEFORE processing
-- Process v_id, v_name here
```

**Example – EXIT WHEN cursor%NOTFOUND:**

```sql
DECLARE
    CURSOR c_products IS SELECT product_id, product_name, price FROM products;
    v_id    products.product_id%TYPE;
    v_name  products.product_name%TYPE;
    v_price products.price%TYPE;
BEGIN
    OPEN c_products;
    LOOP
        FETCH c_products INTO v_id, v_name, v_price;
        EXIT WHEN c_products%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_id || ' | ' || v_name || ' | ' || v_price);
    END LOOP;
    CLOSE c_products;
END;
/
```

---

#### 7) Cursor FOR Loop (Simpler Alternative)

**Theory**

The **cursor FOR loop** automatically OPENs, FETCHes, and CLOSEs the cursor. You don't write OPEN, FETCH, CLOSE, or EXIT WHEN. Oracle handles it.

```sql
FOR record_name IN cursor_name LOOP
    -- record_name.column1, record_name.column2
END LOOP;
```

**Example – Cursor FOR loop:**

```sql
DECLARE
    CURSOR c_emp IS SELECT emp_id, emp_name, salary FROM employees;
BEGIN
    FOR emp_rec IN c_emp LOOP
        DBMS_OUTPUT.PUT_LINE(emp_rec.emp_id || ' - ' || emp_rec.emp_name || ' - ' || emp_rec.salary);
    END LOOP;
END;
/
```

**Inline cursor (no separate DECLARE):**

```sql
BEGIN
    FOR emp_rec IN (SELECT emp_id, emp_name FROM employees WHERE department_id = 10) LOOP
        DBMS_OUTPUT.PUT_LINE(emp_rec.emp_id || ': ' || emp_rec.emp_name);
    END LOOP;
END;
/
```

---

#### Hands-on: Complex Scenarios

- Process rows with a WHILE loop and %FOUND
- Use cursor FOR loop with parameters (Day 3+ topic)
- Combine implicit and explicit cursors in one block

---

### Hour 3: Practice & Application (50 min)

- Comprehensive coding exercises (see below)
- Debugging: cursor not closed, wrong attribute, processing after %NOTFOUND
- Code review: when to use implicit vs explicit, cursor FOR vs manual loop
- Real-world: report generation, batch updates based on cursor
- Best practices: always CLOSE, use cursor FOR when possible, handle %NOTFOUND

---

## HANDS-ON EXERCISES

### Exercise 1: Basic Implicit Cursor Example

Use an implicit cursor (UPDATE or DELETE) and print how many rows were affected using SQL%ROWCOUNT.

<details>
<summary>Solution</summary>

```sql
BEGIN
    UPDATE employees SET salary = salary WHERE department_id = 10;  -- No change, but runs
    DBMS_OUTPUT.PUT_LINE('Rows affected: ' || SQL%ROWCOUNT);
    ROLLBACK;  -- Undo for safety
END;
/
```

</details>

---

### Exercise 2: PL/SQL Cursors with Parameters (Explicit Cursor)

Create an explicit cursor that accepts a department ID and fetches only employees in that department. Use a parameter in the cursor definition.

<details>
<summary>Solution</summary>

```sql
DECLARE
    CURSOR c_emp(p_dept_id NUMBER) IS
        SELECT emp_id, emp_name FROM employees WHERE department_id = p_dept_id;
    v_id   employees.emp_id%TYPE;
    v_name employees.emp_name%TYPE;
BEGIN
    OPEN c_emp(10);  -- Pass department 10
    LOOP
        FETCH c_emp INTO v_id, v_name;
        EXIT WHEN c_emp%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_id || ' - ' || v_name);
    END LOOP;
    CLOSE c_emp;
END;
/
```

</details>

---

### Exercise 3: Error Handling Implementation

Write a block that opens a cursor, fetches rows, and in the EXCEPTION section ensures the cursor is closed (use %ISOPEN) before re-raising or handling the error.

<details>
<summary>Solution</summary>

```sql
DECLARE
    CURSOR c_emp IS SELECT emp_id, emp_name FROM employees;
    v_id   employees.emp_id%TYPE;
    v_name employees.emp_name%TYPE;
BEGIN
    OPEN c_emp;
    LOOP
        FETCH c_emp INTO v_id, v_name;
        EXIT WHEN c_emp%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_id || ' - ' || v_name);
    END LOOP;
    CLOSE c_emp;
EXCEPTION
    WHEN OTHERS THEN
        IF c_emp%ISOPEN THEN
            CLOSE c_emp;
        END IF;
        RAISE;
END;
/
```

</details>

---

### Exercise 4: Complex Logic Building

Use a cursor to fetch employees, and for each row, calculate a bonus (e.g., 10% of salary) and print emp_name, salary, and bonus.

<details>
<summary>Solution</summary>

```sql
DECLARE
    CURSOR c_emp IS SELECT emp_name, salary FROM employees;
    v_name   employees.emp_name%TYPE;
    v_salary employees.salary%TYPE;
    v_bonus  NUMBER;
BEGIN
    FOR rec IN c_emp LOOP
        v_bonus := rec.salary * 0.1;
        DBMS_OUTPUT.PUT_LINE(rec.emp_name || ' | ' || rec.salary || ' | Bonus: ' || v_bonus);
    END LOOP;
END;
/
```

</details>

---

### Exercise 5: Integration with SQL Queries

Fetch department names and the count of employees in each department. Use a cursor over a query that joins departments and employees (or uses a subquery for count).

<details>
<summary>Solution</summary>

```sql
DECLARE
    CURSOR c_dept IS
        SELECT d.department_name, COUNT(e.emp_id) AS emp_count
        FROM departments d
        LEFT JOIN employees e ON d.department_id = e.department_id
        GROUP BY d.department_name;
BEGIN
    FOR rec IN c_dept LOOP
        DBMS_OUTPUT.PUT_LINE(rec.department_name || ': ' || rec.emp_count || ' employees');
    END LOOP;
END;
/
```

</details>

---

### Exercise 6: Reusable Code Component

Write an anonymous block that could be turned into a procedure: it accepts a department_id (via variable), opens a parameterized cursor, and prints all employee names in that department.

<details>
<summary>Solution</summary>

```sql
DECLARE
    p_dept_id NUMBER := 10;  -- Simulated parameter
    CURSOR c_emp(p_did NUMBER) IS SELECT emp_name FROM employees WHERE department_id = p_did;
BEGIN
    FOR rec IN c_emp(p_dept_id) LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_name);
    END LOOP;
END;
/
```

</details>

---

### Exercise 7: Performance Optimization

Compare two approaches: (1) multiple SELECT INTO in a loop vs (2) one explicit cursor. Explain why the cursor is more efficient (single parse, less round-trips).

**Theory:** SELECT INTO in a loop re-pares and executes the query each time. A cursor parses once, opens once, and fetches many times—reducing overhead.

---

### Exercise 8: Debugging Practice

Given a block with a bug (e.g., missing EXIT WHEN, cursor not closed, wrong attribute), identify and fix it. Use DBMS_OUTPUT to print %ROWCOUNT and %NOTFOUND at key points.

---

### Exercise 9: Advanced Pattern – Implicit + Explicit

Use an implicit cursor (UPDATE) to give a 5% raise to employees in department 20. Then use an explicit cursor to list those employees and their new salaries.

<details>
<summary>Solution</summary>

```sql
DECLARE
    CURSOR c_emp IS SELECT emp_name, salary FROM employees WHERE department_id = 20;
BEGIN
    UPDATE employees SET salary = salary * 1.05 WHERE department_id = 20;
    DBMS_OUTPUT.PUT_LINE('Updated ' || SQL%ROWCOUNT || ' rows');

    FOR rec IN c_emp LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_name || ' - ' || rec.salary);
    END LOOP;
    COMMIT;
END;
/
```

</details>

---

### Exercise 10: Complete PL/SQL Cursors I Project

Build a block that:

1. Declares an explicit cursor for employees with salary > 50000
2. Uses OPEN, FETCH, EXIT WHEN %NOTFOUND, CLOSE
3. Prints emp_id, emp_name, salary, and a "High earner" label
4. Handles errors and ensures cursor is closed
5. Prints total count using %ROWCOUNT before CLOSE

<details>
<summary>Solution</summary>

```sql
DECLARE
    CURSOR c_high IS SELECT emp_id, emp_name, salary FROM employees WHERE salary > 50000;
    v_id     employees.emp_id%TYPE;
    v_name   employees.emp_name%TYPE;
    v_salary employees.salary%TYPE;
BEGIN
    OPEN c_high;
    LOOP
        FETCH c_high INTO v_id, v_name, v_salary;
        EXIT WHEN c_high%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_id || ' | ' || v_name || ' | ' || v_salary || ' | High earner');
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('Total high earners: ' || c_high%ROWCOUNT);
    CLOSE c_high;
EXCEPTION
    WHEN OTHERS THEN
        IF c_high%ISOPEN THEN CLOSE c_high; END IF;
        RAISE;
END;
/
```

</details>

---

## RESOURCES PROVIDED

- **PL/SQL Cursors I Guide** – This document
- **Code Examples and Patterns** – Solutions in exercises above
- **Best Practices**
  - Always CLOSE explicit cursors
  - Use cursor FOR loop when you don't need fine-grained control
  - Check SQL%ROWCOUNT immediately after DML
  - Handle %NOTFOUND right after FETCH, before processing
- **Debugging Tips**
  - Add DBMS_OUTPUT for %ROWCOUNT, %FOUND, %NOTFOUND
  - Ensure EXCEPTION handler closes cursor if open

---

## HOMEWORK / PREPARATION

- [ ] Practice 15 cursor programs (implicit and explicit)
- [ ] Build a code library with cursor examples
- [ ] Complete all 10 hands-on exercises
- [ ] Review and refactor code for clarity
- [ ] Document when to use implicit vs explicit cursors

---

## QUICK REFERENCE

```
Implicit:     SQL%FOUND, SQL%NOTFOUND, SQL%ROWCOUNT
Explicit:     DECLARE cursor → OPEN → FETCH → EXIT WHEN %NOTFOUND → CLOSE
Attributes:   cursor%FOUND, cursor%NOTFOUND, cursor%ROWCOUNT, cursor%ISOPEN
Cursor FOR:   FOR rec IN cursor_name LOOP ... END LOOP;
Parameter:    CURSOR c(p_id NUMBER) IS SELECT ... WHERE id = p_id;
```

---

_Day 3 – PL/SQL Cursors I: Implicit, Explicit & Cursor Attributes_
