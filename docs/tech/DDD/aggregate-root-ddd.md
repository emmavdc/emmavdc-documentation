# Guidelines on Aggregates and Aggregate Roots
---
## Overview

When working with DDD (Domain Driven Design), understanding the concepts of Aggregates and Aggregate Roots is crucial for designing a well-structured domain model.

In this document, it explains what Aggregates and Aggregate Roots are, how to implement them, and best practices for using them in your code.

## Aggregate

An Aggregate is a group of related entities and value objects that are treated as a single unit.

It defines a boundary within which data changes are coordinated to keep the model consistent.

### Concrete Example

Let's imagine a process where there is a `Person` and an `Animal`.

The `Person` owns the `Animal`, and any change to the `Animal` must happen through the `Person`.

The Aggregate here is simply the `Person` and the `Animal` together.

~~~mermaid
flowchart TB
subgraph Aggregate_Person [Aggregate]
    direction TB
    Person["Person"]
    Animal["Animal"]
    Person --> Animal
end
~~~

## Aggregate Root

An Aggregate Root is the main entity inside an Aggregate that acts as the single entry point to it.

It is the only object within the Aggregate that external code is allowed to reference or interact with.

The Aggregate Root is responsible for coordinating all changes inside the Aggregate and ensuring it stays in a valid and consistent state.

Any modification to internal entities or value objects must go through the Aggregate Root.

### Concrete Example

Let's keep the `Person` and `Animal` example again.

In this scenario, `Person` is the AggregateRoot, controlling every change inside the Aggregate.

All interactions and changes to `Animal` must go through the `Person`.

 ~~~mermaid
classDiagram
class Person {
    <<AggregateRoot>>
    +id
    +name
    +changeAnimalInfo()
}
class Animal {
    <<Entity>>
    +id
    +name
}
Person --> Animal : owns
~~~

## How to implement Aggregates and Aggregate Roots in C# code?

In C#, an aggregate is modeled as a “root” class (the Aggregate Root) that groups its entities and value objects, and it is the only entry point to modify them.

With DDD, the aggregate root contains the business rules that keep the aggregate consistent. The other objects inside the aggregate (entities and value objects) should not be accessed or modified directly from the outside. Instead, changes must go through methods on the aggregate root.

For example, in the Person–Animal scenario, if you need to change an Animal’s name, you must do it through the Person aggregate root (not by updating the Animal directly).

### Concrete Code Example

The `Person` class is the Aggregate Root: it owns the `Animal` entity and exposes `ChangeAnimalName()` as the only way to update it. `Animal.Name` cannot be set from outside, and `Animal.ChangeName()` is internal, so the name change must go through `Person`, which protects the aggregate’s rules.

```csharp
public class Person // Aggregate Root
{
    public Guid Id { get; init; }

    public string Name { get; init; }

    public Animal Animal { get; init; }

    public Person(Guid id, string name, Animal animal)
    {
        Id = id;
        Name = name;
        Animal = animal;
    }

    public void ChangeAnimalName(string newName)
    {
        Animal.ChangeName(newName);
    }
}

public class Animal // Entity
{
    public Guid Id { get; init; }

    public string Name { get; private set; }

    public Animal(Guid id, string name)
    {
        Id = id;
        Name = name;
    }

    internal void ChangeName(string newName)
    {
        Name = newName;
    }
}
```