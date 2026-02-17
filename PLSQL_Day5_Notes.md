# PL/SQL Procedures – Day 5

## Creating Procedures, Parameters (IN, OUT, IN OUT)

**Training Material for Freshers**

---

## INTRODUCTION: WHAT IS A STORED PROCEDURE?

A **stored procedure** is a named PL/SQL block stored in the database. Unlike anonymous blocks that run once and disappear, procedures can be called repeatedly by name from other blocks, applications, or tools.

**Benefits:**

- **Reusability** – Write once, call many times
- **Modularity** – Break logic into manageable units
- **Security** – Grant execute on procedure without exposing tables
- **Performance** – Parsed and compiled once, cached
- **Maintainability** – Update logic in one place

---

## LEARNING OBJECTIVES

- Master Procedures in PL/SQL
- Write efficient procedural code
- Implement proper error handling
- Build reusable code components
- Apply PL/SQL best practices

---

## TOPICS COVERED

### Hour 1: Creating Procedures Introduction (50 min)

---

#### 1) What is a Stored Procedure?

**Theory**

A procedure is a **named block** with an optional list of parameters. It performs an action (e.g., insert, update, calculate) and can optionally return values through OUT or IN OUT parameters.

| Anonymous Block               | Stored Procedure                    |
| ----------------------------- | ----------------------------------- |
| Not stored                    | Stored in data dictionary           |
| Run once per execution        | Call by name anytime                |
| No parameters (use variables) | Formal parameters (IN, OUT, IN OUT) |
| Good for scripts, ad-hoc      | Good for production, APIs           |

**Procedure vs Function:**

- **Procedure** – Performs action; may return values via OUT parameters; called as a statement
- **Function** – Returns a single value; used in expressions (e.g., SELECT, assignments)

---

#### 2) CREATE PROCEDURE Syntax

**Theory**

**Syntax:**

```sql
CREATE [OR REPLACE] PROCEDURE procedure_name
    [(parameter1 [mode] type, parameter2 [mode] type, ...)]
IS | AS
    [local declarations]
BEGIN
    executable statements;
[EXCEPTION
    exception handlers;]
END [procedure_name];
/
```

- **OR REPLACE** – Recreates the procedure if it exists (preserves grants)
- **IS | AS** – Same meaning; use either
- Parameters are optional; a procedure can have none

**Example – Minimal procedure:**

```sql
CREATE OR REPLACE PROCEDURE say_hello
IS
BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello, World!');
END say_hello;
/

-- Call it:
BEGIN
    say_hello;
END;
/
```

---

#### 3) Procedures Without Parameters

**Theory**

A procedure with no parameters is useful for fixed, repetitive tasks (e.g., daily cleanup, logging, simple reports).

**Example – Procedure without parameters:**

```sql
CREATE OR REPLACE PROCEDURE print_current_date
IS
BEGIN
    DBMS_OUTPUT.PUT_LINE('Today is: ' || TO_CHAR(SYSDATE, 'DD-MON-YYYY'));
END print_current_date;
/

BEGIN
    print_current_date;
END;
/
```

---

#### 4) IN Parameters

**Theory**

**IN** is the default mode. The parameter passes a value **into** the procedure. The procedure can read it but **cannot** change it. The caller's variable is unchanged after the call.

**Syntax:**

```sql
parameter_name IN data_type [:= default_value]
```

**Example 1 – Procedure with IN parameter:**

```sql
CREATE OR REPLACE PROCEDURE greet_employee(p_emp_name IN VARCHAR2)
IS
BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello, ' || p_emp_name || '!');
END greet_employee;
/

BEGIN
    greet_employee('John');
END;
/
-- Output: Hello, John!
```

**Example 2 – Multiple IN parameters:**

```sql
CREATE OR REPLACE PROCEDURE update_salary(
    p_emp_id    IN NUMBER,
    p_increase  IN NUMBER
)
IS
    v_current NUMBER;
BEGIN
    SELECT salary INTO v_current FROM employees WHERE emp_id = p_emp_id;
    UPDATE employees
    SET salary = salary + (salary * p_increase / 100)
    WHERE emp_id = p_emp_id;
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('Updated salary for employee ' || p_emp_id);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee not found');
END update_salary;
/

BEGIN
    update_salary(101, 10);  -- 10% raise
END;
/
```

**Example 3 – IN with default value:**

```sql
CREATE OR REPLACE PROCEDURE log_message(
    p_message IN VARCHAR2,
    p_level   IN VARCHAR2 := 'INFO'
)
IS
BEGIN
    DBMS_OUTPUT.PUT_LINE('[' || p_level || '] ' || p_message);
END log_message;
/

BEGIN
    log_message('User logged in');           -- Uses default INFO
    log_message('Error occurred', 'ERROR');  -- Override default
END;
/
```

