---
title: Book Notes on DDIA
---

# Designing Data-Intensive Applications

This is a collection of my Notes on the Book [Designing Data-Intensive Applications](https://www.amazon.com/Designing-Data-Intensive-Applications-Reliable-Maintainable/dp/1449373321) by Martin Kleppmann.

## Chapter 1: Reliable, Scalable, and Maintainable Applications

> The internet was done so well that most people think of it as a natural resource, like the Pacific Ocean, rather than something that was man-made. When was the last time a technology with a scale like that was so error-free?
- Alan Kay, in interview with Dr. Dobbs Journal (2012)

Chapter 1 is a great introduction to the book, and the topics it covers. It strives to give some overview and context about the core ideas that will be touched on.

*What even is a data-intensive application?* Martin defines it as an application that stores data, and that data is important. This is a pretty broad definition, but it's a good starting point.

> The primary goal of software engineering is to build programs that are correct, performant, and maintainable. This is a very broad goal, and it’s not always clear how to achieve it. In particular, it’s not obvious how to achieve it at the scale of a large software system.
- Martin Kleppmann

He goes on to list some of the challenges that come with building a data-intensive application:

- **Reliability** - The system should continue to work correctly even in the face of adversity (hardware or software faults, and even human error).
- **Scalability** - As the system grows (in data volume, traffic volume, or complexity), there should be reasonable ways of dealing with that growth.
- **Maintainability** - Over time, many different people will work on the system (for different reasons), and they should all be able to work on it productively.

These three goals are the core goals of the book. It will cover how to achieve them in the context of data-intensive applications.

## Chapter 2: Data Models and Query Languages

The second chapter deals with how Data is modeled and accessed in different ways.

> Data models are perhaps the most important part of developing software, because they have such a profound effect: not only how the software is written, but also on how we *think about the problem* we’re solving.

The book goes on to discuss the different types of data models, and how they are used. It also discusses the different types of queries that can be made against a data model, and how they are used. I'll give a brief overview of the key points made, specifically about relational models, document models, and graph models.

### Relational Models

Relational models are the most common type of data model. They are used in most relational databases, and are the basis for SQL. They are also the basis for many other data models, such as document models and graph models.

Relational models are made up of tables, which are made up of rows and columns. Each row represents an entity, and each column represents an attribute of that entity. The rows are identified by a unique key, which is usually a single column, but can be a combination of columns.

The main benefit of relational models is that they are **very easy to query**. They are also very easy to maintain, as they are very structured. The downside is that they are not very flexible, and can be difficult to extend.

### Document Models

Document models are a type of data model that is used in many NoSQL databases. They are made up of **documents**, which are made up of **key-value pairs**. Each document represents an entity, and each key-value pair represents an attribute of that entity. The documents are identified by a unique key, which is usually a single value, but can be a combination of values.

The main benefit of document models is that they are **very flexible**. They are also very easy to query, as they are very structured. The downside is that they are not very easy to maintain, as they are not very structured.

### Graph Models

Graph models are a type of data model that has largely been popularised by their use in social networks, for so called **social-graphs**. They are made up of **nodes** and **edges**. Each node represents an entity, and each edge represents a relationship between two entities. The nodes are identified by a unique key, which is usually a single value, but can be a combination of values.

Graph models are very good at **evolvability**, meaning its easy to add net features to an existing data model. Thats why they are preferred over relational models for cases where the data contains a lot of many-to-many relationships. The downside is that they are not very easy to query, as usually one needs to either know the exact path to some piece of data, or use a query language that is specifically designed for graph models.

### Summary

To boil it down to three key points, each of the data models has largely evolved to target one specific use case:

- **Relational Models** - Use-cases with highly structured and hierarchical data with a well-defined schema, and a lot of one-to-many relationships.
- **Document Models** - Use-cases where data is largely in self-contained documents, and relationships between documents are rare.
- **Graph Models** - Use-cases where anything is potentially related to anything else, and relationships between entities are many-to-many.
