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
author: Siddharth Mishra
---

I've started working on a new specification language for automatic test case generation and software verification. The correct specification of this langauge itself does not exist at the moment. This post and follow-up series of posts will document the train of thought followed for building a new langauge, and that to for writing specifications of programs that needs to be tested. At the time of writing, I've very new to this research area, and the research area is relatively new itself. My aim is to learn about the current existing methods, and apply those to create a new langauge for testing of sofware. Hence, introducing you to...

## Tyzen - A New Specification Language For Software Verification

### On Types
From my current perspective of how I see things, I believe that any computer program, written using any existing high level langauge, uses ___types___ and logic built on these ___types___ to implement different algorithms. Some fundamental types already exist, like in C, there exist `int`, `char`, etc... and some are user defined types. All logic of your program is working on these ___types___.

Using a set of fundamental types, we can define new types. We usually refer to these as user-defined types. The behaviour of these new types depend on their use case and the functions provided for interacting[$^{[2]}$](#On-Operators) with these types. For example, a type named `std::optional<...>` in C++ is used to store values of some pre-defined type, with a `bool` value of ___Boolean___ type that will specify whether or not any ___value___ of `std::optional` type contains a ___valid___ value or not.

In the end however, when your program is being run by the computing machine, none of the high level constructs will mean anything to it. CPUs just work on memory and registers. There is no concept of linked-list, or trees or any other data structure. It's just raw data and logic operating on the data.

Form now on, whether the term "type" is emphasized or not, you must always keep the above point at the back of your head. Whenever you see the term "type", you must assume I'm referring to it in this context.

### On Values
If we think about it more carefully, the logic is actually working on ___values___ of these ___types___, and there are only a finite[$^{[1]}$](#On-Discrete-Nature-of-Values) set of possible ___values___ for any given ___type___. An example of this is `char` type having values from $0$ to $127$, where each value represent a unique ASCII character. Another example will be the type `unsigned int` having values from $0$ to $2^{32}-1$. Also, a value may belong to more than one type at any time, an example of this is all values in range $-128$ to $127$ being, `char`, `short`, `int`, `long int` and `long long int` at the same time.

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

### On Proof & Verification

The logic built-in types and user-defined types may be wrong or buggy and hence verification (or proof) must be provided. By proof, I mean the proof of [___code correctness___](https://en.wikipedia.org/wiki/Correctness_(computer_science)), that the prover code makes sure that there won't exist any invalid state throughout the execution of whole program. Proof of code correctness however is not always possible because of complexity of modern software, lack of formal specifications with good coverage, and many other reasons. A quick prompt to ChatGPT reveals these reasons :
> 1. **Complexity:** Software systems are intricate, making it difficult to cover all scenarios.
> 2. **Incompleteness:** Gaps may exist due to evolving requirements or unaccounted scenarios.
> 3. **State Space Explosion:** Managing numerous system states becomes impractical.
> 4. **Halting Problem:** Certain code behaviors are undecidable, impacting proofs.
> 5. **Human Error:** Mistakes in formal proofs or assumptions can invalidate correctness.
> 6. **Resource Constraints:** Proof efforts might outweigh benefits in time and resources.
> 
> Efforts include testing, code reviews, formal verification for critical parts, and using languages with safety features. Balancing correctness efforts with practical development constraints is crucial.

It is therefore often easier to verify the code correction for a subset of possible states. Since this does not prove that the code is correct, it is still possible that code may contain some dangerous bugs. So, for this, Tyzen's aim is
- To provide proof of code correctness whenever possible and propagating that proof to help in proving or verifying for correctness of other parts of code.
- To provide verification for the code under testing, only to speed up the development process and not to help make the code completely bug free, because from my experience, good test cases help triaging the bugs faster as compared to programs without test. If debuggers are your sword, then test cases are your shield, but Tyzen must be the complete armor.


This area of research is known as [___formal verification___](https://en.wikipedia.org/wiki/Formal_verification) which is a subset of [___formal methods___](https://en.wikipedia.org/wiki/Formal_methods). We write specification in a specification language, and the language then attempts to either prove or verify the code.

## Existing Research

There already exist some very good specification languages, and theories on program verification.

- __Formal Methods__: These involve mathematically based techniques to specify, model, and reason about software systems. Techniques like model checking, theorem proving, and abstract interpretation fall under this category. Researchers often explore how formal methods can be used to verify programs based on specifications, ensuring correctness.

- __Specification Languages__: Different languages and formalisms exist to express program specifications. Languages like Z, VDM-SL, TLA+, and various temporal logics (such as LTL or CTL) provide ways to formally define the desired properties of a system.

- __Model Checking__: This method involves exhaustively checking whether a model of the program satisfies the desired properties specified in a temporal logic formula. It's useful for smaller systems but can face scalability issues with larger programs due to the state-space explosion problem.

- __Theorem Proving__: It involves using mathematical logic to formally prove that a program satisfies its specification. Interactive theorem provers (like Coq, Isabelle/HOL) and automated theorem provers (such as SMT solvers) are commonly used.

- __Abstract Interpretation__: This technique analyzes program behaviors by approximating them with abstract models. It can be used for both program analysis and verification, aiming to prove properties of the program.

- __Runtime Verification__: Unlike static verification techniques, this involves checking properties of a program during execution. Specifications are monitored while the program runs, allowing for dynamic verification.

## Scope of This Research/Work/Project

- __No New Research__ : This work is motivated by the needs of a software engineer, the focus won't be on developing completely new theories, because a lot of literature already exists and I haven't gone through all of them. All I need to do is read those books/papers/blogs/videos/podcasts... and understand how can I apply that for my specific use case. If I feel a need for extending the existing research, then I must welcome my thoughts with open arms.
- __Build For One First__ : I've never even built a compiler before, so some part of my time will be allocated to deciding the grammar of langauge and writing the compiler. This will take time, hence I cannot be too ambitious on how general I want to be. The first version of Tyzen must only focus on testing compiled programs written in C. Then extend to C++, Rust, Zig, and then generalize for all compiled programs. Then slowly move to scripted languages.

> In my experience, iterative software development involves starting with a basic version, adding features gradually, refactoring to generalize existing ones, and extending supported languages step by step before generalizing them.
