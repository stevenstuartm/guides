# Trees & Binary Search Trees

## Why Trees Exist
Hierarchical relationships are everywhere - file systems, decision making, organizing data for fast searches, representing mathematical expressions. Trees provide a natural way to model and efficiently traverse these relationships.

---

## General Trees

### When to Use Trees

**Use when:**
- Data has natural hierarchy (organizational charts, file systems)
- Need efficient searching with dynamic insertions/deletions
- Representing decision trees or game states
- Parsing expressions or syntax trees

**Don't use when:**
- Data is flat with no relationships
- Simple sequential access is all that's needed
- Memory fragmentation is a major concern

**Modern reality:** Most databases use B-trees internally. You'll implement binary trees in interviews but rarely build custom tree structures in production.

### Basic Tree Implementation

#### Python Implementation
```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.children = []
    
    def add_child(self, child_node):
        """Add a child node to this node"""
        print(f"Adding {child_node.value} as child of {self.value}")
        self.children.append(child_node)
    
    def remove_child(self, child_node):
        """Remove a child node from this node"""
        print(f"Removing {child_node.value} from {self.value}")
        self.children = [child for child in self.children if child is not child_node]
    
    def traverse_depth_first(self):
        """Depth-first traversal using recursion"""
        print(self.value)
        for child in self.children:
            child.traverse_depth_first()
    
    def traverse_breadth_first(self):
        """Breadth-first traversal using queue"""
        queue = [self]
        while queue:
            current_node = queue.pop(0)
            print(current_node.value)
            queue.extend(current_node.children)
    
    def find_node(self, target_value):
        """Find a node with specific value using DFS"""
        if self.value == target_value:
            return self
        
        for child in self.children:
            result = child.find_node(target_value)
            if result:
                return result
        
        return None

# Example usage
root = TreeNode("CEO")
vp_engineering = TreeNode("VP Engineering")
vp_marketing = TreeNode("VP Marketing")

root.add_child(vp_engineering)
root.add_child(vp_marketing)

engineer1 = TreeNode("Senior Engineer")
engineer2 = TreeNode("Junior Engineer")
vp_engineering.add_child(engineer1)
vp_engineering.add_child(engineer2)

print("Depth-first traversal:")
root.traverse_depth_first()

print("\nBreadth-first traversal:")
root.traverse_breadth_first()
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
        Console.WriteLine($"Adding {childNode.Value} as child of {Value}");
        Children.Add(childNode);
    }
    
    public void RemoveChild(TreeNode<T> childNode)
    {
        Console.WriteLine($"Removing {childNode.Value} from {Value}");
        Children.RemoveAll(child => ReferenceEquals(child, childNode));
    }
    
    public void TraverseDepthFirst()
    {
        Console.WriteLine(Value);
        foreach (var child in Children)
        {
            child.TraverseDepthFirst();
        }
    }
    
    public void TraverseBreadthFirst()
    {
        var queue = new Queue<TreeNode<T>>();
        queue.Enqueue(this);
        
        while (queue.Count > 0)
        {
            var currentNode = queue.Dequeue();
            Console.WriteLine(currentNode.Value);
            
            foreach (var child in currentNode.Children)
            {
                queue.Enqueue(child);
            }
        }
    }
    
    public TreeNode<T> FindNode(T targetValue)
    {
        if (EqualityComparer<T>.Default.Equals(Value, targetValue))
        {
            return this;
        }
        
        foreach (var child in Children)
        {
            var result = child.FindNode(targetValue);
            if (result != null)
            {
                return result;
            }
        }
        
        return null;
    }
}

// Example usage
var root = new TreeNode<string>("CEO");
var vpEngineering = new TreeNode<string>("VP Engineering");
var vpMarketing = new TreeNode<string>("VP Marketing");

root.AddChild(vpEngineering);
root.AddChild(vpMarketing);

var engineer1 = new TreeNode<string>("Senior Engineer");
var engineer2 = new TreeNode<string>("Junior Engineer");
vpEngineering.AddChild(engineer1);
vpEngineering.AddChild(engineer2);

Console.WriteLine("Depth-first traversal:");
root.TraverseDepthFirst();

Console.WriteLine("\nBreadth-first traversal:");
root.TraverseBreadthFirst();
```

---

## Binary Search Trees (BST)

