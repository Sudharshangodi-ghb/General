## What is a Circular Linked List?
A circular linked list is a linked list in which the last node’s next pointer points back to the head (or any node within the list), forming a loop.

### Types
Singly Circular Linked List (SCLL): Each node has data and next. The last node’s next points to the head.
<img width="707" height="135" alt="image" src="https://github.com/user-attachments/assets/54a19fcf-deb2-4d14-ba6c-10dc1887514b" />

Doubly Circular Linked List (DCLL): Each node has prev and next; prev of head points to tail and next of tail points to head.
<img width="707" height="135" alt="image" src="https://github.com/user-attachments/assets/dca157db-dc2d-4454-85ae-0fe1e8bf4a18" />

---------------------------------------------------------------------------------------
## How do you detect a loop in a circular or singly linked list (Floyd's algorithm)?
Use two pointers:
slow moves 1 step at a time.
fast moves 2 steps at a time.

If the list has a cycle, the two pointers will eventually meet inside the loop.
If fast reaches NULL, the list is acyclic (no loop).
### Why It Works
In a loop, fast gains on slow by 1 node per iteration.
Eventually, fast will catch up to slow inside the cycle.

### Snippet 

#include <stdbool.h>

typedef struct Node {
    int data;
    struct Node *next;
} Node;

bool has_loop(Node *head) {
    Node *slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;           // move 1 step
        fast = fast->next->next;     // move 2 steps
        if (slow == fast) return true; // loop detected
    }
    return false; // no loop
}

--------------------------------------------------
## How do you remove a loop from a linked list?
### Snippet


#include <stdbool.h>

static Node* floyd_meet(Node *head) {
    Node *slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;           // 1 step
        fast = fast->next->next;     // 2 steps
        if (slow == fast) return slow; // meeting point inside loop
    }
    return NULL; // no loop
}

void remove_loop(Node *head) {
    if (!head) return;
    
    Node *meet = floyd_meet(head);
    if (!meet) return; // no loop

    // Find start of loop
    Node *slow = head;
    Node *fast = meet;
    while (slow != fast) {
        slow = slow->next;
        fast = fast->next;
    }
    // Now 'slow' (== fast) is the start of the loop

    // Find the tail of the cycle (node whose next points to start)
    Node *tail = slow;
    while (tail->next != slow) {
        tail = tail->next;
    }

    // Break the loop
    tail->next = NULL;
}

----------------------------------------------------------------------
## How do you convert a circular linked list into a singly linked list?
Start from head.
Traverse until you find the node whose next points back to head (the tail of the circular list).
Set that node’s next = NULL.

head → A → B → C
head → A → B → C → NULL




