---
layout: post
title: 'SICP Chapter 1: Building Abstractions with Procedures'
date: 2024-12-30 14:57 +0200
tags: [SICP, programming, scheme]
comments: true
categories: [books, programming]
---

This chapter introduces the fundamental building blocks of programming in Scheme and lays the groundwork for the core principles of **abstraction**, **procedural decomposition**, and **computational processes** that are explored throughout the rest of the book.

## Core Themes

* **Combining:** SICP emphasizes building complex procedures from simpler ones using **procedure composition** (a hallmark of functional programming). Think about how procedures are combined in higher-order functions like `map`, `filter`, and `accumulate` presented in the book.
* **Relating:** SICP introduces concepts like **pairs** and **lists**, which are fundamental data structures built on the idea of relating data elements. The ability to represent relationships between data elements will continue to show up when discussing more complex data structures.
* **Abstracting:** Abstraction is the heart of SICP. The book consistently builds layers of abstraction, from basic procedures to data abstractions like rational numbers or even to the construction of an interpreter that can evaluate Scheme code.

## Key Concepts

### 1. Elements of Programming

* **Primitive Expressions:** The simplest entities the language is concerned with. In Scheme, these include:
  * Numbers
  * Basic arithmetic operators (`+`, `-`, `*`, `/`)
  * Built-in procedures like `sqrt`
* **Means of Combination:** Ways of building compound expressions from simpler ones. In Scheme, the primary means of combination is the *combination*, which is a list of expressions enclosed in parentheses. The first element is the *operator*, and the rest are *operands*. This is expressed using *prefix notation*.
  * **Example:** `(+ 137 349)`
* **Means of Abstraction:** Ways of giving names to computational objects and manipulating them as units.
  * **Example:** `(define size 2)`  This defines the name `size` and associates it with the value 2.

### 2. Procedure Definitions

* **`(define (name parameters) body)`:** The general form of defining a procedure (function) in Scheme. It associates a name with a compound operation, allowing us to abstract a sequence of operations into a single unit.
  * **Example:** `(define (square x) (* x x))` defines a procedure named `square` that takes one parameter `x` and returns its square.

### 3. The Substitution Model for Procedure Application

* This model explains how Scheme evaluates combinations (procedure calls). It's a simplified model to understand the mechanics, though not necessarily how Scheme is actually implemented.
* **To apply a compound procedure to arguments:**
    1. **Evaluate the subexpressions** of the combination.
    2. **Apply** the procedure (the value of the leftmost subexpression, the operator) to the arguments (the values of the other subexpressions, the operands).
    3. **Replace** the procedure application with the body of the procedure, substituting the arguments for the corresponding formal parameters in the body.
    4. **Evaluate** the resulting expression.
* **Applicative Order vs. Normal Order:**
  * **Applicative Order:** Evaluate the arguments first, *then* apply the procedure (Scheme uses this).
  * **Normal Order:** Fully expand the procedure, *then* evaluate the result (delaying argument evaluation until absolutely needed).
* Both orders will produce the same result for procedures that can be modeled with substitution and don't have side effects.

### 4. Conditional Expressions and Predicates

* **`(cond)`:** A powerful construct for handling conditional logic. It consists of a series of clauses, each with a *predicate* (a test) and a *consequent expression*. Scheme evaluates the predicates in order until one is found to be true, then evaluates the corresponding consequent expression.
  * **Example:**

    ```scheme
    (cond ((> x 0) x)
          ((= x 0) 0)
          ((< x 0) (- x)))
    ```

* **`(if)`:** A simpler form for two-way branching. `(if <predicate> <consequent> <alternative>)`
* **Predicates:** Procedures that return true or false, often used in conditional expressions.
  * Examples: `>`, `<`, `=`, `>=`, `<=`, `and`, `or`, `not`

### 5. Black-Box Abstraction

* A key concept in SICP. Once a procedure is defined, it can be used as a "black box." We only need to know *what* it does (its inputs and outputs), not *how* it does it internally. This is crucial for managing complexity.
* This is the essence of procedural abstraction: hiding implementation details within a procedure, allowing the user to treat it as a building block without needing to understand its inner workings.

### 6. Recursion and Iteration

* **Recursive Procedure:** A procedure that calls itself within its definition.
* **Recursive Process:** A process that expands and then contracts, characterized by a chain of deferred operations.
* **Iterative Process:** A process whose state can be summarized by a fixed number of state variables, along with a rule that describes how the state variables are updated. Iterative processes in Scheme are often described with tail-recursive procedures.
* **Tail Recursion:** A special form of recursion where the recursive call is the *very last* operation in the procedure. Tail-recursive procedures can be optimized by Scheme interpreters to execute as iterative processes, using constant space.

### 7. Procedures as Arguments and Higher-Order Procedures

* **Higher-Order Procedures:** Procedures that take other procedures as arguments or return procedures as values. This is a powerful abstraction mechanism that allows us to express general patterns of computation.
  * **Example:** A `sum` procedure that takes as parameters a `term` to apply, a starting point, a function to go to the `next` value, and an end condition. All these parameters can be provided as procedures.

## Further Reading

* [Link to official SICP online text](https://mitpress.mit.edu/sites/default/files/sicp/full-text/book/book.html)
* [Exercise Solution](https://github.com/IMperiumX/playground/tree/main/src/playground/scheme/SICP)
