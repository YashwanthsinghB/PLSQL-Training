# PL/SQL Basics II – Day 2

## Control Structures: IF, CASE, LOOP, WHILE, FOR

**Training Material for Freshers**

---

## INTRODUCTION: WHAT ARE CONTROL STRUCTURES?

**Control structures** determine the order in which statements run. Without them, the program would run from top to bottom with no choices or repetition.

There are three main types:

| Type                      | Purpose                                 | Examples              |
| ------------------------- | --------------------------------------- | --------------------- |
| **Sequential**            | Execute in order                        | Normal statement flow |
| **Selection (Branching)** | Choose one path based on a condition    | IF, CASE              |
| **Iteration (Looping)**   | Repeat a block until a condition is met | LOOP, WHILE, FOR      |

Day 2 focuses on **selection** and **iteration**—how to make decisions and repeat actions in PL/SQL.

---

## LEARNING OBJECTIVES

- Use conditional logic with `IF` and `CASE`
- Apply iterative processing with `LOOP`, `WHILE`, and `FOR`
- Write clear and readable control-flow code
- Prevent infinite loops and handle edge cases
- Apply best practices for control structures

---

## TOPICS COVERED

### Hour 1: IF and CASE (50 min)

#### 1) IF Statement

**Theory**

In real life, we make decisions: _"If it rains, take an umbrella."_ In programming, **conditional execution** lets the program choose which code runs based on a condition (true or false).

- **IF** performs **branching**: only one path is executed.
- The condition is evaluated once. If it is **TRUE**, the block under `THEN` runs; otherwise it is skipped.
- **IF-ELSE** gives two paths: one when true, another when false.
- **IF-ELSIF-ELSE** handles multiple conditions in order. The first TRUE condition runs; if none is true, the `ELSE` block runs.

**Flow:**

```
        ┌─────────────────┐
        │ Evaluate        │
        │ condition       │
        └────────┬────────┘
                 │
         ┌───────┴───────┐
         │  TRUE?        │
         └───────┬───────┘
            Yes  │  No
         ┌──────┴──────┐
         ▼             ▼
    Execute        Skip (or execute
    THEN block     ELSE block)
```

**Syntax:**

```sql
IF condition THEN
    statements;
END IF;
```

**Conditions you can use:**

- Comparison: `=`, `!=`, `<>`, `>`, `<`, `>=`, `<=`
- Logical: `AND`, `OR`, `NOT`
- NULL checks: `IS NULL`, `IS NOT NULL`
- Pattern checks: `LIKE`, `IN`, `BETWEEN`

**Important note about NULL:**  
`v_value = NULL` is **not** true or false. Always use `IS NULL` / `IS NOT NULL`.

**IF-ELSE:**

```sql
IF condition THEN
    statements;
ELSE
    statements;
END IF;
```

**IF-ELSIF-ELSE:**

```sql
IF condition1 THEN
    statements;
ELSIF condition2 THEN
    statements;
ELSE
    statements;
END IF;
```

**Example 1 – Simple IF (positive, negative, or zero):**

```sql
DECLARE
    v_num NUMBER := -5;
BEGIN
    IF v_num > 0 THEN
        DBMS_OUTPUT.PUT_LINE('Positive');
    ELSIF v_num < 0 THEN
        DBMS_OUTPUT.PUT_LINE('Negative');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Zero');
    END IF;
END;
/
-- Output: Negative
```

**Example 2 – Grade based on marks:**

```sql
DECLARE
    v_marks NUMBER := 72;
BEGIN
    IF v_marks >= 85 THEN
        DBMS_OUTPUT.PUT_LINE('Grade: A');
    ELSIF v_marks >= 70 THEN
        DBMS_OUTPUT.PUT_LINE('Grade: B');
    ELSIF v_marks >= 50 THEN
        DBMS_OUTPUT.PUT_LINE('Grade: C');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Grade: D');
    END IF;
END;
/
```

