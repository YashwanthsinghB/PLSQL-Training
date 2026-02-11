# PL/SQL Basics I – Day 1 Quiz

## Block Structure, Variables & Data Types

**Instructions:** Answer all 15 questions. Time: 15–20 minutes.

---

### Multiple Choice

**Q1.** What does PL/SQL stand for?

- A) Procedural Language for SQL
- B) Procedural Language extension for SQL
- C) Program Logic Structured Query Language
- D) PL Structured Query Language

---

**Q2.** Which section of a PL/SQL block is mandatory?

- A) DECLARE
- B) BEGIN
- C) EXCEPTION
- D) All of the above

---

**Q3.** What does `NUMBER(7,2)` mean?

- A) 7 digits total, 2 before the decimal
- B) 7 digits total, 2 after the decimal
- C) Maximum value is 7.2
- D) 7-digit integer only

---

**Q4.** `VARCHAR2(100)` allows a string of:

- A) Exactly 100 characters
- B) Up to 100 characters
- C) At least 100 characters
- D) 100 bytes only

---

**Q5.** How does `CHAR(5)` store the value `'Hi'`?

- A) As `'Hi'` (2 characters)
- B) As `'Hi   '` (5 characters with 3 trailing spaces)
- C) As `'  Hi '` (5 characters with padding on both sides)
- D) It causes an error

---

**Q6.** What does `SYSDATE` return?

- A) Only the current date
- B) Only the current time
- C) Current date and time from the database server
- D) A string in format 'DD-MON-YYYY'

---

**Q7.** Which data type has values TRUE, FALSE, or NULL but cannot be used in SQL?

- A) VARCHAR2
- B) NUMBER
- C) BOOLEAN
- D) CHAR

---

**Q8.** The `%TYPE` attribute is used to:

- A) Define a record holding all columns of a row
- B) Make a variable inherit the data type of a table column
- C) Specify the maximum length of a string
- D) Convert one data type to another

---

**Q9.** To display output from a PL/SQL block, you should:

- A) Use PRINT statement
- B) Use DBMS_OUTPUT.PUT_LINE and SET SERVEROUTPUT ON
- C) Use DISPLAY command
- D) Use WRITE function

---

**Q10.** When using SELECT INTO, the query must return:

- A) Zero or one row
- B) Exactly one row
- C) One or more rows
- D) Any number of rows

---

### True or False

**Q11.** An anonymous block is stored in the database and can be called by name.

- True / False

---

**Q12.** The EXCEPTION section of a PL/SQL block is optional.

- True / False

---

**Q13.** `%ROWTYPE` creates a single variable that can hold all columns of a table row.

- True / False

---

### Fill in the Blank / Short Answer

**Q14.** Write the exception names for these cases:

- (a) SELECT INTO returns no rows: ******\_\_\_******
- (b) SELECT INTO returns more than one row: ******\_\_\_******

---

**Q15.** Write the correct order of the four sections in a PL/SQL block:

1. ***
2. ***
3. ***
4. ***

---

## Answer Key

| Q#  | Answer                                  |
| --- | --------------------------------------- |
| 1   | B                                       |
| 2   | B                                       |
| 3   | B                                       |
| 4   | B                                       |
| 5   | B                                       |
| 6   | C                                       |
| 7   | C                                       |
| 8   | B                                       |
| 9   | B                                       |
| 10  | B                                       |
| 11  | False                                   |
| 12  | True                                    |
| 13  | True                                    |
| 14  | (a) NO_DATA_FOUND (b) TOO_MANY_ROWS     |
| 15  | 1. DECLARE 2. BEGIN 3. EXCEPTION 4. END |

---

_Use this quiz to assess understanding at the end of Day 1. Consider going through answers as a group discussion._
