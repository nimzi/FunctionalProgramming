# Ruminations of a "Functional" Judoka

## What this braindump is really about
Over the years I've had numerous conversation with smart people in and outside of the Silicon Valley about topics relating to functional programming with interesting outcomes. The majority of these conversations took place with highly educated software professionals with an impressive track record at world's leading technology companies. The fellow technologists were typically NOT functional programmers, although they often had a degree of exposure to the space. I often found it hard to formulate my ideas in a sufficiently concise way and to drive the point home to a sufficient degree. I found it especially challenging to address the question of the relative **perceived** unpopularity of functional programming upon explaining some of its benefits. Thus I set out to write a few paragraphs and hopefully sort out my thoughts better. 

So, really this piece of writing isn't about functional programming as it is understood academically but rather about what the term means to people in the industry who practice it commercially. The more academic definition that could roughly be summarized as _programming by minimizing side effects (also known as mutations) and by maximizing referential transparency_ isn't really what specialists mean when they throw the term arount. More precisely, that is not **all** they mean and that isn't strictly what they mean because real programs have to allow mutations (such as input/output) to be useful. Also real programs ship!

In fact, functional programming today is an umbrella *codeword* that encompasses a bouquet of very _concrete_ and _very abstract_ ideas, patterns, and tools that somehow mutually resonate and yield a thought process that helps tremendously in the development of often large and complex applications. In other words, it helps with the development of the types of applications the industry demands today. In this sense a better codeword to describe this cohesive primordial soup would be **advanced** programming or maybe even **modern** programming, although the latter could never be pinned to a point in time and is therefore an misnomer that carries the concept well nonetheless.

