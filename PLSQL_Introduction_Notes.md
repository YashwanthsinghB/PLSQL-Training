# PL/SQL Basics I – Day 1

## Block Structure, Variables & Data Types

**Training Material for Freshers**

---

## LEARNING OBJECTIVES

- Master PL/SQL Basics I in PL/SQL
- Write efficient procedural code
- Implement proper error handling
- Build reusable code components
- Apply PL/SQL best practices

---

## TOPICS COVERED

### Hour 1: Block Structure, Variables, Data Types Introduction (50 min)

#### What is PL/SQL? Advantages over SQL

**PL/SQL** = Procedural Language extension for SQL. Oracle's proprietary language that extends SQL with:

| PL/SQL Capability            | SQL Limitation            |
| ---------------------------- | ------------------------- |
| Variables, loops, conditions | Single statements only    |
| Procedural logic             | Set-based operations only |
| Error handling (EXCEPTION)   | Limited error control     |
| Reusable blocks              | No procedural reuse       |
| Server-side execution        | Multiple round-trips      |

**Key advantages of PL/SQL over SQL:**

1. **Reduced network traffic** – Execute multiple statements in one call
2. **Procedural power** – Loops, branching, variables
3. **Better performance** – Compiled and cached on server
4. **Encapsulation** – Store logic in procedures/functions
5. **Integration** – Seamless SQL embedding

---

#### PL/SQL Architecture and Engine

```
┌─────────────────────────────────────────────────────────┐
│                    PL/SQL Engine                         │
│  ┌─────────────────┐    ┌─────────────────────────┐    │
│  │ Procedural      │    │ SQL Statement           │    │
│  │ Statement       │    │ Executor                │    │
│  │ Executor        │    │ (SELECT, INSERT, etc.)  │    │
│  └────────┬────────┘    └────────────┬────────────┘    │
│           │                          │                  │
│           └──────────┬───────────────┘                  │
│                      ▼                                  │
│              Oracle Database (SQL Engine)               │
└─────────────────────────────────────────────────────────┘
```

- **Procedural Statement Executor** – Handles variables, loops, IF, assignment
- **SQL Statement Executor** – Parses and executes embedded SQL
- **Anonymous block** – Parsed, compiled, executed each time; not stored
- **Named block** – Stored in data dictionary; compiled once, executed many times

---

#### Anonymous Blocks vs Named Blocks

| Feature      | Anonymous Block            | Named Block                              |
| ------------ | -------------------------- | ---------------------------------------- |
| **Storage**  | Not stored in DB           | Stored (Procedure, Function, Trigger)    |
| **Reuse**    | Run once per execution     | Reusable; call by name                   |
| **Use case** | Scripts, ad-hoc, testing   | Production logic, APIs                   |
| **Syntax**   | DECLARE ... BEGIN ... END; | CREATE OR REPLACE PROCEDURE/FUNCTION ... |

---

#### Block Structure: DECLARE, BEGIN, EXCEPTION, END

Every PL/SQL program is a **block**. Four sections:

```sql
DECLARE
    -- Optional: Variables, constants
    v_name   VARCHAR2(50);
    v_count  NUMBER := 0;
BEGIN
    -- Mandatory: Executable logic
    v_name := 'John';
    v_count := 10;
    DBMS_OUTPUT.PUT_LINE(v_name || ' - ' || v_count);
EXCEPTION
    -- Optional: Error handling
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
END;
/
```

| Section       | Purpose               | Mandatory? |
| ------------- | --------------------- | ---------- |
| **DECLARE**   | Variables, constants  | No         |
| **BEGIN**     | Executable statements | Yes        |
| **EXCEPTION** | Handle errors         | No         |
| **END**       | End of block          | Yes        |

> **Tip:** Run `SET SERVEROUTPUT ON;` before blocks to see output from `DBMS_OUTPUT.PUT_LINE`.

---

#### Hands-on: Basic Exercises