**Example: IF with NULL and range checks**

```sql
DECLARE
    v_salary NUMBER := NULL;
BEGIN
    IF v_salary IS NULL THEN
        DBMS_OUTPUT.PUT_LINE('Salary not entered');
    ELSIF v_salary BETWEEN 0 AND 30000 THEN
        DBMS_OUTPUT.PUT_LINE('Entry-level');
    ELSIF v_salary BETWEEN 30001 AND 70000 THEN
        DBMS_OUTPUT.PUT_LINE('Mid-level');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Senior-level');
    END IF;
END;
/
```

**IF vs CASE (quick rule):**

- Use **IF** for complex or mixed conditions.
- Use **CASE** when many branches depend on one variable.

---

#### 2) CASE Statement

**Theory**

**CASE** is used for **multi-way branching**: when one expression or variable can have several values and each value leads to a different action. It is like a lookup table in code.

- **Simple CASE:** Compares one variable to a list of literal values. Oracle checks each `WHEN` in order and runs the first match.
- **Searched CASE:** Each `WHEN` has its own condition (like separate IFs). More flexible for ranges, multiple columns, or complex logic.
- **CASE expression:** Returns a value instead of executing statements. You can assign the result to a variable.

**When to use CASE vs IF:**

| Use CASE when                                | Use IF when                                       |
| -------------------------------------------- | ------------------------------------------------- |
| One variable, many possible values           | Different conditions (ranges, multiple variables) |
| You want readable multi-way logic            | Logic is complex or mixed                         |
| You need to assign a value (CASE expression) | Conditions are unrelated                          |

**Flow (Simple CASE):**

```
        ┌─────────────────┐
        │ variable = val1?│──Yes──► Execute branch 1
        └────────┬────────┘
                 No
        ┌────────┴────────┐
        │ variable = val2?│──Yes──► Execute branch 2
        └────────┬────────┘
                 No
        ┌────────┴────────┐
        │      ...        │
        └────────┬────────┘
                 │
                 └──────────► Execute ELSE (default)
```

**Simple CASE:**

```sql
CASE variable
    WHEN value1 THEN statements;
    WHEN value2 THEN statements;
    ELSE statements;
END CASE;
```

**Searched CASE:**

```sql
CASE
    WHEN condition1 THEN statements;
    WHEN condition2 THEN statements;
    ELSE statements;
END CASE;
```

**Example 1 – Simple CASE (grade to message):**

```sql
DECLARE
    v_grade CHAR(1) := 'B';
BEGIN
    CASE v_grade
        WHEN 'A' THEN DBMS_OUTPUT.PUT_LINE('Excellent');
        WHEN 'B' THEN DBMS_OUTPUT.PUT_LINE('Good');
        WHEN 'C' THEN DBMS_OUTPUT.PUT_LINE('Average');
        ELSE DBMS_OUTPUT.PUT_LINE('Needs Improvement');
    END CASE;
END;
/
```

**Example 2 – Searched CASE (weekday number to name):**

```sql
DECLARE
    v_day_num NUMBER := 3;
    v_day_name VARCHAR2(20);
BEGIN
    CASE
        WHEN v_day_num = 1 THEN v_day_name := 'Sunday';
        WHEN v_day_num = 2 THEN v_day_name := 'Monday';
        WHEN v_day_num = 3 THEN v_day_name := 'Tuesday';
        WHEN v_day_num = 4 THEN v_day_name := 'Wednesday';
        WHEN v_day_num = 5 THEN v_day_name := 'Thursday';
        WHEN v_day_num = 6 THEN v_day_name := 'Friday';
        WHEN v_day_num = 7 THEN v_day_name := 'Saturday';
        ELSE v_day_name := 'Invalid';
    END CASE;
    DBMS_OUTPUT.PUT_LINE('Day: ' || v_day_name);
END;
/
-- Output: Day: Tuesday
```

**Example 3 – CASE expression (assigning a value):**

