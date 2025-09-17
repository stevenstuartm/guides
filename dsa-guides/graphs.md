# Graphs & Graph Algorithms

## Why Graphs Exist
Model relationships and networks - social networks, maps, dependencies, state machines, computer networks. Graphs are the most flexible data structure for representing complex relationships between entities.

## When to Use Graphs

**Use when:**
- Modeling relationships between entities
- Pathfinding and navigation problems
- Network analysis and flow problems
- Dependency resolution (build systems, package managers)
- State machines and game AI
- Social network analysis

**Don't use when:**
- Simple hierarchical data (use trees)
- No relationships between data points
- Linear or simple key-value relationships

**Modern reality:** Critical for many applications. Often use graph databases (Neo4j) or specialized libraries rather than implementing from scratch.

---

## Graph Types and Terminology

### Key Terms
- **Vertex (Node):** A point in the graph that holds data
- **Edge:** A connection between two vertices
- **Adjacent:** Two vertices connected by an edge
- **Path:** A sequence of edges connecting vertices
- **Cycle:** A path that begins and ends at the same vertex
- **Connected Graph:** Every vertex can reach every other vertex
- **Disconnected Graph:** Some vertices cannot reach others

### Graph Types

**Directed vs Undirected:**
- **Directed (Digraph):** Edges have direction (A → B ≠ B → A)
- **Undirected:** Edges are bidirectional (A ↔ B)

**Weighted vs Unweighted:**
- **Weighted:** Edges have associated costs/distances
- **Unweighted:** All edges have equal weight (or weight = 1)