---

#### Hands-on: Basic Exercises

- Create a procedure with no parameters that prints a welcome message
- Create a procedure with one IN parameter (e.g., department_id) and print employees in that department
- Create a procedure with two IN parameters and default for the second

---

### Hour 2: Advanced Creating Procedures (60 min)

---

#### 5) OUT Parameters

**Theory**

**OUT** parameters are used to **return** values to the caller. The procedure assigns a value to the OUT parameter; that value is available to the caller after the procedure ends. The initial value passed in (if any) is ignored.

**Syntax:**

```sql
parameter_name OUT data_type
```

**Rules:**

- You must assign a value to an OUT parameter before the procedure ends
- The caller must pass a variable (not a literal) to receive the value
- OUT parameters are write-only inside the procedure (reading before assigning may give unpredictable results)

**Example – Procedure with OUT parameter:**

```sql
CREATE OR REPLACE PROCEDURE get_employee_count(
    p_dept_id   IN  NUMBER,
    p_count     OUT NUMBER
)
IS
BEGIN
    SELECT COUNT(*) INTO p_count
    FROM employees
    WHERE department_id = p_dept_id;
END get_employee_count;
/

-- Calling:
DECLARE
    v_count NUMBER;
BEGIN
    get_employee_count(10, v_count);
    DBMS_OUTPUT.PUT_LINE('Employees in dept 10: ' || v_count);
END;
/
```

**Example – Multiple OUT parameters:**

```sql
CREATE OR REPLACE PROCEDURE get_emp_details(
    p_emp_id   IN  NUMBER,
    p_name     OUT VARCHAR2,
    p_salary   OUT NUMBER,
    p_dept_id  OUT NUMBER
)
IS
BEGIN
    SELECT emp_name, salary, department_id
    INTO p_name, p_salary, p_dept_id
    FROM employees
    WHERE emp_id = p_emp_id;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        p_name := NULL;
        p_salary := NULL;
        p_dept_id := NULL;
END get_emp_details;
/

DECLARE
    v_name   VARCHAR2(100);
    v_sal    NUMBER;
    v_dept   NUMBER;
BEGIN
    get_emp_details(101, v_name, v_sal, v_dept);
    DBMS_OUTPUT.PUT_LINE(v_name || ' | ' || v_sal || ' | ' || v_dept);
END;
/
```

---

#### 6) IN OUT Parameters

**Theory**

**IN OUT** parameters pass a value **in** and **out**. The caller provides an initial value; the procedure can read and modify it; the updated value is returned to the caller.

**Syntax:**

```sql
parameter_name IN OUT data_type
```

**Use when:** The procedure needs both the input value and must return a modified value (e.g., swap, format, increment).

**Example – IN OUT parameter:**

```sql
CREATE OR REPLACE PROCEDURE swap_values(
    p_a IN OUT NUMBER,
    p_b IN OUT NUMBER
)
IS
    v_temp NUMBER;
BEGIN
    v_temp := p_a;
    p_a := p_b;
    p_b := v_temp;
END swap_values;
/

DECLARE
    v_x NUMBER := 10;
    v_y NUMBER := 20;
BEGIN
    DBMS_OUTPUT.PUT_LINE('Before: x=' || v_x || ', y=' || v_y);
    swap_values(v_x, v_y);
    DBMS_OUTPUT.PUT_LINE('After:  x=' || v_x || ', y=' || v_y);
END;
/
-- Output: Before: x=10, y=20  |  After: x=20, y=10
```

**Example – Format string (IN OUT):**

```sql
CREATE OR REPLACE PROCEDURE format_phone(p_phone IN OUT VARCHAR2)
IS
BEGIN
    p_phone := '(' || SUBSTR(p_phone, 1, 3) || ') ' ||
               SUBSTR(p_phone, 4, 3) || '-' || SUBSTR(p_phone, 7);
END format_phone;
/

DECLARE
    v_phone VARCHAR2(20) := '5551234567';
BEGIN
    format_phone(v_phone);
    DBMS_OUTPUT.PUT_LINE(v_phone);  -- (555) 123-4567
END;
/
```

---

#### 7) Default Parameter Values

**Theory**

You can assign a **default value** to IN parameters. If the caller omits that parameter, the default is used. Parameters with defaults must come **after** parameters without defaults.

**Syntax:**

```sql
parameter_name IN data_type := default_value
```

**Example:**

