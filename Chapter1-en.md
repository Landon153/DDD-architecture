# Chapter 1: Basic Concepts of DDD

## What are Domain, Model, Domain Model, Data Model, and Modeling?

- **Domain**: The field or area that the software aims to address a problem for.
- **Model**: A system of abstractions that describes an aspect of the domain and can be used to solve problems related to that domain.
- **Modeling**: The process of creating a model.
- **Domain Model**: A model used to solve problems within the domain.
- **Data Model**: A model used to address issues related to data storage.

## What Should Be Included in the Model?

Careful consideration should be given to what is necessary to include in the model and what is not.

For example, with a **Resume** model, the following **important** information should be included:
- Name
- Work experience
- Reason for applying
- Profile picture

The following elements are considered **unimportant**:
- Signature
- Resume maker
- Emotions when writing the resume

## Ensuring Software Inherits the Model's Characteristics

If there is a significant difference between the model and the software implementing it, solving the problem (which is the primary purpose of the model) becomes increasingly difficult.

Moreover, understanding the model by reading the code also becomes more challenging.

Therefore, keep the code and the model as "close" as possible. This is why DDD strongly supports Object-Oriented Programming (OOP).

## "Isolated" Model Architecture

Processing related to **UI** and **DB** should not be "mixed" into the model's code. This is because:
- The amount of code in the model would increase significantly.
- Code readability would decrease.
- Code maintainability would be reduced.

Thus, processing related to **UI**, **DB**, and other components should be **separated into distinct layers**. The layer responsible for the model is called the **domain layer**.

In practice, three common architectures often appear:
- Layer Architecture
- Onion Architecture
- Clean Architecture

## Tactical Design Patterns

We can observe effective designs with:
- Entity
- Repository

These designs are referred to as **Light DDD**. However, if they do not help the software address real-world problems, they are meaningless.

Therefore, in practice, apply:
- Design patterns
- Modeling

together to gradually improve your design, with the goal of enabling the software to solve real-world problems.

## Start Small, Fail Small

Creating a complete model from the start is impossible. Therefore, follow this iterative cycle:

**MODELING → TRY → FIX**

Until you find the most suitable model for your needs. Moreover, if you need to create a design that applies to multiple applications simultaneously, this is extremely difficult and can easily lead to frustration. Start with a small part of the application, and once it feels stable, gradually scale up.
