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
