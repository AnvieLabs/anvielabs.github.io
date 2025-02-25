---
title: "Ambiguity In Grammars"
weight: 5
draft: false
description: ""
slug: "grammar-ambiguity"
tags: ["grammar", "language", "formal-language", "ambiguity", "automata-theory", "parsing", "parsers", "parse", "parse-tree", "meaning", "semantics"]
authors: [ "Siddharth Mishra" ]
series: ["Formal Languages & Gramamars"]
series_order: 3
showComments: true
---

<!-- Enable KaTeX -->
{{< katex >}}

## Language & Parsing

Grammars are what provide a structure to a language. They help linearize a complex
structure, and then provide you a way to de-linearize it again afterwards. 
By linearization I mean forming sentences that follow grammatical rules, and hence
de-linearization would mean taking a previously linearized sentence and converting it back
to it's original form.

This is how we humans communicate as well. We as humans first form a complex meaning
inside our head, then we use a grammar of a language we've previously studied, or know already,
as a tool, to convert it to a sentence (a linearized form of a complex structure in our brain),
then another human (or nowadays a machine) reads this linear form, and then uses the same grammar
you used to convert it back to it's original form. If same grammar is used then both users
get the same piece of information in their head, otherwise, only one has the original sentence
and other does not. So is having the same grammar sufficient to read a sentence and get it's
true meaning back? I love to take my time to explain things, and that's what I'm going to do here.
I'd really love to start with what I mean by _"meaning"_ of a sentence, then some talk about a
generic understanding of this _"complex structure"_ I speak of, and then finally I'll talk
about the main topic of this article.

{{< alert >}}

Please note that I'm no academic researcher, so having this article peer-reviewed and correcting
factual errors is not always possible. I'm also not an expert in the grammars, haven't published
in any journal, and am just a beginner in this domain. So, it's possible that I'll make errors.
I urge the reader to make fixes, if any problem is found and submit a PR in the blog repository.
Thanks in advance! :smile:

{{< /alert >}}

### Language

Formally a language is defined as a set of strings. Don't overthink it, just create a set
of strings, and you have a language. An example language may look something like this :

\\[ L = \lbrace \text{"a"}, \text{"xyz"}, \text{"kimi no nawa?"}, \text{"..."} \rbrace \\]

The language above contains four strings only. This is a very limited and not-so-useful language,
compared to what we already know.

### Grammar

A grammar is a tool used to generate (linearize) or parse (de-linearize) strings of a language.
It provides a structure to strings of the language it corresponds to. In computer science, this
structure is usually a tree data type. There is a root node that represents that starting symbol
in the grammar, each node in the tree represents a non terminal, and then each leaf node represents
a terminal symbol.

I'd really love to talk only about grammars in some other article, let's mark this as a todo then

{{< article link="/blog/formal-languages-and-grammars/" >}}

So, continuing with the _"complex structure"_, in case of machines, this tree is the complex structure,
and I don't know how this works in human brain, but the thing to remember here is that this structure
is not limited to trees or how it works in our brain. How would it work when a language is a sequence
of electronic signals, and there's a circuit that parses this directly as it comes and makes decisions
based on that? I cannot imagine there being something like a tree.

This post is just about computers though, so from now on, let's just limit ourselves to that.
When dealing with computer languages, a grammar is a triplet of a set of terminals \\(\Lambda\\), 
a set of non-terminals \\(N\\), a set of production rules \\(\Gamma\\), and a start symbol \\(S\\), i.e,
a grammar \\(G\\) is \\((\Lambda, N, \\Gamma, S)\\)

An example grammar looks like this :

\\[
\begin{aligned}
\Lambda &= \lbrace "0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "+", "-", "*", "/" \rbrace \\\\
\\
N &= \lbrace 
    \langle \text{expr} \rangle,
    \langle \text{term} \rangle,
    \langle \text{factor} \rangle,
    \langle \text{digit} \rangle,
    \langle \text{number} \rangle
\rbrace \\\\
\end{aligned}
\\]

