# Chapter 4: Basic Design Principles

One of the key principles in design is **High Cohesion, Low Coupling**.

## About Cohesion and Coupling

These two qualities serve as metrics for software quality. They enhance the following software characteristics:
- Code readability
- Scalability
- Reliability: less prone to bugs during code changes
- Reusability: code functionality can be reused in multiple places
- Testability

## Cohesion

Cohesion refers to the degree of connection between a class's `business logic`, `data`, and `methods`. The stronger this connection, the better.

`Business logic` defines what a class is responsible for and what functions it performs.

If the `business logic` is unclear, the design becomes poor, and achieving high cohesion with `data` and `methods` becomes challenging.

### Example of Low Cohesion

```TS
class OperationUtil {           // Vague class name, unclear functionality
  private count: number = 0;

  public void increment() {     // What is being incremented, and from where?
    count++;
  }

  public static void greet() {  // Method unrelated to the count variable
    console.log('Hello');
  }
}
```

When the `business logic` of a class is not clearly defined, unrelated methods may be added, increasing maintenance costs.

### Example of High Cohesion

```TS
class Counter {                        // Clear class name, obvious purpose
  private count: number = 0;          // Data variable relevant to the class

  public void increment() {           // Method related to count and class name
    count++;
  }

  public getCurrentCount(): number {  // Method related to count and class name
    return count;
  }
}
```

## Coupling

Coupling refers to the degree of dependency between classes.

- **Cohesion** applies to the **internal scope of a single class**.
- **Coupling** applies to **interactions between multiple classes**.

In practice, we aim for the lowest possible coupling between classes. High coupling means that a change in one class can affect others, creating a ripple effect. Additionally, requiring multiple classes to work together to perform a function is considered **high coupling**.

### Example of High Coupling

```TS
class Printer {
  public static void print() {
    console.log(Counter.count);   // Depends directly on Counter's count property
  }
}

class Counter {
  public static count: number = 0;
  public static void increment() {
    count++;
  }
}

Printer.print();   // Output: 0
Counter.increment();
Printer.print();  // Output: 1 → Hard to understand why the result increments from the outside.
```

### Example of Low Coupling

```TS
class Printer {
  public static void print(count: number) {
    console.log(count);
  }
}

class Counter {
  private static count: number = 0;
  public static getCount(): number {
    return count;
  }
  public static increment(): void {
    count++;
  }
}

Printer.print(Counter.getCount());
```

In this example, the `Printer` and `Counter` classes are only connected through parameter passing, ensuring low coupling.
