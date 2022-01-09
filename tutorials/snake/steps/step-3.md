# Step 3

## Drawing the grid, the fruit and the snake

We will assign some values to define what each cell of our grid can represent. For now, we will draw a single character as the visual representation.

| value | meaning | character |
|:-----:|---------|:---------:|
| 0     | empty   | `"."`     |
| 1     | fruit   | `"F"`     |
| 2     | head    | `"H"`     |
| 3     | body    | `"B"`     |
| 4     | tail    | `"S"`     |


A snake has a head and a tail, and a body. We can represent these entities as a sequence of coordinates. The first element is the tail, the last one is the head, and everything in between is the body.

=== "EDN"

    ```clojure linenums="1"
    [(int2 1 2) (int2 2 2) (int2 3 2) (int2 3 3) (int2 4 3)] >= .snake
    ```

The fruit, however, occupies a single cell - so we just need one set of coordinates.

=== "EDN"

    ```clojure linenums="1"
    (int2 4 4) >= .fruit
    ```

To simplify the layout we will render the grid using a [`(GUI.Table)`](https://docs.fragcolor.xyz/blocks/GUI/Table/). We just need to push each value to the next column and if there's no more space in that row a new row will be added automatically.

=== "EDN"

    ??? info inline end
        The input of this function will be our grid.

        The [`(ForEach)`](https://docs.fragcolor.xyz/blocks/General/ForEach/) block iterates through all elements in our grid and executes an action on each one of them.

        In that action, we [`(Match)`](https://docs.fragcolor.xyz/blocks/General/Match/) the value to the corresponding character we have chosen.

        Then that character is displayed using [`(GUI.Text)`](https://docs.fragcolor.xyz/blocks/GUI/Text/).

    ```clojure linenums="1"
    (defblocks render []
      (GUI.Table
       :Columns grid-cols :Contents
       (ForEach
        (-> (| (GUI.NextColumn))
            (| (Match
                [0 (-> ".") ; empty
                 1 (-> "F") ; fruit
                 2 (-> "H") ; head
                 3 (-> "B") ; body
                 4 (-> "T") ; tail
                 ]false)
               (GUI.Text))))))
    ```

## Populating the grid

Before we can draw anything we need to update the grid with the fruit and the snake. To update a sequence at a given index, we can use the [`(Assoc)`](https://docs.fragcolor.xyz/blocks/General/Assoc/) block. And since the snake is saved as a sequence itself, we need to iterate through all its elements. However, the head, tail and body are represented by different values, so we will handle them separately.

=== "EDN"

    ??? info inline end
        We have already seen `(Take)` and `get-index` in [step 2](./step-2.md).

        Assoc let us update the sequence.

        [`(Slice)`](https://docs.fragcolor.xyz/blocks/General/Slice/) gives a part of a sequence in a range. `1` means we start at the second element of the sequence (in other words, we skip `1` element), and `-1` means we stop at one element before the last (in other words, we skip `1` element from the end).

        [`(RTake)`](https://docs.fragcolor.xyz/blocks/General/RTake/)  is similar to [`(Take)`](https://docs.fragcolor.xyz/blocks/General/Take/) , except it starts from the end of the sequence instead of the beginning (i.e. "reverse take").

    ```clojure linenums="1"
    (defblocks populate-grid [fruit snake]
      ; saves the input into a variable
      >= .tmp-grid

      ; first the snake tail and body
      snake (Take 0) (get-index) >= .tail-index
      [.tail-index 4] (Assoc .tmp-grid)
      snake (Slice 1 -1)
      (ForEach
       (-> (get-index) >= .limb-index
           [.limb-index 3] (Assoc .tmp-grid)))

      ; then the fruit
      fruit (get-index) >= .fruit-index
      [.fruit-index 1] (Assoc .tmp-grid)

      ; finally the snake head
      snake (RTake 0) (get-index) >= .head-index
      [.head-index 2] (Assoc .tmp-grid)

      ; return the populated grid
      .tmp-grid)
    ```

This new function `populate-grid` will take our empty-grid as input and return a populated grid. That is why we need a temporary variable inside the function (`.tmp-grid`).

??? note
    Another alternative would have been, to have a single grid and erase the previous positions of the fruit and the snake before updating to their new positions. We find it easier to just update the whole grid at once for this tutorial.

## Let's try it out!

Let's put into practice all that we have seen so far.

=== "EDN"

    ```clojure linenums="1"
    (def grid-cols 12)
    (def grid-rows 10)
    (def empty-grid
      [0 0 0 0 0 0 0 0 0 0 0 0
       0 0 0 0 0 0 0 0 0 0 0 0
       0 0 0 0 0 0 0 0 0 0 0 0
       0 0 0 0 0 0 0 0 0 0 0 0
       0 0 0 0 0 0 0 0 0 0 0 0
       0 0 0 0 0 0 0 0 0 0 0 0
       0 0 0 0 0 0 0 0 0 0 0 0
       0 0 0 0 0 0 0 0 0 0 0 0
       0 0 0 0 0 0 0 0 0 0 0 0
       0 0 0 0 0 0 0 0 0 0 0 0])

    (defblocks get-index []
      (| (Take 0) >= .x)
      (| (Take 1) >= .y)
      .y (Math.Multiply grid-cols) (Math.Add .x))

    (defblocks populate-grid [fruit snake]
      ; saves the input into a variable
      >= .tmp-grid

      ; first the snake tail and body
      snake (Take 0) (get-index) >= .tail-index
      [.tail-index 4] (Assoc .tmp-grid)
      snake (Slice 1 -1)
      (ForEach
       (-> (get-index) >= .limb-index
           [.limb-index 3] (Assoc .tmp-grid)))

      ; then the fruit
      fruit (get-index) >= .fruit-index
      [.fruit-index 1] (Assoc .tmp-grid)

      ; finally the snake head
      snake (RTake 0) (get-index) >= .head-index
      [.head-index 2] (Assoc .tmp-grid)

      ; return the populated grid
      .tmp-grid)

    (defblocks render []
      (GUI.Table
       :Columns grid-cols :Contents
       (ForEach
        (-> (| (GUI.NextColumn))
            (| (Match
                [0 (-> ".") ; empty
                 1 (-> "F") ; fruit
                 2 (-> "H") ; head
                 3 (-> "B") ; body
                 4 (-> "T") ; tail
                 ]false)
               (GUI.Text))))))

    (defloop main-chain
      ; logic
      [(int2 1 2) (int2 2 2) (int2 3 2) (int2 3 3) (int2 4 3)] >= .snake
      (int2 6 7) >= .fruit
      empty-grid (populate-grid .fruit .snake) >= .grid
      ; window
      (GFX.MainWindow
       :Title "Snake game" :Width 480 :Height 360
       :Contents
       (-> (GUI.Window
            :Title "canvas" :Width 1.0 :Height 1.0 :Pos (int2 0 0)
            :Flags [GuiWindowFlags.NoTitleBar GuiWindowFlags.NoResize
                    GuiWindowFlags.NoMove GuiWindowFlags.NoCollapse]
            :Contents
            (-> .grid (render))))))

    (defnode root)
    (schedule root main-chain)
    (run root (/ 1.0 60))
    ```

=== "Result"

    ![](populate-draw-grid.png)

--8<-- "includes/license.md"
