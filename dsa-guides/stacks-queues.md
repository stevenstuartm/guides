# Stacks & Queues

## Overview
Two fundamental abstract data types that restrict how elements are added and removed, creating specific behavioral patterns useful for many algorithms and system designs.

---

## Stacks (LIFO - Last In, First Out)

### Why Stacks Exist
LIFO behavior mirrors natural problem patterns - function calls, undo operations, parsing nested structures, backtracking algorithms.

### When to Use Stacks

**Use when:**
- Expression evaluation (parentheses matching, postfix notation)
- Function call management (call stack)
- Undo functionality in applications
- Backtracking algorithms (maze solving, N-queens)
- Parsing nested structures (JSON, XML, code)
- Depth-first search implementations

**Don't use when:**
- Need to access middle elements
- FIFO behavior is required
- Need to process elements in insertion order

**Modern reality:** Most languages provide built-in stacks. In interviews, often implement with arrays or linked lists to show understanding.

### Time Complexity
- **Push:** O(1)
- **Pop:** O(1)  
- **Peek/Top:** O(1)
- **Search:** O(n)

### Python Implementation
```python
class Stack:
    def __init__(self, limit=1000):
        self.top_item = None
        self.size = 0
        self.limit = limit
    
    def push(self, value):
        """Add element to top of stack"""
        if self.has_space():
            item = Node(value)
            item.set_link_node(self.top_item)
            self.top_item = item
            self.size += 1
            print(f"Pushed {value} onto stack")
        else:
            print(f"Stack overflow! No room for {value}")
    
    def pop(self):
        """Remove and return top element"""
        if not self.is_empty():
            item_to_remove = self.top_item
            self.top_item = item_to_remove.get_link_node()
            self.size -= 1
            value = item_to_remove.get_value()
            print(f"Popped {value} from stack")
            return value
        else:
            print("Stack underflow! Stack is empty")
            return None
    
    def peek(self):
        """View top element without removing it"""
        if not self.is_empty():
            return self.top_item.get_value()
        else:
            print("Nothing to peek at!")
            return None
    
    def has_space(self):
        return self.limit > self.size
    
    def is_empty(self):
        return self.size == 0
    
    def get_size(self):
        return self.size

# Example usage
stack = Stack(5)
stack.push("First")
stack.push("Second") 
stack.push("Third")
print(f"Top item: {stack.peek()}")  # Third
print(f"Popped: {stack.pop()}")     # Third
print(f"Popped: {stack.pop()}")     # Second
print(f"Size: {stack.get_size()}")  # 1
```

### C# Implementation
```csharp
public class Stack<T>
{
    private Node<T> topItem;
    private int size;
    private int limit;
    
    public Stack(int limit = 1000)
    {
        this.limit = limit;
        size = 0;
        topItem = null;
    }
    
    public void Push(T value)
    {
        if (HasSpace())
        {
            var item = new Node<T>(value);
            item.SetLinkNode(topItem);
            topItem = item;
            size++;
            Console.WriteLine($"Pushed {value} onto stack");
        }
        else
        {
            Console.WriteLine($"Stack overflow! No room for {value}");
        }
    }
    
    public T Pop()
    {
        if (!IsEmpty())
        {
            var itemToRemove = topItem;
            topItem = itemToRemove.GetLinkNode();
            size--;
            var value = itemToRemove.GetValue();
            Console.WriteLine($"Popped {value} from stack");
            return value;
        }
        else
        {
            Console.WriteLine("Stack underflow! Stack is empty");
            return default(T);
        }
    }
    
    public T Peek()
    {
        if (!IsEmpty())
        {
            return topItem.GetValue();
        }
        else
        {
            Console.WriteLine("Nothing to peek at!");
            return default(T);
        }
    }
    
    public bool HasSpace() => limit > size;
    public bool IsEmpty() => size == 0;
    public int Size => size;
}

// Example usage
var stack = new Stack<string>(5);
stack.Push("First");
stack.Push("Second");
stack.Push("Third");
Console.WriteLine($"Top item: {stack.Peek()}");  // Third
Console.WriteLine($"Popped: {stack.Pop()}");     // Third
Console.WriteLine($"Popped: {stack.Pop()}");     // Second
Console.WriteLine($"Size: {stack.Size}");        // 1
```

