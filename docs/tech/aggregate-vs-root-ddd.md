# Aggregate & Aggregate Root in DDD

## Aggregate  

An **aggregate** is a **group** of domain objects (entities and value objects) that work together and must stay consistent with business rules.

Importantly, the aggregate is the root **and** everything inside it that the root controls. It is a **group**.

Let's imagine a process where there are `Person` and `Animal`. It's only possible to change the animal's information via the person. The Aggregate here is the `Person` and the `Animal` together, along with all other value objects they own or reference.

---

## Aggregate Root  

The **aggregate root** is the main entity of the aggregate.  
It is the **single entry point**: all interactions to manipulate the aggregate go through it.  
The aggregate root controls access to the entire aggregate and enforces business rules.
 
In the aggregate that groups the `Person` and `Animal`, the aggregate root is the `Person` itself.

---

## What's the difference?

- **Aggregate** = the whole group controlled by one root
- **Aggregate Root** = the boss of the group

**You always access the aggregate via its root.**  
No part inside the aggregate should ever be changed or retrieved directly from outside.

---

## How is it in C#?

In C#, the aggregate is implemented as a class that acts as both the aggregate root and the container of its child entities and value objects.

- You expose only the aggregate root class (for example, `Person`).
- All business logic that changes the aggregate goes through this root class.
- The other classes (entities, value objects) are just members âinsideâ that root class and are not accessed or changed independently.
- For external code, you only ever use the rootâthe two ideas blend in code, even if they are separate concepts in DDD theory.
- The aggregate root represents the whole aggregate (it is the front door and the house at the same time).

---

## C# Example

```csharp
public class Person // Aggregate Root (and represents the Aggregate in code)
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public Animal Animal { get; private set; }

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

public class Animal // Just an entity inside Person (part of the aggregate)
{
    public Guid Id { get; set; }
    public string Species { get; set; }
    public string Name { get; private set; }

    public Animal(Guid id, string species, string name)
    {
        Id = id;
        Species = species;
        Name = name;
    }

    public void ChangeName(string newName)
    {
        Name = newName;
    }
}
```

---

## Case: Animal is also an Aggregate Root

If `Animal` has its own business rules and can change independently of `Person`,  
then **Animal** is its own aggregate root.

```csharp
public class Person // Aggregate Root
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public Guid AnimalId { get; private set; }

    public Person(Guid id, string name, Guid animalId)
    {
        Id = id;
        Name = name;
        AnimalId = animalId;
    }
}

public class Animal // Separate Aggregate Root
{
    public Guid Id { get; set; }
    public string Species { get; set; }
    public string Name { get; set; }

    public Animal(Guid id, string species, string name)
    {
        Id = id;
        Species = species;
        Name = name;
    }
}
```

> Modifications to `Animal` happen through the Animal repository or service, not directly through Person.

> **Important:**  
> If `Animal` is also an Aggregate Root, the animal and the person each become separate aggregatesânot one single aggregate together. The relationship is then a link by ID, not direct inclusion, and each aggregate enforces its own business rules independently.

---

## Key principles

- The aggregate root manages all changes and access for its group.
- The aggregate is the root and everything it contains as a coherent whole.
- If an entity (like Animal) needs to have independent business rules, it should become its own aggregate root.
- You should **never** allow one aggregate root to directly modify another aggregate rootâs data.
- Other members of the aggregate (entities/value objects) can only be accessed or changed through the root.

---

## Analogy

- Aggregate = a whole house (Person and all that belongs to them)
- Aggregate Root = the front door (Person class); you always enter/leave/interact via this door, never through a window!

---