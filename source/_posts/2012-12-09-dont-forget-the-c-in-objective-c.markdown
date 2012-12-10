---
layout: post
title: "Don't Forget the \"C\" in Objective-C: C Declarations"
date: 2012-12-10 08:00
comments: true
categories: [Advanced iOS,iOS,Development,ARC,Best-Practices]
keywords: "c,c++,iOS,objective-c,c-declarations,declarations,iOS,development,best-practices,ARC"
published: true
---

It's been awhile. I blame my bike and the Thanksgiving Steam sale. If you're seriously reading this, then it is statistically likely your productivity was at some point affected by Steam's Thanksgiving sale.

This post is going to primarily serve for my personal purposes to bookmark some articles and blog posts I've been referencing to remind myself some of the basics of C declarations while working in, as well as how they relate to Objective-C. This is going to be one of those posts that I'm probably going to add to as time goes on. This is also kind of a prelude to a future similarly-themed bookmarking post for myself on ARC.

Note also that while using very specific type and storage qualifiers can be unnecessary for most Objective-C development, Apple pretty much threw everyone into the fray when they introduced such common identifiers such as `__block` with Grand Central Dispatch. It got even better when ARC was introduced. You'll see...

#Variable Declaration

##The Problem
This is something that was either not immediately obvious to me when writing software, or I simply was never formally shown this. I'm not entirely sure how I never picked up on this in all my years of writing software (even informally), but here it is:

This: `const NSString * myString` is totally not the same as this: `NSString * const myString`.

<!--more-->

Mind blown, right? If you asked either "Why are they not the same?" or "I don't see a difference", we'll get to that in a sec. It all has to do with how the compiler interprets your variable declaration. The whole process is actually a lot more sophisticated and complicated than I'm about to explain it, but it's essentially looking for variable declaration modifiers in the following format:

`[storage class] [type specifier] [type qualifier] [type] [*] [type qualifier] [*] [type qualifier] [...] <variable name>`
  
"Umm..." Yeah, I'll get to it. Read on:

##Declaration Modifiers

There are two primary types of declaration modifiers: type qualifiers and storage classes, as well as a third. The third is a kind of "decorator" that in my words "details how _much_ memory is being allocated for a given variable declaration" - it's called a type _specifier_.