```sql
DECLARE
    v_marks NUMBER := 82;
    v_grade CHAR(1);
BEGIN
    v_grade :=
        CASE
            WHEN v_marks >= 85 THEN 'A'
            WHEN v_marks >= 70 THEN 'B'
            WHEN v_marks >= 50 THEN 'C'
            ELSE 'D'
        END;
    DBMS_OUTPUT.PUT_LINE('Grade: ' || v_grade);
END;
/
```

**CASE vs IF (decision guide):**

| Scenario                    | Best Choice       |
| --------------------------- | ----------------- |
| Many checks on one variable | `CASE`            |
| Complex condition logic     | `IF`              |
| Need to return a value      | `CASE` expression |

---

### Hour 2: Loops (60 min)

#### 1) Basic LOOP

**Theory**

**Loops** repeat a block of statements. Instead of writing the same code many times, you put it in a loop and let it run until a condition is met.

- **Basic LOOP** is an **unconditional loop**: it has no condition at the start. It runs forever until you explicitly stop it with `EXIT` or `EXIT WHEN`.
- You must provide an exit condition; otherwise it becomes an **infinite loop** and the program never ends.
- Use when the stopping condition depends on something computed inside the loop (e.g., sum, user input, or a search result).

**Flow:**

```
        ┌─────────────────┐
        │  Execute        │
        │  loop body      │
        └────────┬────────┘
                 │
        ┌────────┴────────┐
        │ EXIT WHEN       │
        │ condition?      │
        └────────┬────────┘
            No   │   Yes
        ┌────────┴────────┐
        │  (repeat)       │  (exit loop)
        └─────────────────┘
```

```sql
LOOP
    statements;
    EXIT WHEN condition;
END LOOP;
```

**Key points:**

- Use `EXIT WHEN` to stop the loop.
- Without EXIT, it becomes an infinite loop.
- You can also use `EXIT;` inside an `IF` block.

**Example:**

```sql
DECLARE
    v_count NUMBER := 1;
BEGIN
    LOOP
        DBMS_OUTPUT.PUT_LINE('Count: ' || v_count);
        v_count := v_count + 1;
        EXIT WHEN v_count > 5;
    END LOOP;
END;
/
```

**Example: EXIT with IF**

```sql
DECLARE
    v_total NUMBER := 0;
    v_num   NUMBER := 1;
BEGIN
    LOOP
        v_total := v_total + v_num;
        IF v_total >= 15 THEN
            EXIT;
        END IF;
        v_num := v_num + 1;
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('Total: ' || v_total);
END;
/
```

---

#### 2) WHILE LOOP

**Theory**

**WHILE** is an **entry-controlled loop**: the condition is checked **before** each iteration. If it is FALSE at the start, the loop body may not run at all.

- The condition is tested at the **beginning** of every iteration.
- If TRUE → execute body, then check again.
- If FALSE → exit the loop immediately.
- Use when you do **not** know in advance how many times to repeat (e.g., “keep trying until found” or “repeat while valid”).

**Basic LOOP vs WHILE:**

| Basic LOOP                                       | WHILE LOOP                        |
| ------------------------------------------------ | --------------------------------- |
| No condition at start                            | Condition at start                |
| Body runs at least once (if EXIT is inside body) | Body may never run (0 iterations) |
| Exit via EXIT WHEN                               | Exit when condition becomes FALSE |

**Flow:**

```
        ┌─────────────────┐
        │ Check           │
        │ condition       │
        └────────┬────────┘
                 │
         ┌───────┴───────┐
         │  TRUE?        │
         └───────┬───────┘
            Yes  │  No (exit)
         ┌──────┴──────┐
         ▼
    Execute body
         │
         └──────► (loop back to check)
```

```sql
WHILE condition LOOP
    statements;
END LOOP;
```

**When to use WHILE:**

- You don’t know how many times the loop will run.
- You want to check the condition **before** the first iteration.

**Example:**

