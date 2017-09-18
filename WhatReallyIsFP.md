"You Don't Know Functional Programming"

Recently I've been part of several discussions where Functional Programming has come up, and it seems like there's a bit of a misconception of what FP is. Some have said: "Well you could do it the Object Oriented way or the Functional way", others have said FP is about recursion or passing functions or using pure functions or higher order functions.

To me this has really shown that there's confusion as to what functional programming is.

My objective here is to just present a different entry point into functional programming. 

When you ask people what it is, they'll usually list off some 'features', like passing functions, pure functions and maybe recursion. But just like when discussing any technology, understanding the essense and origins will better help you understand where the technology's power comes from. Without deeply understand the underlying ideas of a concept it can be difficult to make correct, informed decisions. Hopefully this will help you understand some of those concepts.

What **is** exciting is that many of the concepts utilized heavily in FP are now widely known, which means there is a base to build on. 

The following set of articles are designed to illuminate the 'essense' of functional programming and why its concepts are so powerful.

Instead of listing out a set of attributes or features of FP, we'll dive into the fundamentals of the functional programming style and features in some languages. On the way you'll see many of the features you've heard of before, but we'll approach them from a different direction, with context, to provide the 'why' in "Why should I care?".


## 

Must use Recursion to be 'functional'




functional style (i.e. the use of functions as the primary mechanism for expressing and composing computation)


Common Features
Functions are first-class entities: They can be assigned to variables, passed as arguments, or returned from other functions.
No state.
No side effects: Anything the code does except produce an output from given inputs.
Lazy evaluation: The compiler can decide when best to run a function.



What Is Functional Programming?
Functional programming uses mathematical functions to solve problems. Functions take an input and give an output, without changing the input:

f(x) -> some function of x



What is FP? Cause it isn’t just first class functions or pure functions. Javascript is **more** functional, but its not **very** functional. What do I even mean by that?
- Expressions vs Imperitive code
-Not Algebra, rather we are going to USE the rules of algebra like associativty to PROVE that our code is correct
What if we can use the rules of math to PROVE our code is correct?

```javascript
function f(){
	return "World"
}
function g(fn){
	return "Hello " + fn()
}

g(f); // "Hello World"
```

Believe it or not, first-class functions, or the ability to pass functions around, does not neccesarily differentiate  the functional style or a functional programming language. Though first class functions are important, we aren't talking about them when were talking about 'functional' programming.

This seems to be a common belief, its understandable, I'm sure I would have said the same thing a year or two ago. This especially makes sense since most of us come from the object oriented, imperative world, where we use methods that can't be passed around.

When you search for "What is Functional Programming", most articles talk about the features of FP, like pure functions, composition, and immutable objects. 

But I don't think that helps people get to the essence of what FP. It just says "Here are a bunch of attributes that FP languages have.

So my attempt here is to get at what functional programming is, what it is all about, and why so many people these days are making such a fuss about it.

## The Rules of Mathematics

Functional Programming it about using the underlying rules of mathematics to prove our programs are correct.

WAIT!! DON'T LEAVE!!

Don't be scared, its different than you might think.

We're **not** talking about the scary parts of math, like calculus and differntial equations. 

Its also **not** about simple arithmetic, addition, subtraction, or multiplication.

Rather, its using the ideas that allow arithmetic and calculus to work. 

For example, you may remember the associative property, where with addition and multiplication you are able to group variables in different ways and ensure the outcome will remain the same:

```
(a + b) + c = a + (b + c)
```

Or the 'commutative' property, where we can swap the order of items:

```
a * b = b * a
```

When people say "Functional Programming is like Math", they are actually talking about using the underlying rules of math to create reliable code. They are not talking about just addition and subtraction, or doing financial calculations. They are talking about the rules that underly all mathematics.

This branch of mathematics called Category Theory. It's a very abstract branch of pure mathematics which essentially focuses on the study of 'universal' math concepts like the associative and commutative properties. 

BUT WAIT! I wouldn't suggest going off to read about it though. Though I haven't explored it, I've heard its a pretty wild jungle and only a small set of category theory is applicable to software development. 

Back to it: FP is like Math.

We often talk about how 'new' the field of software development is. But what if we could use rules that have been developed over thousands of years [1] to prove what we are building will work? Why yes we can. And that's what we'll dig into.

[1]("The Egyptians used the commutative property of multiplication to simplify computing products.")


## Expression Oriented Programming

You've heard of Object Oriented Programming. Here's a new one for you: Expression Oriented Programming.

Whether we want to believe it or not, a lot of our programs end up looking like this:

