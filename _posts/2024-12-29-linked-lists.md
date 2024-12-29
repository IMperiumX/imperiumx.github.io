---
layout: post
title: Linked Lists
date: 2024-12-29 14:58 +0200
---


# Linked List

A linked list is a linear data structure where elements are not stored at contiguous memory locations. Instead, each element (called a **node**) points to the next element in the sequence. This structure allows for efficient insertion and deletion of elements compared to arrays, but it makes accessing elements by index slower.

## Components of a Linked List

A linked list consists of the following components:

* **Node:** Each node in a linked list contains two parts:
  * **Data:** The value or data stored in the node.
  * **Next:** A pointer or reference to the next node in the list. The last node in the list has a `next` pointer that points to `None` (or `NULL` in some languages), indicating the end of the list.
* **Head:** A pointer to the first node in the linked list. It serves as the entry point to the list.
* **Tail:** (Optional) A pointer to the last node in the linked list. It can make appending elements more efficient.

## Types of Linked Lists

There are several types of linked lists, including:

* **Singly Linked List:** Each node has a pointer only to the next node.
* **Doubly Linked List:** Each node has pointers to both the next and the previous nodes. This allows for traversal in both directions but requires more memory per node.
* **Circular Linked List:** The last node's `next` pointer points back to the first node (the head), forming a loop.
* **Circular Doubly Linked List:** A combination of doubly and circular linked lists, where the last node's `next` points to the first node, and the first node's `prev` points to the last node.

## Basic Operations and Time Complexity

| Operation        | Singly Linked List | Doubly Linked List |
| ---------------- | :----------------: | :----------------: |
| Access (by index) |        O(n)        |        O(n)        |
| Insertion (head) |        O(1)        |        O(1)        |
| Insertion (tail) |   O(n) or O(1)\*   |        O(1)        |
| Insertion (middle) |  O(n) (search) + O(1)  |  O(n) (search) + O(1)  |
| Deletion (head)  |        O(1)        |        O(1)        |
| Deletion (tail)  |        O(n)        |   O(n) or O(1)\*   |
| Deletion (middle) |  O(n) (search) + O(1)  |  O(n) (search) + O(1)  |
| Search           |        O(n)        |        O(n)        |

\* O(1) if you have a pointer to the tail node.

## Advantages of Linked Lists

* **Dynamic Size:** Linked lists can grow or shrink dynamically as needed.
* **Efficient Insertions and Deletions:** Inserting or deleting elements at the beginning or middle of a linked list is generally faster than with arrays, as it only requires updating a few pointers.
* **No Memory Waste:** Linked lists only use as much memory as they need to store the elements, unlike arrays, which may have pre-allocated memory that is not fully utilized.

## Disadvantages of Linked Lists

* **Slow Access:** Accessing an element at a specific index is slow (O(n)) because you have to traverse the list from the head.
* **Memory Overhead:** Each node in a linked list requires extra memory to store the pointer(s) to the next (and previous, in doubly linked lists) node(s).
* **Not Cache-Friendly:** Linked list nodes may not be stored contiguously in memory, which can lead to poor cache performance compared to arrays.

## When to Use Linked Lists

* When you need frequent insertions and deletions, especially at the beginning or middle of the list.
* When you don't know the number of elements in advance and need a dynamic data structure.
* When you don't need random access to elements.
* When memory usage is a concern, and you want to avoid pre-allocating a large block of memory.

## Example Implementation (Singly Linked List in Python)

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None

    def append(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
            return
        last_node = self.head
        while last_node.next:
            last_node = last_node.next
        last_node.next = new_node

    def print_list(self):
        curr_node = self.head
        while curr_node:
            print(curr_node.data, end=" -> ")
            curr_node = curr_node.next
        print("None")

# Example Usage
my_list = LinkedList()
my_list.append(1)
my_list.append(2)
my_list.append(3)
my_list.print_list()  # Output: 1 -> 2 -> 3 -> None
```

## Related LeetCode Problems

206. Reverse Linked List
21. Merge Two Sorted Lists
141. Linked List Cycle
2. Add Two Numbers
234. Palindrome Linked List
