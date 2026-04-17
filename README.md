# SQL Joins — A Complete Guide

> Everything you need to understand how tables connect — explained step by step with examples, diagrams, and real syntax.

---

## 📌 What is a JOIN?

In SQL, a **JOIN** is used to combine rows from two or more tables based on a related column between them. Real-world databases almost never store everything in one table. A customer's orders, their addresses, and their products all live in separate tables. JOINs are how you ask a *single question* that spans all of them.

---

## 🗂️ Types of SQL Joins

| # | Join Type | Description |
|---|-----------|-------------|
| 1 | **INNER JOIN** | Only rows that match in both tables |
| 2 | **LEFT JOIN** | All rows from left + matching right |
| 3 | **RIGHT JOIN** | All rows from right + matching left |
| 4 | **FULL OUTER JOIN** | All rows from both tables |
| 5 | **NATURAL JOIN** | Auto-joins on same column names |
| 6 | **CROSS JOIN** | Every combination of both tables |

---

## 01 — INNER JOIN *(Matching Rows Only)*

Returns **only the rows that match in both tables** based on a condition. Unmatched rows are discarded entirely.

INNER JOIN is the most commonly used join. If a row in Table 1 has no counterpart in Table 2 (or vice versa), it simply won't appear in the result.

### Syntax

```sql
SELECT *
FROM   Table1 T1
INNER JOIN Table2 T2
ON     T1.col = T2.col;
```

### How it Works — Step by Step

1. Take one row from Table 1.
2. Compare it against *every* row in Table 2.
3. If the condition matches → output that combined row.
4. Move to the next row in Table 1 and repeat.

### Example

**Table 1 (T1)**

| col |
|-----|
| 1   |
| 1   |
| 2   |

**Table 2 (T2)**

| col |
|-----|
| 1   |
| 2   |
| 2   |

**✅ Result**

| T1.col | T2.col |
|--------|--------|
| 1      | 1      |
| 1      | 1      |
| 2      | 2      |
| 2      | 2      |

> **How to read the output:** T1 has two rows with value `1`. T2 has one row with value `1`. So each T1 row pairs with the one T2 match → **2 output rows** for value `1`. T1 has one row with value `2`. T2 has two rows with value `2` → **2 output rows** for value `2`. Total = **4 rows**.

---

## 02 — LEFT JOIN *(Left Table Priority)*

Returns **all rows from the left table**, plus matching rows from the right. Where there's no match, the right side shows `NULL`.

Use LEFT JOIN when you want to keep every record from your primary (left) table, even if there's nothing matching on the other side.

### Syntax

```sql
SELECT *
FROM   Table1 T1
LEFT JOIN Table2 T2
ON     T1.col = T2.col;
```

### How it Works — Step by Step

1. Take one row from the **left** table (T1).
2. Compare against every row in T2.
3. Match found → output combined row. No match → output row with NULL on the right.
4. Repeat for every row in T1.

### Example

**Table 1 (T1) — LEFT**

| col |
|-----|
| 1   |
| 1   |
| 2   |
| 3   |

**Table 2 (T2) — RIGHT**

| col |
|-----|
| 1   |
| 2   |
| 2   |

**✅ Result**

| T1.col | T2.col |
|--------|--------|
| 1      | 1      |
| 1      | 1      |
| 2      | 2      |
| 2      | 2      |
| 3      | NULL   |

> **Key detail:** Row with value `3` in Table 1 had no match in Table 2 — but it still appears in the output, with `NULL` on the right side. This is what makes LEFT JOIN different from INNER JOIN.

---

## 03 — RIGHT JOIN *(Right Table Priority)*

Returns **all rows from the right table**, plus matching rows from the left. Where there's no match, the left side shows `NULL`.

RIGHT JOIN is essentially a mirror of LEFT JOIN. In practice, most developers rewrite right joins as left joins (by swapping table order) for readability.

### Syntax

```sql
SELECT *
FROM   Table1 T1
RIGHT JOIN Table2 T2
ON     T1.col = T2.col;
```

### How it Works — Step by Step

1. Take one row from the **right** table (T2).
2. Compare against every row in T1.
3. Match → output combined row. No match → output row with NULL on the *left* side.
4. Repeat for every row in T2.

### Example

**Table 1 (T1) — LEFT**

| col |
|-----|
| 1   |
| 1   |
| 2   |
| 3   |

**Table 2 (T2) — RIGHT**

| col |
|-----|
| 1   |
| 2   |
| 2   |
| 4   |

**✅ Result**

| T1.col | T2.col |
|--------|--------|
| 1      | 1      |
| 1      | 1      |
| 2      | 2      |
| 2      | 2      |
| NULL   | 4      |

> **Key detail:** Value `4` exists only in T2. Since RIGHT JOIN preserves all right-table rows, it appears with `NULL` on the T1 side. Value `3` from T1 is dropped because it has no match in T2.

---

## 04 — FULL OUTER JOIN *(All Rows from Both)*

Returns **every row from both tables**. Matches are combined; unmatched rows on either side are padded with `NULL`.

Think of FULL OUTER JOIN as doing a LEFT JOIN and a RIGHT JOIN simultaneously, then merging the results while removing duplicates from the middle.

