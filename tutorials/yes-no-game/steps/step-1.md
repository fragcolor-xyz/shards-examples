# Step 1

This chapter will guide you on setting up the base of your game so that it can start running.

## The Program's Base

A basic Shards program consists of:

- Writing Shards and Wires

- Scheduling Wires onto a Mesh

To learn more about Wires, Meshes, and the overall flow of Shards, check out the primer [here](https://docs.fragcolor.xyz/learn/shards/).

A basic Shards program looks like this:

=== "Code"
    
    ```{.clojure .annotate linenums="1"}
    (defwire game-wire          ;; Define a Wire named "game-wire" (1)
        (Msg "Hello World!"))   ;; Insert stuff to run here (2)

    (defmesh main)              ;; Define a Mesh named "main" (3)
    (schedule main game-wire)   ;; Schedule the Wire on the Mesh (4)
    (run main)                  ;; Run the Mesh (5)
    ```

    1. [`defwire`](https://docs.fragcolor.xyz/docs/functions/macros/#defwire) is a macro used to define a Wire.
    2. [`Msg`](https://docs.fragcolor.xyz/docs/shards/General/Msg/) prints out the string passed into it.
    3. [`defmesh`](https://docs.fragcolor.xyz/docs/functions/macros/#defmesh) is a macro used to define a Mesh.
    4. [`schedule`](https://docs.fragcolor.xyz/docs/functions/misc/#schedule) queues a Wire on the Mesh.
    5. [`run`](https://docs.fragcolor.xyz/docs/functions/misc/#run) executes Wires on the Mesh.

For the program to run continuously, we will be using a looped Wire instead. This can be done by replacing `defwire` with `defloop`. You can then adjust the time interval between each loop by adding a second argument to `(run)`.

Since most games run at 60 FPS (Frames per Second), we will be using `(/ 1.0 60.0)`  to get the time interval between each frame.

!!! note
    `(/ 1.0 60.0)` reads as "1 divided by 60".

=== "Code"
    
    ```{.clojure .annotate linenums="1"}
    (defloop game-loop          ;; Define a Looped Wire named "game-loop" (1)
        (Msg "Hello World!"))   ;; Insert stuff to run here 

    (defmesh main)              ;; Define a Mesh named "main"
    (schedule main game-loop)   ;; Schedule the Loop on the Mesh
    (run main (/ 1.0 60.0))     ;; Run the Mesh
    ```

    1. [`defloop`](https://docs.fragcolor.xyz/docs/functions/macros/#defloop) is a macro used to define a Loop.

## Game Loops 

To make a game, you will need:

- Code to draw the UI (such as your game menus, text boxes, images)

- Code to run the game's logic (such as calculating score, determining when to end a round)

It might be quite disorderly if we did everything within a single `defloop`. 

In order to keep our code easily readable and organized, we split the UI and logic code into separate Loops running together on the Mesh. 

We can achieve this by employing the `Branch` shard and creating two separate loops for the game's logic and UI.

??? "Branch"
    [`Branch`](https://docs.fragcolor.xyz/docs/shards/General/Branch/) creates a mini-Mesh of sorts and schedules Wires to run on it.

=== "Code"
    
    ```{.clojure .annotate linenums="1"}
    (defloop ui-loop)                   ;; Insert UI code here
    (defloop logic-loop)                ;; Insert logic code here

    (defloop game-loop
        (Branch [ui-loop, logic-loop]))

    (defmesh main)
    (schedule main game-loop)
    (run main (/ 1.0 60.0))
    ```

## The Setup Zone

Most games will require code to ready the program, such as by loading resources and setting up variables to use. 

We can ready empty shards `load-resources` and `initialize-variables` to carry out these tasks. Place them in a `Setup` shard within the `game-loop` to ensure that they only run once.

??? "Variables"
    Variables are containers that store values. You define a variable with a name, and assign a value to it. The value can then be retrieved by calling the variable with its assigned name.

??? "Setup"
    The [`Setup`](https://docs.fragcolor.xyz/docs/shards/General/Once/) shard will only be executed once, even within a Looped Wire. This makes it ideal to hold code that is only used once to ready the game. 

=== "Full Code"
    
    ```{.clojure .annotate linenums="1"}
    (defshards load-resources [] nil)          ;; Load resources here (1)
    (defshards initialize-variables [] nil)    ;; Ready variables here
    (defloop ui-loop)                   ;; Insert UI code here
    (defloop logic-loop)                ;; Insert logic code here

    (defloop game-loop
        (Setup (load-resources)(initialize-variables)) ;; (2)
        (Branch [ui-loop, logic-loop]))

    (defmesh main)
    (schedule main game-loop)
    (run main (/ 1.0 60.0))
    ```

    1. [`defshards`](https://docs.fragcolor.xyz/docs/functions/macros/#defshards) allows multiple shards to be grouped together into a single shard. We will be placing shards that load resources into `load-resources` for example.
    2. The shards to load resources and initialize variables are only run once in the game loop

The basic game loop is now ready!

In the next chapter, we will learn about drawing the User Interface and adding graphics.

--8<-- "includes/license.md"