- Write a minimal block: `BEGIN NULL; END;`
- Add DECLARE with one variable, assign and print
- Add EXCEPTION section with `WHEN OTHERS`

---

### Hour 2: Advanced Block Structure, Variables, Data Types (60 min)

#### Variable Declaration and Data Types

**Syntax:** `variable_name data_type [:= default_value];`

```sql
DECLARE
    v_count      NUMBER;
    v_name       VARCHAR2(100) := 'Unknown';
    v_flag       BOOLEAN := TRUE;
    v_hiredate   DATE := SYSDATE;
    v_constant   CONSTANT NUMBER := 3.14;  -- Cannot be changed
BEGIN
    v_count := 10;
    DBMS_OUTPUT.PUT_LINE('Count: ' || v_count);
END;
/
```

---

#### Data Types – Detailed Reference

---

##### 1. NUMBER

Stores numeric values (integers and decimals). Default precision is up to 38 digits.

| Declaration   | Meaning                                            | Example Values                                 | Use Case                                        |
| ------------- | -------------------------------------------------- | ---------------------------------------------- | ----------------------------------------------- |
| `NUMBER`      | Any numeric value, up to 38 digits                 | `5`, `-100`, `3.14159`, `12345678901234567890` | General purpose; when precision is not critical |
| `NUMBER(p)`   | Integer with exactly **p** total digits            | `NUMBER(5)` → `12345`, `-99999`                | IDs, counts, whole numbers                      |
| `NUMBER(p,s)` | **p** = total digits, **s** = digits after decimal | `NUMBER(7,2)` → `12345.67`                     | Money, measurements, percentages                |

**NUMBER(7,2) explained:**

- **7** = total number of digits (precision)
- **2** = digits after decimal point (scale)
- Max value: `99999.99` (5 before + 2 after = 7 total)
- Min value: `-99999.99`

```sql
DECLARE
    v_amount     NUMBER(7,2) := 12345.67;   -- Salary, price
    v_count      NUMBER(5)   := 100;        -- Integer count
    v_percentage NUMBER(5,2) := 18.50;      -- Tax rate
    v_any        NUMBER;                    -- No limit
BEGIN
    DBMS_OUTPUT.PUT_LINE('Amount: ' || v_amount);      -- 12345.67
    DBMS_OUTPUT.PUT_LINE('Count: ' || v_count);        -- 100
    DBMS_OUTPUT.PUT_LINE('Percentage: ' || v_percentage);  -- 18.5
END;
/
```

---

##### 2. VARCHAR2(n)

Variable-length character string. Stores only the actual characters used (no padding).

| Part         | Meaning                                                              |
| ------------ | -------------------------------------------------------------------- |
| **VARCHAR2** | Variable-length string type                                          |
| **(n)**      | **Maximum length** in bytes (or characters, depending on DB setting) |
| **Max n**    | 32,767 bytes in PL/SQL; 4,000 bytes in table columns (Oracle 12c+)   |

**VARCHAR2(100) means:** This variable can hold a string of **up to 100 characters**. If you store `'Hello'`, only 5 characters are used; the rest is not allocated.

```sql
DECLARE
    v_name   VARCHAR2(100) := 'John Doe';     -- Uses 8 chars
    v_city   VARCHAR2(50)  := 'Mumbai';       -- Uses 6 chars
    v_long   VARCHAR2(4000);                  -- Max for table columns
BEGIN
    DBMS_OUTPUT.PUT_LINE('Name: ' || v_name);
    v_name := 'A';  -- OK: short string
    -- v_name := 'X' || RPAD(' ', 100, 'Y');  -- Would exceed 100 - error
END;
/
```

**Use cases:** Names, addresses, descriptions, any text with variable length.

---

##### 3. CHAR(n)

Fixed-length character string. Always allocates **n** characters and **pads with spaces** if the value is shorter.