Many attempts at explaining FP do so by contrasting it with the current status quo (object programming), however, I believe this is a suboptimal pathway to understanding. FP and OO are actually more complementary than they are in conflict. A testament to this are the **modern** programming languages such as Swift, Scala, Rust, OCaml (and F#) which support FP to various degrees of excellence. The well established languages such as the **modernized** Javascript and its improvement the Typescript along with the Javas and C#s of the world are all evolving in the direction of adopting features that well support functional programming. It might have been Martin Odersky (the creator of Scala) that coined a term **postfunctional** that is a decent tag for all this innovation in programming languages. Postfunctional languages exhibit a fuzion of features that cohesively support both paradigms.

More specifically, we can observe the momentum in the form of improved pattern matching, improved support for definition of lambdas, and the tendency toward syntactic concision (especially as it relates to immutable structures) in the well established languages such as C#, Java, JavaScript and even in the extemporaneous C++. The more modern languages are powerhouses of features that support FP concepts out of the box with some notable exponents such as Scala and F# even daring to support the M-word (well... more on that later). Their features include Sum types, Type classes, Generic constraints, Custom Operators, and even type systems with **global resolution** (Rust, OCaml, F#) or global type deduction that extends beyond the scope of functions. Moreover the features as a rule are complemented by very purposeful concise (but not necessarily cryptic) sytax that facilitates a style of programming that I am about to describe.

## How syntax and semantics promote functional thought process 

So, I just said that OO and FP are more complimentary than they are in conflict and I stand by what I said. However, contrasting **syntax** that is **typically** associated with OO is a good point to start our journey. Of course there is no avoiding of picking a target language for this "exercise" in contrast and I will start with an ML derivative such as (OCaml, F#) and will contrast is with something from the variety of the likes of  C# or Java or, say, Python and Javascript.

```F#
type Vector2 = {x:float; y:float}

let squaredLenght v = v.x * v.x + v.y * v.y

let result = squaredLenght {3.0, 4.0}
```

Here we define a functions `squaredLength` that operates on a datatype `Vector2`, a two-dimensional vector. An equivalent Python definition might like like this. In ML derivatives we do not have to use parest during function application. In fact the last line (the one where we apply an argument) can be rewritten as follows:

```F#
let result = {3.0, 4.0} |> squaredLenght 
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
type Metric = Euclidean | Manhattan 

let squaredLenght m v = 
    if m = Euclidean then v.x * v.x + v.y * v.y
    else let sum = v.x + v.y in sum * sum

let result = squaredLenght Manhattan {3.0, 4.0}
// alternatively
let result' = {3.0, 4.0} |> squaredLenght Manhattan
```
The example below uses partial application to compute `result'` (read result prime just like in math... F# allows the prime character in identifiers). What is actually happening here is that the expression on the right of the `|>` operator produces a function and the vector literal is being applied to it. In this scenario vector is flavored to be a primary and metric as secondary. Notice that we made the **role** decision **locally** rather than at the point of definition; as makes sense in our local use case. We could have also made the metric appear as a primary by using a _higher order function_ (a function that operates on other functions) to reverse the order of arguments in `squaredLenght`; let it be called `flip`. The last line of the above example would then look like this:

```F#
let result' =  Manhattan |> (flip squaredLength) {3.0, 4.0}
```

As a sidenote notice that a Python equivalent of the above would look something along the lines of `flip(squaredLength)(Manhattan, Vector2(3,4))`. This isn't bad, but try mentally extending this thought process in its natural direction and its easy to conclude that the operator based syntax exerts much lower cognitive load than the army of parens in a highly nested C-style function call syntax. E.g. `f(g(h(k(arg))))` tends to be less readable than `arg |> k |> h |> g |> f`.

Picking up where we left off, we could think of the F# computation as happening inside a **context** of a metric where we **configure** the context prior to its utilization. 

```F#
type Vector2 = {x:float; y:float}
type Metric = Euclidean | Manhattan 

let squaredLenght m v = // ...


// Now prepare a context
let sl = squaredLenght Manhattan

// Now use the context for our computation

let result = {3.0, 4.0} |> sl
let result' = sl {3.0, 4.0}     // Or even simpler
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
    - Not really. Object programming is a collection of design principles and patterns in its own right. Unfortunately the design philosophy and its machinery tend to be overused at this point in our history. Objects and classes are really excellent top-down design and are about dividing a program into modules with possibly reusable interfaces. OO machinery is great for macro-level decisions and when there is relative clarity and stability around requirements. At the micro level and especially in situations when requirements gyrate FP principles and patterns rule supreme. With OO it is tempting to classify anything and everything into hierarchies or to overbundle behaviors and to develp abstractions at the micro level which do not withstand the test of time. Occasionally there are scenarios where multiple avenues are possible each involving different degrees of object and functional ingredients. In such scenarios design choices can be a matter of taste. However, a developer skilled in both families of patterns should be able to use the **combined** toolbelt to his advandage. More imporatly programming language which make a functional or postfunctional **thought process** ergonomic can accelerate or otherwise improve engineering. 

Let us explore the above example a little further. We were asked by a business to implement some additional operations on vectors. Among them is a query for angle between vectors. Let us, for the sake of the discussion, assume that the metric plays a role in angle measurement. Some months later a new feature in our product requires us to perform compuations on planes and spheres. Straight lines on spheres are actually arcs and their Euclidean lenghts are computed differently (taking the radius into account, etc). Let us not worry about the math and leave implementations out of it.


```F#
type Vector2 = {x:float; y:float}
type Metric = Euclidean | Manhattan 
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

let dist = squaredLength vec
let theta = angle vec
```

Despite being statically typed languages MLs allow us to **overshadow** constants. The `let` keyword actually declares constants and not variables (which is consitent with the functional thought process) however the later `let`s effectively redeclare the constants. This has the effect of us almost specializing functions for a given use case. This is very convenient and very flexible. We are once again not forced to make decisions about which class the computations live on. The requirements might change in the future and functions are just very flexible and **granular** building blocks that are easy to specialize given the support for partial application and more generally the light and concise syntax of MLs. 

> In FP this is thought process is very common. We think of solutions in terms of computations and their contexts and in terms of specializing computations for a given context as in the above example. 

## Referential transparency, immutability, and cognitive load

There is all this buzz around immutability, constants instead of variables, and how it makes programming simpler. Isn't it more difficult to program with more constraints rather than fewer? Isn't it the cast that it is harder to design functional algorithms than their non-functional counterparts? Yes, this is the case. The full explanation is longer than I am willing to commit to in this article, however, hopefully I am going to chizel out a few points that will shed some light on the above. 

First, programming with fewer mutations decreases a **cognitive load** on a programmer. Another way to see this is it is easier to reason about code that makes use of **mostly** immutable structures. The reason I put an emphasis on _mostly_ in the previous sentense is that in many cases it is counterproductive to dead-bolt onself to immutability as one runs the risk of making many simple things overly complicated (and this isn't ideal for real software that ships). However, huge percentages of real codes or real applications stand to benefit enormously from high degree of enforcement of immutability. Consider a function call `f(a,b,c,d)` expressed in C-style syntax or its equivalent `f a b c d` expressed in ML-style syntax. If arguments `a b c d` are immutable structures (in other words constants) upon ruminating over implementation of `f` a developer can assume that the values represented by the variables won't change in the middle of execution of `f`. This is a huge deal! Huge! If in addition `f` doesn't make use of some kind of global mutable state the function is called **pure** or **referentially transparent**. What this means is that `f` can in theor be replaced by a lookup table of all permutations of possible arguments. This makes the function very predictable in a sense that its output depends **only** on the arguments. Why is this desirable? If nothing else such computations are easier to test (as in unit testing) and easier to reason about. Pure computations **compose** into bigger pure computations which in turn again exhibit the nice characteristics we like to see as engineers. 

...Quicksort as a pure computation if mutations are isolated...

## What about Inheritance, Encapsulation, and Polymorphism

So, as OO programmers we are trained to recognize hierarchies and to use the hierarchical nature of things to our advantage and at first sight it might seem that FP does away with all that. Let us remember that hierarchies are first of all graphs and more precisely acyclic digraphs with the most common digraphs being trees (which we lovingly tend to call single inheritance hierarchies). Functional programmers swim with the graphs except they tend use many more patterns to deal with them and to take advantage of, at times, hierarchical nature of varous domains.

Most distributed applications deal with user data at one point or another and very often with their authentication and authorization. Let us briefly remind ourselves taht authentication aspect deals with recognizing a valid user (given, say, and id and a password) while authorization deals with capabilities of users (or application capabilities as they relate to a given user). Let us further assume that in our system we have _basic_ users (some designs use a term guest user) and _advanced_ ones (called admins when approprite for an application). We can imagine that admins are _authorized_ to do everything _guests_ are and more. These requirements immediately present an opportunity to employ OO features and define a hierarchy. Moreover, it might turn out well in the unlikely case that the requirements will persist over the lifecycle of an application. A common functional pattern is to think in terms of _data_ and _evidence_ that the data is something or other and to allow computations on the user data to take place in the **context** of the evidence that a user is something or another. 

I will allow myself a little digression here and will give an analogy from mathematics. Imagine a math libray that operates on different types of number kernels. More specifically, say our number objects are there to model real and complex numbers. Complex numbers can also be viewed as vectors with the corresponding geometric primities. This is also an oportunity for a hierarchy. However, another way to think about the scenario is that of a computation on a complex number as taking place in the face of the _evidence_ (evidence being an evidence object or even a type) that the number is also a vector. How is this good? This is just very flexible. We don't need to ruminate over ascendancy of each concept. Is a complex number a vector or is a vector a complex number. Later we could introduce a point object and in turn a point could be viewed as a vector or a complex number. Under this design we don't need to think about how to fit a point concept into an existing hierarchy of concepts. All we need is the evidence that a point can be viewed as, say, a complex number in certain **contexts**. This is a little abstract at this point but will become crystal clear shortly. I promise!

Back to our users scenario. Let us make things a little more concrete. Our gust users don't need an id or a password, just the contact info, and their authentication never expires once, say, their contact info had been verified. Admins on the other hand have to be registered with the system and upon a period of inactivity they will be asked to re-authenticate.

What might that look like in F#. 

```F#
type ContactInfo = //...

type GuestUser = { Contact:ContactInfo }
type AdminUser = { ID: string; PasswordHash:string; Contact:ContactInfo }
```

The data types are fundamentally different but they are both users. Onto defining a **typeclass**. Wait, what's a typeclass? Hang on. It will become clear soon.

```F#
type Authenticated<'A> =
    abstract member IsAuthenticated : 'A -> bool
```

We are defining a new "interface" (emphasis on quonte-unquote) in order to "tag" different datatypes and put them under the same umbrella. And here are the implementations of this abstract datatype.

```F#
let GuestAuthEvidence =
    { new Authenticated<_> with
        member this.IsAuthenticated(u:GuestUser) = // ...
    }
        
let AdminAuthEvidence =
    { new Authenticated<_> with
        member this.IsAuthenticated(u:AdminUser) = // ...
    }
```

Finally we can provide a generic function to simplify things at the call site

```F#
let isAuthenticated<'A> (evidence:AuthenticatedUser<'A>) user = evidence.IsAuthenticated(user)
```

And, at the call site we have...

```F#
let u:AdminUser = findSuspiciousAdmin ()
if isAuthenticated AdminAuthEvidence u then 
  // start monitoring a potential intruder
else
  // authentication must have expired expell all her activity
```

We can conclude now that a _type class_ is a mechanism to unify **distinct** types. In some sense to add behavior to types post-definition and without modifying their internals. Is is a compile-time mechanism that relies on compiler support for parametric polymorphism.

- Q: Looks very complicated. I could just create a little inheritance hierarchy quickly and be done.
    - We are making distinct datatypes do the same thing without **prematurely** abstracting over them. Sometimes the luxury can require extra ceremony.
    - MLs are featureful languages and give programmers other mechanisms to **emulate** the idea behind type classes (the gold standard being a Haskell implementation or even the upcoming at thime of writing this Scala3's **given**s or Scala2's **implicits**)
    - Other FP-friendly languages have varying degrees of support for such compile-time abstractions. For example in Haskell, Scala, and Swift achieving the above looks much simpler.
- Q: Aren't you making use of OO facilities in F# anyway to achieve this?
    - First, there are other ways to achieve a similar effect in MLs. We demonstrate one good way of this.
    - Second, by no means do I contradict myself by momentarily using interfaces. In fact, we are leaning on inheritance for its support for parametric polymorphism.  
    - Most importantly, I wan't forced to modify the existing data types. Instead, I extended them in the direction of the requirements from "the outside".

This seems to work when we are processing only one type user. Had the user types been proper objects sharing an interface we would be able to process a collection of them uniformly without, say, without type coersion. In FP we can achieve this effect with support of **algebraic datatypes** or more specifically in this case the **sum types**. We could define a _unifier_ for our users as follows:

```F#
type User = Guest of GuestUser | Admin of AdminUser
```

And, say, operate on a list of them 

```F#
let userList:List<User> = [someGuest; someAdmin; anotherGuest]
for elem in userList do
    match elem with
    | Guest g when isAuthenticated GuestAuthEvidence g -> //...
    | Admin a when isAuthenticated AdminAuthEvidence u -> //...
```

Still too complicated? Well, we could have generalized over (or unified) the user datatypes directly without the use of teh _evidence pattern_ above. Our `isAuthenticated` generic function could have been implemented as a **regular** function over the `User` **sum type**. As simple as that. In fact languages such as Typescript and Scala3 allow the declarations with even less ceremony via **nameless sum types** where we declare a function argument as being either a `GuestUser` or an `AdminUser`. This approach is made yet more ergonomic by the excellent **pattern matching** capabilities of modern programming languages.


## Interlude

The above is but a grain of sand in the bigger discussion of modern software engineering and how certain programming languages support and promote useful design patterns and architectures. We may not fully appreciate it at times but programming languages affect and even shape our thought processes and design decisions. A much longer conversation is necessary to cover this space of modern programming languages and how they support functional patterns and concepts. The thought process is rooted in _Algebraic Structures_, _Algebraic Data Types_, _Type classes_, _referential transparency_, _state isolation_, and list goes on. There is also much to be said about how type systems of certain languages support the functional mojo and the whole M-word topic. Really though, functional programmers have come up with way to many scary sounding terms because we, the programmers, do that to **every** domain we touch; and I mean **every**. One look at the terminology of any **unfamiliar** CS subdomains such as Computer Graphics and Geometric Modeling or Machine Learning would have one cringe reflexively even if the undelying concepts are straightforward. Repeating myself, FP is about **advanced** programming concepts, tools, and methods some of which take a significant effort to master. It would be naive to assume that this subdomain would escape the "laws of nature".

## The M-word, language-oriented and aspect-oriented programming

## Bottom-up vs top-down programming 

Let us escape back into the real world of everyday applications; the world where apps manage users, authorizations, and credentials.
