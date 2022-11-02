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
  
    ```clojure linenums="1"
    (defloop logic-loop
      (WhenNot
       :Predicate (-> .game-over)               ; If the game is not in a Game Over state
       :Action
       (->
        (When
         :Predicate (-> .new-round)             ; and it is a new round,
         :Action (-> (initialize-round))))))    ; initialize a new round
    ```

??? "WhenNot"
    [`WhenNot`](https://docs.fragcolor.xyz/docs/shards/General/WhenNot/) will only execute the `Action` specified when the `Predicate` is false. 

??? "When"
    [`When`](https://docs.fragcolor.xyz/docs/shards/General/When/) will only execute the `Action` specified when the `Predicate` is true. 
    
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

To end a round, the user has to give an answer or wait for the timer to expire. We will be tackling the implementation of the timer in the following step. For now, let us take a look at handling user input.

## User Input

User input is managed within the `GFX.Window` of your game. For this game, the only user input required would be the ↑ and ↓ directional key.

We can use `Inputs.KeyDown` to execute code whenever the user presses down on a specified key.

=== "Code"
  
    ```clojure linenums="1"
    (defshards initialize-round [])
    (defloop ui-loop
    (GFX.MainWindow
     :Title "Yes-No Game"
     ...
      (GFX.Render :Steps .render-steps)

      (Inputs.KeyDown
       :Key "up"
       :Action ())                        ; This code is executed when the user presses the ↑ directional key

      (Inputs.KeyDown
       :Key "down"
       :Action ()))))                     ; This code is executed when the user presses the ↓ directional key
    ```

??? "Inputs.KeyDown"
    [`Inputs.KeyDown`](https://docs.fragcolor.xyz/docs/shards/Inputs/KeyDown/) will run the code in its `Action` parameter when the `Key` specified is pressed down by the user.

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
  
    ```clojure linenums="1"
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
         :Action (-> (Dispatch end-round))))))          ; end the round
    ```

??? "Dispatch"
    [`Dispatch`](https://docs.fragcolor.xyz/docs/shards/General/Dispatch/) runs an unscheduled wire inline.
    
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
  
    ```clojure linenums="1"
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

          (UI.Button
           :Label "Play Again!"
           :Action (-> (reset-game-variables))))))))

    ```
??? "UI.Button"
    [`UI.Button`](https://docs.fragcolor.xyz/docs/shards/UI/Button/) is a UI element that appears as a clickable button with the text passed into its `Label` parameter.

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

## Outcome

Congratulations on making it this far!

Your game now has 10 rounds, calculates the total score, shows a Game Over screen and lets you play it again at the end.

![Round number and the score is calculated.](assets/step-4-result-1.png)

![A Game Over screen is shown at the end.](assets/step-4-result-2.png)

=== "Code"
  
    ```clojure linenums="1"
    (def total-rounds 10)
    (def max-timer 5)

    (defshards load-resources []
      (LoadImage "data/cats/cat01.png") (Push :Name .cat-images)
      (LoadImage "data/cats/cat02.png") (Push :Name .cat-images)
      (LoadImage "data/cats/cat03.png") (Push :Name .cat-images)
      (LoadImage "data/cats/cat04.png") (Push :Name .cat-images)
      (LoadImage "data/cats/cat05.png") (Push :Name .cat-images)
      (LoadImage "data/dogs/dog01.png") (Push :Name .dog-images)
      (LoadImage "data/dogs/dog02.png") (Push :Name .dog-images)
      (LoadImage "data/dogs/dog03.png") (Push :Name .dog-images)
      (LoadImage "data/dogs/dog04.png") (Push :Name .dog-images)
      (LoadImage "data/dogs/dog05.png") (Push :Name .dog-images)
      (LoadImage "data/penguins/penguin01.png") (Push :Name .penguin-images)
      (LoadImage "data/penguins/penguin02.png") (Push :Name .penguin-images)
      (LoadImage "data/penguins/penguin03.png") (Push :Name .penguin-images)
      (LoadImage "data/penguins/penguin04.png") (Push :Name .penguin-images)
      (LoadImage "data/penguins/penguin05.png") (Push :Name .penguin-images)
      .cat-images (Push :Name .all-images)
      .dog-images (Push :Name .all-images)
      .penguin-images (Push :Name .all-images))

    (defshards initialize-variables []
      0 >= .left-animal-type
      0 >= .right-animal-type
      0 >= .left-image-index
      0 >= .right-image-index

      (Count .all-images) >= .total-animal-types
      0 >= .left-images-count
      0 >= .right-images-count

      (Sequence .left-images :Types Type.Image)
      (Sequence .right-images :Types Type.Image)
      (Sequence .chosen-indices :Types Type.Int)

      ; Variables to reset each round 
      true >= .new-round
      max-timer >= .time-remaining
      false >= .input-received

      ; Variables to reset each game 
      0 >= .total-score
      1 >= .current-round
      false >= .game-over

      ; Other Shared Variables
      true >= .same-animals)

    (defshards reset-round-variables []
      false > .new-round
      max-timer > .time-remaining
      false > .input-received)

    (defshards reset-game-variables []
      (reset-round-variables)
      0 > .total-score
      1 > .current-round
      false > .game-over)

    (defwire fisher-yates-shuffle
      (ExpectSeq) = .sequence
      (Setup (int 0) >= .max-index)
      (Count .sequence) (Math.Subtract 1) > .max-index      ; -1 from the sequence size to get the max index number

      (Repeat
       (-> (RandomInt :Max .max-index) >= .random-index     ; Select an index of the sequence randomly, the last index will never be chosen as :Max is not inclusive
           .sequence
           (| (Take .random-index) &> .chosen-value)        ; Obtains the value stored at the random index of the sequence
           (| (Take .max-index) &> .last-value)             ; Obtains the last value in the sequence
                                                            ; Swaps the values in the last index of the sequence with the chosen index
           [.random-index .last-value] (Assoc .sequence)    ; Updates the random index selected with the last value of the sequence
           [.max-index .chosen-value] (Assoc .sequence)     ; Updates the last index of the sequence with the value of the index randomly chosen

           (Math.Dec .max-index))                           ; Decreases the max index value so that the shuffle will leave 
                                                        ; the newly swapped value of the current max index alone for the next iteration  
       :Forever true
       :Until (-> .max-index (Is 0)))                       ; Loops until the there are no two values left to swap between

      .sequence)                                            ; Returns the newly shuffled sequence

    (defwire select-two-indices
      = .sequence-length

      (Setup
       (Sequence .indices-sequence)
       0 >= .index)

      (Clear .indices-sequence)
      0 > .index

      (Repeat
       :Action
       (->
        .index (Push .indices-sequence)
        (Math.Inc .index))
       :Times .sequence-length)

      .indices-sequence (Do fisher-yates-shuffle) (Take [0 1]))

    ; Determines the images used for the round
    (defshards initialize-round []
      ; Determine if the images on the left and right will be the same or different
      (If
       :Predicate (-> (RandomInt :Max 2) (Is 0))
       :Then (-> true > .same-animals)
       :Else (-> false > .same-animals))

      (If
       :Predicate (-> .same-animals (Is true))

       ; If both images should show the same animal type
       :Then
       (->
        ; Randomly selects the animal type to be used for the left side
        (RandomInt :Max .total-animal-types) > .left-animal-type
        .left-animal-type > .right-animal-type

        ; Retrieve the images of the chosen animal type for the left image
        .all-images (Take .left-animal-type) > .left-images
        (Count .left-images) > .left-images-count

        .left-images-count (Do select-two-indices) (ExpectIntSeq) > .chosen-indices
        .chosen-indices (Take 0) > .left-image-index
        .chosen-indices (Take 1) > .right-image-index)

       ; If both images should show different animal types
       :Else
       (->
        .total-animal-types (Do select-two-indices) (ExpectIntSeq) > .chosen-indices
        .chosen-indices (Take 0) > .left-animal-type
        .chosen-indices (Take 1) > .right-animal-type
        .all-images (Take .left-animal-type) > .left-images
        .all-images (Take .right-animal-type) > .right-images
        (Count .left-images) > .left-images-count
        (Count .right-images) > .right-images-count
        (RandomInt :Max .left-images-count) > .left-image-index
        (RandomInt :Max .right-images-count) > .right-image-index))

      (reset-round-variables))

    (defwire end-round
      (Setup 0 >= .new-round-number)
      .current-round (Math.Add 1) > .new-round-number
      (If
       :Predicate (-> .new-round-number (IsMore total-rounds))
       :Then
       (-> true > .game-over)
       :Else
       (->
        .new-round-number > .current-round
        true > .new-round)))

    (defshards check-answer [yes-input]
      (When
       :Predicate (-> .input-received (Is false))
       :Action
       (->
        (When
         :Predicate (-> .same-animals (Is yes-input))
         :Action (-> (Math.Inc .total-score)))

        true > .input-received
        (When
         :Predicate (-> .game-over (IsNot true))
         :Action (Dispatch end-round)))))

    ; Creates the main game area
    (defshards main-game-ui []
      (UI.BottomPanel
       :Contents (-> "Are they the same type of animal? Press the UP arrow if YES, and the DOWN arrow if NO." (UI.Label)))

      (UI.TopPanel
       :Contents
       (->
        (UI.Horizontal
         :Contents
         (->
          "Score: " (UI.Label)
          .total-score (ToString) (UI.Label)
          (UI.Separator)
          "Round: " (UI.Label)
          .current-round (ToString) (UI.Label)
          (UI.Separator)
          "Time Left: " (UI.Label)
          .time-remaining (ToString) (UI.Label)))))
  
      (UI.CentralPanel
       :Contents
       (->
        (UI.Horizontal
         :Contents
         (->
          (UI.Area
           :Position (float2 -250.0, 0.0)
           :Anchor Anchor.Center
           :Contents
           (-> .all-images (Take .left-animal-type) (Take .left-image-index) (UI.Image)))
          (UI.Area
           :Position (float2 250.0, 0.0)
           :Anchor Anchor.Center
           :Contents
           (-> .all-images (Take .right-animal-type) (Take .right-image-index) (UI.Image))))))))     

    ; Creates the game over screen
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

          (UI.Button
           :Label "Play Again!"
           :Action (-> (reset-game-variables))))))))

    (defloop ui-loop
      (GFX.MainWindow
       :Title "Yes-No Game"
       :Width 1280 :Height 768
       :Contents
       (->
        (Setup
         (GFX.DrawQueue) >= .ui-draw-queue
         (GFX.UIPass .ui-draw-queue) >> .render-steps)
        (| .ui-draw-queue (GFX.ClearQueue))
    
        (If
         :Predicate (-> .game-over)
         :Then (-> (UI .ui-draw-queue (game-over-ui)))
         :Else (-> (UI .ui-draw-queue (main-game-ui))))

        (GFX.Render :Steps .render-steps)

        (Inputs.KeyDown
         :Key "up"
         :Action (-> (check-answer true)))
    
        (Inputs.KeyDown
         :Key "down"
         :Action (-> (check-answer false))))))

    (defloop logic-loop
      (WhenNot
       :Predicate (-> .game-over)
       :Action
       (->
        (When
         :Predicate (-> .new-round)
         :Action (-> (initialize-round))))))

    (defloop game-loop
      (Setup (load-resources) (initialize-variables))
      (Branch [ui-loop, logic-loop]))

    (defmesh main)
    (schedule main game-loop)
    (run main (/ 1.0 60.0))

    ```

--8<-- "includes/license.md"
