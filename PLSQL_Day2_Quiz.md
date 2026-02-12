# PL/SQL Basics II – Day 2 Quiz

## Control Structures: IF, CASE, LOOP, WHILE, FOR

**Instructions:** Answer all 15 questions. Time: 15–20 minutes.

---

### Multiple Choice

**Q1.** Which of the following is used for conditional branching in PL/SQL?

- A) LOOP
- B) IF
- C) FOR
- D) SELECT

---

**Q2.** In PL/SQL, how do you check if a variable is NULL?

- A) `v_var = NULL`
- B) `v_var IS NULL`
- C) `v_var == NULL`
- D) `NULL(v_var)`

---

**Q3.** What is the correct keyword for the "else if" branch in PL/SQL?

- A) ELSEIF
- B) ELSIF
- C) ELSE IF
- D) ELIF

---

**Q4.** Which CASE type allows different conditions in each WHEN clause (e.g., ranges, multiple variables)?

- A) Simple CASE
- B) Searched CASE
- C) CASE expression
- D) Both A and B

---

**Q5.** A Basic LOOP will run forever unless you use:

- A) STOP
- B) EXIT or EXIT WHEN
- C) BREAK
- D) END LOOP

---

**Q6.** In a WHILE loop, when is the condition checked?

- A) After each iteration
- B) Before each iteration
- C) Only at the start
- D) Never

---

**Q7.** In a FOR loop, can you modify the loop counter variable inside the loop?

- A) Yes, always
- B) No, it is read-only
- C) Only with REVERSE
- D) Only in nested loops

---

**Q8.** What does `FOR i IN REVERSE 1..5` produce?

- A) 1, 2, 3, 4, 5
- B) 5, 4, 3, 2, 1
- C) 0, 1, 2, 3, 4, 5
- D) 5, 4, 3, 2, 1, 0

---

**Q9.** What does CONTINUE do inside a loop?

- A) Exits the loop immediately
- B) Skips the rest of the current iteration and starts the next
- C) Restarts the loop from the beginning
- D) Pauses the loop

---

**Q10.** When you have a known number of iterations (e.g., 1 to 10), which loop is best?

- A) Basic LOOP
- B) WHILE
- C) FOR
- D) All are equal

---

### True or False

**Q11.** In an IF-ELSIF-ELSE structure, only one branch executes—the first one whose condition is TRUE.

- True / False

---

**Q12.** A WHILE loop always executes its body at least once.

- True / False

---

**Q13.** You can use a label with EXIT to exit an outer loop from inside a nested loop.

- True / False

---

### Fill in the Blank / Short Answer

**Q14.** Complete the loop to print numbers 1 to 5:

```sql
FOR i IN ______ LOOP
    DBMS_OUTPUT.PUT_LINE(i);
END LOOP;
```

---

**Q15.** What are the three types of control structures? Give one example of each.

1. ******\_\_\_****** – Example: ******\_\_\_******
2. ******\_\_\_****** – Example: ******\_\_\_******
3. ******\_\_\_****** – Example: ******\_\_\_******

---

## Answer Key

| Q#  | Answer                                                                                                              |
| --- | ------------------------------------------------------------------------------------------------------------------- |
| 1   | B                                                                                                                   |
| 2   | B                                                                                                                   |
| 3   | B                                                                                                                   |
| 4   | B                                                                                                                   |
| 5   | B                                                                                                                   |
| 6   | B                                                                                                                   |
| 7   | B                                                                                                                   |
| 8   | B                                                                                                                   |
| 9   | B                                                                                                                   |
| 10  | C                                                                                                                   |
| 11  | True                                                                                                                |
| 12  | False                                                                                                               |
| 13  | True                                                                                                                |
| 14  | `1..5`                                                                                                              |
| 15  | 1. Sequential – Normal statement flow 2. Selection (Branching) – IF, CASE 3. Iteration (Looping) – LOOP, WHILE, FOR |

---

_Use this quiz to assess understanding at the end of Day 2._