###Type Qualifiers
Type qualifiers is basically you telling the compiler how you are going to use a given variable so that it can either perform optimizations on that variable to increase performance, prevent modification of them by anyone else (constants), or to ensure that you always have the most up-to-date information every time you access that variable (volatile). These are less common to see[^2]. You can add type qualifiers to both types and pointers. More on [type qualifiers here](http://www.idryman.org/blog/2012/10/29/type-qualifiers/) and [here](http://www.eskimo.com/~scs/cclass/int/sx4ga.html).
  
**Type qualifiers** in C99 include:

- `const`
- `volatile`
- `restrict`

###Type Specifiers
Type specifiers are different in that they are basically just telling the compiler how many buckets of data to chunk out for a given variable to use. You can also use type specifiers as types themselves, such as `long int`, or `unsigned long double`.

**Type _specifiers_** include:

- `void`
- `char`
- `int`
- `unsigned`
- `signed`
- `long`
- `double`
- etc. You get the idea.

I know the least about type specifiers.

###Storage Classes
Storage classes are special in that they tell the system _how_ to allocate a given chunk of memory, in what scope that chunk of memory can be accessed, and the lifetime of those variables. More on [C storage classes here](http://www.lix.polytechnique.fr/~liberti/public/computing/prog/c/C/CONCEPT/storage_class.html).

**Storage classes** include

- `static`
- `extern`
- `auto`
- `register`
  
*Note: Certain pairs of storage classes are mutually exclusive with one another and cannot be used together. The same is true for type qualifiers. However, this is beyond the scope of this discussion - it's just something to keep in mind.*

Based on this new-found information, we can dissect our example above and explain why they are not the same, as well as introduce some deliberately complicated examples to show how to figure out how to interpret them.

##Interpreting/Constructing Variable Declarations
Let's look at the previous example to dissect how they are different. If I were reading an English sentence describing each variable declaration, it would go like this for each:

{% codeblock lang:objc %}
const NSString * myString; //The variable myString is a pointer to a constant NSString
NSString * const myString; //The variable myString is a constant pointer to an NSString
{% endcodeblock %}

After reading the above, you can't tell me you still think both of those variable declarations are the same. In fact, based on the above information, we could even construct a third similar-but-different variable declaration:

{% codeblock lang:objc %}
const NSString * const myString //The variable myString is a constant pointer to a constant NSString
{% endcodeblock %}

So how did I perform this black magic? Simply read the variable declaration from the right to left, and when you get to the type, start reading left to right until you get back to the type. [Code Project has an excellent writeup](http://www.codeproject.com/Articles/7042/How-to-interpret-complex-C-C-declarations) on reading complicated C declarations and [ridiculous_fish](http://ridiculousfish.com) has an excellent conversion tool that will even [convert the declarations into English statements](http://cdecl.org) right before your eyes. There are [tons of guides out there](http://lmgtfy.com/?q=complicated+C+variable+declarations). There are other complicated cases that involve array declarations and pointers, as well as comma-delimited declarations that I have yet to see in an Objective-C project (thank you) that can complicate a declaration.

##A Small Dash of Cocoa
You may be thinking: "This is great and all, so why is this so important in Objective-C? I've never seen an `volatile unsigned long int * const` before in my life, and I've been writing in iOS since it began!". While all of that may be true[^3], things got a little more complicated when Apple introduced ARC and blocks with Grand Central Dispatch.

###Block Retain Cycles
When Apple gave us blocks in iOS 4, it was a curse in disguise in terms of memory-management. *Everyone* loves blocks. *Everyone* tries to use blocks *everywhere*, but we'll save that for another topic (hint: don't forget about delegation and notifications).

Because of this, the dreaded retain cycle would rear it's ugly head and engineers would end up scratching their heads if they even noticed it was happening. "Why is my view controller not being released when nothing else has a reference to it!" So you go and watch the WWDC videos on the introduction to blocks and GCD, and you discover the solution: [`__block`](http://developer.apple.com/library/ios/#documentation/cocoa/Conceptual/Blocks/Articles/bxVariables.html#//apple_ref/doc/uid/TP40007502-CH6-SW6). Simply declaring your variables as `__block` will magically make "weak" references to your objects to use inside your blocks.

So we'd end up with this: 

{% codeblock lang:objc %}
__block typeof(self) myself = self;
{% endcodeblock %}

I would consider this incorrect even though more often than not, this will yield the result you want. This is an _inaccurate_ use of `__block`[^1]. `__block` is a _storage class_ (like we discussed above) and is designed for variables that are going to be "created" inside the block so that memory can be allocated appropriately for use inside the block like so:

{% codeblock lang:objc %}
__block int count = 0;

self.completionBlock = ^(BOOL finished){
  if(finished){
    count++;
  }
}
{% endcodeblock %}

In this example, we're creating a block variable outside of our block that is going to be modified inside said block. This is the correct way to use `__block`. To break our retain cycle, the more appropriate solution is to use some specific tools given to us in ARC that we'll get to in just a second. First, an aside on new hotness in ARC to look out for:

###Ownership Qualifiers
When using ARC, we now have even _more_ qualifiers to look out for and use in our code. These ARC-specific qualifiers are called ownership qualifiers. Fitting, given that the primary function of ARC is to manage object lifetimes and ownership.

Ownership qualifiers in ARC include:

- `__autoreleasing`
- `__strong`
- `__unsafe_unretained`
- `__weak`

It's important that your ownership qualifiers [decorate your declarations properly](http://developer.apple.com/library/mac/#releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html%23//apple_ref/doc/uid/TP40011226-CH1-SW7). ARC ownership qualifiers should decorate object pointers, not the object types (`MyVC * __weak weakVC = newVC`, NOT `MyVC __weak * weakVC = newVC`).

@idryman has a really [concise writeup](http://www.idryman.org/blog/2012/10/29/type-qualifiers/#arc-ownership-qualifiers-1) on ARC ownership qualifiers, as well as explaining the improper use of them.

[From the documentation](http://developer.apple.com/library/mac/#releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html%23//apple_ref/doc/uid/TP40011226-CH1-SW7):
{% blockquote %}
You should decorate variables correctly. When using qualifiers in an object variable declaration, the correct format is:

ClassName * qualifier variableName;

for example:

MyClass * __weak myWeakReference;
MyClass * __unsafe_unretained myUnsafeReference;
{% endblockquote %}

####Auto-Nil'ing Weak References
In iOS 5, Apple gave us [auto-nil'ing weak references](http://developer.apple.com/library/ios/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4) under ARC. This means that when you have a *weak* reference to a variable, it will allow you to use that variable and will set that variable to `nil` when no other object has a strong reference to it. This is a perfect place to replace the `__block` storage class with one of ARC's new [ownership qualifiers](http://clang.llvm.org/docs/AutomaticReferenceCounting.html#ownership).  Let's use `__weak`:

`typeof(self) __weak myself = self`

Viola! We now have an auto-nil'ing (auto-zeroing) weak reference to `self` that we can use in a block that `self` has a strong reference to without modifying where the object lives in memory:

{% codeblock lang:objc %}
typeof(self) __weak myself = self;

self.completionBlock = ^(BOOL finished){
  [myself playWithObject:myself];
}
{% endcodeblock %}

##Ok, Cool
I think it's important to understand the guts of a C declaration because of the small nuances that are present when you introduce things like ARC ownership qualifiers.  If you don't know what a storage qualifier is, then you're surely not going to know what an ownership qualifier is or how to properly use it. While it's true for the most part that the compiler will generally let you know when something is awry, isn't it nice to know that you know a little bit more about what the compiler is actually thinking when it tells you that retaining an object strongly in this context could result in a retain cycle?

[^1]: This is my opinion
[^2]: I've actually never seen `volatile` or `register` used anywhere in Objective-C
[^3]: I don't know many real-time systems that require constant volatile pointers compiled in Objective-C

---

Misc Non-Inline References:

1.  [C Storage Classes](http://www.lix.polytechnique.fr/~liberti/public/computing/prog/c/C/CONCEPT/storage_class.html)
2.  [Blocks and Variables - Apple Docs](http://developer.apple.com/library/ios/documentation/cocoa/Conceptual/Blocks/Articles/bxVariables.html#//apple_ref/doc/uid/TP40007502-CH6-SW3)
4. [ANSI C Type Qualifiers - C Primer](http://www.9wy.net/onlinebook/CPrimerPlus5/ch12lev1sec7.html)
6.  [Friday Q&A 2009-06-26: Type Qualifiers in C, Part 1 - Mike Ash](http://www.mikeash.com/pyblog/friday-qa-2009-06-26-type-qualifiers-in-c-part-1.html)

---
