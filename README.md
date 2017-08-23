# Pony_Documentation

### What is Pony?
Pony is an open-source, object-oriented, actor-model, capabilities-secure, high-performance programming language.

### Why is it different?
- **Pony is type safe**: Really type safe. There’s a mathematical proof and everything.
- **Pony is memory safe:** There are no dangling pointers and no buffer overruns. The language doesn’t even have the concept of null!
- **Exception-Safe:** There are no runtime exceptions. All exceptions have defined semantics, and they are always caught.
- **Data-race Free:** Pony doesn’t have locks or atomic operations or anything like that. Instead, the type system ensures at compile time that your concurrent program can never have data races. So you can write highly concurrent code and never get it wrong.
- **Deadlock-Free**: This one is easy because Pony has no locks at all! So they definitely don’t deadlock, because they don’t exist!
- **Native Code**: Pony is an ahead-of-time (AOT) compiled language. There is no interpreter or virtual machine.
- **Compatible with C#**: Pony programs can natively call C libraries. Our compiler is able to generate a C-header file for Pony libraries. Consequently, C/C++ programs can natively call Pony programs!


### Your first program
Let's learm to write a program to print "Hello, world!". First, create a directory called `helloworld`:
```
$ mkdir helloworld
$ cd helloworld
```
Does the name of the directory matter? Yes, it does. It's the name of your program! When your program is compiled, the resulting executable binary will have the same name as the directory your program lives in.
Then, create a file in that directory called `main.pony`.

Does the name of the file matter? Not to the compiler, no. Pony doesn't care about filenames other than that they end in .pony. But it might matter to you! By giving files good names, it can be easier to find the code you're looking for later.

In your file, put the following code:
```pony
actor Main
  new create(env: Env) =>
    env.out.print("Hello, world!")
```
    
#### Compiling your program

If you are in the same directory as your program, you can just do:
``` $ ponyc ```
That tells the Pony compiler that your current working directory contains your source code, and to please compile it. If your source code is in some other directory, you can tell ponyc where it is:

``` 
$ ponyc path/to/my/code 
```

#### Running your program
Now we can run the program:
```
$ ./helloworld
Hello, world! 
```
#### How it works
Let's go through that line by line.

##### Line 1

```pony
actor Main
```

This is a __type declaration__. The keyword `actor` means we are going to define an actor, which is a bit like a class in Python, Java, C#, C++, etc. Pony has classes too, which we'll see later.

The difference between an actor and a class is that an actor can have __asynchronous__ methods, called __behaviours__. We'll talk more about that later.

A Pony program has to have a `Main` actor. It's kind of like the `main` function in C or C++, or the `main` method in Java, or the `Main` method in C#. It's where the action starts.

##### Line 2

```pony
  new create(env: Env) =>
```

This is a __constructor__. The keyword `new` means it's a function that creates a new instance of the type. In this case, it creates a new __Main__.

Unlike other languages, constructors in Pony have names. That means there can be more than one way to construct an instance of a type. In this case, the name of the constructor is `create`.

The parameters of a function come next. In this case, our constructor has a single parameter called `env` that is of the type `Env`.

In Pony, the type of something always comes after its name and is separated by a colon. In C, C++, Java or C#, you might say `Env env`, but we do it the other way around (like Go, Pascal, and a bunch of other languages).

It turns out, our `Main` actor __has__ to have a constructor called `create` that takes a single parameter of type `Env`. That's how all programs start! So the beginning of your program is essentially the body of that constructor.

__Wait, what's the body?__ It's the code that comes after the `=>`.

##### Line 3

```pony
    env.out.print("Hello, world!")
```

This is your program! What the heck is it doing?

In Pony, a dot is either a field access or a method call, much like other languages. If the name after the dot has parentheses after it, it's a method call. Otherwise, it's a field access.

So here, we start with a reference to `env`. We then look up the field `out` on our object `env`. As it happens, that field represents __stdout__, i.e. usually it means printing to your console. Then, we call the `print` method on `env.out`. The stuff inside the parentheses are the arguments to the function. In this case, we are passing a __string literal__, i.e. the stuff in double quotes.

In Pony, string literals can be in double quotes, in which case they follow C/C++ style escaping (using stuff like \n), or they can be in "triple double" quotes, like a Python triple-quoted string, in which case they are considered raw data.

__What's an Env, anyway?__ It's the "environment" your program was invoked with. That means it has command line arguments, environment variables, __stdin__, __stdout__, and __stderr__. Pony has no global variables, so these things are explicitly passed to your program.

##### That's it!

Really, that's it. The program begins by creating a `Main` actor, and in the constructor, we print "Hello, world!" to __stdout__. Next, we'll start diving into the Pony type system.

### The Pony Type System at a Glance

Pony is a _statically typed_ language, like Java, C#, C++, and many others. This means the compiler knows the type of everything in your program. This is different from _dynamically typed_ languages, such as Python, Lua, JavaScript, and Ruby.

When you use a _statically typed_ language, a variable has a type. That is, it can only point to objects of a certain type (although in Pony, a type can actually be a collection of types, as we'll see later). If you have an `x` that expects to point to an integer, you can't assign a string to it. Your compiler complains, and it complains __before__ you ever try to run your program.

# Classes

Just like other object-oriented languages, Pony has __classes__. A class is declared with the keyword `class`, and it has to have a name that starts with a capital letter, like this:

```pony
class Wombat
```

__Do all types start with a capital letter?__ Yes! And nothing else starts with a capital letter. So when you see a name in Pony code, you will instantly know whether it's a type or not.

## What goes in a class?

A class is composed of:

1. Fields.
2. Constructors.
3. Functions.

### Fields

These are just like fields in C structs or fields in classes in C++, C#, Java, Python, Ruby, or basically any language, really. There are three kinds of fields: `var`, `let`, and `embed` fields. A `var` field can be assigned to over and over again, but a `let` field is assigned to in the constructor and never again. Embed fields will be covered in more detail in the documentation on [variables](../expressions/variables.md).

```pony
class Wombat
  let name: String
  var _hunger_level: U64
```

Here, a `Wombat` has a `name`, which is a `String`, and a `_hunger_level`, which is a `U64` (an unsigned 64-bit integer).

__What does the leading underscore mean?__ It means something is __private__. A __private__ field can only be accessed by code in the same type. A __private__ constructor, function, or behaviour can only be accessed by code in the same package. We'll talk more about packages later.

### Constructors

Pony constructors have names. Other than that, they are just like constructors in other languages. They can have parameters, and they always return a new instance of the type. Since they have names, you can have more than one constructor for a type.

Constructors are introduced with the __new__ keyword.

```pony
class Wombat
  let name: String
  var _hunger_level: U64

  new create(name': String) =>
    name = name'
    _hunger_level = 0

  new hungry(name': String, hunger': U64) =>
    name = name'
    _hunger_level = hunger'
```

Here, we have two constructors, one that creates a `Wombat` that isn't hungry, and another that creates a `Wombat` that might be hungry or might not. Unlike some other languages that differentiate between constructors with method overloading, Pony won't presume to know which alternate constructor to invoke based on the arity and type of your arguments. To choose a constructor, invoke it like a method with the `.` syntax:

```pony
let defaultWombat = Wombat("Fantastibat") // Invokes the create method by default
let hungryWombat = Wombat.hungry("Nomsbat", 12) // Invokes the hungry method
```

__What's with the single quote thing, i.e. name'?__ You can use single quotes in parameter and local variable names. In mathematics, it's called a _prime_, and it's used to say "another one of these, but not the same one". Basically, it's just convenient.

Every constructor has to set every field in an object. If it doesn't, the compiler will give you an error. Since there is no `null` in Pony, we can't do what Java, C# and many other languages do and just assign either `null` or zero to every field before the constructor runs, and since we don't want random crashes, we don't leave fields undefined (unlike C or C++).

Sometimes it's convenient to set a field the same way for all constructors.

```pony
class Wombat
  let name: String
  var _hunger_level: U64
  var _thirst_level: U64 = 1

  new create(name': String) =>
    name = name'
    _hunger_level = 0

  new hungry(name': String, hunger': U64) =>
    name = name'
    _hunger_level = hunger'
```

Here, every `Wombat` begins a little bit thirsty, regardless of which constructor is called.

### Functions

Functions in Pony are like methods in Java, C#, C++, Ruby, Python, or pretty much any other object oriented language. They are introduced with the keyword `fun`. They can have parameters like constructors do, and they can also have a result type (if no result type is given, it defaults to `None`).

```pony
class Wombat
  let name: String
  var _hunger_level: U64
  var _thirst_level: U64 = 1

  new create(name': String) =>
    name = name'
    _hunger_level = 0

  new hungry(name': String, hunger': U64) =>
    name = name'
    _hunger_level = hunger'

  fun hunger(): U64 => _hunger_level

  fun ref set_hunger(to: U64 = 0): U64 => _hunger_level = to
```

The first function, `hunger`, is pretty straight forward. It has a result type of `U64`, and it returns `_hunger_level`, which is a `U64`. The only thing a bit different here is that no `return` keyword is used. This is because the result of a function is the result of the last expression in the function, in this case, the value of `_hunger_level`.

__Is there a `return` keyword in Pony?__ Yes. It's used to return "early" from a function, i.e. to return something right away and not keep running until the last expression.

The second function, `set_hunger`, introduces a _bunch_ of new concepts all at once. Let's go through them one by one.

* The `ref` keyword right after `fun`

This is a __reference capability__. In this case, it means the _receiver_, i.e. the object on which the `set_hunger` function is being called, has to be a `ref` type. A `ref` type is a __reference type__, meaning that the object is __mutable__. We need this because we are writing a new value to the `_hunger_level` field.

__What's the receiver reference capability of the `hunger` method?__ The default receiver reference capability if none is specified is `box`, which means "I need to be able to read from this, but I won't write to it".

__What would happen if we left the `ref` keyword off the `set_hunger` method?__ The compiler would give you an error. It would see you were trying to modify a field and complain about it.

* The `= 0` after the parameter `to`

This is a __default argument__. It means that if you don't include that argument at the call site, you will get the default argument. In this case, `to` will be zero if you don't specify it.

* What does the function return?

It returns the _old_ value of `_hunger_level`.

__Wait, seriously? The _old_ value?__ Yes. In Pony, assignment is an expression rather than a statement. That means it has a result. This is true of a lot of languages, but they tend to return the _new_ value. In other words, given `a = b`, in most languages, the value of that is the value of `b`. But in Pony, the value of that is the _old_ value of `a`.

__...why?__ It's called a "destructive read", and it lets you do awesome things with a capabilities-secure type system. We'll talk about that later. For now, we'll just mention that you can also use it to implement a _swap_ operation. In most languages, to swap the values of `a` and `b` you need to do something like:

```pony
var temp = a
a = b
b = temp
```

In Pony, you can just do:

```pony
a = b = a
```

### Finalisers

Finalisers are special functions. They are named `_final`, take no parameters and have a receiver reference capability of `box`. In other words, the definition of a finaliser must be `fun _final()`.

The finaliser of an object is called before the object is collected by the GC. Functions may still be called on an object after its finalisation, but only from within another finaliser. Messages cannot be sent from within a finaliser.

Finalisers are usually used to clean up resources allocated in C code, like file handles, network sockets, etc.

## What about inheritance?

In some object-oriented languages, a type can _inherit_ from another type, like how in Java something can __extend__ something else. Pony doesn't do that. Instead, Pony prefers _composition_ to _inheritance_. In other words, instead of getting code reuse by saying something __is__ something else, you get it by saying something __has__ something else.

On the other hand, Pony has a powerful __trait__ system (similar to Java 8 interfaces that can have default implementations) and a powerful __interface__ system (similar to Go interfaces, i.e. structurally typed).

We'll talk about all that stuff in detail later.

## Naming rules

By now it shouldn't be very surprising to learn that Pony is written in __ASCII__. [ASCII](https://en.wikipedia.org/wiki/ASCII) is a standard text encoding that uses English characters and symbols, and almost every programming language in existence defines source code as a subset of it.

A Pony type, whether it's a class, actor, trait, interface, primitive, or type alias, must start with an uppercase letter. After an underscore for private or special _methods_ (behaviors, constructors, and functions), any method or variable, including parameters and fields, must start with a lowercase letter. In all cases underscores in a row or at the end of a name are not allowed, but otherwise, any combination of letters and numbers is legal.

In fact, numbers may use single underscores inside as a separator too! But only valid variable names can end in primes.

# Primitives

A __primitive__ is similar to a __class__, but there are two critical differences:

1. A __primitive__ has no fields.
2. There is only one instance of a user-defined __primitive__.

Having no fields means primitives are never mutable. Having a single instance means that if your code calls a constructor on a __primitive__ type, it always gets the same result back (except for built-in "machine word" primitives, covered below).

## What can you use a __primitive__ for?

There are three main uses of primitives (four, if you count built-in "machine word" primitives).

1. As a "marker value". For example, Pony often uses the __primitive__ `None` to indicate that something has "no value". Of course, it _does_ have a value, so that you can check what it is, and the value is the single instance of `None`.
2. As an "enumeration" type. By having a __union__ of __primitive__ types, you can have a type-safe enumeration. We'll cover __union__ types later.
3. As a "collection of functions". Since primitives can have functions, you can group functions together in a primitive type. You can see this in the standard library, where path handling functions are grouped in the __primitive__ `Path`, for example.

Primitives are quite powerful, particularly as enumerations. Unlike enumerations in other languages, each "value" in the enumeration is a complete type, which makes attaching data and functionality to enumeration values easy.

## Built-in primitive types

The __primitive__ keyword is also used to introduce certain built-in "machine word" types. Other than having a value associated with them, these work like user-defined primitives. These are:

* __Bool__. This is a 1-bit value that is either `true` or `false`.
* __ISize, ILong, I8, I16, I32, I64, I128__. Signed integers of various widths.
* __USize, ULong, U8, U16, U32, U64, U128__. Unsigned integers of various widths.
* __F32, F64__. Floating point numbers of various widths.

__ISize/USize__ correspond to the bit width of the native type `size_t`, which varies by platform. __ILong/ULong__ similarly correspond to the bit width of the native type `long`, which also varies by platform. The bit width of a native `int` is the same across all the platforms that Pony supports, and you can use __I32/U32__ for this.

## Primitive initialisation and finalisation

Primitives can have two special functions, `_init` and `_final`. `_init` is called before any actor starts. `_final` is called after all actors have terminated. The two functions take no parameter. The `_init` and `_final` functions for different primitives always run sequentially.

A common use case for this is initialising and cleaning up C libraries without risking untimely use by an actor.

# Actors

An __actor__ is similar to a __class__, but with one critical difference: an actor can have __behaviours__.

## Behaviours

A __behaviour__ is like a __function__, except that functions are _synchronous_ and behaviours are _asynchronous_. In other words, when you call a function, the body of the function is executed immediately, and the result of the call is the result of the body of the function. This is just like method invocation in any other object-oriented language.

But when you call a behaviour, the body is __not__ executed immediately. Instead, the body of the behaviour will execute at some indeterminate time in the future.

A behaviour looks like a function, but instead of being introduced with the keyword `fun`, it is introduced with the keyword `be`.

Like a function, a behaviour can have parameters. Unlike a function, it doesn't have a receiver capability (a behaviour can be called on a receiver of any capability) and you can't specify a return type.

__So what does a behaviour return?__ Behaviours always return `None`, like a function without explicit result type, because they can't return something they calculate (since they haven't run yet).

