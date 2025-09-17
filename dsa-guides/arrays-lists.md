# Arrays & Dynamic Arrays

## Why Arrays Exist
Direct memory access enables O(1) lookups by index. Arrays store elements in contiguous memory locations, providing the fastest possible access to data when you know the position. Dynamic arrays solve the fixed-size limitation while maintaining the performance benefits.

## When to Use Arrays

**Use when:**
- Need random access by index
- Cache performance matters (sequential memory access)
- Simple sequential processing
- Memory usage predictability is important
- Working with numerical computations

**Don't use when:**
- Frequent insertions/deletions in middle (use linked structures)
- Unknown maximum size with strict memory constraints
- Need complex relationships between elements (use graphs)

**Modern reality:** Your default choice for most problems. Use `list` in Python, `List<T>` in C#. These are dynamic arrays optimized by language developers.

---

## Static Arrays vs Dynamic Arrays

### Static Arrays
- **Fixed size** at creation time
- **Contiguous memory** allocation
- **No resizing** capability
- **Lower overhead** (no extra capacity)

### Dynamic Arrays
- **Resizable** during runtime
- **Contiguous memory** with growth capability
- **Amortized O(1)** insertion at end
- **Slight overhead** for growth management

---

## Time Complexity

| Operation | Static Array | Dynamic Array | Notes |
|-----------|-------------|---------------|-------|
| **Access by index** | O(1) | O(1) | Direct memory calculation |
| **Search** | O(n) | O(n) | Must check each element |
| **Insert at end** | N/A | O(1) amortized | Occasionally O(n) for resize |
| **Insert at beginning** | N/A | O(n) | Shifts all elements |
| **Insert at position** | N/A | O(n) | Shifts elements after position |
| **Delete at end** | N/A | O(1) | No shifting required |
| **Delete at beginning** | N/A | O(n) | Shifts all elements |
| **Delete at position** | N/A | O(n) | Shifts elements after position |

---

## Static Arrays

### Python Implementation
Python doesn't have built-in static arrays, but we can simulate them:

```python
class StaticArray:
    def __init__(self, size):
        self.size = size
        self.data = [None] * size
    
    def get(self, index):
        """Get element at index"""
        if 0 <= index < self.size:
            return self.data[index]
        raise IndexError("Index out of bounds")
    
    def set(self, index, value):
        """Set element at index"""
        if 0 <= index < self.size:
            self.data[index] = value
        else:
            raise IndexError("Index out of bounds")
    
    def length(self):
        """Get array length"""
        return self.size
    
    def __str__(self):
        return f"StaticArray({self.data})"

# Example usage
arr = StaticArray(5)
arr.set(0, "first")
arr.set(1, "second")
arr.set(4, "last")

print(arr)  # StaticArray(['first', 'second', None, None, 'last'])
print(arr.get(1))  # "second"

# Using Python's array module for numeric arrays
import array
numeric_array = array.array('i', [1, 2, 3, 4, 5])  # 'i' = signed integers
print(numeric_array)  # array('i', [1, 2, 3, 4, 5])
```

### C# Implementation
```csharp
// C# has true static arrays
public class StaticArrayExample
{
    public static void DemonstrateStaticArrays()
    {
        // Static array declaration and initialization
        int[] numbers = new int[5];  // Fixed size of 5
        
        // Initialize with values
        int[] initialized = {1, 2, 3, 4, 5};
        
        // Or using array initializer syntax
        int[] another = new int[] {10, 20, 30};
        
        // Access and modify
        numbers[0] = 100;
        numbers[4] = 500;
        
        Console.WriteLine($"Length: {numbers.Length}");  // 5
        Console.WriteLine($"First: {numbers[0]}");       // 100
        Console.WriteLine($"Last: {numbers[4]}");        // 500
        
        // Iterate through array
        for (int i = 0; i < numbers.Length; i++)
        {
            Console.WriteLine($"numbers[{i}] = {numbers[i]}");
        }
        
        // Enhanced for loop
        foreach (int num in initialized)
        {
            Console.WriteLine(num);
        }
    }
}

// Custom static array class
public class StaticArray<T>
{
    private T[] data;
    private int size;
    
    public StaticArray(int size)
    {
        this.size = size;
        this.data = new T[size];
    }
    
    public T Get(int index)
    {
        if (index >= 0 && index < size)
            return data[index];
        throw new IndexOutOfRangeException("Index out of bounds");
    }
    
    public void Set(int index, T value)
    {
        if (index >= 0 && index < size)
            data[index] = value;
        else
            throw new IndexOutOfRangeException("Index out of bounds");
    }
    
    public int Length => size;
    
    public override string ToString()
    {
        return $"StaticArray[{string.Join(", ", data)}]";
    }
}
```

