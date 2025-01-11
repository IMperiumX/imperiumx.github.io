---
layout: post
title: "SICP Chapter 3: Modularity, Objects and State"
date: 2025-01-11 20:51 +0200
tags: [Python, Scheme, OOP, SICP]
---

# Chapter 3: Modularity, Objects, and State

Disclaimer: This chapter draws inspiration and concepts from two influential texts: Structure and Interpretation of Computer Programs ([SICP](https://mitp-content-server.mit.edu/books/content/sectbyfn/books_pres_0/6515/sicp.zip/index.html)), which primarily uses the Scheme programming language, and [Composing Programs](https://www.composingprograms.com/pages/24-mutable-data.html), which uses Python. We will implement a basic object-oriented system in Python, while also exploring related concepts from SICP, such as mutable state, the environment model, concurrency, and streams. This comparative approach aims to provide a deeper understanding of object-oriented programming and state management by examining different perspectives.

In previous chapters, we explored the power of functions and fundamental data structures like lists and dictionaries in building programs and managing complexity through abstraction. This chapter delves into another powerful programming paradigm: **object-oriented programming (OOP)**. We will implement a basic OOP system using Python, demonstrating that the core concepts of OOP are fundamentally conceptual and can be built using functions and dictionaries.

Furthermore, Scheme as presented in Chapter 3 of the renowned book *Structure and Interpretation of Computer Programs* (SICP). SICP introduces concepts like mutable state, the environment model, concurrency, and streams. Examining these ideas in Scheme will provide valuable contrast and highlight different approaches to managing state in programming, ultimately enriching our understanding of our Python OOP implementation.

## 3.1 Objects as Dictionaries in Python

At its core, an object encapsulates data (attributes) and associated behaviors (methods). In our Python implementation, we will represent objects as **dispatch dictionaries**. These dictionaries will respond to specific messages, such as "get" and "set," to provide controlled access and modification of their internal attributes.

Consider a simple example: a bank account. A bank account might possess attributes like "balance" and "holder" and methods like "deposit" and "withdraw." Using a dictionary, we can represent this as follows:

```python
def make_account(holder):
  """Creates a bank account object."""
  balance = 0

  def get(message):
    if message == 'balance':
      return balance
    elif message == 'holder':
      return holder
    else:
      return "Unknown message"

  def set(message, value):
    nonlocal balance
    if message == 'balance':
      balance = value
    else:
      return "Unknown message"

  def deposit(amount):
    nonlocal balance
    balance += amount
    return balance

  def withdraw(amount):
    nonlocal balance
    if amount > balance:
      return 'Insufficient funds'
    balance -= amount
    return balance

  dispatch = {'get': get, 'set': set, 'deposit': deposit, 'withdraw': withdraw}
  return dispatch

account = make_account('Alice')
print(account['get']('balance'))  # Output: 0
account['deposit'](100)
print(account['get']('balance'))  # Output: 100
```

Here, `make_account` is a function that creates a new bank account object. The `dispatch` dictionary acts as our object, encapsulating the methods (deposit and withdraw) and managing the "get" and "set" messages to provide controlled access to the `balance` and `holder` attributes. This demonstrates the principle of **encapsulation**, where data is protected and accessed only through defined methods.

## 3.1.1. A Look at State in Scheme: Assignment and Local State

SICP's Chapter 3 introduces a different perspective on managing state. In Scheme, the `set!` special form is used to introduce mutability, allowing the modification of variables. This contrasts with our Python example, where we used `nonlocal` to modify the `balance` within the closure.

In Scheme, a bank account might be implemented like this:

```scheme
(define (make-account balance)
  (define (withdraw amount)
    (if (>= balance amount)
        (begin (set! balance (- balance amount))
               balance)
        "Insufficient funds"))
  (define (deposit amount)
    (set! balance (+ balance amount))
    balance)
  (define (dispatch m)
    (cond ((eq? m 'withdraw) withdraw)
          ((eq? m 'deposit) deposit)
          (else (error "Unknown request -- MAKE-ACCOUNT" m))))
  dispatch)

(define acc (make-account 100))
((acc 'withdraw) 50) ; => 50
((acc 'deposit) 25)  ; => 75
```

Here, `balance` is a local state variable modified using `set!`. Each call to `make-account` creates a separate account with its own independent state, similar to how our Python `make_account` function works.

**Key Difference:** Scheme uses an explicit `set!` for mutation, while our Python implementation relies on closures and `nonlocal` to achieve a similar effect.

## 3.2 Classes: Blueprints for Objects in Python

In OOP, a class serves as a blueprint for creating objects. It defines the common attributes and methods that all instances (objects) of that class will share. We can represent classes, too, using dictionaries in Python.

A class dictionary will store:

* **Attributes:** Values common to all instances.
* **Methods:** Functions that operate on instances.
* **A "new" method:** A special method responsible for creating new instances of the class.

Here's how we can create a `make_class` function in Python:

```python
def make_class(attributes, base_class=None):
  """Creates a new class (a dispatch dictionary)."""
  def get(name):
    if name in attributes:
      return attributes[name]
    elif base_class is not None:
      return base_class['get'](name)

  def set(name, value):
    attributes[name] = value

  def new(*args):
    return init_instance(cls, *args)

  cls = {'get': get, 'set': set, 'new': new}
  return cls
```

The `make_class` function creates a class dictionary that responds to "get," "set," and "new" messages. The "get" method first checks if the requested attribute exists in the class's own attributes. If not, and if a base class is provided, it delegates the lookup to the base class, implementing the fundamental concept of **inheritance**. The `new` method is responsible for creating new instances, which we'll explore next.

## 3.3 Instances: Individual Objects in Python

An instance represents a specific object created from a class. We can create instances using a `make_instance` function in Python:

```python
def make_instance(cls):
  """Creates a new instance of the given class."""
  attributes = {}

  def get(name):
    if name in attributes:
      return attributes[name]
    else:
      value = cls['get'](name)
      if callable(value):  # Check if the value is a method
        return bind_method(value, instance)
      else:
        return value

  def set(name, value):
    attributes[name] = value

  instance = {'get': get, 'set': set}
  return instance
```

`make_instance` generates an instance dictionary that can access and modify its own attributes. When an attribute is not found locally, it queries its class for the value using the class's "get" method, potentially traversing the inheritance hierarchy. We directly check if the value is callable to determine if it is a method.

## 3.4 Binding Methods to Instances in Python

Methods are functions defined within the class, but they need to operate on specific instances. This is achieved through **method binding** in Python.

```python
def bind_method(method, instance):
  """Binds a method to an instance."""
  def bound_method(*args):
    return method(instance, *args)
  return bound_method
```

`bind_method` takes a method (a function) and an instance. It returns a new function, `bound_method`, which, when invoked, will call the original method with the instance prepended as the first argument (conventionally named `self`). This ensures that methods have access to the instance's data, enabling them to modify the object's state.

## 3.5 Initialization: Setting Up Instances in Python

When a new instance is created, we often need to initialize its attributes. We can accomplish this using a special method called `__init__`, which acts as a constructor in Python.

```python
def init_instance(cls, *args):
  """Creates a new instance and calls its __init__ method."""
  instance = make_instance(cls)
  init = cls['get']('__init__')
  if init:
    init(instance, *args)
  return instance
```

`init_instance` creates a new instance using `make_instance` and then invokes the `**init** method (if it exists) to initialize the instance with the provided arguments.

## 3.6 Inheritance: Building on Existing Classes in Python

Inheritance enables us to create new classes (subclasses) that inherit attributes and methods from existing classes (base classes). This promotes code reuse and allows us to build specialized classes from more general ones, modeling "is-a" relationships.

We've already implemented the core of inheritance in `make_class`. When creating a subclass, we pass the base class to `make_class`. The subclass's "get" method will first search its own attributes, and if it doesn't find the requested attribute, it will delegate the lookup to the base class, effectively traversing the inheritance chain.

## 3.7 Example: Bank Accounts Revisited in Python

Let's revisit our bank account example and implement it using our newly constructed object system in Python:

```python
# Account class
Account = make_class({
    '__init__': lambda self, account_holder: self['set']('holder', account_holder),
    'deposit': lambda self, amount: (
        self['set']('balance', self['get']('balance') + amount),
        self['get']('balance')
    ),
    'withdraw': lambda self, amount: (
        self['get']('balance') - amount
        if amount <= self['get']('balance')
        else 'Insufficient funds'
    ),
    'balance': 0
})

# CheckingAccount subclass
CheckingAccount = make_class({
    'withdraw_fee': 1,
    'withdraw': lambda self, amount: (
        Account['get']('withdraw')(self, amount + self['get']('withdraw_fee'))
    )
}, Account)

# Usage
acct = Account['new']('Jim')
acct['get']('deposit')(acct, 10)  # Correctly pass acct as the first argument
print(acct['get']('balance'))     # Output: 10
acct['get']('withdraw')(acct, 5)   # Correctly pass acct as the first argument
print(acct['get']('balance'))     # Output: 5

check_acct = CheckingAccount['new']('Jack')
check_acct['get']('deposit')(check_acct, 20)  # Correctly pass check_acct
print(check_acct['get']('balance'))           # Output: 20
check_acct['get']('withdraw')(check_acct, 5)  # Correctly pass check_acct
print(check_acct['get']('balance'))           # Output: 14
```

Here, `Account` is our base class, and `CheckingAccount` is a subclass that inherits from `Account`. `CheckingAccount` overrides the `withdraw` method to include a withdrawal fee, demonstrating **polymorphism**. Notice how `CheckingAccount`'s `withdraw` method utilizes `Account['get']('withdraw')` to access and call the base class's `withdraw` method, showcasing code reuse through inheritance. Also note that in usage, we need to explicitly pass the instance as the first argument to the methods since we are accessing them through the dispatch dictionary.

## 3.8 The Environment Model vs. Our Dictionary-Based Objects

SICP introduces the **environment model** to explain how Scheme manages variables and their values. The environment model uses a sequence of frames, each containing bindings of names to values. This model is crucial for understanding how `set!` works in Scheme and how procedures maintain their lexical scope.

While our Python implementation doesn't explicitly use an environment model like Scheme's, there are parallels. Each instance in our system has its own `attributes` dictionary, which acts like a frame, storing the instance's local state. When a method is called, the `bind_method` function creates a closure that captures the instance, similar to how procedures in Scheme carry a pointer to their defining environment.

**Key Difference:** Scheme's environment model is a more general mechanism for managing variables and their scope, while our Python system is specifically tailored for implementing OOP with dictionaries.

## 3.9 Concurrency and Streams: Lessons from SICP

SICP delves into the complexities of **concurrency**, where multiple processes access and modify shared state. This can lead to unpredictable results if not handled carefully. While our simple Python OOP system doesn't directly address concurrency, the principles discussed in SICP are relevant to any system dealing with shared mutable state.

SICP also introduces **streams**, a powerful data structure for representing potentially infinite sequences using delayed evaluation. Streams provide a way to work with infinite data in a finite way and offer benefits in terms of modularity and efficiency. While not directly implemented in our Python OOP system, the concept of delayed evaluation and its benefits are important to keep in mind when designing data structures and algorithms.

## 3.10 Limitations and Further Exploration

Our simplified object system demonstrates the core principles of OOP, but it's far from complete. It lacks features found in more sophisticated object systems like Python's, such as:

* **Meta-classes:** Classes that create classes.
* **Static methods:** Methods associated with the class itself rather than instances.
* **Multiple inheritance:** The ability to inherit from multiple base classes.
* **Introspection:** The ability to examine an object's type and attributes at runtime.

These advanced features, while beyond the scope of our current discussion, are worthy of further exploration. However, by building this basic implementation, we've gained a fundamental understanding of how objects and classes operate under the hood. You are encouraged to expand this system to incorporate these missing features as an exercise, further solidifying your understanding of OOP concepts.

The concepts from SICP, such as the environment model, concurrency management techniques, and the stream data structure, provide valuable insights into broader programming paradigms and can inspire more sophisticated implementations even within our dictionary-based OOP framework.

## 3.11 Conclusion

In this chapter, we implemented a basic object-oriented system using only functions and dictionaries in Python. This exercise has illuminated the fact that OOP, at its core, is a powerful paradigm built upon fundamental programming concepts. Scheme as presented in SICP, used to explore different approaches to managing state and to learn about concepts like the environment model, concurrency, and streams.

By understanding how objects, classes, inheritance, and method binding can be implemented from scratch and by contrasting our implementation with Scheme's approach, we gain a deeper appreciation for the elegance and power of object-oriented programming and a broader perspective on managing state in software systems. We are now better equipped to utilize OOP effectively in languages that support it natively and to appreciate the core principles that underpin this widely adopted programming style. This foundational knowledge, enriched by our exploration of SICP's concepts, will be invaluable as we continue to explore more complex programming concepts and build increasingly sophisticated software systems.