### When to Use Binary Search Trees

**Use when:**
- Need sorted data with fast insertion/deletion/search
- Want to iterate through data in sorted order
- Building other data structures (heaps, balanced trees)
- Educational purposes or interviews

**Don't use when:**
- Data doesn't have a natural ordering
- Need guaranteed balanced performance (use AVL or Red-Black trees)
- Simple array operations are sufficient

**Modern reality:** Use balanced variants (AVL, Red-Black) or language built-ins like C#'s `SortedDictionary`. Implement BSTs in interviews to show understanding.

### Time Complexity
- **Average case:** O(log n) for all operations
- **Worst case:** O(n) when tree becomes skewed (effectively a linked list)
- **Space complexity:** O(n)

### Binary Search Tree Properties
1. Left subtree contains only nodes with values less than parent
2. Right subtree contains only nodes with values greater than parent
3. Both left and right subtrees are also BSTs
4. No duplicate values (typically)

### Python Implementation
```python
class BinarySearchTree:
    def __init__(self, value, depth=1):
        self.value = value
        self.depth = depth
        self.left = None
        self.right = None
    
    def insert(self, value):
        """Insert a new value into the BST"""
        if value < self.value:
            if self.left is None:
                self.left = BinarySearchTree(value, self.depth + 1)
                print(f"Inserted {value} to the left of {self.value} at depth {self.depth + 1}")
            else:
                self.left.insert(value)
        elif value > self.value:  # No duplicates allowed
            if self.right is None:
                self.right = BinarySearchTree(value, self.depth + 1)
                print(f"Inserted {value} to the right of {self.value} at depth {self.depth + 1}")
            else:
                self.right.insert(value)
        else:
            print(f"Value {value} already exists in the tree")
    
    def search(self, value):
        """Search for a value in the BST"""
        if self.value == value:
            return self
        elif value < self.value and self.left is not None:
            return self.left.search(value)
        elif value > self.value and self.right is not None:
            return self.right.search(value)
        else:
            return None
    
    def inorder_traversal(self):
        """In-order traversal: left -> root -> right (gives sorted order)"""
        result = []
        if self.left is not None:
            result.extend(self.left.inorder_traversal())
        result.append(self.value)
        if self.right is not None:
            result.extend(self.right.inorder_traversal())
        return result
    
    def preorder_traversal(self):
        """Pre-order traversal: root -> left -> right"""
        result = [self.value]
        if self.left is not None:
            result.extend(self.left.preorder_traversal())
        if self.right is not None:
            result.extend(self.right.preorder_traversal())
        return result
    
    def postorder_traversal(self):
        """Post-order traversal: left -> right -> root"""
        result = []
        if self.left is not None:
            result.extend(self.left.postorder_traversal())
        if self.right is not None:
            result.extend(self.right.postorder_traversal())
        result.append(self.value)
        return result
    
    def find_min(self):
        """Find minimum value (leftmost node)"""
        if self.left is None:
            return self.value
        return self.left.find_min()
    
    def find_max(self):
        """Find maximum value (rightmost node)"""
        if self.right is None:
            return self.value
        return self.right.find_max()
    
    def delete(self, value):
        """Delete a node with given value"""
        if value < self.value:
            if self.left is not None:
                self.left = self.left.delete(value)
        elif value > self.value:
            if self.right is not None:
                self.right = self.right.delete(value)
        else:  # Found the node to delete
            # Case 1: No children (leaf node)
            if self.left is None and self.right is None:
                return None
            
            # Case 2: One child
            if self.left is None:
                return self.right
            if self.right is None:
                return self.left
            
            # Case 3: Two children
            # Replace with inorder successor (smallest value in right subtree)
            min_right = self.right.find_min()
            self.value = min_right
            self.right = self.right.delete(min_right)
        
        return self

# Example usage
print("Creating BST with root value 15:")
bst = BinarySearchTree(15)

# Insert values
values = [10, 20, 8, 12, 25, 6, 11, 13, 27]
for value in values:
    bst.insert(value)

print(f"\nIn-order traversal (sorted): {bst.inorder_traversal()}")
print(f"Pre-order traversal: {bst.preorder_traversal()}")
print(f"Post-order traversal: {bst.postorder_traversal()}")

print(f"\nMin value: {bst.find_min()}")
print(f"Max value: {bst.find_max()}")

# Search for values
search_values = [12, 99, 25]
for value in search_values:
    result = bst.search(value)
    print(f"Search for {value}: {'Found' if result else 'Not found'}")
```

