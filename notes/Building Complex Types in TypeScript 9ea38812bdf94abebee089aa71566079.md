# Building Complex Types in TypeScript

Created: September 22, 2022 1:25 AM
Finished: No
Source: https://medium.hexlabs.io/building-complex-types-in-typescript-bfdf5e131dbe

# Part 1: Building Blocks

The type system in TypeScript is extremely powerful, giving the developer the ability to pull problems that would arise at runtime into the compiler to be resolved as soon as the code gets written.

When writing type definitions it can get pretty abstract. This article will attempt to explain how to build complex types using fundamental concepts.

**Part 1** (this article) will explain the building blocks used to build complex types, skip this if you know them already.

**[Part 2](https://medium.hexlabs.io/building-complex-types-in-typescript-804c973ce66f)** builds some complex types by example using these building blocks.

![Building%20Complex%20Types%20in%20TypeScript%209ea38812bdf94abebee089aa71566079/1d0LMS0r3m_U9W7yX8bnCCA.png](Building%20Complex%20Types%20in%20TypeScript%209ea38812bdf94abebee089aa71566079/1d0LMS0r3m_U9W7yX8bnCCA.png)

An example of complex types with tail recursion

# The Basics

TypeScript provides several ways to declare type information, `interface`, `type`, `function`, `class`, the lambda `() => void`, and of course the primitives `boolean, number, string ...` etc. Values can be used as types too.

## Unions

Unions can be used to define a type that represents either of the options, for example, you can specify that something is either a `string` **or** a `number`.

## Intersections

An intersection allows you to specify that a type is all of the options, this is useful when combining object definitions.

## Type Aliases

We will use type aliases a lot in this article, they allow you to provide a name for a complex type so it can be used later. Use `type` to declare a new type alias. Unions and intersections cannot be used when defining an `interface` so type aliases provide a way of achieving more complex type definitions. See `MyError` in the example above.

## Generic Types

Types can be created with generic parameters so that the result is dependent on the given parameter.

## The ? and undefined

A type can define optional parts in two ways, note that they have a similar result but are slightly different. When designing types knowing the difference is crucial.

`test1` above shows that with `?` you don’t have to provide the value, but with `test2` you must provide a value, that value can be `undefined`. You could provide `undefined` for `test1` also. This becomes important when you want to retrieve the keys of a type since in `test1` the keys are `['a']` but in `test2` the keys are `['a', 'b']`.

## The keyof operator

A really powerful tool when building types is the ability to pull out all the keys from a type. A simple example of this is getting all the keys from an object. This also works for arrays, as arrays are also objects.

> Note that keyof returns a union of the keys, this is useful but hard to work with sometimes.
> 

## as const

A value can be solidified into a `readonly` type using `as const`. This will take all of the values and maps them to their type equivalent, or to the least generalized version.

## Type restriction using extends

The `extends` clause allows you to restrict a generic type. This allows you to guarantee certain things later.

## Tuples

A tuple is similar to an array except that it has a set number of elements and each element is typed.

```
// A tuple with two elements, the first a string, the second a number.
type MyTuple = [string, number]const example: MyTuple = ['a', 2];
const invalid: MyTuple = ['a', 2, 1]; //won't compile
```

## Utility Types

There are a range of types provided for you that can help when building your own types. The full list can be found in the link below.

[Building%20Complex%20Types%20in%20TypeScript%209ea38812bdf94abebee089aa71566079/0o73YVy1y9yI52oTz](Building%20Complex%20Types%20in%20TypeScript%209ea38812bdf94abebee089aa71566079/0o73YVy1y9yI52oTz)

Some Utility types I use regularly

# Advanced Types

The above sections include most of the building blocks we need to build useful types. There are a few advanced features that let you go further.

## Index Signatures

An index signature uses square brackets to dynamically set keys. Here is an example of an object that has any number of keys where all their types are `string`. Note that the `key` here is typed, this can be a `string`, `number`, or `symbol`, See the `in` keyword below for more options.

## The in keyword

The `in` keyword can be used to constrain the values that a key can be within an index signature.

## Mapped Types

A generic type can convert one type to another using a mix of an index signature, the `keyof` syntax and the `in` keyword. This is what it looks like.

```
// Map the values of T to string
type MyMappedType<T> = { [key in keyof T]: string }
```

Say I was wanting to write a function that took any object and outputted the same structure but all the values were made into strings. For example, environment variables are always strings. Here is a contrived example of a type that would support such a function.

## The as Keyword

The `as` keyword can be used to transform each key into a new name, here we add `_ENV` to the end of each key to make a new object.

> Here the key is intersected with string since keys can be string, symbols or numbers. This will become a recurring problem when creating complex types.
> 

## What about the value type? T[key]

While mapping over types each key of an object can be used to update the value, but what about the type of the original value, we can get access to it in the following way. This example maps an object to itself without changing the type.

> Believe it or not, although this results in the same type, if you do this to a return type of a function, the IDE will expand out the object and present it better to the user when they hover to get more info. This is useful for building better libraries.
> 

Now we have the ability to make decisions based on the original value or update the original without destroying it.

In this example, every element is unioned with `undefined` to make each value optional.

There is a better way to do this using the mapping modifier `?`.

## Mapping Modifiers

When using mapped types you can update the modifiers that are applied alongside updating types. In the above example, we made a solution that makes all the elements in an object optional, however, the better way to do this is to use `?`. Here is the same example using the modifier `?`.

There is also a `readonly` property that can be set.

**Removing Modifiers**

Alongside the ability to set modifiers, you can also remove them. Here are the opposites for the two example above.

> These are all examples of utility types that are provided by TypeScript
> 

## Recursive Types

TypeScript introduced the ability to build a type that can reference itself. This has a limitation in that it will only loop over the recursion a certain number of times (50 last time I checked). This limit has been updated a few times and it does become a bottleneck. More recently, a change was introduced to support the ability to build a type that is **tail recursive** down to any depth. This is good.

Here is a basic example of recursion used to define a Json type.

## Conditional Types

While building a type you may need to use an `if` statement, but they don’t exist for building types, the equivalent is to use a conditional type expression.

## Distributive Conditional Types

An often missed, slightly obscure feature that can come in very handy. Consider the following, a type to replace the Array type.

```
type MyArray<T> = T[];

//typeof StringArray = (string | number)[]
constStringArray:MyArray<string|number> = ['a', 2];
```

However, if we use a conditional type to define the same type for MyArray, a subtle difference occurs when applying a union type `string | number`. It will distribute the type by looping over each part of the union.

```
type MyArray<T> = Textends any? T[] :never;

//typeof StringArray = string[] | number[]constStringArray:MyArray<string|number> = ['a', 2]; // won't compile
```

> This is how UnionToIntersection works in the image used at the start of this article.
> 

## Type inference with infer

When using conditional type checking with `extends` you can replace the check with the `infer` keyword and set up a new name to store the type in. For example, if you wanted to get the third element of a tuple:

Type inference is very powerful, you can pull out children elements of objects, get the tail of an array using the spread operator, pull out parameters from a function, get a return type of a function, pull out a generic type from another generic type.

Mix this with recursion and the building blocks listed above, you can build almost any type imaginable.

# Useful Type Tricks

## Indexed Access

Access elements from an array by index or an object by key using square brackets.

```
T is an object -> T['keyName']
T is an array -> T[100]
```

You can also get a union of all of the types in a tuple, instead of a specific number you can provide `number`.

> T[number]
> 

> T[keyof T]
> 

This trick pulls out all the types of the values of an object, or an array.

## Check for an empty object

Sometimes you want to conditionally choose a type based on the weather the type is an empty object or not

```
// This does not work
type IsEmpty<T> = T extends {} ? true : false// This, however does
type IsEmpty<T> = T extendsRecord<string, never> ? true : false
```

## Remove Part of Union

> Exclude<’a’ | ‘b’, ‘a’>
> 

Given a union of types, you can use the Exclude utility to remove part of it.

## Any Key

> keyof any
> 

This is equivalent to `string | number | symbol`