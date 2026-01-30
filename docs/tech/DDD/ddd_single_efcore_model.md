# DDD Project Using a Single EF Core Model (No Separate Domain Model)

---

## Overview

This document explains how, in a DDD (Domain-Driven Design) project, you can structure your code and database **using only a single model**: the EF Core entity class.  
You do NOT need to maintain two separate models—one for EF Core (persistence) and another for "pure" DDD domain logic.  
With this approach, you keep things simple, and EF Core can still handle table and foreign key creation correctly.

---

## 1. Single Model: One class for domain AND database

You can define your classes like this, without separate `OwnerEntity` and `OwnerAggregateRoot`.
It is the same for `AnimalEntity` and `AnimalAggregateRoot` :

```csharp
public class OwnerAggregateRoot
{
    public Guid Id { get; set; }                  // PK
    public string Name { get; set; }
    public string Email { get; set; }

    // (Optional) Navigation property to easily navigate with EF Core
    // (Optional) Navigation property for Animals, can be omitted for strict DDD
    //public List<AnimalAggregateRoot> Animals { get; set; } = new();
}

public class AnimalAggregateRoot
{
    public Guid Id { get; set; }                  // PK
    public string Name { get; set; }
    public string Species { get; set; }

    // Important: Foreign Key (FK) to create the relationship in EF
    public Guid OwnerAggregateRootId { get; set; }

    // (Optional) Navigation property to easily navigate with EF Core
    // (Optional) Navigation property back to Owner, can be omitted for strict DDD
    //public OwnerAggregateRoot Owner { get; set; }
}
```

### Why does this work?

- EF Core **detects the foreign key** because of the `OwnerAggregateRootId` property in `AnimalAggregateRoot`.
- If you add the navigation property (`Animals` list) in `OwnerAggregateRoot`, EF Core automatically maps the one-to-many relationship.

---

## 2. Table & Foreign Key Generation

- Table **OwnerAggregateRoots** : stores all owners.
- Table **AnimalAggregateRoots** : stores all animals and has an `OwnerAggregateRootId` column (foreign key) pointing to the owner.

#### Equivalent SQL

```sql
CREATE TABLE OwnerAggregateRoots (
    Id UNIQUEIDENTIFIER PRIMARY KEY,
    Name NVARCHAR(100) NOT NULL,
    Email NVARCHAR(100) NOT NULL
    -- ...
);

CREATE TABLE AnimalAggregateRoots (
    Id UNIQUEIDENTIFIER PRIMARY KEY,
    Name NVARCHAR(100) NOT NULL,
    Species NVARCHAR(100),
    OwnerAggregateRootId UNIQUEIDENTIFIER NOT NULL,
    FOREIGN KEY (OwnerAggregateRootId) REFERENCES OwnerAggregateRoots(Id)
    -- ...
);
```

---

## 3. What if I do NOT want the navigation property in Owner?

You CAN remove the property:

```csharp
public class OwnerAggregateRoot
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
    // No List<AnimalAggregateRoot> Animals
}
```

In this case:
- **Keep the FK property** (`OwnerAggregateRootId`) in `AnimalAggregateRoot`.
- You may need to configure the relationship using `IEntityTypeConfiguration` because EF Core may not infer the navigation automatically. Example:

```csharp
public class AnimalAggregateRootConfiguration : IEntityTypeConfiguration<AnimalAggregateRoot>
{
    public void Configure(EntityTypeBuilder<AnimalAggregateRoot> builder)
    {
        builder.HasKey(a => a.Id);

        builder.HasOne(a => a.Owner)
               .WithMany() // No navigation property in Owner
               .HasForeignKey(a => a.OwnerAggregateRootId)
               .OnDelete(DeleteBehavior.Cascade); // Or another logic
    }
}
```

---

## 4. Summary

- **With a single model**, EF Core creates tables and the foreign key if:
    - You have a FK property (`OwnerAggregateRootId`) in AnimalAggregateRoot;
    - (Optional) You add a navigation property in OwnerAggregateRoot (`List<AnimalAggregateRoot> Animals`) for code navigation.

- **You do NOT need** two separate classes (`OwnerEntity` and `OwnerAggregateRoot`) for EF Core to work and create the expected database.

- **The key point** is having the FK property in the “many” side (here, `AnimalAggregateRoot`).

- **Important**: ⚠️ For strict DDD compliance, navigation properties between Aggregate Roots should be omitted, and only the ID should be used to reference other aggregates. This ensures proper aggregate isolation as recommended by DDD.

---

## 5. Pro tip

- Even with a single model, you can use configuration files (`IEntityTypeConfiguration<T>`) to clarify relationships if your properties do not follow EF Core conventions.

---

## 6. Good practices

- This approach is **perfect for demos, POCs, or simple projects**.
- For strict DDD or bigger projects, you might later separate your domain model and persistence model (with mapping between them).

---