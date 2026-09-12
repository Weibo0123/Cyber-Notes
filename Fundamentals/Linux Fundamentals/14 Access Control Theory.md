# Access Control Theory

This file covers the *conceptual model* behind access control in general. File 13 (Permissions) covers how Linux actually implements a simplified version of it.

## Core Definitions

**Access Control** = who can access what, and what they're allowed to do:
```
Subject → Right → Object       e.g.  Process P → read → File F
```

| Concept | Question it answers |
|---|---|
| **Authentication** | Who are you? |
| **Authorization** | What are you allowed to do? |
| **Policy** | What *should* be allowed? (a rule) |
| **Mechanism** | How is that rule actually enforced? (permission bits, ACLs, capabilities...) |
| **Trust** | Who do we trust to do what? |
| **Risk** | Likelihood × impact of something going wrong — access control reduces, never eliminates it |

```
Login → Authentication ("you are user P") → Authorization ("you may read File F")
```
**Logging/auditing** — recording actions (who changed what, when) so they can later be traced or investigated.

## Subjects, Objects, Rights

An access control model defines three sets: Subjects (S), Objects (O), Rights (R).

- **Subject** — an entity that acts. In Unix, this is a **process** (a user initiates an action, but the OS acts on behalf of a process).
- **Object** — a resource acted upon. In simplified Unix terms, **files**.
- **Right** — an operation a Subject may perform on an Object. A commonly-taught theoretical set: `{r, w, x, a, o}` (read, write, execute, append, own).

⚠️ Note: this `{r,w,x,a,o}` set is the **abstract teaching model**, not literally what `chmod` gives you — real POSIX permission bits are only `r`, `w`, `x` (file 13). The theoretical model separates `write` (modify existing content) from `append` (add to the end only, can't modify existing content) and adds `own` (ability to change who has rights over the object) as distinct concepts worth understanding even though Unix doesn't expose them as separate chmod bits.

## The Access Control Matrix

A grid mapping every (Subject, Object) pair to its set of rights:
```
                Objects
              F        G
           ┌────────┬────────┐
P          │ r,w,o  │ r      │
           ├────────┼────────┤
Q          │ a      │ r,o    │
           └────────┴────────┘
Subjects
```
Formally, `M[S,O]` = the rights Subject `S` has over Object `O`. E.g. `M[P,F] = {r,w,o}` → P can read, write, and own F.

**It's a snapshot, not a guarantee about the future.** The matrix reflects the *current* state; an action (e.g. someone with `own` granting a new right) can transition the system to a new state:
```
State₁ --(action)--> State₂
```
So "Q doesn't have read(G) right now" does not mean "Q can never get it" — if some Subject can modify the matrix, the state can change.

**Strength:** conceptually simple, easy to query ("does Q have read on G?" → look up row Q, column G).
**Weakness:** a real system can have thousands of Subjects and objects — a literal matrix would be enormous. It's mainly a *conceptual* model; real systems implement it differently (below).

## Implementing the Matrix: ACL vs. Capability List

| | Access Control List (ACL) | Capability List |
|---|---|---|
| Stored per | **Object** (one column of the matrix) | **Subject** (one row of the matrix) |
| Answers | "Who can access *this* Object?" | "What can *this* Subject access?" |
| Object-focused review/revocation | Easier | Harder |
| Subject-focused review/revocation | Harder | Easier |

```
ACL(F):  P → {r,w,o}     Capabilities(P):  F → {r,w,o}
         Q → {a}                            G → {r}
```
A **tuple representation** is a third option — each authorization as a standalone `(Subject, Right, Object)` triple, e.g. `(P, read, F)`.

**Capability unforgeability:** a Subject must not be able to fabricate a capability for a right it was never granted — capability systems rely on protected references or cryptography to guarantee this.

## Principle of Least Privilege

> A Subject should have only the privileges strictly necessary for its task — nothing more.

If a process only needs `read(A)`, it should not additionally hold `write(A)`, `read(B)`, `execute(C)`, etc. This limits the blast radius if that Subject is ever compromised or misused — a recurring theme once you get to SUID binaries (file 13) and privilege escalation generally.

## Cheat Sheet

```
Authentication → who are you?          Authorization → what can you do?
Subject = process    Object = file/resource    Right = allowed operation

Access Control Matrix:  M[Subject, Object] = Rights
  → conceptual model; a snapshot that can transition between states

ACL          → per-Object (column)   → "who can access this?"
Capability    → per-Subject (row)     → "what can this access?"
Tuple          → (Subject, Right, Object)

Least Privilege → grant only what's required, nothing more
```
