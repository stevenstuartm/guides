# Heaps & Priority Queues

## Why Heaps Exist
Efficiently maintain the minimum or maximum element while allowing dynamic insertions and deletions. Heaps solve the problem of quickly accessing the "most important" element in a changing dataset without maintaining full sorted order.

## When to Use Heaps

**Use when:**
- Need to repeatedly find/remove min or max element
- Implementing priority queues (task scheduling, algorithms)
- Finding top-K elements in large datasets
- Merging multiple sorted sequences
- Graph algorithms (Dijkstra's, Prim's)
- Streaming data where you need extremes

**Don't use when:**
- Need to search for arbitrary elements (use hash table)
- Need sorted iteration through all elements (use balanced BST)
- Simple min/max of static data (just iterate once)
- Need to access elements by index (use array)

**Modern reality:** Use built-in priority queues like Python's `heapq`, C#'s `PriorityQueue<T,P>`. Implement heaps in interviews to show understanding.

---

## Heap Properties

### Complete Binary Tree Structure
- All levels filled except possibly the last
- Last level filled left-to-right
- Height is always O(log n)
- Can be efficiently represented as an array

### Heap Order Property
**Min-Heap:** Parent ≤ children (root is minimum)
**Max-Heap:** Parent ≥ children (root is maximum)

### Array Representation
For element at index `i`:
- **Parent:** `(i - 1) // 2`
- **Left child:** `2 * i + 1` 
- **Right child:** `2 * i + 2`

```
Array: [1, 3, 6, 5, 9, 8]
Tree:       1
          /   \
         3     6
        / \   /
       5   9 8
```

---

## Time Complexity

| Operation | Binary Heap | Notes |
|-----------|-------------|-------|
| **Insert** | O(log n) | Bubble up to maintain heap property |
| **Extract Min/Max** | O(log n) | Remove root, bubble down |
| **Peek Min/Max** | O(1) | Root element |
| **Delete arbitrary** | O(log n) | Find + bubble down |
| **Build from array** | O(n) | Bottom-up heapify |
| **Search** | O(n) | No ordering except parent-child |

---

## Min-Heap Implementation

### Python Implementation
```python
class MinHeap:
    def __init__(self):
        self.heap = []
    
    def parent(self, i):
        return (i - 1) // 2
    
    def left_child(self, i):
        return 2 * i + 1
    
    def right_child(self, i):
        return 2 * i + 2
    
    def swap(self, i, j):
        self.heap[i], self.heap[j] = self.heap[j], self.heap[i]
    
    def insert(self, value):
        """Insert new element and maintain heap property"""
        self.heap.append(value)
        self._bubble_up(len(self.heap) - 1)
        print(f"Inserted {value}: {self.heap}")
    
    def _bubble_up(self, index):
        """Move element up until heap property is satisfied"""
        while index > 0:
            parent_index = self.parent(index)
            
            # If heap property is satisfied, stop
            if self.heap[parent_index] <= self.heap[index]:
                break
            
            print(f"Bubbling up: swap {self.heap[index]} with parent {self.heap[parent_index]}")
            self.swap(index, parent_index)
            index = parent_index
    
    def extract_min(self):
        """Remove and return minimum element"""
        if not self.heap:
            return None
        
        if len(self.heap) == 1:
            return self.heap.pop()
        
        # Save min value
        min_val = self.heap[0]
        
        # Move last element to root
        self.heap[0] = self.heap.pop()
        print(f"After moving last to root: {self.heap}")
        
        # Restore heap property
        self._bubble_down(0)
        
        print(f"Extracted {min_val}, heap is now: {self.heap}")
        return min_val
    
    def _bubble_down(self, index):
        """Move element down until heap property is satisfied"""
        while True:
            min_index = index
            left = self.left_child(index)
            right = self.right_child(index)
            
            # Find smallest among node and its children
            if left < len(self.heap) and self.heap[left] < self.heap[min_index]:
                min_index = left
            
            if right < len(self.heap) and self.heap[right] < self.heap[min_index]:
                min_index = right
            
            # If no swap needed, heap property is satisfied
            if min_index == index:
                break
            
            print(f"Bubbling down: swap {self.heap[index]} with {self.heap[min_index]}")
            self.swap(index, min_index)
            index = min_index
    
    def peek(self):
        """Return minimum element without removing it"""
        return self.heap[0] if self.heap else None
    
    def size(self):
        return len(self.heap)
    
    def is_empty(self):
        return len(self.heap) == 0
    
    def __str__(self):
        return f"MinHeap({self.heap})"

# Example usage
print("=== Min Heap Demo ===")
heap = MinHeap()

# Insert elements
values = [10, 5, 20, 3, 8, 15]
for val in values:
    heap.insert(val)

print(f"\nFinal heap: {heap}")
print(f"Minimum element: {heap.peek()}")

# Extract elements
print("\nExtracting elements in sorted order:")
while not heap.is_empty():
    print(f"Extracted: {heap.extract_min()}")
```