| Part      | Meaning                                                |
| --------- | ------------------------------------------------------ |
| **CHAR**  | Fixed-length string                                    |
| **(n)**   | **Always** n characters; padded with spaces if shorter |
| **Max n** | 2,000 bytes                                            |

**CHAR(10) with 'Hi':** Stored as `'Hi        '` (8 trailing spaces).

```sql
DECLARE
    v_code CHAR(3)  := 'A';      -- Stored as 'A  ' (2 spaces)
    v_flag CHAR(1)  := 'Y';      -- Single char: 'Y'
BEGIN
    DBMS_OUTPUT.PUT_LINE('Code: [' || v_code || ']');  -- [A  ]
    IF v_flag = 'Y' THEN
        DBMS_OUTPUT.PUT_LINE('Flag is Yes');
    END IF;
END;
/
```

**VARCHAR2 vs CHAR:**

| VARCHAR2(10)          | CHAR(10)                                        |
| --------------------- | ----------------------------------------------- |
| `'Hi'` → 2 chars used | `'Hi'` → 10 chars (8 spaces added)              |
| Variable storage      | Fixed storage                                   |
| Use for most text     | Use for fixed codes (e.g. country code, status) |

**Use cases:** Status codes (`'A'`, `'I'`), country codes (`'IN'`), fixed-format IDs.

---

##### 4. DATE

Stores date and time (no separate time type in older Oracle). Includes century, year, month, day, hour, minute, second.

**SYSDATE** – Built-in function that returns the **current date and time** from the database server.

```sql
DECLARE
    v_today     DATE := SYSDATE;
    v_hiredate  DATE := TO_DATE('15-JAN-2024', 'DD-MON-YYYY');
    v_future    DATE := SYSDATE + 7;   -- 7 days from now
BEGIN
    DBMS_OUTPUT.PUT_LINE('Today: ' || TO_CHAR(v_today, 'DD-MON-YYYY'));
    DBMS_OUTPUT.PUT_LINE('Time: ' || TO_CHAR(v_today, 'HH24:MI:SS'));
    DBMS_OUTPUT.PUT_LINE('Hire Date: ' || TO_CHAR(v_hiredate, 'DD/MM/YYYY'));
    DBMS_OUTPUT.PUT_LINE('Next Week: ' || TO_CHAR(v_future, 'DD-MON-YYYY'));
END;
/
```

**Common date operations:**

```sql
SYSDATE                    -- Current date/time
SYSDATE + 1                -- Tomorrow
SYSDATE - 7                -- 7 days ago
TRUNC(SYSDATE)             -- Today at midnight (no time)
TO_DATE('2024-01-15', 'YYYY-MM-DD')   -- String to date
TO_CHAR(v_date, 'DD-MON-YYYY')        -- Date to string for display
```

**Use cases:** Hire dates, order dates, birth dates, scheduling.

---

##### 5. BOOLEAN

Logical type with three possible values: **TRUE**, **FALSE**, or **NULL**. Used for conditions; **cannot** be printed directly or used in SQL.

```sql
DECLARE
    v_active  BOOLEAN := TRUE;
    v_found   BOOLEAN;
BEGIN
    IF v_active THEN
        DBMS_OUTPUT.PUT_LINE('Status: Active');
    END IF;

    v_found := FALSE;
    IF NOT v_found THEN
        DBMS_OUTPUT.PUT_LINE('Not found');
    END IF;

    -- Cannot do: DBMS_OUTPUT.PUT_LINE(v_active);  -- ERROR
    -- Must convert for display:
    DBMS_OUTPUT.PUT_LINE(CASE WHEN v_active THEN 'TRUE' ELSE 'FALSE' END);
END;
/
```

**Use cases:** Flags, validation results, loop/condition control.

---

##### 6. %TYPE and %ROWTYPE – Difference

| Attribute    | What it does                                                | Use for       | Access                                       |
| ------------ | ----------------------------------------------------------- | ------------- | -------------------------------------------- |
| **%TYPE**    | Variable gets the **same type as one column**               | Single column | Direct: `v_name`                             |
| **%ROWTYPE** | Variable gets the **structure of a full row** (all columns) | Entire row    | Dot notation: `v_emp.emp_id`, `v_emp.salary` |