```pony
actor Aardvark
  let name: String
  var _hunger_level: U64 = 0

  new create(name': String) =>
    name = name'

  be eat(amount: U64) =>
    _hunger_level = _hunger_level - amount.min(_hunger_level)
```

Here we have an `Aardvark` that can eat asynchronously. Clever Aardvark.

## Concurrent

Since behaviours are asynchronous, it's ok to run the body of a bunch of behaviours at the same time. This is exactly what Pony does. The Pony runtime has its own scheduler, which by default has a number of threads equal to the number of CPU cores on your machine. Each scheduler thread can be executing an actor behaviour at any given time, so Pony programs are naturally concurrent.

## Sequential

Actors themselves, however, are sequential. That is, each actor will only execute one behaviour at a time. This means all the code in an actor can be written without caring about concurrency: no need for locks or semaphores or anything like that.

When you're writing Pony code, it's nice to think of actors not as a unit of parallelism, but as a unit of sequentiality. That is, an actor should do only what _has_ to be done sequentially. Anything else can be broken out into another actor, making it automatically parallel.

## Why is this safe?

Because of Pony's __capabilities secure type system__. We've mentioned reference capabilities briefly before when talking about function receiver reference capabilities. The short version is that they are annotations on a type that make all this parallelism safe without any runtime overhead.

We will cover reference capabilities in depth later.

## Actors are cheap

If you've done concurrent programming before, you'll know that threads can be expensive. Context switches can cause problems, each thread needs a stack (which can be a lot of memory), and you need lots of locks and other mechanisms to write thread-safe code.

But actors are cheap. Really cheap. The extra cost of an actor, as opposed to an object, is about 256 bytes of memory. Bytes, not kilobytes! And there are no locks and no context switches. An actor that isn't executing consumes no resources other than the few extra bytes of memory.

It's pretty normal to write a Pony program that uses hundreds of thousands of actors.

## Actor finalisers

Like classes, actors can have finalisers. The finaliser definition is the same (`fun _final()`). All guarantees and restrictions for a class finaliser are also valid for an actor finaliser. In addition, an actor will not receive any further message after its finaliser is called.

# Traits and Interfaces

Like other object-oriented languages, Pony has __subtyping__. That is, some types serve as _categories_ that other types can be members of.

There are two kinds of __subtyping__ in programming languages: __nominal__ and __structural__. They're subtly different, and most programming languages only have one or the other. Pony has both!

## Nominal subtyping

This kind of subtyping is called __nominal__ because it is all about __names__.

If you've done object-oriented programming before, you may have seen a lot of discussion about _single inheritance_, _multiple inheritance_, _mixins_, _traits_, and similar concepts. These are all examples of __nominal subtyping__.

The core idea is that you have a type that declares it has a relationship to some category type. In Java, for example, a __class__ (a concrete type) can __implement__ an __interface__ (a category type). In Java, this means the class is now in the category that the interface represents. The compiler will check that the class actually provides everything it needs to.

## Traits: nominal subtyping

Pony has nominal subtyping, using __traits__. A __trait__ looks a bit like a __class__, but it uses the keyword `trait` and it can't have any fields.

```pony
trait Named
  fun name(): String => "Bob"

class Bob is Named
```

Here, we have a trait `Named` that has a single function `name` that returns a String. It also provides a default implementation of `name` that returns the string literal "Bob".

We also have a class `Bob` that says it `is Named`. This means `Bob` is in the `Named` category. In Pony, we say _Bob provides Named_, or sometimes simply _Bob is Named_.

Since `Bob` doesn't have its own `name` function, it uses the one from the trait. If the trait's function didn't have a default implementation, the compiler would complain that `Bob` had no implementation of `name`.

```pony
class Larry
  fun name(): String => "Larry"
```

Here, we have a class `Larry` that has a `name` function with the same signature. But `Larry` does __not__ provide `Named`!

__Wait, why not?__ Because `Larry` doesn't say it `is Named`. Remember, traits are __nominal__: a type that wants to provide a trait has to explicitly declare that it does. And `Larry` doesn't.

## Structural subtyping

There's another kind of subtyping, where the name doesn't matter. It's called __structural subtyping__, which means that it's all about how a type is built, and nothing to do with names.

A concrete type is a member of a structural category if it happens to have all the needed elements, no matter what it happens to be called.

If you've used Go, you'll recognise that Go interfaces are structural types.

## Interfaces: structural subtyping

Pony has structural subtyping too, using __interfaces__. Interfaces look like traits, but they use the keyword `interface`.

```pony
interface HasName
  fun name(): String
```

Here, `HasName` looks a lot like `Named`, except it's an interface instead of a trait. This means both `Bob` and `Larry` provide `HasName`! The programmers that wrote `Bob` and `Larry` don't even have to be aware that `HasName` exists.

Pony interfaces can have functions with default implementations as well. A type will only pick those up if it explicitly declares that it `is` that interface.

__Should I use traits or interfaces in my own code?__ Both! Interfaces are more flexible, so if you're not sure what you want, use an interface. But traits are a powerful tool as well: they stop _accidental subtyping_.

# Type Aliases

A __type alias__ is just a way to give a different name to a type. This may sound a bit silly: after all, types already have names! However, Pony can express some complicated types, and it can be convenient to have a short way to talk about them.

We'll give a couple examples of using type aliases, just to get the feel of them.

## Enumerations

One way to use type aliases is to express an enumeration. For example, imagine we want to say something must either be Red, Blue or Green. We could write something like this:

```pony
primitive Red
primitive Blue
primitive Green

type Colour is (Red | Blue | Green)
```

There are two new concepts in there. The first is the type alias, introduced with the keyword `type`. It just means that the name that comes after `type` will be translated by the compiler to the type that comes after `is`.

The second new concept is the type that comes after `is`. It's not a single type! Instead, it's a __union__ type. You can read the `|` symbol as __or__ in this context, so the type is "Red or Blue or Green".

A union type is a form of _closed world_ type. That is, it says every type that can possibly be a member of it. In contrast, object-oriented subtyping is usually _open world_, e.g. in Java, an interface can be implemented by any number of classes.

You can also declare constants like in C or Go like this,
```pony
primitive Red    fun apply(): U32 => 0xFF0000FF
primitive Green  fun apply(): U32 => 0x00FF00FF
primitive Blue   fun apply(): U32 => 0x0000FFFF

type Colour is (Red | Blue | Green)
```

or namespace them like this
```pony
primitive Colours
  fun red(): U32 => 0xFF0000FF
  fun green(): U32 => 0x00FF00FF
```

You might also want to iterate over the enum like this to print its name for debugging purposes
```pony
primitive ColourList
  fun tag apply(): Array[Colour] =>
    [Red; Green; Blue]

for colour in ColourList().values() do
end
```

## Complex types

If a type is complicated, it can be nice to give it a mnemonic name. For example, if we want to say that a type must implement more than one interface, we could say:

```pony
interface HasName
  fun name(): String

interface HasAge
  fun age(): U32

interface HasAddress
  fun address(): String

type Person is (HasName & HasAge & HasAddress)
```

This use of complex types applies to traits, not just interfaces:

```pony
trait HasName
  fun name(): String => "Bob"

trait HasAge
  fun age(): U32 => 42

trait HasAddress
  fun address(): String => "3 Abbey Road"

type Person is (HasName & HasAge & HasAddress)
```

There's another new concept here: the type has a `&` in it. This is similar to the `|` of a __union__ type: it means this is an __intersection__ type. That is, it's something that must be _all_ of `HasName`, `HasAge` _and_ `HasAddress`.

But the use of `type` here is exactly the same as the enumeration example above, it's just providing a name for a type that is otherwise a bit tedious to type out over and over.

Another example, this time from the standard library, is `SetIs`. Here's the actual definition:

```pony
type SetIs[A] is HashSet[A, HashIs[A!]]
```

Again there's something new here. After the name `SetIs` comes the name `A` in square brackets. That's because `SetIs` is a __generic type__. That is, you can give a `SetIs` another type as a parameter, to make specific kinds of set. If you've used Java or C#, this will be pretty familiar. If you've used C++, the equivalent concept is templates, but they work quite differently.

And again the use of `type` just provides a more convenient way to refer to the type we're aliasing:

```pony
HashSet[A, HashIs[A!]]
```

That's another __generic type__. It means a `SetIs` is really a kind of `HashSet`. Another concept has snuck in, which is `!` types. This is a type that is the __alias__ of another type. That's tricky stuff that you only need when writing complex generic types, so we'll leave it for later.

One more example, again from the standard library, is the `Map` type that gets used a lot. It's actually a type alias. Here's the real definition of `Map`:

```pony
type Map[K: (Hashable box & Comparable[K] box), V] is HashMap[K, V, HashEq[K]]
```

Unlike our previous example, the first type parameter, `K`, has a type associated with it. This is a __constraint__, which means when you parameterise a `Map`, the type you pass for `K` must be a subtype of the constraint.

Also, notice that `box` appears in the type. This is a __reference capability__. It means there is a certain class of operations we need to be able to do with a `K`. We'll cover this in more detail later.

Just like our other examples, all this really means is that `Map` is really a kind of `HashMap`.

## Other stuff

Type aliases get used for a lot of things, but this gives you the general idea. Just remember that a type alias is always a convenience: you could replace every use of the type alias with the full type after the `is`.

