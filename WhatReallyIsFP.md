
"Maybe you dont really know what functional programming is"


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

Believe it or not, first-class functions, or the ability to pass functions around, does not make a functional programming language. Though first class functions are important, we aren't talking about them when were talking about 'functional' programming.

This seems to be a common belief, its understandable, I'm sure I would have said the same thing a year or two ago. This especially makes sense since most of us come from the object oriented, imperative world, where we use methods that can't be passed around.

When you search for "What is Functional Programming", most articles talk about the features of FP, like pure functions, composition, and immutable objects. 

But I don't think that helps people get to the essence of what FP. It just says "Here are a bunch of attributes that FP languages have.

So my attempt here is to get at what functional programming is, what it is all about, and why so many people these days are making such a fuss about it.

## 


Functional Programming it about using the underlying rules of mathematics to prove our programs are correct.

WAIT!! DON'T LEAVE!!

Don't be scared, its different than you might think.

I'm **not** talking about the scary parts of math, like calculus and differntial equations. 

Its also **not** about simple arithmetic, addition, subtraction, or multiplication.

Rather, its using the ideas that allow arithmetic and calculus to work. 

For example, you may remember the 'associative` property, where with addition and multiplication you are able to group variables in different ways and ensure the outcome will remain the same:

```
(a + b) + c = a + (b + c)
```

Or the 'commutative' property, where we can swap the order of items:

```
a * b = b * a
```

When people say "Functional Programming is like Math", they are actually talking about using the underlying rules of math to create reliable code. They are not talking about just addition and subtraction, or doing financial calculations. They are talking about the rules that underly all mathematics.


This branch of mathematics called Category Theory. It's a very abstract branch of pure mathematics which essentially focuses on the study of 'universal' math concepts like the associative and commutative properties. BUT WAIT, I wouldn't suggest going off to read about this though. Only a small branch of category theory is applicable to software development and though I haven't explored it, I've heard its a pretty wild jungle.

Back to it: FP is like math.

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

Assigning vaiables and using them all over the place is a very common occurence in most imperitive code bases. Many of you might see a couple improvements to this code, but generally might be ok with it. I wrote this code for a long time too. There's nothing 'wrong' with this, but when our whole program is built out of this, it creates a lot of places for bugs to creap in.

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

What if we used the substitution property:

```
c = sqrt((x + 1)^2 + (y + 3)^2)
```

Is there anything different in this code? Is the outcome different? Did ANYTHING change?? No. 

The result is exactly the same, we've just taken two expressions `x + 1` and `y + 3` and substituted them for `a` and `b` in the third expression. 

This is a critical point.

In an imperitive language, we'd 'evaluate' the result of `a` and assign the result to a memory space. Then evaluate `b` and do the same. Finally, we'd retrieve `a` and `b` and pass them into the final expression and evaluate it.

In FP languages, `let` is not 'assignment' or the creation of a memory space to store a result. Rather it is just saying that `a` represents the expression `x + 1`, and you can swap between them. This may be a bit hard to grasp at first.

In FP-land, during the compile step, we'll actually just simplify the expression or put the two expressions into the third. Key thing to note: The code is written for readability, then the compiler simplifies the expression down into something more compact.

In a language like Elm or Haskell, you might see a slightly different syntax like this:

```
let 
	a = x + 1
	b = y + 3
in
	sqrt(a^2 + b^2)
```

Don't be scared of the syntax, its the same thing as before but just less to write. Two expressions declared before the third.

What if we could use the same idea in our code?



Instead, using our first example, we could write something like:

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

Do you see how working with a series of expressions can be of value?

Though this code is incredibly difficult to read, the compiler is reading, not a human.

