# Goodbye Node JS

Created: July 15, 2022 12:40 PM
Finished: No
Source: https://medium.com/@appiahyoofi/goodbye-node-js-9e2f71f5e430

## Who is this new kid ? 🤔

**[Bun**](https://bun.sh/) (and before you ask yes all the cool names have already been taken 😅 ) is a new open source runtime environment created by Jared Sumner and over 40 contributors. This nerdy looking runtime environment really packs a punch.

According to its creator it was made to:

*Start fast*

*Have new levels of performance*

*To be a great and complete tool.*

> On its beta release it’s creator made a claim to be an incredibly fast all-in-one JavaScript runtime.
> 

![Goodbye%20Node%20JS%2039d756ea85044a50bfcf8aafb6bb07e2/1_-eXW38rRFCB4M49TC4RiQ.jpeg](Goodbye%20Node%20JS%2039d756ea85044a50bfcf8aafb6bb07e2/1_-eXW38rRFCB4M49TC4RiQ.jpeg)

## How fast you ask? 🚀

Here are the benchmark performances of Bun in comparison to Node JS and Deno.

![Goodbye%20Node%20JS%2039d756ea85044a50bfcf8aafb6bb07e2/1C5slzNbrm1ol9h6vM_BlNw.png](Goodbye%20Node%20JS%2039d756ea85044a50bfcf8aafb6bb07e2/1C5slzNbrm1ol9h6vM_BlNw.png)

![Goodbye%20Node%20JS%2039d756ea85044a50bfcf8aafb6bb07e2/1OAKBmvGJkfLiZlFSB2qUdQ.png](Goodbye%20Node%20JS%2039d756ea85044a50bfcf8aafb6bb07e2/1OAKBmvGJkfLiZlFSB2qUdQ.png)

![Goodbye%20Node%20JS%2039d756ea85044a50bfcf8aafb6bb07e2/15-Iux4z7Y99ROk7SAspevg.png](Goodbye%20Node%20JS%2039d756ea85044a50bfcf8aafb6bb07e2/15-Iux4z7Y99ROk7SAspevg.png)

Take a moment to soak in those number. And yes its legit well at least according to [Bun](https://bun.sh/) they are . It is not looking good for deno but I am sure [Ryan Dahl](https://en.wikipedia.org/wiki/Ryan_Dahl) and the team at deno have something up their sleeve.

## How does it work? ⚙️

Well Node Js uses [V8 engine](https://v8.dev/) and has made it a great tool because of [JIT](https://www.ibm.com/docs/en/sdk-java-technology/8?topic=reference-jit-compiler) (Just In Time) compilation. Now Bun uses the [JavaScript Core](https://developer.apple.com/documentation/javascriptcore) which is considered to be faster. It was also written in a low level language [Zig](https://ziglang.org/) which is like C and Rust had a baby. Low level control of memory and lack of hidden control flow are the features of [Zig](https://ziglang.org/) that make Bun as fast as it is

## Features 📋

> Native bundler that replaces Web Pack
> 
> 
> Transpiler that enables TypesScript to be written out of the box
> 
> Task runner
> 
> npm client
> 
> Automatically loaded environment variable (bye bye require(“dotenv”).load()).
> 
> Native Test runner
> 
> 90% of the Node API functions
> 

I don’t think it can get better than this.

It is worth noting that since its a new tool it will be buggy. It will be best to use a WSL ( Windows Subsystem for Linux)

The introduction of Bun will definitely be a dream come true for many developers. However will this tool stand the test of time or become the next Windows 8. Hopefully not.

If you would like more information on Bun [click here](https://bun.sh/)

Thanks for reading