### C# Implementation
```csharp
public class MinHeap<T> where T : IComparable<T>
{
    private List<T> heap;
    
    public MinHeap()
    {
        heap = new List<T>();
    }
    
    private int Parent(int i) => (i - 1) / 2;
    private int LeftChild(int i) => 2 * i + 1;
    private int RightChild(int i) => 2 * i + 2;
    
    private void Swap(int i, int j)
    {
        T temp = heap[i];
        heap[i] = heap[j];
        heap[j] = temp;
    }
    
    public void Insert(T value)
    {
        heap.Add(value);
        BubbleUp(heap.Count - 1);
        Console.WriteLine($"Inserted {value}: [{string.Join(", ", heap)}]");
    }
    
    private void BubbleUp(int index)
    {
        while (index > 0)
        {
            int parentIndex = Parent(index);
            
            // If heap property is satisfied, stop
            if (heap[parentIndex].CompareTo(heap[index]) <= 0)
                break;
            
            Console.WriteLine($"Bubbling up: swap {heap[index]} with parent {heap[parentIndex]}");
            Swap(index, parentIndex);
            index = parentIndex;
        }
    }
    
    public T ExtractMin()
    {
        if (heap.Count == 0)
            throw new InvalidOperationException("Heap is empty");
        
        if (heap.Count == 1)
            return heap[0];
        
        // Save min value
        T minVal = heap[0];
        
        // Move last element to root
        heap[0] = heap[heap.Count - 1];
        heap.RemoveAt(heap.Count - 1);
        Console.WriteLine($"After moving last to root: [{string.Join(", ", heap)}]");
        
        // Restore heap property
        if (heap.Count > 0)
            BubbleDown(0);
        
        Console.WriteLine($"Extracted {minVal}, heap is now: [{string.Join(", ", heap)}]");
        return minVal;
    }
    
    private void BubbleDown(int index)
    {
        while (true)
        {
            int minIndex = index;
            int left = LeftChild(index);
            int right = RightChild(index);
            
            // Find smallest among node and its children
            if (left < heap.Count && heap[left].CompareTo(heap[minIndex]) < 0)
                minIndex = left;
            
            if (right < heap.Count && heap[right].CompareTo(heap[minIndex]) < 0)
                minIndex = right;
            
            // If no swap needed, heap property is satisfied
            if (minIndex == index)
                break;
            
            Console.WriteLine($"Bubbling down: swap {heap[index]} with {heap[minIndex]}");
            Swap(index, minIndex);
            index = minIndex;
        }
    }
    
    public T Peek()
    {
        if (heap.Count == 0)
            throw new InvalidOperationException("Heap is empty");
        return heap[0];
    }
    
    public int Count => heap.Count;
    public bool IsEmpty => heap.Count == 0;
    
    public override string ToString()
    {
        return $"MinHeap([{string.Join(", ", heap)}])";
    }
}

// Example usage
Console.WriteLine("=== Min Heap Demo ===");
var heap = new MinHeap<int>();

// Insert elements
int[] values = {10, 5, 20, 3, 8, 15};
foreach (var val in values)
{
    heap.Insert(val);
}

Console.WriteLine($"\nFinal heap: {heap}");
Console.WriteLine($"Minimum element: {heap.Peek()}");

// Extract elements
Console.WriteLine("\nExtracting elements in sorted order:");
while (!heap.IsEmpty)
{
    Console.WriteLine($"Extracted: {heap.ExtractMin()}");
}
```

