# Step 5

## Wall collision and game over conditions

If the snake hits a wall, the game ends. Similarly, if the snake tries to eat a part of its own body, the game still ends.

Remember that we have a sequence that represents the whole snake body. The last element of that sequence is the head.

To test whether the snake is going outside the bounds of the play space, we just need to compare the `x` (first coordinate) and `y` (second coordinate) of the head with the number of columns and rows respectively. If this x or y value is greater than the maximum column or row value respectively, then the snake has breached the grid walls. 

We need to do this before attempting to render as otherwise the computed render position could go out of bounds and produce an error (and since we are not yet handling this error the game would crash). We will use a boolean value (`true` or `false`) to mark when the game is over and avoid updating the grid in such a case.

=== "EDN"

    ```clojure linenums="1"
    ; get the head coordinates
    .snake (RTake 0) >= .head

    ; snake hits a wall?
    (When (-> .head (Take 0) (IsLess 0) (Or) (Take 0) (IsMoreEqual grid-cols)
              (Or)
              .head (Take 1) (IsLess 0) (Or) (Take 1) (IsMoreEqual grid-rows))
          (-> true > .game-over))
    ```

To check whether the snake is eating its own body, we use the fact that the head is the last element of the snake sequence. If we find another body cell that has the same coordinates while having a different index in the sequence, it means that we have an overlap and the poor snake is in pain.

=== "EDN"

    ```clojure linenums="1"
    ; get the index of the head (which is count - 1)
    (Count .snake) (Math.Subtract 1) >= .head-idx

    ; snake eats its own body?
    (When (-> .snake (IndexOf .head) (IsNot .head-idx))
          (-> true > .game-over))
    ```

## A few graphical changes

Now that have conditions to end the game, we can add a bit more logic to display the final score (which in our case will be the length of the snake).

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (UI.BottomPanel
     :Contents
     (->
      (When (-> .game-over)
       (UI.Horizontal
       (-> "GAME OVER!" (UI.Label :Style {:color (color 255 0 0)}) ;; (1)
           (UI.Space 10.0)
           "Final score: " (UI.Label)
           (Count .snake) (ToString) (UI.Label))))))
    ```

    1. `:Style` is an optional parameter for [`(UI.Label)`](https://docs.fragcolor.xyz/docs/shards/UI/Label/). It lets specify the color of the dsiplay text using `(color)` (which is a built-in type that represents an RGBA color, where each component is a value in the `[0, 255]` range.)

We will also add a small menu, to enable the player to cleanly restart or exit the game.

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (defshards menus []
      (UI.MenuBar
       (->
        (UI.Menu ;; (1)
         "File"
         (->
          (UI.Button "New game" (-> (initialize) (UI.CloseMenu)))
          (UI.Separator)
          (UI.Button "Quit" (Stop)))))))
    ```

    1. [`(UI.Menu)`](https://docs.fragcolor.xyz/docs/shards/UI/Menu/) contains one or more [`(UI.Button)`](https://docs.fragcolor.xyz/docs/shards/UI/Button/) and is hosted in a [`(UI.MenuBar)`](https://docs.fragcolor.xyz/docs/shards/UI/MenuBar/).

To display the menu we need to add it to a `(UI.TopPanel)`.

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (UI.TopPanel :Contents (menus))
    ```


That's it! Congratulations on completing the snake tutorial.

The complete code is available in the [full game](../full-game/index.md) section.

--8<-- "includes/license.md"
