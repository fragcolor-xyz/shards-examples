# Step 1

## Basic file structure

In this tutorial, we will program using a Clojure-like syntax and an EDN-like format (JSON equivalent for Clojure). The similarity of Shards' syntax to Clojure means more expressive code in fewer lines (as compared to traditional programming languages like Javascript or Python). Additionally, the code is more focused on data which makes it easier for beginners to grasp.

A simple program in Shards would look like this:

=== "EDN"

    ```clojure linenums="1"
    ; define a wire named 'main-wire'
    (defwire main-wire
      ; print a message on the standard output
      (Msg "Hello World!"))

    ; define the root mesh
    (defmesh root)
    ; schedule the wire on that mesh
    (schedule root main-wire)
    ; run
    (run root)
    ```

To run the program, execute the following from the command line:

=== "Windows"

    ```powershell
    .\cbl.exe <path\to\program.edn>
    ```

=== "Unix"

    ```bash
    ./cbl path/to/program.edn
    ```

=== "Output"

    ```
    [info] [2021-11-12 16:12:52.712] [T-13596] [logging.cpp::94] [main-wire] Hello World!
    ```

## Game loop

Contrary to simple programs (e.g. a command-line utility) that execute an operation and exit afterwards, a game typically runs for a longer time.
Most games have the concept of a "game loop".

A game loop is the overall flow control of the program that runs continuously during gameplay. In each turn of the loop (known as a frame), the program processes the user's input, updates the game state, and renders the game.

In Shards, a wire (self-contained piece of code made that can be suspended/resumed and is made up of shards and functions) can be transformed into a loop by simply replacing `(defwire)` with `(defloop)`. Additionally, `(run)` takes a second argument to specify the delay between two consecutive executions of the loop, specifying the frame rate (FPS). Games tend to run at 60 FPS (or 60 Hz).

=== "EDN"

    ```clojure linenums="1"
    (defloop main-wire
      (Msg "Hello World!"))

    (defmesh root)
    (schedule root main-wire)
    (run root (/ 1.0 60))
    ```

=== "Output"

    ```
    [info] [2021-11-12 16:25:54.487] [T-32668] [logging.cpp::94] [main-wire] Hello World!
    [info] [2021-11-12 16:25:54.514] [T-32668] [logging.cpp::94] [main-wire] Hello World!
    [info] [2021-11-12 16:25:54.530] [T-32668] [logging.cpp::94] [main-wire] Hello World!
    [info] [2021-11-12 16:25:54.546] [T-32668] [logging.cpp::94] [main-wire] Hello World!
    [info] [2021-11-12 16:25:54.562] [T-32668] [logging.cpp::94] [main-wire] Hello World!
    ...
    ```

--8<-- "includes/license.md"
