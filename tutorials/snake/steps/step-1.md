# Step 1

## Setting up the workspace

We will be using Visual Studio code, although any text editor could work.

You will also need a compiled version of chainblocks to be able to run the game. It can be [built from the sources](https://github.com/fragcolor-xyz/chainblocks/wiki/Building).

## Basic file structure

In this tutorial, we will program using a EDN-like format. The syntax resembles clojure and it usually means less line of codes than traditional programming languages. Additionally, the code is more data-focused which makes it easier for beginners.

A simple program for chainblocks would look like this:

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

To run the program, execute the following command line:

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

Contrary to simple programs (e.g. command-line utility) that execute an operation and exit afterwards, a game typically runs for a longer time.
Most game have the concept of a "game loop" (*todo: add more context here*).

In chainblocks, a chain can be transformed into a loop simply by replacing `(defchain)` with `(defloop)`. Additionally, `(run)` takes a second argument to specify the delay between two execution of the loop. Games tend to run at 60 FPS (or 60 Hz).

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