### C# Implementation
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
        int comparison = value.CompareTo(Value);
        
        if (comparison < 0)
        {
            if (Left == null)
            {
                Left = new BinarySearchTree<T>(value, Depth + 1);
                Console.WriteLine($"Inserted {value} to the left of {Value} at depth {Depth + 1}");
            }
            else
            {
                Left.Insert(value);
            }
        }
        else if (comparison > 0)
        {
            if (Right == null)
            {
                Right = new BinarySearchTree<T>(value, Depth + 1);
                Console.WriteLine($"Inserted {value} to the right of {Value} at depth {Depth + 1}");
            }
            else
            {
                Right.Insert(value);
            }
        }
        else
        {
            Console.WriteLine($"Value {value} already exists in the tree");
        }
    }
    
    public BinarySearchTree<T> Search(T value)
    {
        int comparison = value.CompareTo(Value);
        
        if (comparison == 0)
        {
            return this;
        }
        else if (comparison < 0 && Left != null)
        {
            return Left.Search(value);
        }
        else if (comparison > 0 && Right != null)
        {
            return Right.Search(value);
        }
        else
        {
            return null;
        }
    }
    
    public List<T> InorderTraversal()
    {
        var result = new List<T>();
        
        if (Left != null)
            result.AddRange(Left.InorderTraversal());
        
        result.Add(Value);
        
        if (Right != null)
            result.AddRange(Right.InorderTraversal());
        
        return result;
    }
    
    public List<T> PreorderTraversal()
    {
        var result = new List<T> { Value };
        
        if (Left != null)
            result.AddRange(Left.PreorderTraversal());
        
        if (Right != null)
            result.AddRange(Right.PreorderTraversal());
        
        return result;
    }
    
    public List<T> PostorderTraversal()
    {
        var result = new List<T>();
        
        if (Left != null)
            result.AddRange(Left.PostorderTraversal());
        
        if (Right != null)
            result.AddRange(Right.PostorderTraversal());
        
        result.Add(Value);
        
        return result;
    }
    
    public T FindMin()
    {
        if (Left == null)
            return Value;
        return Left.FindMin();
    }
    
    public T FindMax()
    {
        if (Right == null)
            return Value;
        return Right.FindMax();
    }
    
    public BinarySearchTree<T> Delete(T value)
    {
        int comparison = value.CompareTo(Value);
        
        if (comparison < 0)
        {
            if (Left != null)
                Left = Left.Delete(value);
        }
        else if (comparison > 0)
        {
            if (Right != null)
                Right = Right.Delete(value);
        }
        else  // Found the node to delete
        {
            // Case 1: No children (leaf node)
            if (Left == null && Right == null)
                return null;
            
            // Case 2: One child
            if (Left == null)
                return Right;
            if (Right == null)
                return Left;
            
            // Case 3: Two children
            // Replace with inorder successor
            T minRight = Right.FindMin();
            Value = minRight;
            Right = Right.Delete(minRight);
        }
        
        return this;
    }
}

// Example usage
Console.WriteLine("Creating BST with root value 15:");
var bst = new BinarySearchTree<int>(15);

// Insert values
int[] values = {10, 20, 8, 12, 25, 6, 11, 13, 27};
foreach (var value in values)
{
    bst.Insert(value);
}

Console.WriteLine($"\nIn-order traversal (sorted): [{string.Join(", ", bst.InorderTraversal())}]");
Console.WriteLine($"Pre-order traversal: [{string.Join(", ", bst.PreorderTraversal())}]");
Console.WriteLine($"Post-order traversal: [{string.Join(", ", bst.PostorderTraversal())}]");

Console.WriteLine($"\nMin value: {bst.FindMin()}");
Console.WriteLine($"Max value: {bst.FindMax()}");