---

## Dynamic Arrays

### Python Implementation
Python's `list` is a dynamic array, but let's implement our own to understand the concepts:

```python
class DynamicArray:
    def __init__(self, initial_capacity=4):
        self.capacity = initial_capacity
        self.size = 0
        self.data = [None] * self.capacity
    
    def __len__(self):
        return self.size
    
    def __getitem__(self, index):
        """Get element at index"""
        if 0 <= index < self.size:
            return self.data[index]
        raise IndexError("Index out of bounds")
    
    def __setitem__(self, index, value):
        """Set element at index"""
        if 0 <= index < self.size:
            self.data[index] = value
        else:
            raise IndexError("Index out of bounds")
    
    def append(self, value):
        """Add element to end of array"""
        if self.size >= self.capacity:
            self._resize()
        
        self.data[self.size] = value
        self.size += 1
    
    def insert(self, index, value):
        """Insert element at specific index"""
        if index < 0 or index > self.size:
            raise IndexError("Index out of bounds")
        
        if self.size >= self.capacity:
            self._resize()
        
        # Shift elements to the right
        for i in range(self.size, index, -1):
            self.data[i] = self.data[i - 1]
        
        self.data[index] = value
        self.size += 1
    
    def remove_at(self, index):
        """Remove element at specific index"""
        if index < 0 or index >= self.size:
            raise IndexError("Index out of bounds")
        
        removed_value = self.data[index]
        
        # Shift elements to the left
        for i in range(index, self.size - 1):
            self.data[i] = self.data[i + 1]
        
        self.size -= 1
        self.data[self.size] = None  # Clear reference
        
        return removed_value
    
    def remove(self, value):
        """Remove first occurrence of value"""
        for i in range(self.size):
            if self.data[i] == value:
                return self.remove_at(i)
        raise ValueError(f"Value {value} not found")
    
    def _resize(self):
        """Double the capacity when needed"""
        old_capacity = self.capacity
        self.capacity *= 2
        new_data = [None] * self.capacity
        
        # Copy existing data
        for i in range(self.size):
            new_data[i] = self.data[i]
        
        self.data = new_data
        print(f"Resized from {old_capacity} to {self.capacity}")
    
    def __str__(self):
        elements = [str(self.data[i]) for i in range(self.size)]
        return f"DynamicArray([{', '.join(elements)}])"

# Example usage
arr = DynamicArray()
print(f"Initial: {arr}, capacity: {arr.capacity}")

# Add elements
for i in range(10):
    arr.append(f"item_{i}")
    if i in [3, 7]:  # Show resize points
        print(f"After adding {i+1} items: {arr}")

print(f"Final: {arr}")

# Insert and remove
arr.insert(0, "first")
arr.insert(5, "middle")
print(f"After insertions: {arr}")

arr.remove("item_3")
arr.remove_at(0)
print(f"After removals: {arr}")
```

