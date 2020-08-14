# Ruminations of a "Functional" Judoka

## What this braindump is really about
Over the years I've had numerous conversation with smart people in the Silicon Valley and outside of it about topics relating to functional programming with interesting outcomes. The majority of these conversations took place with highly educated software professionals with an impressive track record at world's leading technology companies. The fellow technologists were typically NOT functional programmers, although they often had a degree of exposure to the space. I often found it hard to formulate my ideas in a sufficiently concise way and to drive the point home to a sufficient degree. I found it especially challenging to address the question of the relative **perceived** unpopularity of functional programming when I would have expained some of its benefits. Thus I set out to write a few paragraphs and hopefully organize my thoughts better. 

So, really this piece of writing isn't about functional programming. The term is really ill-defined and the more academic definition that could roughly be summarized as _programming without side effects (also known as mutations)_ isn't what specialists mean when they throw the term arount. More precisely, that is not **all** they mean and that isn't strictly what they mean because real programs have to allow mutations (such as input/output) to be useful.

In fact, functional programming today is an umbrella *codeword* that encompasses a bouquet very _concrete_ and _very abstract_ ideas, patterns, and tools that somehow mutually resonate and yield a thought process that helps tremendously in the development of often large and complex applications. In other words it helps with the development of the types of applications the industry demants today. In this sense a better codeword to describe this cohesive primordial soup would be **advanced** programming or maybe even **modern** programming, although the latter could never be fixed to a point in time and is therefore an mesnomer that carries the concept well nontheless.

Many attempts at explaining FP do so by contrasting it with object programming or procedural programming, however, I believe this is a wrong pathway to put an inquisitive reader on. FP and OO for example are more complementary than they are conflicting and the **modern** programming languages such as Swift, Scala, Rust, OCaml (and F#) along with the **modernized** Javascript (and its improvement - the Typescript), the Javas and C#s of the world are all headed in the direction of adopting features that well support functional programming. It might of been Martin Odersky (the creator of Scala) that coined a term **postfunctional** that is a decent tag for all this innovation in programming languages. 

More specifically, we can observe this evolution in the form of improved pattern matching, improved support for definition of lambdas, and the tendency toward syntactic concision in the well established languages such as C#, Java, JavaScript and even in the extemporaneous C++. The more modern languages are powerhouses of features that support FP concepts out of the box with some notable exponents such as Scala and F# even daring to support the M-word (well... more on that later). The features include Sum types, Type classes, Generic constraints, Custom Operators, and even types systems with **global resolution** (Rust, OCaml, F#) or type deduction that escapes function definitions. Moreover the features as a rule are complemented by very purposeful concise (but not cryptic) sytax that facilitates a style of programming that I am about to describe.

## Starting with a little bit of syntax

So, I just said that OO and FP are more complimentary than they are in conflict and I stand by what I said. However, contrasting **syntax** that is **typically** associated with OO is a good point to start our journey. Of course there is no avoiding of picking a target language for this "exercise" in contrast and I will start with an ML derivative such as (OCaml, F#) and will contrast is with something from the variety of the likes of  C# or Java or, say, Python and Javascript.

```F#
type Vector2 = {x:float; y:float}

let squaredLenght v = v.x * v.x + v.y * v.y

result = squaredLenght {3.0, 4.0}
```

Here we define a functions `squaredLength` that operates on a datatype `Vector2`, a two-dimensional vector. An equivalent Python definition might like like this. In ML derivatives we do not have to use parest during function application. In fact the last line (the one where we apply an argument) can be rewritten as follows:

```F#
result = {3.0, 4.0} |> squaredLenght 
```

That is with argument on the left followed by an "apply" operator and followed by function name. We shall se how this is useful. Now switching to Python...

```Python
class Vector2:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    

    def squaredLength (self):
        return self.x * self.x + self.y * self.y
        

result = Vector2(3,4).squaredLength()
```

We purposefully chose a Python method instead of function to contrast the dot notation with a function call in, say F#. So far so good. Dot notation is actually good and useful. In fact it almost looks like we traded the dot for the `|>` operator except in F# we are 'pushing' an argument into a function rather than a method. We have gained exactly **nothing** in productivity or understandability or architectural advantages.

Now let us imagine we are working toward some GIS package to measure distances between locations on maps. In such packages a concept such as _metric_ is inalienable. A metric is a method of distance measurement. The _Euclidean_ metric is what we are well familiar with and what we've implemented. However in urban enviroments a _Manhattan metric_ is more appropriate where we cannot move accross diagonals and the distance is computed as `x + y`. Of course it is easy to imagine other possibilies metrics and consequently we would like write flexible code that takes a metric into account for our lenght computation.

Well, in an OO scenario we would probably write a _Metric_ class that given a _Vector2_ would compute its lenght (or squaredLength). Makes sense! So, probably `squaredLenght` method now needs to live on a _Metric_ object and maybe we write a another **convenience** method on the _Vector2_ that takes a metric as a parameter. Or maybe we should write just a function (thankfully Python allows writing just functions) that takes the two objects and produces a result. But let us take a step back and assume that we decided to use methods. After all we love our dot notation for a reason. We can chain our calls and make long meaningful expressions of the form `obj.foo(arg).bar(anotherArg).zed(someOtherArg, andYetAnother)`. So we are faced with a decision on whether a _Metric_ object should feature the implementations specific to each type of geometric primitive or the primitive should just compute compute values based on a metric argument. In other words we need to decide a primary object from the secondary. Had we just used a function that took the two arguments we wouldn't have to contend with these **design** decisions. 

ML-style languages provide an interesting feature called **partial application** for functions which is related to the concept of *curried* functions which enables use peculiar way of writing the above scenario down.

```F#
type Vector2 = {x:float; y:float}
type Metric = Eucledian | Manhattan 

let squaredLenght m v = 
    if m = Eucledian then v.x * v.x + v.y * v.y
    else let sum = v.x + v.y in sum * sum

result = squaredLenght Manhattan {3.0, 4.0}
// alternatively
result' = {3.0, 4.0} |> squaredLenght Manhattan
```
The example below uses partial application to compute `result'` (read result prime just like in math... F# allows the prime character in identifiers). What is actually happening here is that the expression on the right of the `|>` operator produces a function and the vector literal is peing applied to it. 

I can already hear the screams, rebuttals, and refutations of the form: "But 