\\[
\Gamma = 
\begin{cases}
\langle \text{digit} \rangle &::&=& "0" \mid "1" \mid "2" \mid "3" \mid "4" \mid "5" \mid "6" \mid "7" \mid "8" \mid "9" \\\\
\\\\
\langle \text{number} \rangle &::&=   & \langle \text{digit} \rangle \langle \text{number} \rangle \\\\
                              &  &\mid& \langle \text{digit} \rangle \\\\
\\\\
\langle \text{term} \rangle &::&=   & \langle \text{expr} \rangle "+" \langle \text{expr} \rangle \\\\
                            &  &\mid& \langle \text{expr} \rangle "-" \langle \text{expr} \rangle \\\\
\\\\
\langle \text{factor} \rangle &::&=   & \langle \text{expr} \rangle "*" \langle \text{expr} \rangle \\\\
                              &  &\mid& \langle \text{expr} \rangle "/" \langle \text{expr} \rangle \\\\
\\\\
\langle \text{expr} \rangle &::&=   & \langle \text{term} \rangle \\\\
                            &  &\mid& \langle \text{factor} \rangle \\\\
                            &  &\mid& \langle \text{number} \rangle \\\\
\end{cases}
\\]

\\[
S = \langle \text{expr} \rangle
\\]

This is a grammar for the language of mathematical expressions that involve only the four basic operations.
Those even with a beginner introduction to grammars might be able to easily think of this grammar
a really bad one (with respect to it's usability in a real language).

### String Generation & Parsing

Now if you start with the non-terminal \\(\langle \text{expr} \rangle\\) and keep following the production
rules in \\(\Gamma\\), and try to reach terminals in \\(\Lambda\\) from there, you'll get a mathematical
expression always. Note that we're not talking about the validity of the expression here. For all we know,
the string generated, when evaluated may give an undefined value (\\(\frac{1}{0}\\) form), but we don't care
about that here (for now).

Examples of strings generated from this are 

- \\(1 + 2 * 3\\)
- \\(1337 + 4095 / 100024840387385245 - 0 / 400000000\\)

Now can you try to get the original value I thought this expression will evaluate to using the same
grammar? You should consider giving it a try! Don't use the original way of evaluating mathematical expressions,
but instead follow the grammar above. Start from the start symbol \\(S\\) and try to generate a parse
tree, and then evaluate the expression based on that.

Following is a parse tree generated while doing left-to-right top-down parsing : 

{{< mermaid >}}
---
title: Figure 1
---
graph TD;
    E["$$\langle \text{expr}_1 \rangle$$"];
    E1["$$\langle \text{expr}_2 \rangle$$"];
    E2["$$\langle \text{expr}_3 \rangle$$"];
    E3["$$\langle \text{expr}_4 \rangle$$"];
    E4["$$\langle \text{expr}_5 \rangle$$"];
    T["$$\langle \text{term} \rangle$$"];
    F["$$\langle \text{factor} \rangle$$"];
    N["$$\langle \text{number} \rangle$$"];
    N1["$$\langle \text{number} \rangle$$"];
    N2["$$\langle \text{number} \rangle$$"];
    D["$$\langle \text{digit} \rangle$$"];
    D1["$$\langle \text{digit} \rangle$$"];
    D2["$$\langle \text{digit} \rangle$$"];
    
    E --> T
    T --> Plus["$$+$$"]
    Plus --> E1 --> N --> D --> Tr1["$$1$$"]
    Plus --> E2 --> F --> Mul["$$*$$"]
    Mul --> E3 --> N1 --> D1 --> Tr2["$$2$$"]
    Mul --> E4 --> N2 --> D2 --> Tr3["$$3$$"]
{{< /mermaid >}}

Interestingly, I won't be surprised if you fail to form this parse tree, because there exists
another incomplete one, that fails to parse this string gracefully, while using the same grammar.
What if instead of applying \\(\langle \text{factor} \rangle\\) when applying \\(\langle \text{expr}_3 \rangle\\),
we apply a \\(\langle \text{number} \rangle\\)?

{{< mermaid >}}
---
title: Figure 2
---
graph TD;
    E["$$\langle \text{expr} \rangle$$"];
    E1["$$\langle \text{expr} \rangle$$"];
    E2["$$\langle \text{expr} \rangle$$"];
    T["$$\langle \text{term} \rangle$$"];
    N["$$\langle \text{number} \rangle$$"];
    N1["$$\langle \text{number} \rangle$$"];
    D["$$\langle \text{digit} \rangle$$"];
    D1["$$\langle \text{digit} \rangle$$"];
    
    E --> T
    T --> Plus["$$+$$"]
    Plus --> E1 --> N --> D --> Tr1["$$1$$"]
    Plus --> E2 --> N1 --> D1 --> Mul["$$2$$"]
{{< /mermaid >}}

We were just able to parse \\(1 + 2\\), and left out the \\(* 3\\) part. Therefore, if someone really
used this grammar, they probably won't be able to understand what \\(1 + 2 * 3\\) means!

This here is ambiguity in the grammar. Also, note that depending on the direction (left-to-right, right-to-left) from which
we start parsing, we can find at least one more parse tree.

## Meaning of Meaning

What does the ___meaning___ of a string mean? The correct term to use here, and to avoid confusion is
___semantics___. There is a completely different branch of computer science and mathematics that studies
this. There are two different types of semantics as far as I know, and that's all I know, and because I don't
know that, I'll explain what I understand by semantics here.

I'd like to compare semantics in context of humans. Each sentence in any language (let's consider English),
can be broken down into one or more sub-sentences, such that we can assign one and only one meaning to it.
The meaning corresponding to each of these sub-sentences is just a real-life action or an entity that we associate with it.
So when I say \\(\text{"I love You!"}\\), I can break it into \\(\lbrace \text{"I"}, \text{"love"}, \text{"You!"} \rbrace\\),
where \\(\text{"I"}\\) is refering to me (or the author/speaker), \\(\text{"love"}\\) is an action of caring about someone,
and \\(\text{"You"}\\) is refering to the reader/listener.