**%TYPE – Single column:**

```sql
v_emp_name  employees.emp_name%TYPE;   -- Same as emp_name column
v_salary    employees.salary%TYPE;     -- Same as salary column
-- You need one variable per column
```

**%ROWTYPE – Whole row (record):**

```sql
v_emp  employees%ROWTYPE;   -- One variable holds ALL columns of a row
-- Access: v_emp.emp_id, v_emp.emp_name, v_emp.salary, v_emp.hire_date, etc.
```

**Comparison:**

```sql
-- Using %TYPE (multiple variables)
DECLARE
    v_id     employees.emp_id%TYPE;
    v_name   employees.emp_name%TYPE;
    v_sal    employees.salary%TYPE;
BEGIN
    SELECT emp_id, emp_name, salary INTO v_id, v_name, v_sal
    FROM employees WHERE emp_id = 101;
    DBMS_OUTPUT.PUT_LINE(v_id || ' - ' || v_name || ' - ' || v_sal);
END;
/

-- Using %ROWTYPE (one record variable)
DECLARE
    v_emp employees%ROWTYPE;
BEGIN
    SELECT * INTO v_emp FROM employees WHERE emp_id = 101;
    DBMS_OUTPUT.PUT_LINE(v_emp.emp_id || ' - ' || v_emp.emp_name || ' - ' || v_emp.salary);
END;
/
```

**When to use which:**

- **%TYPE:** When you need only a few columns
- **%ROWTYPE:** When you fetch a full row (SELECT \*) or need many columns

---

##### Data Types Quick Reference

| Type          | Max Length / Range      | Example         | Typical Use       |
| ------------- | ----------------------- | --------------- | ----------------- |
| `NUMBER`      | 38 digits               | `NUMBER`        | Any number        |
| `NUMBER(p)`   | p digits (integer)      | `NUMBER(5)`     | IDs, counts       |
| `NUMBER(p,s)` | p total, s decimal      | `NUMBER(7,2)`   | Money, rates      |
| `VARCHAR2(n)` | Up to n chars           | `VARCHAR2(100)` | Names, text       |
| `CHAR(n)`     | Always n chars (padded) | `CHAR(3)`       | Codes, flags      |
| `DATE`        | Date + time             | `SYSDATE`       | Dates, timestamps |
| `BOOLEAN`     | TRUE/FALSE/NULL         | `BOOLEAN`       | Conditions, flags |
| `%TYPE`       | Same as column          | `col%TYPE`      | Single column     |
| `%ROWTYPE`    | Full row structure      | `table%ROWTYPE` | Whole row         |

---

#### DBMS_OUTPUT.PUT_LINE

```sql
DBMS_OUTPUT.PUT_LINE('Text');
DBMS_OUTPUT.PUT_LINE('Name: ' || v_name);
DBMS_OUTPUT.PUT_LINE('Salary: ' || TO_CHAR(v_salary));
```

- Use `||` for concatenation
- Convert numbers/dates to strings with `TO_CHAR()` when needed

---

#### SELECT INTO Statement

Fetch one row into variables. **Must return exactly one row.**

```sql
SELECT column1, column2
INTO   variable1, variable2
FROM   table_name
WHERE  condition;
```

```sql
DECLARE
    v_name   employees.emp_name%TYPE;
    v_salary employees.salary%TYPE;
BEGIN
    SELECT emp_name, salary INTO v_name, v_salary
    FROM employees
    WHERE emp_id = 101;
    DBMS_OUTPUT.PUT_LINE(v_name || ' - ' || v_salary);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee not found');
    WHEN TOO_MANY_ROWS THEN
        DBMS_OUTPUT.PUT_LINE('Multiple rows returned');
END;
/
```

---

#### Hands-on: Complex Scenarios

