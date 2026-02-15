# PL/SQL Cursors II – Day 4

## Cursor FOR Loops, Parameterized Cursors & REF Cursors

**Training Material for Freshers**

---

## INTRODUCTION: ADVANCED CURSOR TECHNIQUES

Day 3 introduced basic explicit cursors with manual OPEN, FETCH, CLOSE. Day 4 covers:

1. **Cursor FOR loops** – Simpler syntax; Oracle handles OPEN, FETCH, CLOSE automatically
2. **Parameterized cursors** – Pass values into a cursor so one definition serves multiple scenarios
3. **REF cursors** – Dynamic cursors whose query can change at runtime; useful for flexible, reusable code

These techniques reduce code, avoid common errors, and support more dynamic applications.

---

## LEARNING OBJECTIVES

- Master PL/SQL Cursors II in PL/SQL
- Write efficient procedural code
- Implement proper error handling
- Build reusable code components
- Apply PL/SQL best practices

---

## TOPICS COVERED

### Hour 1: Advanced Cursor Techniques Introduction (50 min)

---

#### 1) Cursor FOR Loop Syntax

**Theory**

A **cursor FOR loop** combines:

- Declaring a cursor (or using an inline subquery)
- Opening it
- Fetching each row into an implicit record variable
- Closing it when done

You write a simple loop; Oracle handles OPEN, FETCH, EXIT WHEN %NOTFOUND, and CLOSE.

**Benefits:**

- Less code and fewer mistakes (no manual OPEN/CLOSE)
- Automatic record variable (no need to declare FETCH variables)
- Cursor always closed, even on exception
- Cleaner, more readable logic

**Syntax (with named cursor):**

```sql
FOR record_name IN cursor_name LOOP
    -- Use record_name.column1, record_name.column2, etc.
END LOOP;
```

**Syntax (with inline subquery):**

```sql
FOR record_name IN (SELECT col1, col2 FROM table WHERE condition) LOOP
    -- Use record_name.column1, record_name.column2
END LOOP;
```

**Example 1 – Cursor FOR loop with named cursor:**

```sql
DECLARE
    CURSOR c_emp IS
        SELECT emp_id, emp_name, salary FROM employees WHERE department_id = 10;
BEGIN
    FOR emp_rec IN c_emp LOOP
        DBMS_OUTPUT.PUT_LINE(emp_rec.emp_id || ' | ' || emp_rec.emp_name || ' | ' || emp_rec.salary);
    END LOOP;
END;
/
```

**Example 2 – Cursor FOR loop with inline subquery:**

```sql
BEGIN
    FOR emp_rec IN (SELECT emp_name, salary FROM employees WHERE salary > 50000) LOOP
        DBMS_OUTPUT.PUT_LINE(emp_rec.emp_name || ' earns ' || emp_rec.salary);
    END LOOP;
END;
/
```

**Example 3 – No separate DECLARE section needed:**

```sql
BEGIN
    FOR dept_rec IN (SELECT department_id, department_name FROM departments ORDER BY 1) LOOP
        DBMS_OUTPUT.PUT_LINE('Dept ' || dept_rec.department_id || ': ' || dept_rec.department_name);
    END LOOP;
END;
/
```

---

#### 2) Automatic OPEN, FETCH, CLOSE

**Theory**

| Step  | Manual Cursor                 | Cursor FOR Loop                   |
| ----- | ----------------------------- | --------------------------------- |
| OPEN  | You write OPEN                | Oracle opens implicitly           |
| FETCH | You write FETCH INTO          | Oracle fetches into loop variable |
| EXIT  | You write EXIT WHEN %NOTFOUND | Oracle handles it                 |
| CLOSE | You write CLOSE               | Oracle closes implicitly          |

The loop variable (e.g., `emp_rec`) is **read-only** and exists only inside the loop. Column names become attributes: `emp_rec.emp_name`, `emp_rec.salary`.

**Scope:** The record is visible only inside the loop body. After END LOOP, it is undefined.

---

#### 3) Parameterized Cursors

**Theory**

A **parameterized cursor** accepts parameters in its definition. The same cursor can be reused with different values—for example, different department IDs—without declaring multiple cursors.

**Syntax:**

```sql
CURSOR cursor_name(param1 TYPE, param2 TYPE, ...) IS
    SELECT ... WHERE column = param1 AND ...;
```

**Opening:** Pass values when opening:

```sql
OPEN cursor_name(value1, value2, ...);
```

Or in a cursor FOR loop:

```sql
FOR rec IN cursor_name(value1, value2) LOOP ...
```

**Example – Parameterized cursor:**

