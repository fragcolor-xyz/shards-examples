# Step 4

## Adding Multiple Rounds
For our game to be able to handle multiple rounds and an eventual Game Over screen, we will need to manage game states.

Let us define some variables to help keep track of the game states. 
Navigate to the `initialize-variables` shard and add in the variables `.new-round` and `.game-over`. 

We can keep our variables more organized by grouping them according to when they will be reset. For example, some variables will be reset at the start of each round, while others are only reset when a new game is started.

=== "Code"
  
    ```clojure linenums="1"
    (defshards initialize-variables []
      0 >= .left-animal-type
      ...
      (Sequence .chosen-indices :Types Type.Int)

      ; Variables to reset each round 
      true >= .new-round                      ; Variable that tracks if a new round should be started
      max-timer >= .time-remaining

      ; Variables to reset each game 
      0 >= .total-score
      1 >= .current-round
      false >= .game-over                     ; Variable that tracks if the game is over

      ; Other Shared Variables
      true >= .same-animals)
    ```

Next, add code in the `logic-loop` to check if there should be a new round before executing `initialize-round`. Remove the previous `Setup` shard and add in conditionals instead.

=== "Code"
  
    ```{.clojure .annotate linenums="1"}
    (defloop logic-loop
      (WhenNot                                  ; (1)
       :Predicate (-> .game-over)               ; If the game is not in a Game Over state
       :Action
       (->
        (When                                   ; (2)
         :Predicate (-> .new-round)             ; and it is a new round,
         :Action (-> (initialize-round))))))    ; initialize a new round
    ```

    1. [`WhenNot`](https://docs.fragcolor.xyz/docs/shards/General/WhenNot/) will only execute the `Action` specified when the `Predicate` is false.
    2. [`When`](https://docs.fragcolor.xyz/docs/shards/General/When/) will only execute the `Action` specified when the `Predicate` is true.     
    Unlike `If`, it does not have an `Else` parameter.

## Handling Game States

At the start of each round, we want to ensure that `.new-round` and `.time-remaining` are reset to their original values. Create the shard `reset-round-variables` to carry out this task.

=== "Code"
  
    ```clojure linenums="1"
    (defshards reset-round-variables []
     false > .new-round
     max-timer > .time-remaining) 
    ```

Add the new `reset-round-variables` shard to `initialize-round` so that the variables are reset whenever a new round begins.

=== "Code"

    ```clojure linenums="1"
    (defshards initialize-round []
      (If
       :Predicate (-> (RandomInt :Max 2) (Is 0))
       ...
      (reset-round-variables))
    ```

When the game ends and a new one begins, we will reset the total score, the round counter and the Game Over state tracker. We will also include the variables we reset at the start of each round. Create the `reset-game-variables` shard to implement this.

=== "Code"

    ```clojure linenums="1"
    (defshards reset-game-variables []
     (reset-round-variables)
     0 > .total-score
     1 > .current-round
     false > .game-over)
    ```

Let us now create a wire named `end-round` to handle the ending of each round. 

The wire first checks if the current round is final. If it is, it sets the `.game-over` variable to true. Otherwise, it sets `.new-round` to true and increments the current round number.

=== "Code"

    ```clojure linenums="1"
    (defwire end-round
      (Setup 0 >= .new-round-number)                  ; Variable to hold the next round's number
      
      .current-round (Math.Add 1)                     ; Increment the current round by one to get the next round's number
      > .new-round-number
      
      (If
       :Predicate
       (-> .new-round-number (IsMore total-rounds))   ; Check if the next round's number exceeds the max number of rounds
       :Then
       (-> true > .game-over)                         ; If it has exceeded, set the .game-over variable to true
       :Else
       (->
        .new-round-number > .current-round            ; Otherwise set the new round number
        true > .new-round)))                          ; and set the .new-round variable to true

    ```

To end a round, the user has to give an answer or wait for the timer to expire. Let us first take a look at handling user input.

## User Input

User input is managed within the `GFX.Window` of your game. For this game, the only user input required would be the ↑ and ↓ directional key.

We can use `Inputs.KeyDown` to execute code whenever the user presses down on a specified key.

=== "Code"
  
    ```{.clojure .annotate linenums="1"}
    (defshards initialize-round [])
    (defloop ui-loop
    (GFX.MainWindow
     :Title "Yes-No Game"
     ...
      (GFX.Render :Steps .render-steps)

      (Inputs.KeyDown  ; (1)
       :Key "up"
       :Action ())     ; This code is executed when the user presses the ↑ directional key

      (Inputs.KeyDown
       :Key "down"
       :Action ()))))  ; This code is executed when the user presses the ↓ directional key
    ```

    1. [`Inputs.KeyDown`](https://docs.fragcolor.xyz/docs/shards/Inputs/KeyDown/) will run the code in its `Action` parameter when the `Key` specified is pressed down by the user.

In order to prevent code within the `Inputs.Keydown` shards from executing whenever the user presses the specified key, we define a variable `.input-received` to track if we have already received the user's input.

Define the variable in `initialize-variables` and add code to reset it in the `reset-round-variables` shard.

=== "Code"
  
    ```clojure linenums="1"
    (defshards initialize-variables []
      0 >= .left-animal-type
      ...
      (Sequence .chosen-indices :Types Type.Int)

      ; Variables to reset each round 
      true >= .new-round
      max-timer >= .time-remaining
      false >= .input-received                ; Variable that tracks if an input from the user has been received
      ...)

    (defshards reset-round-variables []
      false > .new-round
      max-timer > .time-remaining
      false > .input-received)                ; Resets the variable tracking whether user input has been received for each round
    ```

Now that we are able to obtain the user's input, we can proceed to check if the inputs are the correct answer. 

## Checking the Input

Create a shard named `check-answer` which will take in the user's input and check it against the `.same-animals` variable that we created earlier.

If the user pressed the ↑ directional key when `.same-animals` is true, or the ↓ directional key when `.same-animals` is false, we award them a point by increasing the value of `.total-score`.

We will also set `.input-received` to true to prevent further user input and end the round if `.game-over` is not true yet.

=== "Code"
  
    ```{.clojure .annotate linenums="1"}
    (defshards check-answer [yes-input]                 ; Take in the user's input
      (When
       :Predicate (-> .input-received (Is false))       ; Check if a user input has already been received
       :Action
       (->
        (When
         :Predicate (-> .same-animals (Is yes-input))   ; If the user pressed the ↑ directional key when the same animal types are being shown,
         :Action (-> (Math.Inc .total-score)))          ; increase the user's total score

        true > .input-received                          ; Prevents this segment of code from running again until it is reset
        (When
         :Predicate (-> .game-over (IsNot true))        ; If it is not Game Over yet,
         :Action (-> (Dispatch end-round))))))          ; (1) end the round
    ```

    1. [`Dispatch`](https://docs.fragcolor.xyz/docs/shards/General/Dispatch/) runs an unscheduled wire inline.    
    It behaves similarly to [`Do`](https://docs.fragcolor.xyz/docs/shards/General/Do/), except that it has Passthrough enabled. 
    This means that it will ignore the output of the Wire being dispatched and always return the input.

We can now employ the `check-answer` shard in our `Inputs.KeyDown` logic.

=== "Code"
  
    ```clojure linenums="1"
    (defloop ui-loop
     (GFX.MainWindow
      :Title "Yes-No Game"
      ...
       (GFX.Render :Steps .render-steps)

       (Inputs.KeyDown
        :Key "up"
        :Action (-> (check-answer true)))          ; check-answer is called, with true being passed in to indicate that the user chose "Yes"

       (Inputs.KeyDown
        :Key "down"
        :Action (-> (check-answer false))))))      ; check-answer is called, with false being passed in to indicate that the user chose "No"
    ```

Cheers! Your game can now receive user input and you can play up to 10 rounds each time. 

Let us round up this segment by adding in the UI for the Game Over state. We will be using a `UI.Button` to help us handle the user input for restarting the game.
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

          (UI.Button ; (1)
           :Label "Play Again!"
           :Action (-> (reset-game-variables))))))))

    ```

    1. [`UI.Button`](https://docs.fragcolor.xyz/docs/shards/UI/Button/) is a UI element that appears as a clickable button with the text passed into its `Label` parameter.

When the button is pressed, we reset the game's variables, including `.game-over` which will be used to decide what is drawn in our game's window.

Navigate to `ui-loop` and add in `.game-over` conditionals.

=== "Code"
  
    ```clojure linenums="1"
    (defloop ui-loop
     (GFX.MainWindow
      :Title "Yes-No Game"
      ...
       (| .ui-draw-queue (GFX.ClearQueue))

       (If
        :Predicate (-> .game-over)
        :Then 
        (-> (UI .ui-draw-queue (game-over-ui)))     ; Draw the Game Over UI if .game-over is true
        :Else 
        (-> (UI .ui-draw-queue (main-game-ui))))    ; Draw the Main Game UI if .game-over is false

       (GFX.Render :Steps .render-steps)
      ...
    ```

## Implementing a Timer
To add additional challenge to the game (for additional fun!), we can impose a time limit on each round.

Let us write a shard that decreases the variable `.time-remaining` every time it is called. 
=== "Code"

    ```clojure linenums="1"
    (defshards timer-tick []
      (When
       :Predicate (-> .game-over (Is false))            ; When the game is still running,
       :Action
       (->
        (Math.Dec .time-remaining)                      ; Decrease the time remaining by 1
        (When
         :Predicate (-> .time-remaining (IsLess 0))     ; The round is forced to end if the time remaining falls below 0
         :Action (Dispatch end-round)))))

    ```

We can have it called every second consistently by using the `Once` shard. 

Place it within the `logic-loop` alongside the rest of our code where we are checking if a new round should be started.

=== "Code"

    ```{.clojure .annotate linenums="1"}
    (defloop logic-loop
      (Once                         ; (1) Runs the timer-tick shard every 1 second
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

    1. [`Once`](https://docs.fragcolor.xyz/docs/shards/General/Once/) will run the code in its `Action` parameter every time the duration specified in its `Every` parameter has passed.

## Outcome

Congratulations on making it to the end!

Your game now has 10 rounds, calculates the total score, shows a Game Over screen and lets you play it again at the end. It even has a timer to add a time constraint to each round.

![Round number and the score is calculated.](assets/step-4-result-1.png)

![A Game Over screen is shown at the end.](assets/step-4-result-2.png)

The full game script can be found [here](../full-game/yesnogame.edn).

--8<-- "includes/license.md"