Because we can look at our component expressions and know they are valid, we have strong guarantees that our final expression (the compiler's output) will be valid.

Does this make sense?

If we take several small expressions that we know will work, we can then combine them into a larger expression and have high guarantees that the result will be valid.

It has taken me quite a while to get here, but I believe this is the true essence of Functional Programming.

All the other attributes, like pure functions, immutible data, and composition, help us ensure these guarantees, but the 'expression' is at the heart of it all. 


So when someone says Functional Programming is like math, realize they are saying: "The foundations of all math is based on expressions. So if we use expressions we know to be true, we can use the mathematical rules developed over the last several hundred years to prove our programs will run as we'd expect."




## Associative Property

So from here on out, all our code will use one or more expressions, so we can take advantage of the previously discussed perks.

So why are the ideas of Category Theory helpful? Lets look at the Associative property first.

Both addition and multiplication both abide by the associativity property:

```
(a + b) + c = a + (b + c)
```

Remember, addition is just a function:

```
let add = (a, b) => a + b
```

So maybe other functions can abide by the assiciativity property:

```
("a" + "b") + "c" = "a" + ("b" + "c") = "abc"
```

In this case, were using `+` to represent concatenation of strings. And as we can see, string concatentation abides by the associative rule.

Why is this useful?

Lets instead put the strings used into a list, then use the `join` function to combine them into a single list:

```
let l = ["a", "b", "c", "d"];
list.join(""); //"abcd"
```

This makes sense, right? We're going to take `"a"` and concatenate `"b"` to it and concatenate `"c"` to it, etc.

But what if we have one billion strings that we want to combine? We don't want to do that in a single loop. Instead, we might want to break down the list into two or more parts, concatenate the parts, then combine the parts:

```
//step one
("a" + "b") + ("c" + "d")
//step two
("ab") + ("cd")
//step three
"abcd"
```

Because we can use the associative property, we can first can create multiple groups ("ab" and "cd") and combine them, then we can combine the results of those expressions.

So we could either create, or use a library that had a parallel `join` function, maybe called `pjoin` for parallel join.

```
let l = ["a", "b", "c", "d", ...];
list.pjoin(""); //"abcd..."
```

We are relying on a mathematical propery thats been know for 100s or 1000s of years, the associative propery, to combine small parts into larger parts. The elegant thing about it is that we just have to rely on it and a library that enables paralellism.

Though we may not need `pjoin` in our daily work, the property is incredibly valuable because our compilers know about it and they enforce its use, just like in the mathematical expressions.

What this gives us is a gaurantee. When we are gauranteed items will combine in a consistent way we can build applications we know will work. Applications will perform the same calculation and return the same result EVERY time.

This is one of the essential characteristics of functional programs. Gauanteed results regardless of the inputs.






## Simpleifcaion

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

I'm quite impressed.



## Functions

We've now established a nice 'baseline'. Expressions are valuable. But rules on how expressions can be combined using the underlying rules of math are ver helpful.

But now, once you have an expression how do you make it reusable? Well, as you might expect we'll use a function. 

But a math function is a bit different than the methods you're used to.

In math, first, all functions will abide by the rules we have implicitly laid out previously. So there will be some 'calculation'(doesn't have to involve numbers). 

As Wikipedia states: "a function is a relation between a set of inputs and a set of permissible outputs with the property that each input is related to exactly one output."

So if we have an `isEven` function:

```
function isEven(n) {
   return n % 2 == 0;
}
```

The number `2` input, we will always get an output of `true`. And `3` will always equate to `false`.

Another way to think about it, is that an input to a function `n` could be put into a dictionary as the key, and the output could be added as its value, and you'd never have to rerun the function for the value, you'd just look it up from the dictionary.

So just like we replaced `x + 1` for `a`, we can also do the following:

```
//input                   //output
 2        (n % 2 == 0) =  true
 3        (n % 2 == 0) =  false
 4        (n % 2 == 0) =  true
 5        (n % 2 == 0) =  false
```

This process is called 'mapping' from the input(domain) to output(codomain).

When this quality of a function is true, the function can be deemed a 'pure' function.

'Pure functions' are incredibly important in functional programming, as you may have heard. But realize all of this works because of the rules identified previously.

We need expressions that return the same thing everytime (like modal `%` in this case). Without this consistency, we can't build in the gaurantees needed to ensure reliability

You may have noticed that I've switched between the use of 'method' and 'function' in several places. I'd suggest trying to start integrating this differentiation into your language. A function should always return the same output when supplied the same input. A method does not have those same gaurantees.

## Function Composition

Now we have a reliable way to put an expression into a reusable form: the function.

The function will always return the same output, based on the input supplied.

Once we have that, we can now start to combine functions. Because the lower level functions gaurantee reliability, when we combine two functions, the output should also be gauranteed.

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

Because our lower functions are reliable, we can now combine or compose functions that are reliable. 1 will always return 4. 3 will always return 6.

Though this seems simplistic, the simplest things can also be incredibly powerful due to their simplicity.





* math Composition
h∘(g∘f) = (h∘g)∘f = h∘g∘f
Composition is associative

The body of the function follows the equal sign. This terseness is often shocking to newcomers but you will quickly see that it makes perfect sense. Function definition and function call are the bread and butter of functional programming so their syntax is reduced to the bare minimum. Not only are there no parentheses around the argument list but there are no commas between arguments (you’ll see that later, when we define functions of multiple arguments).

The body of a function is always an expression — there are no statements in functions. The result of a function is this expression — here, just x.








Not just about functions. Types are incredibly important, infact the type systems in FP are quite a bit more powerful than the popular imperitive languages




https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/