### C# Implementation
```csharp
public class DynamicArray<T>
{
    private T[] data;
    private int size;
    private int capacity;
    
    public DynamicArray(int initialCapacity = 4)
    {
        capacity = initialCapacity;
        size = 0;
        data = new T[capacity];
    }
    
    public int Count => size;
    public int Capacity => capacity;
    
    public T this[int index]
    {
        get
        {
            if (index >= 0 && index < size)
                return data[index];
            throw new IndexOutOfRangeException("Index out of bounds");
        }
        set
        {
            if (index >= 0 && index < size)
                data[index] = value;
            else
                throw new IndexOutOfRangeException("Index out of bounds");
        }
    }
    
    public void Add(T value)
    {
        if (size >= capacity)
            Resize();
        
        data[size] = value;
        size++;
    }
    
    public void Insert(int index, T value)
    {
        if (index < 0 || index > size)
            throw new IndexOutOfRangeException("Index out of bounds");
        
        if (size >= capacity)
            Resize();
        
        // Shift elements to the right
        for (int i = size; i > index; i--)
        {
            data[i] = data[i - 1];
        }
        
        data[index] = value;
        size++;
    }
    
    public T RemoveAt(int index)
    {
        if (index < 0 || index >= size)
            throw new IndexOutOfRangeException("Index out of bounds");
        
        T removedValue = data[index];
        
        // Shift elements to the left
        for (int i = index; i < size - 1; i++)
        {
            data[i] = data[i + 1];
        }
        
        size--;
        data[size] = default(T); // Clear reference
        
        return removedValue;
    }
    
    public bool Remove(T value)
    {
        for (int i = 0; i < size; i++)
        {
            if (EqualityComparer<T>.Default.Equals(data[i], value))
            {
                RemoveAt(i);
                return true;
            }
        }
        return false;
    }
    
    private void Resize()
    {
        int oldCapacity = capacity;
        capacity *= 2;
        T[] newData = new T[capacity];
        
        Array.Copy(data, newData, size);
        data = newData;
        
        Console.WriteLine($"Resized from {oldCapacity} to {capacity}");
    }
    
    public override string ToString()
    {
        var elements = new string[size];
        for (int i = 0; i < size; i++)
        {
            elements[i] = data[i]?.ToString() ?? "null";
        }
        return $"DynamicArray([{string.Join(", ", elements)}])";
    }
}

// Example usage
var arr = new DynamicArray<string>();
Console.WriteLine($"Initial: {arr}, capacity: {arr.Capacity}");

// Add elements
for (int i = 0; i < 10; i++)
{
    arr.Add($"item_{i}");
    if (i == 3 || i == 7) // Show resize points
        Console.WriteLine($"After adding {i+1} items: {arr}");
}

Console.WriteLine($"Final: {arr}");

// Insert and remove
arr.Insert(0, "first");
arr.Insert(5, "middle");
Console.WriteLine($"After insertions: {arr}");

arr.Remove("item_3");
arr.RemoveAt(0);
Console.WriteLine($"After removals: {arr}");
```

---

## Amortized Analysis of Dynamic Arrays

### Why O(1) Amortized?

When a dynamic array needs to resize:
1. **Allocate** new array (usually 2x size)
2. **Copy** all existing elements  
3. **Update** pointer to new array

Individual operations:
- Most appends: O(1)
- Resize append: O(n)

But resizing happens infrequently:
- Resize at sizes: 4, 8, 16, 32, 64, ...
- Between size n/2 and n, we do n/2 O(1) operations
- Total cost for n operations: O(n)
- Average cost per operation: O(1)

### Visual Example
```
Capacity: 4 -> 8 -> 16 -> 32
Operations to get to 32 elements:
- 32 individual O(1) appends
- 3 resize operations: O(4) + O(8) + O(16) = O(28)
- Total: O(32 + 28) = O(60) = O(n)
- Average per operation: O(60/32) ≈ O(1)
```

---

## Common Array Operations

