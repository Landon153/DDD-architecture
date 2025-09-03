# Chapter 3: DDD Modeling Methods

## What is an Aggregate?

An Aggregate is a collection of closely related objects that must maintain data consistency as a single unit.

## Rules for Designing and Implementing Aggregates

There are two main rules:

① **Data consistency must be ensured within a single Aggregate.**  
② **Only one transaction is allowed per Aggregate.**

For rule ①, consider the following example: In a school, clubs have members. When a club has 5 members, its status becomes "full"; if it has 4 or fewer members, it is "not full."

![Club and Member Relationship](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/f4597436-3e8f-4a6c-9a5b-a209e82b619f)

Here, the status of the club and the number of members are tightly coupled. Therefore, `Club` and `Member` belong to the same Aggregate, while `Student` belongs to a different Aggregate.

In an Aggregate, the parent object is called the **Root Aggregate** or **Aggregate Root**.

![Aggregate Root](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/5c7a63c7-9748-45a3-a717-1eea4250c501)

For rule ②, **Aggregates must be retrieved from and saved to the repository as a single unit**. The primary goal is to ensure data integrity within the Aggregate.

In the example above, if individual members could be deleted from a club, the club's status might incorrectly change to `NOT_FULL`, compromising data integrity. Therefore, all operations on `Member` must go through the `Club` Aggregate.

## How to Define Aggregate Boundaries

There is no standard formula for defining Aggregate boundaries.

> Consider multiple perspectives and find the most balanced solution.

There are two main perspectives:

**Emphasis on Data Integrity**  
If data integrity is the focus, consider consistency within and between Aggregates. However, implementing this can be complex and costly. Whether to enforce integrity **within one Aggregate** or **across multiple Aggregates** depends on the designer’s judgment.

**Reasonable Transaction Scope**  
As mentioned, Aggregates are retrieved and updated within a single transaction. If the transaction scope is too large, it may lead to prolonged database locking.

For the club example, if `Club` is a child of a `School` object, updating a single `Club` could lock the entire `School` and other `Clubs`, preventing updates.

![Transaction Scope](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/7f17b146-2ff0-4938-afaa-481125ad91d3)

## Bounded Context

A Bounded Context can be understood as:

> Clearly defining the scope in which a specific model is applied.

Two typical examples are teams and subsystems.

Consider a more specific example: **Model Sharing**. Suppose you’re building an e-commerce (EC) system involving multiple teams:
- Development team
- Sales team
- Logistics team
- Accounting team

You’re designing a `Product` model, but each team has a different understanding of what a `Product` entails.

![Product Model Differences](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/7b191c0d-e11f-4459-baa5-9d9f74ae1a2c)

> As a project grows in scale, creating a unified model for the entire system becomes nearly impossible and costly.

As Eric Evans’ famous DDD book states:

> Building a unified model for an entire system is both infeasible and expensive.

Therefore, in DDD, **clearly define the scope of a model’s application, and within each scope, use a single model and a consistent language**.

This clearly defined scope is called a **Bounded Context**.

For the e-commerce example above, you would split it into two contexts:
- Sales Context
- Logistics Context

![Bounded Contexts](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/c37ac11e-ce63-4587-a547-504071f576e9)

Within each context, use **one model** and **one language** exclusively.

## Thinking About the Database

When designing a domain model diagram, it’s easy to be influenced by the database structure. To avoid this, imagine you’re using a NoSQL database where everything in the system is an object.

## Aggregates and Transactions

When designing a domain model diagram, avoid technical details and focus on creating a highly abstract diagram. However, when designing Aggregates, pay attention to transactions.