### Common Queue Applications

#### 1. Breadth-First Search
```python
from collections import deque

def bfs(graph, start_node, target):
    queue = deque([start_node])
    visited = set([start_node])
    
    while queue:
        current_node = queue.popleft()
        
        if current_node == target:
            return True
        
        for neighbor in graph[current_node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return False
```

#### 2. Level-Order Tree Traversal
```python
def level_order_traversal(root):
    if not root:
        return []
    
    queue = deque([root])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node.value)
        
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    
    return result
```

#### 3. Producer-Consumer with Queue
```python
import threading
from queue import Queue
import time

def producer(q, items):
    for item in items:
        print(f"Producing {item}")
        q.put(item)
        time.sleep(0.1)

def consumer(q):
    while True:
        if not q.empty():
            item = q.get()
            print(f"Consuming {item}")
            q.task_done()
        else:
            break

# Usage
work_queue = Queue()
items = ["task1", "task2", "task3", "task4"]

producer_thread = threading.Thread(target=producer, args=(work_queue, items))
consumer_thread = threading.Thread(target=consumer, args=(work_queue,))

producer_thread.start()
consumer_thread.start()

producer_thread.join()
consumer_thread.join()
```

## Advanced Variations

### Deque (Double-Ended Queue)
Supports insertion and deletion at both ends.

```python
# Using Python's collections.deque (preferred)
from collections import deque

dq = deque([1, 2, 3])
dq.appendleft(0)    # Add to front: [0, 1, 2, 3]
dq.append(4)        # Add to back: [0, 1, 2, 3, 4]
dq.popleft()        # Remove from front: [1, 2, 3, 4]
dq.pop()            # Remove from back: [1, 2, 3]
```

### Priority Queue
Elements are served based on priority rather than insertion order.

```python
import heapq

class PriorityQueue:
    def __init__(self):
        self.elements = []
    
    def enqueue(self, item, priority):
        heapq.heappush(self.elements, (priority, item))
    
    def dequeue(self):
        if self.elements:
            priority, item = heapq.heappop(self.elements)
            return item
        return None
    
    def is_empty(self):
        return len(self.elements) == 0

# Usage
pq = PriorityQueue()
pq.enqueue("Low priority task", 3)
pq.enqueue("High priority task", 1)
pq.enqueue("Medium priority task", 2)

while not pq.is_empty():
    print(pq.dequeue())
# Output: High priority task, Medium priority task, Low priority task
```

### Circular Queue (Ring Buffer)
Fixed-size queue that wraps around when full.

```python
class CircularQueue:
    def __init__(self, size):
        self.size = size
        self.queue = [None] * size
        self.head = 0
        self.tail = 0
        self.count = 0
    
    def enqueue(self, item):
        if self.count < self.size:
            self.queue[self.tail] = item
            self.tail = (self.tail + 1) % self.size
            self.count += 1
            return True
        return False  # Queue is full
    
    def dequeue(self):
        if self.count > 0:
            item = self.queue[self.head]
            self.queue[self.head] = None
            self.head = (self.head + 1) % self.size
            self.count -= 1
            return item
        return None  # Queue is empty
    
    def is_full(self):
        return self.count == self.size
    
    def is_empty(self):
        return self.count == 0

# Usage
cq = CircularQueue(3)
cq.enqueue("A")
cq.enqueue("B") 
cq.enqueue("C")
print(cq.enqueue("D"))  # False - queue is full
print(cq.dequeue())     # "A"
cq.enqueue("D")         # Now succeeds
```

## Interview Problems

### 1. Implement Queue Using Stacks
```python
class QueueUsingStacks:
    def __init__(self):
        self.stack1 = []  # For enqueue
        self.stack2 = []  # For dequeue
    
    def enqueue(self, item):
        self.stack1.append(item)
    
    def dequeue(self):
        if not self.stack2:
            # Move all elements from stack1 to stack2
            while self.stack1:
                self.stack2.append(self.stack1.pop())
        
        if self.stack2:
            return self.stack2.pop()
        return None
```