### Searching
```python
def linear_search(arr, target):
    """Find first occurrence of target"""
    for i in range(len(arr)):
        if arr[i] == target:
            return i
    return -1

def find_all_occurrences(arr, target):
    """Find all indices where target appears"""
    indices = []
    for i in range(len(arr)):
        if arr[i] == target:
            indices.append(i)
    return indices

# Using built-in methods
arr = [1, 3, 7, 3, 9, 3]
print(arr.index(3))        # 1 (first occurrence)
print(arr.count(3))        # 3 (total occurrences)
print(3 in arr)           # True (membership test)
```

### Two Pointers Technique
```python
def reverse_array(arr):
    """Reverse array in-place using two pointers"""
    left, right = 0, len(arr) - 1
    
    while left < right:
        arr[left], arr[right] = arr[right], arr[left]
        left += 1
        right -= 1
    
    return arr

def remove_duplicates_sorted(arr):
    """Remove duplicates from sorted array"""
    if not arr:
        return 0
    
    write_index = 1
    
    for read_index in range(1, len(arr)):
        if arr[read_index] != arr[read_index - 1]:
            arr[write_index] = arr[read_index]
            write_index += 1
    
    return write_index  # New length

# Example usage
numbers = [1, 2, 3, 4, 5]
reverse_array(numbers)
print(numbers)  # [5, 4, 3, 2, 1]

sorted_with_dups = [1, 1, 2, 2, 2, 3, 4, 4, 5]
new_length = remove_duplicates_sorted(sorted_with_dups)
print(sorted_with_dups[:new_length])  # [1, 2, 3, 4, 5]
```

### Sliding Window Technique
```python
def max_sum_subarray(arr, k):
    """Find maximum sum of subarray of length k"""
    if len(arr) < k:
        return None
    
    # Calculate sum of first window
    window_sum = sum(arr[:k])
    max_sum = window_sum
    
    # Slide the window
    for i in range(k, len(arr)):
        window_sum = window_sum - arr[i - k] + arr[i]
        max_sum = max(max_sum, window_sum)
    
    return max_sum

# Example usage
arr = [2, 1, 5, 1, 3, 2]
print(max_sum_subarray(arr, 3))  # 9 (subarray [5, 1, 3])
```

---

## Multi-dimensional Arrays

### 2D Arrays
```python
# Creating 2D arrays in Python
def create_2d_array(rows, cols, default_value=0):
    return [[default_value for _ in range(cols)] for _ in range(rows)]

# Don't do this - all rows share the same list!
# wrong_2d = [[0] * cols] * rows

# Example usage
matrix = create_2d_array(3, 4, 0)
matrix[0][0] = 1
matrix[1][2] = 5
matrix[2][3] = 9

print("Matrix:")
for row in matrix:
    print(row)

# Matrix operations
def matrix_sum(matrix1, matrix2):
    """Add two matrices"""
    rows = len(matrix1)
    cols = len(matrix1[0])
    result = create_2d_array(rows, cols)
    
    for i in range(rows):
        for j in range(cols):
            result[i][j] = matrix1[i][j] + matrix2[i][j]
    
    return result
```

```csharp
// 2D arrays in C#
public static class Matrix2D
{
    public static void Demonstrate2DArrays()
    {
        // Rectangular array (true 2D array)
        int[,] matrix1 = new int[3, 4];
        int[,] matrix2 = {{1, 2, 3}, {4, 5, 6}};
        
        // Jagged array (array of arrays)
        int[][] jaggedArray = new int[3][];
        jaggedArray[0] = new int[4];
        jaggedArray[1] = new int[3];
        jaggedArray[2] = new int[2];
        
        // Initialize and access
        matrix1[0, 0] = 1;
        matrix1[1, 2] = 5;
        
        Console.WriteLine($"Rows: {matrix1.GetLength(0)}");
        Console.WriteLine($"Cols: {matrix1.GetLength(1)}");
        
        // Iterate through 2D array
        for (int i = 0; i < matrix1.GetLength(0); i++)
        {
            for (int j = 0; j < matrix1.GetLength(1); j++)
            {
                Console.Write($"{matrix1[i, j]} ");
            }
            Console.WriteLine();
        }
    }
    
    public static int[,] MatrixAdd(int[,] a, int[,] b)
    {
        int rows = a.GetLength(0);
        int cols = a.GetLength(1);
        int[,] result = new int[rows, cols];
        
        for (int i = 0; i < rows; i++)
        {
            for (int j = 0; j < cols; j++)
            {
                result[i, j] = a[i, j] + b[i, j];
            }
        }
        
        return result;
    }
}
```