// Search for values
int[] searchValues = {12, 99, 25};
foreach (var value in searchValues)
{
    var result = bst.Search(value);
    Console.WriteLine($"Search for {value}: {(result != null ? "Found" : "Not found")}");
}
```

## Tree Traversal Methods

### When to Use Each Traversal

**In-order (Left → Root → Right):**
- **Use for:** Getting sorted output from BST, expression evaluation
- **BST property:** Always produces sorted sequence

**Pre-order (Root → Left → Right):**
- **Use for:** Copying tree structure, prefix notation, serialization
- **Pattern:** Process node before children

**Post-order (Left → Right → Root):**
- **Use for:** Deleting tree, calculating directory sizes, postfix notation
- **Pattern:** Process children before node

**Level-order (Breadth-first):**
- **Use for:** Level-by-level processing, finding shortest path in tree
- **Implementation:** Uses queue for traversal

### Level-Order Traversal Implementation

#### Python
```python
from collections import deque

def level_order_traversal(root):
    """Level-order traversal using queue"""
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        current_level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            current_level.append(node.value)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(current_level)
    
    return result

# Example with BST
bst = BinarySearchTree(15)
for val in [10, 20, 8, 12, 25, 6]:
    bst.insert(val)

levels = level_order_traversal(bst)
print("Level-order traversal:")
for i, level in enumerate(levels):
    print(f"Level {i}: {level}")
```

#### C#
```csharp
public static List<List<T>> LevelOrderTraversal<T>(BinarySearchTree<T> root) where T : IComparable<T>
{
    if (root == null)
        return new List<List<T>>();
    
    var result = new List<List<T>>();
    var queue = new Queue<BinarySearchTree<T>>();
    queue.Enqueue(root);
    
    while (queue.Count > 0)
    {
        int levelSize = queue.Count;
        var currentLevel = new List<T>();
        
        for (int i = 0; i < levelSize; i++)
        {
            var node = queue.Dequeue();
            currentLevel.Add(node.Value);
            
            if (node.Left != null)
                queue.Enqueue(node.Left);
            if (node.Right != null)
                queue.Enqueue(node.Right);
        }
        
        result.Add(currentLevel);
    }
    
    return result;
}
```

## Common Interview Problems

### 1. Validate Binary Search Tree
```python
def is_valid_bst(node, min_val=float('-inf'), max_val=float('inf')):
    """Check if tree maintains BST property"""
    if not node:
        return True
    
    if node.value <= min_val or node.value >= max_val:
        return False
    
    return (is_valid_bst(node.left, min_val, node.value) and
            is_valid_bst(node.right, node.value, max_val))
```

### 2. Find Lowest Common Ancestor
```python
def find_lca(root, p, q):
    """Find lowest common ancestor in BST"""
    if not root:
        return None
    
    # Both nodes are in left subtree
    if p < root.value and q < root.value:
        return find_lca(root.left, p, q)
    
    # Both nodes are in right subtree
    if p > root.value and q > root.value:
        return find_lca(root.right, p, q)
    
    # Nodes are on different sides (or one is root)
    return root
```

### 3. Convert Sorted Array to BST
```python
def sorted_array_to_bst(nums):
    """Convert sorted array to balanced BST"""
    if not nums:
        return None
    
    mid = len(nums) // 2
    root = BinarySearchTree(nums[mid])
    
    root.left = sorted_array_to_bst(nums[:mid])
    root.right = sorted_array_to_bst(nums[mid + 1:])
    
    return root
```

## Balanced Tree Concepts

### Why Balancing Matters
Unbalanced BSTs can degrade to O(n) operations. Balanced trees guarantee O(log n).

**Self-balancing tree types:**
- **AVL Trees:** Strictly balanced, height difference ≤ 1
- **Red-Black Trees:** Looser balance constraints, used in many libraries
- **B-Trees:** Multi-way trees, used in databases

### When Tree Becomes Skewed
```python
# This creates a skewed tree (effectively a linked list)
skewed_bst = BinarySearchTree(1)
for i in range(2, 8):
    skewed_bst.insert(i)  # Always goes right

# Height = n, operations become O(n)
print(skewed_bst.inorder_traversal())  # [1, 2, 3, 4, 5, 6, 7]
```

## Modern Usage

**Python:** Use `bisect` module for sorted list operations, or implement AVL/Red-Black for guaranteed performance

**C#:** Use `SortedDictionary<K,V>` or `SortedSet<T>` (implemented as Red-Black trees)

**Interview focus:** Understand BST properties, implement basic operations, know common problems like validation and traversals

**Production reality:** Language built-ins are usually better than custom BST implementations unless you have very specific requirements.