# Ruminations of a "Functional" Judoka

## What this braindump is really about
Over the years I've had numerous conversation with smart people in the Silicon Valley and outside of it about topics relating to functional programming with interesting outcomes. The majority of these conversations took place with highly educated software professionals with an impressive track record at world's leading technology companies. The fellow technologists were typically NOT functional programmers, although they often had a degree of exposure to the space. I often found it hard to formulate my ideas in a sufficiently concise way and to drive the point home to a sufficient degree. I found it especially challenging to address the question of the relative **perceived** unpopularity of functional programming when I would have expained some of its benefits. Thus I set out to write a few paragraphs and hopefully organize my thoughts better. 

So, really this piece of writing isn't about functional programming. The term is really ill-defined and the more academic definition that could roughly be summarized as _programming without side effects (also known as mutations)_ isn't what specialists mean when they throw the term arount. More precisely, that is not **all** they mean and that isn't strictly what they mean because real programs have to allow mutations (such as input/output) to be useful.

In fact, functional programming today is an umbrella *codeword* that encompasses a bouquet very _concrete_ and _very abstract_ ideas, patterns, and tools that somehow mutually resonate and yield a thought process that helps tremendously in the development of often large and complex applications. In other words it helps with the development of the types of applications the industry demants today. In this sense a better codeword to describe this cohesive primordial soup would be **advanced** programming or maybe even **modern** programming, although the latter could never be fixed to a point in time and is therefore an mesnomer that carries the concept well nontheless.