### 2. Implement Stack Using Queues
```python
from collections import deque

class StackUsingQueues:
    def __init__(self):
        self.queue1 = deque()
        self.queue2 = deque()
    
    def push(self, item):
        # Add to queue2, move all from queue1 to queue2, then swap
        self.queue2.append(item)
        while self.queue1:
            self.queue2.append(self.queue1.popleft())
        self.queue1, self.queue2 = self.queue2, self.queue1
    
    def pop(self):
        if self.queue1:
            return self.queue1.popleft()
        return None
```

### 3. Valid Parentheses (Stack Application)
```python
def is_valid_parentheses(s):
    stack = []
    mapping = {')': '(', '}': '{', ']': '['}
    
    for char in s:
        if char in mapping:
            if not stack or stack.pop() != mapping[char]:
                return False
        else:
            stack.append(char)
    
    return not stack

# Test cases
print(is_valid_parentheses("()"))       # True
print(is_valid_parentheses("()[]{}"))   # True
print(is_valid_parentheses("(]"))       # False
```

## Modern Usage

**Python:**
- Use `list` for simple stack (append/pop)
- Use `collections.deque` for queue operations
- Use `queue.Queue` for thread-safe operations
- Use `heapq` for priority queues

**C#:**
- Use `Stack<T>` for stack operations
- Use `Queue<T>` for queue operations
- Use `PriorityQueue<TElement, TPriority>` (.NET 6+)
- Use `ConcurrentQueue<T>` for thread-safety

## Key Takeaways

**Stacks are perfect for:**
- Reversing things
- Keeping track of state/history
- Parsing nested structures
- Recursive algorithm implementations

**Queues are perfect for:**
- Processing in order
- Buffering between producers and consumers
- Breadth-first algorithms
- Scheduling and task management

**Interview focus:** Understand when to use each, how to implement with arrays or linked lists, and common applications like expression evaluation and tree traversal. Stack Applications

#### 1. Parentheses Matching
```python
def is_balanced(expression):
    stack = []
    pairs = {'(': ')', '[': ']', '{': '}'}
    
    for char in expression:
        if char in pairs:  # Opening bracket
            stack.append(char)
        elif char in pairs.values():  # Closing bracket
            if not stack:
                return False
            if pairs[stack.pop()] != char:
                return False
    
    return len(stack) == 0

# Test cases
print(is_balanced("()[]{}"))     # True
print(is_balanced("([{}])"))     # True  
print(is_balanced("([)]"))       # False
```

#### 2. Postfix Expression Evaluation
```python
def evaluate_postfix(expression):
    stack = []
    operators = {'+', '-', '*', '/'}
    
    for token in expression.split():
        if token in operators:
            b = stack.pop()
            a = stack.pop()
            
            if token == '+':
                result = a + b
            elif token == '-':
                result = a - b
            elif token == '*':
                result = a * b
            elif token == '/':
                result = a / b
                
            stack.append(result)
        else:
            stack.append(float(token))
    
    return stack[0]

# Example: "3 4 + 2 *" = (3 + 4) * 2 = 14
print(evaluate_postfix("3 4 + 2 *"))  # 14.0
```

---

## Queues (FIFO - First In, First Out)

### Why Queues Exist
FIFO behavior matches real-world scenarios - waiting lines, task scheduling, breadth-first processing, producer-consumer systems.

### When to Use Queues

**Use when:**
- Task scheduling and job processing
- Breadth-first search algorithms
- Handling requests in order (web servers, print queues)
- Producer-consumer scenarios
- Buffer for streaming data
- Level-order tree traversal

**Don't use when:**
- Need random access to elements
- LIFO behavior is required
- Priority matters more than insertion order (use priority queue)

**Modern reality:** Built into most languages. Use `collections.deque` in Python, `Queue<T>` in C#.

### Time Complexity
- **Enqueue (add):** O(1)
- **Dequeue (remove):** O(1)
- **Front/Peek:** O(1)
- **Search:** O(n)

