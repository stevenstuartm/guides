# Object-Oriented Programming Study Guide

## Table of Contents

1. [Fundamentals](#fundamentals)
   - [Core OOP Pillars](#core-oop-pillars)
   - [Key Concepts](#key-concepts)
2. [SOLID Principles](#solid-principles)
3. [Design Patterns](#design-patterns)
   - [GoF Pattern Categories](#gof-pattern-categories)
   - [Creational Patterns](#creational-patterns)
   - [Structural Patterns](#structural-patterns)
   - [Behavioral Patterns](#behavioral-patterns)
4. [Modern Best Practices](#modern-best-practices)
5. [Quick Reference](#quick-reference)

---

## Fundamentals

### Core OOP Pillars

**Abstraction**
- Hide complex implementation details while exposing a simple interface
- Focus on what an object does rather than how it does it
- Use abstract classes and interfaces to define contracts

**Encapsulation**
- Bundle data and methods that operate on that data within a single unit
- Control access through visibility modifiers (private, protected, public)
- Protect internal state from external interference

**Inheritance**
- Create new classes based on existing classes
- Enables code reuse and establishes "is-a" relationships
- Use composition over inheritance when possible (modern best practice)

**Polymorphism**
- Objects of different types can be treated as instances of the same type
- Method overriding enables runtime behavior selection
- Interfaces enable compile-time polymorphism

### Key Concepts

**Covariance**
- Use a more derived type than originally specified
- Example: `IEnumerable<Derived>` can be assigned to `IEnumerable<Base>`

**Contravariance**
- Use a more generic type than originally specified
- Example: `Action<Base>` can be assigned to `Action<Derived>`

**Invariance**
- Use only the originally specified type
- Example: `List<Base>` cannot be assigned to `List<Derived>` or vice versa

---

## SOLID Principles

> **SOLID** principles create maintainable, scalable, and flexible software systems that are easier to understand, modify, and extend over time.

### S - Single Responsibility Principle (SRP)

**Definition**: A class should have only one reason to change, meaning it should have only one job or responsibility.

**Benefits**:
- Easier to test and maintain
- Reduces coupling between components  
- Simplifies debugging and troubleshooting

**Example Violation**:
```csharp
// BAD: Multiple responsibilities
public class Employee 
{
    public void CalculateSalary() { /* salary logic */ }
    public void GeneratePayrollReport() { /* reporting logic */ }
    public void SaveToDatabase() { /* persistence logic */ }
}
```

**Better Approach**:
```csharp
// GOOD: Single responsibilities
public class Employee { /* employee data only */ }
public class SalaryCalculator { /* salary logic */ }
public class PayrollReporter { /* reporting logic */ }
public class EmployeeRepository { /* persistence logic */ }
```

### O - Open-Closed Principle (OCP)

**Definition**: Software entities should be open for extension but closed for modification.

**Benefits**:
- Add new features without changing existing code
- Reduces risk of introducing bugs in working code
- Promotes code reusability

**Implementation**: Use abstractions, interfaces, and inheritance to enable extension points.

### L - Liskov Substitution Principle (LSP)

**Definition**: Objects of a superclass should be replaceable with objects of a subclass without altering the correctness of the program.

**Benefits**:
- Ensures polymorphism works correctly
- Maintains contract integrity
- Enables reliable inheritance hierarchies

**Key Rule**: Subclasses must strengthen postconditions and weaken preconditions.

### I - Interface Segregation Principle (ISP)

**Definition**: Clients should not be forced to depend on interfaces they don't use.

**Benefits**:
- Reduces coupling between components
- Avoids unnecessary dependencies
- Enables more targeted implementations

**Modern Examples**:
- **Specification Pattern**: `ISpecification<T>` with `bool IsSatisfied(T item)`
- **Extension Methods**: Add functionality without modifying existing interfaces
- **Small, Focused Interfaces**: Prefer multiple specific interfaces over large general ones

### D - Dependency Inversion Principle (DIP)

**Definition**: High-level modules should not depend on low-level modules. Both should depend on abstractions.

**Benefits**:
- Improves testability through dependency injection
- Reduces coupling between layers
- Enables flexible architecture

**Implementation**: Use dependency injection containers and inversion of control (IoC).

---

## Design Patterns

> Design patterns are reusable solutions to common problems in software design. The Gang of Four (GoF) identified 23 fundamental patterns that remain relevant today.

### GoF Pattern Categories

**Creational Patterns**
- Focus on object creation mechanisms
- Provide flexibility in how objects are instantiated
- Examples: Factory, Builder, Singleton

**Structural Patterns** 
- Deal with object composition and relationships
- Help form larger structures while maintaining flexibility
- Examples: Adapter, Decorator, Facade

**Behavioral Patterns**
- Focus on communication between objects
- Define how objects interact and distribute responsibilities
- Examples: Observer, Strategy, Command

---

## Creational Patterns

### Builder Pattern

**Purpose**: Construct complex objects step by step, separating construction from representation.

**Modern Variations**:

**Fluent Builder**
```csharp
public class HtmlElement
{
    public string TagName { get; set; }
    public string Content { get; set; }
    public Dictionary<string, string> Attributes { get; set; } = new();
    public List<HtmlElement> Children { get; set; } = new();
}

public class HtmlBuilder
{
    private readonly HtmlElement element = new();
    
    public HtmlBuilder SetTag(string tagName)
    {
        element.TagName = tagName;
        return this;
    }
    
    public HtmlBuilder AddClass(string className)
    {
        element.Attributes["class"] = element.Attributes.ContainsKey("class") 
            ? $"{element.Attributes["class"]} {className}" 
            : className;
        return this;
    }
    
    public HtmlBuilder AddContent(string content)
    {
        element.Content = content;
        return this;
    }
    
    public HtmlElement Build() => element;
}

// Usage: 
var html = new HtmlBuilder()
    .SetTag("div")
    .AddClass("container")
    .AddContent("Hello World")
    .Build();
```

**Stepwise Builder**
```csharp
public interface ICarType
{
    IWheelSize OfType(string carType);
}

public interface IWheelSize  
{
    IBuildable WithWheels(int wheelSize);
}

public interface IBuildable
{
    Car Build();
}

public class CarBuilder : ICarType, IWheelSize, IBuildable
{
    private readonly Car car = new();
    
    public static ICarType Create() => new CarBuilder();
    
    public IWheelSize OfType(string carType)
    {
        car.Type = carType;
        return this;
    }
    
    public IBuildable WithWheels(int wheelSize)
    {
        car.WheelSize = wheelSize;
        return this;
    }
    
    public Car Build() => car;
}

// Usage: CarBuilder.Create().OfType("SUV").WithWheels(18).Build()
```

**Functional Builder**
```csharp
public class PersonBuilder
{
    private readonly List<Action<Person>> actions = new();
    
    public PersonBuilder Called(string name)
    {
        actions.Add(p => p.Name = name);
        return this;
    }
    
    public PersonBuilder WorksAs(string position)
    {
        actions.Add(p => p.Position = position);
        return this;
    }
    
    public PersonBuilder Earning(decimal salary)
    {
        actions.Add(p => p.Salary = salary);
        return this;
    }
    
    public Person Build()
    {
        var person = new Person();
        actions.ForEach(a => a(person));
        return person;
    }
}

// Usage: 
var person = new PersonBuilder()
    .Called("John")
    .WorksAs("Developer")
    .Earning(75000)
    .Build();
```

### Factory Patterns

**Factory Method**
```csharp
public class Person
{
    public string Name { get; set; }
    public string Role { get; set; }
    public decimal Salary { get; set; }
    
    private Person() { } // Private constructor
    
    public static Person NewCustomer(string name)
    {
        return new Person { Name = name, Role = "Customer", Salary = 0 };
    }
    
    public static Person NewEmployee(string name, decimal salary)
    {
        return new Person { Name = name, Role = "Employee", Salary = salary };
    }
}

// Usage:
var customer = Person.NewCustomer("Alice");
var employee = Person.NewEmployee("Bob", 50000);
```

**Abstract Factory**
```csharp
// Abstract products
public interface IButton
{
    void Render();
}

public interface ICheckbox  
{
    void Render();
}

// Concrete products for Windows
public class WindowsButton : IButton
{
    public void Render() => Console.WriteLine("Rendering Windows button");
}

public class WindowsCheckbox : ICheckbox
{
    public void Render() => Console.WriteLine("Rendering Windows checkbox");
}

// Concrete products for Mac
public class MacButton : IButton
{
    public void Render() => Console.WriteLine("Rendering Mac button");
}

public class MacCheckbox : ICheckbox
{
    public void Render() => Console.WriteLine("Rendering Mac checkbox");
}

// Abstract factory
public interface IUIFactory
{
    IButton CreateButton();
    ICheckbox CreateCheckbox();
}

// Concrete factories
public class WindowsUIFactory : IUIFactory
{
    public IButton CreateButton() => new WindowsButton();
    public ICheckbox CreateCheckbox() => new WindowsCheckbox();
}

public class MacUIFactory : IUIFactory
{
    public IButton CreateButton() => new MacButton();
    public ICheckbox CreateCheckbox() => new MacCheckbox();
}

// Usage:
IUIFactory factory = Environment.OSVersion.Platform == PlatformID.Win32NT 
    ? new WindowsUIFactory() 
    : new MacUIFactory();

var button = factory.CreateButton();
var checkbox = factory.CreateCheckbox();
```

**Async Factory**
```csharp
public class DatabaseConnection
{
    private string connectionString;
    
    private DatabaseConnection(string connectionString)
    {
        this.connectionString = connectionString;
    }
    
    public static async Task<DatabaseConnection> CreateAsync(string server, string database)
    {
        // Simulate async connection validation
        await Task.Delay(100);
        
        var connectionString = $"Server={server};Database={database}";
        var connection = new DatabaseConnection(connectionString);
        
        // Additional async initialization
        await connection.InitializeAsync();
        return connection;
    }
    
    private async Task InitializeAsync()
    {
        // Async initialization logic
        await Task.Delay(50);
    }
}

// Usage:
var connection = await DatabaseConnection.CreateAsync("localhost", "MyDB");
```

### Singleton Pattern

**⚠️ Modern Warning**: Traditional singleton implementations are difficult to test and violate dependency inversion.

**Traditional Singleton (Avoid)**
```csharp
public sealed class Database
{
    private static readonly Lazy<Database> instance = new(() => new Database());
    public static Database Instance => instance.Value;
    
    private Database() { }
    
    public void Query(string sql) { /* implementation */ }
}

// Problem: Hard to test, violates DI
```

**Recommended Approaches**:

**Dependency Injection (Preferred)**
```csharp
public interface IDatabase
{
    void Query(string sql);
}

public class Database : IDatabase
{
    public void Query(string sql) { /* implementation */ }
}

// In Startup.cs or Program.cs
services.AddSingleton<IDatabase, Database>();

// In consuming class
public class UserService
{
    private readonly IDatabase database;
    
    public UserService(IDatabase database)
    {
        this.database = database;
    }
}
```

**Thread-Safe Lazy Initialization**
```csharp
public class ConfigurationManager
{
    private static readonly Lazy<ConfigurationManager> instance = 
        new(() => new ConfigurationManager());
    
    public static ConfigurationManager Instance => instance.Value;
    
    private readonly Dictionary<string, string> settings = new();
    
    private ConfigurationManager()
    {
        LoadConfiguration();
    }
    
    private void LoadConfiguration()
    {
        // Load from file, database, etc.
    }
    
    public string GetSetting(string key) => settings.TryGetValue(key, out var value) ? value : "";
}
```

**Alternative Patterns**:

**Per-Thread Singleton**
```csharp
public class ThreadLocalSingleton
{
    private static readonly ThreadLocal<ThreadLocalSingleton> instance = 
        new(() => new ThreadLocalSingleton());
    
    public static ThreadLocalSingleton Instance => instance.Value;
    
    private ThreadLocalSingleton() { }
}
```

**Ambient Context**
```csharp
public class DatabaseContext : IDisposable
{
    private static readonly Stack<DatabaseContext> contexts = new();
    
    public static DatabaseContext Current => contexts.Count > 0 ? contexts.Peek() : null;
    
    public DatabaseContext()
    {
        contexts.Push(this);
    }
    
    public void Dispose()
    {
        if (contexts.Count > 0 && contexts.Peek() == this)
            contexts.Pop();
    }
}

// Usage:
using (new DatabaseContext())
{
    var current = DatabaseContext.Current; // Gets the current context
    // Nested contexts work automatically
    using (new DatabaseContext())
    {
        var nested = DatabaseContext.Current; // Gets the nested context
    }
    // Back to original context
}
```

### Prototype Pattern

**Purpose**: Create new objects by copying existing instances rather than creating from scratch.

```csharp
public interface IPrototype<T>
{
    T Clone();
}

public class Person : IPrototype<Person>
{
    public string Name { get; set; }
    public int Age { get; set; }
    public Address Address { get; set; }
    
    // Deep clone implementation
    public Person Clone()
    {
        return new Person
        {
            Name = this.Name,
            Age = this.Age,
            Address = this.Address?.Clone() // Assuming Address also implements IPrototype
        };
    }
}

public class Address : IPrototype<Address>
{
    public string Street { get; set; }
    public string City { get; set; }
    
    public Address Clone()
    {
        return new Address
        {
            Street = this.Street,
            City = this.City
        };
    }
}

// Modern JSON-based approach (simpler but requires serializable types)
public static class PrototypeHelper
{
    public static T DeepClone<T>(this T source) where T : class
    {
        var serialized = JsonSerializer.Serialize(source);
        return JsonSerializer.Deserialize<T>(serialized);
    }
}

// Usage:
var original = new Person { Name = "John", Age = 30, Address = new Address { Street = "123 Main St", City = "NYC" } };
var clone1 = original.Clone(); // Using interface
var clone2 = original.DeepClone(); // Using JSON serialization
```

---

## Structural Patterns

### Adapter Pattern

**Purpose**: Allow incompatible interfaces to work together by wrapping existing functionality.

```csharp
// Legacy class we cannot modify
public class LegacyRectangle
{
    public int X { get; set; }
    public int Y { get; set; }
    public int Width { get; set; }
    public int Height { get; set; }
    
    public void LegacyDraw()
    {
        Console.WriteLine($"Drawing rectangle at ({X},{Y}) with size {Width}x{Height}");
    }
}

// Modern interface we want to use
public interface IShape
{
    void Draw();
    void Move(int x, int y);
    void Resize(int width, int height);
}

// Adapter that makes LegacyRectangle work with IShape
public class RectangleAdapter : IShape
{
    private readonly LegacyRectangle legacyRectangle;
    
    public RectangleAdapter(LegacyRectangle legacyRectangle)
    {
        this.legacyRectangle = legacyRectangle;
    }
    
    public void Draw()
    {
        legacyRectangle.LegacyDraw();
    }
    
    public void Move(int x, int y)
    {
        legacyRectangle.X = x;
        legacyRectangle.Y = y;
    }
    
    public void Resize(int width, int height)
    {
        legacyRectangle.Width = width;
        legacyRectangle.Height = height;
    }
}

// Usage:
var legacyRect = new LegacyRectangle { X = 10, Y = 20, Width = 100, Height = 50 };
IShape shape = new RectangleAdapter(legacyRect);
shape.Draw();
shape.Move(30, 40);
```

### Bridge Pattern

**Purpose**: Separate abstraction from implementation to avoid Cartesian product complexity.

```csharp
// Implementation interface
public interface IRenderer
{
    void RenderCircle(float radius);
    void RenderRectangle(float width, float height);
}

// Concrete implementations
public class PixelRenderer : IRenderer
{
    public void RenderCircle(float radius)
    {
        Console.WriteLine($"Drawing pixels for circle with radius {radius}");
    }
    
    public void RenderRectangle(float width, float height)
    {
        Console.WriteLine($"Drawing pixels for rectangle {width}x{height}");
    }
}

public class VectorRenderer : IRenderer
{
    public void RenderCircle(float radius)
    {
        Console.WriteLine($"Drawing circle vector with radius {radius}");
    }
    
    public void RenderRectangle(float width, float height)
    {
        Console.WriteLine($"Drawing rectangle vector {width}x{height}");
    }
}

// Abstraction
public abstract class Shape
{
    protected IRenderer renderer;
    
    protected Shape(IRenderer renderer)
    {
        this.renderer = renderer;
    }
    
    public abstract void Draw();
}

// Refined abstractions
public class Circle : Shape
{
    private float radius;
    
    public Circle(IRenderer renderer, float radius) : base(renderer)
    {
        this.radius = radius;
    }
    
    public override void Draw()
    {
        renderer.RenderCircle(radius);
    }
}

public class Rectangle : Shape
{
    private float width, height;
    
    public Rectangle(IRenderer renderer, float width, float height) : base(renderer)
    {
        this.width = width;
        this.height = height;
    }
    
    public override void Draw()
    {
        renderer.RenderRectangle(width, height);
    }
}

// Usage:
IRenderer pixelRenderer = new PixelRenderer();
IRenderer vectorRenderer = new VectorRenderer();

var shapes = new Shape[]
{
    new Circle(pixelRenderer, 5),
    new Rectangle(vectorRenderer, 10, 15),
    new Circle(vectorRenderer, 3)
};

foreach (var shape in shapes)
{
    shape.Draw();
}
```

### Composite Pattern

**Purpose**: Treat individual objects and collections uniformly through a common interface.

```csharp
public abstract class GraphicObject
{
    public virtual string Name { get; set; } = "Group";
    public string Color { get; set; }
    
    private readonly Lazy<List<GraphicObject>> children = new(() => new List<GraphicObject>());
    public List<GraphicObject> Children => children.Value;
    
    public virtual void Draw()
    {
        Draw(0);
    }
    
    protected virtual void Draw(int depth)
    {
        Console.WriteLine($"{new string(' ', depth * 2)}{Name} (Color: {Color})");
        foreach (var child in Children)
        {
            child.Draw(depth + 1);
        }
    }
}

public class Circle : GraphicObject
{
    public override string Name { get; set; } = "Circle";
}

public class Square : GraphicObject
{
    public override string Name { get; set; } = "Square";
}

// Modern approach using IEnumerable for extension methods
public class GraphicGroup : IEnumerable<GraphicObject>
{
    private readonly List<GraphicObject> objects = new();
    public string Name { get; set; } = "Group";
    
    public void Add(GraphicObject obj) => objects.Add(obj);
    public void Remove(GraphicObject obj) => objects.Remove(obj);
    
    public IEnumerator<GraphicObject> GetEnumerator() => objects.GetEnumerator();
    IEnumerator IEnumerable.GetEnumerator() => GetEnumerator();
}

// Extension methods for operations on composites
public static class GraphicExtensions
{
    public static void DrawAll(this IEnumerable<GraphicObject> graphics)
    {
        foreach (var graphic in graphics)
        {
            graphic.Draw();
        }
    }
    
    public static void SetColor(this IEnumerable<GraphicObject> graphics, string color)
    {
        foreach (var graphic in graphics)
        {
            graphic.Color = color;
        }
    }
}

// Usage:
var drawing = new GraphicObject { Name = "My Drawing" };
drawing.Children.Add(new Square { Color = "Red" });
drawing.Children.Add(new Circle { Color = "Yellow" });

var group = new GraphicObject { Name = "Group 1" };
group.Children.Add(new Circle { Color = "Blue" });
group.Children.Add(new Square { Color = "Green" });

drawing.Children.Add(group);
drawing.Draw();
```

**Specification Pattern Example**
```csharp
public interface ISpecification<T>
{
    bool IsSatisfied(T candidate);
}

public class CompositeSpecification<T> : ISpecification<T>
{
    private readonly ISpecification<T>[] specifications;
    
    public CompositeSpecification(params ISpecification<T>[] specifications)
    {
        this.specifications = specifications;
    }
    
    public bool IsSatisfied(T candidate)
    {
        return specifications.All(spec => spec.IsSatisfied(candidate));
    }
}

public class AgeSpecification : ISpecification<Person>
{
    private readonly int minAge;
    
    public AgeSpecification(int minAge)
    {
        this.minAge = minAge;
    }
    
    public bool IsSatisfied(Person candidate)
    {
        return candidate.Age >= minAge;
    }
}

public class NameSpecification : ISpecification<Person>
{
    private readonly string namePattern;
    
    public NameSpecification(string namePattern)
    {
        this.namePattern = namePattern;
    }
    
    public bool IsSatisfied(Person candidate)
    {
        return candidate.Name.Contains(namePattern, StringComparison.OrdinalIgnoreCase);
    }
}

// Usage:
var people = new List<Person> { /* ... */ };
var adultJohns = new CompositeSpecification<Person>(
    new AgeSpecification(18),
    new NameSpecification("John")
);

var filteredPeople = people.Where(p => adultJohns.IsSatisfied(p)).ToList();
```

### Decorator Pattern

**Purpose**: Add behavior to objects dynamically without altering their structure.

```csharp
// Component interface
public interface ICoffee
{
    string GetDescription();
    decimal GetCost();
}

// Concrete component
public class SimpleCoffee : ICoffee
{
    public string GetDescription() => "Simple coffee";
    public decimal GetCost() => 2.00m;
}

// Base decorator
public abstract class CoffeeDecorator : ICoffee
{
    protected ICoffee coffee;
    
    protected CoffeeDecorator(ICoffee coffee)
    {
        this.coffee = coffee;
    }
    
    public virtual string GetDescription() => coffee.GetDescription();
    public virtual decimal GetCost() => coffee.GetCost();
}

// Concrete decorators
public class MilkDecorator : CoffeeDecorator
{
    public MilkDecorator(ICoffee coffee) : base(coffee) { }
    
    public override string GetDescription() => $"{coffee.GetDescription()}, milk";
    public override decimal GetCost() => coffee.GetCost() + 0.50m;
}

public class SugarDecorator : CoffeeDecorator
{
    public SugarDecorator(ICoffee coffee) : base(coffee) { }
    
    public override string GetDescription() => $"{coffee.GetDescription()}, sugar";
    public override decimal GetCost() => coffee.GetCost() + 0.25m;
}

public class WhipDecorator : CoffeeDecorator
{
    public WhipDecorator(ICoffee coffee) : base(coffee) { }
    
    public override string GetDescription() => $"{coffee.GetDescription()}, whip";
    public override decimal GetCost() => coffee.GetCost() + 0.75m;
}

// Usage:
ICoffee coffee = new SimpleCoffee();
coffee = new MilkDecorator(coffee);
coffee = new SugarDecorator(coffee);
coffee = new WhipDecorator(coffee);

Console.WriteLine($"{coffee.GetDescription()} costs ${coffee.GetCost()}");
// Output: Simple coffee, milk, sugar, whip costs $3.50
```

**Modern Functional Approach**
```csharp
public static class CoffeeExtensions
{
    public static ICoffee WithMilk(this ICoffee coffee) => new MilkDecorator(coffee);
    public static ICoffee WithSugar(this ICoffee coffee) => new SugarDecorator(coffee);
    public static ICoffee WithWhip(this ICoffee coffee) => new WhipDecorator(coffee);
}

// Fluent usage:
var coffee = new SimpleCoffee()
    .WithMilk()
    .WithSugar()
    .WithWhip();
```

### Facade Pattern  

**Purpose**: Provide a simplified interface to complex subsystems.

```csharp
// Complex subsystem classes
public class CPU
{
    public void Freeze() => Console.WriteLine("CPU: Freezing processor");
    public void Jump(long position) => Console.WriteLine($"CPU: Jumping to position {position}");
    public void Execute() => Console.WriteLine("CPU: Executing instructions");
}

public class Memory
{
    public void Load(long position, byte[] data) => 
        Console.WriteLine($"Memory: Loading {data.Length} bytes at position {position}");
}

public class HardDrive
{
    public byte[] Read(long lba, int size)
    {
        Console.WriteLine($"HardDrive: Reading {size} bytes from sector {lba}");
        return new byte[size];
    }
}

// Facade
public class ComputerFacade
{
    private readonly CPU cpu;
    private readonly Memory memory;
    private readonly HardDrive hardDrive;
    
    public ComputerFacade()
    {
        cpu = new CPU();
        memory = new Memory();
        hardDrive = new HardDrive();
    }
    
    public void StartComputer()
    {
        Console.WriteLine("Starting computer...");
        cpu.Freeze();
        memory.Load(0, hardDrive.Read(0, 1024));
        cpu.Jump(0);
        cpu.Execute();
        Console.WriteLine("Computer started successfully!");
    }
}

// Usage:
var computer = new ComputerFacade();
computer.StartComputer(); // Simple interface hiding complex operations
```

**Modern API Facade Example**
```csharp
// Complex external services
public interface IPaymentService
{
    Task<bool> ProcessPayment(decimal amount, string cardNumber);
}

public interface IInventoryService  
{
    Task<bool> ReserveItems(List<string> itemIds);
    Task ReleaseItems(List<string> itemIds);
}

public interface IShippingService
{
    Task<string> CreateShipment(string address, List<string> itemIds);
}

// Facade for order processing
public class OrderProcessingFacade
{
    private readonly IPaymentService paymentService;
    private readonly IInventoryService inventoryService;
    private readonly IShippingService shippingService;
    
    public OrderProcessingFacade(
        IPaymentService paymentService,
        IInventoryService inventoryService,
        IShippingService shippingService)
    {
        this.paymentService = paymentService;
        this.inventoryService = inventoryService;
        this.shippingService = shippingService;
    }
    
    public async Task<OrderResult> ProcessOrder(Order order)
    {
        try
        {
            // Reserve inventory
            if (!await inventoryService.ReserveItems(order.ItemIds))
            {
                return new OrderResult { Success = false, Message = "Items not available" };
            }
            
            // Process payment
            if (!await paymentService.ProcessPayment(order.Total, order.CardNumber))
            {
                await inventoryService.ReleaseItems(order.ItemIds);
                return new OrderResult { Success = false, Message = "Payment failed" };
            }
            
            // Create shipment
            var trackingNumber = await shippingService.CreateShipment(order.ShippingAddress, order.ItemIds);
            
            return new OrderResult 
            { 
                Success = true, 
                TrackingNumber = trackingNumber,
                Message = "Order processed successfully" 
            };
        }
        catch (Exception ex)
        {
            // Cleanup and error handling
            await inventoryService.ReleaseItems(order.ItemIds);
            return new OrderResult { Success = false, Message = ex.Message };
        }
    }
}
```

### Flyweight Pattern

**Purpose**: Minimize memory usage by sharing efficiently common data among multiple objects.

```csharp
// Intrinsic state (shared)
public class CharacterFlyweight
{
    private readonly char character;
    private readonly string fontFamily;
    private readonly int fontSize;
    
    public CharacterFlyweight(char character, string fontFamily, int fontSize)
    {
        this.character = character;
        this.fontFamily = fontFamily;
        this.fontSize = fontSize;
    }
    
    // Operation that uses both intrinsic and extrinsic state
    public void Display(int x, int y, string color) // x, y, color are extrinsic
    {
        Console.WriteLine($"Character: {character}, Font: {fontFamily}, Size: {fontSize}, Position: ({x},{y}), Color: {color}");
    }
}

// Flyweight factory
public class CharacterFlyweightFactory
{
    private static readonly Dictionary<string, CharacterFlyweight> flyweights = new();
    
    public static CharacterFlyweight GetFlyweight(char character, string fontFamily, int fontSize)
    {
        string key = $"{character}_{fontFamily}_{fontSize}";
        
        if (!flyweights.ContainsKey(key))
        {
            flyweights[key] = new CharacterFlyweight(character, fontFamily, fontSize);
            Console.WriteLine($"Created new flyweight for: {key}");
        }
        
        return flyweights[key];
    }
    
    public static int GetFlyweightCount() => flyweights.Count;
}

// Context that uses flyweights
public class TextDocument
{
    private readonly List<(CharacterFlyweight flyweight, int x, int y, string color)> characters = new();
    
    public void AddCharacter(char c, string fontFamily, int fontSize, int x, int y, string color)
    {
        var flyweight = CharacterFlyweightFactory.GetFlyweight(c, fontFamily, fontSize);
        characters.Add((flyweight, x, y, color));
    }
    
    public void Display()
    {
        foreach (var (flyweight, x, y, color) in characters)
        {
            flyweight.Display(x, y, color);
        }
    }
}

// Usage:
var document = new TextDocument();

// Even though we're adding many characters, only unique combinations create flyweights
document.AddCharacter('H', "Arial", 12, 0, 0, "Black");
document.AddCharacter('e', "Arial", 12, 10, 0, "Black");
document.AddCharacter('l', "Arial", 12, 20, 0, "Black");
document.AddCharacter('l', "Arial", 12, 30, 0, "Black"); // Reuses existing flyweight
document.AddCharacter('o', "Arial", 12, 40, 0, "Black");

Console.WriteLine($"Total flyweights created: {CharacterFlyweightFactory.GetFlyweightCount()}");
document.Display();
```

**Modern Use Cases**:
- Game development (sharing textures, sprites)
- Text rendering systems
- Caching expensive-to-create objects
- String interning in .NET

### Proxy Pattern

**Purpose**: Control access to objects through a surrogate or placeholder.

**Protection Proxy**
```csharp
public interface ICar
{
    void Drive();
}

public class Car : ICar
{
    public void Drive()
    {
        Console.WriteLine("Car is being driven");
    }
}

public class CarProxy : ICar
{
    private readonly Car car;
    private readonly Driver driver;
    
    public CarProxy(Car car, Driver driver)
    {
        this.car = car;
        this.driver = driver;
    }
    
    public void Drive()
    {
        if (driver.Age >= 16)
        {
            car.Drive();
        }
        else
        {
            Console.WriteLine("Driver too young to drive");
        }
    }
}

public class Driver
{
    public int Age { get; set; }
}

// Usage:
var car = new Car();
var youngDriver = new Driver { Age = 15 };
var carProxy = new CarProxy(car, youngDriver);
carProxy.Drive(); // "Driver too young to drive"
```

**Virtual Proxy (Lazy Loading)**
```csharp
public interface IExpensiveObject
{
    void Process();
}

public class ExpensiveObject : IExpensiveObject
{
    public ExpensiveObject()
    {
        // Simulate expensive initialization
        Console.WriteLine("ExpensiveObject created with heavy initialization");
        Thread.Sleep(1000);
    }
    
    public void Process()
    {
        Console.WriteLine("Processing with ExpensiveObject");
    }
}

public class ExpensiveObjectProxy : IExpensiveObject
{
    private ExpensiveObject expensiveObject;
    
    public void Process()
    {
        // Lazy initialization - object created only when needed
        if (expensiveObject == null)
        {
            Console.WriteLine("Creating ExpensiveObject on first use...");
            expensiveObject = new ExpensiveObject();
        }
        
        expensiveObject.Process();
    }
}

// Usage:
IExpensiveObject proxy = new ExpensiveObjectProxy();
// No expensive object created yet
Console.WriteLine("Proxy created");
// Object is created only when Process is called
proxy.Process();
```

**Caching Proxy**
```csharp
public interface IDataService
{
    Task<string> GetData(string key);
}

public class DatabaseService : IDataService
{
    public async Task<string> GetData(string key)
    {
        // Simulate database call
        Console.WriteLine($"Fetching data from database for key: {key}");
        await Task.Delay(1000);
        return $"Data for {key}";
    }
}

public class CachingProxy : IDataService
{
    private readonly IDataService dataService;
    private readonly Dictionary<string, string> cache = new();
    
    public CachingProxy(IDataService dataService)
    {
        this.dataService = dataService;
    }
    
    public async Task<string> GetData(string key)
    {
        if (cache.TryGetValue(key, out var cachedData))
        {
            Console.WriteLine($"Returning cached data for key: {key}");
            return cachedData;
        }
        
        var data = await dataService.GetData(key);
        cache[key] = data;
        return data;
    }
}

// Usage:
IDataService dataService = new DatabaseService();
IDataService proxy = new CachingProxy(dataService);

var data1 = await proxy.GetData("user123"); // Database call
var data2 = await proxy.GetData("user123"); // Cache hit
```

---

## Behavioral Patterns

### Observer Pattern

**Purpose**: Define one-to-many dependency between objects so that state changes are automatically communicated.

**Traditional Observer**
```csharp
public interface IObserver
{
    void Update(string message);
}

public interface ISubject
{
    void Attach(IObserver observer);
    void Detach(IObserver observer);
    void Notify(string message);
}

public class NewsAgency : ISubject
{
    private readonly List<IObserver> observers = new();
    private string news;
    
    public string News
    {
        get => news;
        set
        {
            news = value;
            Notify(news);
        }
    }
    
    public void Attach(IObserver observer)
    {
        observers.Add(observer);
    }
    
    public void Detach(IObserver observer)
    {
        observers.Remove(observer);
    }
    
    public void Notify(string message)
    {
        foreach (var observer in observers)
        {
            observer.Update(message);
        }
    }
}

public class NewsChannel : IObserver
{
    private string channelName;
    
    public NewsChannel(string channelName)
    {
        this.channelName = channelName;
    }
    
    public void Update(string message)
    {
        Console.WriteLine($"{channelName} received news: {message}");
    }
}

// Usage:
var agency = new NewsAgency();
var cnn = new NewsChannel("CNN");
var bbc = new NewsChannel("BBC");

agency.Attach(cnn);
agency.Attach(bbc);

agency.News = "Breaking news: New technology announced!";
```

**Modern Event-Based Observer**
```csharp
public class ModernNewsAgency
{
    public event Action<string> NewsPublished;
    
    private string news;
    public string News
    {
        get => news;
        set
        {
            news = value;
            NewsPublished?.Invoke(news);
        }
    }
}

public class ModernNewsChannel
{
    private string channelName;
    
    public ModernNewsChannel(string channelName, ModernNewsAgency agency)
    {
        this.channelName = channelName;
        agency.NewsPublished += HandleNews;
    }
    
    private void HandleNews(string news)
    {
        Console.WriteLine($"{channelName} received news: {news}");
    }
}

// Usage:
var agency = new ModernNewsAgency();
var cnn = new ModernNewsChannel("CNN", agency);
var bbc = new ModernNewsChannel("BBC", agency);

agency.News = "Breaking news: Modern events working!";
```

### Strategy Pattern

**Purpose**: Define algorithms as interchangeable objects, enabling runtime selection.

**Dynamic Strategy**
```csharp
public enum OutputFormat
{
    Html,
    Markdown
}

public interface IListStrategy
{
    void Start(StringBuilder sb);
    void End(StringBuilder sb);
    void AddListItem(StringBuilder sb, string item);
}

public class HtmlListStrategy : IListStrategy
{
    public void Start(StringBuilder sb) => sb.AppendLine("<ul>");
    public void End(StringBuilder sb) => sb.AppendLine("</ul>");
    public void AddListItem(StringBuilder sb, string item) => sb.AppendLine($"  <li>{item}</li>");
}

public class MarkdownListStrategy : IListStrategy
{
    public void Start(StringBuilder sb) { } // No special start for markdown
    public void End(StringBuilder sb) { }   // No special end for markdown
    public void AddListItem(StringBuilder sb, string item) => sb.AppendLine($"- {item}");
}

public class TextProcessor
{
    private readonly StringBuilder sb = new();
    private IListStrategy listStrategy;
    
    public void SetOutputFormat(OutputFormat format)
    {
        listStrategy = format switch
        {
            OutputFormat.Html => new HtmlListStrategy(),
            OutputFormat.Markdown => new MarkdownListStrategy(),
            _ => throw new ArgumentException("Invalid format")
        };
    }
    
    public void AppendList(IEnumerable<string> items)
    {
        listStrategy.Start(sb);
        foreach (var item in items)
        {
            listStrategy.AddListItem(sb, item);
        }
        listStrategy.End(sb);
    }
    
    public override string ToString() => sb.ToString();
}

// Usage:
var processor = new TextProcessor();
processor.SetOutputFormat(OutputFormat.Html);
processor.AppendList(new[] { "Item 1", "Item 2", "Item 3" });
Console.WriteLine(processor.ToString());
```

### Command Pattern

**Purpose**: Encapsulate requests as objects, enabling queuing, logging, and undo operations.

```csharp
public interface ICommand
{
    void Execute();
    void Undo();
}

public class BankAccount
{
    public int Balance { get; private set; }
    
    public void Deposit(int amount)
    {
        Balance += amount;
        Console.WriteLine($"Deposited ${amount}, balance is now ${Balance}");
    }
    
    public void Withdraw(int amount)
    {
        if (Balance >= amount)
        {
            Balance -= amount;
            Console.WriteLine($"Withdrew ${amount}, balance is now ${Balance}");
        }
        else
        {
            Console.WriteLine("Insufficient funds");
        }
    }
}

public enum Action
{
    Deposit,
    Withdraw
}

public class BankAccountCommand : ICommand
{
    private BankAccount account;
    private Action action;
    private int amount;
    private bool succeeded;
    
    public BankAccountCommand(BankAccount account, Action action, int amount)
    {
        this.account = account;
        this.action = action;
        this.amount = amount;
    }
    
    public void Execute()
    {
        switch (action)
        {
            case Action.Deposit:
                account.Deposit(amount);
                succeeded = true;
                break;
            case Action.Withdraw:
                int balanceBefore = account.Balance;
                account.Withdraw(amount);
                succeeded = account.Balance != balanceBefore;
                break;
        }
    }
    
    public void Undo()
    {
        if (!succeeded) return;
        
        switch (action)
        {
            case Action.Deposit:
                account.Withdraw(amount);
                break;
            case Action.Withdraw:
                account.Deposit(amount);
                break;
        }
    }
}

// Command processor with undo/redo
public class CommandProcessor
{
    private readonly List<ICommand> commands = new();
    private int current = 0;
    
    public void Execute(ICommand command)
    {
        // Remove any commands after current position
        if (current < commands.Count)
        {
            commands.RemoveRange(current, commands.Count - current);
        }
        
        commands.Add(command);
        command.Execute();
        current++;
    }
    
    public void Undo()
    {
        if (current > 0)
        {
            current--;
            commands[current].Undo();
        }
    }
    
    public void Redo()
    {
        if (current < commands.Count)
        {
            commands[current].Execute();
            current++;
        }
    }
}

// Usage:
var account = new BankAccount();
var processor = new CommandProcessor();

processor.Execute(new BankAccountCommand(account, Action.Deposit, 100));
processor.Execute(new BankAccountCommand(account, Action.Withdraw, 50));
processor.Execute(new BankAccountCommand(account, Action.Withdraw, 25));

Console.WriteLine($"Final balance: ${account.Balance}");

processor.Undo(); // Undo last withdrawal
processor.Undo(); // Undo second withdrawal
processor.Redo(); // Redo second withdrawal
```

### State Pattern

**Purpose**: Allow objects to alter behavior when internal state changes.

**Modern Switch Expression Approach**
```csharp
public enum LightState
{
    Off,
    Low,
    High
}

public enum LightAction
{
    TurnOn,
    TurnOff,
    Brighten
}

public class ModernLight
{
    public LightState State { get; private set; } = LightState.Off;
    
    public void PerformAction(LightAction action)
    {
        var previousState = State;
        
        State = (State, action) switch
        {
            (LightState.Off, LightAction.TurnOn) => LightState.Low,
            (LightState.Low, LightAction.TurnOff) => LightState.Off,
            (LightState.Low, LightAction.Brighten) => LightState.High,
            (LightState.High, LightAction.TurnOff) => LightState.Off,
            (LightState.High, LightAction.Brighten) => LightState.High, // Stay high
            _ => State // No state change
        };
        
        if (previousState != State)
        {
            Console.WriteLine($"Light state changed from {previousState} to {State}");
        }
    }
}

// Usage:
var light = new ModernLight();
light.PerformAction(LightAction.TurnOn);  // Off -> Low
light.PerformAction(LightAction.Brighten); // Low -> High
light.PerformAction(LightAction.TurnOff);  // High -> Off
```

### Template Method Pattern

**Purpose**: Define algorithm skeleton in base class, allowing subclasses to override specific steps.

```csharp
public abstract class Game
{
    protected readonly int NumberOfPlayers;
    protected int CurrentPlayer = 0;
    
    protected Game(int numberOfPlayers)
    {
        NumberOfPlayers = numberOfPlayers;
    }
    
    // Template method
    public void Run()
    {
        Start();
        while (!HasWinner)
        {
            TakeTurn();
            CurrentPlayer = (CurrentPlayer + 1) % NumberOfPlayers;
        }
        Console.WriteLine($"Player {WinningPlayer} wins!");
    }
    
    protected abstract void Start();
    protected abstract void TakeTurn();
    protected abstract bool HasWinner { get; }
    protected abstract int WinningPlayer { get; }
}

public class Chess : Game
{
    private int turn = 1;
    private const int maxTurns = 10; // Simplified for example
    
    public Chess() : base(2) { }
    
    protected override void Start()
    {
        Console.WriteLine($"Starting a game of chess with {NumberOfPlayers} players");
    }
    
    protected override void TakeTurn()
    {
        Console.WriteLine($"Turn {turn++}: Player {CurrentPlayer} is thinking...");
    }
    
    protected override bool HasWinner => turn >= maxTurns;
    
    protected override int WinningPlayer => CurrentPlayer;
}

// Usage:
var chess = new Chess();
chess.Run();
```

### Chain of Responsibility Pattern

**Purpose**: Pass requests along a chain of handlers until one processes it.

```csharp
public abstract class Handler
{
    protected Handler nextHandler;
    
    public Handler SetNext(Handler handler)
    {
        nextHandler = handler;
        return handler;
    }
    
    public virtual string Handle(string request)
    {
        if (nextHandler != null)
        {
            return nextHandler.Handle(request);
        }
        
        return null;
    }
}

public class AuthenticationHandler : Handler
{
    public override string Handle(string request)
    {
        if (request == "authenticate")
        {
            return "Authentication handled";
        }
        
        return base.Handle(request);
    }
}

public class AuthorizationHandler : Handler
{
    public override string Handle(string request)
    {
        if (request == "authorize")
        {
            return "Authorization handled";
        }
        
        return base.Handle(request);
    }
}

public class ValidationHandler : Handler
{
    public override string Handle(string request)
    {
        if (request == "validate")
        {
            return "Validation handled";
        }
        
        return base.Handle(request);
    }
}

// Usage:
var auth = new AuthenticationHandler();
var authz = new AuthorizationHandler();
var validation = new ValidationHandler();

auth.SetNext(authz).SetNext(validation);

Console.WriteLine(auth.Handle("authenticate")); // "Authentication handled"
Console.WriteLine(auth.Handle("authorize"));    // "Authorization handled"
Console.WriteLine(auth.Handle("validate"));     // "Validation handled"
Console.WriteLine(auth.Handle("unknown"));      // null
```

### Mediator Pattern

**Purpose**: Define how objects interact through a mediator, reducing direct dependencies.

```csharp
public interface IChatRoom
{
    void SendMessage(string message, User user);
    void AddUser(User user);
    void RemoveUser(User user);
}

public class ChatRoom : IChatRoom
{
    private readonly List<User> users = new();
    
    public void AddUser(User user)
    {
        users.Add(user);
        Console.WriteLine($"{user.Name} joined the chat");
    }
    
    public void RemoveUser(User user)
    {
        users.Remove(user);
        Console.WriteLine($"{user.Name} left the chat");
    }
    
    public void SendMessage(string message, User sender)
    {
        foreach (var user in users.Where(u => u != sender))
        {
            user.Receive(message, sender.Name);
        }
    }
}

public abstract class User
{
    protected IChatRoom chatRoom;
    public string Name { get; }
    
    protected User(IChatRoom chatRoom, string name)
    {
        this.chatRoom = chatRoom;
        Name = name;
    }
    
    public abstract void Send(string message);
    public abstract void Receive(string message, string senderName);
}

public class ConcreteUser : User
{
    public ConcreteUser(IChatRoom chatRoom, string name) : base(chatRoom, name) { }
    
    public override void Send(string message)
    {
        Console.WriteLine($"{Name} sends: {message}");
        chatRoom.SendMessage(message, this);
    }
    
    public override void Receive(string message, string senderName)
    {
        Console.WriteLine($"{Name} receives from {senderName}: {message}");
    }
}

// Usage:
var chatRoom = new ChatRoom();
var john = new ConcreteUser(chatRoom, "John");
var jane = new ConcreteUser(chatRoom, "Jane");
var bob = new ConcreteUser(chatRoom, "Bob");

chatRoom.AddUser(john);
chatRoom.AddUser(jane);
chatRoom.AddUser(bob);

john.Send("Hello everyone!");
jane.Send("Hi John!");
```

### Iterator Pattern

**Purpose**: Provide a way to access elements of a collection sequentially without exposing its underlying representation.

```csharp
// Custom collection
public class WordCollection : IEnumerable<string>
{
    private readonly List<string> words = new();
    
    public void Add(string word) => words.Add(word);
    
    // Standard IEnumerable implementation
    public IEnumerator<string> GetEnumerator() => words.GetEnumerator();
    IEnumerator IEnumerable.GetEnumerator() => GetEnumerator();
    
    // Custom iterator methods
    public IEnumerable<string> GetWordsStartingWith(char letter)
    {
        foreach (var word in words)
        {
            if (word.StartsWith(letter))
                yield return word;
        }
    }
    
    public IEnumerable<string> GetWordsInReverse()
    {
        for (int i = words.Count - 1; i >= 0; i--)
        {
            yield return words[i];
        }
    }
}

// Usage:
var collection = new WordCollection();
collection.Add("Apple");
collection.Add("Banana");
collection.Add("Cherry");
collection.Add("Avocado");

// Standard iteration
foreach (var word in collection)
{
    Console.WriteLine(word);
}

// Custom iterations
Console.WriteLine("Words starting with 'A':");
foreach (var word in collection.GetWordsStartingWith('A'))
{
    Console.WriteLine(word);
}
```

### Visitor Pattern

**Purpose**: Define operations on object hierarchies without modifying the objects themselves.

**⚠️ Complexity Warning**: Often indicates design issues that could be solved with simpler approaches.

**Modern Pattern Matching Approach (C# 8+)**
```csharp
public abstract record Expression;
public record Number(double Value) : Expression;
public record Addition(Expression Left, Expression Right) : Expression;

public static class ExpressionExtensions
{
    public static string Print(this Expression expression) =>
        expression switch
        {
            Number n => n.Value.ToString(),
            Addition a => $"({a.Left.Print()} + {a.Right.Print()})",
            _ => throw new ArgumentException("Unknown expression type")
        };
    
    public static double Evaluate(this Expression expression) =>
        expression switch
        {
            Number n => n.Value,
            Addition a => a.Left.Evaluate() + a.Right.Evaluate(),
            _ => throw new ArgumentException("Unknown expression type")
        };
}

// Usage:
var expression = new Addition(
    new Number(2),
    new Addition(new Number(3), new Number(4))
);

Console.WriteLine($"Expression: {expression.Print()}"); // (2 + (3 + 4))
Console.WriteLine($"Result: {expression.Evaluate()}");  // 9
```

### Memento Pattern

**Purpose**: Capture object state to enable rollback without violating encapsulation.

```csharp
// Memento
public class EditorMemento
{
    public string Content { get; }
    public int CursorPosition { get; }
    public DateTime Timestamp { get; }
    
    internal EditorMemento(string content, int cursorPosition)
    {
        Content = content;
        CursorPosition = cursorPosition;
        Timestamp = DateTime.Now;
    }
}

// Originator
public class TextEditor
{
    public string Content { get; private set; } = "";
    public int CursorPosition { get; private set; } = 0;
    
    public void Write(string text)
    {
        Content = Content.Insert(CursorPosition, text);
        CursorPosition += text.Length;
    }
    
    public void MoveCursor(int position)
    {
        CursorPosition = Math.Max(0, Math.Min(position, Content.Length));
    }
    
    public EditorMemento CreateMemento()
    {
        return new EditorMemento(Content, CursorPosition);
    }
    
    public void RestoreFromMemento(EditorMemento memento)
    {
        Content = memento.Content;
        CursorPosition = memento.CursorPosition;
    }
}

// Caretaker
public class EditorHistory
{
    private readonly Stack<EditorMemento> history = new();
    private readonly Stack<EditorMemento> redoStack = new();
    
    public void Save(EditorMemento memento)
    {
        history.Push(memento);
        redoStack.Clear(); // Clear redo stack when new state is saved
    }
    
    public EditorMemento Undo()
    {
        if (history.Count > 1) // Keep at least one state
        {
            var current = history.Pop();
            redoStack.Push(current);
            return history.Peek();
        }
        return null;
    }
    
    public EditorMemento Redo()
    {
        if (redoStack.Count > 0)
        {
            var memento = redoStack.Pop();
            history.Push(memento);
            return memento;
        }
        return null;
    }
}

// Usage:
var editor = new TextEditor();
var history = new EditorHistory();

// Save initial state
history.Save(editor.CreateMemento());

editor.Write("Hello ");
history.Save(editor.CreateMemento());

editor.Write("World");
history.Save(editor.CreateMemento());

editor.Write("!");
Console.WriteLine($"Current: '{editor.Content}'"); // Current: 'Hello World!'

// Undo
var previousState = history.Undo();
if (previousState != null)
{
    editor.RestoreFromMemento(previousState);
    Console.WriteLine($"After undo: '{editor.Content}'"); // After undo: 'Hello World'
}
```

### Interpreter Pattern

**Purpose**: Define a grammar for a language and provide an interpreter to process sentences in that language.

```csharp
// Abstract expression
public interface IExpression
{
    int Interpret(Dictionary<char, int> variables);
}

// Terminal expressions
public class NumberExpression : IExpression
{
    private readonly int number;
    
    public NumberExpression(int number)
    {
        this.number = number;
    }
    
    public int Interpret(Dictionary<char, int> variables) => number;
}

public class VariableExpression : IExpression
{
    private readonly char variable;
    
    public VariableExpression(char variable)
    {
        this.variable = variable;
    }
    
    public int Interpret(Dictionary<char, int> variables) =>
        variables.ContainsKey(variable) ? variables[variable] : 0;
}

// Non-terminal expressions
public class AddExpression : IExpression
{
    private readonly IExpression left;
    private readonly IExpression right;
    
    public AddExpression(IExpression left, IExpression right)
    {
        this.left = left;
        this.right = right;
    }
    
    public int Interpret(Dictionary<char, int> variables) =>
        left.Interpret(variables) + right.Interpret(variables);
}

public class SubtractExpression : IExpression
{
    private readonly IExpression left;
    private readonly IExpression right;
    
    public SubtractExpression(IExpression left, IExpression right)
    {
        this.left = left;
        this.right = right;
    }
    
    public int Interpret(Dictionary<char, int> variables) =>
        left.Interpret(variables) - right.Interpret(variables);
}

// Simple parser for expressions like "x + y - 10"
public static class ExpressionParser
{
    public static IExpression Parse(string expression)
    {
        var tokens = expression.Replace(" ", "").ToCharArray();
        var stack = new Stack<IExpression>();
        var operatorStack = new Stack<char>();
        
        for (int i = 0; i < tokens.Length; i++)
        {
            var token = tokens[i];
            
            if (char.IsDigit(token))
            {
                // Handle multi-digit numbers
                var numberStr = "";
                while (i < tokens.Length && char.IsDigit(tokens[i]))
                {
                    numberStr += tokens[i];
                    i++;
                }
                i--; // Back up one
                
                stack.Push(new NumberExpression(int.Parse(numberStr)));
            }
            else if (char.IsLetter(token))
            {
                stack.Push(new VariableExpression(token));
            }
            else if (token == '+' || token == '-')
            {
                while (operatorStack.Count > 0)
                {
                    var op = operatorStack.Pop();
                    var right = stack.Pop();
                    var left = stack.Pop();
                    
                    if (op == '+')
                        stack.Push(new AddExpression(left, right));
                    else
                        stack.Push(new SubtractExpression(left, right));
                }
                
                operatorStack.Push(token);
            }
        }
        
        while (operatorStack.Count > 0)
        {
            var op = operatorStack.Pop();
            var right = stack.Pop();
            var left = stack.Pop();
            
            if (op == '+')
                stack.Push(new AddExpression(left, right));
            else
                stack.Push(new SubtractExpression(left, right));
        }
        
        return stack.Pop();
    }
}

// Usage:
var variables = new Dictionary<char, int>
{
    { 'x', 10 },
    { 'y', 5 }
};

var expression = ExpressionParser.Parse("x + y - 3");
var result = expression.Interpret(variables);
Console.WriteLine($"Result: {result}"); // Result: 12 (10 + 5 - 3)
```

### Null Object Pattern

**Purpose**: Provide a default object that exhibits neutral behavior instead of using null references.

```csharp
// Abstract interface
public interface ILogger
{
    void Log(string message);
    void LogError(string error);
}

// Real implementation
public class FileLogger : ILogger
{
    private readonly string filePath;
    
    public FileLogger(string filePath)
    {
        this.filePath = filePath;
    }
    
    public void Log(string message)
    {
        File.AppendAllText(filePath, $"{DateTime.Now}: {message}\n");
    }
    
    public void LogError(string error)
    {
        File.AppendAllText(filePath, $"{DateTime.Now} ERROR: {error}\n");
    }
}

// Null object implementation
public class NullLogger : ILogger
{
    public void Log(string message)
    {
        // Do nothing
    }
    
    public void LogError(string error)
    {
        // Do nothing
    }
}

// Service that uses logger
public class UserService
{
    private readonly ILogger logger;
    
    public UserService(ILogger logger = null)
    {
        // Use null object instead of null checks
        this.logger = logger ?? new NullLogger();
    }
    
    public void CreateUser(string userName)
    {
        // No null checks needed
        logger.Log($"Creating user: {userName}");
        
        try
        {
            // User creation logic
            Console.WriteLine($"User {userName} created");
            logger.Log($"User {userName} created successfully");
        }
        catch (Exception ex)
        {
            logger.LogError($"Failed to create user {userName}: {ex.Message}");
            throw;
        }
    }
}

// Usage:
var userServiceWithLogger = new UserService(new FileLogger("app.log"));
userServiceWithLogger.CreateUser("John");

var userServiceWithoutLogger = new UserService(); // Uses NullLogger
userServiceWithoutLogger.CreateUser("Jane");
```

**Modern Null Object with Factory**
```csharp
public static class LoggerFactory
{
    public static ILogger CreateLogger(string type) =>
        type.ToLower() switch
        {
            "file" => new FileLogger("app.log"),
            "console" => new ConsoleLogger(),
            "null" or _ => new NullLogger()
        };
}

public class ConsoleLogger : ILogger
{
    public void Log(string message) => Console.WriteLine($"LOG: {message}");
    public void LogError(string error) => Console.WriteLine($"ERROR: {error}");
}

// Usage with configuration
var loggerType = Configuration.GetValue<string>("LoggerType") ?? "null";
var logger = LoggerFactory.CreateLogger(loggerType);
var userService = new UserService(logger);
```

---

## The Result Pattern

**Purpose**: Provide a functional alternative to throwing exceptions for error handling, making failure cases explicit in the return type.

### Basic Result Pattern

```csharp
// Basic Result class
public class Result<T>
{
    public bool IsSuccess { get; }
    public bool IsFailure => !IsSuccess;
    public T Value { get; }
    public string Error { get; }
    
    private Result(bool isSuccess, T value, string error)
    {
        IsSuccess = isSuccess;
        Value = value;
        Error = error;
    }
    
    public static Result<T> Success(T value) => new(true, value, null);
    public static Result<T> Failure(string error) => new(false, default(T), error);
    
    // Implicit conversion from T to Result<T>
    public static implicit operator Result<T>(T value) => Success(value);
    
    // Pattern matching support
    public void Match(Action<T> onSuccess, Action<string> onFailure)
    {
        if (IsSuccess)
            onSuccess(Value);
        else
            onFailure(Error);
    }
    
    public TResult Match<TResult>(Func<T, TResult> onSuccess, Func<string, TResult> onFailure)
    {
        return IsSuccess ? onSuccess(Value) : onFailure(Error);
    }
}

// Non-generic Result for operations that don't return a value
public class Result
{
    public bool IsSuccess { get; }
    public bool IsFailure => !IsSuccess;
    public string Error { get; }
    
    private Result(bool isSuccess, string error)
    {
        IsSuccess = isSuccess;
        Error = error;
    }
    
    public static Result Success() => new(true, null);
    public static Result Failure(string error) => new(false, error);
    
    public void Match(Action onSuccess, Action<string> onFailure)
    {
        if (IsSuccess)
            onSuccess();
        else
            onFailure(Error);
    }
}
```

### Enhanced Result Pattern with Multiple Errors

```csharp
public class Result<T>
{
    public bool IsSuccess { get; }
    public bool IsFailure => !IsSuccess;
    public T Value { get; }
    public IReadOnlyList<string> Errors { get; }
    
    private Result(bool isSuccess, T value, IEnumerable<string> errors)
    {
        IsSuccess = isSuccess;
        Value = value;
        Errors = errors?.ToList().AsReadOnly() ?? new List<string>().AsReadOnly();
    }
    
    public static Result<T> Success(T value) => new(true, value, null);
    public static Result<T> Failure(string error) => new(false, default(T), new[] { error });
    public static Result<T> Failure(IEnumerable<string> errors) => new(false, default(T), errors);
    
    // Combine multiple results
    public static Result<IEnumerable<T>> Combine(params Result<T>[] results)
    {
        var failures = results.Where(r => r.IsFailure).ToList();
        if (failures.Any())
        {
            var allErrors = failures.SelectMany(f => f.Errors);
            return Result<IEnumerable<T>>.Failure(allErrors);
        }
        
        var values = results.Select(r => r.Value);
        return Result<IEnumerable<T>>.Success(values);
    }
    
    // Functional operations
    public Result<TResult> Map<TResult>(Func<T, TResult> func)
    {
        if (IsFailure)
            return Result<TResult>.Failure(Errors);
        
        try
        {
            return Result<TResult>.Success(func(Value));
        }
        catch (Exception ex)
        {
            return Result<TResult>.Failure(ex.Message);
        }
    }
    
    public Result<TResult> Bind<TResult>(Func<T, Result<TResult>> func)
    {
        if (IsFailure)
            return Result<TResult>.Failure(Errors);
        
        return func(Value);
    }
}
```

### Real-World Usage Examples

```csharp
public class UserService
{
    private readonly IUserRepository userRepository;
    private readonly IEmailService emailService;
    
    public UserService(IUserRepository userRepository, IEmailService emailService)
    {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
    
    public Result<User> CreateUser(CreateUserRequest request)
    {
        // Validation
        var validationResult = ValidateCreateUserRequest(request);
        if (validationResult.IsFailure)
            return Result<User>.Failure(validationResult.Errors);
        
        // Check if user already exists
        var existingUser = userRepository.FindByEmail(request.Email);
        if (existingUser != null)
            return Result<User>.Failure("User with this email already exists");
        
        // Create user
        var user = new User
        {
            Id = Guid.NewGuid(),
            Email = request.Email,
            Name = request.Name,
            CreatedAt = DateTime.UtcNow
        };
        
        // Save to database
        var saveResult = userRepository.Save(user);
        if (saveResult.IsFailure)
            return Result<User>.Failure(saveResult.Error);
        
        // Send welcome email (failure here doesn't fail the whole operation)
        emailService.SendWelcomeEmail(user.Email, user.Name);
        
        return Result<User>.Success(user);
    }
    
    private Result ValidateCreateUserRequest(CreateUserRequest request)
    {
        var errors = new List<string>();
        
        if (string.IsNullOrWhiteSpace(request.Email))
            errors.Add("Email is required");
        else if (!IsValidEmail(request.Email))
            errors.Add("Email format is invalid");
        
        if (string.IsNullOrWhiteSpace(request.Name))
            errors.Add("Name is required");
        else if (request.Name.Length < 2)
            errors.Add("Name must be at least 2 characters");
        
        return errors.Any() 
            ? Result.Failure(string.Join("; ", errors))
            : Result.Success();
    }
    
    private bool IsValidEmail(string email)
    {
        try
        {
            var addr = new System.Net.Mail.MailAddress(email);
            return addr.Address == email;
        }
        catch
        {
            return false;
        }
    }
}

// Usage
public class UserController
{
    private readonly UserService userService;
    
    public UserController(UserService userService)
    {
        this.userService = userService;
    }
    
    public IActionResult CreateUser(CreateUserRequest request)
    {
        var result = userService.CreateUser(request);
        
        return result.Match<IActionResult>(
            onSuccess: user => Ok(new { UserId = user.Id, Message = "User created successfully" }),
            onFailure: error => BadRequest(new { Error = error })
        );
    }
}
```

### Railway-Oriented Programming with Results

```csharp
public static class ResultExtensions
{
    // Chain operations together - continues only if all succeed
    public static Result<TResult> Then<T, TResult>(this Result<T> result, Func<T, Result<TResult>> next)
    {
        return result.IsSuccess ? next(result.Value) : Result<TResult>.Failure(result.Errors);
    }
    
    // Transform the value if successful
    public static Result<TResult> Select<T, TResult>(this Result<T> result, Func<T, TResult> selector)
    {
        return result.Map(selector);
    }
    
    // Tap - perform side effects without changing the result
    public static Result<T> Tap<T>(this Result<T> result, Action<T> action)
    {
        if (result.IsSuccess)
            action(result.Value);
        return result;
    }
    
    // Ensure - add additional validation
    public static Result<T> Ensure<T>(this Result<T> result, Func<T, bool> predicate, string error)
    {
        if (result.IsFailure)
            return result;
        
        return predicate(result.Value) 
            ? result 
            : Result<T>.Failure(error);
    }
}

// Railway-oriented example
public Result<string> ProcessOrder(CreateOrderRequest request)
{
    return ValidateOrderRequest(request)
        .Then(req => CreateOrder(req))
        .Then(order => ReserveInventory(order))
        .Then(order => ProcessPayment(order))
        .Tap(order => SendConfirmationEmail(order.CustomerEmail))
        .Then(order => Result<string>.Success($"Order {order.Id} processed successfully"));
}
```

### Benefits of Result Pattern

**Explicit Error Handling**
- Makes failure cases explicit in method signatures
- Forces callers to handle potential failures
- Reduces unexpected runtime exceptions

**Functional Composition**
- Enables railway-oriented programming
- Allows chaining operations that can fail
- Provides clean separation between success and failure paths

**Better Testing**
- Easier to test error conditions
- No need to catch exceptions in tests
- Clear assertion of success vs failure states

**Performance**
- Avoids expensive exception throwing and stack unwinding
- More predictable performance characteristics
- Better for hot code paths

### When to Use Result Pattern vs Exceptions

**Use Result Pattern for:**
- Expected failure cases (validation errors, business rule violations)
- Operations where failure is part of normal flow
- High-performance scenarios where exceptions are costly
- Functional programming approaches
- API boundaries where you want explicit error contracts

**Use Exceptions for:**
- Truly exceptional conditions (system failures, programming errors)
- Unrecoverable errors
- Third-party library integration where exceptions are expected
- When integrating with existing exception-based codebases

---

## Modern Best Practices

### Composition Over Inheritance
- **Prefer**: Dependency injection and composition
- **Avoid**: Deep inheritance hierarchies
- **Benefits**: More flexible, testable, and maintainable

### Dependency Injection
- **Constructor Injection**: Primary method for required dependencies
- **Property Injection**: For optional dependencies
- **Method Injection**: For contextual dependencies

### Clean Code Principles

**KISS (Keep It Simple, Stupid)**
- Choose simple solutions over complex ones
- Avoid over-engineering
- Prefer readable code over clever code

**YAGNI (You Aren't Gonna Need It)**
- Don't implement features before they're needed
- Focus on current requirements
- Avoid speculative generality

**DRY (Don't Repeat Yourself)**
- Eliminate code duplication through abstraction
- Use shared libraries and utilities
- Balance with single responsibility principle

### Testing Considerations
- **Testability**: SOLID principles improve unit testing
- **Mocking**: Interfaces enable effective mocking
- **Isolation**: Single responsibility makes components easier to test

---

## Quick Reference

### When to Use Each Pattern

**Creational (5 patterns)**
- **Factory Method**: Multiple ways to create objects
- **Abstract Factory**: Families of related objects
- **Builder**: Complex object construction
- **Prototype**: Object cloning
- **Singleton**: Shared resources (use DI instead)

**Structural (7 patterns)**
- **Adapter**: Interface compatibility
- **Bridge**: Separate abstraction from implementation
- **Composite**: Tree structures
- **Decorator**: Dynamic behavior addition
- **Facade**: Simplify complex subsystems
- **Flyweight**: Memory optimization through sharing
- **Proxy**: Control object access

**Behavioral (11 patterns)**
- **Chain of Responsibility**: Request handling chain
- **Command**: Action encapsulation
- **Interpreter**: Language processing
- **Iterator**: Collection traversal
- **Mediator**: Object communication
- **Memento**: State capture and restore
- **Observer**: Event notifications
- **State**: Behavior changes with state
- **Strategy**: Algorithm selection
- **Template Method**: Algorithm skeleton
- **Visitor**: Operations on object hierarchies

### Common Anti-Patterns to Avoid

- **God Object**: Classes that do too much
- **Spaghetti Code**: Tangled dependencies
- **Tight Coupling**: Direct dependencies instead of abstractions
- **Premature Optimization**: Complex patterns without justification
- **Pattern Overuse**: Using patterns where simple code suffices

### Modern Framework Integration

**ASP.NET Core**: Extensive use of dependency injection, middleware (decorator), and options pattern
**Entity Framework**: Unit of work, repository, and lazy loading (proxy) patterns
**SignalR**: Observer pattern for real-time communication
**MediatR**: Command and mediator patterns for CQRS

---

> **Remember**: Patterns are tools, not goals. Use them to solve specific problems, not because they exist. Modern development emphasizes simplicity, testability, and maintainability over pattern implementation.