---

## Interview Problems

### 1. Rotate Array
```python
def rotate_array_right(arr, k):
    """Rotate array to the right by k positions"""
    if not arr:
        return arr
    
    n = len(arr)
    k = k % n  # Handle k > n
    
    # Method 1: Using extra space
    result = [0] * n
    for i in range(n):
        result[(i + k) % n] = arr[i]
    
    return result

def rotate_array_inplace(arr, k):
    """Rotate array in-place using reversal"""
    if not arr:
        return arr
    
    n = len(arr)
    k = k % n
    
    def reverse(start, end):
        while start < end:
            arr[start], arr[end] = arr[end], arr[start]
            start += 1
            end -= 1
    
    # Reverse entire array
    reverse(0, n - 1)
    # Reverse first k elements  
    reverse(0, k - 1)
    # Reverse remaining elements
    reverse(k, n - 1)
    
    return arr

# Example usage
arr1 = [1, 2, 3, 4, 5, 6, 7]
print(rotate_array_right(arr1, 3))  # [5, 6, 7, 1, 2, 3, 4]

arr2 = [1, 2, 3, 4, 5, 6, 7]
print(rotate_array_inplace(arr2, 3))  # [5, 6, 7, 1, 2, 3, 4]
```

### 2. Merge Sorted Arrays
```python
def merge_sorted_arrays(arr1, arr2):
    """Merge two sorted arrays"""
    result = []
    i = j = 0
    
    while i < len(arr1) and j < len(arr2):
        if arr1[i] <= arr2[j]:
            result.append(arr1[i])
            i += 1
        else:
            result.append(arr2[j])
            j += 1
    
    # Add remaining elements
    result.extend(arr1[i:])
    result.extend(arr2[j:])
    
    return result

# Example usage
arr1 = [1, 3, 5, 7]
arr2 = [2, 4, 6, 8, 9]
merged = merge_sorted_arrays(arr1, arr2)
print(merged)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 3. Find Peak Element
```python
def find_peak_element(arr):
    """Find a peak element (greater than neighbors)"""
    n = len(arr)
    if n == 1:
        return 0
    
    # Check first element
    if arr[0] > arr[1]:
        return 0
    
    # Check last element
    if arr[n-1] > arr[n-2]:
        return n - 1
    
    # Check middle elements
    for i in range(1, n-1):
        if arr[i] > arr[i-1] and arr[i] > arr[i+1]:
            return i
    
    return -1  # No peak found

# Binary search approach for better performance
def find_peak_binary(arr):
    """Find peak using binary search - O(log n)"""
    left, right = 0, len(arr) - 1
    
    while left < right:
        mid = (left + right) // 2
        
        if arr[mid] > arr[mid + 1]:
            right = mid
        else:
            left = mid + 1
    
    return left

# Example usage
peaks = [1, 2, 3, 1]
print(find_peak_element(peaks))  # 2 (element 3 is peak)
print(find_peak_binary(peaks))   # 2
```

## Modern Usage

**Python:** Use `list` - it's a highly optimized dynamic array with many built-in methods
**C#:** Use `List<T>` for dynamic behavior, arrays for fixed-size scenarios
**Performance:** Built-in implementations are heavily optimized with native code

**Key Takeaways:**
- Arrays are the foundation of most data structures
- Understand the difference between static and dynamic arrays
- Know when O(1) amortized means and why it matters
- Master common patterns: two pointers, sliding window, array manipulation
- Use language built-ins in production, implement from scratch in interviews