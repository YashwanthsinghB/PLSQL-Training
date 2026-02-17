# PL/SQL Exception Handling – Day 6

## Exception Types, User-Defined Exceptions & RAISE

**Training Material for Freshers**

---

## INTRODUCTION: WHY EXCEPTION HANDLING?

**Exceptions** are errors that occur at runtime. Without handling them, the program stops and the error propagates to the caller or user. **Exception handling** lets you:

- Catch and handle errors gracefully
- Provide meaningful messages to users
- Roll back or clean up (e.g., close cursors) on error
- Continue or retry in some cases
- Log errors for debugging

PL/SQL supports **predefined** exceptions (built-in) and **user-defined** exceptions (you declare and raise them).

---

## LEARNING OBJECTIVES

- Master Exception Handling in PL/SQL
- Write efficient procedural code
- Implement proper error handling
- Build reusable code components
- Apply PL/SQL best practices

---

## TOPICS COVERED

### Hour 1: Exception Handling Introduction (50 min)

---

#### 1) Exception Handling Importance

**Theory**

When an error occurs:

1. Control transfers to the EXCEPTION section
2. The matching handler runs
3. After the handler, execution continues with the next statement (or the block ends)

If no handler matches, the exception propagates to the enclosing block or caller. Unhandled exceptions cause the program to fail.

**Basic structure:**

```sql
BEGIN
    -- Your code
EXCEPTION
    WHEN exception_name1 THEN
        -- Handle it
    WHEN exception_name2 THEN
        -- Handle it
    WHEN OTHERS THEN
        -- Catch all others
END;
```

---

#### 2) Predefined Exceptions

**Theory**

Oracle defines common exceptions with meaningful names. You can handle them by name instead of error codes.

| Exception             | Oracle Error | When It Occurs                                                      |
| --------------------- | ------------ | ------------------------------------------------------------------- |
| `NO_DATA_FOUND`       | ORA-01403    | SELECT INTO returns no rows                                         |
| `TOO_MANY_ROWS`       | ORA-01422    | SELECT INTO returns more than one row                               |
| `ZERO_DIVIDE`         | ORA-01476    | Division by zero                                                    |
| `VALUE_ERROR`         | ORA-06502    | Conversion, truncation, or constraint error (e.g., string too long) |
| `DUP_VAL_ON_INDEX`    | ORA-00001    | Unique constraint violation                                         |
| `INVALID_CURSOR`      | ORA-01001    | Invalid cursor operation (e.g., FETCH on closed cursor)             |
| `INVALID_NUMBER`      | ORA-01722    | Invalid character in number conversion                              |
| `CURSOR_ALREADY_OPEN` | ORA-06511    | Attempt to OPEN an already open cursor                              |

**Example 1 – NO_DATA_FOUND:**

```sql
DECLARE
    v_name VARCHAR2(100);
BEGIN
    SELECT emp_name INTO v_name FROM employees WHERE emp_id = 99999;
    DBMS_OUTPUT.PUT_LINE(v_name);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee not found');
END;
/
```

**Example 2 – TOO_MANY_ROWS:**

```sql
DECLARE
    v_sal NUMBER;
BEGIN
    SELECT salary INTO v_sal FROM employees WHERE department_id = 10;
    DBMS_OUTPUT.PUT_LINE(v_sal);
EXCEPTION
    WHEN TOO_MANY_ROWS THEN
        DBMS_OUTPUT.PUT_LINE('Query returned multiple rows; use a cursor');
END;
/
```

**Example 3 – ZERO_DIVIDE:**

```sql
DECLARE
    v_a NUMBER := 10;
    v_b NUMBER := 0;
    v_r NUMBER;
BEGIN
    v_r := v_a / v_b;
    DBMS_OUTPUT.PUT_LINE(v_r);
EXCEPTION
    WHEN ZERO_DIVIDE THEN
        DBMS_OUTPUT.PUT_LINE('Cannot divide by zero');
END;
/
```

**Example 4 – VALUE_ERROR:**

```sql
DECLARE
    v_num NUMBER;
BEGIN
    v_num := TO_NUMBER('ABC');  -- Invalid conversion
    DBMS_OUTPUT.PUT_LINE(v_num);
EXCEPTION
    WHEN VALUE_ERROR THEN
        DBMS_OUTPUT.PUT_LINE('Invalid value or conversion error');
END;
/
```

---

#### Hands-on: Basic Exercises

- Handle NO_DATA_FOUND when selecting a non-existent employee
- Handle TOO_MANY_ROWS when SELECT INTO returns multiple rows
- Handle ZERO_DIVIDE in a division operation
- Handle VALUE_ERROR when converting invalid string to number

---

### Hour 2: Advanced Error Handling (60 min)

