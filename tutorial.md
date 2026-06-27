# Relational Database Theory: An Interactive Tutorial

> **Instructor file.** This document is the full teaching script. A fast model
> (for example Claude Haiku) reads it and presents the course to one student,
> one module at a time, in a chat session. The student is a computer scientist
> with a strong background in AI (robotics, vision, planning, reasoning),
> software engineering, and theory of computation, but almost no prior exposure
> to relational databases. Pitch accordingly: assume mathematical maturity,
> assume comfort with sets, logic, and formal definitions, but assume zero
> database knowledge.

---

## HOW TO RUN THIS TUTORIAL (read this first, tutor)

You are the tutor. Follow these rules every session.

1. **One module at a time.** Present a single module, then stop and wait. Never
   dump several modules at once. Modules are deliberately small so they read
   well on a phone.

2. **The shape of a module.** For each module you present:
   - State the module number and title.
   - Teach the concept using the **Teaching content** below. You may rephrase
     and shorten, but do not add topics from later modules.
   - Show any figure or definition included.
   - Before posing the exercise, **repeat any relevant schema, tables, or data
     from the running example** that the student will need. This avoids forcing
     them to scroll back on a phone screen.
   - Pose the **Exercise** exactly as one clear question (or a short set).
   - **Stop and wait for the student's answer.** Do not reveal the model
     answer first.

3. **Giving feedback.** When the student answers:
   - Compare against the **Model answer** and **Feedback notes**.
   - Affirm what is correct. Correct what is wrong, gently and specifically.
   - If they are stuck, give the hint listed, not the full answer, and invite
     another try.
   - Only after they have genuinely engaged, confirm the full answer and ask if
     they are ready to continue.

4. **Pacing.** After each module, ask whether to continue, pause, or revisit.
   Never auto-advance more than one module without a "yes."

5. **Progress tracking.** Maintain a companion file `progress.md` (a template is
   provided in the repo). After each completed module, update it: mark the
   module done, record the date, and note anything the student found hard or
   any correction you made. At the start of every session, read `progress.md`
   first and resume from the first incomplete module. Briefly recap the prior
   module before continuing.

   **If you cannot read or write `progress.md` (stateless deployment):** ask the
   student at the start of every session: "Which module did we finish on last
   time?" and resume from there. If they are unsure, offer to recap from the
   last module they remember. Do not start over from Module 1 unless they ask.

6. **Handling tangents.** This student is intellectually curious and will ask
   questions that belong to later modules. When that happens: name the module
   that covers it, give one sentence orienting them ("that is exactly what
   Module 38 is about"), and say "let's get there in sequence." Do not skip
   ahead or teach the later topic in full.

7. **Partial answers.** Many answers will be partly right. Affirm the correct
   part explicitly, then say "one thing to add:" and supply the missing piece.
   Do not re-teach from scratch for a half-right answer.

8. **Tone.** Warm, precise, concise. This student likes formal statements:
   give definitions and theorems plainly. Use analogies to AI / theory of
   computation / OO design where the notes suggest them. You may offer analogies
   the notes do not suggest, but label them clearly as analogies, not
   definitions, so the student knows what is formal and what is illustrative.

9. **Notation.** Use these symbols for relational algebra: σ (select), π
   (project), ∪ (union), ∩ (intersect, derived), − (difference), × (product), ⋈
   (natural join), ⟕ ⟖ ⟗ (left/right/full outer join), ÷ (division), ρ
   (rename), γ (grouping/aggregation). Render math inline in plain text when a
   chat cannot show LaTeX. For FDs, X → Y means "X functionally determines Y";
   X⁺ means the closure of attribute set X.

10. **No running a real database.** This course is deliberately pencil-and-paper.
   The student does not need Postgres installed. Everything is reasoned about
   formally. SQL appears as an object of analysis, not as something to execute.

---

## THE RUNNING EXAMPLE (used throughout)

Every module that needs data uses one small **university** schema. Introduce it
formally in Module 7, but here it is for your reference. Keep using it so the
student builds familiarity instead of re-learning a new domain each time.

```
Student(sid, sname, major, year)
Course(cid, title, dept, credits)
Instructor(iid, iname, dept, salary)
Section(cid, term, sec_no, iid, room)
Enrol(sid, cid, term, sec_no, grade)
```

Plain reading:
- A **Student** has an id, a name, a major, and a year of study.
- A **Course** has an id, a title, an owning department, and a credit value.
- An **Instructor** has an id, a name, a department, and a salary.
- A **Section** is one offering of a course in a given term, taught by one
  instructor in one room.
- **Enrol** records that a student took a particular section and the grade
  earned.

Sample rows appear in modules where they help. Keep the numbers small so a
student can verify a query by hand.

---

# PART I. WHY RELATIONAL?

## Module 1. Life before relational databases

**Goal:** motivate the whole course by showing the problem Codd was solving.

**Teaching content.**
Before 1970, data was stored in application-specific files: hierarchies of
records and explicit pointers linking them (the hierarchical and network data
models). To find data you wrote a program that walked those pointers in a fixed
order. The query was the navigation. This had two painful consequences.

First, the program encoded the physical layout. If a database administrator
reorganized storage (added an index, split a file, changed a pointer chain),
the programs broke and had to be rewritten. Logic and storage were welded
together.

Second, asking a new question often meant writing a whole new traversal by
hand. There was no general-purpose way to say "give me X" and let the system
work out how.

A useful analogy from your background: this is like a world where every search
over a data structure had to be hand-coded against the exact memory layout,
with no abstract iterator and no query planner deciding the access path for you.

**Exercise.**
Name the two distinct problems with the pre-relational, pointer-navigation
approach, and say which one you think is more fundamental and why.

**Model answer.**
(1) Programs are coupled to physical storage, so storage changes break code.
(2) Each new question needs hand-written navigation; there is no declarative
query facility. Either answer for "more fundamental" is acceptable if argued.
The intended insight: (1) is the deeper one, because it is about *abstraction*
(separating the logical from the physical). (2) is largely a consequence:
without a logical model to query against, you are forced to navigate physically.

**Feedback notes.**
If the student only lists one problem, prompt: "There is a second, separate
issue about asking new questions." Reward any answer that frames (1) as an
abstraction-boundary problem.

## Module 2. The data independence problem

**Goal:** name the central concept Codd introduced: data independence.

**Teaching content.**
**Data independence** is the property that the logical view of data is
insulated from how it is physically stored, so the storage can change without
forcing application changes. Codd split this into two layers:

- **Physical data independence:** you can change storage structures (file
  organization, indexes, partitioning) without changing the logical schema or
  the queries.
- **Logical data independence:** you can change the logical schema (add a
  column, split a table behind a view) with limited disruption to applications
  that use a higher-level view.

The relational model achieves physical data independence cleanly: queries are
written against logical relations, and the system is free to store and access
those relations however it likes.

Analogy: this is an interface / implementation boundary, exactly like
programming to an abstract data type rather than to its concrete in-memory
representation.

**Exercise.**
Classify each change as needing *physical* or *logical* independence to be safe:
(a) the DBA builds an index to speed up a query;
(b) the DBA moves a table to a faster disk;
(c) a column is added to a table that existing queries do not mention.

**Model answer.**
(a) physical, (b) physical, (c) logical (adding a column is a schema change;
queries that name columns explicitly and do not use `SELECT *` are insulated).

**Feedback notes.**
Common confusion: thinking (a) is logical. Indexes are pure storage/access
structures, invisible to the logical model, so they are the canonical example
of physical independence.

## Module 3. Codd's leap: data as mathematics

**Goal:** state the single big idea of the 1970 paper.

**Teaching content.**
In 1970, Edgar F. Codd published "A Relational Model of Data for Large Shared
Data Banks" in *Communications of the ACM*. The leap: model all data as
**mathematical relations** (sets of tuples) and query it with operations that
have a precise mathematical meaning. No pointers, no prescribed traversal
order. You describe the set of facts you want; the system computes it.

Three payoffs fall out immediately:
- A query has a meaning independent of any storage layout (data independence).
- Queries can be transformed into equivalent queries using algebraic laws
  (the basis for automatic optimization, much later in the course).
- The model rests on set theory and first-order logic, so we can prove things
  about it.

This is why the field treats 1970 as a turning point: data management became a
branch of applied logic rather than a craft of pointer-chasing.

**Exercise.**
In one sentence, what does it buy you to define a query's meaning purely
mathematically, with no reference to storage? Give one concrete downstream
benefit.

**Model answer.**
It decouples *what* is being asked from *how* it is computed, which (among other
things) lets the system choose and later re-choose the execution strategy
(optimization) without changing the query's meaning.

**Feedback notes.**
Accept any of: optimization, data independence, provability/equivalence laws,
portability. The key word to fish for is *decoupling* of meaning from execution.

## Module 4. Sets, tuples, and Cartesian products (refresher)

**Goal:** re-establish the exact set-theory vocabulary the model is built on.

**Teaching content.**
Quick refresher, because the relational definition is literally set theory.

- A **set** is an unordered collection of distinct elements. {1,2} = {2,1} and
  {1,1} = {1}.
- An ordered **n-tuple** (a₁, …, aₙ) is ordered and may repeat: (1,2) ≠ (2,1).
- The **Cartesian product** of sets D₁, …, Dₙ is
  D₁ × … × Dₙ = { (a₁, …, aₙ) : aᵢ ∈ Dᵢ }, the set of all n-tuples drawn one
  component from each set.

Example: {a, b} × {1, 2} = { (a,1), (a,2), (b,1), (b,2) }, four tuples.

Hold onto the tension: a set is unordered, but a tuple is ordered. The next two
modules resolve how a relation uses both ideas at once.

**Exercise.**
Compute {x, y} × {1, 2, 3}. How many tuples does it have? State the general
rule for |D₁ × D₂|.

**Model answer.**
{(x,1),(x,2),(x,3),(y,1),(y,2),(y,3)}, six tuples.
|D₁ × D₂| = |D₁| · |D₂|.

**Feedback notes.**
If they miss a tuple, have them list systematically: fix the first component,
sweep the second. Reinforce |D₁ × D₂| = |D₁|·|D₂|, generalizing to a product.

## Module 5. Domains, attributes, and the formal relation

**Goal:** give the precise definition of a relation.

**Teaching content.**
**Definitions.**
- A **domain** is a named set of atomic (indivisible) values, for example the
  set of all valid student ids, or the integers, or the set of legal grades.
- An **attribute** is a name paired with a domain. We write A : dom(A). For
  example sname : string, year : integer.
- A **relation schema** R(A₁, …, Aₙ) is a set of attributes.
- A **relation instance** (or just relation) over R is a finite **set** of
  tuples, where each tuple assigns, to every attribute Aᵢ, a value from
  dom(Aᵢ). Formally a relation is a subset of dom(A₁) × … × dom(Aₙ).

So a relation is a subset of a Cartesian product of domains. A table is just a
picture of one: columns are attributes, rows are tuples.

**Definition (atomicity / First Normal Form).** Every attribute value is
atomic: no lists, no nested tables inside a cell. We revisit this as 1NF in the
design part.

**Exercise.**
Given domains dom(major) = {CS, Math} and dom(year) = {1, 2}, write the schema
S(major, year) and give the largest possible relation instance over it. How
many tuples does it have, and why can no instance be larger?

**Model answer.**
Schema S(major, year). Largest instance = the full product:
{(CS,1),(CS,2),(Math,1),(Math,2)}, four tuples. No instance can exceed the
product because a relation is by definition a *subset* of dom(major)×dom(year),
which has |{CS,Math}|·|{1,2}| = 4 elements.

**Feedback notes.**
The point to extract: a relation instance is bounded above by the Cartesian
product of its domains. This also previews why "no duplicate rows": it is a set.

## Module 6. Schema vs instance; why no order and no duplicates

**Goal:** cement two consequences of "a relation is a set of tuples."

**Teaching content.**
Two structural facts follow directly from the definition, and both surprise
people coming from spreadsheets or arrays.

1. **No duplicate tuples.** A relation is a *set*, so a tuple either is or is
   not a member. There is no "the same row twice." (Real SQL tables relax this
   into multisets / bags; we note the gap and return to it.)

2. **No ordering of tuples.** Sets are unordered, so there is no first row.
   Likewise, because we treat a schema as a *set of named attributes*, columns
   are identified by name, not by position. ("Order does not matter" for
   columns is the pure-theory stance; SQL does impose a column order.)

Distinguish two words you will use constantly:
- **Schema:** the structure (the attribute names and their domains). Compile
  time, roughly.
- **Instance:** the actual set of tuples right now. Run time, roughly.

Analogy: schema is the type, instance is a value of that type.

**Exercise.**
True or false, with a one-line justification each:
(a) Re-running the same query can return rows in a different order unless you
explicitly sort.
(b) Inserting a tuple that already exists changes a (pure, set-theoretic)
relation.

**Model answer.**
(a) True. A relation has no inherent order, so a system may return tuples in any
order absent an explicit sort.
(b) False. The tuple is already a member of the set; re-inserting it is a no-op
in the pure model (set semantics).

**Feedback notes.**
If the student protests that real databases keep duplicates, affirm it: SQL uses
bag semantics, which we will treat explicitly. Here we are in the pure model.

# PART II. THE RUNNING EXAMPLE AND KEYS

## Module 7. The university schema (our running example)

**Goal:** install the shared example and read a schema fluently.

**Teaching content.**
From here on, all data uses this schema. Underlined attributes (shown in
brackets in plain text) form the primary key; we define keys precisely in the
next two modules.

```
Student( [sid], sname, major, year )
Course(  [cid], title, dept, credits )
Instructor( [iid], iname, dept, salary )
Section( [cid, term, sec_no], iid, room )
Enrol(   [sid, cid, term, sec_no], grade )
```

A tiny instance to reason with:

```
Student                         Course
sid  sname    major  year       cid    title         dept  credits
101  Ada      CS     2          CS101  Intro CS       CS    3
102  Babbage  Math   3          CS305  Databases      CS    4
103  Curie    Phys   1          MA200  Linear Algebra Math  3

Instructor                      Enrol
iid  iname   dept  salary       sid  cid    term   sec_no grade
11   Knuth   CS    150          101  CS101  2026F  1      A
12   Noether Math  140          101  CS305  2026F  1      B
                                 102  MA200  2026F  1      A
Section
cid    term   sec_no iid  room
CS101  2026F  1      11   R1
CS305  2026F  1      11   R2
MA200  2026F  1      12   R3
```

**Exercise.**
Using the instance above, answer by hand (matching values across tables):
(a) Which rows of Enrol belong to student 101 (Ada)?
(b) Using those rows, look up the matching rows in Section. What room does Ada
attend for CS101?

**Model answer.**
(a) The two Enrol rows with sid=101: (101,CS101,2026F,1,A) and
(101,CS305,2026F,1,B).
(b) Match (cid=CS101, term=2026F, sec_no=1) in Section → room R1. Ada attends CS101
in room R1.

**Feedback notes.**
Keep the exercise to pure value-matching across tables; we have not yet defined
keys formally (Modules 8–9). The join path here (Enrol → Section → Instructor
via shared column values) is exactly what we will formalize as a natural join
starting in Module 18.

## Module 8. Superkeys and candidate keys

**Goal:** define keys as a uniqueness property of a schema.

**Teaching content.**
**Definition (superkey).** A set of attributes K of relation R is a *superkey*
if no two distinct tuples of any legal instance can agree on all of K. That is,
K functionally determines the whole tuple.

**Definition (candidate key).** A *candidate key* is a *minimal* superkey: it is
a superkey, and no proper subset of it is a superkey. Minimality is the whole
point: a candidate key carries no redundant attribute.

Example: in Student, {sid} is a candidate key (ids are unique). {sid, sname} is
a superkey but not a candidate key, because it is not minimal ({sid} alone
already works).

A relation can have several candidate keys. The choice of one to be "the"
primary key is made by the designer (next module).

**Exercise.**
In Section( cid, term, sec_no, iid, room ), suppose every room holds at most one
section per term, so (term, room) is unique. List two candidate keys, and give
one superkey that is not a candidate key.

**Model answer.**
Candidate keys: {cid, term, sec_no} and {term, room} (both minimal and unique).
A non-minimal superkey: {cid, term, sec_no, iid} (unique, but {cid,term,sec_no}
already suffices, so not minimal).

**Feedback notes.**
If they offer {iid} as a key, push back: one instructor teaches many sections,
so iid is not unique across Section. Reinforce minimality with the "drop an
attribute and it still works ⇒ not a candidate key" test.

## Module 9. Primary keys and foreign keys

**Goal:** define the chosen identifier and the cross-relation reference.

**Teaching content.**
**Primary key.** The designer picks one candidate key as the **primary key**,
the official identifier for tuples of that relation. By convention it is shown
underlined. In our schema, Student's primary key is {sid}.

**Foreign key.** A **foreign key** is a set of attributes in one relation that
must match the primary key (or some candidate key) of another relation. It is
how relations reference each other without pointers. It encodes a constraint
called **referential integrity**: every value present must actually exist in the
referenced relation.

In our schema:
- Enrol.sid is a foreign key referencing Student.sid.
- Enrol.(cid, term, sec_no) is a foreign key referencing Section.
- Section.iid is a foreign key referencing Instructor.iid.

Referential integrity says: you cannot enrol a student id that does not exist,
and you cannot assign a section to an instructor who does not exist.

**Exercise.**
(a) Why are foreign keys the relational replacement for the pointers of the old
network model? (b) Give one concrete update to our instance that would violate
referential integrity.

**Model answer.**
(a) They express "this tuple refers to that tuple" by *value matching* against a
key, not by a physical pointer, so the reference survives any storage change and
its validity is a checkable logical constraint.
(b) Examples: inserting Enrol(999, CS101, 2026F, 1, A) when no Student 999
exists; or deleting Instructor 11 while Section CS101/2026F/1 still names iid 11.

**Feedback notes.**
Tie back to Module 1: pointers welded logic to storage; foreign keys are
value-based references, so they are storage-independent and checkable.

# PART III. RELATIONAL ALGEBRA

## Module 10. Algebra as a closed system

**Goal:** frame relational algebra and why closure matters.

**Teaching content.**
**Relational algebra** is a small set of operators that take relations as input
and produce a relation as output. That last clause is the key structural fact:
the output is always another relation. This property is **closure**, and it is
exactly why operators compose: the result of one operator can be the input of
the next, with no glue or type change.

Analogy from theory of computation: just as regular operations (union,
concatenation, star) are closed over regular languages, relational operators are
closed over relations. Closure is what lets you build arbitrarily complex
queries from a fixed, tiny vocabulary.

We will learn six fundamental operators (select, project, union, difference,
product, rename) and then several derived operators (joins, intersection,
division, grouping) defined in terms of them.

**Exercise.**
Why does closure (every operator returns a relation) matter for building complex
queries? Contrast with a hypothetical operator that returned, say, a single
number.

**Model answer.**
Closure lets operators nest and compose freely: any sub-expression is itself a
relation, so it can feed another operator. An operator returning a bare number
would break composition, because the next relational operator expects a relation
as input, not a scalar. (This is why aggregation needs care: it is bolted on as
an extension precisely because raw "count" is not relation-valued.)

**Feedback notes.**
Reward the compositionality insight. The regular-languages analogy usually
clicks hard for this student; use it.

## Module 11. Selection (σ): picking rows

**Goal:** the row filter.

**Teaching content.**
**Definition.** σ_condition(R) returns the set of tuples of R that satisfy the
boolean *condition*. It filters rows; it never changes the columns. The schema
of the result equals the schema of R.

The condition is built from attribute names, constants, comparisons
(=, ≠, <, ≤, >, ≥) and the connectives ∧ (and), ∨ (or), ¬ (not).

Example: σ_(major = 'CS')(Student) returns just Ada (101).
Example: σ_(year ≥ 2 ∧ major = 'CS')(Student) also returns just Ada here.

**Exercise.**
Write a selection that returns instructors earning more than 145. Evaluate it on
the sample instance.

**Model answer.**
σ_(salary > 145)(Instructor). Result: just Knuth (iid 11, salary 150).

**Feedback notes.**
Confirm the result schema is unchanged (still all Instructor columns). If they
write the condition correctly but evaluate wrong, recheck Noether's salary (140,
not > 145).

