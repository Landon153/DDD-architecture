## Chapter 6: Implementing the Domain Layer

In the domain layer, we implement the "tactical" design pattern, specifically by representing the `domain model` and dividing it into two types as follows:
- Domain Objects: entity, value-object, domain-event
- Components that use domain objects: repository, factory, domain service

> Entities and value-objects represent the "things" of the domain model
> Domain-events represent the "events" of the domain model

The difference between `entity` and `value-object` lies in:
- `Entity` is distinguished by its ID
- `Value-Object` is a value used to ensure distinction

### Entity

For example, in a student management application, each student has a unique student ID (MSSV), which corresponds to an ID. When student A's academic achievements or address are updated, student A remains student A. However, another student named A (with a different MSSV) is considered a distinct entity from the aforementioned student A.

### Value-Object

Consider two 10,000 VND banknotes printed in 2022 and 2021. In terms of value, they are equivalent. However, in the context of a currency collection, due to the change in context, they are distinguished as separate "entities" based on their printing year.

Moreover, the value of these two banknotes is always 10,000 VND – IT NEVER CHANGES.

This implies:

> Entities can change, while Value-Objects are immutable

### Domain Service

Used when representing a model with a single object is not feasible. Typically, it involves operations with a collection of objects.

For example, a common use case is checking whether an email is duplicated—i.e., whether the email is already used by another user in the system. A single user object may know its own email but cannot access information about the emails of other objects, making it impossible for it to perform the check itself.

Such cases are typically handled by a `domain service`.

However:

> Strive to use entities and value-objects as much as possible and minimize the use of domain services

The reason is that if too much `business logic` is inadvertently written into domain services, they may unintentionally become a `Fat class` in the future.

### Repository

A repository is used to store data of an `aggregate` in a database.

The data of an `aggregate` typically has high cohesion.

A repository is associated with an `aggregate`, but passing an aggregate to the repository or retrieving an aggregate from the repository must go through the `root aggregate`. Referencing child objects within an aggregate must also be done via the `root aggregate`.

It is not permissible to directly return a child object from a repository or create a repository specifically for a child object.

> A repository should be used like a LIST

Specifically: A repository should not contain methods like `Register User` or `Suspend User`. Instead, it should contain a **LIST of users** with states such as `Registering` or `Suspending`. This approach ensures that the repository does not contain excessive methods related to business logic.

Moreover, this approach facilitates `in-memory mock` during testing. If a repository contains `business logic`, it may compromise the accuracy of tests.

### Factory

Used to create new objects when the creation logic is complex or when there is a need to reference another aggregate.

A factory can also be considered a type of `domain service`.

Since it represents domain logic, it can reference or use a `repository`.

### Other Objects in the Domain Layer

In addition to the "tactical" design approach described above, other design approaches can be used as long as they ensure `high cohesion` and `low coupling`.

For example: `First-class collection` or using `enum` to represent business logic.

> The most important aspect of the domain layer is to represent domain logic and write objects that represent the models.

As long as these two factors are ensured, any design or implementation approach is acceptable.

### Ensuring Data Consistency Between Aggregates

There are two ways:
① Implement at the use-case layer: In this layer, repositories corresponding to aggregates are used to store data. This approach is simple but may lead to data conflicts if other use-cases perform similar operations.

② Use domain events: Specifically, in the domain layer, events are created to interact with aggregates. This approach always ensures data consistency but is relatively complex to implement.

### QA - Purpose of Placing Logic in the Domain Layer

Placing too much logic in the domain layer can cause layers dependent on it to be affected if the domain layer changes.

Here, we have the principle of `Stable Dependencies Principle (SDP)`:

> It is difficult to modify a component that many other components depend on. Instead, let components depend on modules that are less likely to change.

The main purpose of making the domain layer independent is to make it `easily modifiable`. The domain layer constantly evolves to accurately and fully reflect business requirements. If the domain layer is affected by or dependent on technologies such as:
- Frameworks
- Databases
it can lead to situations where implementing business requirements becomes impossible because the framework or database does not support certain features.

When this happens frequently, the domain and actual business requirements drift apart, resulting in a system that cannot address the problem at hand.

If changes to the domain affect other layers, the testing process will handle this.

### QA - Using Primitive Data Types

In addition to using value-objects, you can consider using primitive data types if they are suitable.

### QA - How to Represent Data Retrieved from the Database in an Instance

You can create dedicated `constructors` that take input data and preserve the values when creating an instance.

Pay attention to the `private/public` visibility of these constructors.

### QA - Can Reconstruct Methods Be Written in the Infrastructure Layer?

This is entirely possible but should be considered depending on the programming language used.

For example, in Java, writing in the infrastructure layer may not leverage the language's **reflection** capabilities for data types.

### QA - Do Domain Objects Need to Have Attributes Identical to Database Table Columns?

Not necessarily. The key point is that **the infrastructure layer must depend on the domain layer**, so the attributes of the domain layer are determined by business requirements, not necessarily by the infrastructure layer.

### QA - Implementing Sorting

Sorting should be implemented in the infrastructure layer. The repository interface is highly abstract, so sorting implementation will occur in the infrastructure layer, indicated by a parameter specifying the sort order, for example.

### QA - Implementing Caching

Consider implementing caching with CQRS. Define separate query models for querying and implement caching to improve performance when retrieving data for the query model.

### QA - Relationship Between External APIs and Repositories

If values sent to or received from an external API are meaningful to the domain layer, they should also be defined in the domain layer.

Otherwise, consider the example of using notifications. Receiving and using notifications from an external API should be handled in the use-case layer. Define a `NotificationAdapter` interface in this layer and implement it in the infrastructure layer.

### QA - Storing and Using Data Retrieved from External APIs

The repository interface in the domain layer is highly abstract, focusing only on `what conditions are provided and what data is retrieved`. The processes of:
- Retrieving data
- Transforming data
are handled in the infrastructure layer.

### QA - Cohesion Between Entities

For example, if one user has N tasks, which
