# Chapter 5: Architecture

## Three-Tier Architecture

This is a widely used architecture for web systems.

![Three-Tier Architecture](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/a204467f-0bd8-4a0a-a4e5-3fc0bd558c3b)

A key issue with this architecture is the **low cohesion** in the `business logic` layer. Classes like `XXXService` or `XXXLogic` often grow to hundreds or thousands of lines, increasing maintenance costs and reducing code readability.

Why is cohesion low? Consider a task management application with the following features:
- Users cannot register multiple emails simultaneously.
- A newly created user has the `USING` status but can transition to `SUSPENDING`.
- Tasks can only be assigned to users with the `USING` status.
- Tasks have only two statuses: `COMPLETE` or `DOING`.

The use case diagram for this application is as follows:

![Use Case Diagram](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/fc517506-8f67-4ac4-86e1-0fc815fb6c35)

The domain knowledge can be divided into two groups:

① **User**:
- Cannot register multiple emails simultaneously.
- A new user starts with the `USING` status but can transition to `SUSPENDING`.

② **Task**:
- Can only be assigned to users with the `USING` status.
- Has only two statuses: `COMPLETE` or `DOING`.

Since these two domain knowledge groups are distinct, combining them into a single `business logic layer` is a mistake, leading to an overloaded layer and reduced cohesion.

Moreover, domain knowledge consists of both `model` and `table` components, causing it to be scattered into the `data access` layer, which increases coupling between layers.

## Layered Architecture

![Layered Architecture](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/a204467f-0bd8-4a0a-a4e5-3fc0bd558c3b)

In this architecture, the `business logic layer` is split into two layers:
- **Application Layer**: Handles use case execution.
- **Domain Layer**: Handles domain knowledge.

This increases cohesion between layers. However, a problem arises: the domain layer depends on the infrastructure layer (e.g., database or ORM), which should be avoided.

## Onion Architecture

Similar to Layered Architecture, but the dependency relationship between the **Domain Layer** and **Infrastructure Layer** is reversed.

Note: The white arrows in the diagram below represent interface implementations.

![Onion Architecture](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/04c47574-7338-4080-bd74-d984485c41e2)

Since `repository interfaces` are implemented in the **Infrastructure Layer**, the **Infrastructure Layer** depends on the **Domain Layer**.

Key considerations for each layer:

**Domain Layer**:
- Must be independent and not depend on other layers.
- Contains classes like `Domain Entity`, `Value-Object`, `Domain Event`.
- Includes `Repository Interfaces`, `Domain Service`, and `Factory` using the above classes.

**Use Case Layer**:
- Uses public methods of `Domain Entity`, etc.
- Does not depend on the client.

**Presentation Layer**:
- Interacts with the client.
- Contains classes like **Controller** and **External-Controller**.

![Layer Interaction](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/b764c98c-d332-4e95-8f7a-992d3d4f4f65)

Requests from the client reach the **Adapter** (controller). Similarly, database interactions occur through the **Adapter** (implementation classes in the infrastructure layer).

## Hexagonal Architecture (Ports and Adapters Architecture)

The core idea is that the application interacts with the outside world through:
- **Adapters**
- **Specialized Ports**

![Hexagonal Architecture](https://user-images.githubusercontent.com/15076665/174482537-dc017d93-6ff8-4baa-b14a-f9b086bd75a2.png)

Conceptually, **Hexagonal Architecture** is similar to **Layered Architecture**, as the former inspired the latter, but it has a clearer separation of layers within the application.

## Clean Architecture

This architecture combines and builds upon **Onion Architecture** and **Hexagonal Architecture**.

![Clean Architecture](https://user-images.githubusercontent.com/15076665/175429364-68f2d02f-2956-4278-8ea6-c84ae3377139.png)

Layer naming may differ slightly:
- **Use Case Layer** → **Application Layer**
- **Adapter Layer** → **Interface Adapter Layer**

However, it fundamentally inherits all elements from **Onion Architecture** and **Hexagonal Architecture**.

## Comparison Between Onion Architecture and Clean Architecture

Both architectures inherit the principles of **Hexagonal Architecture**, but there are two key differences when applying DDD:

**1. Simplicity**  
**Onion Architecture** has fewer components than **Clean Architecture**. For a typical web application, the additional complexity of **Clean Architecture** may not be necessary.

**2. Different Perspectives on Layer Responsibilities**  
In **Clean Architecture**, the **Entity Layer** is described as containing `enterprise-wide business rules`, meaning its rules apply across the entire system or enterprise. However, in DDD, we must clearly define separate contexts within a system or enterprise, making this approach less suitable for DDD.

## Implementing Context Separation

When discussing context separation, one straightforward approach is **1 context = 1 application**, which aligns with implementing microservices.

This approach is challenging and costly.

Alternatively, you can use **1 application = n contexts**. This approach is difficult in terms of context separation within a single application.

**1 Context = 1 Application**  
Returning to the e-commerce example, you can split the EC system into two contexts:

![Context Separation](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/a1431a07-67f3-4c0b-96ff-91d4455f1952)

The implementation diagram is as follows:

![Context Interaction](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/c951cd08-c2ef-4b4b-abfe-803eff897fed)

This diagram illustrates how two contexts interact. There are two interaction methods:
- **Synchronous**: Through direct calls (e.g., REST API calls).
- **Asynchronous**: Through event sending (e.g., AWS SQS).

**1 Application = n Contexts**  
You can organize folders corresponding to each context, as shown below:

```
app
--- delivery
------- domain
------- infra
------- repo
--- sale
------- domain
------- infra
------- repo
```

> Carefully organize folders from the start to make it easier to split the application when it grows larger later.