---

## Building Heap from Array (O(n) Algorithm)

### Bottom-Up Heapify
```python
def build_min_heap(arr):
    """Build min heap from array in O(n) time"""
    heap = arr[:]  # Make copy
    n = len(heap)
    
    # Start from last non-leaf node and heapify down
    for i in range((n - 2) // 2, -1, -1):
        _heapify_down(heap, i, n)
        print(f"After heapifying index {i}: {heap}")
    
    return heap

def _heapify_down(heap, index, heap_size):
    """Helper function for heapify down"""
    while True:
        min_index = index
        left = 2 * index + 1
        right = 2 * index + 2
        
        if left < heap_size and heap[left] < heap[min_index]:
            min_index = left
        
        if right < heap_size and heap[right] < heap[min_index]:
            min_index = right
        
        if min_index == index:
            break
        
        heap[index], heap[min_index] = heap[min_index], heap[index]
        index = min_index

# Example usage
array = [20, 15, 8, 10, 5, 7, 6, 2, 9, 1]
print(f"Original array: {array}")
heap = build_min_heap(array)
print(f"Min heap: {heap}")
```

### Why O(n) and not O(n log n)?
- Most nodes are near the bottom (leaves need no work)
- Nodes at height h: at most ⌈n/2^(h+1)⌉
- Work at height h: at most h operations
- Total work: Σ(h × n/2^(h+1)) = O(n)

---

## Priority Queue Implementation

### Priority Queue with Custom Comparison
```python
import heapq
from dataclasses import dataclass
from typing import Any

@dataclass
class PriorityItem:
    priority: int
    item: Any
    
    def __lt__(self, other):
        return self.priority < other.priority

class PriorityQueue:
    def __init__(self):
        self._queue = []
        self._index = 0  # For tie-breaking
    
    def push(self, item, priority):
        """Add item with given priority"""
        heapq.heappush(self._queue, (priority, self._index, item))
        self._index += 1
        print(f"Added {item} with priority {priority}")
    
    def pop(self):
        """Remove and return highest priority item"""
        if self._queue:
            priority, _, item = heapq.heappop(self._queue)
            print(f"Removed {item} with priority {priority}")
            return item
        raise IndexError("Priority queue is empty")
    
    def peek(self):
        """Return highest priority item without removing"""
        if self._queue:
            return self._queue[0][2]
        return None
    
    def is_empty(self):
        return len(self._queue) == 0
    
    def size(self):
        return len(self._queue)

# Example: Task scheduling
pq = PriorityQueue()
pq.push("Low priority task", 3)
pq.push("High priority task", 1)
pq.push("Medium priority task", 2)
pq.push("Another high priority", 1)

print("\nProcessing tasks by priority:")
while not pq.is_empty():
    task = pq.pop()
    print(f"Processing: {task}")
```