**Examples:**
- **Social Network:** Undirected, unweighted (friendship is mutual)
- **Twitter Follows:** Directed, unweighted (following isn't mutual)
- **Road Map:** Undirected, weighted (roads have distances)
- **Web Pages:** Directed, unweighted (links point one way)

---

## Graph Representation

### Adjacency List vs Adjacency Matrix

| Aspect | Adjacency List | Adjacency Matrix |
|--------|----------------|------------------|
| **Space** | O(V + E) | O(V²) |
| **Add Vertex** | O(1) | O(V²) |
| **Add Edge** | O(1) | O(1) |
| **Check Edge** | O(degree) | O(1) |
| **Iterate Neighbors** | O(degree) | O(V) |
| **Best For** | Sparse graphs | Dense graphs |

**Use Adjacency List when:**
- Sparse graphs (few edges relative to vertices)
- Need to iterate through neighbors frequently
- Memory efficiency is important

**Use Adjacency Matrix when:**
- Dense graphs (many edges)
- Need fast edge existence checks
- Simple implementation is preferred

---

## Graph Implementation

### Python Implementation

#### Adjacency List Representation
```python
class Vertex:
    def __init__(self, value):
        self.value = value
        self.edges = {}  # neighbor -> weight
    
    def add_edge(self, vertex, weight=1):
        """Add an edge to another vertex"""
        self.edges[vertex] = weight
    
    def get_edges(self):
        """Get all neighboring vertices"""
        return list(self.edges.keys())

class Graph:
    def __init__(self, directed=False):
        self.graph_dict = {}  # vertex_value -> Vertex object
        self.directed = directed
    
    def add_vertex(self, vertex):
        """Add a vertex to the graph"""
        self.graph_dict[vertex.value] = vertex
    
    def add_edge(self, from_vertex, to_vertex, weight=1):
        """Add an edge between vertices"""
        self.graph_dict[from_vertex.value].add_edge(to_vertex.value, weight)
        
        # For undirected graphs, add edge in both directions
        if not self.directed:
            self.graph_dict[to_vertex.value].add_edge(from_vertex.value, weight)
    
    def find_path_bfs(self, start_vertex, end_vertex):
        """Find path using breadth-first search"""
        queue = [start_vertex]
        visited = set()
        parent = {start_vertex: None}
        
        while queue:
            current_vertex = queue.pop(0)
            
            if current_vertex in visited:
                continue
                
            visited.add(current_vertex)
            print(f"Visiting {current_vertex}")
            
            if current_vertex == end_vertex:
                # Reconstruct path
                path = []
                while current_vertex is not None:
                    path.append(current_vertex)
                    current_vertex = parent[current_vertex]
                return path[::-1]  # Reverse to get start->end
            
            # Add unvisited neighbors to queue
            neighbors = self.graph_dict[current_vertex].edges.keys()
            for neighbor in neighbors:
                if neighbor not in visited and neighbor not in parent:
                    parent[neighbor] = current_vertex
                    queue.append(neighbor)
        
        return None  # No path found
    
    def find_path_dfs(self, start_vertex, end_vertex, visited=None, path=None):
        """Find path using depth-first search"""
        if visited is None:
            visited = set()
        if path is None:
            path = []
        
        visited.add(start_vertex)
        path.append(start_vertex)
        print(f"Visiting {start_vertex}")
        
        if start_vertex == end_vertex:
            return path[:]  # Return copy of path
        
        neighbors = self.graph_dict[start_vertex].edges.keys()
        for neighbor in neighbors:
            if neighbor not in visited:
                result = self.find_path_dfs(neighbor, end_vertex, visited.copy(), path[:])
                if result:
                    return result
        
        return None  # No path found
    
    def print_graph(self):
        """Print the graph structure"""
        for vertex_value in self.graph_dict:
            vertex = self.graph_dict[vertex_value]
            print(f"{vertex_value} connects to:")
            
            if len(vertex.edges) == 0:
                print("  No connections")
            else:
                for neighbor, weight in vertex.edges.items():
                    print(f"  -> {neighbor} (weight: {weight})")

# Example usage
print("Creating a transportation network:")
graph = Graph(directed=False)

# Create vertices (cities)
cities = ["New York", "Los Angeles", "Chicago", "Houston", "Phoenix"]
city_vertices = {}

for city in cities:
    vertex = Vertex(city)
    city_vertices[city] = vertex
    graph.add_vertex(vertex)

# Add edges (routes with distances)
routes = [
    ("New York", "Chicago", 790),
    ("New York", "Houston", 1630),
    ("Los Angeles", "Phoenix", 370),
    ("Los Angeles", "Houston", 1550),
    ("Chicago", "Houston", 1080),
    ("Chicago", "Phoenix", 1440)
]

for from_city, to_city, distance in routes:
    graph.add_edge(city_vertices[from_city], city_vertices[to_city], distance)

graph.print_graph()

# Find paths
print(f"\nBFS path from New York to Phoenix:")
bfs_path = graph.find_path_bfs("New York", "Phoenix")
print(f"Path: {' -> '.join(bfs_path) if bfs_path else 'No path found'}")

print(f"\nDFS path from New York to Phoenix:")
dfs_path = graph.find_path_dfs("New York", "Phoenix")
print(f"Path: {' -> '.join(dfs_path) if dfs_path else 'No path found'}")
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
    
    public void AddEdge(Vertex<T> fromVertex, Vertex<T> toVertex, int weight = 1)
    {
        graphDict[fromVertex.Value].AddEdge(toVertex.Value, weight);
        
        // For undirected graphs, add edge in both directions
        if (!directed)
        {
            graphDict[toVertex.Value].AddEdge(fromVertex.Value, weight);
        }
    }
    
    public List<T> FindPathBFS(T startVertex, T endVertex)
    {
        var queue = new Queue<T>();
        var visited = new HashSet<T>();
        var parent = new Dictionary<T, T>();
        
        queue.Enqueue(startVertex);
        parent[startVertex] = default(T);
        
        while (queue.Count > 0)
        {
            T currentVertex = queue.Dequeue();
            
            if (visited.Contains(currentVertex))
                continue;
                
            visited.Add(currentVertex);
            Console.WriteLine($"Visiting {currentVertex}");
            
            if (currentVertex.Equals(endVertex))
            {
                // Reconstruct path
                var path = new List<T>();
                var current = currentVertex;
                
                while (!EqualityComparer<T>.Default.Equals(current, default(T)))
                {
                    path.Add(current);
                    current = parent[current];
                }
                
                path.Reverse();
                return path;
            }
            
            // Add unvisited neighbors to queue
            var neighbors = graphDict[currentVertex].Edges.Keys;
            foreach (var neighbor in neighbors)
            {
                if (!visited.Contains(neighbor) && !parent.ContainsKey(neighbor))
                {
                    parent[neighbor] = currentVertex;
                    queue.Enqueue(neighbor);
                }
            }
        }
        
        return null; // No path found
    }
    
    public List<T> FindPathDFS(T startVertex, T endVertex, HashSet<T> visited = null, List<T> path = null)
    {
        if (visited == null)
            visited = new HashSet<T>();
        if (path == null)
            path = new List<T>();
        
        visited.Add(startVertex);
        path.Add(startVertex);
        Console.WriteLine($"Visiting {startVertex}");
        
        if (startVertex.Equals(endVertex))
        {
            return new List<T>(path); // Return copy of path
        }
        
        var neighbors = graphDict[startVertex].Edges.Keys;
        foreach (var neighbor in neighbors)
        {
            if (!visited.Contains(neighbor))
            {
                var newVisited = new HashSet<T>(visited);
                var newPath = new List<T>(path);
                var result = FindPathDFS(neighbor, endVertex, newVisited, newPath);
                if (result != null)
                {
                    return result;
                }
            }
        }
        
        return null; // No path found
    }
    
    public void PrintGraph()
    {
        foreach (var kvp in graphDict)
        {
            var vertex = kvp.Value;
            Console.WriteLine($"{vertex.Value} connects to:");
            
            if (vertex.Edges.Count == 0)
            {
                Console.WriteLine("  No connections");
            }
            else
            {
                foreach (var edge in vertex.Edges)
                {
                    Console.WriteLine($"  -> {edge.Key} (weight: {edge.Value})");
                }
            }
        }
    }
}

// Example usage
Console.WriteLine("Creating a transportation network:");
var graph = new Graph<string>(directed: false);

// Create vertices (cities)
string[] cities = {"New York", "Los Angeles", "Chicago", "Houston", "Phoenix"};
var cityVertices = new Dictionary<string, Vertex<string>>();

foreach (var city in cities)
{
    var vertex = new Vertex<string>(city);
    cityVertices[city] = vertex;
    graph.AddVertex(vertex);
}

// Add edges (routes with distances)
var routes = new[]
{
    ("New York", "Chicago", 790),
    ("New York", "Houston", 1630),
    ("Los Angeles", "Phoenix", 370),
    ("Los Angeles", "Houston", 1550),
    ("Chicago", "Houston", 1080),
    ("Chicago", "Phoenix", 1440)
};

foreach (var (fromCity, toCity, distance) in routes)
{
    graph.AddEdge(cityVertices[fromCity], cityVertices[toCity], distance);
}

graph.PrintGraph();

// Find paths
Console.WriteLine("\nBFS path from New York to Phoenix:");
var bfsPath = graph.FindPathBFS("New York", "Phoenix");
Console.WriteLine($"Path: {(bfsPath != null ? string.Join(" -> ", bfsPath) : "No path found")}");

Console.WriteLine("\nDFS path from New York to Phoenix:");
var dfsPath = graph.FindPathDFS("New York", "Phoenix");
Console.WriteLine($"Path: {(dfsPath != null ? string.Join(" -> ", dfsPath) : "No path found")}");
```

---

## Graph Search Algorithms

### Depth-First Search (DFS)

**When to use:**
- Finding connected components
- Cycle detection
- Topological sorting
- Maze solving
- Finding any path (not necessarily shortest)

**Time Complexity:** O(V + E)

#### DFS Applications

##### 1. Detect Cycle in Undirected Graph
```python
def has_cycle_undirected(graph, start_vertex):
    """Detect cycle in undirected graph using DFS"""
    visited = set()
    
    def dfs(vertex, parent):
        visited.add(vertex)
        
        for neighbor in graph.graph_dict[vertex].edges:
            if neighbor not in visited:
                if dfs(neighbor, vertex):
                    return True
            elif neighbor != parent:
                # Found back edge (cycle)
                return True
        
        return False
    
    return dfs(start_vertex, None)
```

##### 2. Topological Sort
```python
def topological_sort(graph):
    """Topological sort using DFS (for directed acyclic graphs)"""
    visited = set()
    stack = []
    
    def dfs(vertex):
        visited.add(vertex)
        
        for neighbor in graph.graph_dict[vertex].edges:
            if neighbor not in visited:
                dfs(neighbor)
        
        stack.append(vertex)  # Add to stack after visiting all children
    
    # Visit all vertices
    for vertex in graph.graph_dict:
        if vertex not in visited:
            dfs(vertex)
    
    return stack[::-1]  # Reverse to get topological order
```

### Breadth-First Search (BFS)

**When to use:**
- Finding shortest path in unweighted graphs
- Level-order traversal
- Finding minimum steps/moves
- Web crawling
- Social network analysis (degrees of separation)

**Time Complexity:** O(V + E)

#### BFS Applications

##### 1. Shortest Path in Unweighted Graph
```python
from collections import deque

def shortest_path_bfs(graph, start, end):
    """Find shortest path using BFS"""
    queue = deque([(start, [start])])
    visited = set([start])
    
    while queue:
        vertex, path = queue.popleft()
        
        if vertex == end:
            return path
        
        for neighbor in graph.graph_dict[vertex].edges:
            if neighbor not in visited:
                visited.add(neighbor)
                new_path = path + [neighbor]
                queue.append((neighbor, new_path))
    
    return None  # No path found
```

##### 2. Count Connected Components
```python
def count_connected_components(graph):
    """Count number of connected components using BFS"""
    visited = set()
    components = 0
    
    def bfs(start_vertex):
        queue = deque([start_vertex])
        visited.add(start_vertex)
        
        while queue:
            vertex = queue.popleft()
            
            for neighbor in graph.graph_dict[vertex].edges:
                if neighbor not in visited:
                    visited.add(neighbor)
                    queue.append(neighbor)
    
    for vertex in graph.graph_dict:
        if vertex not in visited:
            bfs(vertex)
            components += 1
    
    return components
```

---

## Advanced Graph Algorithms

### Dijkstra's Algorithm (Shortest Path with Weights)

**When to use:**
- Finding shortest path in weighted graphs with non-negative weights
- GPS navigation systems
- Network routing protocols
- Cost optimization problems

**Time Complexity:** O((V + E) log V) with binary heap

#### Python Implementation
```python
import heapq
from math import inf

def dijkstra(graph, start_vertex):
    """Find shortest paths from start vertex to all other vertices"""
    distances = {vertex: inf for vertex in graph.graph_dict}
    distances[start_vertex] = 0
    
    # Priority queue: (distance, vertex)
    priority_queue = [(0, start_vertex)]
    visited = set()
    previous = {vertex: None for vertex in graph.graph_dict}
    
    while priority_queue:
        current_distance, current_vertex = heapq.heappop(priority_queue)
        
        if current_vertex in visited:
            continue
            
        visited.add(current_vertex)
        
        # Check all neighbors
        for neighbor, weight in graph.graph_dict[current_vertex].edges.items():
            distance = current_distance + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                previous[neighbor] = current_vertex
                heapq.heappush(priority_queue, (distance, neighbor))
    
    return distances, previous

def get_shortest_path(previous, start, end):
    """Reconstruct shortest path from previous pointers"""
    path = []
    current = end
    
    while current is not None:
        path.append(current)
        current = previous[current]
    
    if path[-1] != start:
        return None  # No path exists
    
    return path[::-1]  # Reverse to get start->end path

# Example usage with weighted graph
weighted_graph = Graph(directed=False)
cities = ["A", "B", "C", "D", "E"]

for city in cities:
    weighted_graph.add_vertex(Vertex(city))

# Add weighted edges
edges = [("A", "B", 4), ("A", "C", 2), ("B", "C", 1), ("B", "D", 5), 
         ("C", "D", 8), ("C", "E", 10), ("D", "E", 2)]

for from_city, to_city, weight in edges:
    weighted_graph.add_edge(Vertex(from_city), Vertex(to_city), weight)

distances, previous = dijkstra(weighted_graph, "A")
print("Shortest distances from A:")
for vertex, distance in distances.items():
    path = get_shortest_path(previous, "A", vertex)
    print(f"  To {vertex}: {distance} via {' -> '.join(path) if path else 'No path'}")
```

#### C# Implementation
```csharp
public static class Dijkstra
{
    public static (Dictionary<T, double> distances, Dictionary<T, T> previous) 
        FindShortestPaths<T>(Graph<T> graph, T startVertex)
    {
        var distances = new Dictionary<T, double>();
        var previous = new Dictionary<T, T>();
        var priorityQueue = new SortedSet<(double distance, T vertex)>();
        var visited = new HashSet<T>();
        
        // Initialize distances
        foreach (var vertex in graph.GetVertices())
        {
            distances[vertex] = double.PositiveInfinity;
            previous[vertex] = default(T);
        }
        distances[startVertex] = 0;
        
        priorityQueue.Add((0, startVertex));
        
        while (priorityQueue.Count > 0)
        {
            var (currentDistance, currentVertex) = priorityQueue.Min;
            priorityQueue.Remove(priorityQueue.Min);
            
            if (visited.Contains(currentVertex))
                continue;
                
            visited.Add(currentVertex);
            
            // Check all neighbors
            var neighbors = graph.GetNeighbors(currentVertex);
            foreach (var (neighbor, weight) in neighbors)
            {
                double distance = currentDistance + weight;
                
                if (distance < distances[neighbor])
                {
                    distances[neighbor] = distance;
                    previous[neighbor] = currentVertex;
                    priorityQueue.Add((distance, neighbor));
                }
            }
        }
        
        return (distances, previous);
    }
    
    public static List<T> GetShortestPath<T>(Dictionary<T, T> previous, T start, T end)
    {
        var path = new List<T>();
        var current = end;
        
        while (!EqualityComparer<T>.Default.Equals(current, default(T)))
        {
            path.Add(current);
            current = previous[current];
        }
        
        if (!path[path.Count - 1].Equals(start))
            return null; // No path exists
        
        path.Reverse();
        return path;
    }
}
```

### A* Algorithm (Heuristic Search)

**When to use:**
- Pathfinding with known goal location
- Game AI movement
- GPS navigation with traffic considerations
- Robotics path planning

A* uses a heuristic function to guide search toward the goal, making it more efficient than Dijkstra for single-target searches.

#### Python Implementation
```python
import heapq
from math import sqrt

def manhattan_distance(pos1, pos2):
    """Manhattan distance heuristic for grid-based pathfinding"""
    return abs(pos1[0] - pos2[0]) + abs(pos1[1] - pos2[1])

def euclidean_distance(pos1, pos2):
    """Euclidean distance heuristic"""
    return sqrt((pos1[0] - pos2[0])**2 + (pos1[1] - pos2[1])**2)

def a_star(graph, start, goal, positions, heuristic=manhattan_distance):
    """A* pathfinding algorithm"""
    open_set = [(0, start)]
    came_from = {}
    
    g_score = {vertex: float('inf') for vertex in graph.graph_dict}
    g_score[start] = 0
    
    f_score = {vertex: float('inf') for vertex in graph.graph_dict}
    f_score[start] = heuristic(positions[start], positions[goal])
    
    while open_set:
        current = heapq.heappop(open_set)[1]
        
        if current == goal:
            # Reconstruct path
            path = []
            while current in came_from:
                path.append(current)
                current = came_from[current]
            path.append(start)
            return path[::-1]
        
        for neighbor, weight in graph.graph_dict[current].edges.items():
            tentative_g_score = g_score[current] + weight
            
            if tentative_g_score < g_score[neighbor]:
                came_from[neighbor] = current
                g_score[neighbor] = tentative_g_score
                f_score[neighbor] = g_score[neighbor] + heuristic(positions[neighbor], positions[goal])
                heapq.heappush(open_set, (f_score[neighbor], neighbor))
    
    return None  # No path found

# Example usage for grid pathfinding
grid_graph = Graph(directed=False)
positions = {
    'A': (0, 0), 'B': (1, 0), 'C': (2, 0),
    'D': (0, 1), 'E': (1, 1), 'F': (2, 1),
    'G': (0, 2), 'H': (1, 2), 'I': (2, 2)
}

# Create grid connections (each cell connects to adjacent cells)
for pos in positions:
    grid_graph.add_vertex(Vertex(pos))

# Add edges between adjacent cells
adjacencies = [
    ('A', 'B'), ('B', 'C'), ('A', 'D'), ('B', 'E'), ('C', 'F'),
    ('D', 'E'), ('E', 'F'), ('D', 'G'), ('E', 'H'), ('F', 'I'),
    ('G', 'H'), ('H', 'I')
]

for from_cell, to_cell in adjacencies:
    grid_graph.add_edge(Vertex(from_cell), Vertex(to_cell), 1)

# Find path from A to I
path = a_star(grid_graph, 'A', 'I', positions)
print(f"A* path from A to I: {' -> '.join(path) if path else 'No path'}")
```

## Common Interview Problems

### 1. Number of Islands (2D Grid)
```python
def num_islands(grid):
    """Count number of islands using DFS"""
    if not grid or not grid[0]:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    visited = set()
    islands = 0
    
    def dfs(r, c):
        if (r, c) in visited or r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] == '0':
            return
        
        visited.add((r, c))
        # Visit all 4 directions
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1' and (r, c) not in visited:
                dfs(r, c)
                islands += 1
    
    return islands
```

### 2. Course Schedule (Cycle Detection)
```python
def can_finish_courses(num_courses, prerequisites):
    """Detect if course schedule is possible (no cycles)"""
    # Build adjacency list
    graph = [[] for _ in range(num_courses)]
    for course, prereq in prerequisites:
        graph[prereq].append(course)
    
    # 0 = unvisited, 1 = visiting, 2 = visited
    state = [0] * num_courses
    
    def has_cycle(course):
        if state[course] == 1:  # Currently visiting - cycle detected
            return True
        if state[course] == 2:  # Already processed
            return False
        
        state[course] = 1  # Mark as visiting
        for next_course in graph[course]:
            if has_cycle(next_course):
                return True
        
        state[course] = 2  # Mark as visited
        return False
    
    for course in range(num_courses):
        if state[course] == 0:
            if has_cycle(course):
                return False
    
    return True
```

### 3. Clone Graph
```python
def clone_graph(node):
    """Deep clone a graph"""
    if not node:
        return None
    
    clones = {}  # original -> clone mapping
    
    def dfs(original):
        if original in clones:
            return clones[original]
        
        clone = Node(original.val)
        clones[original] = clone
        
        for neighbor in original.neighbors:
            clone.neighbors.append(dfs(neighbor))
        
        return clone
    
    return dfs(node)
```

## Graph Applications in Real World

**Social Networks:** Friend recommendations, influence analysis, community detection
**Maps & Navigation:** Shortest path, traffic routing, location services  
**Web:** PageRank, link analysis, web crawling
**Compilers:** Dependency analysis, optimization, register allocation
**Biology:** Protein interactions, gene networks, phylogenetic trees
**Finance:** Risk analysis, fraud detection, market networks
**Games:** AI pathfinding, state space search, decision trees

## Modern Usage

**Python:** Use `networkx` library for complex graph operations
**C#:** Use graph libraries or implement custom structures for specific needs
**Databases:** Neo4j, Amazon Neptune for large-scale graph data

**Interview focus:** Understand BFS/DFS deeply, know when to use each, implement basic graph operations, recognize graph problems in disguise (trees, 2D grids, state spaces)T vertex, int weight = 1)
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
    
    public void AddEdge(