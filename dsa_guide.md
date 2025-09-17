# Data Structures & Algorithms Study Guide

## Table of Contents

1. [Nodes](#nodes)
2. [Linked Lists](#linked-lists)
   - [Singly Linked List](#singly-linked-list)
   - [Doubly Linked List](#doubly-linked-list)
   - [Swapping Nodes](#swapping-nodes)
3. [Stacks](#stacks)
4. [Queues](#queues)
5. [Hash Maps](#hash-maps)
6. [Trees](#trees)
   - [Binary Trees](#binary-trees)
   - [Binary Search Trees](#binary-search-trees)
   - [Tree Traversal](#tree-traversal)
7. [Heaps](#heaps)
   - [Max Heap](#max-heap)
   - [Heap Sort](#heap-sort)
8. [Graphs](#graphs)
   - [Graph Representation](#graph-representation)
   - [Depth-First Search (DFS)](#depth-first-search-dfs)
   - [Breadth-First Search (BFS)](#breadth-first-search-bfs)
9. [Sorting Algorithms](#sorting-algorithms)
   - [Bubble Sort](#bubble-sort)
   - [Merge Sort](#merge-sort)
   - [Quick Sort](#quick-sort)
10. [Search Algorithms](#search-algorithms)
    - [Linear Search](#linear-search)
    - [Binary Search](#binary-search)
11. [Advanced Algorithms](#advanced-algorithms)
    - [Dijkstra's Algorithm](#dijkstras-algorithm)
    - [A* Pathfinding](#a-pathfinding)
12. [Recursion](#recursion)
13. [Pattern Matching](#pattern-matching)

## Nodes

**Key Concepts:**
- Building blocks of linked data structures
- Contain data and references to other nodes
- Form the basis for lists, trees, stacks, and queues

### Python Implementation

```python
class Node:
    def __init__(self, value, link_node=None):
        self.value = value
        self.link_node = link_node
    
    def set_link_node(self, link_node):
        self.link_node = link_node
    
    def get_link_node(self):
        return self.link_node
    
    def get_value(self):
        return self.value

# Example usage
node1 = Node("First")
node2 = Node("Second")
node1.set_link_node(node2)
```

### C# Implementation

```csharp
public class Node<T>
{
    public T Value { get; set; }
    public Node<T> LinkNode { get; set; }
    
    public Node(T value, Node<T> linkNode = null)
    {
        Value = value;
        LinkNode = linkNode;
    }
    
    public void SetLinkNode(Node<T> linkNode)
    {
        LinkNode = linkNode;
    }
    
    public Node<T> GetLinkNode()
    {
        return LinkNode;
    }
    
    public T GetValue()
    {
        return Value;
    }
}

// Example usage
var node1 = new Node<string>("First");
var node2 = new Node<string>("Second");
node1.SetLinkNode(node2);
```

## Linked Lists

**Key Concepts:**
- Sequential collection of nodes
- Dynamic size (grows/shrinks during runtime)
- Efficient insertion/deletion at any position
- No random access (must traverse from head)

### Singly Linked List

**Time Complexity:**
- Access: O(n)
- Search: O(n)
- Insertion: O(1) at head, O(n) at arbitrary position
- Deletion: O(1) at head, O(n) at arbitrary position

#### Python Implementation

```python
class LinkedList:
    def __init__(self, value=None):
        self.head_node = Node(value) if value is not None else None
    
    def get_head_node(self):
        return self.head_node
    
    def insert_beginning(self, new_value):
        new_node = Node(new_value)
        new_node.set_link_node(self.head_node)
        self.head_node = new_node
    
    def stringify_list(self):
        string_list = ""
        current_node = self.get_head_node()
        while current_node:
            if current_node.get_value() is not None:
                string_list += str(current_node.get_value()) + "\n"
            current_node = current_node.get_link_node()
        return string_list
    
    def remove_node(self, value_to_remove):
        if self.head_node is None:
            return
            
        if self.head_node.get_value() == value_to_remove:
            self.head_node = self.head_node.get_link_node()
            return
            
        current_node = self.head_node
        while current_node.get_link_node() is not None:
            if current_node.get_link_node().get_value() == value_to_remove:
                current_node.set_link_node(current_node.get_link_node().get_link_node())
                return
            current_node = current_node.get_link_node()
```

#### C# Implementation

```csharp
public class LinkedList<T>
{
    private Node<T> headNode;
    
    public LinkedList(T value = default(T))
    {
        if (!EqualityComparer<T>.Default.Equals(value, default(T)))
        {
            headNode = new Node<T>(value);
        }
    }
    
    public Node<T> GetHeadNode()
    {
        return headNode;
    }
    
    public void InsertBeginning(T newValue)
    {
        var newNode = new Node<T>(newValue);
        newNode.SetLinkNode(headNode);
        headNode = newNode;
    }
    
    public string StringifyList()
    {
        var stringList = new StringBuilder();
        var currentNode = GetHeadNode();
        
        while (currentNode != null)
        {
            if (currentNode.GetValue() != null)
            {
                stringList.AppendLine(currentNode.GetValue().ToString());
            }
            currentNode = currentNode.GetLinkNode();
        }
        
        return stringList.ToString();
    }
    
    public void RemoveNode(T valueToRemove)
    {
        if (headNode == null) return;
        
        if (EqualityComparer<T>.Default.Equals(headNode.GetValue(), valueToRemove))
        {
            headNode = headNode.GetLinkNode();
            return;
        }
        
        var currentNode = headNode;
        while (currentNode.GetLinkNode() != null)
        {
            if (EqualityComparer<T>.Default.Equals(currentNode.GetLinkNode().GetValue(), valueToRemove))
            {
                currentNode.SetLinkNode(currentNode.GetLinkNode().GetLinkNode());
                return;
            }
            currentNode = currentNode.GetLinkNode();
        }
    }
}
```

### Doubly Linked List

**Key Features:**
- Bidirectional traversal
- Each node has references to both next and previous nodes
- More memory overhead but more flexible operations

#### Python Implementation

```python
class DoublyLinkedNode:
    def __init__(self, value, next_node=None, prev_node=None):
        self.value = value
        self.next_node = next_node
        self.prev_node = prev_node
    
    def set_next_node(self, next_node):
        self.next_node = next_node
    
    def get_next_node(self):
        return self.next_node
    
    def set_prev_node(self, prev_node):
        self.prev_node = prev_node
    
    def get_prev_node(self):
        return self.prev_node
    
    def get_value(self):
        return self.value

class DoublyLinkedList:
    def __init__(self):
        self.head_node = None
        self.tail_node = None
    
    def add_to_head(self, new_value):
        new_head = DoublyLinkedNode(new_value)
        current_head = self.head_node
        
        if current_head is not None:
            current_head.set_prev_node(new_head)
            new_head.set_next_node(current_head)
        
        self.head_node = new_head
        
        if self.tail_node is None:
            self.tail_node = new_head
    
    def add_to_tail(self, new_value):
        new_tail = DoublyLinkedNode(new_value)
        current_tail = self.tail_node
        
        if current_tail is not None:
            current_tail.set_next_node(new_tail)
            new_tail.set_prev_node(current_tail)
        
        self.tail_node = new_tail
        
        if self.head_node is None:
            self.head_node = new_tail
    
    def remove_head(self):
        removed_head = self.head_node
        
        if removed_head is None:
            return None
        
        self.head_node = removed_head.get_next_node()
        
        if self.head_node is not None:
            self.head_node.set_prev_node(None)
        else:
            self.tail_node = None
        
        return removed_head.get_value()
    
    def remove_tail(self):
        removed_tail = self.tail_node
        
        if removed_tail is None:
            return None
        
        self.tail_node = removed_tail.get_prev_node()
        
        if self.tail_node is not None:
            self.tail_node.set_next_node(None)
        else:
            self.head_node = None
        
        return removed_tail.get_value()
```

#### C# Implementation

```csharp
public class DoublyLinkedNode<T>
{
    public T Value { get; set; }
    public DoublyLinkedNode<T> NextNode { get; set; }
    public DoublyLinkedNode<T> PrevNode { get; set; }
    
    public DoublyLinkedNode(T value, DoublyLinkedNode<T> nextNode = null, DoublyLinkedNode<T> prevNode = null)
    {
        Value = value;
        NextNode = nextNode;
        PrevNode = prevNode;
    }
}

public class DoublyLinkedList<T>
{
    private DoublyLinkedNode<T> headNode;
    private DoublyLinkedNode<T> tailNode;
    
    public void AddToHead(T newValue)
    {
        var newHead = new DoublyLinkedNode<T>(newValue);
        var currentHead = headNode;
        
        if (currentHead != null)
        {
            currentHead.PrevNode = newHead;
            newHead.NextNode = currentHead;
        }
        
        headNode = newHead;
        
        if (tailNode == null)
        {
            tailNode = newHead;
        }
    }
    
    public void AddToTail(T newValue)
    {
        var newTail = new DoublyLinkedNode<T>(newValue);
        var currentTail = tailNode;
        
        if (currentTail != null)
        {
            currentTail.NextNode = newTail;
            newTail.PrevNode = currentTail;
        }
        
        tailNode = newTail;
        
        if (headNode == null)
        {
            headNode = newTail;
        }
    }
    
    public T RemoveHead()
    {
        var removedHead = headNode;
        
        if (removedHead == null)
        {
            return default(T);
        }
        
        headNode = removedHead.NextNode;
        
        if (headNode != null)
        {
            headNode.PrevNode = null;
        }
        else
        {
            tailNode = null;
        }
        
        return removedHead.Value;
    }
    
    public T RemoveTail()
    {
        var removedTail = tailNode;
        
        if (removedTail == null)
        {
            return default(T);
        }
        
        tailNode = removedTail.PrevNode;
        
        if (tailNode != null)
        {
            tailNode.NextNode = null;
        }
        else
        {
            headNode = null;
        }
        
        return removedTail.Value;
    }
}
```

## Stacks

**Key Concepts:**
- Last In, First Out (LIFO) data structure
- Operations: Push (add), Pop (remove), Peek (view top)
- Used in: function call management, undo operations, expression evaluation

**Time Complexity:**
- Push: O(1)
- Pop: O(1)
- Peek: O(1)

### Python Implementation

```python
class Stack:
    def __init__(self, limit=1000):
        self.top_item = None
        self.size = 0
        self.limit = limit
    
    def push(self, value):
        if self.has_space():
            item = Node(value)
            item.set_link_node(self.top_item)
            self.top_item = item
            self.size += 1
        else:
            print(f"No room for {value}!")
    
    def pop(self):
        if not self.is_empty():
            item_to_remove = self.top_item
            self.top_item = item_to_remove.get_link_node()
            self.size -= 1
            return item_to_remove.get_value()
        else:
            print("Stack is empty!")
            return None
    
    def peek(self):
        if not self.is_empty():
            return self.top_item.get_value()
        else:
            print("Nothing to see here!")
            return None
    
    def has_space(self):
        return self.limit > self.size
    
    def is_empty(self):
        return self.size == 0
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
        }
        else
        {
            Console.WriteLine($"No room for {value}!");
        }
    }
    
    public T Pop()
    {
        if (!IsEmpty())
        {
            var itemToRemove = topItem;
            topItem = itemToRemove.GetLinkNode();
            size--;
            return itemToRemove.GetValue();
        }
        else
        {
            Console.WriteLine("Stack is empty!");
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
            Console.WriteLine("Nothing to see here!");
            return default(T);
        }
    }
    
    public bool HasSpace()
    {
        return limit > size;
    }
    
    public bool IsEmpty()
    {
        return size == 0;
    }
    
    public int Size => size;
}
```

## Queues

**Key Concepts:**
- First In, First Out (FIFO) data structure
- Operations: Enqueue (add to back), Dequeue (remove from front), Peek (view front)
- Used in: process scheduling, breadth-first search, handling requests

**Time Complexity:**
- Enqueue: O(1)
- Dequeue: O(1)
- Peek: O(1)

### Python Implementation

```python
class Queue:
    def __init__(self, max_size=None):
        self.head = None
        self.tail = None
        self.max_size = max_size
        self.size = 0
    
    def enqueue(self, value):
        if self.has_space():
            item_to_add = Node(value)
            if self.is_empty():
                self.head = item_to_add
                self.tail = item_to_add
            else:
                self.tail.set_link_node(item_to_add)
                self.tail = item_to_add
            self.size += 1
        else:
            print("Sorry, no more room!")
    
    def dequeue(self):
        if self.get_size() > 0:
            item_to_remove = self.head
            if self.get_size() == 1:
                self.head = None
                self.tail = None
            else:
                self.head = self.head.get_link_node()
            self.size -= 1
            return item_to_remove.get_value()
        else:
            print("The queue is totally empty!")
            return None
    
    def peek(self):
        if self.size > 0:
            return self.head.get_value()
        else:
            print("No items waiting!")
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
            Console.WriteLine("Sorry, no more room!");
        }
    }
    
    public T Dequeue()
    {
        if (GetSize() > 0)
        {
            var itemToRemove = head;
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
            Console.WriteLine("The queue is totally empty!");
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
            Console.WriteLine("No items waiting!");
            return default(T);
        }
    }
    
    public int GetSize()
    {
        return size;
    }
    
    public bool HasSpace()
    {
        if (maxSize == null)
        {
            return true;
        }
        else
        {
            return maxSize > GetSize();
        }
    }
    
    public bool IsEmpty()
    {
        return size == 0;
    }
}
```

## Hash Maps

**Key Concepts:**
- Key-value storage with fast lookups
- Uses hash function to map keys to array indices
- Handles collisions through chaining or open addressing
- Average O(1) access time

**Time Complexity:**
- Average case: O(1) for all operations
- Worst case: O(n) when many collisions occur

### Python Implementation

```python
class HashMap:
    def __init__(self, array_size):
        self.array_size = array_size
        self.array = [None for _ in range(array_size)]
    
    def hash(self, key, count_collisions=0):
        key_bytes = key.encode()
        hash_code = sum(key_bytes)
        return hash_code + count_collisions
    
    def compressor(self, hash_code):
        return hash_code % self.array_size
    
    def assign(self, key, value):
        array_index = self.compressor(self.hash(key))
        current_array_value = self.array[array_index]
        
        if current_array_value is None:
            self.array[array_index] = [key, value]
            return
        
        if current_array_value[0] == key:
            self.array[array_index] = [key, value]
            return
        
        # Handle collision using linear probing
        number_collisions = 1
        while current_array_value[0] != key:
            new_hash_code = self.hash(key, number_collisions)
            new_array_index = self.compressor(new_hash_code)
            current_array_value = self.array[new_array_index]
            
            if current_array_value is None:
                self.array[new_array_index] = [key, value]
                return
            
            if current_array_value[0] == key:
                self.array[new_array_index] = [key, value]
                return
            
            number_collisions += 1
    
    def retrieve(self, key):
        array_index = self.compressor(self.hash(key))
        possible_return_value = self.array[array_index]
        
        if possible_return_value is None:
            return None
        
        if possible_return_value[0] == key:
            return possible_return_value[1]
        
        # Handle collision case
        retrieval_collisions = 1
        while possible_return_value and possible_return_value[0] != key:
            new_hash_code = self.hash(key, retrieval_collisions)
            retrieving_array_index = self.compressor(new_hash_code)
            possible_return_value = self.array[retrieving_array_index]
            
            if possible_return_value is None:
                return None
            
            if possible_return_value[0] == key:
                return possible_return_value[1]
            
            retrieval_collisions += 1
        
        return None
```

### C# Implementation

```csharp
public class HashMap<TKey, TValue>
{
    private int arraySize;
    private (TKey key, TValue value)?[] array;
    
    public HashMap(int arraySize)
    {
        this.arraySize = arraySize;
        array = new (TKey key, TValue value)?[arraySize];
    }
    
    private int Hash(TKey key, int countCollisions = 0)
    {
        int hashCode = key.GetHashCode();
        return Math.Abs(hashCode + countCollisions);
    }
    
    private int Compressor(int hashCode)
    {
        return hashCode % arraySize;
    }
    
    public void Assign(TKey key, TValue value)
    {
        int arrayIndex = Compressor(Hash(key));
        var currentArrayValue = array[arrayIndex];
        
        if (!currentArrayValue.HasValue)
        {
            array[arrayIndex] = (key, value);
            return;
        }
        
        if (EqualityComparer<TKey>.Default.Equals(currentArrayValue.Value.key, key))
        {
            array[arrayIndex] = (key, value);
            return;
        }
        
        // Handle collision using linear probing
        int numberCollisions = 1;
        while (!EqualityComparer<TKey>.Default.Equals(currentArrayValue.Value.key, key))
        {
            int newHashCode = Hash(key, numberCollisions);
            int newArrayIndex = Compressor(newHashCode);
            currentArrayValue = array[newArrayIndex];
            
            if (!currentArrayValue.HasValue)
            {
                array[newArrayIndex] = (key, value);
                return;
            }
            
            if (EqualityComparer<TKey>.Default.Equals(currentArrayValue.Value.key, key))
            {
                array[newArrayIndex] = (key, value);
                return;
            }
            
            numberCollisions++;
            
            // Prevent infinite loop
            if (numberCollisions >= arraySize)
            {
                throw new InvalidOperationException("HashMap is full");
            }
        }
    }
    
    public TValue Retrieve(TKey key)
    {
        int arrayIndex = Compressor(Hash(key));
        var possibleReturnValue = array[arrayIndex];
        
        if (!possibleReturnValue.HasValue)
        {
            return default(TValue);
        }
        
        if (EqualityComparer<TKey>.Default.Equals(possibleReturnValue.Value.key, key))
        {
            return possibleReturnValue.Value.value;
        }
        
        // Handle collision case
        int retrievalCollisions = 1;
        while (possibleReturnValue.HasValue && !EqualityComparer<TKey>.Default.Equals(possibleReturnValue.Value.key, key))
        {
            int newHashCode = Hash(key, retrievalCollisions);
            int retrievingArrayIndex = Compressor(newHashCode);
            possibleReturnValue = array[retrievingArrayIndex];
            
            if (!possibleReturnValue.HasValue)
            {
                return default(TValue);
            }
            
            if (EqualityComparer<TKey>.Default.Equals(possibleReturnValue.Value.key, key))
            {
                return possibleReturnValue.Value.value;
            }
            
            retrievalCollisions++;
            
            // Prevent infinite loop
            if (retrievalCollisions >= arraySize)
            {
                break;
            }
        }
        
        return default(TValue);
    }
}
```

## Sorting Algorithms

### Bubble Sort

**Key Concepts:**
- Simple comparison-based sorting
- Repeatedly swaps adjacent elements if they're in wrong order
- Multiple passes until no swaps needed

**Time Complexity:**
- Best case: O(n) - already sorted
- Average case: O(n²)
- Worst case: O(n²)
- Space complexity: O(1)

#### Python Implementation

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        swapped = False
        # Last i elements are already in place
        for j in range(0, n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                swapped = True
        
        # If no swapping occurred, array is sorted
        if not swapped:
            break
    
    return arr

# Example usage
numbers = [64, 34, 25, 12, 22, 11, 90]
sorted_numbers = bubble_sort(numbers.copy())
print(f"Sorted array: {sorted_numbers}")
```

#### C# Implementation

```csharp
public static class BubbleSort
{
    public static T[] Sort<T>(T[] arr) where T : IComparable<T>
    {
        int n = arr.Length;
        T[] result = (T[])arr.Clone();
        
        for (int i = 0; i < n; i++)
        {
            bool swapped = false;
            // Last i elements are already in place
            for (int j = 0; j < n - i - 1; j++)
            {
                if (result[j].CompareTo(result[j + 1]) > 0)
                {
                    // Swap elements
                    T temp = result[j];
                    result[j] = result[j + 1];
                    result[j + 1] = temp;
                    swapped = true;
                }
            }
            
            // If no swapping occurred, array is sorted
            if (!swapped)
                break;
        }
        
        return result;
    }
}

// Example usage
int[] numbers = {64, 34, 25, 12, 22, 11, 90};
int[] sortedNumbers = BubbleSort.Sort(numbers);
Console.WriteLine($"Sorted array: [{string.Join(", ", sortedNumbers)}]");
```

### Merge Sort

**Key Concepts:**
- Divide-and-conquer algorithm
- Recursively divides array into halves
- Merges sorted halves back together
- Stable sort (maintains relative order of equal elements)

**Time Complexity:**
- All cases: O(n log n)
- Space complexity: O(n)

#### Python Implementation

```python
def merge_sort(items):
    if len(items) <= 1:
        return items
    
    middle_index = len(items) // 2
    left_split = items[:middle_index]
    right_split = items[middle_index:]
    
    left_sorted = merge_sort(left_split)
    right_sorted = merge_sort(right_split)
    
    return merge(left_sorted, right_sorted)

def merge(left, right):
    result = []
    left_index = 0
    right_index = 0
    
    # Merge the two arrays while both have elements
    while left_index < len(left) and right_index < len(right):
        if left[left_index] <= right[right_index]:
            result.append(left[left_index])
            left_index += 1
        else:
            result.append(right[right_index])
            right_index += 1
    
    # Add remaining elements
    result.extend(left[left_index:])
    result.extend(right[right_index:])
    
    return result

# Example usage
unordered_list = [356, 746, 264, 569, 949, 895, 125, 455]
ordered_list = merge_sort(unordered_list)
print(f"Sorted: {ordered_list}")
```

#### C# Implementation

```csharp
public static class MergeSort
{
    public static T[] Sort<T>(T[] items) where T : IComparable<T>
    {
        if (items.Length <= 1)
            return (T[])items.Clone();
        
        int middleIndex = items.Length / 2;
        T[] leftSplit = new T[middleIndex];
        T[] rightSplit = new T[items.Length - middleIndex];
        
        Array.Copy(items, 0, leftSplit, 0, middleIndex);
        Array.Copy(items, middleIndex, rightSplit, 0, items.Length - middleIndex);
        
        T[] leftSorted = Sort(leftSplit);
        T[] rightSorted = Sort(rightSplit);
        
        return Merge(leftSorted, rightSorted);
    }
    
    private static T[] Merge<T>(T[] left, T[] right) where T : IComparable<T>
    {
        var result = new List<T>();
        int leftIndex = 0;
        int rightIndex = 0;
        
        // Merge the two arrays while both have elements
        while (leftIndex < left.Length && rightIndex < right.Length)
        {
            if (left[leftIndex].CompareTo(right[rightIndex]) <= 0)
            {
                result.Add(left[leftIndex]);
                leftIndex++;
            }
            else
            {
                result.Add(right[rightIndex]);
                rightIndex++;
            }
        }
        
        // Add remaining elements
        while (leftIndex < left.Length)
        {
            result.Add(left[leftIndex]);
            leftIndex++;
        }
        
        while (rightIndex < right.Length)
        {
            result.Add(right[rightIndex]);
            rightIndex++;
        }
        
        return result.ToArray();
    }
}

// Example usage
int[] unorderedList = {356, 746, 264, 569, 949, 895, 125, 455};
int[] orderedList = MergeSort.Sort(unorderedList);
Console.WriteLine($"Sorted: [{string.Join(", ", orderedList)}]");
```

### Quick Sort

**Key Concepts:**
- Divide-and-conquer algorithm using pivot element
- Partitions array around pivot
- Recursively sorts subarrays
- In-place sorting (low space complexity)

**Time Complexity:**
- Best/Average case: O(n log n)
- Worst case: O(n²) - when pivot is always smallest/largest
- Space complexity: O(log n) for recursion stack

#### Python Implementation

```python
from random import randrange

def quicksort(arr, start=0, end=None):
    if end is None:
        end = len(arr) - 1
    
    if start >= end:
        return
    
    # Select random pivot and move to end
    pivot_idx = randrange(start, end + 1)
    arr[end], arr[pivot_idx] = arr[pivot_idx], arr[end]
    pivot_element = arr[end]
    
    # Partition array
    less_than_pointer = start
    
    for i in range(start, end):
        if arr[i] < pivot_element:
            arr[i], arr[less_than_pointer] = arr[less_than_pointer], arr[i]
            less_than_pointer += 1
    
    # Move pivot to correct position
    arr[end], arr[less_than_pointer] = arr[less_than_pointer], arr[end]
    
    # Recursively sort left and right subarrays
    quicksort(arr, start, less_than_pointer - 1)
    quicksort(arr, less_than_pointer + 1, end)

# Example usage
test_list = [5, 3, 1, 7, 4, 6, 2, 8]
print(f"Before sorting: {test_list}")
quicksort(test_list)
print(f"After sorting: {test_list}")
```

#### C# Implementation

```csharp
public static class QuickSort
{
    private static Random random = new Random();
    
    public static void Sort<T>(T[] arr, int start = 0, int? end = null) where T : IComparable<T>
    {
        if (end == null)
            end = arr.Length - 1;
        
        if (start >= end)
            return;
        
        // Select random pivot and move to end
        int pivotIdx = random.Next(start, end.Value + 1);
        Swap(arr, end.Value, pivotIdx);
        T pivotElement = arr[end.Value];
        
        // Partition array
        int lessThanPointer = start;
        
        for (int i = start; i < end; i++)
        {
            if (arr[i].CompareTo(pivotElement) < 0)
            {
                Swap(arr, i, lessThanPointer);
                lessThanPointer++;
            }
        }
        
        // Move pivot to correct position
        Swap(arr, end.Value, lessThanPointer);
        
        // Recursively sort left and right subarrays
        Sort(arr, start, lessThanPointer - 1);
        Sort(arr, lessThanPointer + 1, end);
    }
    
    private static void Swap<T>(T[] arr, int i, int j)
    {
        T temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}

// Example usage
int[] testList = {5, 3, 1, 7, 4, 6, 2, 8};
Console.WriteLine($"Before sorting: [{string.Join(", ", testList)}]");
QuickSort.Sort(testList);
Console.WriteLine($"After sorting: [{string.Join(", ", testList)}]");
```

## Search Algorithms

### Linear Search

**Key Concepts:**
- Brute force search through each element sequentially
- Works on unsorted data
- Guaranteed to find element if it exists

**Time Complexity:**
- Best case: O(1) - element is first
- Average case: O(n)
- Worst case: O(n) - element is last or not found

#### Python Implementation

```python
def linear_search(search_list, target_value):
    for idx in range(len(search_list)):
        if search_list[idx] == target_value:
            return idx
    raise ValueError(f"{target_value} not in list")

# Example usage
recipe = ["nori", "tuna", "soy sauce", "sushi rice"]
try:
    index = linear_search(recipe, "tuna")
    print(f"Found 'tuna' at index {index}")
except ValueError as e:
    print(e)
```

#### C# Implementation

```csharp
public static class LinearSearch
{
    public static int Search<T>(T[] searchList, T targetValue) where T : IEquatable<T>
    {
        for (int idx = 0; idx < searchList.Length; idx++)
        {
            if (searchList[idx].Equals(targetValue))
            {
                return idx;
            }
        }
        throw new ArgumentException($"{targetValue} not in list");
    }
}

// Example usage
string[] recipe = {"nori", "tuna", "soy sauce", "sushi rice"};
try
{
    int index = LinearSearch.Search(recipe, "tuna");
    Console.WriteLine($"Found 'tuna' at index {index}");
}
catch (ArgumentException e)
{
    Console.WriteLine(e.Message);
}
```

### Binary Search

**Key Concepts:**
- Efficient search algorithm for sorted arrays
- Repeatedly divides search space in half
- Compares target with middle element

**Time Complexity:**
- All cases: O(log n)
- Space complexity: O(1) iterative, O(log n) recursive

#### Python Recursive Implementation

```python
def binary_search_recursive(sorted_list, left_pointer, right_pointer, target):
    if left_pointer >= right_pointer:
        return "value not found"
    
    mid_idx = (left_pointer + right_pointer) // 2
    mid_val = sorted_list[mid_idx]
    
    if mid_val == target:
        return mid_idx
    elif mid_val > target:
        return binary_search_recursive(sorted_list, left_pointer, mid_idx, target)
    else:
        return binary_search_recursive(sorted_list, mid_idx + 1, right_pointer, target)

# Example usage
values = [77, 80, 102, 123, 288, 300, 540]
result = binary_search_recursive(values, 0, len(values), 288)
print(f"Element 288 is located at index {result}")
```

#### Python Iterative Implementation

```python
def binary_search_iterative(sorted_list, target):
    left_pointer = 0
    right_pointer = len(sorted_list)
    
    while left_pointer < right_pointer:
        mid_idx = (left_pointer + right_pointer) // 2
        mid_val = sorted_list[mid_idx]
        
        if mid_val == target:
            return mid_idx
        elif target < mid_val:
            right_pointer = mid_idx
        else:
            left_pointer = mid_idx + 1
    
    return "Value not in list"

# Test cases
print(binary_search_iterative([5,6,7,8,9], 9))  # 4
print(binary_search_iterative([5,6,7,8,9], 10)) # Value not in list
print(binary_search_iterative([5,6,7,8,9], 8))  # 3
```

#### C# Implementation

```csharp
public static class BinarySearch
{
    // Recursive version
    public static int SearchRecursive<T>(T[] sortedList, int leftPointer, int rightPointer, T target) 
        where T : IComparable<T>
    {
        if (leftPointer >= rightPointer)
            return -1; // Not found
        
        int midIdx = (leftPointer + rightPointer) / 2;
        T midVal = sortedList[midIdx];
        
        int comparison = midVal.CompareTo(target);
        if (comparison == 0)
            return midIdx;
        else if (comparison > 0)
            return SearchRecursive(sortedList, leftPointer, midIdx, target);
        else
            return SearchRecursive(sortedList, midIdx + 1, rightPointer, target);
    }
    
    // Iterative version
    public static int SearchIterative<T>(T[] sortedList, T target) where T : IComparable<T>
    {
        int leftPointer = 0;
        int rightPointer = sortedList.Length;
        
        while (leftPointer < rightPointer)
        {
            int midIdx = (leftPointer + rightPointer) / 2;
            T midVal = sortedList[midIdx];
            
            int comparison = midVal.CompareTo(target);
            if (comparison == 0)
                return midIdx;
            else if (comparison > 0)
                rightPointer = midIdx;
            else
                leftPointer = midIdx + 1;
        }
        
        return -1; // Not found
    }
}

// Example usage
int[] values = {77, 80, 102, 123, 288, 300, 540};
int result = BinarySearch.SearchRecursive(values, 0, values.Length, 288);
Console.WriteLine($"Element 288 is located at index {result}");

// Test cases
int[] testArray = {5, 6, 7, 8, 9};
Console.WriteLine(BinarySearch.SearchIterative(testArray, 9));  // 4
Console.WriteLine(BinarySearch.SearchIterative(testArray, 10)); // -1
Console.WriteLine(BinarySearch.SearchIterative(testArray, 8));  // 3
```

## Trees

**Key Concepts:**
- Hierarchical data structure with nodes
- Root node at top, leaf nodes at bottom
- Each node can have multiple children
- Used in file systems, decision making, parsing

### Binary Trees

**Key Features:**
- Each node has at most two children (left and right)
- Foundation for binary search trees, heaps, and expression trees

#### Python Implementation

```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.children = []
    
    def add_child(self, child_node):
        print(f"Adding {child_node.value}")
        self.children.append(child_node)
    
    def remove_child(self, child_node):
        print(f"Removing {child_node.value} from {self.value}")
        self.children = [child for child in self.children if child is not child_node]
    
    def traverse(self):
        nodes_to_visit = [self]
        while len(nodes_to_visit) > 0:
            current_node = nodes_to_visit.pop()
            print(current_node.value)
            nodes_to_visit += current_node.children

# Example usage
root = TreeNode("Root")
child1 = TreeNode("Child 1")
child2 = TreeNode("Child 2")
root.add_child(child1)
root.add_child(child2)
root.traverse()
```

#### C# Implementation

```csharp
public class TreeNode<T>
{
    public T Value { get; set; }
    public List<TreeNode<T>> Children { get; set; }
    
    public TreeNode(T value)
    {
        Value = value;
        Children = new List<TreeNode<T>>();
    }
    
    public void AddChild(TreeNode<T> childNode)
    {
        Console.WriteLine($"Adding {childNode.Value}");
        Children.Add(childNode);
    }
    
    public void RemoveChild(TreeNode<T> childNode)
    {
        Console.WriteLine($"Removing {childNode.Value} from {Value}");
        Children.RemoveAll(child => ReferenceEquals(child, childNode));
    }
    
    public void Traverse()
    {
        var nodesToVisit = new Stack<TreeNode<T>>();
        nodesToVisit.Push(this);
        
        while (nodesToVisit.Count > 0)
        {
            var currentNode = nodesToVisit.Pop();
            Console.WriteLine(currentNode.Value);
            
            foreach (var child in currentNode.Children)
            {
                nodesToVisit.Push(child);
            }
        }
    }
}

// Example usage
var root = new TreeNode<string>("Root");
var child1 = new TreeNode<string>("Child 1");
var child2 = new TreeNode<string>("Child 2");
root.AddChild(child1);
root.AddChild(child2);
root.Traverse();
```

### Binary Search Trees

**Key Concepts:**
- Binary tree with ordering property
- Left subtree values < root < right subtree values
- Enables efficient searching, insertion, and deletion

**Time Complexity:**
- Average case: O(log n) for all operations
- Worst case: O(n) when tree becomes skewed

#### Python Implementation

```python
class BinarySearchTree:
    def __init__(self, value, depth=1):
        self.value = value
        self.depth = depth
        self.left = None
        self.right = None
    
    def insert(self, value):
        if value < self.value:
            if self.left is None:
                self.left = BinarySearchTree(value, self.depth + 1)
                print(f'Tree node {value} added to the left of {self.value} at depth {self.depth + 1}')
            else:
                self.left.insert(value)
        else:
            if self.right is None:
                self.right = BinarySearchTree(value, self.depth + 1)
                print(f'Tree node {value} added to the right of {self.value} at depth {self.depth + 1}')
            else:
                self.right.insert(value)
    
    def get_node_by_value(self, value):
        if self.value == value:
            return self
        elif self.left is not None and value < self.value:
            return self.left.get_node_by_value(value)
        elif self.right is not None and value >= self.value:
            return self.right.get_node_by_value(value)
        else:
            return None
    
    def depth_first_traversal(self):
        if self.left is not None:
            self.left.depth_first_traversal()
        print(f'Depth={self.depth}, Value={self.value}')
        if self.right is not None:
            self.right.depth_first_traversal()

# Example usage
import random
print("Creating Binary Search Tree rooted at value 15:")
tree = BinarySearchTree(15)
for x in range(10):
    tree.insert(random.randint(0, 100))
print("Printing the inorder depth-first traversal:")
tree.depth_first_traversal()
```

#### C# Implementation

```csharp
public class BinarySearchTree<T> where T : IComparable<T>
{
    public T Value { get; set; }
    public int Depth { get; set; }
    public BinarySearchTree<T> Left { get; set; }
    public BinarySearchTree<T> Right { get; set; }
    
    public BinarySearchTree(T value, int depth = 1)
    {
        Value = value;
        Depth = depth;
        Left = null;
        Right = null;
    }
    
    public void Insert(T value)
    {
        if (value.CompareTo(Value) < 0)
        {
            if (Left == null)
            {
                Left = new BinarySearchTree<T>(value, Depth + 1);
                Console.WriteLine($"Tree node {value} added to the left of {Value} at depth {Depth + 1}");
            }
            else
            {
                Left.Insert(value);
            }
        }
        else
        {
            if (Right == null)
            {
                Right = new BinarySearchTree<T>(value, Depth + 1);
                Console.WriteLine($"Tree node {value} added to the right of {Value} at depth {Depth + 1}");
            }
            else
            {
                Right.Insert(value);
            }
        }
    }
    
    public BinarySearchTree<T> GetNodeByValue(T value)
    {
        if (Value.CompareTo(value) == 0)
        {
            return this;
        }
        else if (Left != null && value.CompareTo(Value) < 0)
        {
            return Left.GetNodeByValue(value);
        }
        else if (Right != null && value.CompareTo(Value) >= 0)
        {
            return Right.GetNodeByValue(value);
        }
        else
        {
            return null;
        }
    }
    
    public void DepthFirstTraversal()
    {
        Left?.DepthFirstTraversal();
        Console.WriteLine($"Depth={Depth}, Value={Value}");
        Right?.DepthFirstTraversal();
    }
}

// Example usage
var random = new Random();
Console.WriteLine("Creating Binary Search Tree rooted at value 15:");
var tree = new BinarySearchTree<int>(15);
for (int x = 0; x < 10; x++)
{
    tree.Insert(random.Next(0, 100));
}
Console.WriteLine("Printing the inorder depth-first traversal:");
tree.DepthFirstTraversal();
```

## Heaps

**Key Concepts:**
- Complete binary tree with heap property
- Max-heap: parent ≥ children, Min-heap: parent ≤ children
- Root contains maximum (max-heap) or minimum (min-heap) element
- Used in priority queues and heap sort

**Time Complexity:**
- Insert: O(log n)
- Extract max/min: O(log n)
- Peek: O(1)

### Max Heap

#### Python Implementation

```python
class MaxHeap:
    def __init__(self):
        self.heap_list = [None]  # Index 0 unused for easier math
        self.count = 0
    
    def parent_idx(self, idx):
        return idx // 2
    
    def left_child_idx(self, idx):
        return idx * 2
    
    def right_child_idx(self, idx):
        return idx * 2 + 1
    
    def child_present(self, idx):
        return self.left_child_idx(idx) <= self.count
    
    def add(self, element):
        self.count += 1
        print(f"Adding: {element} to {self.heap_list}")
        self.heap_list.append(element)
        self.heapify_up()
    
    def heapify_up(self):
        print("Heapifying up")
        idx = self.count
        while self.parent_idx(idx) > 0:
            child = self.heap_list[idx]
            parent = self.heap_list[self.parent_idx(idx)]
            if parent < child:
                print(f"swapping {parent} with {child}")
                self.heap_list[idx] = parent
                self.heap_list[self.parent_idx(idx)] = child
            idx = self.parent_idx(idx)
        print(f"Heap Restored {self.heap_list}")
    
    def retrieve_max(self):
        if self.count == 0:
            print("No items in heap")
            return None
        
        max_value = self.heap_list[1]
        print(f"Removing: {max_value} from {self.heap_list}")
        self.heap_list[1] = self.heap_list[self.count]
        self.count -= 1
        self.heap_list.pop()
        print(f"Last element moved to first: {self.heap_list}")
        if self.count > 0:
            self.heapify_down()
        return max_value
    
    def heapify_down(self):
        idx = 1
        while self.child_present(idx):
            print("Heapifying down!")
            larger_child_idx = self.get_larger_child_idx(idx)
            child = self.heap_list[larger_child_idx]
            parent = self.heap_list[idx]
            if parent < child:
                self.heap_list[idx] = child
                self.heap_list[larger_child_idx] = parent
                idx = larger_child_idx
            else:
                break
        print(f"HEAP RESTORED! {self.heap_list}")
    
    def get_larger_child_idx(self, idx):
        if self.right_child_idx(idx) > self.count:
            print("There is only a left child")
            return self.left_child_idx(idx)
        else:
            left_child = self.heap_list[self.left_child_idx(idx)]
            right_child = self.heap_list[self.right_child_idx(idx)]
            if left_child > right_child:
                print(f"Left child {left_child} is larger than right child {right_child}")
                return self.left_child_idx(idx)
            else:
                print(f"Right child {right_child} is larger than left child {left_child}")
                return self.right_child_idx(idx)

# Example usage
heap = MaxHeap()
heap.add(10)
heap.add(5)
heap.add(15)
heap.add(20)
print(f"Max value: {heap.retrieve_max()}")
```

#### C# Implementation

```csharp
public class MaxHeap<T> where T : IComparable<T>
{
    private List<T> heapList;
    private int count;
    
    public MaxHeap()
    {
        heapList = new List<T> { default(T) }; // Index 0 unused
        count = 0;
    }
    
    private int ParentIdx(int idx) => idx / 2;
    private int LeftChildIdx(int idx) => idx * 2;
    private int RightChildIdx(int idx) => idx * 2 + 1;
    private bool ChildPresent(int idx) => LeftChildIdx(idx) <= count;
    
    public void Add(T element)
    {
        count++;
        Console.WriteLine($"Adding: {element} to heap");
        heapList.Add(element);
        HeapifyUp();
    }
    
    private void HeapifyUp()
    {
        Console.WriteLine("Heapifying up");
        int idx = count;
        while (ParentIdx(idx) > 0)
        {
            T child = heapList[idx];
            T parent = heapList[ParentIdx(idx)];
            if (parent.CompareTo(child) < 0)
            {
                Console.WriteLine($"Swapping {parent} with {child}");
                heapList[idx] = parent;
                heapList[ParentIdx(idx)] = child;
            }
            else
            {
                break;
            }
            idx = ParentIdx(idx);
        }
        Console.WriteLine("Heap Restored");
    }
    
    public T RetrieveMax()
    {
        if (count == 0)
        {
            Console.WriteLine("No items in heap");
            return default(T);
        }
        
        T maxValue = heapList[1];
        Console.WriteLine($"Removing: {maxValue} from heap");
        heapList[1] = heapList[count];
        count--;
        heapList.RemoveAt(heapList.Count - 1);
        
        if (count > 0)
        {
            HeapifyDown();
        }
        
        return maxValue;
    }
    
    private void HeapifyDown()
    {
        int idx = 1;
        while (ChildPresent(idx))
        {
            Console.WriteLine("Heapifying down!");
            int largerChildIdx = GetLargerChildIdx(idx);
            T child = heapList[largerChildIdx];
            T parent = heapList[idx];
            
            if (parent.CompareTo(child) < 0)
            {
                heapList[idx] = child;
                heapList[largerChildIdx] = parent;
                idx = largerChildIdx;
            }
            else
            {
                break;
            }
        }
        Console.WriteLine("HEAP RESTORED!");
    }
    
    private int GetLargerChildIdx(int idx)
    {
        if (RightChildIdx(idx) > count)
        {
            Console.WriteLine("There is only a left child");
            return LeftChildIdx(idx);
        }
        else
        {
            T leftChild = heapList[LeftChildIdx(idx)];
            T rightChild = heapList[RightChildIdx(idx)];
            
            if (leftChild.CompareTo(rightChild) > 0)
            {
                Console.WriteLine($"Left child {leftChild} is larger than right child {rightChild}");
                return LeftChildIdx(idx);
            }
            else
            {
                Console.WriteLine($"Right child {rightChild} is larger than left child {leftChild}");
                return RightChildIdx(idx);
            }
        }
    }
    
    public int Count => count;
}

// Example usage
var heap = new MaxHeap<int>();
heap.Add(10);
heap.Add(5);
heap.Add(15);
heap.Add(20);
Console.WriteLine($"Max value: {heap.RetrieveMax()}");
```

## Recursion

**Key Concepts:**
- Function calls itself with modified parameters
- Must have base case to prevent infinite recursion
- Useful for problems with self-similar subproblems
- Uses call stack to track function calls

**Essential Components:**
1. **Base Case**: Condition that stops recursion
2. **Recursive Case**: Function calls itself with simpler input

### Python Implementation

```python
def fibonacci(n):
    # Base case
    if n <= 1:
        print("Base case reached")
        return n
    
    # Recursive case
    print(f"Computing fibonacci({n-2}) + fibonacci({n-1})")
    return fibonacci(n - 2) + fibonacci(n - 1)

# Example usage
print(f"Fibonacci(8) = {fibonacci(8)}")

# More efficient version with memoization
def fibonacci_memo(n, memo={}):
    if n in memo:
        return memo[n]
    
    if n <= 1:
        return n
    
    memo[n] = fibonacci_memo(n-1, memo) + fibonacci_memo(n-2, memo)
    return memo[n]

print(f"Fibonacci(8) with memoization = {fibonacci_memo(8)}")
```

### C# Implementation

```csharp
public static class Recursion
{
    public static int Fibonacci(int n)
    {
        // Base case
        if (n <= 1)
        {
            Console.WriteLine("Base case reached");
            return n;
        }
        
        // Recursive case
        Console.WriteLine($"Computing fibonacci({n-2}) + fibonacci({n-1})");
        return Fibonacci(n - 2) + Fibonacci(n - 1);
    }
    
    // More efficient version with memoization
    public static int FibonacciMemo(int n, Dictionary<int, int> memo = null)
    {
        if (memo == null)
            memo = new Dictionary<int, int>();
            
        if (memo.ContainsKey(n))
            return memo[n];
        
        if (n <= 1)
            return n;
        
        memo[n] = FibonacciMemo(n - 1, memo) + FibonacciMemo(n - 2, memo);
        return memo[n];
    }
}

// Example usage
Console.WriteLine($"Fibonacci(8) = {Recursion.Fibonacci(8)}");
Console.WriteLine($"Fibonacci(8) with memoization = {Recursion.FibonacciMemo(8)}");
```

## Graphs

**Key Concepts:**
- Collection of vertices (nodes) connected by edges
- Can be directed or undirected, weighted or unweighted
- Used to model networks, relationships, maps

**Graph Types:**
- **Undirected**: Edges have no direction
- **Directed**: Edges have direction (digraph)
- **Weighted**: Edges have associated costs/weights
- **Unweighted**: All edges have equal weight

### Graph Representation

#### Adjacency List
- Array/list of lists representing neighbors
- Efficient for sparse graphs
- Good for finding adjacent vertices

#### Adjacency Matrix
- 2D array where cell [i][j] represents edge between vertices i and j
- Suitable for dense graphs
- Fast edge existence checks

### Python Implementation

```python
class Vertex:
    def __init__(self, value):
        self.value = value
        self.edges = {}
    
    def add_edge(self, vertex, weight=0):
        self.edges[vertex] = weight
    
    def get_edges(self):
        return list(self.edges.keys())

class Graph:
    def __init__(self, directed=False):
        self.graph_dict = {}
        self.directed = directed
    
    def add_vertex(self, vertex):
        self.graph_dict[vertex.value] = vertex
    
    def add_edge(self, from_vertex, to_vertex, weight=0):
        self.graph_dict[from_vertex.value].add_edge(to_vertex.value, weight)
        if not self.directed:
            self.graph_dict[to_vertex.value].add_edge(from_vertex.value, weight)
    
    def find_path(self, start_vertex, end_vertex):
        queue = [start_vertex]
        visited = set()
        
        while queue:
            current_vertex = queue.pop(0)
            visited.add(current_vertex)
            print(f"Visiting {current_vertex}")
            
            if current_vertex == end_vertex:
                return True
            
            neighbors = set(self.graph_dict[current_vertex].edges.keys())
            queue.extend([vertex for vertex in neighbors if vertex not in visited])
        
        return False
```

### C# Implementation

```csharp
public class Vertex<T>
{
    public T Value { get; set; }
    public Dictionary<T, int> Edges { get; set; }
    
    public Vertex(T value)
    {
        Value = value;
        Edges = new Dictionary<T, int>();
    }
    
    public void AddEdge(T vertex, int weight = 0)
    {
        Edges[vertex] = weight;
    }
    
    public List<T> GetEdges()
    {
        return Edges.Keys.ToList();
    }
}

public class Graph<T>
{
    private Dictionary<T, Vertex<T>> graphDict;
    private bool directed;
    
    public Graph(bool directed = false)
    {
        graphDict = new Dictionary<T, Vertex<T>>();
        this.directed = directed;
    }
    
    public void AddVertex(Vertex<T> vertex)
    {
        graphDict[vertex.Value] = vertex;
    }
    
    public void AddEdge(Vertex<T> fromVertex, Vertex<T> toVertex, int weight = 0)
    {
        graphDict[fromVertex.Value].AddEdge(toVertex.Value, weight);
        if (!directed)
        {
            graphDict[toVertex.Value].AddEdge(fromVertex.Value, weight);
        }
    }
    
    public bool FindPath(T startVertex, T endVertex)
    {
        var queue = new Queue<T>();
        var visited = new HashSet<T>();
        
        queue.Enqueue(startVertex);
        
        while (queue.Count > 0)
        {
            T currentVertex = queue.Dequeue();
            visited.Add(currentVertex);
            Console.WriteLine($"Visiting {currentVertex}");
            
            if (currentVertex.Equals(endVertex))
            {
                return true;
            }
            
            var neighbors = graphDict[currentVertex].Edges.Keys;
            foreach (var neighbor in neighbors)
            {
                if (!visited.Contains(neighbor))
                {
                    queue.Enqueue(neighbor);
                }
            }
        }
        
        return false;
    }
}
```

## Graph Search Algorithms

### Depth-First Search (DFS)

**Key Concepts:**
- Explores as deeply as possible before backtracking
- Uses stack (implicit via recursion or explicit)
- Good for finding connected components, cycle detection

**Time Complexity:** O(V + E) where V = vertices, E = edges

#### Python Implementation

```python
def dfs(graph, current_vertex, target_value, visited=None):
    if visited is None:
        visited = []
    
    visited.append(current_vertex)
    print(f"Visiting {current_vertex}")
    
    if current_vertex == target_value:
        return visited
    
    for neighbor in graph[current_vertex]:
        if neighbor not in visited:
            path = dfs(graph, neighbor, target_value, visited.copy())
            if path:
                return path
    
    return None

# Example usage
graph = {
    'A': ['B', 'C'],
    'B': ['D', 'E'],
    'C': ['F'],
    'D': [],
    'E': ['F'],
    'F': []
}

path = dfs(graph, 'A', 'F')
print(f"Path found: {path}")
```

#### C# Implementation

```csharp
public static class GraphSearch
{
    public static List<T> DFS<T>(Dictionary<T, List<T>> graph, T currentVertex, T targetValue, HashSet<T> visited = null)
    {
        if (visited == null)
            visited = new HashSet<T>();
        
        visited.Add(currentVertex);
        Console.WriteLine($"Visiting {currentVertex}");
        
        if (currentVertex.Equals(targetValue))
        {
            return new List<T>(visited);
        }
        
        if (graph.ContainsKey(currentVertex))
        {
            foreach (var neighbor in graph[currentVertex])
            {
                if (!visited.Contains(neighbor))
                {
                    var newVisited = new HashSet<T>(visited);
                    var path = DFS(graph, neighbor, targetValue, newVisited);
                    if (path != null)
                    {
                        return path;
                    }
                }
            }
        }
        
        return null;
    }
}

// Example usage
var graph = new Dictionary<string, List<string>>
{
    {"A", new List<string> {"B", "C"}},
    {"B", new List<string> {"D", "E"}},
    {"C", new List<string> {"F"}},
    {"D", new List<string>()},
    {"E", new List<string> {"F"}},
    {"F", new List<string>()}
};

var path = GraphSearch.DFS(graph, "A", "F");
Console.WriteLine($"Path found: [{string.Join(", ", path ?? new List<string>())}]");
```

### Breadth-First Search (BFS)

**Key Concepts:**
- Explores level by level from starting vertex
- Uses queue for processing
- Finds shortest path in unweighted graphs

**Time Complexity:** O(V + E)

#### Python Implementation

```python
from collections import deque

def bfs(graph, start_vertex, target_value):
    queue = deque([(start_vertex, [start_vertex])])
    visited = set()
    
    while queue:
        current_vertex, path = queue.popleft()
        
        if current_vertex in visited:
            continue
            
        visited.add(current_vertex)
        print(f"Visiting {current_vertex}")
        
        if current_vertex == target_value:
            return path
        
        for neighbor in graph[current_vertex]:
            if neighbor not in visited:
                new_path = path + [neighbor]
                queue.append((neighbor, new_path))
    
    return None

# Example usage
path = bfs(graph, 'A', 'F')
print(f"Shortest path: {path}")
```

#### C# Implementation

```csharp
public static List<T> BFS<T>(Dictionary<T, List<T>> graph, T startVertex, T targetValue)
{
    var queue = new Queue<(T vertex, List<T> path)>();
    var visited = new HashSet<T>();
    
    queue.Enqueue((startVertex, new List<T> { startVertex }));
    
    while (queue.Count > 0)
    {
        var (currentVertex, path) = queue.Dequeue();
        
        if (visited.Contains(currentVertex))
            continue;
            
        visited.Add(currentVertex);
        Console.WriteLine($"Visiting {currentVertex}");
        
        if (currentVertex.Equals(targetValue))
        {
            return path;
        }
        
        if (graph.ContainsKey(currentVertex))
        {
            foreach (var neighbor in graph[currentVertex])
            {
                if (!visited.Contains(neighbor))
                {
                    var newPath = new List<T>(path) { neighbor };
                    queue.Enqueue((neighbor, newPath));
                }
            }
        }
    }
    
    return null;
}

// Example usage
var shortestPath = GraphSearch.BFS(graph, "A", "F");
Console.WriteLine($"Shortest path: [{string.Join(", ", shortestPath ?? new List<string>())}]");
```

## Advanced Algorithms

### Dijkstra's Algorithm

**Key Concepts:**
- Finds shortest path from source to all vertices in weighted graph
- Uses greedy approach with priority queue
- Cannot handle negative edge weights

**Time Complexity:** O((V + E) log V) with binary heap

#### Python Implementation

```python
import heapq
from math import inf

def dijkstra(graph, start):
    distances = {vertex: inf for vertex in graph}
    distances[start] = 0
    
    priority_queue = [(0, start)]
    visited = set()
    
    while priority_queue:
        current_distance, current_vertex = heapq.heappop(priority_queue)
        
        if current_vertex in visited:
            continue
            
        visited.add(current_vertex)
        
        for neighbor, weight in graph[current_vertex]:
            distance = current_distance + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(priority_queue, (distance, neighbor))
    
    return distances

# Example usage
weighted_graph = {
    'A': [('B', 4), ('C', 2)],
    'B': [('C', 1), ('D', 5)],
    'C': [('D', 8), ('E', 10)],
    'D': [('E', 2)],
    'E': []
}

distances = dijkstra(weighted_graph, 'A')
print(f"Shortest distances from A: {distances}")
```

#### C# Implementation

```csharp
public static class Dijkstra
{
    public static Dictionary<T, double> FindShortestPaths<T>(
        Dictionary<T, List<(T neighbor, double weight)>> graph, T start)
    {
        var distances = new Dictionary<T, double>();
        var priorityQueue = new SortedSet<(double distance, T vertex)>();
        var visited = new HashSet<T>();
        
        // Initialize distances
        foreach (var vertex in graph.Keys)
        {
            distances[vertex] = double.PositiveInfinity;
        }
        distances[start] = 0;
        
        priorityQueue.Add((0, start));
        
        while (priorityQueue.Count > 0)
        {
            var (currentDistance, currentVertex) = priorityQueue.Min;
            priorityQueue.Remove(priorityQueue.Min);
            
            if (visited.Contains(currentVertex))
                continue;
                
            visited.Add(currentVertex);
            
            if (graph.ContainsKey(currentVertex))
            {
                foreach (var (neighbor, weight) in graph[currentVertex])
                {
                    double distance = currentDistance + weight;
                    
                    if (distance < distances[neighbor])
                    {
                        distances[neighbor] = distance;
                        priorityQueue.Add((distance, neighbor));
                    }
                }
            }
        }
        
        return distances;
    }
}

// Example usage
var weightedGraph = new Dictionary<string, List<(string, double)>>
{
    {"A", new List<(string, double)> {("B", 4), ("C", 2)}},
    {"B", new List<(string, double)> {("C", 1), ("D", 5)}},
    {"C", new List<(string, double)> {("D", 8), ("E", 10)}},
    {"D", new List<(string, double)> {("E", 2)}},
    {"E", new List<(string, double)>()}
};

var distances = Dijkstra.FindShortestPaths(weightedGraph, "A");
foreach (var kvp in distances)
{
    Console.WriteLine($"Distance to {kvp.Key}: {kvp.Value}");
}
```

### A* Pathfinding

**Key Concepts:**
- Extension of Dijkstra's with heuristic function
- Uses f(n) = g(n) + h(n) where:
  - g(n) = actual cost from start to n
  - h(n) = heuristic estimate from n to goal
- More efficient than Dijkstra for single target searches

#### Python Implementation

```python
import heapq
from math import sqrt

def manhattan_distance(pos1, pos2):
    return abs(pos1[0] - pos2[0]) + abs(pos1[1] - pos2[1])

def euclidean_distance(pos1, pos2):
    return sqrt((pos1[0] - pos2[0])**2 + (pos1[1] - pos2[1])**2)

def a_star(graph, start, goal, positions, heuristic=manhattan_distance):
    open_set = [(0, start)]
    came_from = {}
    g_score = {node: float('inf') for node in graph}
    g_score[start] = 0
    f_score = {node: float('inf') for node in graph}
    f_score[start] = heuristic(positions[start], positions[goal])
    
    while open_set:
        current = heapq.heappop(open_set)[1]
        
        if current == goal:
            path = []
            while current in came_from:
                path.append(current)
                current = came_from[current]
            path.append(start)
            return path[::-1]
        
        for neighbor, weight in graph[current]:
            tentative_g_score = g_score[current] + weight
            
            if tentative_g_score < g_score[neighbor]:
                came_from[neighbor] = current
                g_score[neighbor] = tentative_g_score
                f_score[neighbor] = g_score[neighbor] + heuristic(positions[neighbor], positions[goal])
                heapq.heappush(open_set, (f_score[neighbor], neighbor))
    
    return None

# Example usage with grid positions
grid_graph = {
    'A': [('B', 1), ('C', 1)],
    'B': [('A', 1), ('D', 1)],
    'C': [('A', 1), ('D', 1)],
    'D': [('B', 1), ('C', 1)]
}

positions = {
    'A': (0, 0),
    'B': (1, 0),
    'C': (0, 1),
    'D': (1, 1)
}

path = a_star(grid_graph, 'A', 'D', positions)
print(f"A* path from A to D: {path}")
```

#### C# Implementation

```csharp
public static class AStar
{
    public static double ManhattanDistance((double x, double y) pos1, (double x, double y) pos2)
    {
        return Math.Abs(pos1.x - pos2.x) + Math.Abs(pos1.y - pos2.y);
    }
    
    public static double EuclideanDistance((double x, double y) pos1, (double x, double y) pos2)
    {
        return Math.Sqrt(Math.Pow(pos1.x - pos2.x, 2) + Math.Pow(pos1.y - pos2.y, 2));
    }
    
    public static List<T> FindPath<T>(
        Dictionary<T, List<(T neighbor, double weight)>> graph,
        T start,
        T goal,
        Dictionary<T, (double x, double y)> positions,
        Func<(double, double), (double, double), double> heuristic = null)
    {
        if (heuristic == null)
            heuristic = ManhattanDistance;
            
        var openSet = new SortedSet<(double fScore, T node)>();
        var cameFrom = new Dictionary<T, T>();
        var gScore = new Dictionary<T, double>();
        var fScore = new Dictionary<T, double>();
        
        // Initialize scores
        foreach (var node in graph.Keys)
        {
            gScore[node] = double.PositiveInfinity;
            fScore[node] = double.PositiveInfinity;
        }
        
        gScore[start] = 0;
        fScore[start] = heuristic(positions[start], positions[goal]);
        openSet.Add((fScore[start], start));
        
        while (openSet.Count > 0)
        {
            var current = openSet.Min.node;
            openSet.Remove(openSet.Min);
            
            if (current.Equals(goal))
            {
                // Reconstruct path
                var path = new List<T>();
                var temp = current;
                while (cameFrom.ContainsKey(temp))
                {
                    path.Add(temp);
                    temp = cameFrom[temp];
                }
                path.Add(start);
                path.Reverse();
                return path;
            }
            
            if (graph.ContainsKey(current))
            {
                foreach (var (neighbor, weight) in graph[current])
                {
                    double tentativeGScore = gScore[current] + weight;
                    
                    if (tentativeGScore < gScore[neighbor])
                    {
                        cameFrom[neighbor] = current;
                        gScore[neighbor] = tentativeGScore;
                        fScore[neighbor] = gScore[neighbor] + heuristic(positions[neighbor], positions[goal]);
                        
                        openSet.Add((fScore[neighbor], neighbor));
                    }
                }
            }
        }
        
        return null; // No path found
    }
}

// Example usage
var gridGraph = new Dictionary<string, List<(string, double)>>
{
    {"A", new List<(string, double)> {("B", 1), ("C", 1)}},
    {"B", new List<(string, double)> {("A", 1), ("D", 1)}},
    {"C", new List<(string, double)> {("A", 1), ("D", 1)}},
    {"D", new List<(string, double)> {("B", 1), ("C", 1)}}
};

var positions = new Dictionary<string, (double, double)>
{
    {"A", (0, 0)},
    {"B", (1, 0)},
    {"C", (0, 1)},
    {"D", (1, 1)}
};

var path = AStar.FindPath(gridGraph, "A", "D", positions);
Console.WriteLine($"A* path from A to D: [{string.Join(", ", path ?? new List<string>())}]");
```

## Pattern Matching

### Naive Pattern Search

**Key Concepts:**
- Simple brute-force string matching
- Check every position in text for pattern match
- Not efficient but easy to understand

**Time Complexity:** O(nm) where n = text length, m = pattern length

#### Python Implementation

```python
def naive_pattern_search(text, pattern):
    matches = []
    print(f"Input Text: {text}, Input Pattern: {pattern}")
    
    for i in range(len(text) - len(pattern) + 1):
        print(f"Text Index: {i}")
        match_count = 0
        
        for j in range(len(pattern)):
            print(f"Pattern Index: {j}")
            if i + j < len(text) and pattern[j] == text[i + j]:
                match_count += 1
            else:
                break
        
        if match_count == len(pattern):
            print(f"{pattern} found at index {i}")
            matches.append(i)
    
    return matches

# Example usage
text = "HAYHAYNEEDLEHAYHAYHAYNEEDLEHAYHAYHAYHAYNEEDLE"
pattern = "NEEDLE"
matches = naive_pattern_search(text, pattern)
print(f"Pattern found at positions: {matches}")
```

#### C# Implementation

```csharp
public static class PatternSearch
{
    public static List<int> NaiveSearch(string text, string pattern)
    {
        var matches = new List<int>();
        Console.WriteLine($"Input Text: {text}, Input Pattern: {pattern}");
        
        for (int i = 0; i <= text.Length - pattern.Length; i++)
        {
            Console.WriteLine($"Text Index: {i}");
            int matchCount = 0;
            
            for (int j = 0; j < pattern.Length; j++)
            {
                Console.WriteLine($"Pattern Index: {j}");
                if (pattern[j] == text[i + j])
                {
                    matchCount++;
                }
                else
                {
                    break;
                }
            }
            
            if (matchCount == pattern.Length)
            {
                Console.WriteLine($"{pattern} found at index {i}");
                matches.Add(i);
            }
        }
        
        return matches;
    }
}

// Example usage
string text = "HAYHAYNEEDLEHAYHAYHAYNEEDLEHAYHAYHAYHAYNEEDLE";
string pattern = "NEEDLE";
var matches = PatternSearch.NaiveSearch(text, pattern);
Console.WriteLine($"Pattern found at positions: [{string.Join(", ", matches)}]");
```

## Time Complexity Summary

| Algorithm | Best Case | Average Case | Worst Case | Space Complexity |
|-----------|-----------|--------------|------------|------------------|
| **Bubble Sort** | O(n) | O(n²) | O(n²) | O(1) |
| **Merge Sort** | O(n log n) | O(n log n) | O(n log n) | O(n) |
| **Quick Sort** | O(n log n) | O(n log n) | O(n²) | O(log n) |
| **Heap Sort** | O(n log n) | O(n log n) | O(n log n) | O(1) |
| **Linear Search** | O(1) | O(n) | O(n) | O(1) |
| **Binary Search** | O(1) | O(log n) | O(log n) | O(1) |
| **DFS/BFS** | O(V + E) | O(V + E) | O(V + E) | O(V) |
| **Dijkstra** | O((V + E) log V) | O((V + E) log V) | O((V + E) log V) | O(V) |

## Key Data Structure Operations

| Data Structure | Access | Search | Insertion | Deletion | Space |
|----------------|--------|--------|-----------|----------|-------|
| **Array** | O(1) | O(n) | O(n) | O(n) | O(n) |
| **Linked List** | O(n) | O(n) | O(1) | O(1) | O(n) |
| **Stack** | O(n) | O(n) | O(1) | O(1) | O(n) |
| **Queue** | O(n) | O(n) | O(1) | O(1) | O(n) |
| **Hash Table** | N/A | O(1)* | O(1)* | O(1)* | O(n) |
| **Binary Search Tree** | O(log n)* | O(log n)* | O(log n)* | O(log n)* | O(n) |
| **Heap** | N/A | O(n) | O(log n) | O(log n) | O(n) |

*Average case. Worst case can be O(n) for hash tables and BST.

## Study Tips

1. **Practice Implementation**: Write code for each data structure and algorithm from scratch
2. **Understand Trade-offs**: Know when to use each data structure based on requirements
3. **Visualize**: Draw diagrams to understand how algorithms work step-by-step
4. **Analyze Complexity**: Always consider both time and space complexity
5. **Real-world Applications**: Connect concepts to practical problems
6. **Edge Cases**: Consider empty inputs, single elements, and boundary conditions
7. **Optimization**: Learn both naive and optimized versions of algorithms