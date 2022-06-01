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
    (GUI.Window
      ; some lines omitted
      (-> .grid (render)
          (GUI.Separator)
          (When (-> .game-over)
                (-> "GAME OVER" (GUI.Text :Color (color 255 0 0 255)) ;; (1)
                    (Count .snake) (GUI.Text :Format "Final score: {}"))))) ;; (2)
    ```

    1. `:Color` is an optional parameter for [`(GUI.Text)`](https://docs.fragcolor.xyz/shards/GUI/Text/). It specifies the color of the dsiplay text using `color` (which is a built-in type that represents an RGBA color, where each component is a value in the `[0, 255]` range.)
    2. `:Format` is an optional parameter for [`(GUI.Text)`](https://docs.fragcolor.xyz/shards/GUI/Text/). It replaces `{}` in a given string by the input value.

We will also change the background color of the game's play-space. We could change the background color of the whole window, but then the area with the **GAME OVER** text would also share that same color. Instead, we will create a new area for the game itself. To do so we can use a child window.

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (color 90 165 80 255) (GUI.Style GuiStyle.ChildBgColor) ;; (1)
    (GUI.ChildWindow :Height 240 :Contents (-> .grid (render)))
    ```

    1. [`(GUI.Style)`](https://docs.fragcolor.xyz/shards/GUI/Style/) lets you modify a UI style setting identified by the `GuiStyle` enum.

We will also add a small menu, to enable the player to cleanly restart or exit the game.

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (defshards menus []
      (GUI.MenuBar
       (-> (GUI.Menu ;; (1)
            "File" :Contents
            (-> (GUI.MenuItem "New game" :Shortcut "Space" :Action (initialize))
                (GUI.Separator)
                (GUI.MenuItem "Quit" :Shortcut "Alt+F4" :Action (Stop)))))))
    ```

    1. [`(GUI.Menu)`](https://docs.fragcolor.xyz/shards/GUI/Menu/) contains one or more [`(GUI.MenuItem)`](https://docs.fragcolor.xyz/shards/GUI/MenuItem/) and is hosted in a [`(GUI.MenuBar)`](https://docs.fragcolor.xyz/shards/GUI/MenuBar/).

To display the menu we need to add the `GuiWindowFlags.MenuBar` to the sequence of flags given to the window.

That's it! Congratulations on completing the snake tutorial.

The complete code is available in the [full game](../full-game/index.md) section.

--8<-- "includes/license.md"
