# 5 things you need to know about the delete operator in JavaScript

Created: July 5, 2022 10:04 AM
Finished: No
Source: https://levelup.gitconnected.com/5-facts-about-delete-operator-in-javascript-c16fd2588cd

[5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/0Kp2lpsu-UwCHe-j7](5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/0Kp2lpsu-UwCHe-j7)

Photo by [Markus Winkler](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

In JavaScript, the **delete** operator is employed to delete a property of an object. After deleting the actual property, that property won’t be accessible and returns `undefined`.

The invocation of the `delete` operator returns `true` when it removes a property and `false` otherwise. it’s only effective on an object’s properties, it has no effect on variable or function names.

The **delete** operator shouldn’t be used on predefined JavaScript object properties like `window`, `Math`, and `Date` objects. It can crash your application.

Let’s **scrutinize** some facts about the `delete` operator.

## Delete object properties

The only way to fully remove the properties of an object in JavaScript is by using `delete` operator.

![5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/16bCVXtPldEPm4vt-Ugz_9A.png](5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/16bCVXtPldEPm4vt-Ugz_9A.png)

If the property which you’re trying to delete doesn’t exist, delete won’t have any effect and can return true.

![5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/1qDpAOOLVw-c1WQ5dbriRGw.png](5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/1qDpAOOLVw-c1WQ5dbriRGw.png)

## Can we delete variables in Javascript?

The **delete** operator removes a property from an object. It cannot delete a variable. Any property declared with `var` cannot be deleted from the global scope or from a function's scope.

![5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/1frrhhlmSCnBSfxr9N-QKLg.png](5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/1frrhhlmSCnBSfxr9N-QKLg.png)

If you declare a variable without **var**, it can be deleted. Let’s look into the example below.

![5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/1GgpyE37_H8rnaXSjV7smwQ.png](5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/1GgpyE37_H8rnaXSjV7smwQ.png)

*The variable declared without the `var` keyword internally stores it as a property of the `window` object. So we can delete the properties of the `window` object.*

## **Can we delete values from an array?**

Since JavaScript arrays are objects, elements can be deleted by using `delete`.

![5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/1sOg5B7FC48TD4XkQxyrySQ.png](5%20things%20you%20need%20to%20know%20about%20the%20delete%20operato%20bff680d82d9a4210aefbc44f386da42f/1sOg5B7FC48TD4XkQxyrySQ.png)

`delete` will delete the object property, but will not reindex the array or update its length. This makes it appear as if it is `undefined`.

Using `delete` may leave undefined holes in the array. Use `pop()`, `shift()`, or `splice()` instead.

## Can we delete built-in objects?

Deleting built-in objects like `Math`, `Date`, and `window` objects are unsafe, and they can crash your entire application.

## Deleting non-configurable properties

Object properties, besides a `value`, has three special attributes:

- **`writable`** – if `true`, the value can be changed, otherwise, it’s read-only.
- **`enumerable`** – if `true`, it is listed in loops, otherwise not listed.
- **`configurable`** – **if `true`, the property can be deleted** or the attributes can be modified, otherwise, it cannot be changed.

The values assigned by using `Object.defineProperty` and set to `configurable: false` in an object cannot be deleted.

In strict mode, it will throw an error if you try to delete a non-configurable property.

## Conclusion

`delete` is the only true way to remove an object’s properties without any leftovers, but it works **~100 times slower** if you are using `delete` in loops.

The alternative solution is setting the value to u`n`defined like `object[key] = undefined`. It doesn’t fully delete the property, it just sets the value to undefined. the choice isn’t exactly a prominent solution, but if you utilize it with care then you’ll be able to improve the performance.

That’s all! I hope this text might help to perforate the delete operator

Thanks for reading :)