```sql
DECLARE
    CURSOR c_emp(p_dept_id NUMBER) IS
        SELECT emp_id, emp_name, salary FROM employees WHERE department_id = p_dept_id;
BEGIN
    DBMS_OUTPUT.PUT_LINE('--- Department 10 ---');
    FOR rec IN c_emp(10) LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_id || ' - ' || rec.emp_name);
    END LOOP;

    DBMS_OUTPUT.PUT_LINE('--- Department 20 ---');
    FOR rec IN c_emp(20) LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_id || ' - ' || rec.emp_name);
    END LOOP;
END;
/
```

---

#### 4) CURSOR with Parameters – Multiple Parameters

**Theory**

You can pass multiple parameters. Use them in the WHERE clause, ORDER BY, or even in expressions.

**Example – Multiple parameters:**

```sql
DECLARE
    CURSOR c_emp(p_dept_id NUMBER, p_min_sal NUMBER) IS
        SELECT emp_name, salary
        FROM employees
        WHERE department_id = p_dept_id AND salary >= p_min_sal
        ORDER BY salary DESC;
BEGIN
    FOR rec IN c_emp(10, 30000) LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_name || ' - ' || rec.salary);
    END LOOP;
END;
/
```

---

#### Hands-on: Basic Exercises

- Write a cursor FOR loop with an inline subquery
- Create a parameterized cursor and use it with two different department IDs
- Use a cursor FOR loop with two parameters (e.g., dept_id and min_salary)

---

### Hour 2: Advanced Cursor Techniques (60 min)

---

#### 5) Opening Cursor with Values

**Theory**

For parameterized cursors, you pass values when opening (manual) or when using the cursor in a FOR loop.

**Manual OPEN with parameters:**

```sql
OPEN c_emp(10);           -- One parameter
OPEN c_emp(10, 50000);    -- Two parameters
```

**Named parameters (for clarity):**

```sql
OPEN c_emp(p_dept_id => 10, p_min_sal => 30000);
```

**Using variables:**

```sql
v_dept_id := 20;
OPEN c_emp(v_dept_id);
```

---

#### 6) SYS_REFCURSOR Type

**Theory**

A **REF cursor** (reference cursor) is a pointer to a cursor. Unlike a static cursor, the query is not fixed at compile time. You can open it with different queries at runtime.

**SYS_REFCURSOR** is a built-in type: a weakly typed REF cursor. It can point to any SELECT. Use it when the result structure varies or when building dynamic queries.

| Static Cursor               | REF Cursor (SYS_REFCURSOR)       |
| --------------------------- | -------------------------------- |
| Query fixed at compile time | Query can change at runtime      |
| Declared with CURSOR ... IS | Declared as SYS_REFCURSOR        |
| Always same columns         | Can return different result sets |

**Declaration:**

```sql
v_cursor SYS_REFCURSOR;
```

---

#### 7) OPEN FOR Statement

**Theory**

**OPEN FOR** associates a REF cursor with a query and executes it. The query can be:

- A static string (known at compile time)
- A variable containing a dynamic SQL string

**Syntax:**

```sql
OPEN ref_cursor_variable FOR select_statement;
```

**Example – OPEN FOR with static query:**

```sql
DECLARE
    v_cur   SYS_REFCURSOR;
    v_id    employees.emp_id%TYPE;
    v_name  employees.emp_name%TYPE;
    v_sal   employees.salary%TYPE;
BEGIN
    OPEN v_cur FOR SELECT emp_id, emp_name, salary FROM employees WHERE department_id = 10;

    LOOP
        FETCH v_cur INTO v_id, v_name, v_sal;
        EXIT WHEN v_cur%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_id || ' - ' || v_name || ' - ' || v_sal);
    END LOOP;

    CLOSE v_cur;
END;
/
```

**Example – Cursor FOR loop with REF cursor:**

```sql
DECLARE
    v_cur SYS_REFCURSOR;
BEGIN
    OPEN v_cur FOR SELECT emp_name, salary FROM employees WHERE department_id = 20;

    FOR rec IN v_cur LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_name || ' - ' || rec.salary);
    END LOOP;

    CLOSE v_cur;
END;
/
```

---

#### 8) Dynamic SQL with REF Cursors

**Theory**

**Dynamic SQL** means the query is built at runtime (string concatenation, variables). REF cursors are often used with dynamic SQL when:

- Table or column names come from user input or configuration
- WHERE conditions change based on parameters
- Different queries are needed in different branches

**Syntax:**

```sql
OPEN ref_cursor FOR dynamic_sql_string [USING bind_var1, bind_var2, ...];
```

Use bind variables (e.g., `:dept_id`) to avoid SQL injection and improve performance.

**Example – Dynamic SQL with REF cursor:**