### Syntax

```sql
SELECT *
FROM   Table1 T1
FULL OUTER JOIN Table2 T2
ON     T1.col = T2.col;
```

### How it Works — Step by Step

1. Perform an INNER JOIN — get all matching rows.
2. Add unmatched rows from the **left** table (right side = NULL).
3. Add unmatched rows from the **right** table (left side = NULL).

### Example

**Table 1 (T1)**

| col |
|-----|
| 1   |
| 1   |
| 2   |
| 3   |

**Table 2 (T2)**

| col |
|-----|
| 1   |
| 2   |
| 2   |
| 4   |

**✅ Result**

| T1.col | T2.col |
|--------|--------|
| 1      | 1      |
| 1      | 1      |
| 2      | 2      |
| 2      | 2      |
| 3      | NULL   |
| NULL   | 4      |

> **How to read the output:** Rows `1` and `2` matched → appear normally. Row `3` only exists in T1 → right side is NULL. Row `4` only exists in T2 → left side is NULL. You get *everything* from both sides.

---

## 05 — NATURAL JOIN *(Auto-detected Columns)*

Automatically joins two tables based on **columns that share the same name**. No explicit `ON` condition needed.

NATURAL JOIN is convenient but risky — if new columns with matching names are added to either table in the future, the join condition silently changes. Use with care in production.

### Syntax

```sql
SELECT *
FROM   Table1
NATURAL JOIN Table2;
-- SQL finds common column names automatically
```

### How it Works — Step by Step

1. SQL inspects both table schemas and finds column names they share (e.g., `id`).
2. Performs an INNER JOIN on those shared columns automatically.
3. Removes duplicate columns from the output — only one `id` appears.

> **Internally equivalent to:**
> ```sql
> SELECT *
> FROM   T1
> INNER JOIN T2
> ON     T1.id = T2.id;
> ```

### Example

**Table 1 (T1)**

| id | name |
|----|------|
| 1  | A    |
| 2  | B    |
| 3  | C    |

**Table 2 (T2)**

| id | salary |
|----|--------|
| 2  | 5000   |
| 3  | 6000   |
| 4  | 7000   |

**✅ Result**

| id | name | salary |
|----|------|--------|
| 2  | B    | 5000   |
| 3  | C    | 6000   |

> **Key detail:** SQL auto-detected `id` as the common column. Rows with `id = 1` (only in T1) and `id = 4` (only in T2) are excluded — just like an INNER JOIN. Notice only **one** `id` column appears in the result.

---

## 06 — CROSS JOIN *(Cartesian Product)*

Returns **every possible combination** of rows from both tables — also called the *Cartesian Product*.

If Table 1 has **m** rows and Table 2 has **n** rows, CROSS JOIN produces **m × n** rows. No join condition is needed or used.

### Syntax

```sql
SELECT *
FROM   T1
CROSS JOIN T2;
-- Every row in T1 paired with every row in T2
```

> ⚠️ **Careful with large tables!** A CROSS JOIN on two tables with 1,000 rows each produces 1,000,000 rows. Always verify your intent before running this on large datasets.

### How it Works — Step by Step

1. Take the first row from T1.
2. Pair it with *every* row in T2.
3. Move to the next row in T1 and repeat.

### Example

**Table 1 (T1)**

| num |
|-----|
| 1   |
| 2   |

**Table 2 (T2)**

| letter |
|--------|
| A      |
| B      |
| C      |

**✅ Result (2 × 3 = 6 rows)**

| num | letter |
|-----|--------|
| 1   | A      |
| 1   | B      |
| 1   | C      |
| 2   | A      |
| 2   | B      |
| 2   | C      |

> **How to read the output:** Row `1` from T1 pairs with every row in T2 (A, B, C). Then row `2` does the same. Result: all 6 combinations. Common use case: generating test data or all possible options (e.g., sizes × colors for a product catalog).

---

## 📋 Quick Reference Summary

| Join Type | Left Table | Right Table | NULLs? | Needs ON? |
|-----------|------------|-------------|--------|-----------|
| **INNER JOIN** | Matched only | Matched only | No | Yes |
| **LEFT JOIN** | All rows | Matched only | Right = NULL | Yes |
| **RIGHT JOIN** | Matched only | All rows | Left = NULL | Yes |
| **FULL OUTER JOIN** | All rows | All rows | Both sides | Yes |
| **NATURAL JOIN** | Matched only | Matched only | No | No (auto) |
| **CROSS JOIN** | All rows | All rows | No | No |

---

## 🔑 Key Takeaways

- **INNER JOIN** — Use when you only want records that exist in both tables.
- **LEFT JOIN** — Use when you want all records from the left table, regardless of matches.
- **RIGHT JOIN** — Mirror of LEFT JOIN; most devs prefer LEFT JOIN for consistency.
- **FULL OUTER JOIN** — Use when you want no data left behind from either table.
- **NATURAL JOIN** — Convenient shortcut, but avoid in production due to implicit behavior.
- **CROSS JOIN** — Use for combinatorial data; always be cautious of row explosion.

---

*SQL Joins Reference Guide · Based on standard SQL (ANSI)*


If you want to see in web :-
