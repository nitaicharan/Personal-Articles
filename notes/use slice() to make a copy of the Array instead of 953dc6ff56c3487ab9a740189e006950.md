# use .slice() to make a copy of the Array instead of using the spread operator?

Created: August 24, 2022 11:07 AM
Finished: No
Source: https://omarsaade.hashnode.dev/use-slice-to-make-a-copy-of-the-array-instead-of-using-the-spread-operator

[use%20slice()%20to%20make%20a%20copy%20of%20the%20Array%20instead%20of%20953dc6ff56c3487ab9a740189e006950/image](use%20slice()%20to%20make%20a%20copy%20of%20the%20Array%20instead%20of%20953dc6ff56c3487ab9a740189e006950/image)

No matter whether we create a copy with slice(), with the spread operator, or with Object.assign: we will always create a shallow copy, no deep copy.

[https://www.geeksforgeeks.org/what-is-shallow-copy-and-deep-copy-in-javascript/](https://omarsaade.hashnode.dev/Link)

Shallow Copy: When a reference variable is copied into a new reference variable using the assignment operator, a shallow copy of the referenced object is created. In simple words, a reference variable mainly stores the address of the object it refers to. When a new reference variable is assigned the value of the old reference variable, the address stored in the old reference variable is copied into the new one. This means both the old and new reference variable point to the same object in memory. As a result if the state of the object changes through any of the reference variables it is reflected for both. Let us take an example to understand it better.

Deep Copy: Unlike the shallow copy, deep copy makes a copy of all the members of the old object, allocates separate memory location for the new object and then assigns the copied members to the new object. In this way, both the objects are independent of each other and in case of any modification to either one the other is not affected. Also, if one of the objects is deleted the other still remains in the memory. Now to create a deep copy of an object in JavaScript we use JSON.parse() and JSON.stringify() methods. Let us take an example to understand it better.

![use%20slice()%20to%20make%20a%20copy%20of%20the%20Array%20instead%20of%20953dc6ff56c3487ab9a740189e006950/Dg3BFEDHm.png](use%20slice()%20to%20make%20a%20copy%20of%20the%20Array%20instead%20of%20953dc6ff56c3487ab9a740189e006950/Dg3BFEDHm.png)