---

#### 3) OTHERS Exception Handler

**Theory**

`WHEN OTHERS THEN` catches **any** exception not handled by previous handlers. Use it as a fallback, but:

- **Best practice:** Log the error (SQLCODE, SQLERRM) and then re-raise with `RAISE;` so the caller knows an error occurred
- Avoid swallowing errors silently (e.g., empty handler)

**Example – OTHERS with re-raise:**

```sql
DECLARE
    v_name VARCHAR2(100);
BEGIN
    SELECT emp_name INTO v_name FROM employees WHERE emp_id = 101;
    DBMS_OUTPUT.PUT_LINE(v_name);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee not found');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error ' || SQLCODE || ': ' || SQLERRM);
        RAISE;  -- Propagate to caller
END;
/
```

---

#### 4) User-Defined Exceptions

**Theory**

You can declare your own exceptions for business rule violations (e.g., "Salary too high", "Invalid status"). Declare them in the DECLARE section and raise them with `RAISE` when the condition occurs.

**Syntax:**

```sql
DECLARE
    exception_name EXCEPTION;
BEGIN
    IF condition THEN
        RAISE exception_name;
    END IF;
EXCEPTION
    WHEN exception_name THEN
        -- Handle it
END;
```

**Example – User-defined exception:**

```sql
DECLARE
    e_salary_too_high EXCEPTION;
    v_salary NUMBER := 200000;
BEGIN
    IF v_salary > 150000 THEN
        RAISE e_salary_too_high;
    END IF;
    DBMS_OUTPUT.PUT_LINE('Salary OK');
EXCEPTION
    WHEN e_salary_too_high THEN
        DBMS_OUTPUT.PUT_LINE('Salary exceeds maximum limit of 150000');
END;
/
```

**Example – User-defined with PRAGMA EXCEPTION_INIT (associate with Oracle error):**

```sql
DECLARE
    e_foreign_key_violation EXCEPTION;
    PRAGMA EXCEPTION_INIT(e_foreign_key_violation, -2291);  -- ORA-02291
BEGIN
    INSERT INTO employees (emp_id, emp_name, department_id)
    VALUES (100, 'Test', 999);  -- Invalid dept_id
EXCEPTION
    WHEN e_foreign_key_violation THEN
        DBMS_OUTPUT.PUT_LINE('Invalid department - foreign key violation');
        ROLLBACK;
END;
/
```

---

#### 5) RAISE Statement

**Theory**

**RAISE** does two things:

1. **RAISE exception_name** – Raises a user-defined (or predefined) exception you name
2. **RAISE** – Re-raises the current exception (used inside an exception handler to propagate it)

**Syntax:**

```sql
RAISE exception_name;   -- Raise specific exception
RAISE;                  -- Re-raise current exception (only in EXCEPTION section)
```

**Example – RAISE user-defined:**

```sql
DECLARE
    e_invalid_status EXCEPTION;
    v_status CHAR(1) := 'X';
BEGIN
    IF v_status NOT IN ('A', 'I', 'P') THEN
        RAISE e_invalid_status;
    END IF;
EXCEPTION
    WHEN e_invalid_status THEN
        DBMS_OUTPUT.PUT_LINE('Status must be A, I, or P');
END;
/
```

**Example – RAISE (re-raise):**

```sql
BEGIN
    -- some code
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Handled locally');
        RAISE;  -- Also propagate to outer block
END;
/
```

---

#### 6) RAISE_APPLICATION_ERROR

**Theory**

`RAISE_APPLICATION_ERROR` raises a **user-defined** error with a custom error number and message. Use it for business rule validation.

**Syntax:**

```sql
RAISE_APPLICATION_ERROR(error_code, message [, keep_stack]);
```

- **error_code** – Integer from -20000 to -20999 (reserved for applications)
- **message** – VARCHAR2, up to 2048 bytes
- **keep_stack** – Optional; TRUE keeps the error stack

**Example:**

```sql
DECLARE
    v_salary NUMBER := -5000;
BEGIN
    IF v_salary < 0 THEN
        RAISE_APPLICATION_ERROR(-20001, 'Salary cannot be negative');
    END IF;
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
END;
/
```

**Note:** RAISE_APPLICATION_ERROR immediately stops execution and propagates; you don't need to declare an exception for it.

---

#### 7) SQLCODE and SQLERRM

**Theory**

| Function  | Returns                                                               |
| --------- | --------------------------------------------------------------------- |
| `SQLCODE` | Numeric error code (e.g., 100 for NO_DATA_FOUND, negative for others) |
| `SQLERRM` | Error message text                                                    |

Use them in WHEN OTHERS to log or display the actual error. Call them **immediately** in the handler; nested calls can overwrite them.

