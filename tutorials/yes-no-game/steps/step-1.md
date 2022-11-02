# Step 1

## The Program's Base

A basic Shards program consists of writing Wires and scheduling them onto a Mesh. To learn more about Wires, Meshes, and the overall flow of Shards, check out the primer [here](https://docs.fragcolor.xyz/learn/shards/).

A basic Shards program would look like this:

=== "Code"

    ```{.clojure .annotate linenums="1"}
    (defwire game-wire          ; (1) Define a Wire named "game-wire"
        (Msg "Hello World!"))   ; (2) Insert stuff to run here

    (defmesh main)              ; Define a Mesh named "main"
    (schedule main game-wire)   ; (3) Schedule the Wire on the Mesh
    (run main)                  ; (4) Run the Mesh
    ```

    1. [`defwire`](https://docs.fragcolor.xyz/docs/functions/macros/#defwire), [`defloop`](https://docs.fragcolor.xyz/docs/functions/macros/#defloop) and [`defmesh`](https://docs.fragcolor.xyz/docs/functions/macros/#defmesh) are macros used to define a Wire, Looped Wire and Mesh respectively.
    2. [`Msg`](https://docs.fragcolor.xyz/docs/shards/General/Msg/) prints out the string passed into it.
    3. [`schedule`](https://docs.fragcolor.xyz/docs/functions/misc/#schedule) queues a Wire on the Mesh.
    4. [`run`](https://docs.fragcolor.xyz/docs/functions/misc/#run) executes Wires on the Mesh.

In order to have the program run continuously to keep the game running, we will be using a looped Wire instead. This can be done by replacing `defwire` with `defloop`. `(run)` can take a second argument to specify the time interval between each loop.

Since most games run at 60 FPS (Frames per Second), we will be using `(/ 1.0 60.0)` which reads as "1 divided by 60" to get the time interval between each frame to achieve a 60 FPS loop.

=== "Code"

    ```clojure linenums="1"
    (defloop game-loop          ; Define a Looped Wire named "game-loop"
        (Msg "Hello World!"))   ; Insert stuff to run here

    (defmesh main)              ; Define a Mesh named "main"
    (schedule main game-loop)   ; Schedule the Loop on the Mesh
    (run main (/ 1.0 60.0))     ; Run the Mesh
    ```

## Game Loops

To make a game, you will need code to draw the UI (such as your game menus, text boxes, images, etc.), and code to run the game's logic. It might be quite disorderly if we did everything within a single `defloop`.

In order to keep our code easily readable and organized, it is advisable to split your UI and logic code into separate Loops running together on the Mesh. We can achieve this by employing the `Branch` shard and creating two separate loops for the game's logic and UI.

=== "Code"

    ```{.clojure .annotate linenums="1"}
    (defloop ui-loop)                   ; Insert UI code here
    (defloop logic-loop)                ; Insert logic code here

    (defloop game-loop
        (Branch [ui-loop, logic-loop])) ; (1)

    (defmesh main)
    (schedule main game-loop)
    (run main (/ 1.0 60.0))
    ```

    1. [`Branch`](https://docs.fragcolor.xyz/docs/shards/General/Branch/) creates a mini-Mesh of sorts and schedules Wires to run on it.

## The Setup Zone

Most games will also require code to prepare itself, such as by loading resources and setting up variables to use.

We can ready empty shards `load-resources` and `initialize-variables` that will carry out these tasks. To ensure that they only run once, we place them in a `Setup` shard within the `game-loop`.

!!! note "Variables"
    Variables are containers that store values. You define a variable with a name, and assign a value to it. The value can then be retrieved by calling the variable with its assigned name.

=== "Code"

    ```{.clojure .annotate linenums="1"}
    (defshards load-resources [] nil)       ; (1) Load resources here
    (defshards initialize-variables [] nil) ; Ready variables here
    (defloop ui-loop)                       ; Insert UI code here
    (defloop logic-loop)                    ; Insert logic code here

    (defloop game-loop
        ; the shards to load resources and initialize variables
        ; are only run once in the game loop
        (Setup (load-resources)(initialize-variables)) ; (2)
        (Branch [ui-loop, logic-loop]))

    (defmesh main)
    (schedule main game-loop)
    (run main (/ 1.0 60.0))
    ```

    1. [`defshards`](https://docs.fragcolor.xyz/docs/functions/macros/#defshards) allows multiple shards to be grouped together into a single shard.
    2. The [`Setup`](https://docs.fragcolor.xyz/docs/shards/General/Once/) shard will only be executed once, even within a Looped Wire. This makes it ideal to hold code that is only used once to ready the game.

The basic game loop is now ready!

--8<-- "includes/license.md"