### Python Implementation
```python
class Queue:
    def __init__(self, max_size=None):
        self.head = None
        self.tail = None
        self.max_size = max_size
        self.size = 0
    
    def enqueue(self, value):
        """Add element to back of queue"""
        if self.has_space():
            item_to_add = Node(value)
            print(f"Adding {value} to the queue")
            
            if self.is_empty():
                self.head = item_to_add
                self.tail = item_to_add
            else:
                self.tail.set_link_node(item_to_add)
                self.tail = item_to_add
            
            self.size += 1
        else:
            print("Queue is full! Cannot add more items")
    
    def dequeue(self):
        """Remove and return front element"""
        if self.get_size() > 0:
            item_to_remove = self.head
            print(f"{item_to_remove.get_value()} is being served!")
            
            if self.get_size() == 1:
                self.head = None
                self.tail = None
            else:
                self.head = self.head.get_link_node()
            
            self.size -= 1
            return item_to_remove.get_value()
        else:
            print("The queue is empty!")
            return None
    
    def peek(self):
        """View front element without removing it"""
        if self.size > 0:
            return self.head.get_value()
        else:
            print("No items in queue!")
            return None
    
    def get_size(self):
        return self.size
    
    def has_space(self):
        if self.max_size is None:
            return True
        else:
            return self.max_size > self.get_size()
    
    def is_empty(self):
        return self.size == 0

# Example usage
print("Creating a deli queue with max 5 orders...")
deli_line = Queue(5)

# Add orders
orders = ["Sandwich", "Soup", "Salad", "Pizza", "Burger"]
for order in orders:
    deli_line.enqueue(order)

print(f"\nNext order up: {deli_line.peek()}")

# Serve orders
while not deli_line.is_empty():
    deli_line.dequeue()
```

### C# Implementation  
```csharp
public class Queue<T>
{
    private Node<T> head;
    private Node<T> tail;
    private int? maxSize;
    private int size;
    
    public Queue(int? maxSize = null)
    {
        this.maxSize = maxSize;
        size = 0;
        head = null;
        tail = null;
    }
    
    public void Enqueue(T value)
    {
        if (HasSpace())
        {
            var itemToAdd = new Node<T>(value);
            Console.WriteLine($"Adding {value} to the queue");
            
            if (IsEmpty())
            {
                head = itemToAdd;
                tail = itemToAdd;
            }
            else
            {
                tail.SetLinkNode(itemToAdd);
                tail = itemToAdd;
            }
            
            size++;
        }
        else
        {
            Console.WriteLine("Queue is full! Cannot add more items");
        }
    }
    
    public T Dequeue()
    {
        if (GetSize() > 0)
        {
            var itemToRemove = head;
            Console.WriteLine($"{itemToRemove.GetValue()} is being served!");
            
            if (GetSize() == 1)
            {
                head = null;
                tail = null;
            }
            else
            {
                head = head.GetLinkNode();
            }
            
            size--;
            return itemToRemove.GetValue();
        }
        else
        {
            Console.WriteLine("The queue is empty!");
            return default(T);
        }
    }
    
    public T Peek()
    {
        if (size > 0)
        {
            return head.GetValue();
        }
        else
        {
            Console.WriteLine("No items in queue!");
            return default(T);
        }
    }
    
    public int GetSize() => size;
    
    public bool HasSpace()
    {
        if (maxSize == null)
            return true;
        else
            return maxSize > GetSize();
    }
    
    public bool IsEmpty() => size == 0;
}

// Example usage
Console.WriteLine("Creating a deli queue with max 5 orders...");
var deliLine = new Queue<string>(5);

// Add orders
string[] orders = {"Sandwich", "Soup", "Salad", "Pizza", "Burger"};
foreach (var order in orders)
{
    deliLine.Enqueue(order);
}

Console.WriteLine($"\nNext order up: {deliLine.Peek()}");

// Serve orders
while (!deliLine.IsEmpty())
{
    deliLine.Dequeue();
}
```

### Common