```
var a = m + ", hello."
var b = n + " World "
a = a.toUpper();
b = b.toLower();
var p = a + " " + b;
var c = p.trim();
```

Assigning vaiables and using them all over the place is a very common occurence in most imperitive code. Many of you might see a couple improvements to this code, but generally might be ok with it. I wrote this code for a long time too. There's nothing 'wrong' with this, but when our whole program is built out of this, it creates a lot of places for bugs to creap in.

Lets instead use the 'rules' of math to improve this.

In math you have an expression like:

```
c = sqrt(a^2 + b^2)
```

Can you pick out the three functions used? Notice how its all on one line?

What if we did something like this:

```
let a = x + 1
let b = y + 3
c = sqrt(a^2 + b^2)
```

Then used the substitution property:

```
c = sqrt((x + 1)^2 + (y + 3)^2)
```

Is there anything different in this code? Is the outcome different? Did ANYTHING change? No. 

The result is exactly the same, we've just taken two expressions `x + 1` and `y + 3` and substituted them for `a` and `b` in the third expression. 

This is a critical point.

In an imperitive language, we'd 'evaluate' the result of `a` and assign the result to a memory space. Then evaluate `b` and do the same. Finally, we'd retrieve `a` and `b` and pass them into the final statement and evaluate it.

In FP languages, `let` is not 'assignment' or the creation of a memory space to store a result. Rather it is just saying that `a` represents the expression `x + 1`, and you can swap or substiture one for the other. This may be a bit hard to grasp at first but hang in there.

In FP-land, during the compile step, we'll actually just simplify the expression or put the two expressions into the third. So the code we will write will be readable, but the compiler infact will simplify the set of expressions into a single expression.

**Key thing to note**: The code is written for readability, but the compiler simplifies the expression down into something more compact.

In a language like Elm or Haskell, you might see a slightly different syntax like this:

```
let 
	a = x + 1
	b = y + 3
in
	sqrt(a^2 + b^2)
```

Don't be scared of the syntax, its the same thing as before but just less to write. Two expressions are declared before the third. When the compiler runs, it will result in only a single statement:

```
sqrt((x + 1)^2 + (y + 3)^2)
```

We know this is correct because the rules of math tell us it is correct.

What if we could use the same idea in our code?





Instead, using our first example:

```
var a = m + ", hello."
var b = n + " World "
a = a.toUpper();
b = b.toLower();
var p = a + " " + b;
var c = p.trim();
```

We could write something like:

```
let
	a = (m + ", hello.").toUpper()
	b = (n + " World ").toLower()
in 
	(a + " " + b).trim();
```

Which the compliler would then reduce down to a single expression:

```
((m + ", hello.").toUpper() + " " + (n + " World ").toLower()).trim();
```

The series of expressions allows us to create small, reusable expressions that we can group into ever larger expressions. Because we know the small expressions work, we have strong guarentees that the larger expressions will work.

Does this make sense?

If we take several small expressions that we know will work, we can then combine them into a larger expression and have high guarantees that the result will be valid.

It has taken me quite a while to get here, but I believe this is the true essence of Functional Programming.

When everything is an expression (and expressions can be composed of sub-expressions) we 


What you will see below is taking this small idea and building on it. All the other tools of functional programming, like pure functions, immutible data, and composition, grow from the simple idea of expressions. Once we have expressions, we now need to use things like pure functions, immutible data, and composition to create programs. 

But expressions are at the heart of FP. In pure functional languages virtually everything you write will be an expression of some type, or reduce down to one.


So when someone says Functional Programming is like math, realize they are saying: "The usable unit in math is an expression. So if we use expressions we know to be true, we can use the mathematical rules developed over the last several hundred years to combine these expressions into programs which are provable and operate in a reliable, consistent manner."

## Associative Property

So from here on out, all our code will use one or more expressions, so we can take advantage of the previously discussed perks.

So why are the ideas of Category Theory helpful? Lets look at the Associative property first.

Both addition and multiplication both abide by the associativity property:

```
(a + b) + c = a + (b + c)
```

And remember addition is just a function:

```
let add = (a, b) => a + b
```

So maybe other functions can also abide by the associativity property.

Lets look at concatenation of strings. Does it abide by the associativity property?

```
("a" + "b") + "c" = "a" + ("b" + "c") = "abc"
```

In this case, were using `+` to represent concatenation of strings. And as we can see, string concatentation does abides by the associative rule!

Does this seem too simplistic? Maybe, but remember many of the most powerful concepts actually derive from quite simple ideas.

Why is this useful?

Lets instead put the strings used into a list, then use the `join` function to combine them into a single list:

```
let l = ["a", "b", "c", "d"];
list.join(""); //"abcd"
```