```sql
CREATE OR REPLACE PROCEDURE report_employees(
    p_dept_id   IN NUMBER DEFAULT NULL,   -- NULL means all departments
    p_min_sal   IN NUMBER DEFAULT 0
)
IS
BEGIN
    FOR rec IN (
        SELECT emp_name, salary, department_id
        FROM employees
        WHERE (p_dept_id IS NULL OR department_id = p_dept_id)
          AND salary >= p_min_sal
    ) LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_name || ' | ' || rec.salary || ' | ' || rec.department_id);
    END LOOP;
END report_employees;
/

BEGIN
    report_employees;              -- All depts, min_sal=0
    report_employees(10);          -- Dept 10, min_sal=0
    report_employees(10, 30000);   -- Dept 10, min_sal=30000
END;
/
```

---

#### 8) Named vs Positional Notation

**Theory**

**Positional notation** – Pass arguments in the same order as parameters:

```sql
proc_name(value1, value2, value3);
```

**Named notation** – Pass by parameter name (order does not matter):

```sql
proc_name(p_param2 => value2, p_param1 => value1, p_param3 => value3);
```

**Mixed notation** – Positional first, then named (recommended for skipping optional params):

```sql
proc_name(value1, p_param3 => value3);  -- Skip param2, use default
```

**Example:**

```sql
CREATE OR REPLACE PROCEDURE add_employee(
    p_id   IN NUMBER,
    p_name IN VARCHAR2,
    p_dept IN NUMBER DEFAULT 10
)
IS
BEGIN
    INSERT INTO employees (emp_id, emp_name, department_id) VALUES (p_id, p_name, p_dept);
    COMMIT;
END add_employee;
/

-- Positional:
add_employee(201, 'Alice', 20);

-- Named:
add_employee(p_name => 'Bob', p_id => 202, p_dept => 30);

-- Mixed (skip p_dept, use default 10):
add_employee(203, p_name => 'Charlie');
```

---

#### 9) Exception Handling in Procedures

**Theory**

Procedures can have an EXCEPTION section. Unhandled exceptions propagate to the caller. Handle errors inside the procedure when you can recover or log; otherwise, let the caller handle them.

**Best practices:**

- Handle expected errors (e.g., NO_DATA_FOUND, DUP_VAL_ON_INDEX)
- Use WHEN OTHERS to log and re-raise: `RAISE;`
- Avoid swallowing errors silently

**Example – Exception handling in procedure:**

```sql
CREATE OR REPLACE PROCEDURE safe_update_salary(
    p_emp_id   IN NUMBER,
    p_new_sal  IN NUMBER
)
IS
    v_old_sal NUMBER;
BEGIN
    IF p_new_sal <= 0 THEN
        RAISE_APPLICATION_ERROR(-20001, 'Salary must be positive');
    END IF;

    SELECT salary INTO v_old_sal FROM employees WHERE emp_id = p_emp_id;
    UPDATE employees SET salary = p_new_sal WHERE emp_id = p_emp_id;
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('Updated from ' || v_old_sal || ' to ' || p_new_sal);

EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee ' || p_emp_id || ' not found');
    WHEN DUP_VAL_ON_INDEX THEN
        ROLLBACK;
        RAISE;
    WHEN OTHERS THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
        RAISE;
END safe_update_salary;
/
```

---

#### Hands-on: Complex Scenarios

- Create a procedure with OUT parameters returning multiple values
- Use IN OUT to implement a "double and return" logic
- Call a procedure using named notation
- Add exception handling for NO_DATA_FOUND and invalid input

---

### Hour 3: Practice & Application (50 min)

- Comprehensive coding exercises (see below)
- Debugging: parameter order, unassigned OUT, exception propagation
- Code review: when to use IN vs OUT vs IN OUT, default values
- Real-world: CRUD procedures, validation, business rules
- Best practices: naming (p\_ for parameters), validation, COMMIT/ROLLBACK placement

---

## HANDS-ON EXERCISES

### Exercise 1: Basic Procedure Example

Create a procedure with no parameters that prints "PL/SQL Procedures - Day 5".

<details>
<summary>Solution</summary>

```sql
CREATE OR REPLACE PROCEDURE print_welcome
IS
BEGIN
    DBMS_OUTPUT.PUT_LINE('PL/SQL Procedures - Day 5');
END print_welcome;
/

BEGIN
    print_welcome;
END;
/
```

</details>

---

### Exercise 2: Procedures with Parameters

Create a procedure that accepts employee ID (IN) and prints the employee's name and salary. Use SELECT INTO.

<details>
<summary>Solution</summary>

