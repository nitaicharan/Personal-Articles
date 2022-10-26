# An introduction to Aspect-Oriented Programming

Created: November 22, 2021 7:02 PM
Finished: No
Source: https://medium.com/@blueish/an-introduction-to-aspect-oriented-programming-5a2988f51ee2

![An%20introduction%20to%20Aspect-Oriented%20Programming%208d4b4d50dc194de0a82b5c2e9b50c601/1fQf22XtVy4Dl0AdkwzLjaQ.png](An%20introduction%20to%20Aspect-Oriented%20Programming%208d4b4d50dc194de0a82b5c2e9b50c601/1fQf22XtVy4Dl0AdkwzLjaQ.png)

# Table of Contents

# Aspect-Oriented Programming definition

> In computing, aspect-oriented programming (AOP) is a programming paradigm that aims to increase modularity by allowing the separation of cross-cutting concerns.
> 
> 
> **[Cross-cutting concerns](https://en.wikipedia.org/wiki/Cross-cutting_concern)** are aspects of a program that affect other concerns. These concerns often cannot be cleanly decomposed from the rest of the system in both the design and implementation, and can result in either *scattering* (code duplication), *tangling* (significant dependencies between systems).
> 

Examples of concerns that tend to be cross-cutting include:

- Caching
- Data validation
- Logging
- Format data
- Transaction processing

## Take this scenario as an example:

You develop a service with lots of `getter` methods to get data from the database and format the result in JSON string.

```
classService{
getUser({id, name}) {
    if (!Number.isInteger(id))
      return null

const user = DAO.fetch(User, id, name)    return JSON.stringify(user)
  }

getArticle({id}) {
    if (!Number.isInteger(id))
      return null

const article = DAO.fetch(Article, id)

    return JSON.stringify(article)
  }

  // getXyz ...
}
```

The **Data Access Object** (DAO) can be simplified as:

```
constDAO= {
  // database access, query and retrieve the results here
  fetch(model, ...params) {
    return new model(...params)
  }
}
```

And here are your models:

```
classArticle{
  constructor(id) {
    this.id = id
  }
}classUser {
  constructor(id, name) {
    this.id = id
    this.name = name
  }
}
```

As you can see, any `getter` methods of the `Service` can be divided by 3 parts (or 3 concerns):

- **Data validation**: Check if `id` is integer.
- **Main concern**: Communicate with data access layer.
- **Format data**: Convert result to JSON string.

In this example, these 3 concerns are over-simplified. But in real-world applications, the **main concern,** the **data validation,** and the **format data** logics can get very complicated.

The **data validation** and **format data** logics are **cross-cutting concerns** and should be isolated from the **main concern** because they distract developers from concentrating on the main business logic.

Thus, we should separate and put them in 3 different places:

- The **Service** module should *only* deal with the **main concern**, or the core business logic.
- The **data validation** module should *only* take care of validating data.
- The **format data** module should *only* take care of formatting data.

# Apply AOP with Javascript

There are many libraries and frameworks that allow us to implement an AOP solution to cope with cross-cutting concerns.

Here, we use a Javascript library: **[aspect.js](https://github.com/mgechev/aspect.js)**

Removing all the cross-cutting concerns from the main concern, we rewrite the `Service` module as follow:

```
import {Advised} from 'aspect.js'@Advised()
class Service {
  getUser({id, name}) {
    return DAO.fetch(User, id, name)
  }

  getArticle({id}) {
    return DAO.fetch(Article, id)
  }
}
```

The `@Advised` **decorator** helps **wire** the `ValidateAspect` and `FormatAspect` into the `Service` class. The implementation of `Service` is very clean and easy to read. It only focuses on its main responsibility.

Finally, we can try calling some methods to see the result:

```
console.log(service.getArticle({id: '1'}))
// nullconsole.log(service.getUser({id: 2, name: 'Hello Kitty'}))
// {"id":2,"name":"Hello Kitty"}
```

You can clone the full source code here:

# Core concepts of AOP explained

## 1/ Aspect

An **aspect** is a module which has a set of cross-cutting functions.

E.g: `ValidateAspect`, `FormatAspect`

To form an **aspect**, we define **pointcut** and **advice**.

## 2/ Join point

A **join point** is a specific point in the application where we can plug-in the AOP **aspect**. Such as method execution, exception handling, variable modification… Most of the time, a **join point** represents a method execution.

## 3/ Advice

An **advice** is an action taken at a particular **join point**. In other words, it is the actual code that gets executed before or after the **join point**.

E.g: `@beforeMethod`, `@afterMethod`, `@aroundMethod`

## 4/ Pointcut

A **pointcut** is a predicate that matches with **join point** to determine whether **advice** needs to run. Depending on the libraries or frameworks, **pointcut** can be specified differently with pattern or expression.

With the library **[aspect.js](https://github.com/mgechev/aspect.js)** used in this article, a **pointcut** is specified with Javascript regular expression (Regex):

## 5/ Target object

A **target object** is an object being **advised** by one or more **aspects**. Also referred to as the **advised object**.

E.g: The `Service` class used in the example.

## 6/ Weaving

**Weaving** is the process of linking **aspects** with other objects to create an **advised object**.

E.g: Apply weaving on the `Service` class with `@Advised` decorator.

# A few thoughts on AOP

## Benefits

- Embrace modularity.
- Reduce coupling between dependencies.
- Make code easier to read, reuse, and maintain.

## Drawbacks

- Not included as part of programming languages, must rely on libraries to work.
- Debugging can be difficult because code flow is more than meets the eyes.
- Require disciplines to not overuse.

Aspect-Oriented Programming is considered “dark art” in the OOP world because it can intervene in your code and perform some black magic behind the scene. For example, it can modify your method, replace the result, or even block your code from executing.

Despite all the power it gives, we should use AOP with care and deep understanding. Unnecessary use of AOP in your application can be harmful and may lead to many unexpected errors.

**Aspect-Oriented Programming** is **not** an absolute replacement of **Object-Oriented Programming**. Instead, they are companion.

> Aspect-Oriented Programming complements Object-Oriented Programming by providing another way of thinking about program structure. The key unit of modularity in OOP is the class, whereas in AOP the unit of modularity is the aspect.
>