**Example:**

```sql
BEGIN
    INSERT INTO nonexistent_table VALUES (1);
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('SQLCODE: ' || SQLCODE);
        DBMS_OUTPUT.PUT_LINE('SQLERRM: ' || SQLERRM);
        RAISE;
END;
/
```

---

#### Hands-on: Complex Scenarios

- Use OTHERS with SQLCODE and SQLERRM, then re-raise
- Declare and raise a user-defined exception for invalid input
- Use RAISE_APPLICATION_ERROR for validation
- Associate a user exception with an Oracle error using PRAGMA EXCEPTION_INIT

---

### Hour 3: Practice & Application (50 min)

- Comprehensive coding exercises (see below)
- Debugging: exception not caught, wrong handler order, missing RAISE
- Code review: when to use predefined vs user-defined vs RAISE_APPLICATION_ERROR
- Real-world: validation, cleanup on error, logging
- Best practices: handle expected errors, use OTHERS as fallback, re-raise when appropriate

---

## HANDS-ON EXERCISES

### Exercise 1: Basic Error Handling Example

Write a block that selects an employee by ID and handles NO_DATA_FOUND with a friendly message.

<details>
<summary>Solution</summary>

```sql
DECLARE
    v_name employees.emp_name%TYPE;
BEGIN
    SELECT emp_name INTO v_name FROM employees WHERE emp_id = 99999;
    DBMS_OUTPUT.PUT_LINE(v_name);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee not found');
END;
/
```

</details>

---

### Exercise 2: Exception Handling with Parameters

Create a block that accepts an employee ID (variable), fetches salary, and handles both NO_DATA_FOUND and TOO_MANY_ROWS.

<details>
<summary>Solution</summary>

```sql
DECLARE
    p_emp_id NUMBER := 101;
    v_salary employees.salary%TYPE;
BEGIN
    SELECT salary INTO v_salary FROM employees WHERE emp_id = p_emp_id;
    DBMS_OUTPUT.PUT_LINE('Salary: ' || v_salary);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee ' || p_emp_id || ' not found');
    WHEN TOO_MANY_ROWS THEN
        DBMS_OUTPUT.PUT_LINE('Multiple employees with ID ' || p_emp_id);
END;
/
```

</details>

---

### Exercise 3: Error Handling Implementation

Write a block that divides two numbers. Handle ZERO_DIVIDE and VALUE_ERROR (invalid number input).

<details>
<summary>Solution</summary>

```sql
DECLARE
    v_a NUMBER := 10;
    v_b NUMBER := 0;
    v_r NUMBER;
BEGIN
    v_r := v_a / v_b;
    DBMS_OUTPUT.PUT_LINE('Result: ' || v_r);
EXCEPTION
    WHEN ZERO_DIVIDE THEN
        DBMS_OUTPUT.PUT_LINE('Cannot divide by zero');
    WHEN VALUE_ERROR THEN
        DBMS_OUTPUT.PUT_LINE('Invalid numeric value');
END;
/
```

</details>

---

### Exercise 4: Complex Logic Building

Declare a user-defined exception `e_invalid_age`. If age is less than 18 or greater than 120, RAISE it and handle it with a message.

<details>
<summary>Solution</summary>

```sql
DECLARE
    e_invalid_age EXCEPTION;
    v_age NUMBER := 150;
BEGIN
    IF v_age < 18 OR v_age > 120 THEN
        RAISE e_invalid_age;
    END IF;
    DBMS_OUTPUT.PUT_LINE('Age is valid: ' || v_age);
EXCEPTION
    WHEN e_invalid_age THEN
        DBMS_OUTPUT.PUT_LINE('Age must be between 18 and 120');
END;
/
```

</details>

---

### Exercise 5: Integration with SQL Queries

Write a block that inserts a row and handles DUP_VAL_ON_INDEX. If duplicate, print "Record already exists" and continue.

<details>
<summary>Solution</summary>

```sql
BEGIN
    INSERT INTO employees (emp_id, emp_name, salary)
    VALUES (101, 'Duplicate Test', 50000);
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('Inserted successfully');
EXCEPTION
    WHEN DUP_VAL_ON_INDEX THEN
        DBMS_OUTPUT.PUT_LINE('Record already exists - duplicate key');
        ROLLBACK;
END;
/
```

</details>

---

### Exercise 6: Reusable Code Component

Create a procedure that validates salary (must be positive and < 1000000). Use RAISE_APPLICATION_ERROR for invalid values. Call it with valid and invalid inputs.

<details>
<summary>Solution</summary>

