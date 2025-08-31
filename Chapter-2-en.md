# Chapter 2: From Modeling to Implementation

## Use Case Diagram

A use case diagram describes the actions of a system in response to user requirements. For example, in a task management application, the use case diagram might look like this:

![Use Case Diagram](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/871c0a6e-2867-4019-9f2e-a874c1a6978a)

The **use case diagram** is created before the **domain model diagram**. Why do we need a use case diagram? Here are the reasons:
- Without clear use cases, designing the model becomes challenging. In the example above, from an employee's perspective, task management differs from a manager's perspective.

Additionally, the **scope** of the domain model must be defined. Without defining the scope, the problem's scope may become too broad, rendering the domain model design ineffective.

You can start with a small scope for the domain model and gradually scale it up. Accordingly, the scope of the use case diagram should also be scaled to maintain consistency.

## Domain Model Diagram

Essentially, this is a simplified version of a class diagram. It includes the following elements:
- Key properties of objects (methods are not necessarily included).
- Clearly defined rules and business logic.
- Representation of relationships between objects.
- Definition of multiplicity.
- Definition of the scope of Aggregates.
- Concrete examples for better understanding.

After designing the Aggregate, you can decide how to design and implement Repositories.

> Whether working on a use case diagram or a domain model diagram, it’s recommended to use a whiteboard during discussions with team members to facilitate idea exchange and completion of these diagrams.

For the use case diagram of the task management application, the domain model diagram might look like this:

![Domain Model Diagram](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/a046a6c0-af0d-42c5-a744-9b107867b93f)

You can see that one user can have 0 to n tasks, and the rules for creating or modifying users and tasks are also visible.

## How to Document Rules / Business Logic

When you have ideas or innovations regarding rules or business logic, document them as bullet points.

A typical approach is to use a **state transition diagram**, which is much clearer than plain text. In such cases, combining a **state transition diagram** with a **domain model diagram** is effective.

## How to Document Aggregates

When documenting Aggregates, it’s crucial to represent the relationships between objects. Distinguishing whether objects are inside or outside an Aggregate is important, as they are represented differently:
- Within the same Aggregate, use the **composition** notation to reference objects.
- Across different Aggregates, use the **association** notation to reference objects.

**Note**: The **association** and **composition** notations are part of UML. (Ref: https://github.com/tuananhhedspibk/TeckStack/issues/95)

## DDD Anemia

This term refers to a model that contains almost no business logic.

## Using Onion Architecture

Here, layers are defined to determine which classes are implemented in each layer.

![Onion Architecture](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/69f72072-9277-4698-83b5-4179ee477465)

## DDD Anemic Code

```TS
class TaskEntity {
  private id: number;
  private status: TaskStatus;
  private name: string;
  private dueDate: Date;
  private postponeCount: number;
}
// Assume each property has corresponding getters and setters
// e.g., setStatus, getStatus, setName, getName, ...
```

At first glance, this class seems fine, but when used in a use case like the one below, issues arise:

```TS
class TaskCreateUsecase {
  execute(name: string, dueDate: Date) {
    if (!name || !dueDate) {
      throw new UsecaseError();
    }

    const task = new Task();
    task.setStatus(TaskStatus.UNDONE);
    task.setName(name);
    task.setDueDate(dueDate);
  }
}
```

Everything seems fine, but consider the following class:

```TS
class EvilTaskUsecase {
  execute() {
    task.setStatus(TaskStatus.DONE);
    task.setName('');
  }
}
```

This introduces two problems:

**1. Data Overwriting Without Restriction**  
The issue arises because setters are public methods, allowing other use cases to modify the entity's data freely.

**2. Difficulty in Tracing and Reading Code**  
To resolve data conflicts, you’d need to trace references to setters across multiple places, increasing the cost of code reading and maintenance.

Moreover, understanding the model from such code becomes challenging.

## Where Should Domain Knowledge Be Written?

As seen in the example above, placing domain knowledge in the use case layer leads to significant issues. Instead of writing it in the use case layer, as shown below:

![Use Case Layer](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/16f625c6-02ac-4cd9-b55d-6e573f606aac)

Focus on writing it in the **domain layer**:

![Domain Layer](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/12e554cd-174c-4540-b0fb-cc244ed8f107)

When domain knowledge is written in the domain layer, the use case layer looks like this:

```TS
class TaskCreateUseCase {
  private taskRepository: TaskRepository;

  execute(name: string, dueDate: Date) {
    const taskEntity = new TaskEntity(name, dueDate);
    taskRepository.save(taskEntity);
  }
}
```

```TS
class TaskPostponeUseCase {
  private taskRepository: TaskRepository;

  execute(id: number) {
    const taskEntity = taskRepository.findById(id);
    taskEntity.postpone();
  }
}
```

Now, the use case layer is much more abstract. It:
- **Hides** the implementation details (HOW).
- **Only expresses** what is being done (WHAT).

The entity code in the domain layer looks like this:

```TS
class TaskEntity {
  private id: number;
  private taskStatus: TaskStatus;
  private name: string;
  private postponeCount: number;
  private dueDate: Date;

  constructor(name: string, dueDate: Date) {
    this.name = name;
    this.dueDate = dueDate;
    this.taskStatus = TaskStatus.UNDONE;
    this.postponeCount = 0;
  }

  postpone() {
    // implementation
  }
}
```

All public setters are eliminated, preventing unwanted value assignments.

## Basic Principles for Designing Objects in the Domain Layer

There are two fundamental principles:

① **Encapsulate all domain knowledge in the object.**

② **Only allow valid instances to exist.**

For principle ②, whenever data is saved to the database, validate whether the domain model object’s data causes conflicts. This can be done in two ways:

① **Impose conditions during instance creation**  
Create instances using a **constructor** or **factory method** to enforce data conditions, preventing data conflicts.

② **Impose conditions during modification**  
Enforce data conditions in methods that handle state transitions for entity instances. These methods can be exposed publicly. Unlike setters, which violate data conflict prevention, methods like `postpone` in the example above are safe.

Following these principles:
- Improves code readability.
- Enhances code maintainability.

## How to Manage Ubiquitous Language?

The Ubiquitous Language is created during the domain model design process. Instead of defining it in a separate document, include it with the domain model diagram for easier updates and maintenance.