```sql
DECLARE
    v_count NUMBER := 1;
BEGIN
    WHILE v_count <= 5 LOOP
        DBMS_OUTPUT.PUT_LINE('Count: ' || v_count);
        v_count := v_count + 1;
    END LOOP;
END;
/
```

**Example 1 – Find first number divisible by 7:**

```sql
DECLARE
    v_num NUMBER := 1;
BEGIN
    WHILE MOD(v_num, 7) != 0 LOOP
        v_num := v_num + 1;
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('First divisible by 7: ' || v_num);
END;
/
-- Output: First divisible by 7: 7
```

**Example 2 – Count digits in a number:**

```sql
DECLARE
    v_num    NUMBER := 12345;
    v_count  NUMBER := 0;
    v_temp   NUMBER;
BEGIN
    v_temp := v_num;
    WHILE v_temp > 0 LOOP
        v_count := v_count + 1;
        v_temp := FLOOR(v_temp / 10);
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('Digits in ' || v_num || ': ' || v_count);
END;
/
-- Output: Digits in 12345: 5
```

---

#### 3) FOR LOOP

**Theory**

**FOR** is a **count-controlled loop**: the number of iterations is known. A counter runs from a start value to an end value, and the body executes once for each value.

- The loop variable (counter) is created automatically. You **cannot** assign to it inside the loop; it is read-only.
- `start_value..end_value` defines the range (inclusive). For `1..5`, the counter takes 1, 2, 3, 4, 5.
- Use when you know exactly how many times to repeat (e.g., process each of 10 rows, print 1 to N).
- **REVERSE** counts backward: `REVERSE 1..5` gives 5, 4, 3, 2, 1.

**Choosing a loop:**

| Situation                             | Best loop               |
| ------------------------------------- | ----------------------- |
| Known number of iterations            | FOR                     |
| Unknown iterations, check before each | WHILE                   |
| Unknown iterations, check inside body | Basic LOOP              |
| Need to skip current iteration        | Use CONTINUE (any loop) |

**Flow:**

```
        ┌─────────────────┐
        │ counter = start │
        └────────┬────────┘
                 │
        ┌────────┴────────┐
        │ counter <= end? │
        └────────┬────────┘
            Yes  │   No (exit)
         ┌──────┴──────┐
         ▼
    Execute body
         │
         ▼
    counter := counter + 1
         │
         └──────► (loop back)
```

```sql
FOR counter IN start_value..end_value LOOP
    statements;
END LOOP;
```

**Key points:**

- Loop counter is created automatically and is **read-only**.
- Executes a fixed number of times.
- Best for known ranges.

**Example:**

```sql
BEGIN
    FOR i IN 1..5 LOOP
        DBMS_OUTPUT.PUT_LINE('Count: ' || i);
    END LOOP;
END;
/
```

**Reverse FOR loop:**

```sql
BEGIN
    FOR i IN REVERSE 1..5 LOOP
        DBMS_OUTPUT.PUT_LINE('Reverse: ' || i);
    END LOOP;
END;
/
```

**Example 1 – Sum of squares:**

```sql
DECLARE
    v_sum NUMBER := 0;
BEGIN
    FOR i IN 1..5 LOOP
        v_sum := v_sum + (i * i);
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('Sum of squares: ' || v_sum);
END;
/
-- Output: Sum of squares: 55  (1²+2²+3²+4²+5²)
```

**Example 2 – Factorial (real-world use):**

```sql
DECLARE
    v_n    NUMBER := 5;
    v_fact NUMBER := 1;
BEGIN
    FOR i IN 1..v_n LOOP
        v_fact := v_fact * i;
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('Factorial of ' || v_n || ' = ' || v_fact);
END;
/
-- Output: Factorial of 5 = 120
```

---

#### 4) CONTINUE and Labels (Advanced)

**Theory**

