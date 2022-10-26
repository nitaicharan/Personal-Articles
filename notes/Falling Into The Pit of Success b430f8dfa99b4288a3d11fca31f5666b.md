# Falling Into The Pit of Success

Created: May 26, 2022 8:17 AM
Finished: No
Source: https://blog.codinghorror.com/falling-into-the-pit-of-success/

Eric Lippert notes [the perils of programming in C++](http://blogs.msdn.com/ericlippert/archive/2007/08/14/c-and-the-pit-of-despair.aspx):

> 
> 
> 
> I often think of C++ as my own personal Pit of Despair Programming Language. **Unmanaged C++ makes it so easy to fall into traps. Think buffer overruns, memory leaks, double frees, mismatch between allocator and deallocator, using freed memory, umpteen dozen ways to trash the stack or heap – and those are just some of the memory issues**. There are lots more "gotchas" in C++. C++ often throws you into the Pit of Despair and you have to climb your way up the Hill of Quality. (Not to be confused with scaling the Cliffs of Insanity. That's different.)
> 

That's [the problem with C++](http://www.codinghorror.com/blog/archives/000768.html). It does a terrible job of protecting you from your own worst enemy – yourself. When you write code in C++, you're always circling the pit of despair, just one misstep away from plunging to your doom.

![Falling%20Into%20The%20Pit%20of%20Success%20b430f8dfa99b4288a3d11fca31f5666b/6a0120a85dcdae970b0120a86d91e6970b-pi.jpg](Falling%20Into%20The%20Pit%20of%20Success%20b430f8dfa99b4288a3d11fca31f5666b/6a0120a85dcdae970b0120a86d91e6970b-pi.jpg)

Wouldn't it be nice to use a language designed to keep you from falling into the pit of despair? But avoiding horrific, trainwreck failure modes isn't a particularly laudable goal. Wouldn't it be even *better* if you used a language that let you effortlessly [fall into The Pit of Success](http://blogs.msdn.com/brada/archive/2003/10/02/50420.aspx)?

> 
> 
> 
> The Pit of Success: in stark contrast to a summit, a peak, or a journey across a desert to find victory through many trials and surprises, we want our customers to simply fall into winning practices by using our platform and frameworks. To the extent that we make it easy to get into trouble we fail.
> 

Rico Mariani coined this term when talking about language design. You may give up some performance when you choose to code in C#, Python, or Ruby instead of C++. But what you get in return is a much higher likelihood of avoiding the miserable Pit of Despair – and the opportunity to fall into the far more desirable Pit of Success instead.

As Brad Abrams points out, this concept extends beyond language. A well designed API should *also* [allow developers to fall into the pit of success](http://blogs.msdn.com/brada/archive/2003/10/02/50420.aspx):

> 
> 
> 
> [Rico] admonished us to think about how we can build platforms that lead developers to write great, high performance code such that developers just fall into doing the "right thing". That concept really resonated with me. It is the key point of good API design. We should build APIs that steer and point developers in the right direction.
> 

I think this concept extends even farther, to applications of all kinds: big, small, web, GUIs, console applications, you name it. I've often said that **a well-designed system makes it easy to do the right things and annoying (but not impossible) to do the wrong things**. If we design our applications properly, our users should be inexorably drawn into the pit of success. Some may take longer than others, but they should all get there eventually.

If users *aren't* finding success on their own – or if they're not finding it within a reasonable amount of time – it's not their fault. It's *our* fault. **We didn't make it easy enough for them to fall into the pit of success.** Consider your project a [Big Dig](http://en.wikipedia.org/wiki/Big_Dig_%28Boston%2C_Massachusetts%29): your job is to constantly rearchitect your language, your API, or your application to make that pit of success ever deeper and wider.

Indoor enthusiast. Co-founder of Stack Overflow and Discourse. Disclaimer: I have no idea what I'm talking about. Find me here: [http://twitter.com/codinghorror](http://twitter.com/codinghorror)