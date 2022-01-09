# Step 2

In this tutorial we are going to implement a simplified version of the snake game. We won't have high-end graphics but will instead focus on the game logic.

## Defining the play space

The snake evolves inside a grid. It moves one cell at a time either horizontally or vertically. The fruit the snake has to eat to grow can appear on any unoccupied cell.

Let's define that grid. There are different ways to define a grid. We could use a multi-dimensional array (or in our case, a sequence of sequences). However, to simplify the code, we will instead define a flat sequence.

=== "EDN"

    ??? info inline end
        The [`def`](https://docs.fragcolor.xyz/functions/macros/#def) keyword associates a value with a name.

        `[]` is the syntax to define a sequence of values.

    ```clojure linenums="1"
    (def grid-cols 5)
    (def grid-rows 4)
    (def empty-grid
      [0 0 0 0 0
       0 0 0 0 0
       0 0 0 0 0
       0 0 0 0 0])
    ```

Then to compute the index in that sequence from a set of 2D coordinates, we can define the following function.

=== "EDN"

    ??? info inline end
        The [`defn`](https://docs.fragcolor.xyz/functions/macros/#defn) keyword associates a function with a name. Note the `[]` after the `get-index` name. This indicates that this function has 0 parameters. We will see later functions which do have parameters.

        `(->)` is a block container that will executes its inner block(s) in order.

        `(|)` is an alias for [`(Sub)`](https://docs.fragcolor.xyz/blocks/General/Sub/). It allows to reuse the same input in the next block.

        [`(Take)`](https://docs.fragcolor.xyz/blocks/General/Take/) returns the value from a sequence at a given index (starting at `0`).

        `>=` saves the output of a block into a context variable.

        [`(Math.Multiply)`](https://docs.fragcolor.xyz/blocks/Math/Multiply/) multiplies its input with a given value and outputs the result.

        [`(Math.Add)`](https://docs.fragcolor.xyz/blocks/Math/Add/) adds a value to its input and outputs the result.

    ```clojure linenums="1"
    (defn get-index []
      (-> (| (Take 0) >= .x)
          (| (Take 1) >= .y)
          .y (Math.Multiply grid-cols) (Math.Add .x)))
    ```

    ??? note
        Because `defn` expects a single "value" after the name and the list of parameters, a `(->)` block is required to group several blocks together. As it is a common occurrence, a shortcut is also provided: `defblocks`.

        ```clojure linenums="1"
        (defblocks get-index []
          (| (Take 0) >= .x)
          (| (Take 1) >= .y)
          .y (Math.Multiply grid-cols) (Math.Add .x))
        ```

It can be a bit confusing considering that the function doesn't have any parameters. This is because there is an implicit parameter which is the input. Similarly, there is an implicit output at the end of the function (the equivalent of the `return` statement in other programming languages).

Let's break down the last line to understand how this works.

=== "EDN"

    ```clojure linenums="1"
    .y (Math.Multiply grid-cols) (Math.Add .x)
    ```

- Here `.y` is a context variable.
- Its value becomes the input of the next block: `(Math.Multiply)`.
- `(Math.Multiply)` takes that value, multiplies it by `grid-cols` and returns the result as output.
- The output becomes the input for the next block: `(Math.Add)`.
- `(Math.Add)` takes that input and adds it to the value of the context variable `.x`.
- Since this is the last block of the function, the output of this block becomes the output of the whole function.

Whenever this function is called, the same processing will happen.

=== "EDN"

    ```clojure linenums="1"
    (int2 1 2) (get-index)
    ```

This time our input is a 2D integer vector represented as a single `int2` value. Inside `(get-index)` each component of that vector will be extracted using `(Take)` (see above code listings).

## Creating a window

We will render our game as a windowed application. Therefore we first need to define a window.

=== "EDN"

    ??? info inline end
        We have already seen `defloop`, `defnode`, `schedule` and `run` in [step 1](./step-1.md).

        `(GFX.Window)` creates the application window.

        `(GUI.Window)` creates a UI window inside our application.

    ```clojure linenums="1"
    (defloop main-chain
      (GFX.MainWindow
       :Title "Snake game" :Width 480 :Height 360
       :Contents
       (-> (GUI.Window
            :Title "canvas" :Width 1.0 :Height 1.0 :Pos (int2 0 0)
            :Flags [GuiWindowFlags.NoTitleBar GuiWindowFlags.MenuBar
                    GuiWindowFlags.NoResize GuiWindowFlags.NoMove GuiWindowFlags.NoCollapse]))))

    (defnode root)
    (schedule root main-chain)
    (run root (/ 1.0 60))
    ```

=== "Result"

    ![](window.png)

Since we are going to use the UI system to display our game, we need to define a render area. This is why we have a UI window inside our application window.

However we don't want that UI window to display a title bar and we want it to stay at position `(0 0)` i.e. anchored at the top left of our application. To that end, we set the width and height of the window to be 100% of the available space and we use a few flags to prevent the moving or resizing of this window.

You can play around by removing some of the flags or modifying other parameters and see how the game behavior changes.

## Let's try it out!

Let's give our function a try. First we will change a few values in the grid to be something other than `0`. And then we will try to retrieve and display those values in our brand-new window. Can you guess which values will be displayed?

=== "EDN"

    ```clojure linenums="1"
    (def grid-cols 5)
    (def grid-rows 4)
    (def grid
      [0 2 0 0 3
       1 0 7 0 0
       0 0 0 4 0
       6 0 5 0 8])

    (defn get-index []
      (-> (| (Take 0) >= .x)
          (| (Take 1) >= .y)
          .y (Math.Multiply grid-cols) (Math.Add .x)))

    (defloop main-chain
      (GFX.MainWindow
       :Title "Snake game" :Width 480 :Height 360
       :Contents
       (-> (GUI.Window
            :Title "canvas" :Width 1.0 :Height 1.0 :Pos (int2 0 0)
            :Flags [GuiWindowFlags.NoTitleBar GuiWindowFlags.NoResize
                    GuiWindowFlags.NoMove GuiWindowFlags.NoCollapse]
            :Contents
            (-> (int2 0 1) (get-index) >= .a
                (int2 3 2) (get-index) >= .b

                grid (Take .a) (GUI.Text)
                grid (Take .b) (GUI.Text))))))

    (defnode root)
    (schedule root main-chain)
    (run root (/ 1.0 60))
    ```

=== "Result"

    ![](get-index.png)

--8<-- "includes/license.md"
