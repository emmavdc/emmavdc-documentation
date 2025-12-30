# C# and .NET Modern Cheat Sheet

---

## Core Types
- int, float, double, decimal, char, string, bool, DateTime
- Nullable types:
  int? number = null;

---

## Modern String Features

### String Interpolation 

**Note**: Available since C# 6

```csharp
string name = "Emma";
int age = 30;
string intro = $"Hello, my name is {name} and I am {age} years old.";
```

### Multi-line Strings

**Note**: Available since C# 11

```csharp
string message = """
Hello,
This is a multi-line
string.
""";
```

## Method Declarations and Calls

### Expression-bodied Methods

```csharp
int Addition(int a, int b) => a + b;
```

### Traditional Method

```csharp
int Addition2(int a, int b)
{
    return a + b;
}
```

## Record 

**Note**:  Available since C# 9

```csharp
public record Person(string Name, int Age);
```

ce qui revient à faire:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }

    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }
}
```

It's mean that for the record:

- The 2 properties are read only
- Has a constructor
- The == operator on records compares all components defined in the primary constructor; if any value differs, the equality is false.

```csharp

public record Person(string FirstName, string LastName);

var a = new Person("Emma", 25);
var b = new Person("Emma", 25);
var c =  new Person("Emma2",25);

Console.WriteLine(a.FirstName == b.FirstName); // True
Console.WriteLine(a.FirstName == c.FirstName); // False
```

- immuable: cannot change the value of a property. Only possible to copy the object and change the value with the `with`

```csharp
var p = new Person("Emma", 25);

var p2 = p with {Age = 16} // new modify clone

// ==> donc p2 is "Emma" and 16

```

There are 2 types of record: record class (default) and record struct (sine C#10)

## Modern Switch case 

**Note**: since C#8

```csharp
var jour = "Lundi";
var message = jour switch
{
    "Lundi" => "C'est le premier jour de la semaine.",
    "Mardi" => "C'est le deuxième jour de la semaine.",
    "Mercredi" => "C'est le troisième jour de la semaine.",
    _ => "C'est un autre jour."
};
```

## Nullable reference Types

**Note**: since C#8

```csharp
string? optionalName = null;
string name = "Emma";
```




