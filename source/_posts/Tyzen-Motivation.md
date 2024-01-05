---
title: Tyzen - Motivation
date: 2024-01-05 18:09:39
tags:
- formal-methods
- formal-verification
- software-testing
- language
categories:
- research
- tizen
mathjax: true
---

I've started working on a new specification langauge for automatic test case generation and software verification. The correct specification of this langauge itself does not exist at the moment. This post and follow-up series of posts will document the train of thought followed for building a new langauge, and that to for writing specifications of programs that needs to be tested. At the time of writing, I've very new to this research area, and the research area is relatively new itself. My aim is to learn about the current existing methods, and apply those to create a new langauge for testing of sofware. Hence, introducing you to...

## Tyzen - A New Specification Language For Software Verification

### On Types
From my current perspective of how I see things, I believe that any computer program, written using any existing high level langauge, uses ___types___ and logic built on these ___types___ to implement different algorithms. Some fundamental types already exist, like in C, there exist `int`, `char`, etc... and some are user defined types. All logic of your program is working on these ___types___.

Form now on, whether the term "type" is emphasized or not, you must always keep the above point at the back of your head. Whenever you see the term "type", you must assume I'm referring to it in this context.

### On Values
If we think about it more carefully, the logic is actually working on ___values___ of these ___types___, and there are only a finite[$^{[1]}$](#On-Discrete-Nature-of-Values) set of possible ___values___ for any given ___type___. An example of this is `char` type having values from $0$ to $127$, where each value represent a unique ASCII character. Another example will be the type `unsigned int` having values from $0$ to $2^{32}-1$. Also, a value may belong to more than one type at any time, an example of this is all values in range $-128$ to $127$ being, `char`, `short`, `int`, `long int` and `long long int` at the same time.

Using a set of fundamental types, we can define new types. We usually refer to these as user-defined types. The behaviour of these new types depend on their use case and the functions provided for interacting[$^{[2]}$](#On-Operators) with these types. For example, a type named `std::optional<...>` in C++ is used to store values of some pre-defined type, with a `bool` value of ___Boolean___ type that will specify whether or not any ___value___ of `std::optional` type contains a ___valid___ value or not.

#### On Discrete Nature of Values
Why finite? Computers work in a very discrete world. Nothing is dense and [uncountable](https://en.wikipedia.org/wiki/Uncountable_set) like real numbers and similar objects in mathematics. Therefore, even if we define any new type, they're bound to have finite set of values, because each type it is composed of also has finite set of possible values.

#### On Validity of Values
Sometimes, value of a certain type is ___valid___ and sometimes it's ___invalid___. The validity matters, because performing any computing on an invalid type generates an invalid result. Invalid result means an invalid state, and an invalid state must result in a termination (generally).

### On Operators

In computing, operators are abstractions of mathematical operations on mathematical objects. Basically everything is abstracted over some mathematical object in the world of computer science. The operator can be of logical, algebraical, or some other use case (that I don't know about yet). Now, there exists some language that allow you to perform some [syntactical sugaring](https://en.wikipedia.org/wiki/Syntactic_sugar) to overload some operators for your user-defined types (C++), and then there are languages that don't really care about it (C). All in all, whether you do overloading or not, you end up calling a function for these user-defined types to perform the equivalent operation on it.

One example, where you define your own operators is when you're implementing a `Complex` type to represent set of complex numbers in your program.  

```c
/**
 * A new Complex user-defined type.
 * Note that it is made of two fundamental
 * types.
 *
 * But this is now a new type in itself, because
 * the old +, -, /, *, ^, &, |, etc... don't work
 * on this new type.
 * */
typedef struct Complex {
    double x;
    double y;
} Complex;

/**
 * Operator defined for performing addition
 * over complex type
 *
 * Note how it returns pointer to Complex type.
 * */
Complex* complex_add(Complex* c1, Complex* c2) {
    /* compute sum of two complex numbers and */
    return result;
}

Complex* complex_mul(Complex* c1, Complex* c2) {
    /* define it ... */
}

Complex* complex_mul_scalar(Complex* c, double s) {
    /* define it ... */
}
```

You go through similar process when implementing data structures like stack, vector, linked-list, etc...

### The Testing Phase

After you've defined your own types and a set of operator functions to work with these types, you'll probably feel the urge to write tests or do some other type of testing (like fuzzing) to verify that your program works fine (or atleast looks fine). If you don't think so, then you're either a always on the end-user side of all the tools you use, or you just are perfect that you write correct programs always (maybe because you're code is mostly simple and easy to debug/verify).

I however take testing quite seriously, and have been spending time on learning about different ways to do it. They save a lot of time findings