```sql
CREATE OR REPLACE PROCEDURE print_employee(p_emp_id IN NUMBER)
IS
    v_name   employees.emp_name%TYPE;
    v_salary employees.salary%TYPE;
BEGIN
    SELECT emp_name, salary INTO v_name, v_salary
    FROM employees WHERE emp_id = p_emp_id;
    DBMS_OUTPUT.PUT_LINE(p_emp_id || ' | ' || v_name || ' | ' || v_salary);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee ' || p_emp_id || ' not found');
END print_employee;
/

BEGIN
    print_employee(101);
END;
/
```

</details>

---

### Exercise 3: Error Handling Implementation

Create a procedure that divides two numbers (IN parameters). Handle ZERO_DIVIDE and invalid inputs. Use RAISE_APPLICATION_ERROR for validation.

<details>
<summary>Solution</summary>

```sql
CREATE OR REPLACE PROCEDURE safe_divide(
    p_a IN NUMBER,
    p_b IN NUMBER
)
IS
    v_result NUMBER;
BEGIN
    IF p_b = 0 THEN
        RAISE_APPLICATION_ERROR(-20002, 'Divisor cannot be zero');
    END IF;
    v_result := p_a / p_b;
    DBMS_OUTPUT.PUT_LINE(p_a || ' / ' || p_b || ' = ' || v_result);
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
        RAISE;
END safe_divide;
/

BEGIN
    safe_divide(10, 2);
    safe_divide(10, 0);  -- Will raise error
END;
/
```

</details>

---

### Exercise 4: Complex Logic Building

Create a procedure that accepts department_id (IN) and returns the total salary (OUT) and employee count (OUT) for that department.

<details>
<summary>Solution</summary>

```sql
CREATE OR REPLACE PROCEDURE get_dept_summary(
    p_dept_id   IN  NUMBER,
    p_total_sal OUT NUMBER,
    p_emp_count OUT NUMBER
)
IS
BEGIN
    SELECT SUM(salary), COUNT(*)
    INTO p_total_sal, p_emp_count
    FROM employees
    WHERE department_id = p_dept_id;

    IF p_emp_count = 0 THEN
        p_total_sal := 0;
    END IF;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        p_total_sal := 0;
        p_emp_count := 0;
END get_dept_summary;
/

DECLARE
    v_total NUMBER;
    v_count NUMBER;
BEGIN
    get_dept_summary(10, v_total, v_count);
    DBMS_OUTPUT.PUT_LINE('Total: ' || v_total || ', Count: ' || v_count);
END;
/
```

</details>

---

### Exercise 5: Integration with SQL Queries

Create a procedure that accepts department_id and uses a cursor to print all employees in that department (emp_id, emp_name, salary).

<details>
<summary>Solution</summary>

```sql
CREATE OR REPLACE PROCEDURE list_dept_employees(p_dept_id IN NUMBER)
IS
    CURSOR c_emp IS
        SELECT emp_id, emp_name, salary FROM employees WHERE department_id = p_dept_id;
BEGIN
    FOR rec IN c_emp LOOP
        DBMS_OUTPUT.PUT_LINE(rec.emp_id || ' | ' || rec.emp_name || ' | ' || rec.salary);
    END LOOP;
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
END list_dept_employees;
/

BEGIN
    list_dept_employees(10);
END;
/
```

</details>

---

### Exercise 6: Reusable Code Component

Create a procedure that accepts emp_id (IN), new_salary (IN), and returns success flag (OUT) and message (OUT). Update the salary if valid; set success=0 and message on error.

<details>
<summary>Solution</summary>

```sql
CREATE OR REPLACE PROCEDURE update_emp_salary(
    p_emp_id    IN  NUMBER,
    p_new_sal   IN  NUMBER,
    p_success   OUT NUMBER,
    p_message   OUT VARCHAR2
)
IS
BEGIN
    IF p_new_sal < 0 THEN
        p_success := 0;
        p_message := 'Salary cannot be negative';
        RETURN;
    END IF;

    UPDATE employees SET salary = p_new_sal WHERE emp_id = p_emp_id;
    IF SQL%ROWCOUNT = 0 THEN
        p_success := 0;
        p_message := 'Employee not found';
        RETURN;
    END IF;

    COMMIT;
    p_success := 1;
    p_message := 'Salary updated successfully';
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
        p_success := 0;
        p_message := SQLERRM;
END update_emp_salary;
/

DECLARE
    v_ok  NUMBER;
    v_msg VARCHAR2(200);
BEGIN
    update_emp_salary(101, 55000, v_ok, v_msg);
    DBMS_OUTPUT.PUT_LINE(v_ok || ' | ' || v_msg);
END;
/
```

</details>

---

### Exercise 7: Performance Optimization