- Use %TYPE for multiple columns from a table
- Use %ROWTYPE and print fields from the record
- SELECT INTO with NO_DATA_FOUND and TOO_MANY_ROWS handling

---

### Hour 3: Practice & Application (50 min)

- Comprehensive coding exercises (see below)
- Debugging PL/SQL (check syntax, use `DBMS_OUTPUT`, trace variable values)
- Code review (naming, %TYPE usage, exception handling)
- Real-world: employee lookup, simple calculations, data validation
- Best practices: use %TYPE/%ROWTYPE, handle exceptions, name variables clearly (`v_`, `p_`)

---

## HANDS-ON EXERCISES

### Exercise 1: Basic Block Structure Example

Write an anonymous block that declares two variables (`v_a` and `v_b`), assigns values, adds them, and prints the result.

<details>
<summary>Solution</summary>

```sql
SET SERVEROUTPUT ON;
DECLARE
    v_a NUMBER := 10;
    v_b NUMBER := 20;
    v_sum NUMBER;
BEGIN
    v_sum := v_a + v_b;
    DBMS_OUTPUT.PUT_LINE('Sum: ' || v_sum);
END;
/
```

</details>

---

### Exercise 2: PL/SQL Basics I with Parameters (Variables)

Using variables as "parameters," write a block that accepts a product price and tax rate, computes tax and total, and prints both.

<details>
<summary>Solution</summary>

```sql
DECLARE
    v_price    NUMBER := 100;
    v_tax_rate NUMBER := 0.18;
    v_tax      NUMBER;
    v_total    NUMBER;
BEGIN
    v_tax := v_price * v_tax_rate;
    v_total := v_price + v_tax;
    DBMS_OUTPUT.PUT_LINE('Tax: ' || v_tax || ', Total: ' || v_total);
END;
/
```

</details>

---

### Exercise 3: Error Handling Implementation

Write a block that uses SELECT INTO to fetch an employee by ID. Handle NO_DATA_FOUND and TOO_MANY_ROWS, and use WHEN OTHERS as a fallback.

<details>
<summary>Solution</summary>

```sql
DECLARE
    v_emp_id   NUMBER := 999;
    v_emp_name employees.emp_name%TYPE;
BEGIN
    SELECT emp_name INTO v_emp_name
    FROM employees WHERE emp_id = v_emp_id;
    DBMS_OUTPUT.PUT_LINE('Name: ' || v_emp_name);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee not found');
    WHEN TOO_MANY_ROWS THEN
        DBMS_OUTPUT.PUT_LINE('Multiple employees found');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
END;
/
```

</details>

---

### Exercise 4: Complex Logic Building

Using variables and data types only (no IF/LOOP yet), compute: area of a circle (`π * r²`), declare `PI` as CONSTANT, and print the result.

<details>
<summary>Solution</summary>

```sql
DECLARE
    PI CONSTANT NUMBER := 3.14159;
    v_radius NUMBER := 5;
    v_area   NUMBER;
BEGIN
    v_area := PI * v_radius * v_radius;
    DBMS_OUTPUT.PUT_LINE('Area: ' || ROUND(v_area, 2));
END;
/
```

</details>

---

### Exercise 5: Integration with SQL Queries

Use SELECT INTO with %ROWTYPE to fetch one employee row and print all relevant columns (e.g., emp_id, emp_name, salary, hire_date).

<details>
<summary>Solution</summary>

```sql
DECLARE
    v_emp employees%ROWTYPE;
BEGIN
    SELECT * INTO v_emp
    FROM employees
    WHERE emp_id = 101;
    DBMS_OUTPUT.PUT_LINE(v_emp.emp_id || ' - ' || v_emp.emp_name ||
                         ' - ' || v_emp.salary || ' - ' || v_emp.hire_date);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Not found');
END;
/
```

</details>

---

### Exercise 6: Reusable Code Component