## Module 12. Projection (π): picking columns

**Goal:** the column selector, and the hidden duplicate-elimination.

**Teaching content.**
**Definition.** π_(A₁,…,Aₖ)(R) returns, for every tuple of R, just the listed
attributes. It keeps all rows but narrows the columns. The result schema is
(A₁,…,Aₖ).

The subtle part: because a relation is a set, projection **eliminates
duplicates** that result from dropping columns. If two tuples become identical
after you discard a column, the result holds only one copy.

Example: π_(major)(Student) on our instance = { (CS), (Math), (Phys) }. Even if
ten students were CS, you get one (CS) tuple.

**Exercise.**
Evaluate π_(dept)(Instructor) on the sample instance. Then explain why the
number of result tuples can be smaller than the number of input tuples.

**Model answer.**
π_(dept)(Instructor) = { (CS), (Math) }, two tuples (from Knuth/CS and
Noether/Math). It can shrink because dropping columns can make distinct input
tuples coincide, and set semantics keeps only one copy of each.

**Feedback notes.**
This duplicate-elimination is a frequent surprise and a common SQL bug source
later (SELECT vs SELECT DISTINCT). Flag it now.

## Module 13. Union (∪)

**Goal:** first of the set operators; introduce union compatibility.

**Teaching content.**
**Definition.** R ∪ S is the set of tuples in R or S (or both). For this to make
sense, R and S must be **union-compatible**: same number of attributes, with
matching domains, position by position. The result holds each tuple once.

Example: (π_sid σ_(major='CS') Student) ∪ (π_sid σ_(year=1) Student) gives the
set of student ids who are CS majors *or* first-years.

**Exercise.**
Are Student and Instructor union-compatible? Why or why not? What is the general
requirement?

**Model answer.**
No. Student is (sid, sname, major, year) and Instructor is (iid, iname, dept,
salary). The arity is the same (both 4), which is necessary but not sufficient.
The domains and meanings per position do not match (major vs dept, year vs
salary), so union is not meaningful. The requirement: same arity and compatible
domains position by position (and, in practice, comparable attribute meanings).

**Feedback notes.**
If they say "yes, both have 4 columns," correct: equal arity is necessary but
not sufficient; the per-position domains must match. Union is over sets of
tuples of the *same type*.

## Module 14. Difference (−) and intersection (∩)

**Goal:** the other set operators; intersection as derived.

**Teaching content.**
**Difference.** R − S is the set of tuples in R but not in S. Requires
union-compatibility. It is one of the fundamental six.

**Intersection.** R ∩ S is the set of tuples in both. It is *derived*, not
fundamental, because R ∩ S = R − (R − S). (It also equals S − (S − R).)

Difference is how you express "but not": students who are CS majors but are not
enrolled in anything, for instance, would use a difference of id-sets.

**Exercise.**
(a) Prove informally that R ∩ S = R − (R − S). (b) Express "instructors who are
not in the CS department" using selection (two ways: with σ and ¬, or with −).

**Model answer.**
(a) R − S removes from R everything also in S. Subtracting that from R leaves
exactly the tuples of R that *were* in S, i.e. R ∩ S. (A tuple t survives
R−(R−S) iff t ∈ R and t ∉ (R−S), i.e. t ∈ R and (t ∉ R or t ∈ S), i.e. t ∈ R and
t ∈ S.)
(b) σ_(dept ≠ 'CS')(Instructor), or Instructor − σ_(dept='CS')(Instructor).

**Feedback notes.**
The set-identity proof exercises the same membership reasoning they know from
discrete math; let them write it in whatever style they prefer.

## Module 15. Cartesian product (×)

**Goal:** the combining operator, and why it is rarely used raw.

**Teaching content.**
**Definition.** R × S pairs every tuple of R with every tuple of S. If R has m
tuples and S has n tuples, R × S has m·n tuples, and its schema is the
concatenation of both schemas (rename if names collide; see Module 16).

Raw product is almost never what you want: it pairs unrelated things (every
student with every course). Its value is as the raw material for joins, which
are a product followed by a selection that keeps only the meaningful pairs.

Example sizes: Student (3) × Course (3) = 9 tuples, most meaningless.

**Exercise.**
Section has 3 tuples and Instructor has 2. How many tuples in Section ×
Instructor? Of those, how many have Section.iid = Instructor.iid (the
"meaningful" pairs)?

**Model answer.**
3 · 2 = 6 tuples total. Meaningful pairs: Section CS101→iid11, CS305→iid11,
MA200→iid12; each matches exactly one instructor, so 3 meaningful pairs out of 6.

**Feedback notes.**
Set up the punchline for Module 18: the join is exactly "keep the meaningful
pairs," i.e. σ over a product.

## Module 16. Rename (ρ)

**Goal:** the operator that makes self-joins and name clashes tractable.

**Teaching content.**
**Definition.** ρ_S(R) renames relation R to S; ρ_(S(B₁,…,Bₙ))(R) also renames
the attributes. Rename does not change any tuple; it changes names so that
expressions can refer unambiguously to columns.

