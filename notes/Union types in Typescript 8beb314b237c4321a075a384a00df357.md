# Union types in Typescript

Created: July 12, 2022 7:52 AM
Finished: No
Source: https://blog.angulartraining.com/union-types-in-typescript-2517a82a1ea0
Tags: #angular

![Union%20types%20in%20Typescript%208beb314b237c4321a075a384a00df357/1Qxjy9lnOnQpUqYtLXhEkqg.png](Union%20types%20in%20Typescript%208beb314b237c4321a075a384a00df357/1Qxjy9lnOnQpUqYtLXhEkqg.png)

Union types are a very cool feature of Typescript that deserves to be more widely used. In this post I’m going to highlight a few interesting things we can do with union types.

# 1. Replace enums

Enums are great to create types that wrap a list of constants, like the following list of colors:

```tsx
export enumColor {RED, BLUE, WHITE}
```

But the thing with Angular is that we are very likely to use enums to actually render something out of them in our HTML code. By default, enums have index values (0, 1 ,2, etc.) that we can change to strings:

```tsx
export enumColor {RED= ‘red’, BLUE = ‘blue’, WHITE = ‘white’}
```

The above code makes the enum more interesting as I can use **Color.RED** to display something in red color in a component template for instance. But that syntax is fairly verbose.

Instead, we could use union types and get similar benefits in a much shorter fashion:

```tsx
export typeColor ='red'|'white'|'blue';
```

And this works! I can now use the **Color** type and assign a string value to it, which is a win-win as my code is strongly typed and I get immediate access to all of the functions of the string type:

```tsx
constmyColor: Color ='red';
myColor.toUpperCase();
```

![Union%20types%20in%20Typescript%208beb314b237c4321a075a384a00df357/1NVL-uzDa_dx2MOCbC8647A.png](Union%20types%20in%20Typescript%208beb314b237c4321a075a384a00df357/1NVL-uzDa_dx2MOCbC8647A.png)

# 2. Mark a property as optional

Union types also work with **undefined**, which means we can do something like this:

```tsx
let user:User | undefined;
```

This makes it explicit that we expect the user to be undefined at times. We can push that approach one step further by creating a generic type out of the previous example:

```tsx
export type Optional<T> = T | undefined;
```

# 3. Union types as an alternative to inheritance

This approach is recommended if you use NgRx with Angular. In the above code, we define that **AuthAction** can be any of the following types:

```tsx
export typeAuthAction = LoginAction
                          | LoginSuccessfulAction
                          | LoginErrorAction
                          | LogoutAction
                          | LogoutSuccesfulAction;
```

That’s something we could have achieved by making **AuthAction** an empty abstract class, and then have all of the Action classes extend it. But I find the above syntax much lighter and much more explicit, as we know right away all of the actions that are **AuthActions**.

**Do you know of any other clever usage of union types? Feel free to let me know in the comments section. Thanks!**

> My name is Alain Chautard. I am a Google Developer Expert in Angular, as well as founding consultant and trainer at Angular Training where I help web development teams learn and become fluent with Angular.
> 
> 
> If you enjoyed this article, please clap for it or share it. Your help is always appreciated.
>