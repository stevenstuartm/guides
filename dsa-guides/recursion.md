# Recursion

## Why Recursion Exists
Some problems are naturally self-similar - tree traversal, mathematical sequences, divide-and-conquer algorithms. Recursion provides an elegant way to express solutions that work on progressively smaller versions of the same problem.

## When to Use Recursion

**Use when:**
- Problem has self-similar subproblems
- Working with recursive data structures (trees, graphs)
- Divide-and-conquer algorithms
- Backtracking problems (N-queens, sudoku)
- Mathematical sequences and calculations
- Parsing nested structures

**Don't use when:**
- Simple iteration suffices
- Stack space is limited (embedded systems)
- Performance is critical and iterative solution exists
- Deep recursion might cause stack overflow

**Modern reality:** Essential for interviews and certain algorithms, but iterative solutions are often preferred in production for performance and stack safety.

---

## Essential Recursion Components

### 1. Base Case
The condition that stops recursion. Without it, you get infinite recursion and stack overflow.

### 2. Recursive Case
The function calls itself with a modified (usually smaller) input that moves toward the base case.

### 3. Progress Toward Base Case
Each recursive call must get "closer" to the base case to ensure termination.

---

## Classic Examples

### Factorial
```python
def factorial(n):
    # Base case
    if n <= 1:
        return 1
    
    # Recursive case: n! = n * (n-1)!
    return n * factorial(n - 1)

# Example usage
print(factorial(5))  # 120
print(factorial(0))  # 1
```

```csharp
public static int Factorial(int n)
{
    // Base case
    if (n <= 1)
        return 1;
    
    // Recursive case
    return n * Factorial(n - 1);
}

// Example usage
Console.WriteLine(Factorial(5)); // 120
Console.WriteLine(Factorial(0)); // 1
```

### Fibonacci Sequence
```python
def fibonacci_naive(n):
    """Naive recursive fibonacci - exponential time complexity"""
    if n <= 1:
        return n
    
    return fibonacci_naive(n - 1) + fibonacci_naive(n - 2)

# This is inefficient for large n due to repeated calculations
print(fibonacci_naive(10))  # 55, but slow for n > 30
```

### Optimized Fibonacci with Memoization
```python
def fibonacci_memo(n, memo={}):
    """Optimized fibonacci using memoization"""
    if n in memo:
        return memo[n]
    
    if n <= 1:
        return n
    
    memo[n] = fibonacci_memo(n - 1, memo) + fibonacci_memo(n - 2, memo)
    return memo[n]

# Much faster for large n
print(fibonacci_memo(50))  # 12586269025
```

```csharp
public static class Fibonacci
{
    private static Dictionary<int, long> memo = new Dictionary<int, long>();
    
    public static long FibonacciMemo(int n)
    {
        if (memo.ContainsKey(n))
            return memo[n];
        
        if (n <= 1)
            return n;
        
        memo[n] = FibonacciMemo(n - 1) + FibonacciMemo(n - 2);
        return memo[n];
    }
    
    // Alternative with explicit memo parameter
    public static long FibonacciMemoExplicit(int n, Dictionary<int, long> memo = null)
    {
        if (memo == null)
            memo = new Dictionary<int, long>();
            
        if (memo.ContainsKey(n))
            return memo[n];
        
        if (n <= 1)
            return n;
        
        memo[n] = FibonacciMemoExplicit(n - 1, memo) + FibonacciMemoExplicit(n - 2, memo);
        return memo[n];
    }
}

// Usage
Console.WriteLine(Fibonacci.FibonacciMemo(50)); // 12586269025
```

---

## Tree Recursion