```sql
DECLARE
    v_cur     SYS_REFCURSOR;
    v_dept_id NUMBER := 10;
    v_sql     VARCHAR2(500);
    v_id      employees.emp_id%TYPE;
    v_name    employees.emp_name%TYPE;
BEGIN
    v_sql := 'SELECT emp_id, emp_name FROM employees WHERE department_id = :d';
    OPEN v_cur FOR v_sql USING v_dept_id;

    LOOP
        FETCH v_cur INTO v_id, v_name;
        EXIT WHEN v_cur%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_id || ' - ' || v_name);
    END LOOP;

    CLOSE v_cur;
END;
/
```

**Example – Building query dynamically (table/column from variable):**

```sql
DECLARE
    v_cur   SYS_REFCURSOR;
    v_sql   VARCHAR2(500);
    v_count NUMBER;
BEGIN
    v_sql := 'SELECT COUNT(*) FROM employees WHERE department_id = 10';
    OPEN v_cur FOR v_sql;
    FETCH v_cur INTO v_count;
    CLOSE v_cur;
    DBMS_OUTPUT.PUT_LINE('Count: ' || v_count);
END;
/
```

---

#### Hands-on: Complex Scenarios

- Build a REF cursor that opens with different WHERE clauses based on a parameter
- Use OPEN FOR with USING for bind variables
- Return a REF cursor from a procedure (for use by other programs or reports)

---

### Hour 3: Practice & Application (50 min)

- Comprehensive coding exercises (see below)
- Debugging: REF cursor not closed, wrong OPEN FOR string, parameter mismatches
- Code review: when to use cursor FOR vs manual, when REF cursor is appropriate
- Real-world: reporting procedures that return result sets, dynamic filters
- Best practices: bind variables, always CLOSE REF cursors, exception handling

---

## HANDS-ON EXERCISES

### Exercise 1: Basic Cursor FOR Loop Example

Write a cursor FOR loop (inline subquery) to display all department names from the departments table.

<details>
<summary>Solution</summary>

```sql
BEGIN
    FOR rec IN (SELECT department_name FROM departments ORDER BY department_name) LOOP
        DBMS_OUTPUT.PUT_LINE(rec.department_name);
    END LOOP;
END;
/
```

</details>

---

### Exercise 2: PL/SQL Cursors II with Parameters

Create a parameterized cursor that accepts a minimum salary. Use it in a cursor FOR loop to list employees earning more than that amount.

<details>
<summary>Solution</summary>

```sql
DECLARE
    CURSOR c_emp(p_min_sal NUMBER) IS
        SELECT emp_name, salary FROM employees WHERE salary >= p_min_sal;
BEGIN
    FOR rec IN c_emp(50000) LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_name || ' - ' || rec.salary);
    END LOOP;
END;
/
```

</details>

---

### Exercise 3: Error Handling Implementation

Write a block that opens a REF cursor, fetches rows in a loop, and ensures the cursor is closed in the EXCEPTION handler if an error occurs.

<details>
<summary>Solution</summary>

```sql
DECLARE
    v_cur  SYS_REFCURSOR;
    v_name employees.emp_name%TYPE;
BEGIN
    OPEN v_cur FOR SELECT emp_name FROM employees;
    LOOP
        FETCH v_cur INTO v_name;
        EXIT WHEN v_cur%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_name);
    END LOOP;
    CLOSE v_cur;
EXCEPTION
    WHEN OTHERS THEN
        IF v_cur%ISOPEN THEN
            CLOSE v_cur;
        END IF;
        RAISE;
END;
/
```

</details>

---

### Exercise 4: Complex Logic Building

Use a parameterized cursor (department_id, min_salary) and a cursor FOR loop. For each employee, calculate a bonus (10% of salary) and print emp_name, salary, and bonus.

<details>
<summary>Solution</summary>

```sql
DECLARE
    CURSOR c_emp(p_dept NUMBER, p_min NUMBER) IS
        SELECT emp_name, salary FROM employees
        WHERE department_id = p_dept AND salary >= p_min;
    v_bonus NUMBER;
BEGIN
    FOR rec IN c_emp(10, 30000) LOOP
        v_bonus := rec.salary * 0.1;
        DBMS_OUTPUT.PUT_LINE(rec.emp_name || ' | ' || rec.salary || ' | Bonus: ' || v_bonus);
    END LOOP;
END;
/
```

</details>

---

### Exercise 5: Integration with SQL Queries

Use a cursor FOR loop over a query that joins employees and departments, displaying employee name and department name for each row.

<details>
<summary>Solution</summary>

```sql
BEGIN
    FOR rec IN (
        SELECT e.emp_name, d.department_name
        FROM employees e
        JOIN departments d ON e.department_id = d.department_id
    ) LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_name || ' - ' || rec.department_name);
    END LOOP;
END;
/
```

</details>

---

### Exercise 6: Reusable Code Component

Write a block that could become a procedure: it accepts department_id via a variable, uses a parameterized cursor, and prints all employees in that department using a cursor FOR loop.

