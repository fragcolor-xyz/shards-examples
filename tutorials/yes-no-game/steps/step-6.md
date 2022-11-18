# Step 6

Let us round up this tutorial by adding in the UI for the Game Over state and adding a Timer for extra challenge.

We will be using a `UI.Button` to help us handle the user input for restarting the game.

## Game Over

=== "Code"
  
    ```{.clojure .annotate linenums="1"}
    (defshards game-over-ui []
      (UI.CentralPanel
       :Contents
       (->
        (UI.Area
         :Position (float2 0.0, 0.0)
         :Anchor Anchor.Center
         :Contents
         (->
          "GAME OVER" (UI.Label)
          (UI.Horizontal
           :Contents
           (->
            "Final Score: " (UI.Label)
            .total-score (ToString) (UI.Label)
            "/"  (UI.Label)
            total-rounds (ToString) (UI.Label)))

          (UI.Button ;; (1)
           :Label "Play Again!"
           :Action (-> (reset-game-variables))))))))

    ```

    1. [`UI.Button`](https://docs.fragcolor.xyz/docs/shards/UI/Button/) is a UI element that appears as a clickable button with the text passed into its `Label` parameter displayed on it.

When the button is pressed, we reset the game's variables, including `.game-over` which will be used to decide what is drawn in our game's window.

Navigate to `ui-loop` and add in `.game-over` conditionals.

=== "Before"
  
    ```{.clojure .annotate linenums="1"}
    (defloop ui-loop
     (GFX.MainWindow
      :Title "Yes-No Game"
      ...
       (| .ui-draw-queue (GFX.ClearQueue))
       (UI .ui-draw-queue (main-game-ui))
       (GFX.Render :Steps .render-steps))
      ...
    ```
=== "After"
  
    ```{.clojure .annotate linenums="1"}
    (defloop ui-loop
     (GFX.MainWindow
      :Title "Yes-No Game"
      ...
       (| .ui-draw-queue (GFX.ClearQueue))

       (If
        :Predicate (-> .game-over)
        :Then 
        (-> (UI .ui-draw-queue (game-over-ui))) ;; (1)
        :Else 
        (-> (UI .ui-draw-queue (main-game-ui)))) ;; (2)

       (GFX.Render :Steps .render-steps)
      ...
    ```

    1. Draw the Game Over UI if .game-over is true.
    2. Draw the Main Game UI if .game-over is false.

The game will now display the main game area or a game over screen depending on whether `.game-over` is true.

## Implementing a Timer
To add additional challenge to the game (for additional fun!), we can impose a time limit on each round.

Let us write a shard that decreases the `.time-remaining` every time it is called. 
=== "Code"

    ```{.clojure .annotate linenums="1"}
    (defshards timer-tick []
      (When
       :Predicate (-> .game-over (Is false)) ;; (1)
       :Action
       (->
        (Math.Dec .time-remaining) ;; (2)
        (When
         :Predicate (-> .time-remaining (IsLess 0)) ;; (3)
         :Action (Step end-round)))))
    ```
    
    1. When the game is still running...
    2. ... decrease the time remaining by 1.
    3. The round is forced to end if the time remaining falls below 0.

We can have `timer-tick` called every second consistently by using the `Once` shard. 

Place it within the `logic-loop` where we are checking if a new round should be started.

=== "Code"

    ```{.clojure .annotate linenums="1"}
    (defloop logic-loop
      (Once ;; (1)(2)
       :Action (-> (timer-tick))
       :Every 1.0)

      (WhenNot
       :Predicate (-> .game-over)
       :Action
       (->
        (When
         :Predicate (-> .new-round)
         :Action (-> (initialize-round))))))
    ```

    1. Runs the timer-tick shard every 1 second.
    2. [`Once`](https://docs.fragcolor.xyz/docs/shards/General/Once/) will run the code in its `Action` parameter every time the duration specified in its `Every` parameter has passed.

!!! note
    You can adjust the difficulty by changing the value of the `max-timer` constant we defined at the start.

## Outcome

Congratulations on making it to the end!

Your game now has 10 rounds, calculates the total score, shows a Game Over screen and lets you play it again at the end. It even has a timer to add a time constraint to each round.

![Round number and the score is calculated.](assets/step-6-result-1.png)

![A Game Over screen is shown at the end.](assets/step-6-result-2.png)

Now that the tutorial is over, why not challenge yourself further by implementing a high-score system? You could also try adding support for a second player! 

The possibilities are endless with the power of Shards in your hands.

The full game script can be found [here](../full-game/yesnogame.edn).

--8<-- "includes/license.md"