### Tree Traversal
```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def inorder_traversal(node):
    """In-order: left -> root -> right"""
    if node is None:
        return []
    
    result = []
    result.extend(inorder_traversal(node.left))   # Left subtree
    result.append(node.value)                     # Root
    result.extend(inorder_traversal(node.right))  # Right subtree
    
    return result

def tree_height(node):
    """Calculate height of tree"""
    if node is None:
        return 0
    
    left_height = tree_height(node.left)
    right_height = tree_height(node.right)
    
    return 1 + max(left_height, right_height)

def tree_sum(node):
    """Sum all values in tree"""
    if node is None:
        return 0
    
    return node.value + tree_sum(node.left) + tree_sum(node.right)

# Example usage
root = TreeNode(1)
root.left = TreeNode(2)
root.right = TreeNode(3)
root.left.left = TreeNode(4)
root.left.right = TreeNode(5)

print(inorder_traversal(root))  # [4, 2, 5, 1, 3]
print(tree_height(root))        # 3
print(tree_sum(root))          # 15
```

```csharp
public class TreeNode<T>
{
    public T Value { get; set; }
    public TreeNode<T> Left { get; set; }
    public TreeNode<T> Right { get; set; }
    
    public TreeNode(T value)
    {
        Value = value;
        Left = null;
        Right = null;
    }
}

public static class TreeRecursion
{
    public static List<T> InorderTraversal<T>(TreeNode<T> node)
    {
        if (node == null)
            return new List<T>();
        
        var result = new List<T>();
        result.AddRange(InorderTraversal(node.Left));  // Left subtree
        result.Add(node.Value);                        // Root
        result.AddRange(InorderTraversal(node.Right)); // Right subtree
        
        return result;
    }
    
    public static int TreeHeight<T>(TreeNode<T> node)
    {
        if (node == null)
            return 0;
        
        int leftHeight = TreeHeight(node.Left);
        int rightHeight = TreeHeight(node.Right);
        
        return 1 + Math.Max(leftHeight, rightHeight);
    }
    
    public static int TreeSum(TreeNode<int> node)
    {
        if (node == null)
            return 0;
        
        return node.Value + TreeSum(node.Left) + TreeSum(node.Right);
    }
}

// Example usage
var root = new TreeNode<int>(1);
root.Left = new TreeNode<int>(2);
root.Right = new TreeNode<int>(3);
root.Left.Left = new TreeNode<int>(4);
root.Left.Right = new TreeNode<int>(5);

var inorder = TreeRecursion.InorderTraversal(root);
Console.WriteLine($"Inorder: [{string.Join(", ", inorder)}]"); // [4, 2, 5, 1, 3]
Console.WriteLine($"Height: {TreeRecursion.TreeHeight(root)}"); // 3
Console.WriteLine($"Sum: {TreeRecursion.TreeSum(root)}");       // 15
```

---

## Backtracking with Recursion

### N-Queens Problem
```python
def solve_n_queens(n):
    """Solve N-Queens problem using backtracking"""
    def is_safe(board, row, col):
        # Check column
        for i in range(row):
            if board[i] == col:
                return False
        
        # Check diagonals
        for i in range(row):
            if abs(board[i] - col) == abs(i - row):
                return False
        
        return True
    
    def backtrack(board, row):
        if row == n:
            return [board[:]]  # Found solution, return copy
        
        solutions = []
        for col in range(n):
            if is_safe(board, row, col):
                board[row] = col                    # Place queen
                solutions.extend(backtrack(board, row + 1))  # Recurse
                # No need to remove queen - we overwrite board[row] in next iteration
        
        return solutions
    
    board = [-1] * n  # board[i] = column position of queen in row i
    return backtrack(board, 0)

# Example usage
solutions = solve_n_queens(4)
print(f"Found {len(solutions)} solutions for 4-Queens")
for i, solution in enumerate(solutions):
    print(f"Solution {i + 1}: {solution}")
```