With similar analogy, in computers, we can break down a sentence into it's sub-parts, and then assign a unique meaning
to each of these sub-parts. Just consider a computer program, the complete program in itself is a sentence, but it's made
up of functions, classes, mathematical expressions, if-else statements, etc... Each of these are sub-parts that keep
breaking down recursively, and each ___mean___ something! A variable, a jump action, a move action, an arithmetic action, a memory address etc...

In the end, we merge all these small meanings to make up a bigger meaning of the complete original sentence.

### Writing Down The Meaning

To make it formal, and have my own definition of _meaning_ of a sentence, I'll define _meaning_ as the 
[inorder traversal](https://en.wikipedia.org/wiki/Tree_traversal#In-order,_LNR)
of the parse tree generated while parsing a sentence. We can use [S-Expr](https://en.wikipedia.org/wiki/S-expression) 
form to write down the meaning of a sentence. Why? because it clearly lays out the actions and entities involved
in the order in which they're executed and used within the machine.

As an example, S-Expr for Figure 1 will be 
`(expr (term (+ (expr (number (digit 1))) (expr (factor (* (expr (number (digit 2))) (expr (number (digit 3)))))))))`

and the same for Figure 2 will be 
`(expr (term (+ (expr (number (digit 1))) (expr (number (digit 2))))))`

Two meanings for the same string! There we have my friends, the ambiguity! Two machines understanding the same
string differently, depending on what decisions they made. This happens in real life as well, with so called
___double meaning___ sentences, where until unless you're provided the context of conversation, you can never
be sure what the sentence actually means.

## One Way To Remove Ambiguity

### Inherently Ambiguous Languages

Some languages are inherently ambiguous. I think I still need to spend some more time understanding
inherently ambiguous languages, so I'll mark that as a todo again, and skip for now. You can never
remove ambiguity from an inherently ambiguous language, because no matter what grammar you find for it,
that will be ambiguous, and will end up having more than one S-Expr meaning for atleast one string in
the language.

{{< article link="/blog/inherently-ambiguous-languages/" >}}

One possible way though would be to carefully remove these sentences by considering these as exceptions,
from the language itself, given that it's tolerable to remove these sentences.

### Precedence

There's a reason why I picked up the expression grammar :yum:. It's a well known grammar, and I
fought with it a few hours ago, trying to write it on my own without looking at existing solutions.
Precedence is a tool that forces the grammar to apply certain rules first before others. The reason
why our current version of expression grammar is ambiguous is because we can expand any of the \\(\langle \text{expr} \rangle\\)
at any step we want. Precedence removes any chances of this.

Precedence usually works by strongly coupling a non-terminal with a terminal, such that whenever
the terminal is encountered, the parser (reader/listener) will know for sure what needs to be expanded.
This way, the order in which we apply the non-terminals is not relevant (like it is in current version).

In our case we follow the B.O.D.M.A.S (Bracket of Division Multiplication Addition Subtraction) principle.
So brackets have the highest precedence, so they must be attempted to be parsed earlier than any other
non-terminal, then well parse Multiplication and Division with same precedence, and then finally Addition and Subtraction.

Following this ideaology, I cooked up the following new version of the expression grammar, which 

\\[
\Gamma = 
\begin{cases}
\langle \text{digit} \rangle &::&=   & "0" \mid "1" \mid "2" \mid "3" \mid "4" \\\\
                             &  &\mid& "5" \mid "6" \mid "7" \mid "8" \mid "9" \\\\
\\\\
\langle \text{number} \rangle &::&=   & \langle \text{digit} \rangle \langle \text{number} \rangle  \\\\
                              &  &\mid& \langle \text{digit} \rangle \\\\
\\\\
\langle \text{expr0} \rangle &::&=   & \langle \text{expr1} \rangle "+" \langle \text{expr0} \rangle \\\\ 
                             &  &\mid& \langle \text{expr1} \rangle "-" \langle \text{expr0} \rangle \\\\
                             &  &\mid& \langle \text{expr1} \rangle \\\\
\\\\
\langle \text{expr1} \rangle &::&=   & \langle \text{expr} \rangle "*" \langle \text{expr1} \rangle \\\\ 
                             &  &\mid& \langle \text{expr} \rangle "/" \langle \text{expr1} \rangle \\\\
                             &  &\mid& \langle \text{num} \rangle \\\\
\\\\
\langle \text{expr} \rangle  &::&=   & \langle \text{expr0} \rangle \\\\ 
\end{cases}
\\]

Let's try to generate a parse tree using this grammar while parsing the string \\(1 + 2 * 3\\).
Again I'll do left-to-right top-down parsing, because it comes more naturally to me (and almost to everyone).

{{< mermaid >}}
---
title: Figure 3
---
graph TD;
    E["$$\langle \text{expr} \rangle$$"];
    E0["$$\langle \text{expr} \rangle$$"];
    Ex0["$$\langle \text{expr0} \rangle$$"];
    Ex00["$$\langle \text{expr0} \rangle$$"];
    Ex01["$$\langle \text{expr0} \rangle$$"];
    Ex1["$$\langle \text{expr1} \rangle$$"];
    Ex10["$$\langle \text{expr1} \rangle$$"];
    Ex11["$$\langle \text{expr1} \rangle$$"];
    Ex12["$$\langle \text{expr1} \rangle$$"];
    N["$$\langle \text{number} \rangle$$"];
    N0["$$\langle \text{number} \rangle$$"];
    N1["$$\langle \text{number} \rangle$$"];
    D["$$\langle \text{digit} \rangle$$"];
    D0["$$\langle \text{digit} \rangle$$"];
    D1["$$\langle \text{digit} \rangle$$"];
    
    E --> Ex0
    Ex0 --> Plus["$$+$$"]
    Plus --> Ex1 --> N --> D --> Tr1["$$1$$"]
    Plus --> Ex00 --> Ex10 --> Mul["$$*$$"]
    Mul --> E0 --> Ex01 --> Ex12 --> N0 --> D0 --> Tr2["$$2$$"]
    Mul --> Ex11 --> N1 --> D1 --> Tr3["$$3$$"]
{{< /mermaid >}}

This parse tree is always unique, no matter which non-terminal you expand first.
Here I associated \\("+"\\) and \\("-"\\) with \\(\langle \text{expr0} \rangle\\),
and similarly \\("*"\\) and \\("/"\\) with \\(\langle \text{expr1} \rangle\\).
If you notice a pattern, you can add even more higher layers of precedence over
this by adding more rules and adjusting existing ones.

There might be other ways for removing ambiguity in grammars, but this
is the one I had to learn recently. I really suffered a lot with a poor
grammar recently, and I don't want to make the same mistake again, while
working on a new language.

This time, the meaning of the expression is `(expr (expr0 (+ (expr1 (number (digit 1))) (expr0 (expr1 (* (expr (expr0 (expr1 (number (digit 2))))) (expr1 (number (digit 3)))))))))`