<details>
<summary>Solution</summary>

```sql
DECLARE
    p_dept_id NUMBER := 20;  -- Simulated parameter
    CURSOR c_emp(p_did NUMBER) IS
        SELECT emp_id, emp_name, salary FROM employees WHERE department_id = p_did;
BEGIN
    FOR rec IN c_emp(p_dept_id) LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_id || ' | ' || rec.emp_name || ' | ' || rec.salary);
    END LOOP;
END;
/
```

</details>

---

### Exercise 7: Performance Optimization

Compare (1) a cursor FOR loop with inline subquery vs (2) a cursor FOR loop with a named parameterized cursor called multiple times with different parameters. Discuss when each is more efficient (e.g., single use vs reuse).

---

### Exercise 8: Debugging Practice

Given a block with a REF cursor that is not closed on exception, or with a wrong OPEN FOR string, identify and fix the issues.

---

### Exercise 9: Advanced Pattern – Dynamic REF Cursor

Build a block that uses a variable to choose the sort column (emp_name or salary). Use dynamic SQL with OPEN FOR to build the ORDER BY and fetch results.

<details>
<summary>Solution</summary>

```sql
DECLARE
    v_cur      SYS_REFCURSOR;
    v_order_by VARCHAR2(50) := 'emp_name';  -- or 'salary'
    v_sql      VARCHAR2(500);
    v_name     employees.emp_name%TYPE;
    v_sal      employees.salary%TYPE;
BEGIN
    v_sql := 'SELECT emp_name, salary FROM employees ORDER BY ' || v_order_by;
    OPEN v_cur FOR v_sql;

    LOOP
        FETCH v_cur INTO v_name, v_sal;
        EXIT WHEN v_cur%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_name || ' - ' || v_sal);
    END LOOP;

    CLOSE v_cur;
END;
/
```

</details>

---

### Exercise 10: Complete PL/SQL Cursors II Project

Build a block that:

1. Declares a REF cursor
2. Opens it with a dynamic query (using a bind variable for department_id)
3. Uses a cursor FOR loop to process rows (or manual FETCH loop)
4. Handles errors and closes the cursor
5. Prints a summary (e.g., row count)

<details>
<summary>Solution</summary>

```sql
DECLARE
    v_cur     SYS_REFCURSOR;
    v_dept_id NUMBER := 10;
    v_sql     VARCHAR2(500);
    v_count   NUMBER := 0;
BEGIN
    v_sql := 'SELECT emp_id, emp_name, salary FROM employees WHERE department_id = :d';
    OPEN v_cur FOR v_sql USING v_dept_id;

    FOR rec IN v_cur LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_id || ' | ' || rec.emp_name || ' | ' || rec.salary);
        v_count := v_count + 1;
    END LOOP;

    CLOSE v_cur;
    DBMS_OUTPUT.PUT_LINE('Total: ' || v_count || ' employees');
EXCEPTION
    WHEN OTHERS THEN
        IF v_cur%ISOPEN THEN CLOSE v_cur; END IF;
        RAISE;
END;
/
```

</details>

---

## RESOURCES PROVIDED

- **PL/SQL Cursors II Guide** – This document
- **Code Examples and Patterns** – Solutions in exercises above
- **Best Practices**
  - Prefer cursor FOR loop when you don't need manual control
  - Use parameterized cursors for reusable logic
  - Use REF cursors when the query must be built dynamically
  - Always CLOSE REF cursors; use EXCEPTION handler if needed
  - Use bind variables (USING) with dynamic SQL to avoid injection and improve performance
- **Debugging Tips**
  - Verify OPEN FOR string syntax; test the query in SQL\*Plus first
  - Ensure parameter types and count match between cursor and OPEN
  - Check %ISOPEN before CLOSE in exception handlers

---

## HOMEWORK / PREPARATION

- [ ] Practice 15 programs using cursor FOR loops, parameterized cursors, and REF cursors
- [ ] Build a code library with examples
- [ ] Complete all 10 hands-on exercises
- [ ] Review when to use each cursor type
- [ ] Document cursor FOR vs manual vs REF cursor decision guide

---

## QUICK REFERENCE

```
Cursor FOR:    FOR rec IN cursor_name LOOP ... END LOOP;
Inline:        FOR rec IN (SELECT ...) LOOP ... END LOOP;
Parameterized: CURSOR c(p_id NUMBER) IS SELECT ... WHERE id = p_id;
               FOR rec IN c(10) LOOP ...
REF cursor:    v_cur SYS_REFCURSOR;
               OPEN v_cur FOR 'SELECT ...' [USING bind_var];
               FETCH / FOR rec IN v_cur / CLOSE v_cur;
```

---

_Day 4 – PL/SQL Cursors II: Cursor FOR Loops, Parameterized Cursors, REF Cursors_