### Generate All Subsets
```python
def generate_subsets(nums):
    """Generate all possible subsets using recursion"""
    def backtrack(start, current_subset):
        # Add current subset to results
        result.append(current_subset[:])  # Make a copy
        
        # Try adding each remaining element
        for i in range(start, len(nums)):
            current_subset.append(nums[i])      # Choose
            backtrack(i + 1, current_subset)   # Explore
            current_subset.pop()               # Unchoose (backtrack)
    
    result = []
    backtrack(0, [])
    return result

# Example usage
subsets = generate_subsets([1, 2, 3])
print("All subsets:")
for subset in subsets:
    print(subset)
# Output: [], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]
```

```csharp
public static class Backtracking
{
    public static List<List<int>> GenerateSubsets(int[] nums)
    {
        var result = new List<List<int>>();
        
        void Backtrack(int start, List<int> currentSubset)
        {
            // Add current subset to results
            result.Add(new List<int>(currentSubset));
            
            // Try adding each remaining element
            for (int i = start; i < nums.Length; i++)
            {
                currentSubset.Add(nums[i]);         // Choose
                Backtrack(i + 1, currentSubset);   // Explore
                currentSubset.RemoveAt(currentSubset.Count - 1); // Unchoose
            }
        }
        
        Backtrack(0, new List<int>());
        return result;
    }
}

// Example usage
int[] nums = {1, 2, 3};
var subsets = Backtracking.GenerateSubsets(nums);
Console.WriteLine("All subsets:");
foreach (var subset in subsets)
{
    Console.WriteLine($"[{string.Join(", ", subset)}]");
}
```

---

## Divide and Conquer

### Merge Sort (Recursive)
```python
def merge_sort(arr):
    """Recursive merge sort implementation"""
    # Base case
    if len(arr) <= 1:
        return arr
    
    # Divide
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    
    # Conquer (merge)
    return merge(left, right)

def merge(left, right):
    """Merge two sorted arrays"""
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    # Add remaining elements
    result.extend(left[i:])
    result.extend(right[j:])
    
    return result

# Example usage
arr = [64, 34, 25, 12, 22, 11, 90]
sorted_arr = merge_sort(arr)
print(f"Original: {arr}")
print(f"Sorted: {sorted_arr}")
```

### Binary Search (Recursive)
```python
def binary_search_recursive(arr, target, left=0, right=None):
    """Recursive binary search"""
    if right is None:
        right = len(arr) - 1
    
    # Base case: not found
    if left > right:
        return -1
    
    mid = (left + right) // 2
    
    if arr[mid] == target:
        return mid
    elif arr[mid] > target:
        return binary_search_recursive(arr, target, left, mid - 1)
    else:
        return binary_search_recursive(arr, target, mid + 1, right)

# Example usage
sorted_arr = [1, 3, 5, 7, 9, 11, 13, 15]
index = binary_search_recursive(sorted_arr, 7)
print(f"Found 7 at index: {index}")  # Found 7 at index: 3
```

---

## Recursion vs Iteration

### When to Choose Each

**Recursion is better when:**
- Problem naturally recursive (trees, fractals)
- Code is much cleaner and easier to understand
- Stack depth is reasonable
- Performance is not critical

**Iteration is better when:**
- Simple loops suffice
- Memory is limited
- Performance is critical
- Stack overflow is a concern

### Converting Recursion to Iteration

#### Tail Recursion → Loop
```python
# Tail recursive (easy to convert)
def factorial_tail_recursive(n, acc=1):
    if n <= 1:
        return acc
    return factorial_tail_recursive(n - 1, n * acc)

# Iterative equivalent
def factorial_iterative(n):
    acc = 1
    while n > 1:
        acc *= n
        n -= 1
    return acc
```

#### General Recursion → Stack
```python
# Recursive tree traversal
def inorder_recursive(node):
    if node is None:
        return []
    
    result = []
    result.extend(inorder_recursive(node.left))
    result.append(node.value)
    result.extend(inorder_recursive(node.right))
    return result

# Iterative using explicit stack
def inorder_iterative(root):
    stack = []
    result = []
    current = root
    
    while stack or current:
        # Go to leftmost node
        while current:
            stack.append(current)
            current = current.left
        
        # Process current node
        current = stack.pop()
        result.append(current.value)
        
        # Move to right subtree
        current = current.right
    
    return result
```