Simple right? We'll take `"a"` and concatenate `"b"`, then concatenate `"c"`, etc.

Here's the kicker:

What if we have one billion strings that we want to combine? We don't want to do that in a single loop, it would take a long time. Instead, we might want to break down the list into two or more parts, concatenate the parts, then combine the parts:

```
//step one
("a" + "b") + ("c" + "d")
//step two
("ab") + ("cd")
//step three
"abcd"
```

Because we can use the associative property, we can first can create multiple groups ("a", "b" and "c", "d"), concatenate the groups, then just concatenate the groups.

So we could either create or use a library that had a parallel `join` function, maybe called `pjoin` for parallel join.

```
let l = ["a", "b", "c", "d", ...];
list.pjoin(""); //"abcd..."
```

The `pjoin` function would allow us to rely on a mathematical propery thats been know for 100s or 1000s of years, the associative propery, to combine small parts into larger parts. The elegant thing about it is that we just have to rely on it and a library that enables paralellism.

Though we may not need `pjoin` in our daily work, this concept is incredibly valuable because our compilers know about it and they enforce its use, just like in the mathematical expressions.

What this gives us is a gaurantee. When we are gauranteed items will combine in a consistent way we can build applications we know will work. Applications will perform the same calculation and return the same result EVERY time.

This is one of the essential characteristics of functional programs. Gauanteed results regardless of the inputs (assuming the type of inputs abide by the same rules).

## Simplification

I was recently working with ReasonML, a new syntax on top of ML/OCaml, which compiles to JavaScript (If you didnt understand what I just said, carry on...)

Maybe the coolest thing about it was the text editor capability to simplify a complex set of expressions into a simplified one.

For example, I wrote something like this:

```
function f(x){
	return function (y){
		return x + y
	}
}
```

But but when I saved, it turned that set of functions into something like this:

```
let f = x => y => x + y;
```

Though this might be a bit unfamiliar to some, it infact is exactly the same code, just with the ES6 syntax using fat arrows (`=>`).

This was pretty mind blowing. But if you think about it, it is really quite easy to turn the semi-imperitive version into a simple expression (I'm sure the implementation magic is not simple, just the concept is simple)

But this reinforced the power of expressions. When you use expressions, not only can the compiler simplify the expression, but the tooling can be built to help you too! 

I was quite impressed and excited because the tooling did heavy lifting and I didn't need to think too hard.

## Functions

We've now established a nice 'baseline'. Expressions are valuable, and rules on how expressions can be combined using the underlying rules of mathematics.

But now, once you have an expression how do you make it reusable? Well, as you might expect we'll use a function. 

But a math function is a bit different than the methods or subroutines you're used to.

In math, all functions abide by a set of rules, but we don't have to just use them for numbers.

As Wikipedia states: "a function is a relation between a set of inputs and a set of permissible outputs with the property that each input is related to exactly one output."

So if we have an `isEven` function:

```
function isEven(n) {
   return n % 2 == 0;
}
```

The number `2` input, we will always get an output of `true`. And `3` will always equate to `false`.

Another way to think about it, is that an input `n` to a function could be put into a dictionary as the key, and the output could be added as its value, and you'd never have to rerun the function for the value, you'd just look it up from the dictionary.

So we could build a dictionary like this:

```
//input                   //output
 2        (n % 2 == 0) =  true
 3        (n % 2 == 0) =  false
 4        (n % 2 == 0) =  true
 5        (n % 2 == 0) =  false
```

This process is called 'mapping' from the input(domain) to output(codomain).

When this quality of a function is true, the function can be deemed a 'pure' function.

'Pure functions' are incredibly important in functional programming, as you may have heard. 

Why is this useful?

There are many reasons, but lets cover two:

1) We can test our function does exactly what we think it does
2) We can optimize the function

Testing
When a function returns the same output based on the same input we can test that function very easily. There may be many things we want to test, but we don't have to worry about complex setups to run the test. Testing has gained incredible traction in the last decade and so pure functions allow you to say "Yes, I unit tested my code".

Optimization
Lets use the factorial problem as an example, but this applies to any CPU intensive operation.

```
funtion factorial(i){
	if (i == 0)
        return 1;  
    else
        return (i * factorial(i - 1));
}
```

If we execute `factorial(10)` the calculation will be incredibly fast. But what happens when we need to execute with large numbers? Like `factorial(10000)`. That can take a lot of processing power.

But because we know that `factorial` returns the same thing every time, we can store the result for `i` and reuse the result! This process is called memoization, or "to memoize" a function.

