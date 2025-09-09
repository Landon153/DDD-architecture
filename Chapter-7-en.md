## Chapter 7: Implementing the Use-Case Layer

### Use-Case

The use-case class utilizes the `public methods` of entities defined in the domain layer to execute a specific use-case of the system.

For example, a use-case for changing the status of a `Task`:

```TS
class TaskPostponeUsecase {
  constructor(private taskRepository: TaskRepository) {}

  execute(taskId: number) {
    const taskEntity: TaskEntity = this.taskRepository.findById(taskId);
    taskEntity.postpone();
    this.taskRepository.save(taskEntity);
  }
}
```

This approach enhances the abstraction of methods in the domain layer, as the use-case layer only needs to focus on using the methods without needing to understand their implementation details.

> The primary responsibility of the use-case layer is to use the public methods of entities to implement use-cases.

### Return Value Class from Use-Case

Regarding the transfer of return values from the use-case layer to the presentation layer, there are two approaches:

① Create a dedicated class to hold the data type being passed down.

② Pass the domain object directly to the presentation layer.

**Approach ①** has the following advantages and disadvantages:

**Advantages**
- Prevents the presentation layer from accessing methods containing domain business logic.
- Avoids changes in the domain layer affecting the presentation layer.

**Disadvantages**
- Increases the cost of data type conversion.

**Approach ②** has advantages and disadvantages that are the complete opposite of approach ①.

If approach ② is implemented, the domain object needs to include `methods for display purposes`, which can cause the domain object to become bloated and difficult to maintain. Therefore, while approach ② saves effort on data conversion, the trade-off is equally significant.

**Naming the Return Value Class**

You can name the class using the format **XXXDTO**, where **DTO** stands for **Data Transfer Object**.

![Screenshot 2024-05-02 at 23 20 54](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/fcd98f68-7f12-47b3-bdd1-2ee38f2b2138)

### QA - Dividing Classes in the Use-Case Layer

Typically, the approach is `1 class - 1 public method`.

In cases where common processing is needed, extract the common processing into a separate class so that other classes can reference it.

Example: A converter class.

### QA - Naming Classes

Naming conventions may vary depending on the project.

For classes with multiple methods, omit the accompanying verb (e.g., `TaskUsecase`).
For classes with a single method, retain the verb (e.g., `CreateTaskUsecase`).

Naming should begin when designing the use-case diagram (identifying use-cases and their tasks).