In fact, that's exactly what the compiler does.

# Type Expressions

The types we've talked about so far can also be combined in __type expressions__. If you're used to object-oriented programming, you may not have seen these before, but they are common in functional programming. A __type expression__ is also called an __algebraic data type__.

There are three kinds of type expression: __tuples__, __unions__, and __intersections__.

## Tuples

A __tuple__ type is a sequence of types. For example, if we wanted something that was a `String` followed by a `U64`, we would write this:

```pony
var x: (String, U64)
x = ("hi", 3)
x = ("bye", 7)
```

All type expressions are written in parentheses, and the elements of a tuple are separated by a comma. We can also destructure a tuple using assignment:

```pony
(var y, var z) = x
```

Or we can access the elements of a tuple directly:

```pony
var y = x._1
var z = x._2
```

Note that there's no way to assign to an element of a tuple. Instead, you can just reassign the entire tuple, like this:

```pony
x = ("wombat", x._2)
```

__Why use a tuple instead of a class?__ Tuples are a way to express a collection of values that doesn't have any associated code or expected behaviour. Basically, if you just need a quick collection of things, maybe to return more than one value from a function, for example, you can use a tuple.

## Unions

A __union__ type is written like a __tuple__, but it uses a `|` (pronounced "or" when reading the type) instead of a `,` between its elements. Where a tuple represents a collection of values, a union represents a _single_ value that can be any of the specified types.

Unions can be used for tons of stuff that require multiple concepts in other languages. For example, optional values, enumerations, marker values, and more.

```pony
var x: (String | None)
```

Here we have an example of using a union to express an optional type, where `x` might be a `String`, but it also might be `None`.

## Intersections

An __intersection__ uses a `&` (pronounced "and" when reading the type) between its elements. It represents the exact opposite of a union: it is a _single_ value that is _all_ of the specified types, at the same time!

This can be very useful for combining traits or interfaces, for example. Here's something from the standard library:

```pony
type Map[K: (Hashable box & Comparable[K] box), V] is HashMap[K, V, HashEq[K]]
```

That's a fairly complex type alias, but let's look at the constraint of `K`. It's `(Hashable box & Comparable[K] box)`, which means `K` is `Hashable` _and_ it is `Comparable[K]`, at the same time.

## Combining type expressions

Type expressions can be combined into more complex types. Here's another example from the standard library:

```pony
var _array: Array[((K, V) | _MapEmpty | _MapDeleted)]
```

Here we have an array where each element is either a tuple of `(K, V)` or a `_MapEmpty` or a `_MapDeleted`.

Because every type expression has parentheses around it, they are actually easy to read once you get the hang of it. However, if you use a complex type expression often, it can be nice to provide a type alias for it.

```pony
type Number is (Signed | Unsigned | Float)

type Signed is (I8 | I16 | I32 | I64 | I128)

type Unsigned is (U8 | U16 | U32 | U64 | U128)

type Float is (F32 | F64)
```

Those are all type aliases used by the standard library.

__Is `Number` a type alias for a type expression that contains other type aliases?__ Yes! Fun, and convenient.

# Variables

Like most other programming languages Pony allows you to store data in variables. There are a few different kinds of variables which have different lifetimes and are used for slightly different purposes.

## Local variables

Local variables in Pony work very much as they do in other languages, allowing you to store temporary values while you perform calculations. Local variables live within a chunk of code (they are _local_ to that chunk) and are created every time that code chunk executes and disposed of when it completes.

