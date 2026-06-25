# Tutorial progress tracker

> **For the tutor model.** Read this file at the start of every session, before
> teaching anything. Resume from the first module whose status is not `done`.
> After each completed module, update its row (status, date, notes) and save.
> Keep the "Session log" and "Open questions" sections current too. Dates are
> ISO format (YYYY-MM-DD). Do not present more than one module without the
> student's go-ahead.

## Student profile (for tailoring)

- Background: computer scientist. Strong in AI (robotics, vision, planning,
  reasoning), software engineering, theory of computation. Comfortable with sets,
  logic, formal definitions. New to relational databases.
- Lean on analogies to: theory of computation (closure, regular operations),
  first-order logic / set-builder notation, AI search and planning, and the
  contrast with object-oriented design.
- Stated goals: (1) reason about good ER modelling, efficient design, and
  querying; (2) understand why PostgreSQL is considered well-designed; (3)
  understand why Codd and Stonebraker earned their Turing Awards.
- Format note: studies on a phone, so modules are kept small. One at a time.

## Status legend

- `todo`        not started
- `in-progress` presented, awaiting or mid-discussion of the exercise
- `done`        exercise answered and feedback given
- `review`      done, but flagged to revisit

## Module checklist

| #  | Module | Status | Date | Notes (struggles, corrections, hints used) |
|----|--------|--------|------|---------------------------------------------|
| 1  | Life before relational databases            | todo |  |  |
| 2  | The data independence problem               | todo |  |  |
| 3  | Codd's leap: data as mathematics            | todo |  |  |
| 4  | Sets, tuples, Cartesian products            | todo |  |  |
| 5  | Domains, attributes, formal relation        | todo |  |  |
| 6  | Schema vs instance; no order/no duplicates  | todo |  |  |
| 7  | The university schema (running example)     | todo |  |  |
| 8  | Superkeys and candidate keys                | todo |  |  |
| 9  | Primary keys and foreign keys               | todo |  |  |
| 10 | Algebra as a closed system                  | todo |  |  |
| 11 | Selection (σ)                               | todo |  |  |
| 12 | Projection (π)                              | todo |  |  |
| 13 | Union (∪)                                   | todo |  |  |
| 14 | Difference (−) and intersection (∩)         | todo |  |  |
| 15 | Cartesian product (×)                        | todo |  |  |
| 16 | Rename (ρ)                                   | todo |  |  |
| 17 | Composition and query trees                 | todo |  |  |
| 18 | Natural join (⋈): the idea                  | todo |  |  |
| 19 | Natural join: worked example                | todo |  |  |
| 20 | Theta-join and equi-join                    | todo |  |  |
| 21 | Outer joins                                 | todo |  |  |
| 22 | Semijoin and antijoin                       | todo |  |  |
| 23 | Division (÷): the "for all" operator        | todo |  |  |
| 24 | Aggregation and grouping (γ)                | todo |  |  |
| 25 | Declarative vs procedural querying          | todo |  |  |
| 26 | Tuple relational calculus (TRC)             | todo |  |  |
| 27 | TRC worked examples                         | todo |  |  |
| 28 | Domain relational calculus and safety       | todo |  |  |
| 29 | Codd's theorem: algebra = calculus          | todo |  |  |
| 30 | SELECT-FROM-WHERE as a calculus formula     | todo |  |  |
| 31 | Mapping multi-relation SQL to algebra       | todo |  |  |
| 32 | Three-valued logic and NULL (1)             | todo |  |  |
| 33 | NULL gotchas (2)                            | todo |  |  |
| 34 | JOINs in SQL                                | todo |  |  |
| 35 | GROUP BY and HAVING                         | todo |  |  |
| 36 | Subqueries and correlation                  | todo |  |  |
| 37 | EXISTS and IN                               | todo |  |  |
| 38 | Division in SQL: double NOT EXISTS          | todo |  |  |
| 39 | Set operations: UNION/INTERSECT/EXCEPT      | todo |  |  |
| 40 | Reasoning about query equivalence           | todo |  |  |
| 41 | ER modelling: entities and attributes       | todo |  |  |
| 42 | Relationships and cardinality               | todo |  |  |
| 43 | ER is not object-oriented design            | todo |  |  |
| 44 | Weak entities and participation             | todo |  |  |
| 45 | Translating ER to relations                 | todo |  |  |
| 46 | Functional dependencies                     | todo |  |  |
| 47 | Closure and Armstrong's axioms              | todo |  |  |
| 48 | The three anomalies                         | todo |  |  |
| 49 | First and second normal form (1NF, 2NF)     | todo |  |  |
| 50 | Third normal form (3NF)                      | todo |  |  |
| 51 | Boyce-Codd normal form (BCNF)               | todo |  |  |
| 52 | Lossless-join decomposition                 | todo |  |  |
| 53 | Dependency preservation; 3NF vs BCNF        | todo |  |  |
| 54 | Transactions and ACID                       | todo |  |  |
| 55 | Schedules and serializability               | todo |  |  |
| 56 | Isolation levels and anomalies              | todo |  |  |
| 57 | Locking and MVCC                            | todo |  |  |
| 58 | Indexes and physical storage                | todo |  |  |
| 59 | The optimizer as a search problem           | todo |  |  |
| 60 | Cost estimation and join ordering           | todo |  |  |
| 61 | The lineage: System R, INGRES, Postgres     | todo |  |  |
| 62 | Why PostgreSQL is well-designed             | todo |  |  |
| 63 | Codd's Turing Award (1981)                  | todo |  |  |
| 64 | Stonebraker's Turing Award (2014)           | todo |  |  |
| 65 | Capstone and next steps                     | todo |  |  |

## Session log

> One line per session: date, modules covered, where we stopped.

- (no sessions yet)

## Open questions / things to revisit

> Anything the student asked that we deferred, or concepts flagged `review`.

- (none yet)
