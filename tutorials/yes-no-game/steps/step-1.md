# Step 1

This chapter will guide you on setting up the base of your game so that it can start running.

## The Program's Base

A basic Shards program consists of:

- Writing Shards and Wires

- Scheduling Wires onto a Mesh

To learn more about Wires, Meshes, and the overall flow of Shards, check out the Shards Primer [here](https://docs.fragcolor.com/learn/shards/).

A basic Shards program looks like this:

=== "Code"
    
    ```shards
    @wire( game-wire {       ;; Define a Wire named "game-wire" (1)
        Msg("Hello World!")
    } Looped: false)   ;; Insert stuff to run here (2)

    @mesh(main)              ;; Define a Mesh named "main" (3)
    @schedule(main game-wire)   ;; Schedule the Wire on the Mesh (4)
    @run(main)                  ;; Run the Mesh (5)
    ```

    1. [`@wire`](https://docs.fragcolor.com/reference/shards/built-ins/execution/#@wire) is a macro used to define a Wire.
    2. [`Msg`](https://docs.fragcolor.com/reference/shards/shards/General/Msg/) prints out the string passed into it.
    3. [`@mesh`](https://docs.fragcolor.com/reference/shards/built-ins/execution/#@mesh) is a macro used to define a Mesh.
    4. [`@schedule`](https://docs.fragcolor.com/reference/shards/built-ins/execution/#@schedule) queues a Wire on the Mesh.
    5. [`@run`](https://docs.fragcolor.com/reference/shards/built-ins/execution/#@run) executes Wires on the Mesh.

For the program to run continuously, we will be using a looped Wire instead. This can be done by setting the `Looped` parameter as true. You can then adjust the time interval between each loop by adding a second argument to `@run`.

Since most games run at 60 FPS (Frames per Second), we will set the `FPS` parameter to 60.

=== "Code"
    
    ```shards
    @wire(game-loop {         ;; Define a Looped Wire named "game-loop"
        Msg("Hello World!")
    } Looped: true)           ;; Insert stuff to run here 

    @mesh(main)               ;; Define a Mesh named "main"
    @schedule(main game-loop) ;; Schedule the Loop on the Mesh
    @run(main FPS: 60)        ;; Run the Mesh
    ```

## Game Loops 

To make a game, you will need:

- Code to draw the UI (such as your game menus, text boxes, and images)

- Code to run the game's logic (such as calculating the score, determining when to end a round)

It might be quite disorderly if we did everything within a single wire. 

To keep our code easily readable and organized, we split the UI and logic code into separate Loops running together on the `Mesh`. 

We can achieve this by employing the `Branch` shard and creating two separate loops for the game's logic and UI.

??? "Branch"
    [`Branch`](https://docs.fragcolor.com/reference/shards/shards/General/Branch/) creates offshoot from the current mesh and schedules Wires to run on it.

=== "Code"
    
    ```shards
    @wire(ui-loop {Pass} Looped: true)                   ;; Insert UI code here
    @wire(logic-loop {Pass} Looped: true)                ;; Insert logic code here

    @wire(game-loop{
        Branch(Wires:[ui-loop, logic-loop])
    } Looped: true)

    @mesh(main)
    @schedule(main game-loop)
    @run(main FPS: 60) 
    ```

## The Initialization Zone

Most games will require code to ready the program, such as by loading resources and setting up variables to use. 

We can ready `@define`s, `load-resources` and `initialize-variables` to carry out these tasks. Place them in a `Once` shard within the `game-loop` to ensure that they only run once.

??? "Variables"
    Variables are containers that store values. You define a variable with a name, and assign a value to it. The value can then be retrieved by calling the variable with its assigned name.

??? "Once"
    The [`Once`](https://docs.fragcolor.com/reference/shards/shards/General/Once/) shard will only be executed once, even within a Looped Wire. This makes it ideal to hold code that is only used once to ready the game. 

=== "Full Code"
    
    ```shards
    @define(load-resources {Pass})              ;; Load resources here (1)
    @define(initialize-variables {Pass})        ;; Ready variables here
    @wire(ui-loop {Pass} Looped: true)          ;; Insert UI code here
    @wire(logic-loop {Pass} Looped: true)       ;; Insert logic code here

    @wire(game-loop {
        Once({
            @load-resources
            @initialize-variables
        })                                      ;; (2)
        Branch(Wires:[ui-loop, logic-loop])
    } Looped: true)

    @mesh(main)
    @schedule(main game-loop)
    @run(main FPS: 60) 
    ```

    1. [`@define`](https://docs.fragcolor.com/reference/shards/built-ins/macros-templating/#@define) allows multiple shards to be grouped together into a single shard. We will be placing shards that load resources into `load-resources` for example.
    2. The shards to load resources and initialize variables are only run once in the game loop

The basic game loop is now ready!

In the next chapter, we will learn about drawing the User Interface and adding graphics.

--8<-- "includes/license.md"