Many attempts at explaining FP do so by contrasting it with object programming or procedural programming, however, I believe this is a wrong pathway to put an inquisitive reader on. FP and OO for example are more complementary than they are conflicting and the **modern** programming languages such as Swift, Scala, Rust, OCaml (and F#) along with the **modernized** Javascript (and its improvement - the Typescript), the Javas and C#s of the world are all headed in the direction of adopting features that well support functional programming. It might of been Martin Odersky (the creator of Scala) that coined a term **postfunctional** that is a decent tag for all this innovation in programming languages. 

More specifically, we can observe this evolution in the form of improved pattern matching, improved support for definition of lambdas, and the tendency toward syntactic concision in the well established languages such as C#, Java, JavaScript and even in the extemporaneous C++. The more modern languages are powerhouses of features that support FP concepts out of the box with some notable exponents such as Scala and F# even daring to support the M-word (well... more on that later). The features include Sum types, Type classes, Generic constraints, Custom Operators, and even types systems with **global resolution** (Rust, OCaml, F#) or type deduction that escapes function definitions. Moreover the features as a rule are complemented by very purposeful concise (but not cryptic) sytax that facilitates a style of programming that I am about to describe.

## How syntax and semantics promote functional thought process 

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
The example below uses partial application to compute `result'` (read result prime just like in math... F# allows the prime character in identifiers). What is actually happening here is that the expression on the right of the `|>` operator produces a function and the vector literal is being applied to it. In this scenario vector is flavored to be a primary and metric as secondary. Notice that we made the **role** decision **locally** rather than at the point of definition; as makes sense in our local use case. We could have also made the metric appear as a primary by using a _higher order function_ (a function that operates on other functions) to reverse the order of arguments in `squaredLenght`; let it be called `flip`. The last line of the above example would then look like this:

```F#
result' =  Manhattan |> (flip squaredLength) {3.0, 4.0}
```

As a sidenote notice that a Python equivalent of the above would look something along the lines of `flip(squaredLength)(Manhattan, Vector2(3,4))`. This isn't bad, but try mentally extending this thought process in its natural direction and its easy to conclude that the operator based syntax exerts much less cognitive load than the army of parens in a highly nested C-style function call syntax. E.g. `f(g(h(k(arg))))` tends to be less readable than `arg |> k |> h |> g |> f`.

Picking up where we left off, we could think of the F# computation as happening inside a **context** of a metric where we **configure** the context prior to its utilization. 

```F#
type Vector2 = {x:float; y:float}
type Metric = Eucledian | Manhattan 

let squaredLenght m v = // ...


// Now prepare a context
let sl = squaredLenght Manhattan

// Now use the context for our computation

result = {3.0, 4.0} |> sl
result' = sl {3.0, 4.0}     // Or even simpler
```

* I can already hear the screams, rebuttals, and refutations. 
   - Yes, you can defune just functions in Python. 
   - Yes, you can even define curried functions. 
   - Yes, you can even emulate custom operators and write code similar to the above with some effort. But it takes affort and it is unnatural. And, this is consistent with my introductory point that you can do functional programming in almost any language. However, if things are too unnatural or difficult in programming developers aren't likely to do them. 
* Why code in this weird language with weird syntax?
    - Well, because it is actually proven to be more **ergonomic** and as the saying goes: "There is no such thing as intuitive. There's only the familiar". Really, truely deep. 
* Why all this abstract mumbo jumbo? Where are the productivity gains?
    - It is true that it is not extremely clear **yet** what they are, but if nothing else we had to think less about design. And the setup is more flexible. Good abstractions are hard to come up with and we ideally would like to defer the abstraction step until we absolutely are forced to take it (or better yet have the option of making it local)
* But didn't you say that OO and FP are complementary? It seems you are arguing against OO here. 
    - Not really. Object programming is a collection of design principles and patterns in its own right. Unfortunately the design philosophy and its machinery tend to be overused. Objects and classes are really about top level design and are about dividing a program into modules with possibly reusable interfaces. OO Machinery is great for macro-level design and when there is relative clarity and stability around requirements. At the micro level and especially in situations when requirements gyrate FP principles and patterns rule supreme. With OO it is tempting to classify anything and everything or to overbundle behaviors and develp abstractions at the micro level which do not withstand the test of time. Occasionally there are scenarios where multiple avenues are possible each involving different degrees of object and functional ingredients and design choices are a matter of taste. However, a developer skilled in both families of patterns should be able to use the **combined** toolbelt to his advandage. More imporatly here we are focusing on syntactic aspects and on how syntax (and cerain semantics) can be conducive to utilizing a **thought process** that can accelerate or otherwise improve engineering. 

Let us explore the above example a little further. We were asked by a business to implement some additional operations on vectors. Among them is a query for angle between vectors. Let us, for the sake of the discussion, assume that the metric plays a role in angle measurement. Some months later a new feature in our product requires us to perform compuations on planes and spheres. Straight lines on spheres are actually arcs and their Euclidean lenghts are computed differently (taking the radius into account, etc). Let us not worry about the math and leave implementations out of it.


```F#
type Vector2 = {x:float; y:float}
type Metric = Eucledian | Manhattan 
type Space = Planar | Spherical of radius:float

let earthRadius = 6356 // kilometers 

let squaredLength m s v = // ... now have 3 parameters where s is space
let angle m s v v = // ... 

// Now prepare a context
let context = Manhattan, Spherical(earthRadius)
let squaredLength = context ||> squaredLenght 
let angle = context ||> angle
// Now use the context for our computation

let a = {3.0, 4.0}
let b = {1.0, 7.0}
dist = squaredLength vec
theta = angle vec
```

Despite being statically typed languages MLs allow us to **overshadow** constants. The `let` keyword actually declares constants and not variables (which is consitent with the functional thought process) however the later `let`s effectively redeclare the constants. This has the effect of us almost specializing functions for a given use case. This is very convenient and very flexible. We are once again not forced to make decisions about which class the computations live on. The requirements might change in the future and functions are just very flexible and **granular** building blocks that are easy to specialize given partial application and more generally the light and concise syntax of MLs. 

The above is but a grain of sand in the bigger discussion of modern software engineering and how certain programming languages support and promote useful design patterns and architectures. We may not fully appreciate it at times but programming languages affect and even shape our thought processes and design decisions. A much longer conversation is necessary to cover this space of modern programming languages and how they support functional patterns and concepts. The thought process is rooted in _Algebraic Structures_, _Algebraic Data Types_, _Type classes_, _referential transparency_, _state isolation_, and list goes on. There is also much to be said about how type systems of certain languages support the functional mojo and the whole M-word topic. Really though, functional programmers have come up with way to many scary sounding terms because we, the programmers, do that to **every** domain we touch; and I mean **every**. One look at the terminology of any **unfamiliar** CS subdomains such as Computer Graphics and Geometric Modeling or Machine Learning would have one cringe reflexively even if the undelying concepts are straightforward. Repeating myself, FP is about **advanced** programming concepts, tools, and methods some of which take a significant effort to master. It would be naive this subdomain would escape the fate of its peers.  
