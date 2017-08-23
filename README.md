# Pony_Documentation

#### What is Pony?
Pony is an open-source, object-oriented, actor-model, capabilities-secure, high-performance programming language.

#### Why is it different?
- **Pony is type safe**: Really type safe. There’s a mathematical proof and everything.
- **Pony is memory safe:** There are no dangling pointers and no buffer overruns. The language doesn’t even have the concept of null!
- **Exception-Safe:** There are no runtime exceptions. All exceptions have defined semantics, and they are always caught.
- **Data-race Free:** Pony doesn’t have locks or atomic operations or anything like that. Instead, the type system ensures at compile time that your concurrent program can never have data races. So you can write highly concurrent code and never get it wrong.
- **Deadlock-Free**: This one is easy because Pony has no locks at all! So they definitely don’t deadlock, because they don’t exist!
- **Native Code**: Pony is an ahead-of-time (AOT) compiled language. There is no interpreter or virtual machine.
- **Compatible with C#**: Pony programs can natively call C libraries. Our compiler is able to generate a C-header file for Pony libraries. Consequently, C/C++ programs can natively call Pony programs!


#### Your first program
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
