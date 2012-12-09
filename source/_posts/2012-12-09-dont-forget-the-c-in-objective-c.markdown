---
layout: post
title: "Don't Forget the \"C\" in Objective-C"
date: 2012-12-09 15:15
comments: true
categories: 
published: false
---

It's been awhile. I blame my bike and the Thanksgiving Steam sale. You probably did, too.

This post is going to primarily serve for my personal purposes to bookmark some articles and blog posts I've been referencing to remind myself some of the basics of C while working in Objective-C. This is going to be one of those posts that I'm probably going to add to as time goes on. This is also kind of a prelude to a future similarly-themed bookmarking post for myself on ARC.

Note also that while using very specific type and storage qualifiers can be unnecessary for most Objective-C development, Apple pretty much threw everyone into the fray when they introduced such common identifiers such as `__block` with Grand Central Dispatch. It got even better when ARC was introduced. You'll see.

#Variable Declaration
##The Problem
This is something that was either not immediately obvious to me when writing software, or I simply was never formally shown this. I'm not entirely sure how I never picked up on this in all my years of writing software (even informally), but here it is:

This: `const NSString * myString` is totally not the same as this: `NSString * const myString`.

Mind blown, right? If you asked either "Why are they not the same?" or "I don't see a difference", we'll get to that in a sec. It all has to do with how the compiler interprets your variable declaration. The whole process is actually a lot more sophisticated and complicated than I'm about to explain it, but it's essentially looking for variable declaration modifiers in the following format:

##Type Qualifiers vs Storage Classes
`[storage class] [type specifier] [type qualifier] [type] [*] [type qualifier] [*] [type qualifier] [...] <variable name>`
  
**Type qualifiers** in C99 include `const`, `volatile` and `restrict`.  
**Type specifiers** include `unsigned`, `signed`, `long`, `double`, etc. You get the idea.  
**Storage classes** include `static`, `extern`, `auto` and `register`.  
  
*Note: Certain pairs of storage classes are mutually exclusive with one another and cannot be used together. The same is true for type qualifiers. However, this is beyond the scope of this discussion - it's just something to keep in mind.*

Based on this new-found information, we can dissect our example above and explain why they are not the same, as well as introduce some deliberately complicated examples to show how to figure out how to interpret them.

##Interpreting/Constructing Variable Declarations
Let's look at the previous example to dissect how they are different. If I were reading an English sentence describing each variable declaration, it would go like this for each:

- `const NSString * myString` - The variable "`myString`" is a pointer to a constant `NSString`
- `NSString * const myString` - The variable "`myString`" is a constant pointer to an `NSString`

After reading the above, you can't tell me you still think both of those variable declarations are the same. In fact, based on the above information, we could even construct a third similar-but-different variable declaration:

- `const NSString * const myString` - The variable "`myString`" is a constant pointer to a constant `NSString`

So how did I perform this black magic? Simply read the variable declaration from the right to left. [Code Project has an excellent writeup](http://www.codeproject.com/Articles/7042/How-to-interpret-complex-C-C-declarations) on reading complicated C declarations. There are [tons of guides out there](http://lmgtfy.com/?q=complicated+C+variable+declarations). There are other complicated cases that involve array declarations and pointers, as well as comma-delimited declarations that I have yet to see in an Objective-C project (thank you) that can complicate a declaration.

Let's try this example from gbdirect's "[The C Book](http://publications.gbdirect.co.uk/c_book/chapter8/const_and_volatile.html)":

`extern const volatile unsigned long int rt_clk`

##A Small Dash of Cocoa
You may be thinking: "This is great and all, so why is this so important in Objective-C? I've never seen an `auto register const long float` before in my life, and I've been writing in iOS since it began!". While all of that may be true (I don't know many real-time systems that require volatile pointers running in Objective-C), things got a little more complicated when Apple introduced ARC and blocks with Grand Central Dispatch.

###Blocks & GCD
When Apple gave us blocks in iOS 4, it was a secret curse in disguise in terms of memory-management. *Everyone* loves blocks. *Everyone* tries to use blocks *everywhere*, but we'll save that for another topic (hint: don't forget about delegation and notifications).

Because of this, the dreaded retain cycle would rear it's ugly head and developers would end up scratching their heads. "Why is my view controller not being released when nothing else has a reference to it!" So you go and watch the WWDC videos on the introduction to blocks and GCD, and you discover the solution: `__block`. Simply declaring your variables as `__block` will magically make "weak" references to your objects to use inside your blocks. We've all done this:  

`__block typeof(self) myself = self`  

This is a fundamentally flawed view of `__block` and this isn't it's intent. `__block` is designed for variables that are going to be "created" inside the block so that memory can be allocated appropriately like so:

{% codeblock lang:objc %}
__block int count = 0;

self.completionBlock = ^(BOOL finished){
  if(finished){
    count++;
  }
}
{% endcodeblock %}

In iOS 5, Apple gave us [auto-nil'ing weak references](http://developer.apple.com/library/ios/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4) under ARC. This means that when you have a *weak* reference to a variable, it will allow you to use that variable and will set that variable to `nil` when no other object has a strong reference to it. This is a perfect place to replace the `__block` storage class with one of ARC's new [ownership qualifiers](http://clang.llvm.org/docs/AutomaticReferenceCounting.html#ownership). Ownership qualifiers in ARC include `__autoreleasing`, `__strong`, `__unsafe_unretained` and `__weak`. Let's use `__weak`:

`typeof(self) __weak myself = self`

Viola! We now have an auto-nil'ing weak reference to ourself that we can use in a block that `self` has a strong reference to without modifying where the object lives in memory:

{% codeblock lang:objc %}
typeof(self) __weak myself = self;

self.completionBlock = ^(BOOL finished){
  [myself play];
}
{% endcodeblock %}

http://www.lix.polytechnique.fr/~liberti/public/computing/prog/c/C/CONCEPT/storage_class.html

http://developer.apple.com/library/ios/documentation/cocoa/Conceptual/Blocks/Articles/bxVariables.html#//apple_ref/doc/uid/TP40007502-CH6-SW3
http://www.eskimo.com/~scs/cclass/int/sx4ga.html
http://www.9wy.net/onlinebook/CPrimerPlus5/ch12lev1sec7.html
http://www.idryman.org/blog/2012/10/29/type-qualifiers/