### C# Priority Queue (.NET 6+)
```csharp
// Using built-in PriorityQueue (available in .NET 6+)
public static void DemonstratePriorityQueue()
{
    var pq = new PriorityQueue<string, int>();
    
    // Add items (lower number = higher priority)
    pq.Enqueue("Low priority task", 3);
    pq.Enqueue("High priority task", 1);
    pq.Enqueue("Medium priority task", 2);
    pq.Enqueue("Another high priority", 1);
    
    Console.WriteLine("Processing tasks by priority:");
    while (pq.Count > 0)
    {
        var task = pq.Dequeue();
        Console.WriteLine($"Processing: {task}");
    }
}

// Custom priority queue for older .NET versions
public class CustomPriorityQueue<T> where T : IComparable<T>
{
    private List<(T item, int priority)> heap;
    
    public CustomPriorityQueue()
    {
        heap = new List<(T, int)>();
    }
    
    public void Enqueue(T item, int priority)
    {
        heap.Add((item, priority));
        BubbleUp(heap.Count - 1);
    }
    
    public T Dequeue()
    {
        if (heap.Count == 0)
            throw new InvalidOperationException("Queue is empty");
        
        var result = heap[0].item;
        
        // Move last to first and bubble down
        heap[0] = heap[heap.Count - 1];
        heap.RemoveAt(heap.Count - 1);
        
        if (heap.Count > 0)
            BubbleDown(0);
        
        return result;
    }
    
    private void BubbleUp(int index)
    {
        while (index > 0)
        {
            int parentIndex = (index - 1) / 2;
            
            if (heap[parentIndex].priority <= heap[index].priority)
                break;
            
            (heap[index], heap[parentIndex]) = (heap[parentIndex], heap[index]);
            index = parentIndex;
        }
    }
    
    private void BubbleDown(int index)
    {
        while (true)
        {
            int minIndex = index;
            int left = 2 * index + 1;
            int right = 2 * index + 2;
            
            if (left < heap.Count && heap[left].priority < heap[minIndex].priority)
                minIndex = left;
            
            if (right < heap.Count && heap[right].priority < heap[minIndex].priority)
                minIndex = right;
            
            if (minIndex == index)
                break;
            
            (heap[index], heap[minIndex]) = (heap[minIndex], heap[index]);
            index = minIndex;
        }
    }
    
    public int Count => heap.Count;
    public bool IsEmpty => heap.Count == 0;
}
```

---

## Heap Applications

### 1. Top-K Elements
```python
import heapq

def find_k_largest(nums, k):
    """Find k largest elements using min-heap"""
    if k >= len(nums):
        return sorted(nums, reverse=True)
    
    # Use min-heap of size k
    heap = []
    
    for num in nums:
        if len(heap) < k:
            heapq.heappush(heap, num)
        elif num > heap[0]:
            heapq.heappushpop(heap, num)  # Push and pop in one operation
    
    return sorted(heap, reverse=True)

def find_k_smallest(nums, k):
    """Find k smallest elements using max-heap"""
    # Python heapq is min-heap, so negate values for max-heap
    heap = []
    
    for num in nums:
        if len(heap) < k:
            heapq.heappush(heap, -num)  # Negate for max-heap behavior
        elif num < -heap[0]:  # num < max element in heap
            heapq.heappushpop(heap, -num)
    
    return sorted([-x for x in heap])

# Example usage
numbers = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]
print(f"Original: {numbers}")
print(f"3 largest: {find_k_largest(numbers, 3)}")
print(f"3 smallest: {find_k_smallest(numbers, 3)}")
```

### 2. Merge K Sorted Arrays
```python
import heapq

def merge_k_sorted_arrays(arrays):
    """Merge k sorted arrays using heap"""
    heap = []
    result = []
    
    # Add first element from each array to heap
    for i, arr in enumerate(arrays):
        if arr:  # Skip empty arrays
            heapq.heappush(heap, (arr[0], i, 0))  # (value, array_index, element_index)
    
    while heap:
        value, arr_idx, elem_idx = heapq.heappop(heap)
        result.append(value)
        
        # Add next element from the same array
        if elem_idx + 1 < len(arrays[arr_idx]):
            next_value = arrays[arr_idx][elem_idx + 1]
            heapq.heappush(heap, (next_value, arr_idx, elem_idx + 1))
    
    return result

# Example usage
arrays = [
    [1, 4, 5],
    [1, 3, 4],
    [2, 6]
]
merged = merge_k_sorted_arrays(arrays)
print(f"Merged: {merged}")  # [1, 1, 2, 3, 4, 4, 5, 6]
```

