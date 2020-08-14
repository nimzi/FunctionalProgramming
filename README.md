# Ruminations of a "Functional" Judoka

## What this braindump is really about
Over the years I've had numerous conversation with smart people in the Silicon Valley and outside of it about topics relating to functional programming with interesting outcomes. The majority of these conversations took place with highly educated software professionals with an impressive track record at world's leading technology companies. The fellow technologists were typically NOT functional programmers, although they often had a degree of exposure to the space. I often found it hard to formulate my ideas in a sufficiently concise way and to drive the point home to a sufficient degree. I found it especially challenging to address the question of the relative **perceived** unpopularity of functional programming when I would have expained some of its benefits. Thus I set out to write a few paragraphs and hopefully organize my thoughts better. 

So, really this piece of writing isn't about functional programming. The term is really ill-defined and the more academic definition that could roughly be summarized as _programming without side effects (also known as mutations)_ isn't what specialists mean when they throw the term arount. More precisely, that is not **all** they mean and that isn't strictly what they mean because real programs have to allow mutations (such as input/output) to be useful.

In fact, functional programming today is an umbrella *codeword* that encompasses a bouquet very _concrete_ and _very abstract_ ideas, patterns, and tools that somehow mutually resonate and yield a thought process that helps tremendously in the development of often large and complex applications. In other words it helps with the development of the types of applications the industry demants today. In this sense a better codeword to describe this cohesive primordial soup would be **advanced** programming or maybe even **modern** programming, although the latter could never be fixed to a point in time and is therefore an mesnomer that carries the concept well nontheless.

Many attempts at explaining FP do so by contrasting it with object programming or procedural programming, however, I believe this is a wrong pathway to put an inquisitive reader on. FP and OO for example are more complementary than they are conflicting and the **modern** programming languages such as Swift, Scala, Rust, OCaml (and F#) along with the **modernized** Javascript (and its improvement - the Typescript), the Javas and C#s of the world are all headed in the direction of adopting features that well support functional programming. It might of been Martin Odersky (the creator of Scala) that coined a term **postfunctional** that is a decent tag for all this innovation in programming languages. 

More specifically, we can observe this evolution in the form of improved pattern matching, improved support for definition of lambdas, and the tendency toward syntactic concision in the well established languages such as C#, Java, JavaScript and even in the extemporaneous C++. The more modern languages are powerhouses of features that support FP concepts out of the box with some notable exponents such as Scala and F# even daring to support the M-word (well... more on that later). The features include Sum types, Type classes, Generic constraints, Custom Operators, and even types systems with **global resolution** (Rust, OCaml, F#) or type deduction that escapes function definitions. Moreover the features as a rule are complemented by very purposeful concise (but not cryptic) sytax that facilitate a style of programming that I am about to describe.  

