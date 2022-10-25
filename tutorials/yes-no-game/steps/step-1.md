# Step 1

## Setting up the Game Loop

A basic Shards program consists of writing Wires and scheduling them onto a Mesh. To learn more about Wires, Meshes, and the overall flow of Shards, check out the primer [here](https://docs.fragcolor.xyz/learn/shards/).

A basic Shards program would look like this:

=== "Command"
    
    ```clojure linenums="1"
    (defwire game-wire          ; define a Wire named "game-wire"
        (Msg "Hello World!"))   ; insert stuff to run here 

    (defmesh main)              ; define a Mesh named "main"
    (schedule main game-wire)   ; schedule the Wire on the Mesh
    (run main)                  ; run the Mesh
    ```

??? "defwire, defloop and defmesh"
    [`defwire`](https://docs.fragcolor.xyz/docs/functions/macros/#defwire), [`defloop`](https://docs.fragcolor.xyz/docs/functions/macros/#defloop) and [`defmesh`](https://docs.fragcolor.xyz/docs/functions/macros/#defmesh) are macros used to define a Wire, Looped Wire and Mesh respectively.

??? "Msg"
    [`Msg`](https://docs.fragcolor.xyz/docs/shards/General/Msg/) prints out the string passed into it.

??? "schedule"
    [`schedule`](https://docs.fragcolor.xyz/docs/functions/misc/#schedule) queues a Wire on the Mesh.

??? "run"
    [`run`](https://docs.fragcolor.xyz/docs/functions/misc/#run) executes Wires on the Mesh.

In order to have the program run continuously to keep the game running, we will be using a looped Wire instead. This can be done by replacing `defwire` with `defloop`. `(run)` can take a second argument to specify the time interval between each loop. 

Since most games run at 60 FPS (Frames per Second), we will be using `(/ 1.0 60.0)` which reads as "1 divided by 60" to get the time interval between each frame to achieve a 60 FPS loop.

=== "Command"
    
    ```clojure linenums="1"
    (defloop game-loop          ; define a Looped Wire named "game-loop"
        (Msg "Hello World!"))   ; insert stuff to run here 

    (defmesh main)              ; define a Mesh named "main"
    (schedule main game-loop)   ; schedule the Loop on the Mesh
    (run main (/ 1.0 60.0))     ; run the Mesh
    ```

To make a game, you will need code to draw the UI (such as your game menus, text boxes, images, etc.), and code to run the game's logic. It might be quite disorderly if we did everything within a single `defloop`. 

In order to keep our code easily readable and organized, it is advisable to split your UI and logic code into separate Loops running together on the Mesh. We can achieve this by employing the `Branch` shard and creating two separate loops for the game's logic and UI.

??? "Branch"
    [`Branch`](https://docs.fragcolor.xyz/docs/shards/General/Branch/) creates a mini-Mesh of sorts and schedules Wires to run on it.

=== "Command"
    
    ```clojure linenums="1"
    (defloop ui-loop)                   ; insert UI code here
    (defloop logic-loop)                ; insert logic code here

    (defloop game-loop
        (Branch [ui-loop, logic-loop]))

    (defmesh main)
    (schedule main game-loop)
    (run main (/ 1.0 60.0))
    ```

Most games will also require code to prepare itself, such as by loading resources and setting up variables to use. 

We can ready empty shards `load-resources` and `initialize-variables` that will carry out these tasks and place them in a `Setup` shard in the `game-loop`.

??? "Variables"
    Variables are containers that store values. You define a variable with a name, and assign a value to it. The value can then be retrieved by calling the variable with its assigned name.

??? "Setup"
    The [`Setup`](https://docs.fragcolor.xyz/docs/shards/General/Once/) shard will only be executed once, even within a Looped Wire. This makes it ideal to hold code that is only used once to ready the game. 


=== "Command"
    
    ```clojure linenums="1"
    (defshards load-resources)          ; load resources here 
    (defshards initialize-variables)    ; ready variables here
    (defloop ui-loop)                   ; insert UI code here
    (defloop logic-loop)                ; insert logic code here

    (defloop game-loop
        ; the shards to load resources and initialize variables 
        ; are only run once in the game loop
        (Setup (load-resources)(initialize-variables))
        (Branch [ui-loop, logic-loop]))

    (defmesh main)
    (schedule main game-loop)
    (run main (/ 1.0 60.0))
    ```

The basic game loop is now ready!

--8<-- "includes/license.md"