### 3. Running Median
```python
import heapq

class MedianFinder:
    """Find median from data stream using two heaps"""
    def __init__(self):
        self.max_heap = []  # Left half (negated for max-heap)
        self.min_heap = []  # Right half
    
    def add_number(self, num):
        # Add to max_heap first
        heapq.heappush(self.max_heap, -num)
        
        # Move largest from max_heap to min_heap
        heapq.heappush(self.min_heap, -heapq.heappop(self.max_heap))
        
        # Balance heaps (max_heap can have at most one more element)
        if len(self.min_heap) > len(self.max_heap):
            heapq.heappush(self.max_heap, -heapq.heappop(self.min_heap))
    
    def find_median(self):
        if len(self.max_heap) > len(self.min_heap):
            return -self.max_heap[0]
        else:
            return (-self.max_heap[0] + self.min_heap[0]) / 2

# Example usage
median_finder = MedianFinder()
numbers = [1, 2, 3, 4, 5]

for num in numbers:
    median_finder.add_number(num)
    print(f"Added {num}, median: {median_finder.find_median()}")
```

---

## Interview Problems

### 1. Kth Largest Element
```python
import heapq

def find_kth_largest(nums, k):
    """Find kth largest element using heap"""
    # Method 1: Use min-heap of size k
    heap = []
    for num in nums:
        if len(heap) < k:
            heapq.heappush(heap, num)
        elif num > heap[0]:
            heapq.heappushpop(heap, num)
    
    return heap[0]

def find_kth_largest_quickselect(nums, k):
    """Alternative: Quick select algorithm"""
    def partition(left, right, pivot_index):
        pivot_value = nums[pivot_index]
        nums[pivot_index], nums[right] = nums[right], nums[pivot_index]
        
        store_index = left
        for i in range(left, right):
            if nums[i] > pivot_value:
                nums[store_index], nums[i] = nums[i], nums[store_index]
                store_index += 1
        
        nums[right], nums[store_index] = nums[store_index], nums[right]
        return store_index
    
    def select(left, right, k_smallest):
        if left == right:
            return nums[left]
        
        pivot_index = left + (right - left) // 2
        pivot_index = partition(left, right, pivot_index)
        
        if k_smallest == pivot_index:
            return nums[k_smallest]
        elif k_smallest < pivot_index:
            return select(left, pivot_index - 1, k_smallest)
        else:
            return select(pivot_index + 1, right, k_smallest)
    
    return select(0, len(nums) - 1, k - 1)

# Example usage
nums = [3, 2, 1, 5, 6, 4]
k = 2
print(f"Array: {nums}")
print(f"{k}th largest (heap): {find_kth_largest(nums, k)}")
print(f"{k}th largest (quickselect): {find_kth_largest_quickselect(nums[:], k)}")
```

### 2. Task Scheduler
```python
import heapq
from collections import Counter, deque

def least_interval(tasks, n):
    """Minimum intervals to execute all tasks with cooldown n"""
    if n == 0:
        return len(tasks)
    
    # Count frequency of each task
    task_counts = Counter(tasks)
    
    # Max heap of frequencies (negate for max-heap)
    heap = [-count for count in task_counts.values()]
    heapq.heapify(heap)
    
    # Queue to store (count, available_time)
    queue = deque()
    time = 0
    
    while heap or queue:
        time += 1
        
        # Add back available tasks from queue
        if queue and queue[0][1] == time:
            heapq.heappush(heap, queue.popleft()[0])
        
        # Execute most frequent available task
        if heap:
            count = heapq.heappop(heap)
            count += 1  # Reduce frequency (was negative)
            
            if count < 0:  # Still has remaining executions
                queue.append((count, time + n + 1))
    
    return time

# Example usage
tasks = ['A', 'A', 'A', 'B', 'B', 'B']
n = 2
result = least_interval(tasks, n)
print(f"Tasks: {tasks}, cooldown: {n}")
print(f"Minimum intervals: {result}")
```

---

## Modern Usage

**Python:** 
- Use `heapq` module for basic heap operations
- `queue.PriorityQueue` for thread-safe priority queue
- Build your own for custom comparisons

**C#:**
- Use `PriorityQueue<TElement, TPriority>` (.NET 6+)
- `SortedSet<T>` for ordered operations
- Custom implementation for older versions

**Key Takeaways:**
- Heaps are perfect for "find extremes repeatedly" problems
- Building heap from array is O(n), not O(n log n)
- Priority queues are heaps with application-specific semantics  
- Two-heap technique useful for streaming statistics
- Essential for graph algorithms and scheduling systems