To define a local variable the `var` keyword is used (`let` can also be used, but we'll get to that later). Right after the `var` comes the variable's name, and then you can (optionally) put a `:` followed by the variable's type. For example:

```pony
var x: String = "Hello"
```

Here, we're assigning the __string literal__ `"Hello"` to `x`.

You don't have to give a value to the variable when you define it: you can assign one later if you prefer. If you try to read the value from a variable before you've assigned one, the compiler will complain instead of allowing the dreaded _uninitialised variable_ bug.

Every variable has a type, but you don't have to specify it in the declaration if you provide an initial value. The compiler will automatically use the type of the initial value of the variable.

The following definitions of `x`, `y` and `z` are all effectively identical.

```pony
var x: String = "Hello"

var y = "Hello"

var z: String
z = "Hello"
```

__Can I miss out both the type and initial value for a variable?__ No. The compiler will complain that it can't figure out a type for that variable.

All local variable names start with a lowercase letter. If you want to you can end them with a _prime_ `'` (or more than one) which is useful when you need a second variable with almost the same meaning as the first. For example, you might have one variable called `time` and another called `time'`.

The chunk of code that a variable lives in is known as its __scope__. Exactly what its scope is depends on where it is defined. For example, the scope of a variable defined within the `then` expression of an `if` statement is that `then` expression. We haven't looked at `if` statements yet, but they're very similar to every other language.

```pony
if a > b then
  var x = "a is bigger"
  env.out.print(x)  // OK
end

env.out.print(x)  // Illegal
```

Variables only exist from when they are defined until the end of the current scope. For our variable `x` this is the `end` at the end of the then expression: after that, it cannot be used.

## Var vs. let

Local variables are declared with either a `var` or a `let`. Using `var` means the variable can be assigned and reassigned as many times as you like. Using `let` means the variable can only be assigned once.

```pony
var x: U32 = 3
let y: U32 = 4
x = 5  // OK
y = 6  // Error, y is let
```

You never have to declare variables as `let`, but if you know you're never going to change a variable then using `let` is a good way to catch errors. It can also serve as a useful comment, indicating the value is not meant to be changed.

## Fields

In Pony, fields are variables that live within objects. They work like fields in other object-oriented languages.

Fields have the same lifetime as the object they're in, rather than being scoped. They are set up by the object constructor and disposed of along with the object.

If the name of a field starts with `_`, it's __private__. That means only the type the field is in can have code that reads or writes that field. Otherwise, the field is __public__ and can be read or written from anywhere.

Just like local variables, fields can be `var` or `let`. They can also have an initial value assigned in their definition, just like local variables, or they can be given their initial value in a constructor.

__Can fields come after the constructor?__ No. To keep Pony's grammar unambiguous, only type aliases are allowed between an `actor Name`, `object is Trait`, etc. and a field definition. In any case, it's good style to make such variables easily visible to the programmer because fields are accessible from _any_ method of the type they're in.

Unlike local variables, some types of fields can be declared using `embed`. Specifically, only classes or structs can be embedded - interfaces, traits, primitives and numeric types cannot. A field declared using `embed` is similar to one declared using `let`, but at the implementation level, the memory for the embedded class is laid out directly within the outer class. Contrast this with `let` or `var`, where the implementation uses pointers to reference the field class. Embedded fields can be passed to other functions in exactly the same way as `let` or `var` fields. Embedded fields must be initialised from a constructor expression.

__Why would I use `embed`?__ `embed` avoids a pointer indirection when accessing a field and a separate memory allocation when creating that field. By default, it is advised to use `embed` if possible. However, since an embedded field is allocated alongside its parent object, exterior references to the field forbids garbage collection of the parent, which can result in higher memory usage if a field outlives its parent. Use `let` if this is a concern for you.

## Globals

Some programming languages have __global variables__ that can be accessed from anywhere in the code. What a bad idea! Pony doesn't have global variables at all.

## Shadowing

Some programming languages let you declare a variable with the same name as an existing variable, and then there are rules about which one you get. This is called _shadowing_, and it's a source of bugs. If you accidentally shadow a variable in Pony, the compiler will complain.

If you need a variable with _nearly_ the same name, you can use a prime `'`.

# Infix Operators

Infix operators take two operands and are written between those operands. Arithmetic and comparison operators are the most common:

```pony
1 + 2
a < b
```

Pony has pretty much the same set of infix operators as other languages.

## Precedence

When using infix operators in complex expressions a key question is the __precedence__, i.e. which operator is evaluated first. Given this expression:

```pony
1 + 2 * 3
```

We will get a value of 9 if we evaluate the addition first and 7 if we evaluate the multiplication first. In mathematics, there are rules about the order in which to evaluate operators and most programming languages follow this approach.

The problem with this is that the programmer has to remember the order and people aren't very good at things like that. Most people will remember to do multiplication before addition, but what about left bit shifting versus bitwise and? Sometimes people misremember (or guess wrong) and that leads to bugs. Worse, those bugs are often very hard to spot.

Pony takes a different approach and outlaws infix precedence. Any expression where more than one infix operator is used __must__ use parentheses to remove the ambiguity. If you fail to do this the compiler will complain.

This means that the example above is illegal in Pony and should be rewritten as:

```pony
1 + (2 * 3)
```

Repeated use of a single operator, however, is fine:

```pony
1 + 2 + 3
```

## Operator aliasing

Most infix operators in Pony are actually aliases for functions. The left operand is the receiver the function is called on and the right operand is passed as an argument. For example, the following expressions are equivalent:

```pony
x + y
x.add(y)
```

This means that `+` is not a special symbol that can only be applied to magic types. Any type can provide its own `add` function and the programmer can then use `+` with that type if they want to.

When defining your own `add` function there is no restriction on the types of the parameter or the return type. The right side of the `+` will have to match the parameter type and the whole `+` expression will have the type that `add` returns.

Here's a full example for defining a type which allows the use of `+`. This is all you need:

```pony
// Define a suitable type
class Pair
  var _x: U32 = 0
  var _y: U32 = 0

  new create(x: U32, y: U32) =>
    _x = x
    _y = y

  // Define a + function
  fun add(other: Pair): Pair =>
    Pair(_x + other._x, _y + other._y)

// Now let's use it
class Foo
  fun foo() =>
    var x = Pair(1, 2)
    var y = Pair(3, 4)
    var z = x + y
```

[Case functions](http://tutorial.ponylang.org/pattern-matching/case-functions.html) can be used to provide more than one `add` function. It is also possible to overload infix operators to some degree using union types or f-bounded polymorphism, but this is beyond the scope of this tutorial. See the Pony standard library for further information.

You do not have to worry about any of this if you don't want to. You can simply use the existing infix operators for numbers just like any other language and not provide them for your own types.

The full list of infix operators that are aliases for functions is:

---
```
Operator | Method   | Description
---------|----------|------------
+        | add()    | Addition
-        | sub()    | Subtraction
*        | mul()    | Multiplication
/        | div()    | Division
%        | mod()    | Modulus
<<       | shl()    | Left bit shift
>>       | shr()    | Right bit shift
and      | op_and() | And, both bitwise and logical
or       | op_or()  | Or, both bitwise and logical
xor      | op_xor() | Xor, both bitwise and logical
==       | eq()     | Equality
!=       | ne()     | Non-equality
<        | lt()     | Less than
<=       | le()     | Less than or equal
>=       | ge()     | Greater than or equal
>        | gt()     | Greater than
```
---

## Short circuiting

The `and` and `or` operators use __short circuiting__ when used with Bool variables. This means that the first operand is always evaluated, but the second is only evaluated if it can affect the result.

For `and`, if the first operand is __false__ then the second operand is not evaluated since it cannot affect the result.

For `or`, if the first operand is __true__ then the second operand is not evaluated since it cannot affect the result.

This is a special feature built into the compiler, it cannot be used with operator aliasing for any other type.

## Unary operators

The unary operators are handled in the same manner, but with only one operand. For example, the following expressions are equivalent:

```pony
-x
x.neg()
```

The full list of unary operators that are aliases for functions is:

---
```
Operator | Method   | Description
---------|----------|------------
-        | neg()    | Arithmetic negation
not      | op_not() | Not, both bitwise and logical
```
---

# Control Structures

To do real work in a program you have to be able to make decisions, iterate through collections of items and perform actions repeatedly. For this, you need control structures. Pony has control structures that will be familiar to programmers who have used most languages, such as `if`, `while` and `for`, but in Pony, they work slightly differently.

## Conditionals

The simplest control structure is the good old `if`. It allows you to perform some action only when a condition is true. In Pony it looks like this:

```pony
if a > b then
  env.out.print("a is bigger")
end
```

__Can I use integers and pointers for the condition like I can in C?__ No. In Pony `if` conditions must have type Bool, i.e. they are always true or false. If you want to test whether a number `a` is not 0, then you need to explicitly say `a != 0`. This restriction removes a whole category of potential bugs from Pony programs.

If you want some alternative code for when the condition fails just add an `else`:

```pony
if a > b then
  env.out.print("a is bigger")
else
  env.out.print("a is not bigger")
end
```

Often you want to test more than one condition in one go, giving you more than two possible outcomes. You can nest `if` statements, but this quickly gets ugly:

```pony
if a == b then
  env.out.print("they are the same")
else
  if a > b then
    env.out.print("a is bigger")
  else
    env.out.print("b bigger")
  end
end
```

As an alternative Pony provides the `elseif` keyword that combines an `else` and an `if`. This works the same as saying `else if` in other languages and you can have as many `elseif`s as you like for each `if`.

```pony
if a == b then
  env.out.print("they are the same")
elseif a > b then
  env.out.print("a is bigger")
else
  env.out.print("b bigger")
end
```

__Why can't I just say "else if" like I do in C? Why the extra keyword?__ The relationship between `if` and `else` in C, and other similar languages, is ambiguous. For example:

```C
// C code
if(a)
  if(b)
    printf("a and b\n");
else
  printf("not a\n");
```
Here it is not obvious whether the `else` is an alternative to the first or the second `if`. In fact here the `else` relates to the `if(b)` so our example contains a bug. Pony avoids this type of bug by handling `if` and `else` differently and the need for `elseif` comes out of that.

## Everything is an expression

The big difference for control structures between Pony and other languages is that in Pony everything is an expression. In languages like C++ and Java `if` is a statement, not an expression. This means that you can't have an `if` inside an expression, there has to be a separate conditional operator '?'.

In Pony there are no statements there are only expressions, everything hands back a value. Your `if` statement hands you back a value. Your `for` loop (which we'll get to a bit later) hands you back a value.

This means you can use `if` directly in a calculation:

```pony
x = 1 + if lots then 100 else 2 end
```

This will give __x__ a value of either 3 or 101, depending on the variable __lots__.

If the `then` and `else` branches of an `if` produce different types then the `if` produces a __union__ of the two.

```pony
var x: (String | Bool) =
  if friendly then
    "Hello"
  else
    false
  end
```

__But what if my if doesn't have an else?__ Any `else` branch that doesn't exist gives an implicit `None`.

```pony
var x: (String | None) =
  if friendly then
    "Hello"
  end
```

__Does Pony still have the conditional operator "?"?__ No, it's not needed. Just use `if`.

## Loops

`if` allows you to choose what to do, but to do something more than once you want a loop.

### While

Pony `while` loops are very similar to those in other languages. A condition expression is evaluated and if it's true we execute the code inside the loop. When we're done we evaluate the condition again and keep going until it's false.

Here's an example that prints out the numbers 1 to 10:

```pony
var count: U32 = 1

while count <= 10 do
  env.out.print(count.string())
  count = count + 1
end
```

Just like `if` expressions `while` is also an expression. The value returned is just the value of the expression inside the loop the last time we go round it. For this example that will be the value given by `count = count + 1` when count is incremented to 11. Since Pony assignments hand back the _old_ value our `while` loop will return 10.

__But what if the condition evaluates to false the first time we try, then we don't go round the loop at all?__ In Pony `while` expressions can also have an `else` block. In general, Pony `else` blocks provide a value when the expression they are attached to doesn't. A `while` doesn't have a value to give if the condition evaluates to false the first time, so the `else` provides it instead.

__So is this like an else block on a while loop in Python?__ No, this is very different. In Python, the `else` is run when the `while` completes. In Pony the `else` is only run when the expression in the `while` isn't.

### Break

Sometimes you want to stop part-way through a loop and give up altogether. Pony has the `break` keyword for this and it is very similar to its counterpart in languages like C++, C#, and Python.

`break` immediately exits from the innermost loop it's in. Since the loop has to return a value `break` can take an expression. This is optional and if it's missed out the value from the `else` block is returned.

Let's have an example. Suppose you want to go through a list of names you're getting from somewhere, looking for either "Jack" or "Jill". If neither of those appear you'll just take the last name you're given and if you're not given any names at all you'll use "Herbert".

```pony
var name =
  while moreNames() do
    var name' = getName()
    if name' == "Jack" or name' == "Jill" then
      break name'
    end
    name'
  else
    "Herbert"
  end
```

So first we ask if there are any more names to get. If there are then we get a name and see if it's "Jack" or "Jill". If it is we're done and we break out of the loop, handing back the name we've found. If not we try again.

The line `name'` appears at the end of the loop so that will be our value returned from the last iteration if neither "Jack" nor "Jill" is found.

The `else` block provides our value of "Herbert" if there are no names available at all.

__Can I break out of multiple, nested loops like the Java labeled break?__ No, Pony does not support that. If you need to break out of multiple loops you should probably refactor your code or use a worker function.

### Continue

Sometimes you want to stop part-way through one loop iteration and move onto the next. Like other languages, Pony uses the `continue` keyword for this.

`continue` stops executing the current iteration of the innermost loop it's in and evaluates the condition ready for the next iteration.

If `continue` is executed during the last iteration of the loop then we have no value to return from the loop. In this case, we use the loop's `else` expression to get a value. As with the `if` expression if no `else` expression is provided `None` is returned.

__Can I continue an outer, nested loop like the Java labeled continue?__ No, Pony does not support that. If you need to continue an outer loop you should probably refactor your code.

### For

For iterating over a collection of items Pony uses the `for` keyword. This is very similar to `foreach` in C#, `for`..`in` in Python and `for` in Java when used with a collection. It is very different to `for` in C and C++.

The Pony `for` loop iterates over a collection of items using an iterator. On each iteration, round the loop, we ask the iterator if there are any more elements to process and if there are we ask it for the next one.

For example to print out all the strings in an array:

```pony
for name in ["Bob"; "Fred"; "Sarah"].values() do
  env.out.print(name)
end
```

Note the call to `values()` on the array, this is because the loop needs an iterator, not an array.

The iterator does not have to be of any particular type, but needs to provide the following methods:
```pony
  fun has_next(): Bool
  fun next(): T?
```

where T is the type of the objects in the collection. You don't need to worry about this unless you're writing your own iterators. To use existing collections, such as those provided in the standard library, you can just use `for` and it will all work. If you do write your own iterators note that we use structural typing, so your iterator doesn't need to declare that it provides any particular type.

You can think of the above example as being equivalent to:

```pony
let iterator = ["Bob"; "Fred"; "Sarah"].values()
while iterator.has_next() do
  let name = iterator.next()?
  env.out.print(name)
end
```

Note that the variable __name__ is declared _let_, you cannot assign to the control variable within the loop.

__Can I use break and continue with for loops?__ Yes, `for` loops can have `else` expressions attached and can use `break` and `continue` just as for `while`.

### Repeat

The final loop construct that Pony provides is `repeat` `until`. Here we evaluate the expression in the loop and then evaluate a condition expression to see if we're done or we should go round again.

This is similar to `do` `while` in C++, C# and Java except that the termination condition is reversed, i.e. those languages terminate the loop when the condition expression is false, Pony terminates the loop when the condition expression is true.

The differences between `while` and `repeat` in Pony are:

1. We always go around the loop at least once with `repeat`, whereas with `while` we may not go round at all.
1. The termination condition is reversed.

Suppose we're trying to create something and we want to keep trying until it's good enough:

```pony
repeat
  var present = makePresent()
until present.marksOutOfTen() > 7 end
```

Just like `while` loops the value given by a `repeat` loop is the value of the expression within the loop on the last iteration and `break` and `continue` can be used.

__Since you always go round a repeat loop at least once do you ever need to give it an else expression?__ Yes, you may need to. A `continue` in the last iteration of a `repeat` loop needs to get a value from somewhere and an `else` expression is used for that.

# Methods

All Pony code that actually does something, rather than defining types etc, appears in named blocks which are referred to as methods. There are three kinds of methods: functions, constructors, and behaviours. All methods are attached to type definitions (e.g. classes) - there are no global functions.

Behaviours are used for handling asynchronous messages sent to actors. We'll look at those later.

__Can I have some code outside of any methods like I do in Python?__ No. All Pony code must be within a method.

## Functions

Pony functions are quite like functions (or methods) in other languages. They can have 0 or more parameters and 0 or 1 return values. If the return type is omitted then the function will have a return value of `None`.

```pony
class C
  fun add(x: U32, y: U32): U32 =>
    x + y

  fun nop() =>
    add(1, 2)  // Pointless, we ignore the result
```

The function parameters (if any) are specified in parentheses after the function name. Functions that don't take any parameters still need to have the parentheses.

Each parameter is given a name and a type. In our example function `add` has 2 parameters, `x` and `y`, both of which are type `U32`. The values passed to a function call (the `1` and `2` in our example) are called arguments and when the call is made they are evaluated and assigned to the parameters. Parameters may not be assigned to within the function - they are effectively declared `let`.

After the parameters comes the return type. If nothing will be returned this is simply omitted.

After the return value, there's a `=>` and then finally the function body. The value returned is simply the value of the function body (remember that everything is an expression), which is simply the value of the last command in the function.

If you want to exit a function early then use the `return` command. If the function has a return type then you need to provide a value to return. If the function does not have a return type then `return` should appear on its own, without a value.

Pony applies tail call optimization when applicable to allow a recursive implementation such as the following factorial function:

```pony
fun factorial(x: I32): I32 ? =>
  if x < 0 then error end
  if x == 0 then
    1
  else
    x * factorial(x - 1)?
  end
```
The exact requirements to qualify for this optimization depends on the version of the LLVM compiler.

__Can I overload functions by argument type?__ [Case functions](http://tutorial.ponylang.org/pattern-matching/case-functions.html) provide a mechanism for providing several functions with the same name with different implementations that are selected by argument type.

## Constructors

Pony constructors are used to initialise newly created objects, as in many languages. However, unlike many languages, Pony constructors are named so you can have as many as you like, taking whatever parameters you like. By convention, the main constructor of each type (if there is such a thing for any given type) is called `create`.

```pony
class Foo
  var _x: U32

  new create() =>
    _x = 0

  new from_int(x: U32) =>
    _x = x
```

The purpose of a constructor is to set up the internal state of the object being created. To ensure this is done constructors must initialise all the fields in the object being constructed.

__Can I exit a constructor early?__ Yes. Just then use the `return` command without a value. The object must already be in a legal state to do this.

## Calling

As in many other languages, methods in Pony are called by providing the arguments within parentheses after the method name. The parentheses are required even if there are no arguments being passed to the method.

```pony
class Foo
  fun hello(name: String): String =>
    "hello " + name

  fun f() =>
    let a = hello("Fred")
```

Constructors are usually called "on" a type, by specifying the type that is to be created. To do this just specify the type, followed by a dot, followed by the name of the constructor you want to call.

```pony
class Foo
  var _x: U32

  new create() =>
    _x = 0

  new from_int(x: U32) =>
    _x = x

class Bar
  fun f() =>
    var a: Foo = Foo.create()
    var b: Foo = Foo.from_int(3)
```

Functions are always called on an object. Again just specify the object, followed by a dot, followed by the name of the function to call. If the object to call on is omitted then the current object is used (i.e. `this`).

```pony
class Foo
  var _x: U32

  new create() =>
    _x = 0

  new from_int(x: U32) =>
    _x = x

  fun get(): U32 =>
    _x

class Bar
  fun f() =>
    var a: Foo = Foo.from_int(3)
    var b: U32 = a.get()
    var c: U32 = g(b)

  fun g(x: U32): U32 =>
    x + 1

```

Constructors can also be called on an expression. Here an object is created of the same type as the specified expression - this is equivalent to directly specifying the type.

```pony
class Foo
  var _x: U32

  new create() =>
    _x = 0

  new from_int(x: U32) =>
    _x = x

class Bar
  fun f() =>
    var a: Foo = Foo.create()
    var b: Foo = a.from_int(3)
```

## Default arguments

When defining a method you can provide default values for any of the arguments. The caller then has the choice to use the values you have provided or to provide their own. Default argument values are specified with a `=` after the parameter name.

```pony
class Coord
  var _x: U32
  var _y: U32

  new create(x: U32 = 0, y: U32 = 0) =>
    _x = x
    _y = y

class Bar
  fun f() =>
    var a: Coord = Coord.create()     // Contains (0, 0)
    var b: Coord = Coord.create(3)    // Contains (3, 0)
    var b: Coord = Coord.create(3, 4) // Contains (3, 4)
```

__Do I have to provide default values for all of my arguments?__ No, you can provide defaults for as many, or as few, as you like.

## Named arguments

So far, when calling methods we have always given all the arguments in order. This is known as using __positional__ arguments. However, you can also specify the arguments in any order you like by specifying their names. This is known as using __named__ arguments.

To call a method using named arguments use the `where` keyword, followed by the named arguments and their values.

```pony
class Coord
  var _x: U32
  var _y: U32

  new create(x: U32 = 0, y: U32 = 0) =>
    _x = x
    _y = y

class Bar
  fun f() =>
    var a: Coord = Coord.create(where y = 4, x = 3)
```

__Should I specify `where` for each named argument?__ No. There must only be one `where` in each method call.

Named and positional arguments can be used together in a single call. Just start with the positional arguments you want to specify, then a `where` and finally the named arguments. But be careful, each argument must be specified only once.

Default arguments can also be used in combination with positional and named arguments - just miss out any for which you want to use the default.

```pony
class Foo
  fun f(a:U32 = 1, b: U32 = 2, c: U32 = 3, d: U32 = 4, e: U32 = 5): U32  =>
    0

  fun g() =>
    f(6, 7 where d = 8)
    // Equivalent to:
    f(6, 7, 3, 8, 5)
```

__Can I call using positional arguments but miss out the first one?__ No. If you use positional arguments they must be the first ones in the call.

## Chaining

Method chaining allows you to chain calls on an object without requiring the method to return its receiver. The syntax to call a method and chain the receiver is `object.>method()`, which is roughly equivalent to `(object.method() ; object)`. Chaining a method discards its normal return value.

```pony
primitive Printer
  fun print_two_strings(out: StdStream, s1: String, s2: String) =>
    out.>print(s1).>print(s2)
    // Equivalent to:
    out.print(s1)
    out.print(s2)
    out
```

Note that the last `.>` in a chain can be a `.` if the return value of the last call matters.

```pony
interface Factory
  fun add_option(o: Option)
  fun make_object(): Object

primitive Foo
  fun object_wrong(f: Factory, o1: Option, o2: Option): Object =>
    f.>add_option(o1).>add_option(o2).>make_object() // Error! The expression returns a Factory

  fun object_right(f: Factory, o1: Option, o2: Option): Object =>
    f.>add_option(o1).>add_option(o2).make_object() // Works. The expression returns an Object
```

## Privacy

In Pony method names start either with a lower case letter or with an underscore followed by a lowercase letter. Methods with a leading underscore are private. This means they can only be called by code within the same package. Methods without a leading underscore are public and can be called by anyone.

__Can I start my method name with 2 (or more) underscores?__ No. If the first character is an underscore then the second one MUST be a lower case letter.

# Exceptions

Pony provides a simple exception mechanism to aid in error handling. At any point, the code may decide to declare an `error` has occurred. Code execution halts at that point and the call chain is unwound until the nearest enclosing error handler is found. This is all checked at compile time so errors cannot cause the whole program to crash.

## Raising and handling errors

An error is raised with the command `error`. Error handlers are declared using the `try`-`else` syntax.

```pony
try
  callA()
  if not callB() then error end
  callC()
else
  callD()
end
```

In the above code `callA()` will always be executed and so will `callB()`. If the result of `callB()` is true then we will proceed to `callC()` in the normal fashion and `callD()` will not then be executed.

However, if `callB()` returns false, then an error will be raised. At this point, execution will stop and the nearest enclosing error handler will be found and executed. In this example that is, our else block and so `callD()` will be executed.

In either case, execution will then carry on with whatever code comes after the `try end`.

__Do I have to provide an error handler?__ No. The `try` block will handle any errors regardless. If you don't provide an error handler then no error handling action will be taken - execution will simply continue after the `try` expression.

If you want to do something that might raise an error, but you don't care if it does you can just put in it a `try` block without an `else`.

```pony
try
  // Do something that may raise an error
end
```

__Is there anything my error handler has to do?__ No. If you provide an error handler then it must contain some code, but it is entirely up to you what it does.

__What's the resulting value of a try block?__ The result of a `try` block is the value of the last statement in the `try` block, or the the value of the last statement in the `else` clause if an error was raised. If an error was raised and there was no `else` clause provided, the result value will be `None`.


## Partial functions

Pony does not require that all errors are handled immediately as in our previous examples. Instead, functions can raise errors that are handled by whatever code calls them. These are called partial functions (this is a mathematical term meaning a function that does not have a defined result for all possible inputs, i.e. arguments). Partial functions __must__ be marked as such in Pony with a `?`, both in the function signature (after the return type) and at the call site (after the closing parentheses).

For example, a somewhat contrived version of the factorial function that accepts a signed integer will error if given a negative input. It's only partially defined over its valid input type.

```pony
fun factorial(x: I32): I32 ? =>
  if x < 0 then error end
  if x == 0 then
    1
  else
    x * factorial(x - 1)?
  end
```

Everywhere that an error can be generated in Pony (an error command, a call to a partial function, or certain built-in language constructs) must appear within a `try` block or a function that is marked as partial. This is checked at compile time, ensuring that an error cannot escape handling and crash the program.

Prior to Pony 0.16.0, call sites of partial functions were not required to be marked with a `?`. This often led to confusion about the possibilities for control flow when reading code. Having every partial function call site clearly marked makes it very easy for the reader to immediately understand everywhere that a block of code may jump away to the nearest error handler, making the possible control flow paths more obvious and explicit.

## Partial constructors and behaviours

Constructors may also be marked as partial. If a constructor raises an error then the construction is considered to have failed and the object under construction is discarded without ever being returned to the caller.

When an actor constructor is called the actor is created and a reference to it is returned immediately. However, the constructor code is executed asynchronously at some later time. If an actor constructor were to raise an error it would already be too late to report this to the caller. For this reason, constructors for actors may not be partial.

Behaviours are also executed asynchronously and so cannot be partial for the same reason.

## Try-then blocks

In addition to an `else` error handler, a `try` command can have a `then` block. This is executed after the rest of the `try`, whether or not an error is raised or handled. Expanding our example from earlier:

```pony
try
  callA()
  if not callB() then error end
  callC()
else
  callD()
then
  callE()
end
```

The `callE()` will always be executed. If `callB()` returns true then the sequence executed is `callA()`, `callB()`, `callC()`, `callE()`. If `callB()` returns false then the sequence executed is `callA()`, `callB()`, `callD()`, `callE()`.

__Do I have to have an else error handler to have a then block?__ No. You can have a `try`-`then` block without an `else` if you like.

__Will my then block really always be executed, even if I return inside the try?__ Yes, your `then` expression will __always__ be executed when the `try` block is complete. The only way it won't be is if the `try` never completes (due to an infinite loop), the machine is powered off, or the process is killed (and then, maybe).

## With blocks

A `with` expression can be used to ensure disposal of an object when it is no longer needed. A common case is a database connection which needs to be closed after use to avoid resource leaks on the server. For example:

```pony
with obj = SomeObjectThatNeedsDisposing() do
  // use obj
end
```

`obj.dispose()` will be called whether the code inside the `with` block completes successfully or raises an error. To take part in a `with` expression, the object that needs resource clean-up must, therefore, provide a `dispose()` method:

```pony
class SomeObjectThatNeedsDisposing
  // constructor, other functions

  fun dispose() =>
    // release resources
```

It is possible to provide an `else` clause, which is called only in error cases:

```pony
with obj = SomeObjectThatNeedsDisposing() do
  // use obj
else
  // only run if an error has occurred
end
```

Multiple objects can be set up for disposal:

```pony
with obj = SomeObjectThatNeedsDisposing(), other = SomeOtherDisposableObject() do
  // use obj
end
```

The value of a `with` expression is the value of the last expression in the block, or of the last expression in the `else` block if there is one and an error occurred.

## Language constructs that can raise errors

The only language construct that can raise an error, other than the `error` command or calling a partial method, is the `as` command. This converts the given value to the specified type if it can be. If it can't then an error is raised. This means that the `as` command can only be used inside a try block or a partial method.

## Comparison to exceptions in other languages

Pony exceptions behave very much the same as those in C++, Java, C#, Python, and Ruby. The key difference is that Pony exceptions do not have a type or instance associated with them. This makes them the same as C++ exceptions would be if a fixed literal was always thrown, e.g. `throw 3;`. This difference simplifies exception handling for the programmer and allows for much better runtime error handling performance.

The `else` handler in a `try` expression is just like a `catch(...)` in C++, `catch(Exception e)` in Java or C#, `except:` in Python, or `rescue` in Ruby. Since exceptions do not have types there is no need for handlers to specify types or to have multiple handlers in a single try block.

The `then` block in a `try` expression is just like a `finally` in Java, C#, or Python and `ensure` in Ruby.

If required, error handlers can "reraise" by using the `error` command within the handler.

# Equality in Pony

Pony features two forms of equality: by structure and by identity.  

## Identity equality

Identity equality checks in Pony are done via the `is` keyword. `is` verifies that the two items are the same.

```pony
if None is None then
  // TRUE!
  // There is only 1 None so the identity is the same
end

let a = Foo("hi")
let b = Foo("hi")

if a is b then
  // NOPE. THIS IS FALSE
end

let c = a
if a is c then
  // YUP! TRUE!
end
```

## Structural equality

Structural equality checking in Pony is done via the infix operator `==`. It verifies that two items have the same value. If the identity of the items being compared is the same, then by definition they have the same value.

You can define how structural equality is checked on your object by implementing `fun eq(that: box->Foo): Bool`. Remember, since `==` is an infix operator, `eq` must be defined on the left operand, and the right operand must be of type `Foo`.

```pony
class Foo
  let _a: String

  new create(a: String) =>
    _a = a

  fun eq(that: box->Foo): Bool =>
    this._a == that._a

actor Main
  new create(e: Env) =>
    let a = Foo("hi")
    let b = Foo("bye")
    let c = Foo("hi")

    if a == b then
      // won't print
      e.out.print("1")
    end

    if a == c then
      // will print
      e.out.print("2")
    end

    if a is c then
      // won't print
      e.out.print("3")
    end
```

If you don't define your own `eq`, you will inherit the default implementation that defines equal by value as being the same as by identity.

```pony
interface Equatable[A: Equatable[A] #read]
  fun eq(that: box->A): Bool => this is that
  fun ne(that: box->A): Bool => not eq(that)
```

## Primitives and equality

As you might remember from [Chapter 2](http://tutorial.ponylang.org/types/primitives.html), primitives are the same as classes except for two important differences:

* A primitive has no fields.
* There is only one instance of a user-defined primitive.

This means, that every primitive of a given type, is always structurally equal and equal based on identity. So, for example, None is always None.

```pony
if None is None then
  // this is always true
end

if None == None then
  // this is also always true
end
```

# Sugar

Pony allows you to omit certain small details from your code and will put them back in for you. This is done to help make your code less cluttered and more readable. Using sugar is entirely optional, you can always write out the full version if you prefer.

## Apply

Many Pony classes have a function called `apply` which performs whatever action is most common for that type. Pony allows you to omit the word `apply` and just attempt to do a call directly on the object. So:

```pony
var foo = Foo.create()
foo()
```

becomes:

```pony
var foo = Foo.create()
foo.apply()
```

Any required arguments can be added just like normal method calls.

```pony
var foo = Foo.create()
foo(x, 37 where crash = false)
```

becomes:

```pony
var foo = Foo.create()
foo.apply(x, 37 where crash = false)
```

__Do I still need to provide the arguments to apply?__ Yes, only the `apply` will be added for you, the correct number and type of arguments must be supplied. Default and named arguments can be used as normal.

__How do I call a function foo if apply is added?__ The `apply` sugar is only added when calling an object, not when calling a method. The compiler can tell the difference and only adds the `apply` when appropriate.

## Create

To create an object you need to specify the type and call a constructor. Pony allows you to miss out the constructor and will insert a call to `create()` for you. So:

```pony
var foo = Foo
```

becomes:

```pony
var foo = Foo.create()
```

Normally types are not valid things to appear in expressions, so omitting the constructor call is not ambiguous. Remember that you can easily spot that a name is a type because it will start with a capital letter.

If arguments are needed for `create` these can be provided as if calling the type. Default and named arguments can be used as normal.

```pony
var foo = Foo(x, 37 where crash = false)
```

becomes:

```pony
var foo = Foo.create(x, 37 where crash = false)
```

__What if I want to use a constructor that isn't named create?__ Then the sugar can't help you and you have to write it out yourself.

__If the create I want to call takes no arguments can I still put in the parentheses?__ No. Calls of the form `Type()` use the combined create-apply sugar (see below). To get `Type.create()` just use `Type`.

## Combined create-apply

If a type has a create constructor that takes no arguments then the create and apply sugar can be used together. Just call on the type and calls to create and apply will be added. The call to create will take no arguments and the call to apply will take whatever arguments are supplied.

```pony
var foo = Foo()
var bar = Bar(x, 37 where crash = false)
```

becomes:

```pony
var foo = Foo.create().apply()
var bar = Bar.create().apply(x, 37 where crash = false)
```

__What if the create has default arguments? Do I get the combined create-apply sugar if I want to use the defaults?__ The combined create-apply sugar can only be used when the `create` constructor has no arguments. If there are default arguments then this sugar cannot be used.

## Update

The `update` sugar allows any class to use an assignment to accept data. Many languages allow this for assigning into collections, for example, a simple C array, `a[3] = x;`.

In any assignment where the left-hand side is a function call, Pony will translate this to a call to update, with the value from the right-hand side as an extra argument. So:

```pony
foo(37) = x
```

becomes:

```pony
foo.update(37 where value = x)
```

The value from the right-hand side of the assignment is always passed to a parameter named `value`. Any object can allow this syntax simply by providing an appropriate function `update` with an argument `value`.

__Does my update function have to have a single parameter that takes an integer?__ No, you can define update to take whatever parameters you like, as long as there is one called `value`. The following are all fine:

```pony
foo1(2, 3) = x
foo2() = x
foo3(37, "Hello", 3.5 where a = 2, b = 3) = x
```

__Does it matter where `value` appears in my parameter list?__ Whilst it doesn't strictly matter it is good practice to put `value` as the last parameter. That way all of the others can be specified by position.

# Object Literals

Sometimes it's really convenient to be able to write a whole object inline. In Pony, this is called an object literal, and it does pretty much exactly what an object literal in JavaScript does: it creates an object that you can use immediately.

But Pony is statically typed, so an object literal also creates an anonymous type that the object literal fulfills. This is similar to anonymous classes in Java and C#. In Pony, an anonymous type can provide any number of traits and interfaces.

## What's this look like, then?

It basically looks like any other type definition, but with some small differences. Here's a simple one:

```pony
object
  fun apply(): String => "hi"
end
```

Ok, that's pretty trivial. Let's extend it so that it explicitly provides an interface so that the compiler will make sure the anonymous type fulfills that interface. You can use the same notation to provide traits as well.

```pony
object is Hashable
  fun apply(): String => "hi"
  fun hash(): U64 => this().hash()
end
```

What we can't do is specify constructors in an object literal, because the literal _is_ the constructor. So how do we assign to fields? Well, we just assign to them. For example:

```pony
class Foo
  fun foo(str: String): Hashable =>
    object is Hashable
      let s: String = str
      fun apply(): String => s
      fun hash(): U64 => s.hash()
    end
```

When we assign to a field in the constructor, we are _capturing_ from the lexical scope the object literal is in. Pretty fun stuff! It lets us have arbitrarily complex __closures__ that can even have multiple entry points (i.e. functions you can call on a closure).

An object literal with fields is returned as a `ref` by default unless an explicit reference capability is declared by specifying the capability after the `object` keyword. For example, an object with sendable captured references can be declared as `iso` if needed:

```pony
class Foo
  fun foo(str: String): Hashable iso^ =>
    object iso is Hashable
      let s: String = str
      fun apply(): String => s
      fun hash(): U64 => s.hash()
    end
```

We can also implicitly capture values from the lexical scope by using them in the object literal. Sometimes values that aren't local variables, aren't fields, and aren't parameters of a function are called _free variables_. By using them in a function, we are _closing over_ them - that is, capturing them. The code above could be written without the field `s`:

```pony
class Foo
  fun foo(str: String): Hashable iso^ =>
    object iso is Hashable
      fun apply(): String => str
      fun hash(): U64 => str.hash()
    end
```

## Lambdas

Arbitrarily complex closures are nice, but sometimes we just want a simple closure. In Pony, you can use the lambdas for that. A lambda is written as a function (implicitly named `apply`) enclosed in curly brackets:

```pony
{(s: String): String => "lambda: " + s }
```

This produces the same code as:

```pony
object
  fun apply(s: String): String => "lambda: " + s
end
```

The reference capability of the lambda object can be declared by appending it after the closing curly bracket:

```pony
{(s: String): String => "lambda: " + s } iso
```

This produces the same code as:

```pony
object iso
  fun apply(s: String): String => "lambda: " + s
end
```

Lambdas can be used to capture from the lexical scope in the same way as object literals can:

```pony
class Foo
  new create(env:Env) =>
    foo({(s: String) => env.out.print(s) })

  fun foo(f: {(String)}) =>
    f("Hello World")
```

assign from the lexical scope to a field. This is done by adding a second argument list after the parameters:

```pony
class Foo
  new create(env:Env) =>
    foo({(s: String)(env) => env.out.print(s) })

  fun foo(f: {(String)}) =>
    f("Hello World")
```

It's also possible to use a _capture list_ to create new names for things. A capture list is a second parenthesised list after the parameters:

```pony
  new create(env:Env) =>
    foo({(s: String)(myenv = env) => myenv.out.print(s) })
```

The type of a lambda is also declared using curly brackets. Within the brackets, the function parameter types are specified within parentheses followed by an optional colon and return type. The example above uses `{(String)}` to be the type of a lambda function that takes a `String` as an argument and returns nothing.

If the lambda object is not declared with a specific reference capability, the reference capability is inferred from the structure of the lambda. If the lambda does not have any captured references, it will be `val` by default; if it does have captured references, it will be `ref` by default. The following is an example of a `val` lambda object:

```pony
actor Main
  new create(env:Env) =>
    let l = List[U32]
    l.push(10).push(20).push(30).push(40)
    let r = reduce(l, 0, {(a:U32, b:U32): U32 => a + b })
    env.out.print("Result: " + r.string())

  fun reduce(l: List[U32], acc: U32, f: {(U32, U32): U32} val): U32 =>
    try
      let acc' = f(acc, l.shift()?)
      reduce(l, acc', f)
    else
      acc
    end
```

The `reduce` method in this example requires the lambda type for the `f` parameter to require a reference capability of `val`. The lambda object passed in as an argument does not need to declare an explicit reference capability because `val` is the default for a lambda that does not capture anything.

As mentioned previously the lambda desugars to an object literal with an `apply` method. The reference capability for the `apply` method defaults to `box` like any other method. In a lambda that captures this needs to be `ref` if the function needs to modify any of the captured variables or call `ref` methods on them. The reference capability for the method (versus the reference capability for the object which was described above) is defined by putting the capability before the parenthesized argument list.

```pony
actor Main
  new create(env:Env) =>
    let l = List[String]
    l.push("hello").push("world")
    var count = U32(0)
    for_each(l, {ref(s:String) =>
      env.out.print(s)
      count = count + 1
    })
    // Displays '0' as the count
    env.out.print("Count: " + count.string())

  fun for_each(l: List[String], f: {ref(String)} ref) =>
    try
      f(l.shift()?)
      for_each(l, f)
    end
```

This example declares the type of the apply function that is generated by the lambda expression as being `ref`. The lambda type declaration for the `f` parameter in the `for_each` method also declares it as `ref`. The reference capability of the lambda type must also be `ref` so that the method can be called. The lambda object does not need to declare an explicit reference capability because `ref` is the default for a lambda that has captures.

## Actor literals

Normally, an object literal is an instance of an anonymous class. To make it an instance of an anonymous actor, just include one or more behaviours in the object literal definition.

```pony
object
  be apply() => env.out.print("hi")
end
```

An actor literal is always returned as a `tag`.

## Primitive literals

When an anonymous type has no fields and no behaviours (like, for example, an object literal declared as a lambda literal), the compiler generates it as an anonymous primitive, unless a non-`val` reference capability is explicitly given. This means no memory allocation is needed to generate an instance of that type.

In other words, in Pony, a lambda that doesn't close over anything has no memory allocation overhead. Nice.

A primitive literal is always returned as a `val`.

# Partial Application

Partial application lets us supply _some_ of the arguments to a constructor, function, or behaviour, and get back something that lets us supply the rest of the arguments later.

## A simple case

A simple case is to create a "callback" function. For example:

```pony
class Foo
  var _f: F64 = 0

  fun ref addmul(add: F64, mul: F64): F64 =>
    _f = (_f + add) * mul

class Bar
  fun apply() =>
    let foo: Foo = Foo
    let f = foo~addmul(3)
    f(4)
```

This is a bit of a silly example, but hopefully, the idea is clear. We partially apply the `addmul` function on `foo`, binding the receiver to `foo` and the `add` argument to `3`. We get back an object, `f`, that has an `apply` method that takes a `mul` argument. When it's called, it in turn calls `foo.addmul(3, mul)`.

We can also bind all the arguments:

```pony
let f = foo~addmul(3, 4)
f()
```

Or even none of the arguments:

```pony
let f = foo~addmul()
f(3, 4)
```

## Out of order arguments

Partial application with named arguments allows binding arguments in any order, not just left to right. For example:

```pony
let f = foo~addmul(where mul = 4)
f(3)
```

Here, we bound the `mul` argument but left `add` unbound.

## Partially applying a partial application

Since partial application results in an object with an apply method, we can partially apply the result!

```pony
let f = foo~addmul()
let f2 = f~apply(where mul = 4)
f2(3)
```

## Partial application is an object literal

Under the hood, we're assembling an object literal for partial application. It captures some of the lexical scope as fields and has an `apply` method that takes some, possibly reduced, number of arguments. This is actually done as sugar, by rewriting the abstract syntax tree for partial application to be an object literal, before code generation.

That means partial application results in an anonymous class and returns a `ref`. If you need another reference capability, you can wrap partial application in a `recover` expression.

# Gotchas

Every programming language has gotchas. Those "wat" moments that make us all laugh when someone does a presentation on them. They often shoot to the top of sites like Hacker News and Reddit. It's all in good fun, except, it isn't. Each of those gotchas and the laughs we get from them, hide someone's pain. This chapter covers some common Pony gotchas that new Pony programmers often stumble across with painful results. Probably the best way to approach this chapter is to imagine each section has a giant flashing "DO NOT DO THIS" sign.

# Divide by Zero

What's 1 divided by 0? How about 10 divided by 0? What is the result you get in your favorite programming language? 

In math, divide by zero is undefined. There is no answer to that question as the expression 1/0 has no meaning. In many programming languages, the answer is a runtime exception that the user has to handle. In Pony, things are a bit different.

## Divide by zero in Pony

In Pony, *divide by zero results in zero*. That's right,

```pony
let x = 1 / 0
```

results in `0` being assigned to `x`. Insane right? Well, yes and no. From a mathematical standpoint, it is very much insane. From a practical standpoint, it is very much not. 

## Partial functions and math in Pony

As you might remember from the [exceptions portion of this tutorial](/expressions/exceptions.html), Pony handles extraordinary circumstances via the `error` keyword and partial functions. In an ideal world, you might imagine that a Pony divide method for `U64` might look like:

```pony
fun divide(n: U64, d: 64) ? =>
  if d == 0 then error end

  n / d
```

We indicate that our function is partial via the `?` because we can't compute a result for all inputs. In this case, having 0 as a denominator. In fact, originally, this is how division worked in Pony. And then practicality intervened.

## Death by a thousand `try`s

From a practical perspective, having division as a partial function is awful. You end up with code littered with `try`s attempting to deal with the possibility of division by zero. Even if you had asserted that your denominator was not zero, you'd still need to protect against divide by zero because, at this time, the compiler can't detect that value dependent typing. So, as of right now (ponyc v0.2), divide by zero in Pony does not result in `error` but rather `0`. 

# Garbage Collection

There's a common GC anti-pattern that many new Pony programmers accidentally stumble across. Usually, this results in a skyrocketing memory usage in their test program and questions on the mailing list and IRC as to why Pony isn't working correctly. It is, in fact, working correctly, albeit not obviously.

## Garbage Collection in the world at large

Garbage collection, in most languages, can run at any time. Your program can be paused so that memory can be freed up. This sucks if you want predictable completion of sections of code. Most of the time, your function will finish in less than a millisecond but every now and then, its paused during execution to GC. There are advantages to this approach. Whenever you run low on memory, the GC can attempt to free some memory and get you more. In general, this is how people expect Pony's garbage collector to work. As you might guess though, it doesn't work that way.

## Garbage Collection in Pony

Garbage collection is never attempted on any actor while it is executing a behavior. This gives you very predictable performance when executing behaviors but also makes it easy to grab way more memory than you intend to. Let's take a look at how that can happen via the "long-running behavior problem".

## Long running behaviors and memory

Here's a typical "I'm learning Pony" program:

```pony
actor Main
  new create(env: Env)
    for i in Range(1, 2_000_000) do
      ... something that uses up heap ... 
    end
```

This program will never garbage collect before exiting. `create` is run as a behavior on actors which means that no garbage collection will occur while it's running. Long loops in behaviors are a good way to exhaust memory. Don't do it. If you want to execute something in such a fashion, use a [Timer](http://stdlib.ponylang.org/time-Timer/).

# Appendix

Welcome to the appendix; the land of misshapen and forgotten documentation. Ok, not really forgotten just... 'lesser' sounds wrong. Some of this material could get some loving and be promoted to a full chapter, some are always going to be an appendix, some might be worthy of a short book unto itself. Right now though it lives here, have a look through. You'll find a lexicon of standard Pony terminology, a symbol lookup cheat sheet that can help you locate documentation on all our funny symbols like ^, ! and much more.

# Pony Lexicon

Words are hard. We can all be saying the same thing but do we _mean_ the same thing? It's tough to know. Hopefully, this lexicon helps a little.

## Terminology

**Braces**: { }. Synonymous with curly brackets.

**Brackets**: This term is ambiguous. In the UK it usually means ( ) in the US is usually means [ ]. It should, therefore, be avoided for use for either of these. Can be used as a general term for any unspecified grouping punctuation, including { }.

**Compatible type**: Two types are compatible if there can be any single object which is an instance of both types. Note that a suitable type for the single object does not have to have been defined, as long as it could be. For example, any two traits are compatible because a class could be defined that provides both of them, even if such a class has not been defined. Conversely, no two classes can ever be compatible because no object can be an instance of both.

**Compound type**: A type combining multiple other types, ie union, intersection, and tuple. Opposite of a single type.

**Concrete type**: An actor, class or primitive.

**Curly brackets**: { }. Synonymous with braces.

**Declaration** and **definition**: synonyms for each other, we do not draw the C distinction between forward declarations and full definitions.

**Default method body**: Method body defined in a trait and optionally used by concrete types.

**Entity**: Top level definition within a file, ie alias, trait, actor, class, primitive.

**Explicit type**: An actor, class or primitive.

**Member**: Method or field.

**Method**: Something callable on a concrete type/object. Function, behaviour or constructor.

**Override**: When a concrete type has its own body for a method with a default body provided by a trait.

**Parentheses**: ( ). Synonymous with round brackets.

**Provide**: An entity's usage of traits and the methods they contain. Equivalent to implements or inherits from.

**Round brackets**: ( ). Synonymous with parentheses.

**Single type**: Any type which is not defined as a collection of other types. Actors, classes, primitives, traits and structural types are all single types. Opposite of a compound type.

**Square brackets**: [ ]

**Trait clash**: A trait clashes with another type if it contains a method with the same name, but incompatible signature as a method in the other type. A clashing trait is incompatible with the other type. Traits can clash with actors, classes, primitives, intersections, structural types and other traits.

# Pony Symbols

Pony, like just about any other programming language, has plenty of odd symbols that make up its syntax. If you don't remember what one means, it can be hard to search for them. Below you'll find a table with various Pony symbols and what you should search the tutorial for in order to learn more about the symbol.

|Symbol | Search Keywords|
| --- | --- |
| `!`  | Alias |
| `->` | Arrow type, viewpoint |
| `^`  | Ephemeral |
| `@`  | FFI |
| `&`  | Intersection |
| `=>` | Match arrow |
| `~`  | Partial application |
| `?`  | Partial function |
| `'`  | Prime |
| `<:` | Subtype |


Here is a more elaborate explanation of Pony's use of special characters: (a line with (2) or (3) means an alternate usage of the symbol of the previous  line)

|Symbol | Usage|
| --- | --- |
| `,`  | to separate parameters in a function signature, or the items of a tuple |
| `.`  | (1) to call a field or a function on a variable (field access or method call) |
|    | (2) to qualify a type/method with its package name |
| `.>` | to call a method on an object and return the receiver (chaining) |
| `'`  | used as alternative name in parameters (prime) |
| `"`  | to delineate a literal string |
| `"""`  | to delineate a documentation string |
| `(` | (1) start of line: start of a tuple |
|    | (2) middle of line: method call |
| `()` | (1) parentheses, for function or behavior parameters |
|    | (2) making a tuple (values separated by `,`) |
|    | (3) making an enumeration (values separated by <code>&#124;</code>) |
| `[`  | (1) start of line: start of an array literal |
|    | (2) middle of line: generic formal parameters |
| `[]`  | (1) to indicate a generic type, for example `Range[U64]` |
|      | (2) to indicate the return type of an FFI function call |
| `{}`  | a function type |
| `:`  | (1) after a variable: is followed by the type name |
|    | (2) to indicate a function return type |
|    | (3) a type constraint |
| `;`  | only used to separate expressions on the same line |
| `=`  | (1) (destructive) assignment |
|    | (2) in: use alias = packname |
| `!`  | (1) boolean negation |
|    | (2) a type that is an alias of another type |
| `?`  | (1) T?  type T or None |
|    | (2) partial functions |
|    | (3) a call to a C function that could raise an error |
| `-`  | (1) start of line: unary negation |
|    | (2) middle of line: subtraction |
| `_`  | (1) to indicate a private variable, constructor, function, behavior |
|    | (2) to ignore a tuple item in a pattern match |
| `~`  | partial application |
| `^`  | an ephemeral type |
| <code>&#124;</code> | (1) separates the types in an enumeration (the value can be any of these types) |
|    | (2) starts a branch in a match |
| `&`  | (1) separates the types in a complex type (the value is of all of these types) |
|    | (2) intersection |
| `@`  | FFI call |
| `//`  | comments |
| `/* */`  | multi-line or block comments |
| `=>`  | (1) start of a function body |
|     | (2) starts the code of a matching branch |
| `->`  | (1) arrow type |
|     | (2) viewpoint |
| `._i` | where `i = 1,2,…`  means the ith item of a tuple |
| `<:`  | "is a subtype of" or "can be substituted for" |

# Keywords

This listing explains the usage of every Pony keyword.

|Keyword | Usage|
| --- | --- |
| actor | defines an actor
| as | conversion of a value to another Type (can raise an error)
| be | behavior, executed asynchronously
| box | default reference capability – object is readable, but not writable
| break | to step out of a loop statement
| class | defines a class
| compile_error | will provoke a compile error 
| continue | continues a loop with the next iteration
| consume | move a value to a new variable, leaving the original variable empty
| do | loop statement, or after a with statement
| else | conditional statement in if, for, while, repeat, try (as a catch block), match 
| elseif | conditional statement, also used with ifdef
| embed | embed a class as a field of another class
| end | ending of: if then, ifdef, while do, for in, repeat until, try, object, lambda, recover, match
| error | raises an error
| for | loop statement
| fun | define a function, executed synchronously
| if  | (1) conditional statement
|     | (2) to define a guard in a pattern match
| ifdef | when defining a build flag at compile time:  ponyc –D "foo"
| in | used in a for in - loop statement
| interface | used in structural subtyping
| is | (1) used in nominal subtyping
|    | (2) in type aliasing
| iso | reference capability – read and write uniqueness
| lambda | to make a closure
| let | declaration of immutable variable: you can't rebind this name to a new value
| match | pattern matching
| new | constructor
| object | to make an object literal
| primitive | declares a primitive type
| recover | removes the reference capability of a variable
| ref | reference capability – object (on which function is called) is mutable
| repeat | loop statement
| return | to return early from a function
| tag | reference capability – neither readable nor writeable, only object identity
| then | (1) in if conditional statement 
|      | (2) as a (finally) block in try
| this | the current object
| trait | used in nominal subtyping:  class Foo is TraitName
| trn | reference capability – write uniqueness, no other actor can write to the object
| try | error handling
| type | to declare a type alias
| until | loop statement
| use | (1) using a package
|     | (2) using an external library foo: use "lib:foo"
|     | (3) declaration of an FFI signature
|     | (4) add a search path for external libraries: use "path:/usr/local/lib"
| var | declaration of mutable variable: you can rebind this name to a new value
| val | reference capability – globally immutable object
| where | when specifying named arguments 
| while | loop statement
| with  | ensure disposal of an object

# Examples

Small _how do I_ examples for Pony. These will eventually find another home. Until then, they live here.

## Enum with values
```pony
primitive Black fun apply(): U32 => 0xFF000000
primitive Red   fun apply(): U32 => 0xFFFF0000
```

## Enum with values with namespace
```pony
primitive Colours
  fun black(): U32 => 0xFF000000
  fun red(): U32 => 0xFFFF0000
```

## Enum which can be iterated
```pony
primitive Black
primitive Blue
primitive Red
primitive Yellow

type Colour is (Black | Blue | Red | Yellow)

primitive ColourList
  fun tag apply(): Array[Colour] =>
    [Black; Blue; Red; Yellow]

for colour in ColourList().values() do
end
```

## Read struct values from FFI
If you have a C struct which returns a struct with data like this
```c
typedef struct {
  uint8_t code;
  float x;
  float y;
} EGLEvent;

EGLEvent getEvent() {
    EGLEvent e = {1, ev.xconfigure.width, ev.xconfigure.height};
    return e;
}

```
the you can destructure it and get the values using a tuple
```pony
type EGLEvent is (U8, F32, F32)
(var code, var x, var y) = @getEvent[EGLEvent]()
```

## Get and pass pointers to FFI
```pony
primitive _XDisplayHandle
primitive _EGLDisplayHandle

let x_dpy = @XOpenDisplay[Pointer[_XDisplayHandle]](U32(0))
if x_dpy.is_null() then
  env.out.print("XOpenDisplay failed")
end

let e_dpy = @eglGetDisplay[Pointer[_EGLDisplayHandle]](x_dpy)
if e_dpy.is_null() then
  env.out.print("eglGetDisplay failed")
end
```

## Pass an Array of values to FFI (TODO)
```pony
primitive _EGLConfigHandle
let a = Array[U16](8)
a.push(0x3040)
a.push(0x4)
a.push(0x3033)
a.push(0x4)
a.push(0x3022)
a.push(0x8)
a.push(0x3023)
a.push(0x8)
a.push(0x3024)
let config = Pointer[_EGLConfigHandle]
if @eglChooseConfig[U32](e_dpy, a, config, U32(1), Pointer[U32]) == 0 then
    env.out.print("eglChooseConfig failed")
end
```

## How to access command line arguments
```pony
actor Main
  new create(env: Env) =>
    // The no of arguments
    env.out.print(env.args.size().string())
    for value in env.args.values() do
      env.out.print(value)
    end
    // Access the arguments the first one will always be the the appication name
    try env.out.print(env.args(0)?) end
```

## How to use options
```pony
actor Main
  new create(env: Env) =>
    var options = Options(env)

    options
      .add("output", "o", StringArgument)

    env.out.print(options.has_argument())

    for option in options do
      match option
      | ("output", var arg: String) => _outputFileName = arg.string()
      | let err: ParseError =>
          err.report(env.out)
          env.out.print(
            """
            pony-embed [OPTIONS]
              --output     name   string output filename.
            """
          )
      end
    end

```

## How to write tests
Just create a test.pony file
```pony
use "ponytest"

actor Main is TestList
  new create(env: Env) => PonyTest(env, this)
  new make() => None

  fun tag tests(test: PonyTest) =>
    test(_TestAddition)

class iso _TestAddition is UnitTest
  """
  Adding 2 numbers
  """
  fun name(): String => "u32/add"

  fun apply(h: TestHelper): TestResult =>
    h.expect_eq[U32](2 + 2, 4)
```

Some assertions you can make with the TestHelper are
```pony
fun tag log(msg: String, verbose: Bool = false)
be fail() =>
be assert_failed(msg: String) =>
fun tag assert_true(actual: Bool, msg: String = "") ?
fun tag expect_true(actual: Bool, msg: String = ""): Bool
fun tag assert_false(actual: Bool, msg: String = "") ?
fun tag expect_false(actual: Bool, msg: String = ""): Bool
fun tag assert_error(test: ITest, msg: String = "") ?
fun tag expect_error(test: ITest box, msg: String = ""): Bool
fun tag assert_is (expect: Any, actual: Any, msg: String = "") ?
fun tag expect_is (expect: Any, actual: Any, msg: String = ""): Bool
fun tag assert_eq[A: (Equatable[A] #read & Stringable)]
  (expect: A, actual: A, msg: String = "") ?
fun tag expect_eq[A: (Equatable[A] #read & Stringable)]
  (expect: A, actual: A, msg: String = ""): Bool
```

## Operator overloading (easy for copy and paste)
```pony
fun add(other: A): A
fun sub(other: A): A
fun mul(other: A): A
fun div(other: A): A
fun mod(other: A): A
fun eq(other: A): Bool
fun ne(other: A): Bool
fun lt(other: A): Bool
fun le(other: A): Bool
fun ge(other: A): Bool
fun gt(other: A): Bool
fun shl(other: A): A
fun shr(other: A): A
fun op_and(other:A): A
fun op_or(other: A): A
fun op_xor(othr: A): A
```

## Create empty functions in a class
```pony
class Test
  fun alpha() =>
    """
    """
  fun beta() =>
    """
    """
```

## How to create Arrays with values

Single values can be separated by semicolon or newline.

```pony
let dice: Array[U32] = [1; 2; 3
  4
  5
  6
]
```

## How to modify a lexically captured variable in a closure

```pony
actor Main
  fun foo(n:U32): {ref(U32): U32} =>
    var s: Array[U32] = Array[U32].init(n, 1)
    {ref(i:U32)(s): U32 =>
      try
        s(0) = s(0) + i
        s(0)
      else
        0
      end
    }

  new create(env:Env) =>
    var f = foo(5)
    env.out.print(f(10).string())
    env.out.print(f(20).string())
```

# Pony and Whitespace

Whitespace (e.g. spaces, tabs, newlines, etc.) in Pony isn't significant.

Well, it mostly isn't significant.

## Mostly insignificant whitespace

Pony reads a bit like Python, which is a _whitespace significant_ language. That is, the amount of indentation on a line means something in Python. In Pony, the amount of indentation is meaningless.

That means Pony programmers can format their code in whatever way suits them.

There are three exceptions:

1. A `-` at the beginning of a line starts a new expression (unary negation), whereas a `-` in the middle of an expression is a binary operator (subtraction).
2. A `(` at the beginning of a line starts a new expression (a tuple), whereas a `(` in the middle of an expression is a method call.
3. A `[` at the beginning of a line starts a new expression (an array literal), whereas a `[` in the middle of an expression is generic formal parameters.

That stuff may seem a little esoteric right now, but we'll explain it all later. The `-` part should make sense though.

```pony
a - b
```

That means "subtract b from a".

```pony
a
-b
```

That means "first do a, then, in a new expression, do a unary negation of b".

## Semicolons

In Pony, you don't end an expression with a `;`, unlike C, C++, Java, C#, etc. In fact, you don't need to end it at all! The compiler knows when an expression has finished, like Python or Ruby.

However, sometimes it's convenient to put more than one expression on the same line. When you want to do that, you __must__ separate them with a `;`.

__Why? Can't the compiler tell an expression has finished?__ Yes, it can. The compiler doesn't really need the `;`. However, it turns out the programmer does! By requiring a `;` between expressions on the same line, the compiler can catch some pretty common syntax errors for you.

## Docstrings

Including documentation in your code makes you awesome. If you do it, everyone will love you.

Pony makes it easy by allowing you to put a __docstring__ on every type and on every method. Just put a string literal right after declaring the type, or right after the `=>` of a method, before writing the body. The compiler will know what to do with them.

For traits and interfaces that have methods without bodies, you can put the docstring after the method declaration, even though there is no `=>`.

By convention, a docstring should be a triple-quoted string, and it should use Markdown for any formatting.

```pony
actor Main
"""
This is documentation for my actor
"""
  new create(env: Env) =>
  """
  This is documentation for my create method
  """
  None

trait Readable
  fun val read()
  """
  This is documentation on my unimplemented trait method
  """
```

## Comments

Use __docstrings__ first! But if you need to put some comments in the implementation of your methods, perhaps to explain what's happening on various lines, you can use C++ style comments. In Pony, block comments can be nested.

```pony
// This is a line comment.
/* This is a block comment. */
/* This block comment /* has another block comment */ inside of it. */
```

# Compiler Arguments

`ponyc`, the compiler, is usually called in the project directory, where it finds the `.pony` files and its dependencies automatically. There it will create the binary based on the directory name. You can override this and tune the compilation with several options as described via `ponyc --help` and you can pass a separate source directory as an argument.

```bash
ponyc [OPTIONS] <package directory>
```

The most useful options are `--debug`, `--path` or just `-p`, `--output` or just `-o` and `--docs` or `-g`. With `-l` you can generate a C library, `lib<directory>`.

`--debug` will skip the LLVM optimizations passes. This should not be mixed up with `make config=debug`, the default make configuration target. `config=debug` will create DWARF symbols, and add slower assertions to ponyc, but not to the generated binaries. For those, you can omit DWARF symbols with the `--strip` or `-s` option.

`--path` or `-p` take a `:` separated path list as the argument and adds those to the compile-time library paths for the linker to find source packages and the native libraries, static or dynamic, being linked at compile-time or via the FFI at run-time. The system adds several paths already, e.g. on windows it queries the registry to find the compiler run-time paths, you can also use `use "lib:path"` statements in the source code and as a final possibility, you can add `-p` paths. But if you want the generated binary to accept such a path to find a dynamic library on your client system, you need to handle that in your source code by yourself. See the `options` package for this.

`--output` or `-o` takes a directory name where the final binary is created.

`--docs` or -`g` creates a directory of the package with documentation in [readthedocs.org](http://readthedocs.org) format, i.e. markdown with nice navigation.

Let's study the documentation of the builtin stdlib:

```bash
  pip install mkdocs
  ponyc packages/stdlib --docs && cd stdlib-docs && mkdocs serve
```

And point your web browser to [http://127.0.0.1:8000](http://127.0.0.1:8000) serving a live-reloading local version of the docs.

Note that there is _no built-in debugger_ to interactively step through your program and interpret the results. But ponyc creates proper DWARF symbols and you can step through your programs with a conventional debugger, such as GDB or LLDB.


# Runtime options for Pony programs

Besides using the `options` package, there are also several built-in options for the generated binary (_not for use with ponyc_) starting with `--pony*`, see `ponyc --help`, to tweak runtime performance. You can override the number of initial threads, tune cycle detection (_CD_), the garbage collector and even turn off yield, which is not really recommended.

# A short guide to Pony error messages

You've been through the tutorial, you've watched some videos, and now you're ready to write some Pony code. You fire up your editor, shovel coal into the compiler, and... you find yourself looking at a string of gibberish.

Don't panic! Pony's error messages try to be as helpful as possible and the ultimate goal is to improve them further. But, in the meantime, they can be a little intimidating.

This section tries to provide a short bestiary of Pony's error messages, along with a guide to understanding them.

Let's start with a simple one.

## left side must be something that can be assigned to

Suppose you wrote:

```pony
actor Main
  let x: I64 = 0
  new create(env: Env) =>
    x = 12
```

The error message would be:

```
Error:
.../a.pony:6:5: can't assign to a let or embed definition more than once
    x = 12
    ^
Error:
.../a.pony:6:7: left side must be something that can be assigned to
    x = 12
      ^
```

What happened is that you declared `x` as a constant, by writing `let x`, and then tried to assign a new value to it, 12. To fix the error, replace `let` with `var` or reconsider what value you want `x` to have.

That one error resulted in two error messages. The first, pointing to the `x`, describes the specific problem, that `x` was defined with `let`. The second, pointing to the `=` describes a more general error, that whatever is on the left side of the assignment is not something that can be assigned to. You would get that same error message if you attempted to assign a value to a literal, like `3`.

## cannot write to a field in a box function

Suppose you create a class with a mutable field and added a method to change the field:

```pony
class Wombat
  var color: String = "brown"
  fun dye(new_color: String) =>
    color = new_color
```

The error message would be:

```
Error:
.../a.pony:4:11: cannot write to a field in a box function. If you are trying to change state in a function use fun ref
    color = new_color
          ^
```

To understand this error message, you have to have some background. The field `color` is mutable since it is declared with `var`, but the method `dye` does not have an explicit receiver reference capability. The default receiver reference capability is `box`, which allows `dye` to be called on any mutable or immutable `Wombat`; the `box` reference capability says that the method may read from but not write to the receiver. As a result, it is illegal to attempt to modify the receiver in the method.

To fix the error, you would need to give the `dye` method a mutable reference capability, such as `ref`: `fun ref dye(new_color: String) => ...`.

## receiver type is not a subtype of target type

Suppose you made a related, but slightly different error:

```pony
class Rainbow
  let colors: Array[String] = Array[String]
  fun add_stripe(color: String) =>
    colors.push(color)
```

In this example, rather than trying to change the value of a field, the code calls a method which attempts to modify the object referred to by the field.

The problem is very similar to that of the last section, but the error message is significantly more complicated:

```
Error:
../a.pony:4:16: receiver type is not a subtype of target type
    colors.push(color)
               ^
    Info:
    .../a.pony:4:5: receiver type: this->Array[String val] ref
        colors.push(color)
        ^
    .../ponyc/packages/builtin/array.pony:252:3: target type: Array[String val] ref
      fun ref push(value: A): Array[A]^ =>
      ^
    .../a.pony:2:15: Array[String val] box is not a subtype of Array[String val] ref: box is not a subtype of ref
      let colors: Array[String] = Array[String]()
                  ^
```

Once again, Pony is trying to be helpful. The first few lines describe the error, in general terms that only a programming language maven would like: an incompatibility between the receiver type and the target type. However, Pony provides more information: the lines immediately after "Info:" tell you what it believes the receiver type to be and the next few lines describe what it believes the target type to be. Finally, the last few lines describe in detail what the problem is.

Unfortunately, this message does not locate the error as clearly as the previous examples.

Breaking it down, the issue seems to be with the call to `push`, with the receiver `colors`. The receiver type is `this->Array[String val] ref`; in other words, the view that this method has of a field whose type is `Array[String val] ref`. In the class `Rainbow`, the field `colors` is indeed declared with the type `Array[String]`, and the default reference capability for `String`s is `val` while the default reference capability for `Array` is `ref`. 

The "target type" in this example is the type declaration for the method `push` of the class `Array`, with its type variable `A` replaced by `String` (again, with a default reference capability of `val`). The reference capability for the overall array, as required by the receiver reference capability of `push`, is `ref`. It seems that the receiver type and the target type should be pretty close.

But take another look at the final lines: what Pony thinks is the actual receiver type, `Array[String val] box`, is significantly different from what it thinks is the actual target type, `Array[String val] ref`. And a type with a reference capability of `box`, which is immutable, is indeed not a subtype of a type with a reference capability of `ref`, which *is* mutable.

The issue must lie with the one difference between the receiver type and the target type, which is the prefix "this->". The type `this->Array[String val] ref` is a viewpoint adapted type, or arrow type, that describes the `Array[String val] ref` "as seen by the receiver". The receiver, in this case, has the receiver reference capability of the method `add_stripe`, which is the default `box`. *That* is why the final type is `Array[String val] box`.

The fundamental error in this example is the same as the last: the default receiver reference capability for a method is `box`, which is immutable. This method, however, is attempting to modify the receiver, by adding another color stripe. That is not legal at all.

As an aside, while trying to figure out what is happening, you may have been misled by the declaration of the `colors` field, `let colors...`. That declaration makes the `colors` binding constant. As a result, you cannot assign a new array to the field. On the other hand, the array itself can be mutable or immutable. In this example, it is mutable, allowing `push` to be called on the `colors` field in the `add_stripe` method.

## A note on compiler versions

The error messages shown in this section are from ponyc 0.2.1-1063-g6ae110f [release], the current head of the master branch at the time this is written. The messages from other versions of the compiler may be different, to a greater or lesser degree.

