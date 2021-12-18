# Step 1

## Basic file structure

In this tutorial we will program using a Clojure-like syntax and an EDN-like format (JSON equivalent for Clojure). The similarity of Chainblock's syntax to Clojure means more expressive code in fewer lines (as compared to traditional programming languages like Javascript or Python). Additionally, the code is more focused on data which makes it easier for beginners to grasp.

A simple program in Chainblocks would look like this:

=== "EDN"

    ```clojure linenums="1"
    ; define a chain named 'main-chain'
    (defchain main-chain
      ; print a message on the standard output
      (Msg "Hello World!"))

    ; define the root node
    (defnode root)
    ; schedule the chain on that node
    (schedule root main-chain)
    ; run
    (run root)
    ```

To run the program, execute the following from the command line:

=== "Windows"

    ```powershell
    .\cbl.exe path\to\program.edn
    ```

=== "Unix"

    ```bash
    ./cbl path/to/program.edn
    ```

=== "Output"

    ```
    [info] [2021-11-12 16:12:52.712] [T-13596] [logging.cpp::94] [main-chain] Hello World!
    ```

## Game loop

Contrary to simple programs (e.g. command-line utility) that execute an operation and exit afterwards a game typically runs for a longer time.
Most games have the concept of a "game loop" (*todo: add more context here*).

In Chainblocks a chain can be transformed into a loop by simply replacing `(defchain)` with `(defloop)`. Additionally, `(run)` takes a second argument to specify the delay between two executions of the loop. Games tend to run at 60 FPS (or 60 Hz).

=== "EDN"

    ```clojure linenums="1"
    (defloop main-chain
      (Msg "Hello World!"))

    (defnode root)
    (schedule root main-chain)
    (run root (/ 1.0 60))
    ```

=== "Output"

    ```
    [info] [2021-11-12 16:25:54.487] [T-32668] [logging.cpp::94] [main-chain] Hello World!
    [info] [2021-11-12 16:25:54.514] [T-32668] [logging.cpp::94] [main-chain] Hello World!
    [info] [2021-11-12 16:25:54.530] [T-32668] [logging.cpp::94] [main-chain] Hello World!
    [info] [2021-11-12 16:25:54.546] [T-32668] [logging.cpp::94] [main-chain] Hello World!
    [info] [2021-11-12 16:25:54.562] [T-32668] [logging.cpp::94] [main-chain] Hello World!
    ...
    ```

--8<-- "includes/license.md"
