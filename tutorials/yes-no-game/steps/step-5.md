# Step 5

For our game to engage and interact with users, we need it to be responsive to user input. 

In this chapter, we will be looking at how user input is handled in Shards.

## User Input

User input is managed within the `GFX.Window` of your game. For this game, the only user input required would be the ↑ and ↓ directional key.

We can use `Inputs.KeyDown` to execute code whenever the user presses down on a specified key.

=== "Code"
  
    ```{.clojure .annotate linenums="1"}
    (defloop ui-loop
     (GFX.MainWindow
      :Title "Yes-No Game"
      ...
       (GFX.Render :Steps .render-steps)

       (Inputs.KeyDown ;; (3)
        :Key "up"
        :Action ()) ;; (1) 

       (Inputs.KeyDown
        :Key "down"
        :Action ())))) ;; (2) 
    ```

    1. This code is executed when the user presses the ↑ directional key.
    2. This code is executed when the user presses the ↓ directional key.
    3. [`Inputs.KeyDown`](https://docs.fragcolor.xyz/docs/shards/Inputs/KeyDown/) will run the code in its `Action` parameter when the `Key` specified is pressed down by the user.


In order to prevent code within the `Inputs.Keydown` shards from executing whenever the user presses the specified key, we define a variable `.input-received` to track if we have already received the user's input.

Define the variable in `initialize-variables` and add code to reset it in `reset-round-variables`.

=== "Code"
  
    ```{.clojure .annotate linenums="1"}
    (defshards initialize-variables []
      ;; Variables to reset each round 
      true >= .new-round
      max-timer >= .time-remaining
      false >= .input-received ;; (1) 
      ...)

    (defshards reset-round-variables []
      false > .new-round
      max-timer > .time-remaining
      false > .input-received) ;; (2) 
    ```

    1. Variable that tracks if an input from the user has been received.
    2. Resets the variable tracking whether user input has been received for each round.

Now that we are able to obtain the user's input, we can proceed to check if the user pressed the correct button. 

## Checking the Input

Based on the images chosen, we can determine whether the user needs to select the ↑ or ↓ directional key. 

In `initialize-variables`, create a variable named `.same-image`. This will be used to check if the user pressed the correct key later.

=== "Code"
  
    ```{.clojure .annotate linenums="1"}
    (defshards initialize-variables []
      ...

      ;; Other Shared Variables
      0 >= .left-image-index
      0 >= .right-image-index
      (Count .images) >= .total-images
      true >= .same-image ;; (1) 
    )
    ```

    1. Tracks whether the same image is used.


Navigate to where we chose the images in `initialize-round`. 

Check if the chosen images are the same, and assign `true` or `false` to `.same-image` accordingly.

=== "Code"

    ```{.clojure .annotate linenums="1"}
    (defshards initialize-round [] 
      (RandomInt :Max .total-images) > .left-image-index
      (RandomInt :Max .total-images) > .right-image-index
  
      (If ;; (1)
       :Predicate (-> .left-image-index (Is .right-image-index)) ;; (2)
       :Then (-> true > .same-image)
       :Else (-> false > .same-image))
       
      (reset-round-variables))
  
    ```

    1. [`If`](https://docs.fragcolor.xyz/docs/shards/General/If/) checks the `Predicate` given and runs the code within `Then` if it is true. If false, the code within `Else` is run instead.
    2. Check if the images on the left and right are the same or different.

Create a shard named `check-answer` which will take in the user's input and check it against `.same-image`.

The user is correct if they:

- Pressed ↑ when `.same-image` is true

- Pressed ↓ when `.same-image` is false

We award them a point by increasing the value of `.total-score`.

Set `.input-received` to true to prevent further user input, and end the round if `.game-over` is false.

=== "Code"
  
    ```{.clojure .annotate linenums="1"}
    (defshards check-answer [yes-input] ;; (1) 
      (When
       :Predicate (-> .input-received (Is false)) ;; (2)
       :Action
       (->
        (When
         :Predicate (-> .same-image (Is yes-input)) ;; (3)
         :Action (-> (Math.Inc .total-score))) ;; (4)

        true > .input-received ;; (5) 
        (When
         :Predicate (-> .game-over (IsNot true)) ;; (6)
         :Action (-> (Step end-round)))))) ;; (7)(8)
    ```

    1. Take in the user's input.
    2. Check if a user input has already been received.
    3. If the user pressed the ↑ directional key when the same images are being shown...
    4. ... increase the user's total score.
    5. Prevents this segment of code from running again until it is reset.
    6. If it is not Game Over yet...
    7. ... end the round.
    8. [`Step`](https://docs.fragcolor.xyz/docs/shards/General/Step/) runs a wire inline.

??? "Step vs Do"
    Step behaves similarly to [`Do`](https://docs.fragcolor.xyz/docs/shards/General/Do/), except that it allows you to use a *Looped Wire* like a function. `Step` will run one iteration of the Loop before returning control back to the Wire that called it. If you try to use `Do` on a Loop, the Loop will run indefinitely.

    Use a stepped Loop when you want a variable within the Loop to persist. When a Wire finishes, any changes made to the variables within it will be lost. A Looped Wire lives on, thereby retaining any changes within it. 
    
    Check out the primer [here](../../../shards/the-flow/#step--branch--stepmany) for more information.

We can now employ the `check-answer` shard in our `Inputs.KeyDown` logic.

=== "Code"
  
    ```{.clojure .annotate linenums="1"}
    (defloop ui-loop
     (GFX.MainWindow
      :Title "Yes-No Game"
      ...
       (GFX.Render :Steps .render-steps)

       (Inputs.KeyDown
        :Key "up"
        :Action (-> (check-answer true))) ;; (1)

       (Inputs.KeyDown
        :Key "down"
        :Action (-> (check-answer false)))))) ;; (2)
    ```

    1. Check-answer is called, with true being passed in to indicate that the user chose "Yes".
    2. Check-answer is called, with false being passed in to indicate that the user chose "No".

Cheers! Your game can now receive user input, tabulate the score, and allows players to play up to 10 rounds each time.


=== "Full Code"
  
    ```{.clojure .annotate linenums="1"}
    (def total-rounds 10)
    (def max-timer 5)

    (defshards load-resources []
      (LoadImage "data/cats/cat01.png") (Push :Name .images)
      (LoadImage "data/cats/cat02.png") (Push :Name .images)
      (LoadImage "data/cats/cat03.png") (Push :Name .images))


    (defshards initialize-variables []
      ;; Variables to reset each round 
      true >= .new-round
      max-timer >= .time-remaining
      false >= .input-received

      ;; Variables to reset each game 
      0 >= .total-score
      1 >= .current-round
      false >= .game-over

      ;; Other Shared Variables
      0 >= .left-image-index
      0 >= .right-image-index
      (Count .images) >= .total-images
      true >= .same-image)

    (defshards reset-round-variables []
      false > .new-round
      max-timer > .time-remaining
      false > .input-received)

    (defshards reset-game-variables []
      (reset-round-variables)
      0 > .total-score
      1 > .current-round
      false > .game-over)

    (defshards initialize-round []
      .total-images
      (RandomInt :Max .total-images) > .left-image-index
      (RandomInt :Max .total-images) > .right-image-index
  
      (If
       :Predicate (-> .left-image-index (Is .right-image-index))
       :Then (-> true > .same-image)
       :Else (-> false > .same-image))
  
      (reset-round-variables))

    (defloop end-round
      (Setup 0 >= .new-round-number)

      .current-round (Math.Add 1)
      > .new-round-number

      (If
       :Predicate
       (-> .new-round-number (IsMore total-rounds))
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
         :Predicate (-> .same-image (Is yes-input))
         :Action (-> (Math.Inc .total-score)))
    
        true > .input-received
        (When
         :Predicate (-> .game-over (IsNot true))
         :Action (-> (Step end-round))))))


    (defshards main-game-ui []
      (UI.BottomPanel
       :Contents (-> "Are they the same image? Press the UP arrow if YES, and the DOWN arrow if NO." (UI.Label)))

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
           (-> .images (Take .left-image-index) (UI.Image)))
          (UI.Area
           :Position (float2 250.0, 0.0)
           :Anchor Anchor.Center
           :Contents
           (-> .images (Take .right-image-index) (UI.Image))))))))

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
        (UI .ui-draw-queue (main-game-ui))
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
        (When ;; 
         :Predicate (-> .new-round)
         :Action (-> (initialize-round))))))


    (defshards initialize-round []
      .total-images
      (RandomInt :Max .total-images) > .left-image-index (Log "left")
      (RandomInt :Max .total-images) > .right-image-index (Log "right")

      ; Determine if the images on the left and right are the same or different
      (If
       :Predicate (-> .left-image-index (Is .right-image-index))
       :Then (-> true > .same-image)
       :Else (-> false > .same-image))

      (reset-round-variables))

    (defloop game-loop
      (Setup (load-resources) (initialize-variables))
      (Branch [ui-loop, logic-loop]))

    (defmesh main)
    (schedule main game-loop)
    (run main (/ 1.0 60.0))
    ```

=== "Result"    
    ![User input, score tabulation, and multiple rounds.](assets/step-5-result.png)


--8<-- "includes/license.md"
