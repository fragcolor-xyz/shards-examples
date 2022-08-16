# What is Shards?

Shards is a highly flexible and powerful scripting language designed from the ground-up for developing games and for exploring creative ideas.

It's Lisp-inspired syntax is easy to read and reason in, and makes programming in Shards feel like working with a [visual programming language](https://en.wikipedia.org/wiki/Visual_programming_language).

## Shard

In Shards, the data transformation logic blocks are called [shards](https://docs.fragcolor.xyz/shards/) (with a lowercase *'s'*).

For example, the shard [`(Math.Sqrt)`](https://docs.fragcolor.xyz/shards/Math/Sqrt/) can compute the square root of a number.

Figure 4

![Shard computes square root](assets/a-shard-1.png)

And the shard [`(Log)`](https://docs.fragcolor.xyz/shards/General/Log/) can print given data to the terminal screen.

Figure 5

![Shard prints data to terminal display](assets/a-shard-2.png)

Multiple shards maybe combined together to achieve a particular objective.

For example, suppose we want to print the square root of the square root of a given number to the terminal screen.

We could do this with a combination of `(Math.Sqrt)` and `(Log)` shards like this:

Figure 6

![Shard prints data to terminal display](assets/a-shard-3.png)

## Wire

A sequence of shards that work together (like in Figure 6 above), along with its related data (input, intermediate results, output, etc.) is called a *wire* in Shards.

These wire constructs need to be identified by a unique name as you may have more than one wire in a Shards script.

The figure below depicts a wire made up of the shard sequence from Figure 6 and is called *"mywire"*.

Figure 7

![Shard sequence with related data is a wire](assets/a-wire-1.png)

### Non-looped wire

A wire that's supposed to execute only once is called a non-looped wire and is created by using the macro `(defwire)`.

Such a wire is useful for writing out logic that you want to execute only once everytime you run the Shards script.

??? note
    A macro is a part of the Shards composer language Mal/EDN, as explained in [macros](#macros) below.

You can visualize a non-looped wire as a broken necklace (but with its beads in place), laid out end to end. The beads here represent shards and the thread itself represents the wire (i.e., the flow of the data through the shards).

The execution starts at first shard and continues till the last shard is executed. The output of the wire maybe stored and/or transferred to other constructs in your Shards script.

In the figure below we visualize the wire *"mywire"* as a non-looped wire.

Figure 8

![Shard sequence with related data is a wire](assets/a-wire-2.png)

### Looped wire

A wire that executes continuously in a loop is called a looped wire and is created by using the macro `(defloop)`.

Such a wire may execute a finite or infinite number of loops depending on its parameters (like maximum loop iterations, time interval between two consecutive loop iterations, etc.).

Such a wire is useful when you want to execute some logic continously once you trigger your Shards script. This is a frequent use-case in game development.

You can visualize a looped wire as a necklace with beads. The beads here represent shards and the thread itself represents the wire (i.e., the flow of the data through the shards).

Once the last shard in the sequence is executed the control transfers again to the first shard in the sequence (hence the looped execution). The output of one iteration may be consumed again by the wire itself in the next iteration or transferred to other constructs in your Shards script.

In the figure below we visualize the wire *"mywire"* as a looped wire.

Figure 8

![Shard sequence with related data is a wire](assets/a-wire-3.png)

## Mesh
...explain mesh; equate to a lego board

??? note "Tech tip"
    - Shards are logic blocks that compose the wire
    - A wire is a coroutine or a stateful smart function that is made up of a sequence of shards and can persist its memory across wire execution/iterations
    - A mesh is a thread scheduler that schedules and executes the wire coroutines

## Language layers

The Shards language is made up of two layers:

- Inner layer - the core language
- Outer layer - the composer language

### The core language

The inner (core) layer is the actual Shards language.

It's the collection of all the existing , and the related keywords/syntax that allows you to invoke,combine, and manipulate these shards.

These logic blocks can be further combined to form logic sequences (wires) which are then scheduled and run in a software context (mesh).

A Shards script (or source program) should contain at least one shard, a wire, and a mesh.

### The composer language


If place the 

### The Mal/EDN layer
[appendix D - Mal/EDN](#appendix-d-maledn)

## Parsing and execution



### Compose phase
using `(schedule)`

### Execution phase
using `(run)`

### Macros

`(def)`
`(defshard)`
`(defwire)`
`(defloop)`
`(defmesh)`


## Some code examples

Let's take all the concepts covered in this section till now and illustrate them via some real Shards code. 

!!! note
    1. Shards is the programming language that we are discussing here whereas a *shard* is (written with a lower-case *'s'*) is an inbuilt utility that allows you to transform data.
    2. Shards includes over 500 shards and around 100 functions allowing you to manipulate data and compose logic the way that you want.
    3. With a few exceptions, all shard names use [Pascal case](https://en.wiktionary.org/wiki/Pascal_case) (like `(ChaChaPoly)`).

To get an idea of what functions and shards look like let's look at a function and a shard that do the same thing - i.e., print some data to the terminal screen.

??? note "How to run Shards sample code from this primer"
    1. If you're new to programming you should [install VS Code and set it up](#-code-editor). 
    2. The Shards [web sandbox](https://learn.fragcolor.xyz/sandbox) is coming soon but for now please [build Shards](https://docs.fragcolor.xyz/contribute/code/building-shards/) on your machine to work through the examples in this guide.
    3. Once the `shards.exe` is built, copy paste the code sample you want to run from this guide into a new file with an extension `edn` (that's the Shards script file extension for now). This is your Shards script file. 
    4. Place your Shards script (say`abc.edn`) into the`/build` folder and from there run the script `./shards <abc.edn>` to execute your Shards script.
    5. You just need to build the `shards.exe` once. After that you can change the code in your Shards script (or create a new Shards script) and run that script with the `./shards...` command
    6. If you're using [VS Code](https://code.visualstudio.com/) as your editor you can install the [code-runner plugin](https://marketplace.visualstudio.com/items?itemName=formulahendry.code-runner) and set up your VS Code to execute your Shards script on the click of the `Run` button (see [this](https://docs.fragcolor.xyz/contribute/code/building-shards/#verify-build-and-run) for details).


`(println)` is a function that prints the data (that you passed to it in double quotes) to the terminal screen (you can copy and run *Code example 1* as explained in the note above):

*Code example 1*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (println "I'm a function!")
    ```
    
=== "Result"

    ```
    I'm a function!
    ```

While `(Msg)` is a shard that does the exact same thing (we haven't yet introduced wires and meshes so *Code example 2* won't run standalone; to run the full code sample see the note after the code fragment):

*Code example 2*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (Msg "I'm a shard!")
    ```
    
=== "Result"

    ```
    I'm a shard!
    ```


??? Full `(Msg)` code sample
    === "EDN"

        ```{.clojure .annotate linenums="1"}
        (defmesh mesh)
        (defwire wire
            (Msg "I'm a shard!")
            )
        (schedule mesh wire)
        (run mesh)
        ```
        
    === "Result"

        ```
        [info] [2022-08-15 13:49:15.552] [T-11856] [logging.cpp::98] [wire] I'm a shard!
        ```


As you can see:

- The function name is in kebab case
- The shard name is in Pascal case
- Both the function and shard take an input (the data passed to them in double quotes) and transform it (i.e., print it to the terminal screen)

### The duality

The function `(prn)` and the shard `(Msg)` seem to be doing 

Now let's run some Shards code!



Since functions are native entities, they can be run as they are.

[`(println)`](https://docs.fragcolor.xyz/functions/standard-output/#println) is a function that prints to the terminal whatever you pass to it.

In the following example, let's pass the text "Hello World" to it and run the code (copy-paste and run this code in your editor).

*Code example 1*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (println "Hello World")
    ```
    
=== "Result"

    ```
    Hello World 
    ```

Now let's try the same with the shard [`(Msg)`](https://docs.fragcolor.xyz/shards/General/Msg/), which is similar in functionality to the function `(println)`.

*Code example 2*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (Msg "Hello World")
    ```
    
=== "Result"

    ```
    ```

But this didn't work! There is no output.

This is because, unlike a function, a shard needs additional software context to run. These software constructs are called *wires* and *meshes*.

Now, let's execute `(Msg)` with the required wire and mesh context, and you should see the output appear.

*Code example 3*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (defmesh root)
    (defwire mywire
        (Msg "Hello World")
        )
    (schedule root mywire)
    (run root)
    ```
    
=== "Result"

    ```
    [info] [2022-07-21 16:16:15.648] [T-29488] [logging.cpp::98] [mywire] Hello World
    ```

## Shards, wires, and meshes

To understand why the last code example worked, let's see what's needed to execute a shard:

* You need to first queue it on a *wire* (a software construct that can hold a sequence of shards) - In the code above function [`(defwire)`](https://docs.fragcolor.xyz/functions/macros/#defwire) creates a wire by the name 'mywire' at line no. 2. This wire is enclosed in a set of parentheses (line no. 2 and 4) and houses the shard `(Msg)` at line no. 3.
* Next, you need to schedule the wire on a *mesh* (a software construct that can hold many wires for execution) - A mesh named 'root' is created by function [`(defmesh)`](https://docs.fragcolor.xyz/functions/macros/#defmesh) at line no. 1. And then the function [`(schedule)`](https://docs.fragcolor.xyz/functions/misc/#schedule) schedules wire 'mywire' on mesh 'root' for execution.
* Once all wires are scheduled on a mesh, you need to *run* the mesh - The function [`(run)`](https://docs.fragcolor.xyz/functions/misc/#run) invokes the mesh 'root' for execution at line no. 6. This invocation leads to the execution of the wires scheduled on that mesh, and hence the execution of the shards contained within each wire

Of course, in the above example, we have just one shard and one wire - but each wire can have multiple shards (sequence of shards that execute in order), and each mesh may have multiple wires scheduled on it for execution.




!!! note
    Pick up two necklaces in one hand and start counting the beads on both - and you have the case of a single mesh running multiple wires! Such wires can be independently executed and/or paused and can even share state (memory variables).

The following code is an example of a single non-looped wire ('mywire') scheduled and then executed on a mesh ('root').

*Code example 4*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (defmesh root)
    (defwire mywire
        (Msg "Hello World")
        )
    (schedule root mywire)
    (run root)
    ```
    
=== "Result"

    ```
    [info] [2022-07-21 16:16:15.648] [T-29488] [logging.cpp::98] [mywire] Hello World
    ```

## Loops and non-loops

'mywire' in *Code example 4* is a non-looped wire and that's the reason `(run)` doesn't need any other parameters other than just the name of the mesh to run (which is 'root' in this case). Since this is a non-looped wire example, the wire executes only once and that's why you see just one run of the output.

Let's spice up things a bit - change the `defwire` function in *Code example 4* to `defloop` and run the code again.

*Code example 5*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (defmesh root)
    (defloop mywire
        (Msg "Hello World")
        )
    (schedule root mywire)
    (run root)
    ```
    
=== "Result"

    ```
    [info] [2022-07-21 22:00:37.309] [T-15968] [logging.cpp::98] [mywire] Hello World
    [info] [2022-07-21 22:00:37.312] [T-15968] [logging.cpp::98] [mywire] Hello World
    [info] [2022-07-21 22:00:37.314] [T-15968] [logging.cpp::98] [mywire] Hello World
    [info] [2022-07-21 22:00:37.315] [T-15968] [logging.cpp::98] [mywire] Hello World
    [info] [2022-07-21 22:00:37.317] [T-15968] [logging.cpp::98] [mywire] Hello World
    [info] [2022-07-21 22:00:37.319] [T-15968] [logging.cpp::98] [mywire] Hello World
    [info] [2022-07-21 22:00:37.320] [T-15968] [logging.cpp::98] [mywire] Hello World
    [info] [2022-07-21 22:00:37.322] [T-15968] [logging.cpp::98] [mywire] Hello World
    .
    .
    .
    ```

You'll see the wire executing continuously in a loop (go to  your terminal and hit the `Ctrl+C` key on Windows to break the loop; `command + .` if you're on a Mac).
This is because we defined 'mywire' as a looped wire and hence it now needs to be controlled via some extra parameters in the `(run)` command.

To handle looped wires `(run)` takes two more parameters that are placed after the name of the mesh. The first parameter specifies the time (in seconds) between wire iterations and the second parameter sepcifies the maximum number of runs allowed for the wire.

So `(run root 1 3)` will execute the wire three times with an interval of 1 second between iterations, as illustrated in the following code example.

*Code example 6*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (defmesh root)
    (defloop mywire
        (Msg "Hello World")
        )
    (schedule root mywire)
    (run root 1 3)
    ```
    
=== "Result"

    ```
    [info] [2022-07-21 22:11:55.167] [T-14432] [logging.cpp::98] [mywire] Hello World
    [info] [2022-07-21 22:11:56.174] [T-14432] [logging.cpp::98] [mywire] Hello World
    [info] [2022-07-21 22:11:57.181] [T-14432] [logging.cpp::98] [mywire] Hello World
    ```

The iteration control parameters for `(run)` don't affect the non-looped (`(defwire)`) wires.

??? note Game loops
    (`defloop`) can be used to easily implment game loops (i.e.) game code that needs to run periodically

Before we end this section let's take two examples of multiple wires:

- *Code example 7* has two non-looped wires
- *Code example 8* has one looped and one non-looped wire

!!! note
    Try to anticipate the output of these programs and then confirm your assumptions by running the program in your terminal. Change the code to play around with the order of the shards, the wire schedules, and `run` control parameters. Try this out with every example code in this primer. This exercise will help you build your intuition about how Shards works.

*Code example 7*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (defmesh root)

    (defwire mywire1
        (Msg "Hello World")
        (Msg "Bye World")
        )

    (defwire mywire2
        (Msg "Hello Universe")
        (Msg "Bye Universe")
        )

    (schedule root mywire1)
    (schedule root mywire2)
    (run root)
    ```
    
=== "Result"

    ```
    [info] [2022-07-21 22:16:58.691] [T-11720] [logging.cpp::98] [mywire1] Hello World
    [info] [2022-07-21 22:16:58.703] [T-11720] [logging.cpp::98] [mywire1] Bye World
    [info] [2022-07-21 22:16:58.705] [T-11720] [logging.cpp::98] [mywire2] Hello Universe
    [info] [2022-07-21 22:16:58.706] [T-11720] [logging.cpp::98] [mywire2] Bye Universe
    ```

*Code example 7*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (defmesh root)

    (defwire nonloopedwire
        (Msg "Hello World, just once!"))

    (defloop loopedwire
        (Msg "Hello World, on repeat!"))

    (schedule root nonloopedwire)
    (schedule root loopedwire)
    (run root)
    ```
    
=== "Result"

    ```
    [info] [2022-07-28 19:16:57.985] [T-40588] [logging.cpp::98] [nonloopedwire] Hello World, just once!
    [info] [2022-07-28 19:16:57.995] [T-40588] [logging.cpp::98] [loopedwire] Hello World, on repeat!
    [info] [2022-07-28 19:16:58.993] [T-40588] [logging.cpp::98] [loopedwire] Hello World, on repeat!
    [info] [2022-07-28 19:16:59.989] [T-40588] [logging.cpp::98] [loopedwire] Hello World, on repeat!
    ```

One last example, before we move on to the next section, to show how we can use both functions and shards together (of course, the shards still need to be inside a wire).

!!! note
    1. In *Code example 8* we're using the shard [`(Log)`](https://docs.fragcolor.xyz/shards/General/Log/) which takes an input (coming from the left of the shard) and prints that to the screen. This is in contrast with `(Msg)` which doesn't accept any input but instead prints the value of its parameter (parameters stay inside the parentheses with the shard). More on this in the [Anatomy of a shard](../anatomy-shard/#anatomy-of-a-shard) section.
    2. [`(def)`](https://docs.fragcolor.xyz/functions/macros/#def) is a function that defines an alias (or a name) for a value, and `(Log)` accesses that value ("Hello World) via its alias ('greeting') and prints it to the terminal.

*Code example 8*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (println "Start script execution")
    (def greeting "Hello World")

    (defmesh root)
    
    (defwire mywire
        greeting (Log)
        )
    
    (schedule root mywire)
    (run root)
    
    (println "End script execution")
    ```
    
=== "Result"

    ```
    Start script execution
    [info] [2022-07-21 22:43:39.904] [T-1512] [logging.cpp::55] [mywire] Hello World
    End script execution
    ```


--8<-- "includes/license.md"