Create a procedure that uses bulk operations (hint: FORALL) or efficient single UPDATE instead of row-by-row updates. For this exercise, use a single UPDATE with a WHERE clause and SQL%ROWCOUNT to report affected rows.

<details>
<summary>Solution</summary>

```sql
CREATE OR REPLACE PROCEDURE give_raise_to_dept(
    p_dept_id   IN NUMBER,
    p_pct       IN NUMBER DEFAULT 10
)
IS
    v_count NUMBER;
BEGIN
    UPDATE employees
    SET salary = salary * (1 + p_pct / 100)
    WHERE department_id = p_dept_id;
    v_count := SQL%ROWCOUNT;
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('Updated ' || v_count || ' employees');
END give_raise_to_dept;
/

BEGIN
    give_raise_to_dept(10, 5);
END;
/
```

</details>

---

### Exercise 8: Debugging Practice

Given a procedure with wrong parameter order, unassigned OUT parameter, or missing exception handling, identify and fix the issues.

---

### Exercise 9: Advanced Pattern – IN OUT

Create a procedure that accepts a string (IN OUT) and appends a timestamp to it in the format: "OriginalText [2024-01-15 10:30:00]".

<details>
<summary>Solution</summary>

```sql
CREATE OR REPLACE PROCEDURE append_timestamp(p_text IN OUT VARCHAR2)
IS
BEGIN
    p_text := p_text || ' [' || TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS') || ']';
END append_timestamp;
/

DECLARE
    v_msg VARCHAR2(200) := 'Log entry';
BEGIN
    append_timestamp(v_msg);
    DBMS_OUTPUT.PUT_LINE(v_msg);
END;
/
```

</details>

---

### Exercise 10: Complete Procedures Project

Build a procedure that:

1. Accepts department_id (IN)
2. Returns: emp_count (OUT), total_salary (OUT), avg_salary (OUT)
3. Uses a single SELECT with aggregation
4. Handles NO_DATA_FOUND (set all OUTs to 0/NULL)
5. Uses named notation when called

<details>
<summary>Solution</summary>

```sql
CREATE OR REPLACE PROCEDURE get_dept_stats(
    p_dept_id    IN  NUMBER,
    p_emp_count  OUT NUMBER,
    p_total_sal  OUT NUMBER,
    p_avg_sal    OUT NUMBER
)
IS
BEGIN
    SELECT COUNT(*), NVL(SUM(salary), 0), NVL(AVG(salary), 0)
    INTO p_emp_count, p_total_sal, p_avg_sal
    FROM employees
    WHERE department_id = p_dept_id;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        p_emp_count := 0;
        p_total_sal := 0;
        p_avg_sal := 0;
END get_dept_stats;
/

DECLARE
    v_count NUMBER;
    v_total NUMBER;
    v_avg   NUMBER;
BEGIN
    get_dept_stats(
        p_dept_id   => 10,
        p_emp_count => v_count,
        p_total_sal => v_total,
        p_avg_sal   => v_avg
    );
    DBMS_OUTPUT.PUT_LINE('Count: ' || v_count || ', Total: ' || v_total || ', Avg: ' || v_avg);
END;
/
```

</details>

---

## RESOURCES PROVIDED

- **PL/SQL Procedures Guide** – This document
- **Code Examples and Patterns** – Solutions in exercises above
- **Best Practices**
  - Use `p_` prefix for parameters
  - Validate inputs; use RAISE_APPLICATION_ERROR for bad data
  - Place COMMIT at end of procedure (or let caller control transaction)
  - Use OUT for returning values; IN OUT only when both read and write needed
  - Prefer named notation for procedures with many optional parameters
- **Debugging Tips**
  - Check parameter order (positional) or names (named)
  - Ensure OUT parameters are assigned before procedure ends
  - Use DBMS_OUTPUT or logging to trace execution

---

## HOMEWORK / PREPARATION

- [ ] Practice 15 procedure programs
- [ ] Build a code library with IN, OUT, IN OUT examples
- [ ] Complete all 10 hands-on exercises
- [ ] Review when to use each parameter mode
- [ ] Document procedure naming and parameter conventions

---

## QUICK REFERENCE

```
CREATE [OR REPLACE] PROCEDURE name (p1 IN type, p2 OUT type, p3 IN OUT type)
IS
BEGIN ... END;

IN:      Pass in, read-only
OUT:     Return value, must assign
IN OUT:  Pass in and out, read and write
Default: p_name IN NUMBER := 10
Named:   proc(p_param => value);
```

---

_Day 5 – PL/SQL Procedures: Creating Procedures, Parameters (IN, OUT, IN OUT)_
