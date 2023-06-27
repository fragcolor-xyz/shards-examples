# Step 3

## Drawing the grid, the fruit, and the snake

We will assign some values to define what entity each cell of our grid can represent. For now, we will map these numeric values to draw a unique single character each as the visual representation.

| value | entity | character |
|:-----:|---------|:---------:|
| 0     | empty   | `"."`     |
| 1     | fruit   | `"F"`     |
| 2     | head    | `"H"`     |
| 3     | body    | `"B"`     |
| 4     | tail    | `"T"`     |

A snake has a head and a tail, and a body. We can represent these entities as a sequence of coordinates. The first element is the tail, the last one is the head, and everything in between is the body.

=== "EDN"

    ```clojure linenums="1"
    [(int2 1 2) (int2 2 2) (int2 3 2) (int2 3 3) (int2 4 3)] >= .snake
    ```

The fruit, however, occupies a single cell - so we just need one set of coordinates.

Every other cell is empty (or unoccupied) and is represented with a ‘.’.

=== "EDN"

    ```clojure linenums="1"
    (int2 4 4) >= .fruit
    ```

To position the cells, we will use a [`(UI.Area)`](https://docs.fragnova.com/reference/shards/shards/UI/Area/). By default, it is anchored at the top left corner, which means the position at that corner is `(float2 0 0)`.

To calculate the position of our cell, we take into account the number of columns, the size in pixels we want our cell to have (`cell-size`) and additional `x-offset` and `y-offset` so that the overall grid is not stuck at the top left corner but slightly moved right and down.

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (def cell-size 18)
    (def x-offset 48)
    (def y-offset 36)
    (defn render-cell [n] ;; (1)
      (| (int2 ;; (2)
          (% n grid-cols) ;; (3)
          (/ n grid-cols)) ;; (4)
        (ToFloat2) ;; (5)
        (Math.Multiply (float2 cell-size)) ;; (6)
        (Math.Add (float2 x-offset y-offset)) > .position) ;; (7)
      (| (Take n) ;; (8)
        (UI.Area
          :Position .position
          :Contents
          (-> (Match ;; (9)
              [0 (-> ".") ; empty
                1 (-> "F") ; fruit
                2 (-> "H") ; head
                3 (-> "B") ; body
                4 (-> "T") ; tail
                ]false)
              (UI.Label))))) ;; (10)
    ```

    1. The input of this function will be our grid. The parameter `n` is the cell index we want to render.
    2. We want to apply mathematical functions to integral numbers (so-called integers) so we explicitly use the `(int2)`type.
    3. `%` is the modulo function. We use it to get the `x` coordinate, i.e. the index of the column in our grid.
    4. `/` is the division function. We use it to get the `y` coordinate, i.e. the index of the row in our grid.
    5. [`(UI.Area)`](https://docs.fragnova.com/reference/shards/shards/UI/Area/) requires a `(float2)` type for its `:Position` parameter, so we do a conversion using [`(ToFloat2)`](https://docs.fragnova.com/reference/shards/shards/General/ToFloat2/)
    6. We defined the size for a single cell to be `cell-size`. Without it, it would have been a 1x1 pixel which we could hardly see.
    7. Finally, we add the offsets so that our grid is more centered. The final value is saved into the `.position` variable.
    8. Now, we get the value of `n`-indexed cell from the `.grid` variable that was given as input to the `render-area` function.
    9. In that action, we [`(Match)`](https://docs.fragnova.com/reference/shards/shards/General/Match/) the value of the cell to the corresponding character we have chosen.
    10. Then the matched character is displayed in place of that grid element using [`(UI.Label)`](https://docs.fragnova.com/reference/shards/shards/UI/Label/).

The above function only deals with a single cell. We want to render all cells. To do so we will slightly modify it to recursively apply to each cell index (from `0` to `(- (* grid-cols grid-rows) 1)`, i.e. the number of columns times the number of rows, minus 1 because we start our index at `0`).

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (def cell-size 18)
    (def x-offset 48)
    (def y-offset 36)
    (defn render-cells [n] ;; (1)
      (if
      (= n -1) ;; (2)
        nil  ;; (3)
        (-> ;; (4)
        (| (int2
            (% n grid-cols)
            (/ n grid-cols))
            (ToFloat2)
            (Math.Multiply (float2 cell-size))
            (Math.Add (float2 x-offset y-offset)) > .position)
        (| (Take n)
            (UI.Area
            :Position .position
            :Contents
            (-> (Match
                  [0 (-> ".") ; empty
                  1 (-> "F") ; fruit
                  2 (-> "H") ; head
                  3 (-> "B") ; body
                  4 (-> "T") ; tail
                  ]false)
                (UI.Label))))
        (render-cells (- n 1))))) ;; (5)
    ```

    1. We pluralized the function since it now renders several cells.
    2. We test whether `n` equals `-1`. Since we want to render cells from `0` to `(- (* grid-cols grid-rows) 1)`, this is our stopping condition.
    3. If we are in that case, we do nothing (`nil`).
    4. Otherwise, we perform the same code as we did above.
    5. Finally we do the recursion, which is just calling the same `render-cells` function but with a decremented value for `n`.

## Populating the grid

Before we can draw anything we need to update the grid with the fruit and the snake. To update a sequence at a given index, we can use the [`(Assoc)`](https://docs.fragnova.com/reference/shards/shards/General/Assoc/) shard. And since the snake is saved as a sequence itself, we need to iterate through all its elements. However, the head, tail, and body are represented by different values, so we will handle them separately.

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (defshards populate-grid [fruit snake]
      ; saves the input into a variable
      >= .tmp-grid

      ; first the snake tail and body
      snake (Take 0) (get-index) >= .tail-index ;; (1)
      [.tail-index 4] (Assoc .tmp-grid) ;; (2)
      snake (Slice 1 -1) ;; (3)
      (ForEach
       (-> (get-index) >= .limb-index
           [.limb-index 3] (Assoc .tmp-grid)))

      ; then the fruit
      fruit (get-index) >= .fruit-index
      [.fruit-index 1] (Assoc .tmp-grid)

      ; finally the snake head
      snake (RTake 0) (get-index) >= .head-index ;; (4)
      [.head-index 2] (Assoc .tmp-grid)

      ; return the populated grid
      .tmp-grid)
    ```
    
    1. We have already seen `(Take)` and `get-index` in [step 2](./step-2.md).
    2. Assoc lets us update the sequence.
    3. [`(Slice)`](https://docs.fragnova.com/reference/shards/shards/General/Slice/) gives a part of a sequence in a range. `1` means we start at the second element of the sequence (in other words, we skip `1` element), and `-1` means we stop at one element before the last (in other words, we skip `1` element from the end).
    4. [`(RTake)`](https://docs.fragnova.com/reference/shards/shards/General/RTake/) is similar to [`(Take)`](https://docs.fragnova.com/reference/shards/shards/General/Take/), except it starts from the end of the sequence instead of the beginning (i.e. "reverse take").

This new function `populate-grid` will take our empty grid as input and return a populated grid. That is why we need a temporary variable inside the function (`.tmp-grid`).

??? note
    Another alternative would have been, to have a single grid and erase the previous positions of the fruit and the snake before updating to their new positions. We find it easier to just update the whole grid at once for this tutorial.

## Let's try it out!

Let's put into practice all that we have seen so far.

=== "EDN"

    ```clojure linenums="1"
    --8<-- "tutorials/snake/steps/3-populate-draw-grid.edn"
    ```

=== "Result"

    ![](3-populate-draw-grid.png)

--8<-- "includes/license.md"