Why you need it: after a product, two columns may share a name (both called
iid). And to compare a relation with itself (a self-join, for example "pairs of
instructors in the same department") you must give the two copies different
names.

Example: ρ_(I1)(Instructor) × ρ_(I2)(Instructor) lets you write
σ_(I1.dept = I2.dept ∧ I1.iid < I2.iid)(…) to get unordered same-department
pairs.

**Exercise.**
Write an expression for all unordered pairs of distinct instructors who share a
department. Why is the condition iid < iid (rather than iid ≠ iid)?

**Model answer.**
σ_(I1.dept = I2.dept ∧ I1.iid < I2.iid)( ρ_(I1)(Instructor) × ρ_(I2)(Instructor) ).
Using `<` instead of `≠` yields each unordered pair once; `≠` would yield both
(a,b) and (b,a), i.e. each pair twice.

**Feedback notes.**
The `<` trick to dedupe unordered pairs is worth dwelling on; it recurs in SQL.

## Module 17. Composition and query trees

**Goal:** read and build nested expressions as trees.

**Teaching content.**
Because of closure (Module 10), any expression is a tree: leaves are base
relations, internal nodes are operators, and each subtree denotes a relation.
Evaluating bottom-up gives the answer.

Example query: "names of CS students in year 2."
π_(sname)( σ_(major='CS' ∧ year=2)(Student) )

As a tree:
```
        π_sname
           |
     σ_(major='CS' ∧ year=2)
           |
        Student
```

A guideline you will revisit in optimization: pushing σ and π *down* the tree
(closer to the leaves) shrinks intermediate relations early. The result is the
same (algebraic equivalence), but the work can be far less.

**Exercise.**
Write an algebra expression and draw its tree for: "titles of courses worth more
than 3 credits in the CS department."

**Model answer.**
π_(title)( σ_(credits > 3 ∧ dept='CS')(Course) ). Tree: Course → σ → π. On the
sample data, that is just "Databases" (CS305, 4 credits).

**Feedback notes.**
If they push π below σ here, note the subtlety: you cannot project away `credits`
or `dept` *before* the selection that uses them. Projection can only drop columns
no longer needed upstream. Good moment to preview optimization legality.

## Module 18. Natural join (⋈): the idea

**Goal:** define the workhorse operator conceptually.

**Teaching content.**
**Definition.** The **natural join** R ⋈ S combines tuples of R and S that agree
on all attributes the two share by name. The result schema is the union of the
attributes (shared attributes appear once). Operationally:

R ⋈ S = π_(all attrs, shared once)( σ_(equality on shared attrs)( R × S ) ).

So a natural join is exactly: take the product, keep only tuples that agree on
the common columns, and merge those common columns. It is "follow the foreign
key" expressed algebraically.

Example: Section ⋈ Instructor joins on the shared attribute iid, pairing each
section with the instructor who teaches it.

**Exercise.**
On what attribute(s) does Enrol ⋈ Section join? Describe in words what one
result tuple represents.

**Model answer.**
They share (cid, term, sec_no), so the join matches on all three. A result tuple
represents one enrolment together with the full details of the section it refers
to (the instructor iid and room), i.e. "this student took this specific offering,
which was taught by this instructor in this room."

**Feedback notes.**
Make sure they spotted all three shared attributes, not just cid. Joining on too
few attributes is a classic real-world bug; the natural join's "all shared
names" rule is both its convenience and its hazard.

## Module 19. Natural join: worked example

**Goal:** evaluate a multi-relation join by hand.

**Teaching content.**
Let us compute "names of instructors who teach student 101 (Ada)," step by step,
purely by hand on the sample instance.

1. σ_(sid=101)(Enrol) → Ada's enrolments: (101,CS101,2026F,1,A) and
   (101,CS305,2026F,1,B).
2. Join that with Section on (cid,term,sec_no) → both rows pick up iid 11
   (Section CS101/2026F/1 and CS305/2026F/1 both have iid 11).
3. Join with Instructor on iid → iid 11 = Knuth.
4. π_(iname) → { Knuth }.

Full expression:
π_(iname)( σ_(sid=101)(Enrol) ⋈ Section ⋈ Instructor ).

**Exercise.**
By the same method, compute the set of *room*s in which student 101 has classes.
Show your steps.

**Model answer.**
σ_(sid=101)(Enrol) gives CS101/2026F/1 and CS305/2026F/1. Join with Section:
CS101→R1, CS305→R2. π_(room) = { R1, R2 }.

**Feedback notes.**
This hand-evaluation muscle matters: it is how the student will sanity-check SQL
later without running it. Praise clear step labelling.

## Module 20. Theta-join and equi-join

**Goal:** generalize join beyond equality-on-shared-names.

**Teaching content.**
The natural join is a special case. The **theta-join** R ⋈_θ S is defined as
σ_θ(R × S) for an arbitrary condition θ. When θ is a conjunction of equalities,
it is an **equi-join**. When those equalities are exactly "shared attributes are
equal" and we then drop the duplicate columns, we recover the natural join.

You need theta-joins when the matching condition is not equality on identically
named columns, for example joining on an inequality (salary bands) or on columns
with different names.

Example (inequality join): pair each instructor with every instructor who earns
strictly more, to build a "ranked above" relation:
ρ_(I1)(Instructor) ⋈_(I1.salary < I2.salary) ρ_(I2)(Instructor).

**Exercise.**
Express the natural join Section ⋈ Instructor as an explicit equi-join over a
product, including the rename and final projection needed to avoid a duplicate
iid column.

**Model answer.**
π_(cid,term,sec_no,room,iid,iname,dept,salary)(
  σ_(Section.iid = I.iid)( Section × ρ_(I)(Instructor) ) ),
keeping a single iid column. (Any equivalent that joins on iid equality and
projects away one of the two iid columns is correct.)

**Feedback notes.**
The teaching point: natural join = equi-join on shared names + dedupe of the
join columns. Seeing them define one in terms of the other locks it in.

## Module 21. Outer joins

**Goal:** keep unmatched tuples with nulls.

**Teaching content.**
An ordinary (inner) join drops tuples that have no match. Sometimes you want to
keep them. **Outer joins** pad the missing side with **null** (a placeholder for
"no value").

- **Left outer join** R ⟕ S: every tuple of R appears at least once; if it had
  no match in S, the S-attributes are null.
- **Right outer join** R ⟖ S: symmetric, keeps all of S.
- **Full outer join** R ⟗ S: keeps unmatched tuples from both sides.

Example: Instructor ⟕ Section lists every instructor, including any who teaches
no section (their section columns would be null).

**Exercise.**
Suppose we add Instructor (13, Lovelace, CS, 130) who teaches nothing. In
Instructor ⟕ Section (join on iid), what tuple(s) does Lovelace contribute, and
what do the section columns hold?

**Model answer.**
Lovelace contributes exactly one tuple: (iid 13, Lovelace, CS, 130) with
cid/term/sec_no/room all null, because she has no matching Section row. An inner
join would have dropped her entirely.

**Feedback notes.**
This is the first appearance of null. Flag that null is not zero and not empty
string; it means "no value here." Null propagates through comparisons in a
non-obvious way (a comparison like `salary > 100` on a null salary does not
return false; it returns a third value, "unknown"). We treat this carefully in
Modules 32–33; for now, just know that null means "absent."

## Module 22. Semijoin and antijoin

**Goal:** filter one relation by the existence of a match in another.

**Teaching content.**
Sometimes you want rows of R that *do* (or *do not*) have a partner in S,
without actually pulling in S's columns.

- **Semijoin** R ⋉ S = π_(attrs of R)(R ⋈ S): the tuples of R that have at least
  one match in S. "Exists a match."
- **Antijoin** R ▷ S = R − (R ⋉ S): the tuples of R with *no* match in S.
  "Exists no match."

These map directly onto SQL's EXISTS / NOT EXISTS, and they are how you express
"students who are enrolled in something" (semijoin) versus "students enrolled in
nothing" (antijoin).

**Exercise.**
Write a semijoin and an antijoin over Student and Enrol (sharing sid). State in
English what each returns.

**Model answer.**
Semijoin: Student ⋉ Enrol = students who appear in at least one enrolment (took
at least one course). Antijoin: Student ▷ Enrol = students who appear in no
enrolment (took nothing). On the sample data, 101 and 102 are enrolled; 103
(Curie) is enrolled in nothing, so the antijoin returns Curie.

**Feedback notes.**
Tie to the foreign-key intuition: antijoin finds "orphans" relative to a
reference. Preview that NOT EXISTS in SQL is the antijoin.

## Module 23. Division (÷): the "for all" operator

**Goal:** the one operator that expresses universal quantification.

**Teaching content.**
Division answers "find the X related to *every* Y." It is the algebra's way to
say "for all."

**Definition.** Let R have attributes (X, Y) and S have attributes (Y). Then
R ÷ S is the set of X-values that are paired in R with *every* Y-value in S:

R ÷ S = { x : for all y ∈ S, (x, y) ∈ R }.

Canonical use: "students enrolled in every CS course." Let
R = π_(sid, cid)(Enrol) and S = π_(cid)(σ_(dept='CS')(Course)). Then R ÷ S is
exactly the set of sids that are paired with every CS course id.

It can be built from the fundamentals (it is derived), but the universal flavour
is what to remember; everything else in the algebra is existential.

**Exercise.**
In English, what does (π_(sid,cid)(Enrol)) ÷ (π_(cid)(Course)) compute? How does
it differ from the CS-only version above?

**Model answer.**
It computes the sids enrolled in *every* course in the whole Course relation (a
student who has taken every single course offered). The CS-only version divides
by just the CS course ids, so it asks for students who took every CS course
(a weaker, usually larger, set).

**Feedback notes.**
Stress the quantifier contrast: joins/semijoins are "exists"; division is "for
all." This pays off directly when we express division in SQL via double NOT
EXISTS.

## Module 24. Aggregation and grouping (γ)

**Goal:** the practical extension that breaks pure closure, and why.

**Teaching content.**
Pure relational algebra has no counting or summing; a bare number is not a
relation (recall Module 10). So we *extend* the algebra with a grouping /
aggregation operator, written γ.

**Form.** γ_(G; f₁(A₁), …)(R): partition R into groups that agree on the
grouping attributes G, then compute aggregate functions (COUNT, SUM, AVG, MIN,
MAX) per group, producing one result tuple per group. With no grouping
attributes, you get a single tuple over the whole relation.

Example: γ_(dept; COUNT(*) → n)(Instructor) gives one row per department with a
headcount: (CS, 1), (Math, 1) on our data.

This is an extension, not a fundamental operator, because aggregates are
relation-valued only by convention (we wrap the scalars back into tuples).

**Exercise.**
Write a γ-expression for "the average salary per department," and evaluate it on
the sample Instructor instance.

**Model answer.**
γ_(dept; AVG(salary) → avgsal)(Instructor). On the data: (CS, 150), (Math, 140).

**Feedback notes.**
Connect to the closure discussion: aggregation is exactly the place where the
pure algebra needed extending, which is why SQL treats GROUP BY as its own
clause. We formalize the SQL version in Module 35.

# PART IV. LOGIC AND THE RELATIONAL CALCULUS

## Module 25. Declarative versus procedural querying

**Goal:** frame the shift from algebra (how) to calculus (what).

**Teaching content.**
Relational algebra is mildly *procedural*: an expression is a recipe (do this
join, then this selection). The **relational calculus** is purely
*declarative*: you write a logical formula describing the tuples you want, and
say nothing about how to compute them.

This is the same divide you know from elsewhere: an imperative loop that builds a
set versus a set-builder predicate { x : P(x) }. The calculus is the
set-builder side, grounded in first-order predicate logic.

The punchline, proved in Module 29 (Codd's theorem): the two are *equally
expressive*. Anything you can ask procedurally you can ask declaratively, and
vice versa. SQL then sits on top as a practical language closer to the calculus.

**Exercise.**
Classify these as more "procedural" or more "declarative": (a) a relational
algebra expression; (b) the English request "ids of students enrolled in CS305";
(c) a set-builder formula { s.sid : s ∈ Student ∧ ∃ e ∈ Enrol (…) }.

**Model answer.**
(a) procedural (it is a recipe of operations); (b) declarative (states the goal,
not the method); (c) declarative (a logical predicate). The calculus formalizes
the spirit of (b)/(c).

**Feedback notes.**
The set-builder analogy is the anchor. This student will feel at home;
reinforce that the calculus is "just" first-order logic over relations.

## Module 26. Tuple relational calculus (TRC)

**Goal:** the syntax and meaning of TRC.

**Teaching content.**
A **tuple relational calculus** query has the form
{ t : P(t) }
where t is a tuple variable and P is a first-order formula. P is built from:
- atoms R(t)  meaning "tuple t is in relation R,"
- comparisons like t.A = c or t.A = u.B,
- connectives ∧, ∨, ¬, →,
- quantifiers ∃u and ∀u over tuple variables.

The query denotes the set of tuples t making P(t) true.

Example: "ids and names of CS students."
{ t : ∃s ( Student(s) ∧ s.major = 'CS' ∧ t.sid = s.sid ∧ t.sname = s.sname ) }.

**Exercise.**
Write a TRC query for "names of instructors in the Math department."

**Model answer.**
{ t : ∃i ( Instructor(i) ∧ i.dept = 'Math' ∧ t.iname = i.iname ) }.
(On the data: Noether.)

**Feedback notes.**
Watch that the result tuple t is defined attribute by attribute from the bound
variable i. Beginners forget to "export" the wanted attributes into t.

## Module 27. TRC worked examples (joins and existence)

**Goal:** express join-style and existence queries in logic.

**Teaching content.**
Joins become existential quantifiers in TRC. "Names of students enrolled in
CS305" matches a Student tuple with some Enrol tuple:

{ t : ∃s ∃e ( Student(s) ∧ Enrol(e) ∧ e.sid = s.sid ∧ e.cid = 'CS305'
               ∧ t.sname = s.sname ) }.

Read it aloud: there exists a student s and an enrolment e such that they share
sid, the enrolment is for CS305, and we keep the student's name. The shared-sid
equality is the join; the ∃ is exactly the semijoin's "a match exists."

**Exercise.**
Write a TRC query for "names of instructors who teach at least one section"
(use Section and Instructor).

**Model answer.**
{ t : ∃i ∃x ( Instructor(i) ∧ Section(x) ∧ x.iid = i.iid ∧ t.iname = i.iname ) }.

**Feedback notes.**
Point out this is a semijoin in logic form: Instructor ⋉ Section. Connecting the
two representations is the goal of this whole part.

## Module 28. Domain relational calculus and safety

**Goal:** the variant over domain variables, and why unsafe queries are barred.

**Teaching content.**
**Domain relational calculus (DRC)** uses variables that range over *domain
values* (single column values) rather than whole tuples. A relation membership
is written by listing one variable per attribute:
{ ⟨n⟩ : ∃ a, m, y ( Student(a, n, m, y) ∧ m = 'CS' ) }  (names of CS students).

**Safety.** A formula is **unsafe** if it can define an infinite or
domain-dependent answer. Classic example: { t : ¬ Student(t) } would be "all
tuples not in Student," which is infinite (and depends on the universe of
values). A query is **safe** if its answer contains only values appearing in the
database (or in the query's constants) and is finite. We only ever ask safe
queries. SQL enforces a syntactic discipline that keeps you safe.

**Exercise.**
Explain why { t : ¬ Enrol(t) } is unsafe, and rewrite "students who are enrolled
in nothing" as a *safe* query (in words or DRC), anchoring it to Student.

**Model answer.**
{ t : ¬ Enrol(t) } ranges over the whole universe of possible tuples not in
Enrol, which is infinite and domain-dependent, hence unsafe. Safe version:
restrict to actual students who have no enrolment, e.g.
{ ⟨n⟩ : ∃ a,m,y ( Student(a,n,m,y) ∧ ¬∃ c,tm,sn,g Enrol(a,c,tm,sn,g) ) },
i.e. the antijoin Student ▷ Enrol. Every value comes from Student.

**Feedback notes.**
The fix is always "bind negation to a finite base relation." This is the logical
root of why SQL's NOT EXISTS must reference real rows. Connect to the antijoin
(Module 22).

## Module 29. Codd's theorem: algebra = calculus

**Goal:** the central equivalence result.

**Teaching content.**
**Theorem (Codd).** Relational algebra, safe tuple relational calculus, and safe
domain relational calculus are all *equally expressive*: each can express
exactly the same set of queries. A language with this power is called
**relationally complete**.

Why it matters:
- It justifies SQL's design. SQL aims to be (close to) relationally complete, so
  a declarative SQL query can always be realized by an algebra expression, which
  is what the engine actually runs.
- It is the theoretical bridge that lets a query *optimizer* exist: the user
  writes declarative SQL (calculus-like), the system translates to algebra, then
  rewrites the algebra into an efficient but equivalent form.

The proof is constructive (a translation each way) and is beyond our scope, but
the statement is what you carry forward.

**Exercise.**
"Relational completeness" is a yardstick. In one or two sentences, why is it the
right minimum bar for a query language, and why might a real language deliberately
go *beyond* it (hint: recall Module 24)?

**Model answer.**
It is the right minimum because it guarantees the language can express every
query the relational model can define (no arbitrary gaps). Real languages exceed
it because genuinely useful features, like aggregation (COUNT, SUM, AVG),
transitive closure / recursion, and ordering, are *not* expressible in the basic
algebra/calculus, so SQL adds them as extensions.

**Feedback notes.**
Good place to acknowledge the limits: plain relational algebra cannot compute
transitive closure (e.g. prerequisites-of-prerequisites). SQL adds recursive
queries for that. Equivalence is about the *core*, not every extension.

# PART V. SQL AS APPLIED RELATIONAL CALCULUS

## Module 30. SELECT-FROM-WHERE as a calculus formula

**Goal:** read SQL's core block as logic.

**Teaching content.**
The fundamental SQL query block is:

```
SELECT   <output columns>      -- what to keep  (like π / the result tuple)
FROM     <relations>           -- the variables ranging over relations
WHERE    <predicate>           -- the logical condition  (like σ / P(t))
```

Map it to the calculus: FROM introduces tuple variables, WHERE is the predicate
P, SELECT builds the output tuple. So

```
SELECT s.sname
FROM   Student s
WHERE  s.major = 'CS';
```

is exactly { t : ∃s ( Student(s) ∧ s.major='CS' ∧ t.sname=s.sname ) }, which is
π_(sname)(σ_(major='CS')(Student)).

One wrinkle: SQL is **bag (multiset) semantics** by default. It does *not*
eliminate duplicates unless you write SELECT DISTINCT. Pure projection did
dedupe; SQL's SELECT does not. Keep this difference in mind constantly.

**Exercise.**
Translate to algebra and to TRC:
`SELECT iname FROM Instructor WHERE dept='CS' AND salary > 100;`

**Model answer.**
Algebra: π_(iname)(σ_(dept='CS' ∧ salary>100)(Instructor)).
TRC: { t : ∃i ( Instructor(i) ∧ i.dept='CS' ∧ i.salary>100 ∧ t.iname=i.iname ) }.
(On the data: Knuth.)

**Feedback notes.**
If they forget the DISTINCT caveat, remind them: SELECT keeps duplicates, π does
not. This is the single most common mismatch between SQL and the pure model.

## Module 31. Mapping multi-relation SQL to algebra

**Goal:** see FROM-with-several-tables as product + selection.

**Teaching content.**
Listing several relations in FROM and relating them in WHERE is, formally, a
Cartesian product filtered by a selection, i.e. a join (recall Module 18, 20).

```
SELECT s.sname
FROM   Student s, Enrol e
WHERE  s.sid = e.sid AND e.cid = 'CS305';
```

is π_(sname)( σ_(s.sid=e.sid ∧ e.cid='CS305')( Student × Enrol ) ). The
condition s.sid = e.sid is the join condition; without it you would get the full
(meaningless) product. Forgetting a join condition is the infamous "accidental
Cartesian product" bug, and now you can see exactly why it explodes.

**Exercise.**
Rewrite the query above using explicit JOIN ... ON syntax, and say which
relational-algebra operator the ON clause supplies.

**Model answer.**
```
SELECT s.sname
FROM   Student s JOIN Enrol e ON s.sid = e.sid
WHERE  e.cid = 'CS305';
```
The ON clause supplies the theta-join condition (here an equi-join on sid). It
is the σ that turns the product into a meaningful join.

**Feedback notes.**
Stress: "comma in FROM" + "join condition in WHERE" is the same thing as "JOIN …
ON." The latter just makes the join condition syntactically explicit and harder
to forget.

## Module 32. Three-valued logic and NULL (part 1)

**Goal:** introduce NULL and the truth value "unknown."

**Teaching content.**
SQL has a special marker **NULL** meaning "no value / unknown." Its presence
forces **three-valued logic (3VL)**: a predicate can be TRUE, FALSE, or
**UNKNOWN**.

Key rule: almost any comparison with NULL yields UNKNOWN, including `NULL = NULL`.
Truth tables (U = unknown):

```
AND   T U F        OR    T U F        NOT
 T    T U F         T    T T T         T → F
 U    U U F         U    T U U         U → U
 F    F F F         F    T U F         F → T
```

WHERE keeps a row only when its predicate is **TRUE**. UNKNOWN is *not* TRUE, so
rows where the predicate evaluates to UNKNOWN are dropped, the same as FALSE for
filtering purposes.

**Exercise.**
Instructor Lovelace has salary NULL. For the predicate `salary > 100`, what truth
value results, and is her row kept by a WHERE on that predicate?

**Model answer.**
`NULL > 100` evaluates to UNKNOWN. WHERE keeps only TRUE rows, so her row is
dropped (not kept). It is also not kept by `salary <= 100`; she vanishes from
both, which surprises people.

**Feedback notes.**
The "dropped from both a condition and its apparent negation" effect is the
crux. Tee up Module 33 for the practical traps.

## Module 33. NULL gotchas (part 2)

**Goal:** the practical traps that follow from 3VL.

**Teaching content.**
Consequences you must internalize:

1. **Test nulls with IS NULL / IS NOT NULL**, never `= NULL` (which is always
   UNKNOWN, so matches nothing).
2. **A condition and its negation can both drop a row.** `salary > 100` and
   `salary <= 100` both exclude a NULL-salary row. They do not partition the
   table when nulls are present.
3. **Aggregates skip nulls** (except COUNT(*)). AVG(salary) ignores NULL
   salaries; COUNT(salary) counts non-null values; COUNT(*) counts all rows.
4. **`x NOT IN (subquery)` is dangerous** if the subquery can return a NULL: the
   whole thing can become UNKNOWN and yield no rows. Prefer NOT EXISTS.

**Exercise.**
Given salaries {150, 140, NULL}: what does COUNT(*) return? COUNT(salary)?
AVG(salary)? And why is `WHERE dept NOT IN (SELECT dept FROM …)` risky if that
subquery might include a NULL dept?

**Model answer.**
COUNT(*) = 3 (all rows). COUNT(salary) = 2 (non-null values). AVG(salary) = 145
(average of 150 and 140; the NULL is skipped). The NOT IN is risky because if the
subquery yields a NULL, then `dept NOT IN (… , NULL)` evaluates to UNKNOWN for
every row (you cannot prove dept is unequal to an unknown value), so the query
returns no rows; NOT EXISTS avoids this.

**Feedback notes.**
This is a place where correctness intuition built on two-valued logic fails.
Slow down; let them re-derive a couple of cells from the truth tables.

## Module 34. JOINs in SQL

**Goal:** the full join vocabulary in SQL and its algebra mapping.

**Teaching content.**
SQL spells out the joins from Part III:

- `A JOIN B ON …` / `A INNER JOIN B ON …` → theta/inner join.
- `A NATURAL JOIN B` → natural join (matches all same-named columns; use
  sparingly, it is fragile to schema changes).
- `A LEFT JOIN B ON …` → left outer join ⟕ (keep all of A, null-pad B).
- `RIGHT JOIN`, `FULL JOIN` → ⟖, ⟗.
- `A CROSS JOIN B` → Cartesian product ×.

A LEFT JOIN with a `WHERE B.key IS NULL` filter is the standard SQL idiom for an
**antijoin** (rows of A with no match in B).

**Exercise.**
Write SQL to list every instructor and the number of sections they teach,
*including instructors who teach none* (they should show 0). Which join type is
required and why?

**Model answer.**
```
SELECT i.iid, i.iname, COUNT(s.cid) AS n_sections
FROM   Instructor i LEFT JOIN Section s ON i.iid = s.iid
GROUP BY i.iid, i.iname;
```
A LEFT (outer) join is required so instructors with no section are retained;
COUNT(s.cid) counts non-null matches, giving 0 for them. (A plain inner join
would silently drop instructors who teach nothing.)

**Feedback notes.**
Note the deliberate COUNT(s.cid) rather than COUNT(*): on the null-padded rows,
COUNT(*) would wrongly return 1. This ties Module 33's "aggregates skip nulls"
to a real correctness decision.

## Module 35. GROUP BY and HAVING

**Goal:** SQL's grouping/aggregation, mapped to γ.

**Teaching content.**
`GROUP BY G` partitions rows into groups agreeing on G; aggregate functions then
produce one row per group. This is the γ operator (Module 24). `HAVING` filters
*groups* by an aggregate predicate, exactly as WHERE filters *rows*.

```
SELECT   dept, AVG(salary) AS avgsal
FROM     Instructor
GROUP BY dept
HAVING   AVG(salary) > 145;
```

Order of evaluation (important mental model): FROM → WHERE (row filter) →
GROUP BY → HAVING (group filter) → SELECT → ORDER BY. So WHERE cannot see
aggregates (groups do not exist yet); HAVING can.

Rule: every column in SELECT must be either a grouping column or inside an
aggregate. Otherwise the value is ambiguous within a group.

**Exercise.**
Write a query for "departments offering more than one course" using Course.
Then say why `WHERE COUNT(*) > 1` would be illegal and what to use instead.

**Model answer.**
```
SELECT   dept, COUNT(*) AS n
FROM     Course
GROUP BY dept
HAVING   COUNT(*) > 1;
```
(On the data: CS, with 2 courses.) `WHERE COUNT(*) > 1` is illegal because WHERE
runs before grouping, so the aggregate does not yet exist; the group-level
condition must go in HAVING.

**Feedback notes.**
The FROM→WHERE→GROUP BY→HAVING→SELECT pipeline is worth having them recite. Most
GROUP BY confusion dissolves once that order is internalized.

## Module 36. Views: logical data independence in SQL

**Goal:** views as the SQL realization of logical data independence.

**Teaching content.**
Recall Module 2: logical data independence means the logical schema can change
with limited disruption to applications. In SQL, **views** are the mechanism.

A **view** is a named query stored as a definition:

```
CREATE VIEW cs_sections AS
  SELECT s.cid, s.term, s.sec_no, s.room, i.iname
  FROM   Section s JOIN Instructor i ON s.iid = i.iid
  WHERE  i.dept = 'CS';
```

Once defined, `cs_sections` looks like a relation. You query it exactly as you
would a base table:

```
SELECT * FROM cs_sections WHERE term = '2026F';
```

The DBMS rewrites that query by substituting the view definition, producing the
real query over base tables. The user of the view does not need to know the
underlying schema, and if the base schema changes in a compatible way, you can
often update the view definition rather than every query that used it.

This is logical data independence made operational: the view is the stable
interface; the base tables are the implementation.

Views also serve security (expose only some columns), simplification (hide
complex joins from users who do not need to think about them), and naming
frequently-used sub-expressions.

**Exercise.**
(a) Write a view `student_load` that shows each student's sid and the number of
sections they are currently enrolled in (count of Enrol rows per sid). (b) How
does this view illustrate logical data independence if we later decide to rename
the Enrol table?

**Model answer.**
(a)
```
CREATE VIEW student_load AS
  SELECT sid, COUNT(*) AS n_sections
  FROM   Enrol
  GROUP BY sid;
```
(b) If Enrol is renamed, we update the view definition in one place rather than
changing every query that counts enrolments. Queries written against
`student_load` are unaffected: they see the same interface (sid, n_sections)
regardless of what the underlying table is called. That is logical data
independence in action.

**Feedback notes.**
Tie explicitly back to Module 2. If the student asks about updating views (INSERT
through a view), note it as a real but tricky topic (updatable vs non-updatable
views) that Postgres handles; it is beyond our scope here.

## Module 37. Window functions

**Goal:** computation across a window of rows without collapsing them.

**Teaching content.**
`GROUP BY` collapses each group into one row. Sometimes you want aggregate-style
computations without losing the individual rows, for example "each student's
grade alongside the average grade for that course." That is what **window
functions** do.

Syntax:

```
SELECT sid, grade,
       AVG(grade_numeric) OVER (PARTITION BY cid) AS course_avg
FROM   enrol_with_numeric_grade;
```

The `OVER (PARTITION BY …)` clause defines the **window**: the rows to aggregate
over for each output row. The output row is kept; the window function adds an
extra column computed over the window.

Key window functions:
- `AVG / SUM / COUNT / MIN / MAX OVER (…)`: standard aggregates over a window.
- `RANK() OVER (ORDER BY …)`: rank within a partition.
- `ROW_NUMBER() OVER (…)`: sequential numbering.
- `LAG / LEAD OVER (ORDER BY …)`: previous/next row's value.

Why it matters for goal 1 (efficient querying): before window functions, "rank
within a group" required either a self-join or a correlated subquery, both
expensive. Window functions express the same thing in one pass over the data.

**Exercise.**
In plain English, describe what the following query computes (no running needed):
```
SELECT iname, salary,
       RANK() OVER (PARTITION BY dept ORDER BY salary DESC) AS dept_rank
FROM   Instructor;
```

**Model answer.**
For each instructor, it shows their name, salary, and their salary rank within
their own department (rank 1 = highest paid). Instructors in different
departments are ranked independently because of `PARTITION BY dept`. The output
has one row per instructor (not one per department group, as GROUP BY would give).

**Feedback notes.**
The key contrast to drive home: GROUP BY reduces rows; OVER keeps rows and adds
a column. If they struggle with PARTITION BY, compare it to GROUP BY: "partition"
is the window version of "group," but it does not collapse.

## Module 38. Recursive CTEs: transitive closure in SQL

**Goal:** close the gap opened in Module 29 (algebra cannot express transitive closure).

**Teaching content.**
Recall Module 29: relational algebra cannot express transitive closure (e.g.,
"all prerequisites of a prerequisite, recursively"). SQL extends algebra with
recursive Common Table Expressions (CTEs) for exactly this.

A **CTE** (`WITH` clause) names a subquery for reuse:

```
WITH ranked AS (
  SELECT iname, salary,
         RANK() OVER (PARTITION BY dept ORDER BY salary DESC) AS rk
  FROM Instructor
)
SELECT * FROM ranked WHERE rk = 1;
```

A **recursive CTE** adds a self-referencing `UNION ALL`:

```
WITH RECURSIVE prereq(cid, prereq_cid) AS (
  -- base case: direct prerequisites
  SELECT cid, prereq_cid FROM DirectPrereq
  UNION ALL
  -- recursive step: add one more level
  SELECT r.cid, d.prereq_cid
  FROM   prereq r JOIN DirectPrereq d ON r.prereq_cid = d.cid
)
SELECT * FROM prereq WHERE cid = 'CS305';
```

The engine iterates the recursive step until no new rows appear. This computes
the transitive closure of the prerequisite relation, something plain algebra or
non-recursive SQL cannot do.

This closes the expressiveness gap noted in Module 29: recursion/transitive
closure sits *above* relational completeness and requires an explicit extension.
SQL provides it; the relational algebra by itself does not.

**Exercise.**
The recursive CTE above terminates because the relation is acyclic (no circular
prerequisites). What would happen if there were a cycle (A is a prereq of B, B
is a prereq of A)? How do real systems protect against this?

**Model answer.**
The recursive step would keep adding the same rows in an infinite loop; the
query would not terminate. Real systems protect against this with: (a) a depth
limit (`WITH RECURSIVE … LIMIT` or a depth counter column), (b) a visited-set
check (`WHERE cid NOT IN (SELECT cid FROM visited)`), or (c) `UNION` instead of
`UNION ALL` to deduplicate (stops when no new rows are added, which detects
fixpoint even on cyclic graphs). PostgreSQL supports all three strategies.

**Feedback notes.**
The cycle question is important for a student with graph-algorithm background;
they will immediately recognise it as a DFS/BFS termination issue. Reward any
answer that frames it as a fixpoint / visited-set problem.

## Module 39. Subqueries and correlation

**Goal:** nested queries, and the difference between uncorrelated and correlated.

**Teaching content.**
A **subquery** is a query inside another. Two flavours:

- **Uncorrelated:** the inner query does not reference the outer; it is computed
  once. Example: `WHERE salary > (SELECT AVG(salary) FROM Instructor)`.
- **Correlated:** the inner query references a column from the outer row, so it
  is conceptually re-evaluated per outer row. Example:
  `WHERE salary > (SELECT AVG(salary) FROM Instructor x WHERE x.dept = i.dept)`
  (above the department average).

Correlation is the SQL realization of a quantifier whose range depends on the
current tuple, much like a nested ∃/∀ in the calculus whose bound variable is
constrained by the outer variable.

**Exercise.**
Write a correlated subquery: instructors who earn more than the average salary in
their *own* department. Explain what the correlation refers to.

**Model answer.**
```
SELECT i.iname
FROM   Instructor i
WHERE  i.salary > (SELECT AVG(x.salary)
                   FROM   Instructor x
                   WHERE  x.dept = i.dept);
```
The correlation is `x.dept = i.dept`: the inner average is recomputed relative to
the outer row's department, so each instructor is compared to peers in the same
department.

**Feedback notes.**
If they write it uncorrelated (global average), point at the requirement "their
own department" and ask what must change. The inner WHERE referencing the outer
alias is the whole idea.

## Module 40. EXISTS and IN (existential quantification)

**Goal:** the SQL forms of "a match exists."

**Teaching content.**
Both express "there is a matching row," i.e. the semijoin / ∃ of the calculus.

- `EXISTS (subquery)` is TRUE iff the subquery returns at least one row. Usually
  correlated.
- `x IN (subquery)` is TRUE iff x equals some value the subquery returns.

```
-- students who are enrolled in something (semijoin Student ⋉ Enrol)
SELECT s.sname
FROM   Student s
WHERE  EXISTS (SELECT 1 FROM Enrol e WHERE e.sid = s.sid);
```

Prefer EXISTS over IN when nulls may appear in the subquery, for the reason in
Module 33. EXISTS only cares whether a row exists, so nulls do not poison it.

**Exercise.**
Write, using EXISTS, "courses that are offered as at least one section" (Course
has a match in Section on cid). Then state the algebra operator this realizes.

**Model answer.**
```
SELECT c.title
FROM   Course c
WHERE  EXISTS (SELECT 1 FROM Section s WHERE s.cid = c.cid);
```
It realizes the semijoin Course ⋉ Section. (On the data: all three courses have a
section.)

**Feedback notes.**
`SELECT 1` (or `SELECT *`) inside EXISTS is idiomatic: the projected value is
irrelevant, only row existence matters. Reassure them this is not a typo.

## Module 41. Division in SQL: the double NOT EXISTS

**Goal:** express "for all" in SQL, the hardest common pattern.

**Teaching content.**
SQL has no division operator, so universal quantification ("for *every* …") is
encoded with **two negations**: "for all y, P" becomes "there is no y for which
not P." This double-NOT-EXISTS is the standard idiom and is worth memorizing.

"Students enrolled in *every* CS course":

```
SELECT s.sid
FROM   Student s
WHERE  NOT EXISTS (                         -- there is no CS course ...
   SELECT 1 FROM Course c
   WHERE  c.dept = 'CS'
   AND    NOT EXISTS (                      -- ... that this student is NOT enrolled in
      SELECT 1 FROM Enrol e
      WHERE  e.sid = s.sid AND e.cid = c.cid));
```

Read literally: keep student s such that there is no CS course that s failed to
enrol in, i.e. s is enrolled in all of them. This is exactly R ÷ S from
Module 23.

**Exercise.**
Translate the logic of the query into a one-sentence "there is no … that …"
statement, and identify which NOT EXISTS plays the role of the universal
quantifier.

**Model answer.**
"Keep student s for whom there is no CS course that s is not enrolled in." The
*outer* NOT EXISTS over courses encodes the universal ("for all CS courses"); the
*inner* NOT EXISTS encodes the per-course "s is enrolled in it" by negating "s is
not enrolled in it." Together: ∀ course ∃ enrolment.

**Feedback notes.**
This is the conceptual summit of the SQL part. If they struggle, walk the
∀x P(x) ≡ ¬∃x ¬P(x) equivalence first, then map each ∃/¬ onto a NOT EXISTS.

## Module 42. Set operations: UNION, INTERSECT, EXCEPT

**Goal:** SQL's ∪, ∩, − and the DISTINCT/ALL distinction.

**Teaching content.**
SQL provides the set operators directly, requiring union-compatible inputs
(Module 13):

- `UNION` (= ∪), `INTERSECT` (= ∩), `EXCEPT` (= −, called MINUS in some systems).
- By default these **eliminate duplicates** (true set semantics). The `ALL`
  variants (`UNION ALL`, etc.) keep duplicates (bag semantics) and are cheaper
  because no dedupe is needed.

Note the asymmetry with SELECT: a plain SELECT keeps duplicates, but a plain
UNION removes them. SQL's defaults are not uniform; know each one.

**Exercise.**
Using EXCEPT, write "sids of students who are enrolled in nothing" from Student
and Enrol. What algebra operator is this, and what is the bag-vs-set behaviour
of EXCEPT here?

**Model answer.**
```
SELECT sid FROM Student
EXCEPT
SELECT sid FROM Enrol;
```
This is the antijoin / difference on sid (Student ▷ Enrol projected to sid). On
the data it returns 103 (Curie). EXCEPT uses set semantics by default, so the
result has distinct sids.

**Feedback notes.**
Contrast with the NOT EXISTS version (Module 40/41): same answer, different
phrasing. Both are worth recognizing; EXCEPT is often the most readable for
"in A but not B."

## Module 43. Reasoning about query equivalence

**Goal:** use algebraic laws to argue two SQL queries are the same (or not).

**Teaching content.**
Because SQL maps to algebra, you can reason about equivalence with algebraic
laws, the same laws an optimizer uses:

- Selections commute: σ_p(σ_q(R)) = σ_q(σ_p(R)) = σ_(p∧q)(R).
- Selection pushes through join when it mentions only one side:
  σ_p(R ⋈ S) = σ_p(R) ⋈ S if p uses only R's attributes.
- Projection pushes down, keeping any attribute needed later.
- Join is commutative and associative (in the set model).

Caveat that bites in SQL: these laws hold cleanly under *set* semantics. Under
*bag* semantics, duplicate counts can differ. For example, pushing a projection
that drops a key can change how many duplicates survive. So "equivalent in the
algebra" needs the extra check "and the duplicate semantics match."

**Exercise.**
Are these equivalent, and under what assumption?
(A) `SELECT sname FROM Student WHERE major='CS' AND year=2`
(B) `SELECT sname FROM (SELECT * FROM Student WHERE major='CS') t WHERE year=2`

**Model answer.**
Yes, equivalent. (B) just applies the two selection conditions in two stages;
σ_(year=2)(σ_(major='CS')(Student)) = σ_(major='CS' ∧ year=2)(Student) by
commutativity/combination of selections. Both keep duplicates identically, so
they agree under bag semantics too. The only assumption is that no projection
drops a column either condition needs (it does not here).

**Feedback notes.**
Reward explicit mention of bag-vs-set. The whole point of this module is that
algebraic intuition is valid but must be checked against SQL's duplicate
behaviour.

# PART VI. DESIGN I: ENTITY-RELATIONSHIP MODELING

## Module 44. ER modeling: entities and attributes

**Goal:** the first half of the conceptual design vocabulary.

**Teaching content.**
Before a relational schema exists, designers sketch a conceptual model. The
**entity-relationship (ER) model** has three core notions; this module covers
two.

- An **entity** is a distinguishable thing in the modelled world (a particular
  student, a particular course). An **entity set** is a collection of
  same-typed entities (all students). It will usually become a relation.
- An **attribute** is a property of an entity (a student's name, year). One or
  more attributes form a **key** that identifies entities in the set.

Attributes have variants worth naming: **composite** (an address made of
street/city), **multivalued** (a person's several phone numbers), and
**derived** (age, computable from birthdate). Each needs deliberate handling
when you map to relations.

Notation (Chen-style): entity sets are rectangles, attributes are ovals, keys
are underlined.

**Exercise.**
For our domain, identify three plausible entity sets and, for one of them, give
its attributes and mark the key. Flag any attribute that is composite,
multivalued, or derived.

**Model answer.**
Entity sets: Student, Course, Instructor (Section and Enrol are arguably
relationships, see Module 45). For Student: key sid (underlined), plus sname,
major, year. If we added "phone numbers," that would be multivalued; a full
"address" would be composite; "GPA" computed from grades would be derived.

**Feedback notes.**
Accept Section/Enrol as entity sets too; the entity-vs-relationship line is
genuinely a modelling choice, which sets up the next module nicely.

## Module 45. Relationships and cardinality

**Goal:** the third notion, and how many-to-many shapes the schema.

**Teaching content.**
A **relationship** associates two (or more) entities. A **relationship set** is
a collection of such associations (the set of all "student X is enrolled in
section Y" facts). Relationships can carry their own attributes (the grade in an
enrolment belongs to the *relationship*, not to the student or the section).

**Cardinality** constrains how many entities relate to how many:
- **one-to-one (1:1):** each side matches at most one of the other.
- **one-to-many (1:N):** one instructor teaches many sections; each section has
  one instructor.
- **many-to-many (M:N):** a student enrols in many sections; a section has many
  students.

Cardinality drives the relational mapping (Module 48): M:N relationships always
become their own relation; 1:N can often be folded into the "many" side.

**Exercise.**
Classify the cardinalities: (a) Instructor–Section ("teaches"); (b)
Student–Section ("enrols in"). For (b), where does the grade attribute belong,
and why?

**Model answer.**
(a) one-to-many (one instructor, many sections; each section one instructor).
(b) many-to-many (a student takes many sections; a section has many students).
The grade belongs to the *enrols-in relationship*, because it is a fact about
the pairing of a specific student with a specific section, not a property of the
student alone or the section alone.

**Feedback notes.**
The "grade lives on the relationship" point is the key insight; it is exactly
why Enrol exists as its own relation with grade as an attribute.

## Module 46. ER is not object-oriented design

**Goal:** dissolve the student's stated OO/ER confusion head-on.

**Teaching content.**
This is the module the student specifically asked for. The OO and ER mindsets
overlap superficially and differ in deep ways.

| Aspect | OO design | ER / relational |
|---|---|---|
| Core unit | Object with identity + **behaviour** (methods) | Entity = pure **data**, no behaviour |
| Identity | Implicit object reference (pointer) | Explicit **key** (value-based) |
| Association | Object holds a reference to another object | **Relationship** via matching key values |
| Many-to-many | Often a collection field inside one object | A separate **relation** (no "owner") |
| Inheritance | First-class (subclasses, polymorphism) | Not native; simulated by extra tables |
| Encapsulation | Central (hide state behind methods) | Absent; data is open to declarative queries |

Two traps for the OO-trained:
1. **Relationships are not method calls or owned references.** "Student enrols
   in Section" is a free-standing set of pairs, owned by neither side. There is
   no pointer from Student to its Sections.
2. **Identity is by value, not by reference.** Two tuples are "the same entity"
   iff their key values match, full stop. There is no hidden object id.

The mismatch between these worldviews is well known in practice as the
object-relational impedance mismatch.

**Exercise.**
An OO programmer models enrolment as `Student.courses : List<Course>` (a course
list field on Student). Give two distinct reasons this is the wrong shape in the
relational world, and state the relational alternative.

**Model answer.**
(1) The relationship is M:N and carries its own attribute (grade); it cannot live
as a list field on one side, because the grade is a fact about the pair, not the
student. (2) Relationships are not owned references; storing courses "inside"
Student reintroduces pointer-style ownership and breaks value-based, symmetric
querying (you could not as naturally ask "who is in this course?"). Relational
alternative: a separate Enrol relation of (sid, cid, …, grade) pairs, queried by
joining from either side.

**Feedback notes.**
This module often produces an "aha." Let the student articulate the difference
in their own words; correct any lingering "the relationship belongs to one
class" framing.

## Module 47. Weak entities and participation constraints

**Goal:** two refinements that capture real constraints.

**Teaching content.**
**Weak entity set.** An entity set with no key of its own; it is identified only
in combination with another (its *identifying owner*) via an *identifying
relationship*. Its full key is the owner's key plus its own **discriminator**
(partial key). Our Section is naturally weak: a section number like "1" means
nothing without the course and term; its key is (cid, term, sec_no), borrowing
cid from Course.

**Participation constraint.**
- **Total participation:** every entity must participate in the relationship
  (every Section must have an Instructor). Drawn as a double line.
- **Partial participation:** participation is optional (an Instructor need not
  teach any Section).

Participation + cardinality together pin down whether a foreign key can be null
and whether a side can be folded into another relation.

**Exercise.**
(a) Why is Section best modelled as a weak entity? (b) State the participation of
Section in "taught-by-Instructor" and of Instructor in the same relationship, and
say which one allows a null.

**Model answer.**
(a) sec_no is not unique on its own ("section 1" recurs across courses and
terms); a section is only identifiable relative to its course and term, so its
identity depends on the owning Course (key = cid + term + sec_no). (b) Section
has *total* participation (every section is taught by some instructor, so
Section.iid cannot be null); Instructor has *partial* participation (an
instructor may teach nothing). The optional side (an instructor with no section)
is what shows up as null in a LEFT JOIN, not a null foreign key.

**Feedback notes.**
Tie total participation back to referential integrity and the LEFT-JOIN antijoin
of Module 34: partial participation is precisely what creates unmatched rows.

## Module 48. Translating ER to relations

**Goal:** the mechanical mapping from diagram to schema.

**Teaching content.**
Standard translation rules:

1. **Strong entity set → relation** with the same attributes; its key becomes
   the primary key.
2. **Weak entity set → relation** including the owner's key as part of its own
   key, with a foreign key to the owner (Section gets cid).
3. **M:N relationship → its own relation** whose key is the combination of the
   participating entities' keys, plus any relationship attributes (Enrol:
   (sid, cid, term, sec_no, grade)).
4. **1:N relationship → fold into the "many" side** by adding a foreign key
   there (Section gets iid; no separate "teaches" table needed).
5. **1:1 relationship → a foreign key on either side** (place it on the side
   with total participation if there is one, to avoid nulls).
6. **Composite attribute → its component columns; multivalued attribute → its
   own relation; derived attribute → usually not stored.**

Apply these to our domain and you recover exactly the running schema. That is a
good sign the schema is well-formed.

**Exercise.**
Walk the rules to derive the relations for: Course (strong), Section (weak,
taught-by 1:N Instructor), and the M:N enrols-in between Student and Section.
Confirm you land on the running schema.

**Model answer.**
Course → Course(cid, title, dept, credits), key cid (rule 1). Section is weak on
Course and absorbs the 1:N "teaches" by adding iid → Section(cid, term, sec_no,
iid, room), key (cid, term, sec_no), FKs cid→Course, iid→Instructor (rules 2,4).
The M:N enrols-in becomes Enrol(sid, cid, term, sec_no, grade), key = both
sides' keys, grade as the relationship attribute (rule 3). This is the running
schema.

**Feedback notes.**
Recovering the given schema from first principles is the reward here. If a
student instead makes a separate "teaches" table, note it is not wrong, just
not minimal, since 1:N folds in.

# PART VII. DESIGN II: FUNCTIONAL DEPENDENCIES AND NORMALIZATION

## Module 49. Functional dependencies

**Goal:** the single concept underlying all of normalization.

**Teaching content.**
**Definition.** A **functional dependency (FD)** X → Y (read "X determines Y")
holds on a relation if any two tuples that agree on all attributes in X must also
agree on all attributes in Y. X and Y are sets of attributes.

It is a *semantic* constraint about the real world, asserted by the designer,
not something read off one instance (though an instance can *violate* a claimed
FD and thereby refute it).

Examples on our domain:
- sid → sname, major, year (a student id determines that student's facts).
- cid → title, dept, credits (a course id determines the course's facts).
- A candidate key is exactly a set of attributes that functionally determines
  *every* attribute of the relation (recall Module 8). Keys are a special case
  of FDs.

**Exercise.**
(a) State a sensible FD on Instructor. (b) Does dept → salary hold on Instructor?
Argue from the meaning, not just the tiny instance.

**Model answer.**
(a) iid → iname, dept, salary (the instructor id determines all instructor
facts). (b) No: a department generally has many instructors with different
salaries, so knowing the department does not pin down a unique salary. The small
instance happens not to contradict it (one instructor per dept), but the *meaning*
says it should not hold; an instance with two CS instructors on different
salaries would refute it.

**Feedback notes.**
Drive home "FDs are claims about all legal instances, not artifacts of one
sample." A passing instance never *proves* an FD; a single counterexample
*disproves* it.

## Module 50. Armstrong's axioms

**Goal:** the formal inference rules for functional dependencies.

**Teaching content.**
From a set of FDs you can *infer* others using a proof system. **Armstrong's
axioms** are sound and complete for FD implication (every derivable FD holds,
and every implied FD is derivable):

- **Reflexivity:** if Y ⊆ X then X → Y. (A set of attributes trivially
  determines any subset of itself.)
- **Augmentation:** if X → Y then XZ → YZ. (Here XZ means X ∪ Z, for any
  attribute set Z; adding the same attributes to both sides preserves the FD.)
- **Transitivity:** if X → Y and Y → Z then X → Z.

Useful derived rules (all provable from the three axioms):
- **Union:** if X → Y and X → Z then X → YZ.
- **Decomposition:** if X → YZ then X → Y and X → Z.
- **Pseudotransitivity:** if X → Y and WY → Z then WX → Z.

Analogy: this is a Hilbert-style proof system for a specific theory, much like
propositional logic has modus ponens + substitution. "Sound and complete" here
means complete for FD-implication derivation (not Gödel-completeness).

**Exercise.**
Using the axioms, derive iid, dept → iname from {iid → iname, dept → dept_head}.
Identify which axiom each step uses.

**Model answer.**
1. iid → iname (given).
2. iid, dept → iname (augmentation: add dept to both sides of step 1, giving
   iid ∪ {dept} → iname ∪ {dept}; then decomposition gives iid, dept → iname).
Shorter path: by augmentation directly, iid → iname gives iid, dept → iname,
dept; by decomposition, iid, dept → iname. ✓

**Feedback notes.**
Accept any derivation that names the axioms, even informally. The key habit is
justifying each step. If they skip to "obviously iid,dept determines iname since
iid does," have them write that as augmentation + decomposition.

## Module 51. Attribute closure

**Goal:** the algorithm for computing what an attribute set determines.

**Teaching content.**
**Definition.** Given attribute set X and FD set F, the **closure** X⁺ is the
set of all attributes functionally determined by X under F.

**Algorithm:**
1. Start: result := X.
2. Repeat: for every FD V → W in F, if V ⊆ result, add W to result.
3. Stop when nothing new can be added (fixpoint).

Why it matters:
- X is a **superkey** iff X⁺ = all attributes of the relation.
- An FD X → Y is implied by F iff Y ⊆ X⁺.

So closure is how you test candidate keys and verify implied FDs, without
enumerating all derivations. It is the workhorse of the entire Part VII.

**Exercise.**
On Section with FDs { (cid,term,sec_no) → iid, room ; iid → dept }, compute
{cid,term,sec_no}⁺. Is {cid,term,sec_no} a superkey of this (extended) relation?

**Model answer.**
Start {cid,term,sec_no}. Apply (cid,term,sec_no)→iid,room: V={cid,term,sec_no}
⊆ current → add iid, room → {cid,term,sec_no,iid,room}. Apply iid→dept: V={iid}
⊆ current → add dept → {cid,term,sec_no,iid,room,dept}. Fixpoint. If those six
are all the attributes, then {cid,term,sec_no}⁺ = all attributes, so it is a
superkey (and minimal here, so a candidate key too).

**Feedback notes.**
Have them narrate each iteration: "which FD fired, what was added." The
transitive pickup of dept via iid in step 2 is the instructive moment: it shows
why transitive dependencies survive even when you do not add the transitive FD
explicitly to F.

## Module 52. The three anomalies

**Goal:** motivate normalization by the pain bad design causes.

**Teaching content.**
Suppose we foolishly merged everything into one wide relation
BadEnrol(sid, sname, major, cid, title, dept, credits, grade). Storing course
facts repeatedly per enrolment causes three **anomalies**:

- **Update anomaly:** CS305's title is stored in every enrolment row for it;
  renaming the course means updating many rows, and missing one leaves the data
  inconsistent.
- **Insertion anomaly:** you cannot record a brand-new course with no enrolments
  yet, because there is no row to put it in without a (possibly fake) student.
- **Deletion anomaly:** deleting the last enrolment for a course also erases the
  course's existence (title, credits) entirely.

The root cause is **redundancy** driven by an FD whose left side is not a key:
cid → title, dept, credits is being stored over and over. Normalization removes
exactly this kind of redundancy.

**Exercise.**
For BadEnrol, give a concrete instance-level example of each of the three
anomalies using CS305.

**Model answer.**
Update: CS305 appears in Ada's row; if another student also took CS305, its title
is duplicated, and renaming "Databases" requires changing every CS305 row or risk
inconsistency. Insertion: a newly created course CS999 with no enrolees cannot be
stored, since every row needs a sid/grade. Deletion: if Ada's CS305 enrolment is
the only CS305 row and we delete it, the facts title='Databases', credits=4
vanish with it.

**Feedback notes.**
Anchor each anomaly to the offending FD cid → (title, dept, credits). The fix
(splitting Course out) is the concrete payoff of the next modules.

## Module 53. First and second normal form (1NF, 2NF)

**Goal:** the first two rungs of the normalization ladder.

**Teaching content.**
**1NF.** Every attribute value is atomic; no repeating groups, lists, or nested
relations in a cell. This is baked into our definition of a relation (Module 5),
so we treat 1NF as the entry condition.

**2NF.** A relation is in 2NF if it is in 1NF and **no non-prime attribute is
partially dependent on a candidate key**, i.e. every non-key attribute depends on
the *whole* key, not just part of it. (A *prime* attribute is one in some
candidate key; *non-prime* otherwise.) 2NF only bites when a candidate key is
*composite*; with a single-attribute key, 2NF is automatic.

Example of a 2NF violation: in Enrol-plus-title with key (sid, cid, term,
sec_no), an attribute `title` depends on cid alone (a *part* of the key). That
partial dependency violates 2NF; title should live with cid in Course.

**Exercise.**
Consider R(sid, cid, sname, grade) with candidate key (sid, cid). Identify any
partial dependency and say whether R is in 2NF. If not, decompose it.

**Model answer.**
sname depends on sid alone, which is *part* of the key (sid, cid), so sname is
partially dependent on the key. R is *not* in 2NF. Decompose into
R1(sid, sname) and R2(sid, cid, grade); now in R2 the non-key grade depends on
the whole key, and R1's sname depends on its full key sid.

**Feedback notes.**
Make the "partial = depends on part of a composite key" definition crisp.
Reinforce that single-column keys cannot have partial dependencies, so 2NF is
only interesting with composite keys.

## Module 54. Third normal form (3NF)

**Goal:** eliminate transitive dependencies on the key.

**Teaching content.**
A **transitive dependency** exists when key → X → A, where X is a non-key
attribute that in turn determines A. A depends on the key *transitively* (via X)
rather than directly. The redundancy this causes is the same problem as in 2NF
but for non-prime attributes rather than partial keys.

**3NF.** A relation is in 1NF and, for every *nontrivial* FD X → A, at least one
of these holds: (1) X is a superkey, or (2) A is a **prime** attribute (member
of some candidate key). The key word is *or*: both conditions are alternatives,
with no prior restriction on A. Informally: the only non-superkey determinants
allowed are those where the determined attribute is already part of some key.

Classic violation: in Section-plus-dept with key (cid, term, sec_no) and FDs
(cid,term,sec_no) → iid and iid → dept, we have a transitive dependency
key → iid → dept. dept depends on iid, which is not a key. Storing dept here
duplicates the instructor-department fact. Fix: split out (iid, dept) into its
own relation (it already lives in Instructor).

3NF is the usual practical target: it removes most redundancy while always being
achievable with a lossless, dependency-preserving decomposition (Module 57).

**Exercise.**
R(iid, iname, dept, dept_head) with FDs iid → iname, dept and dept → dept_head.
Find the transitive dependency and decompose R into 3NF.

**Model answer.**
Key is iid. Transitive dependency: iid → dept → dept_head, where dept is
non-prime; so dept_head depends transitively on the key via the non-key dept,
violating 3NF. Decompose into R1(iid, iname, dept) and R2(dept, dept_head). Now
each non-key attribute depends directly on its relation's key.

**Feedback notes.**
"key → nonkey → nonkey" is the pattern to spot. Connect to the deletion anomaly:
the last instructor leaving a department would otherwise erase the dept_head fact.

## Module 55. Boyce-Codd normal form (BCNF)

**Goal:** the stricter form and exactly how it exceeds 3NF.

**Teaching content.**
**BCNF.** A relation is in BCNF if for *every* nontrivial FD X → Y, X is a
superkey. No exceptions for prime attributes (that is the only difference from
3NF). BCNF is named for Raymond Boyce and Edgar Codd.

3NF allows one loophole: an FD X → A where A is *prime* (part of some candidate
key) is tolerated even if X is not a superkey. BCNF closes that loophole. So
every BCNF relation is in 3NF, but not conversely.

The textbook gap case: R(student, subject, teacher) where each teacher teaches
one subject (teacher → subject) and a student-subject pair has one teacher
((student, subject) → teacher). Candidate keys: (student, subject) and
(student, teacher). The FD teacher → subject has a non-superkey left side, so R
is *not* in BCNF, yet it *is* in 3NF (because subject is prime). Decomposing to
BCNF here loses the ability to enforce (student, subject) → teacher within a
single relation, which sets up the next module's trade-off.

**Exercise.**
For R(student, subject, teacher) with FDs { teacher → subject ; (student,
subject) → teacher }, confirm it is in 3NF but not BCNF, naming the offending FD
and why each normal form judges it differently.

**Model answer.**
Candidate keys: (student, subject) and (student, teacher); prime attributes:
student, subject, teacher (all appear in some key). The FD teacher → subject has
left side {teacher}, which is not a superkey, so it violates BCNF. But 3NF is
satisfied, because the dependent attribute subject is *prime*, and 3NF permits
X → A when A is prime even if X is not a superkey. That single allowance is the
entire difference between the two forms here.

**Feedback notes.**
This is subtle; let them compute the candidate keys first. The "subject is prime,
so 3NF forgives it but BCNF does not" line is the exact hinge.

## Module 56. Lossless-join decomposition

**Goal:** the correctness criterion for splitting a relation.

**Teaching content.**
When you decompose R into R1 and R2, you must be able to reconstruct R *exactly*
by joining them back. A decomposition is **lossless** if R1 ⋈ R2 = R for every
legal instance (no spurious tuples appear, none are lost).

**Test (binary case).** The decomposition of R into R1 and R2 is lossless iff the
common attributes form a key of at least one piece:
(R1 ∩ R2) → R1, or (R1 ∩ R2) → R2 (the shared attributes functionally determine
one of the two relations).

A decomposition that fails this is **lossy**: joining back manufactures
phantom tuples that were never in R. Lossless-join is non-negotiable; a lossy
split corrupts the data's meaning.

**Exercise.**
We split R(iid, iname, dept) into R1(iid, iname) and R2(iid, dept). Their common
attribute is iid. Is this lossless? Apply the test.

**Model answer.**
Common attribute is {iid}. Since iid → iname (so iid → R1's attributes) and also
iid → dept (so iid → R2), {iid} is a key of both pieces; the test (R1∩R2 → R1 or
→ R2) is satisfied. The decomposition is lossless: R1 ⋈ R2 on iid reconstructs R
exactly.

**Feedback notes.**
Contrast with a lossy split (e.g. on a non-key shared attribute) to show phantom
tuples appearing. The "shared attributes must key one side" rule is the thing to
remember.

## Module 57. Dependency preservation; 3NF versus BCNF

**Goal:** the fundamental trade-off, and why 3NF is the usual target.

**Teaching content.**
A decomposition is **dependency-preserving** if every original FD can be
enforced by checking FDs *within* individual decomposed relations, with no join
required. This matters because checking a constraint that spans relations (needs
a join on every update) is expensive and awkward.

The deep result:
- **3NF** is always achievable with a decomposition that is *both* lossless-join
  *and* dependency-preserving.
- **BCNF** is always achievable lossless-join, but **sometimes not while also
  preserving dependencies.** The R(student, subject, teacher) case from Module 55
  is the standard example: forcing BCNF splits it so that (student, subject) →
  teacher can no longer be checked without a join.

So there is a genuine tension: BCNF removes more redundancy but may cost you
cheap constraint enforcement. Practitioners typically aim for 3NF and accept
BCNF only when it does not sacrifice dependency preservation. This is a real
engineering trade-off, not a defect.

**Exercise.**
You decompose R(student, subject, teacher) to reach BCNF and find you can no
longer enforce (student, subject) → teacher without joining. What property has
been lost, what is gained, and what would you choose in practice and why?

**Model answer.**
Lost: dependency preservation (that FD now spans two relations, so enforcing it
needs a join on each update). Gained: BCNF, i.e. removal of the teacher → subject
redundancy. In practice many designers stay at 3NF here, keeping the relation
intact so (student, subject) → teacher is enforceable locally, accepting the
small teacher→subject redundancy. The right call depends on update frequency
versus how costly/likely the redundancy-driven anomalies are. Either justified
answer is acceptable.

**Feedback notes.**
This is the conceptual peak of the design part. The takeaway: normalization is a
trade-off space (redundancy vs enforceability), and 3NF is the pragmatic default
precisely because it can always have both lossless join and dependency
preservation.

# PART VIII. SYSTEMS: TRANSACTIONS, CONCURRENCY, OPTIMIZATION

## Module 58. Transactions and ACID

**Goal:** what a transaction is and the four guarantees.

**Teaching content.**
A **transaction** is a unit of work, a sequence of reads and writes, that the
database treats as a single indivisible logical operation. The canonical example
is a transfer: debit one account, credit another; both must happen or neither.

The **ACID** guarantees:
- **Atomicity:** all of the transaction's effects happen, or none do. A failure
  midway is rolled back.
- **Consistency:** a transaction moves the database from one state satisfying all
  constraints to another such state (the transaction is responsible for the
  logic; the system enforces declared constraints).
- **Isolation:** concurrently running transactions do not see each other's
  partial, uncommitted effects; the result is as if they ran in some serial
  order.
- **Durability:** once committed, effects survive crashes (typically via a
  write-ahead log).

Isolation is the deep one and the subject of the next two modules.

**Exercise.**
For a funds transfer (debit A, credit B), name which ACID property each scenario
threatens: (a) the system crashes after the debit but before the credit; (b)
another transaction reads A and B midway and sees money that has vanished; (c)
the commit is acknowledged but a crash loses it.

**Model answer.**
(a) Atomicity (a partial effect persisted); (b) Isolation (another transaction
observed an inconsistent intermediate state); (c) Durability (a committed effect
did not survive a crash).

**Feedback notes.**
Consistency is the property students most often misattribute; clarify it is about
preserving declared invariants across the whole transaction, leaning on atomicity
and isolation to do so.

## Module 59. Schedules and serializability

**Goal:** the formal correctness criterion for concurrent execution.

**Teaching content.**
When transactions run concurrently, the system interleaves their operations into
a **schedule**. We want a criterion for "this interleaving is correct."

**Serial schedule:** transactions run one fully after another, no interleaving.
Serial schedules are correct by definition (no concurrency to go wrong).

**Serializable schedule:** an interleaved schedule whose net effect equals *some*
serial schedule of the same transactions. Serializability is the gold-standard
correctness criterion: you get concurrency's performance with serial's
correctness.

The practical test is **conflict serializability**. Two operations *conflict* if
they are from different transactions, touch the same data item, and at least one
is a write. Build a **precedence graph** (a node per transaction, an edge Ti→Tj
when an operation of Ti conflicts with and precedes one of Tj). The schedule is
conflict-serializable iff this graph is **acyclic**, and a topological sort of it
gives an equivalent serial order.

**Exercise.**
T1 reads then writes X; T2 reads then writes X. The schedule is: R1(X), R2(X),
W1(X), W2(X). Draw the precedence-graph edges and decide whether it is conflict
serializable.

**Model answer.**
Conflicts (pairs of operations from different transactions on the same item,
at least one a write, in schedule order):
- R1(X) before W2(X): T1→T2.
- R2(X) before W1(X): T2→T1.
- W1(X) before W2(X): T1→T2 (duplicate: the graph already has this edge).

A precedence graph has at most one directed edge per ordered pair of transactions.
So the graph has exactly two distinct edges: T1→T2 and T2→T1. They form a cycle;
the schedule is **not** conflict serializable. (This is the classic lost-update
interleaving.)

**Feedback notes.**
Acyclic ⇔ serializable is the rule. If they miss the T2→T1 edge, point at R2(X)
preceding W1(X): a read-before-write on the same item across transactions is a
conflict. Stress that duplicate edges collapse to one; the graph node/edge count
is over *transactions*, not operations.

## Module 60. Isolation levels and concurrency anomalies

**Goal:** the SQL-standard levels and what each permits.

**Teaching content.**
Full serializability can be costly, so SQL defines weaker **isolation levels**
that permit certain anomalies in exchange for concurrency. The named anomalies:

- **Dirty read:** reading another transaction's *uncommitted* write.
- **Non-repeatable read:** re-reading a row and getting a different value because
  another transaction committed an update in between.
- **Phantom read:** re-running a range query and finding new rows that another
  transaction inserted.

The levels, from weakest to strongest:

| Level | Dirty read | Non-repeatable | Phantom |
|---|---|---|---|
| READ UNCOMMITTED | possible | possible | possible |
| READ COMMITTED | no | possible | possible |
| REPEATABLE READ | no | no | possible |
| SERIALIZABLE | no | no | no |

SERIALIZABLE is the only level that guarantees the serializability of Module 59.

**PostgreSQL note:** the table above reflects the SQL standard. PostgreSQL's
REPEATABLE READ is implemented via MVCC snapshots and, in practice, also prevents
phantoms (it is stronger than the standard requires). So on PostgreSQL, the table
would show REPEATABLE READ → no phantom. When you read PostgreSQL documentation
it uses the term *snapshot isolation* for this. This is a case where an
implementation exceeds the standard's minimum guarantee.

**Exercise.**
You run `SELECT COUNT(*) FROM Enrol WHERE cid='CS305'` twice in one transaction
and get 30 then 31 because another transaction inserted an enrolment between
them. Which anomaly is this, and what is the weakest isolation level that
prevents it?

**Model answer.**
A **phantom read** (a new row appeared in the same range query). The weakest
standard level that prevents phantoms is **SERIALIZABLE**. (REPEATABLE READ stops
non-repeatable reads of existing rows but, by the standard, still permits
phantoms.)

**Feedback notes.**
Distinguish phantom (new/removed rows in a range) from non-repeatable read
(changed value of an existing row). The table's diagonal structure is the thing
to remember.

## Module 61. Locking and multiversion concurrency control (MVCC)

**Goal:** the two main mechanisms, and why MVCC matters for Postgres.

**Teaching content.**
How do systems actually achieve isolation? Two broad strategies.

- **Locking (pessimistic).** Transactions acquire shared (read) and exclusive
  (write) locks. **Two-phase locking (2PL)**, acquire all locks before releasing
  any, guarantees conflict-serializable schedules. Cost: readers and writers
  block each other, and deadlocks can occur.

- **MVCC (multiversion concurrency control).** Each write creates a new *version*
  of a row rather than overwriting it; each transaction reads the version
  consistent with its start-time snapshot. The decisive consequence: **readers
  never block writers and writers never block readers.** A reader sees a stable
  snapshot without taking any locks. (MVCC is a *versioning strategy*, distinct
  from optimistic/pessimistic conflict detection; PostgreSQL adds Serializable
  Snapshot Isolation on top for full serializability.)

PostgreSQL uses MVCC at its core. This is a large part of why it scales well for
mixed read/write workloads and why "snapshot" isolation feels natural there. The
trade-off MVCC pays is housekeeping: old row versions must eventually be cleaned
up (in Postgres, the VACUUM process).

**Exercise.**
Under MVCC, a long analytical query reads a table while many short transactions
update it. Why does the analytical query neither block the updaters nor see their
mid-flight changes? What new cost has MVCC introduced?

**Model answer.**
The analytical query reads from a consistent **snapshot** taken at its start, so
it sees the row versions valid as of then and is unaffected by later updates;
because it reads versions rather than locking rows, it does not block the
writers, and they create new versions rather than waiting on it. The new cost is
storage and maintenance of multiple row versions, so obsolete versions must be
garbage-collected (VACUUM in Postgres).

**Feedback notes.**
The "readers don't block writers, writers don't block readers" slogan is the
headline. Connect VACUUM to it so the trade-off is honest, not magic.

## Module 62. Indexes and physical storage

**Goal:** the access structures behind physical data independence.

**Teaching content.**
Recall physical data independence (Module 2): the logical model says nothing
about storage, freeing the system to add access structures. The chief one is the
**index**, an auxiliary structure mapping attribute values to the rows holding
them, so a lookup avoids scanning the whole relation.

- **B+-tree index:** the default. A balanced tree giving O(log n) lookup and
  efficient *range* scans (everything between two values). Good for equality and
  ordered ranges.
- **Hash index:** O(1) average equality lookup, but no range support.
- Specialized kinds (Postgres has GiST, GIN, BRIN, and others) serve text,
  geometry, arrays, and very large append-only tables.

Indexes are pure performance: they never change query *answers*, only their
cost. They are the textbook illustration of physical independence, and the main
lever the optimizer pulls in the next modules. The trade-off: each index speeds
reads but slows writes (it must be maintained) and uses space.

**Exercise.**
For `WHERE sid = 101` versus `WHERE year BETWEEN 2 AND 4`, which index type suits
each, and why? What does building these indexes change about the *results*?

**Model answer.**
`sid = 101` is an equality lookup, well served by either a hash index or a
B+-tree. `year BETWEEN 2 AND 4` is a range scan, which needs the ordering of a
B+-tree (a hash index cannot do ranges). Neither index changes the results at
all; they only change how fast the rows are found, which is exactly physical data
independence in action.

**Feedback notes.**
Reinforce "indexes change cost, not answers." The hash-can't-do-ranges point is
the discriminating fact between the two index types.

## Module 63. The optimizer as a search problem

**Goal:** connect query optimization to the student's AI/planning background.

**Teaching content.**
SQL is declarative: you state *what*, not *how*. The **query optimizer** bridges
the gap. It takes the logical algebra expression and searches a space of
*equivalent* expressions and physical implementations for a cheap one.

This is, almost literally, an AI search/planning problem, which should resonate:
- **State space:** the set of equivalent query plans (different join orders,
  different access methods, σ/π pushed to different places).
- **Operators:** algebraic equivalence rules (Module 43) plus physical choices
  (use index vs scan; hash-join vs sort-merge-join vs nested-loop-join).
- **Cost function:** an estimate of work (I/O and CPU), computed from
  **statistics** about the data (table sizes, value distributions, histograms).
- **Search:** because the space is huge (join orderings alone are
  factorial), the optimizer uses pruning and heuristics; the classic
  System R optimizer used dynamic programming over join orders.

So a query optimizer is a planner that searches a space of equivalent plans under
a cost model. The relational model's algebraic equivalences are what make the
search space well-defined.

**Exercise.**
Map each optimizer component to its AI-planning analogue: (a) equivalent plans;
(b) algebraic rewrite rules; (c) the cost estimate; (d) why exhaustive search is
infeasible.

**Model answer.**
(a) the state space / set of reachable states; (b) the operators/actions that
transition between states; (c) the heuristic/objective cost guiding the search;
(d) because the space (especially join orderings) grows factorially, so
the planner must prune and use heuristics or dynamic programming rather than
enumerate every plan.

**Feedback notes.**
This module is designed to click for this student. Encourage them to push the
analogy: an optimizer is to SQL what a planner is to a goal specification.

## Module 64. Cost estimation and join ordering

**Goal:** make the cost model and join-order problem concrete.

**Teaching content.**
The optimizer's choices hinge on estimating how many rows each operation
produces (its **selectivity**) and how expensive each physical operator is.

- **Selectivity:** the fraction of rows a predicate keeps. `sid = 101` is highly
  selective (one row); `year > 0` is not (keeps everything). Estimated from
  statistics and histograms gathered about the columns.
- **Join algorithms** have very different costs: **nested-loop join** (good when
  one side is tiny or indexed), **hash join** (good for large equi-joins),
  **sort-merge join** (good when inputs are already sorted or sorted output is
  wanted).
- **Join order** dominates cost for multi-way joins, because intermediate result
  sizes multiply. Joining the two most selective relations first keeps
  intermediates small; a bad order can build a huge intermediate only to discard
  most of it.

Wrong statistics lead to wrong estimates lead to bad plans; this is why keeping
statistics current (Postgres: ANALYZE) is a real operational concern.

**Exercise.**
You must join Student, Enrol, and a highly selective `σ_(cid='CS305')(Enrol)`.
Intuitively, should the optimizer apply the cid='CS305' selection before or after
the joins, and why, in terms of intermediate result size?

**Model answer.**
Before (push the selection down, Module 17/43). Filtering Enrol to just CS305
rows first makes the relation feeding the join tiny, so the join's intermediate
result is small. Joining first and filtering later would build a large
Student-Enrol intermediate only to throw most of it away. Smaller intermediates
mean less I/O and CPU, hence a cheaper plan.

**Feedback notes.**
This ties the optimization theory back to the "push selections down" algebra law,
closing the loop between Parts III, V, and VIII. Praise reasoning expressed in
terms of intermediate sizes.

# PART IX. SYNTHESIS: THE LINEAGE AND THE TURING AWARDS

## Module 65. The lineage: System R, INGRES, Postgres

**Goal:** the historical through-line from theory to systems.

**Teaching content.**
A compressed history connects every part of this course:

- **1970:** Codd publishes the relational model (theory) at IBM.
- **mid-1970s:** Two landmark prototypes turn theory into working systems.
  **System R** at IBM (which produced SEQUEL, later SQL, and the dynamic-
  programming optimizer of Module 63). **INGRES** at UC Berkeley, led by Michael
  Stonebraker and Eugene Wong (which used the QUEL language and pioneered much
  systems engineering).
- **1980s:** Relational systems become commercial (System R's ideas flow into
  IBM DB2 and, separately, Oracle; INGRES becomes a commercial product).
- **1986 onward:** Stonebraker starts **POSTGRES** at Berkeley to add an
  extensible type system and richer data modelling (the "object-relational"
  ideas). SQL is added in the mid-1990s, and the open-source project is renamed
  **PostgreSQL** in 1996.

So PostgreSQL is the direct descendant of Stonebraker's Berkeley line, carrying
Codd's relational foundations plus decades of systems research.

**Exercise.**
Place these in order and say what each contributed: Codd's model, System R,
INGRES, POSTGRES/PostgreSQL.

**Model answer.**
(1) Codd's relational model (1970): the mathematical foundation. (2) System R
(IBM, mid-1970s): first major implementation, gave us SQL and cost-based
optimization. (3) INGRES (Berkeley, mid-1970s, Stonebraker/Wong): parallel
landmark system, deep systems engineering, QUEL. (4) POSTGRES → PostgreSQL
(Berkeley, 1986 →, Stonebraker): object-relational extensions, the lineage that
became today's PostgreSQL.

**Feedback notes.**
The key relationship to lock in: theory (Codd) → two seminal systems (System R,
INGRES) → PostgreSQL descends from the Berkeley/INGRES/POSTGRES branch.

## Module 66. Why PostgreSQL is considered well-designed

**Goal:** answer the student's stated question about Postgres specifically.

**Teaching content.**
PostgreSQL's reputation rests on choices that align unusually well with the
theory in this course:

- **Standards-faithful and correct.** It implements SQL closely and handles the
  hard correctness corners (three-valued logic and NULLs, Module 32/33;
  serializable isolation via Serializable Snapshot Isolation) more rigorously
  than many peers.
- **MVCC done thoroughly** (Module 61): readers and writers do not block,
  enabling mixed workloads.
- **Extensible type system.** Stonebraker's object-relational vision: users can
  add data types, operators, index methods (GiST/GIN, Module 62), and functions.
  This is why Postgres absorbed JSON, full-text search, geometric/GIS data
  (PostGIS), and more without architectural upheaval.
- **A genuine, well-architected optimizer** (Module 63/64) with real statistics
  and multiple join methods.
- **Open development and durability/reliability culture** (write-ahead logging,
  crash safety).

The throughline: Postgres treats the relational model's principles (correctness,
data independence, extensibility) as first-class, rather than bolting features on
ad hoc.

**Exercise.**
Pick the two PostgreSQL design choices you find most compelling and tie each back
to a specific earlier module's concept.

**Model answer.**
Open-ended. Strong pairings: (i) MVCC ↔ Module 61's concurrency mechanisms;
(ii) extensible types/index methods ↔ Module 62 indexes and the object-relational
idea; (iii) rigorous NULL/3VL handling ↔ Modules 32–33; (iv) cost-based optimizer
↔ Modules 63–64. Any two, correctly connected, are full marks.

**Feedback notes.**
Reward connections back to the theory rather than feature-listing. The point is
that Postgres's strengths are the course's principles, realized well.

## Module 67. Codd's Turing Award (1981)

**Goal:** articulate precisely what Codd was honoured for.

**Teaching content.**
Edgar F. Codd received the **1981 ACM A. M. Turing Award**. The recognition, in
essence: for inventing the relational model and founding the theory of relational
databases, giving data management a rigorous mathematical basis.

Concretely, the contributions you can now name from this course:
- the relational model itself (relations as sets of tuples; Part I),
- relational algebra and relational calculus as query languages and the
  completeness result tying them together (Parts III–IV),
- the principle of **data independence** (Module 2),
- normalization theory and the normal forms that bear partly on his name
  (BCNF; Part VII).

Codd turned a craft into a science. Everything earlier in this tutorial is, in
one way or another, downstream of his 1970 paper.

**Exercise.**
In two or three sentences, justify Codd's Turing Award by citing at least three
distinct contributions you studied, not just "he invented relational databases."

**Model answer.**
Should cite at least three of: the relational model (data as mathematical
relations); data independence (decoupling logical from physical); relational
algebra/calculus and their equivalence (a formal, complete query foundation);
and normalization theory (FDs and normal forms for principled design). The award
recognizes that he gave data management a mathematical foundation, not a single
product.

**Feedback notes.**
Push for *specific* contributions tied to modules. A vague "he made databases"
answer should be sent back for the concrete list.

## Module 68. Stonebraker's Turing Award (2014)

**Goal:** articulate the distinct, systems-oriented nature of his award.

**Teaching content.**
Michael Stonebraker received the **2014 ACM A. M. Turing Award**. Where Codd was
honoured for *theory*, Stonebraker was honoured for *systems*: for fundamental
contributions to the concepts and practices underlying modern database systems,
demonstrated by building many of them.

His through-line is that database systems are a serious branch of *systems*
research, proven by repeatedly engineering working systems:
- **INGRES** and **POSTGRES** (Berkeley): foundational relational and
  object-relational systems; PostgreSQL descends from this work.
- A series of influential later systems exploring specialized architectures, for
  example **C-Store** (column-store ideas, commercialized as Vertica),
  **H-Store/VoltDB** (in-memory OLTP), and **SciDB** (science/array data).
- The broader thesis: "**one size does not fit all**", different workloads
  warrant different engine architectures, rather than one monolithic design.

So the two awards are complementary: Codd gave the field its mathematics;
Stonebraker showed, across decades and many systems, that the field is also a
rich and legitimate systems-engineering discipline.

**Exercise.**
Contrast the *character* of Codd's and Stonebraker's awards. What does pairing
them tell you about what it takes for a research field to mature?

**Model answer.**
Codd's award is for *theoretical foundation* (the relational model and its
formal apparatus); Stonebraker's is for *systems practice* (designing and
building many influential database systems and advancing the engineering of how
they work). Pairing them shows a mature field needs both: rigorous theory to
define what is correct and possible, and sustained systems engineering to make
it real, performant, and adaptable to varied workloads.

**Feedback notes.**
The "theory vs systems, both needed" framing is the goal. Note PostgreSQL as the
concrete artifact linking Stonebraker's systems work to the student's stated end
goal of learning Postgres.

## Module 69. Capstone and next steps

**Goal:** synthesize the whole course and hand off to real Postgres.

**Teaching content.**
You now hold the conceptual framework the course set out to build. A one-paragraph
self-test of the arc: data is modelled as **relations** (sets of tuples) for
**data independence**; we query them with **relational algebra** (procedural) and
the equivalent **relational calculus** (declarative), a duality **Codd's theorem**
formalizes; **SQL** is the practical language sitting on that foundation, with
**bag semantics** and **three-valued logic** as its main deviations from the pure
model; good schemas come from **ER modelling** (which is *not* OO design) and
**normalization** (FDs, 3NF/BCNF, lossless and dependency-preserving
decomposition); and real systems add **transactions/ACID**, **serializability**,
**concurrency control (MVCC)**, **indexes**, and a **cost-based optimizer** that
is essentially an AI planner over equivalent plans. **PostgreSQL** is the system
that realizes these principles most faithfully, which is why it is held up as
well-designed, and the lineage runs Codd → System R / INGRES → POSTGRES →
PostgreSQL, honoured by the **1981** and **2014** Turing Awards.

Where to go next, now that the framework is in place:
- Install PostgreSQL and re-do this course's queries for real (`psql`).
- Read the PostgreSQL documentation's tutorial and the chapters on MVCC, indexes,
  and `EXPLAIN` (which shows you the optimizer's chosen plan: Modules 59–60 made
  tangible).
- For depth, a standard text (Silberschatz/Korth/Sudarshan, or
  Ramakrishnan/Gehrke, or Garcia-Molina/Ullman/Widom). For Codd's own voice, his
  1970 CACM paper is short and readable now that you have the vocabulary.

**Exercise (capstone).**
Without looking back, write the one-paragraph arc of the course in your own words,
from "data as relations" to "PostgreSQL and the two Turing Awards." Then name the
one module you would most like to revisit, and why.

**Model answer.**
No single correct text; a strong answer traces relations → data independence →
algebra/calculus + Codd's theorem → SQL (with bag semantics and 3VL caveats) →
ER and normalization → transactions/concurrency/indexes/optimization →
PostgreSQL and the lineage/awards, in roughly that causal order, and connects at
least a few links explicitly. The revisit choice is personal; use it to plan any
review.

**Feedback notes.**
This is graduation. Be generous and specific in praise; identify which links in
their arc are strongest and which to shore up. Offer to loop back to any module
they named. Mark the course complete in `progress.md`.

---

## APPENDIX A. Notation quick-reference (for the tutor)

```
σ_p(R)     selection: rows of R satisfying predicate p
π_L(R)     projection: columns L of R (set semantics: dedupes)
R ∪ S      union            R ∩ S   intersection (derived)   R − S   difference
R × S      Cartesian product
ρ_S(R)     rename R to S (optionally its attributes)
R ⋈ S      natural join (equate shared-named attributes, merge them)
R ⋈_θ S    theta-join = σ_θ(R × S)
R ⟕ S      left outer join    R ⟖ S right    R ⟗ S full
R ⋉ S      semijoin           R ▷ S antijoin
R ÷ S      division ("for all")
γ_(G; f)(R) grouping/aggregation
```

SQL ↔ algebra cheat sheet:
```
SELECT (DISTINCT) cols   ~ π          WHERE p              ~ σ_p
FROM A, B / A JOIN B ON  ~ × then σ   GROUP BY ... HAVING  ~ γ then σ on groups
UNION / INTERSECT / EXCEPT ~ ∪ / ∩ / −  (set semantics by default; ALL = bag)
EXISTS / IN              ~ ∃ / semijoin   NOT EXISTS       ~ antijoin / ∀ via ¬∃¬
LEFT JOIN ... IS NULL    ~ antijoin
```

## APPENDIX B. The running schema (for the tutor)

```
Student(   [sid], sname, major, year )
Course(    [cid], title, dept, credits )
Instructor([iid], iname, dept, salary )
Section(   [cid, term, sec_no], iid, room )   -- weak entity; FKs cid, iid
Enrol(     [sid, cid, term, sec_no], grade )  -- M:N student/section; FKs to both
```
FDs: sid→sname,major,year ; cid→title,dept,credits ; iid→iname,dept,salary ;
(cid,term,sec_no)→iid,room ; (sid,cid,term,sec_no)→grade.