**CONTINUE** skips the rest of the current iteration and jumps to the next one. It does **not** exit the loop—it only skips the remaining code in the current pass. Use it when you want to ignore certain cases (e.g., skip even numbers, invalid data).

**Labels** name a block or loop (e.g., `<<outer_loop>>`). With `EXIT label_name`, you can exit an outer loop from inside a nested loop. Without a label, `EXIT` only exits the innermost loop. Labels make nested loop control clearer and safer.

**CONTINUE flow:**

```
    Loop iteration N
        │
        ├── Statement 1
        ├── Statement 2
        ├── CONTINUE;  ──────► Skip to next iteration
        ├── Statement 3      (not executed)
        └── Statement 4      (not executed)
```

**CONTINUE** skips the rest of the current loop iteration.

```sql
BEGIN
    FOR i IN 1..6 LOOP
        IF MOD(i, 2) = 0 THEN
            CONTINUE; -- Skip even numbers
        END IF;
        DBMS_OUTPUT.PUT_LINE('Odd: ' || i);
    END LOOP;
END;
/
```

**Labels** help control nested loops:

```sql
DECLARE
    v_count NUMBER := 0;
BEGIN
    <<outer_loop>>
    FOR i IN 1..3 LOOP
        FOR j IN 1..3 LOOP
            v_count := v_count + 1;
            IF v_count = 5 THEN
                EXIT outer_loop; -- Exit both loops
            END IF;
        END LOOP;
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('Count: ' || v_count);
END;
/
```

---

### Hour 3: Practice & Application (50 min)

- Apply IF, CASE, and loops to real problems
- Debugging control flow (off-by-one, infinite loops)
- Code review for readability and correctness
- Best practices in choosing the right structure
- Translate business rules into clear conditions
- Refactor nested logic into smaller readable blocks

---

## HANDS-ON EXERCISES

1. **IF Practice:** Check if a number is positive, negative, or zero.
2. **IF-ELSIF:** Convert marks to grade using conditions.
3. **CASE Practice:** Map a weekday number (1–7) to weekday name.
4. **Basic LOOP:** Print numbers 1 to 10 with EXIT WHEN.
5. **WHILE LOOP:** Sum numbers from 1 to 50.
6. **FOR LOOP:** Print squares of numbers 1 to 10.
7. **Reverse FOR:** Print 10 down to 1.
8. **Nested IF:** Check salary range and print a bonus category.
9. **CASE (Searched):** Categorize age into child, adult, senior.
10. **Mini Project:** Generate a multiplication table (1–10) using nested loops.

---

## BEST PRACTICES

- Use `IF` when conditions are complex, `CASE` for cleaner multi-branch logic.
- Always ensure loops have a clear exit condition.
- Avoid hardcoding magic numbers; use named variables or constants.
- Keep nested logic shallow when possible; refactor into smaller blocks.
- Add `DBMS_OUTPUT` statements for debugging during practice.
- Check NULLs using `IS NULL` / `IS NOT NULL`.
- In FOR loops, avoid modifying the loop counter.

---

## COMMON PITFALLS

- **Using `=` with NULL** (should be `IS NULL`)
- **Infinite loops** due to missing `EXIT` or incorrect condition
- **Off-by-one errors** (incorrect start/end values)
- **Over-nesting** makes logic hard to read and maintain
- **Using CASE when IF is clearer** (and vice versa)

---

## QUICK REFERENCE

```
IF condition THEN ... END IF;
IF ... ELSIF ... ELSE ... END IF;
CASE var WHEN v1 THEN ... ELSE ... END CASE;
CASE WHEN cond THEN ... END CASE;
LOOP ... EXIT WHEN cond; END LOOP;
LOOP ... IF cond THEN EXIT; END IF; END LOOP;
WHILE cond LOOP ... END LOOP;
FOR i IN 1..N LOOP ... END LOOP;
FOR i IN REVERSE 1..N LOOP ... END LOOP;
```

---

_Day 2 – PL/SQL Basics II: Control Structures_
