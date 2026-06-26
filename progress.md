# Tutorial progress tracker

> **For the tutor model.** Read this file at the start of every session, before
> teaching anything. Resume from the first module whose status is not `done`.
> After each completed module, update its row (status, date, notes) and save.
> Keep the "Session log" and "Open questions" sections current too. Dates are
> ISO format (YYYY-MM-DD). Do not present more than one module without the
> student's go-ahead.
>
> **If you cannot read or write this file (stateless deployment):** ask the
> student "Which module did we finish on last time?" and resume from there.
> If they are unsure, offer to recap from the last module they remember.

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
| 1  | Life before relational databases                        | done | 2026-06-26 | Correctly identified both problems; noted (2) is a consequence of (1), not independent — sharp catch, affirmed |
| 2  | The data independence problem                           | in-progress | 2026-06-26 | Exercise posed, awaiting answer |
| 3  | Codd's leap: data as mathematics                        | todo |  |  |
| 4  | Sets, tuples, and Cartesian products (refresher)        | todo |  |  |
| 5  | Domains, attributes, and the formal relation            | todo |  |  |
| 6  | Schema vs instance; why no order and no duplicates      | todo |  |  |
| 7  | The university schema (running example)                 | todo |  |  |
| 8  | Superkeys and candidate keys                            | todo |  |  |
| 9  | Primary keys and foreign keys                           | todo |  |  |
| 10 | Algebra as a closed system                              | todo |  |  |
| 11 | Selection (σ): picking rows                             | todo |  |  |
| 12 | Projection (π): picking columns                         | todo |  |  |
| 13 | Union (∪)                                               | todo |  |  |
| 14 | Difference (−) and intersection (∩)                     | todo |  |  |
| 15 | Cartesian product (×)                                    | todo |  |  |
| 16 | Rename (ρ)                                               | todo |  |  |
| 17 | Composition and query trees                             | todo |  |  |
| 18 | Natural join (⋈): the idea                              | todo |  |  |
| 19 | Natural join: worked example                            | todo |  |  |
| 20 | Theta-join and equi-join                                | todo |  |  |
| 21 | Outer joins                                             | todo |  |  |
| 22 | Semijoin and antijoin                                   | todo |  |  |
| 23 | Division (÷): the "for all" operator                    | todo |  |  |
| 24 | Aggregation and grouping (γ)                            | todo |  |  |
| 25 | Declarative versus procedural querying                  | todo |  |  |
| 26 | Tuple relational calculus (TRC)                         | todo |  |  |
| 27 | TRC worked examples (joins and existence)               | todo |  |  |
| 28 | Domain relational calculus and safety                   | todo |  |  |
| 29 | Codd's theorem: algebra = calculus                      | todo |  |  |
| 30 | SELECT-FROM-WHERE as a calculus formula                 | todo |  |  |
| 31 | Mapping multi-relation SQL to algebra                   | todo |  |  |
| 32 | Three-valued logic and NULL (part 1)                    | todo |  |  |
| 33 | NULL gotchas (part 2)                                   | todo |  |  |
| 34 | JOINs in SQL                                            | todo |  |  |
| 35 | GROUP BY and HAVING                                     | todo |  |  |
| 36 | Views: logical data independence in SQL                 | todo |  |  |
| 37 | Window functions                                        | todo |  |  |
| 38 | Recursive CTEs: transitive closure in SQL               | todo |  |  |
| 39 | Subqueries and correlation                              | todo |  |  |
| 40 | EXISTS and IN (existential quantification)              | todo |  |  |
| 41 | Division in SQL: the double NOT EXISTS                  | todo |  |  |
| 42 | Set operations: UNION, INTERSECT, EXCEPT                | todo |  |  |
| 43 | Reasoning about query equivalence                       | todo |  |  |
| 44 | ER modeling: entities and attributes                    | todo |  |  |
| 45 | Relationships and cardinality                           | todo |  |  |
| 46 | ER is not object-oriented design                        | todo |  |  |
| 47 | Weak entities and participation constraints             | todo |  |  |
| 48 | Translating ER to relations                             | todo |  |  |
| 49 | Functional dependencies                                 | todo |  |  |
| 50 | Armstrong's axioms                                      | todo |  |  |
| 51 | Attribute closure                                       | todo |  |  |
| 52 | The three anomalies                                     | todo |  |  |
| 53 | First and second normal form (1NF, 2NF)                 | todo |  |  |
| 54 | Third normal form (3NF)                                 | todo |  |  |
| 55 | Boyce-Codd normal form (BCNF)                           | todo |  |  |
| 56 | Lossless-join decomposition                             | todo |  |  |
| 57 | Dependency preservation; 3NF versus BCNF                | todo |  |  |
| 58 | Transactions and ACID                                   | todo |  |  |
| 59 | Schedules and serializability                           | todo |  |  |
| 60 | Isolation levels and concurrency anomalies              | todo |  |  |
| 61 | Locking and multiversion concurrency control (MVCC)     | todo |  |  |
| 62 | Indexes and physical storage                            | todo |  |  |
| 63 | The optimizer as a search problem                       | todo |  |  |
| 64 | Cost estimation and join ordering                       | todo |  |  |
| 65 | The lineage: System R, INGRES, Postgres                 | todo |  |  |
| 66 | Why PostgreSQL is considered well-designed              | todo |  |  |
| 67 | Codd's Turing Award (1981)                              | todo |  |  |
| 68 | Stonebraker's Turing Award (2014)                       | todo |  |  |
| 69 | Capstone and next steps                                 | todo |  |  |

## Session log

> One line per session: date, modules covered, where we stopped.

- 2026-06-26: Session 1. Completed Module 1; Module 2 exercise posed, paused.

## Open questions / things to revisit

> Anything the student asked that we deferred, or concepts flagged `review`.

- (none yet)