```sql
CREATE OR REPLACE PROCEDURE validate_salary(p_salary IN NUMBER)
IS
BEGIN
    IF p_salary <= 0 THEN
        RAISE_APPLICATION_ERROR(-20002, 'Salary must be positive');
    END IF;
    IF p_salary >= 1000000 THEN
        RAISE_APPLICATION_ERROR(-20003, 'Salary exceeds maximum');
    END IF;
    DBMS_OUTPUT.PUT_LINE('Salary valid: ' || p_salary);
END validate_salary;
/

BEGIN
    validate_salary(50000);   -- OK
    validate_salary(-100);    -- Raises -20002
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
END;
/
```

</details>

---

### Exercise 7: Performance Optimization

Use WHEN OTHERS to catch any error, log SQLCODE and SQLERRM, then re-raise so the caller can handle it. Show how this helps with debugging without hiding errors.

---

### Exercise 8: Debugging Practice

Given a block where an exception is raised but not caught, or OTHERS catches everything but doesn't re-raise, identify and fix the issues.

---

### Exercise 9: Advanced Pattern – PRAGMA EXCEPTION_INIT

Use PRAGMA EXCEPTION_INIT to associate a user-named exception with ORA-02291 (foreign key violation). Perform an insert that violates the FK and handle it with your named exception.

<details>
<summary>Solution</summary>

```sql
DECLARE
    e_fk_violation EXCEPTION;
    PRAGMA EXCEPTION_INIT(e_fk_violation, -2291);
BEGIN
    INSERT INTO employees (emp_id, emp_name, department_id)
    VALUES (999, 'Test', 99999);  -- Invalid department_id
    COMMIT;
EXCEPTION
    WHEN e_fk_violation THEN
        DBMS_OUTPUT.PUT_LINE('Invalid department - parent key not found');
        ROLLBACK;
END;
/
```

</details>

---

### Exercise 10: Complete Exception Handling Project

Build a block that:

1. Declares a user-defined exception `e_invalid_dept`
2. Accepts a department_id (variable)
3. If department_id is NULL or < 1, RAISE e_invalid_dept
4. SELECT count of employees in that department
5. Handle NO_DATA_FOUND, e_invalid_dept, and OTHERS (log and re-raise)

<details>
<summary>Solution</summary>

```sql
DECLARE
    e_invalid_dept EXCEPTION;
    p_dept_id   NUMBER := 0;
    v_count     NUMBER;
BEGIN
    IF p_dept_id IS NULL OR p_dept_id < 1 THEN
        RAISE e_invalid_dept;
    END IF;

    SELECT COUNT(*) INTO v_count
    FROM employees WHERE department_id = p_dept_id;

    DBMS_OUTPUT.PUT_LINE('Employees in dept ' || p_dept_id || ': ' || v_count);

EXCEPTION
    WHEN e_invalid_dept THEN
        DBMS_OUTPUT.PUT_LINE('Department ID must be positive');
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No data found');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error ' || SQLCODE || ': ' || SQLERRM);
        RAISE;
END;
/
```

</details>

---

## RESOURCES PROVIDED

- **PL/SQL Exception Handling Guide** – This document
- **Code Examples and Patterns** – Solutions in exercises above
- **Best Practices**
  - Handle specific exceptions before OTHERS
  - Use RAISE in OTHERS when you can't fully handle the error
  - Use RAISE_APPLICATION_ERROR for business validation (-20000 to -20999)
  - Use user-defined exceptions for clear business rules
  - Log SQLCODE and SQLERRM in OTHERS for debugging
- **Debugging Tips**
  - Test with invalid data to trigger exceptions
  - Use DBMS_OUTPUT in handlers to trace flow
  - Ensure ROLLBACK in handlers when you modify data

---

## HOMEWORK / PREPARATION

- [ ] Practice 15 exception handling programs
- [ ] Build a code library with predefined, user-defined, and RAISE_APPLICATION_ERROR examples
- [ ] Complete all 10 hands-on exercises
- [ ] Review when to use each exception type
- [ ] Document exception handling patterns for your projects

---

## QUICK REFERENCE

```
EXCEPTION
  WHEN no_data_found THEN ...;
  WHEN too_many_rows THEN ...;
  WHEN zero_divide THEN ...;
  WHEN value_error THEN ...;
  WHEN OTHERS THEN ...; RAISE;

User-defined:  e_name EXCEPTION;  RAISE e_name;
PRAGMA:        PRAGMA EXCEPTION_INIT(e_name, -2291);
RAISE_APPLICATION_ERROR:  RAISE_APPLICATION_ERROR(-20001, 'message');
Re-raise:      RAISE;  (inside handler)
Logging:       SQLCODE, SQLERRM
```

---

_Day 6 – PL/SQL Exception Handling: Exception Types, User-Defined Exceptions, RAISE_