---

## Common Recursion Patterns

### 1. Linear Recursion
Each function calls itself at most once.
```python
def sum_array(arr, index=0):
    if index >= len(arr):
        return 0
    return arr[index] + sum_array(arr, index + 1)
```

### 2. Tree Recursion  
Function calls itself multiple times.
```python
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)  # Two recursive calls
```

### 3. Tail Recursion
Recursive call is the last operation.
```python
def gcd(a, b):
    if b == 0:
        return a
    return gcd(b, a % b)  # Last operation is recursive call
```

### 4. Mutual Recursion
Functions call each other.
```python
def is_even(n):
    if n == 0:
        return True
    return is_odd(n - 1)

def is_odd(n):
    if n == 0:
        return False
    return is_even(n - 1)
```

---

## Recursion Debugging Tips

### 1. Print Trace
```python
def factorial_debug(n, depth=0):
    indent = "  " * depth
    print(f"{indent}factorial({n})")
    
    if n <= 1:
        print(f"{indent}-> returning 1")
        return 1
    
    result = n * factorial_debug(n - 1, depth + 1)
    print(f"{indent}-> returning {result}")
    return result

factorial_debug(4)
```

### 2. Visualize Call Stack
```python
def fibonacci_trace(n, call_stack=[]):
    call_stack.append(f"fib({n})")
    print(" -> ".join(call_stack))
    
    if n <= 1:
        result = n
    else:
        left = fibonacci_trace(n - 1, call_stack[:])
        right = fibonacci_trace(n - 2, call_stack[:])
        result = left + right
    
    call_stack.pop()
    return result
```

### 3. Count Function Calls
```python
def fibonacci_with_counter(n, counter={'calls': 0}):
    counter['calls'] += 1
    
    if n <= 1:
        return n
    
    return (fibonacci_with_counter(n - 1, counter) + 
            fibonacci_with_counter(n - 2, counter))

# Usage
counter = {'calls': 0}
result = fibonacci_with_counter(10, counter)
print(f"Result: {result}, Function calls: {counter['calls']}")
```

---

## Interview Problems

### 1. Power Calculation
```python
def power(base, exp):
    """Calculate base^exp using recursion"""
    if exp == 0:
        return 1
    if exp == 1:
        return base
    
    # Optimize by using exp//2
    half_power = power(base, exp // 2)
    if exp % 2 == 0:
        return half_power * half_power
    else:
        return base * half_power * half_power

print(power(2, 10))  # 1024
```

### 2. Reverse String
```python
def reverse_string(s):
    """Reverse string using recursion"""
    if len(s) <= 1:
        return s
    
    return reverse_string(s[1:]) + s[0]

print(reverse_string("hello"))  # "olleh"
```

### 3. Palindrome Check
```python
def is_palindrome(s, left=0, right=None):
    """Check if string is palindrome using recursion"""
    if right is None:
        right = len(s) - 1
    
    if left >= right:
        return True
    
    if s[left] != s[right]:
        return False
    
    return is_palindrome(s, left + 1, right - 1)

print(is_palindrome("racecar"))  # True
print(is_palindrome("hello"))    # False
```

## Performance Considerations

**Space Complexity:** O(d) where d is maximum recursion depth
**Stack Overflow:** Deep recursion can exhaust call stack
**Optimization:** Tail call optimization (not available in Python, limited in C#)

**Best Practices:**
1. Always define clear base cases
2. Ensure progress toward base case
3. Consider iterative alternatives for performance
4. Use memoization for overlapping subproblems
5. Be mindful of stack depth limits

**Modern Usage:** Essential for tree/graph algorithms, divide-and-conquer, and backtracking. Often preferred in functional programming languages with tail call optimization.