Create an anonymous block that could be turned into a procedure later: it takes an employee ID via a variable, fetches name and salary using %TYPE, and prints them. Include exception handling.

<details>
<summary>Solution</summary>

```sql
DECLARE
    p_emp_id   NUMBER := 101;  -- Simulated parameter
    v_emp_name employees.emp_name%TYPE;
    v_salary   employees.salary%TYPE;
BEGIN
    SELECT emp_name, salary INTO v_emp_name, v_salary
    FROM employees WHERE emp_id = p_emp_id;
    DBMS_OUTPUT.PUT_LINE('Name: ' || v_emp_name || ', Salary: ' || v_salary);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee ' || p_emp_id || ' not found');
END;
/
```

</details>

---

### Exercise 7: Performance Optimization (Variable Best Practices)

Refactor a block that uses hardcoded types (e.g., `VARCHAR2(100)`) to use %TYPE and %ROWTYPE. Show before/after.

**Before:** `v_name VARCHAR2(100);`  
**After:** `v_name employees.emp_name%TYPE;`

---

### Exercise 8: Debugging Practice

Given a block with a bug (e.g., wrong variable name, missing INTO, type mismatch), identify and fix it. Use DBMS_OUTPUT to trace values.

---

### Exercise 9: Advanced Pattern Implementation

Write a block that uses SELECT INTO to get aggregate data (e.g., COUNT, AVG) into variables and prints formatted output with TO_CHAR.

<details>
<summary>Solution</summary>

```sql
DECLARE
    v_count      NUMBER;
    v_avg_salary NUMBER;
BEGIN
    SELECT COUNT(*), AVG(salary) INTO v_count, v_avg_salary
    FROM employees;
    DBMS_OUTPUT.PUT_LINE('Employees: ' || v_count);
    DBMS_OUTPUT.PUT_LINE('Avg Salary: ' || TO_CHAR(v_avg_salary, '99999.99'));
END;
/
```

</details>

---

### Exercise 10: Complete PL/SQL Basics I Project

Build a block that:

1. Declares variables using %TYPE and %ROWTYPE
2. Uses SELECT INTO to fetch employee data
3. Performs a calculation (e.g., bonus = salary \* 0.1)
4. Uses DBMS_OUTPUT to display results
5. Handles NO_DATA_FOUND and OTHERS in EXCEPTION

---

## RESOURCES PROVIDED

- **PL/SQL Basics I Guide** – This document
- **Code Examples and Patterns** – Solutions in exercise sections
- **Best Practices**
  - Use %TYPE and %ROWTYPE instead of hardcoded types
  - Always handle NO_DATA_FOUND for SELECT INTO
  - Use clear naming: `v_` for variables, `p_` for parameters, `c_` for constants
  - Enable `SET SERVEROUTPUT ON` when using DBMS_OUTPUT
- **Debugging Tips**
  - Add DBMS_OUTPUT.PUT_LINE at key points
  - Check for NULL in variables
  - Verify SELECT returns exactly one row for SELECT INTO

---

## HOMEWORK / PREPARATION

- [ ] Practice 15 programs covering block structure, variables, and data types
- [ ] Build a code library with your working examples
- [ ] Complete all 10 hands-on exercises
- [ ] Review and refactor your code for clarity and best practices
- [ ] Create short notes on: block structure, %TYPE vs %ROWTYPE, SELECT INTO rules, common exceptions

---

## Quick Reference – Day 1

```
Block:    DECLARE ... BEGIN ... EXCEPTION ... END;
Variable: name data_type [:= value];
%TYPE:    var_name table.column%TYPE;
%ROWTYPE: rec_name table%ROWTYPE;
Output:   DBMS_OUTPUT.PUT_LINE('text' || variable);
Fetch:    SELECT col INTO var FROM table WHERE ...;
Exceptions: NO_DATA_FOUND, TOO_MANY_ROWS, OTHERS
```

---

_Day 1 – PL/SQL Basics I: Block Structure, Variables, Data Types_