So if we run `factorial(10000)` we will execute the function and store the result in a dictionary. So the next time we run `factorial(10000)` we look up `10000` in our dictionary, and since it exists, return it, eliminating all of the CPU processing.

Even better... if we run `factorial(9999)` because this is recursive, when we calculated `10000` we also calculated `9999` and so already have the value stored! 

If we didn't have the consistency of a one to one mapping, which is a challenge in much of our code, we could not do this. But when we build our systems out of reliable pieces like this, we gain big advantages.

Note: You may have noticed that I've switched between the use of 'method' and 'function' in several places.  A function should always return the same output when supplied the same input. A method does not have those same gaurantees. I'd suggest trying to start integrating this differentiation into the language you use.

## Function Composition

Now we have a reliable way to put an expression into a reusable form: the function.

The function will always return the same output, based on the input supplied.

Once we have that, we can now start to combine functions in a consistent and reliable way. And because our lower level functions gaurantee reliability, when we combine two functions, the output should also be gauranteed.

Lets look at an example:

```
function f(x) {
	return x + 1
}
function g(y) {
	return y + 2
}

g(f(1)) //4
```

We know `f` will always increment by 1, and `g` will always increment by 2. So if we 'compose' or combine `f` and `g`, we know we will get this type of outcome:

```
//input		//output
1			g(f(1)) = 4
2			g(f(2)) = 5
3			g(f(3)) = 6
4			g(f(4)) = 7
```

Because our lower functions are reliable, we can now combine or 'compose' functions that are reliable. `1` will always return `4`. `3` will always return `6`.

Again, this may seems simplistic, but the simplest things, you will find, are sometimes the most powerful because of their simplicity. Simplicity often `==` reusability.

Lets take a look at ReactJS to prove this out. With the ReactJS library, you can combine two functions together to create a third.

```
let subcomponent = name => `<div>Name: <span>${name}</span></div>`
let component = sub => `<div>Person: ${sub}</div>`
let topLevel = name => component(subcomponent(name))
```

Here we can see that we've combined a subcomponent with a component. This code is:

* Reusable
* Testable
* Readable

When doing something like `component(sub(name))` this can become hard to read very quickly though, and so we use a `compose` function to help us:

```
let topLevel = name => compose(component, subcomponent(name))
```

But then a new requirement comes along, and we need to accept multiple names. Instead of changing everything, we just need to make a couple tweaks:

```
let subcomponent = name => `<div>Name: <span>${name}</span></div>`
let component = subs => `<div>${subs.length>1?'People':'Person'}: ${subs}</div>`

let topLevel = names => compose(component, names.map, subcomponent)
//same as:
let topLevel = names => component(names.map(subcomponent))
```

So we needed to change the `component` to handle multiple names, and we passed the `list.map` function as the second argument to `compose`. 

Though this may look a bit unfamiliar, we haven't significantly increased the complexity of the code. There are still just three lines of code, no looping is involved, and we just need to update our unit tests.

Chaining these functions together is quite simple and reusable. It creates a quite powerful abstraction that can be used over and over without impacting other functionality.


I won't go into it here... but function composition is also associative. Yes thats right! We can not only use the associative proerty at the expression level, but also the function level. Why do you think that is? If you can answer that, maybe you have learned something today...




## Conclusion



For many people this may not have been very interesting. Sorry.

For anyone that made it this far, thanks. Hopefully you got something out of it.

I hope you now have a slightly better appreciation for the low level rules or tools that govern how functional programming works, and how those tools can be used to build on each other. 

Some of you might ask: "But how do we really use those tools to create software".

My answer is: Start using expressions instead of statements. Start combining expressions into larger ones. Start by using functions instead of methods.

I cannot show you the path, you have to find it. But starting to use the tools we've discussed will start opening doors, and you'll be able to answer that question your self.


You don't have to understand category theory to understand functional programming. But if you're interested in learning more, check out "Category Theory for Programmers". Its a great intro without having to read shit like:

> Bicategories are a weaker notion of 2-dimensional categories in which the composition of morphisms is not strictly associative, but only associative "up to" an isomorphism.

https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/




* math Composition
h∘(g∘f) = (h∘g)∘f = h∘g∘f
Composition is associative

The body of the function follows the equal sign. This terseness is often shocking to newcomers but you will quickly see that it makes perfect sense. Function definition and function call are the bread and butter of functional programming so their syntax is reduced to the bare minimum. Not only are there no parentheses around the argument list but there are no commas between arguments (you’ll see that later, when we define functions of multiple arguments).

The body of a function is always an expression — there are no statements in functions. The result of a function is this expression — here, just x.








Not just about functions. Types are incredibly important, infact the type systems in FP are quite a bit more powerful than the popular imperitive languages




