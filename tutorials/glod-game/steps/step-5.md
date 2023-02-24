# Step 5

## Dropping Coins and Scoring Points - Overview

Good job on reaching this far! Now that we have moving character, we can add more game elements. As mentioned in the intro, this will be a point collection game. So let's start by creating coins for Glod to collect. In this chapter we will be:

1. Drawing a coin image on screen
2. Having it move by using variables
3. Creating a spawning system to randomly spawn coins on screen.
4. Creating a collision system to have Glod be able to collect 
5. Reuse code to create more coins by making shards accept arguments.

## Step 5.1

Drawing an image on screen and using variables to move them. Does that not sound familiar? 🤔 Yes! It is using the same logic as making Glod. Let's start, first of all download the images that we will be using.

- Download Coin Images [here](https://drive.google.com/file/d/1zqdvZtocDsU9mI_VmN4oqpAYF6L5vSzJ/view?usp=share_link)

Let's start off nice and easy by first drawing the coin image on screen.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") = .coin-1)
    ```
    > Let's start of by creating a new Initialize_Coin Defshard. We will separate initializing variables related to our coins from Innitialize_Character to make our code more neater.

    ```{.clojure .annotate linenums="1"}
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin))
    ```

    > Then, remember to call `innitialize-coin` in the `Setup` of the `main-wire`. We are calling `initialize-coin` in setup as we only need to create these variables once.

    ```{.clojure .annotate linenums="1"}
    (UI.Area :Position (float2 0 0)
            :Anchor Anchor.Top
            :Contents (->
                          .coin-1 (UI.Image :Scale (float2 0.2))))
    ```

    > We then create a new UI.Area to draw this coin on screen.

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") = .coin-1)

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin))

      (run-logic)
      (gravity-logic)

      (Step idle-animation)
      (Step walking-animation)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-1 (UI.Image :Scale (float2 0.2))))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

## Step 5.2

Try running your code now. You should see a coin being drawn on the top of the screen. Congrats! We are off to a good start. Now, let's animate this coin. Yes! The logic will be exactly the same as when we animated Glod. Except this will be much easier as we won't need any conditional statements to control the coin.

- Download coin images [here](https://drive.google.com/drive/folders/1_iAhj70R3kS5hBXKtqh67Cfb_jBKRlzh?usp=share_link).

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    0.0 >= .coinx-1
    0.0 >= .coiny-1
    (float2 .coinx-1 .coiny-1) >= .coin-position-1
    ```
    > First we create the coin's X, Y and position variables.

    ```{.clojure .annotate linenums="1"}
    (UI.Area :Position .coin-position-1
            :Anchor Anchor.Top
            :Contents (->
                          .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))
    ```
    > We then replace the position under the :`Position` tag in our coin's `UI.Area`.

    ```{.clojure .annotate linenums="1"}
    (Step coin-animation) ;; (1)
    ```

    1. Then remember to `Step` into the Coin animation in your main-wire

    ```{.clojure .annotate linenums="1"}
    (UI.Area :Position (float2 0 0)
         :Anchor Anchor.Top
         :Contents (->
                      .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))
    ```

    > Lastly, update your coin `UI.Area` to draw the .coin-image-index'th image in the `.coin-image-array`.

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) = .coin-image-index-max
      0 >= .coin-image-index
      0.1 = .coin-animation-speed)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMoreEqual .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin))

      (run-logic)
      (gravity-logic)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Try running your code to see if your coin is animated. Good job!

## Step 5.2

Now let's make our coin fall. To do this, like we did with Glod, we once again replace the `(float2 0 0)` in the `:Position` tag in our coin's `UI.Area` with a variable. Then, we change this variable accordingly. Without further ado, let's get started!

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    0.0 >= .character-x-velocity
    ```

    > We first create a .`character-x-velocity`. We will change this value when the Right and Left buttons are pressed to make our character move. This code is added uner initialize-character on line 15

    ```{.clojure .annotate linenums="1"}
    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position)
    ```

    > Next we create the `run-logic`. When `.character-x-velocity` is changed, it will be added to `.X` and the `.character-position` will be updated accordingly Added on lines 77-82

    ```{.clojure .annotate linenums="1"}
    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")
                1 > .character-state
                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state))

      (Inputs.KeyUp
      :Key "left"
      :Action (-> 
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (-> 
                0 > .character-state 
                0.0 > .character-x-velocity)))

    ```

    > Then we just change `.character-x-velocity` whenever the Left and Right buttons are pressed. Remember to change back the `.character-x-velocity` to 0 when the buttons are released or Glod will move to the left or right forever.Added under `button-inputs` on lines 84 - 118

    ```{.clojure .annotate linenums="1"}
    ;; ------------ Character Run Logic ----------------
    (run-logic)
    ```

    > And lastly, we call our run-logic in our main-wire, line 125

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) = .coin-image-index-max
      0 >= .coin-image-index
      0.1 = .coin-animation-speed)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMoreEqual .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin))

      (run-logic)
      (gravity-logic)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Try running your code to see if your coin is animated. Good job!

## Step 5.3

Now let's make our coin fall. To do this, like we did with Glod, we once again replace the `(float2 0 0)` in the `:Position` tag in our coin's `UI.Area` with a variable. Then, we change this variable accordingly. Without further ado, let's get started!


=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    0.0 >= .coinx-1
    0.0 >= .coiny-1
    (float2 .coinx-1 .coiny-1) >= .coin-position-1 ;; (1)
    ```

    1. First we create the coin's X, Y and position variables.

    ```{.clojure .annotate linenums="1"}
    (UI.Area :Position .coin-position-1 ;; (1)
            :Anchor Anchor.Top
            :Contents (->
                          .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2)))) 
    ```

    1. We then replace the position under the :Position tag in our coin's UI.Area.

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) = .coin-image-index-max
      0 >= .coin-image-index
      0.1 = .coin-animation-speed

      ;; ----- Coin 1 ------
      0.0 >= .coinx-1
      0.0 >= .coiny-1
      (float2 .coinx-1 .coiny-1) >= .coin-position-1)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMoreEqual .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin))

      (run-logic)
      (gravity-logic)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            (UI.Area :Position .coin-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Try running your code and changing the .coinx-1 or .coiny-1 values to check if your coin's position is changing. Wonderful! Now, similar to our Run Logic and Jump Logic, we have to create a Coin gravity Logic to make our coin fall. Let's once again use velocity and acceleration to make our coin fall realistically.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    0.0 >= .coin-velocity-1
    0.5 >= .coin-acceleration
    ```

    > First lets create our velocity and acceleration variables under our initialize-coin shard

    ```{.clojure .annotate linenums="1"}
    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic []

      .coiny-1 (Math.Add .coin-velocity-1)
      > .coiny-1

      .coin-velocity-1 (Math.Add .coin-acceleration)
      > .coin-velocity-1

      (float2 .coinx-1 .coiny-1) > .coin-position-1)
    ```

    > Then, we create our `coin-gravity-logic` defshard. The logic behind coin is the same as our jump logic. We add velocity to y position then add acceleration to our velocity to increase the rate of increase.

    ```{.clojure .annotate linenums="1"}
    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")
                1 > .character-state
                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state))

      (Inputs.KeyUp
      :Key "left"
      :Action (-> 
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (-> 
                0 > .character-state 
                0.0 > .character-x-velocity)))

    ```

    > Then we just change `.character-x-velocity` whenever the Left and Right buttons are pressed. Remember to change back the `.character-x-velocity` to 0 when the buttons are released or Glod will move to the left or right forever.Added under `button-inputs` on lines 84 - 118

    ```{.clojure .annotate linenums="1"}
    (coin-gravity-logic) ;; (1)
    ```

    1. Remember to call our Coin_Gravity_Logic shard in our main-wire loop

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) = .coin-image-index-max
      0 >= .coin-image-index
      0.1 = .coin-animation-speed

      ;; ----- Coin 1 ------
      0.0 >= .coinx-1
      0.0 >= .coiny-1
      (float2 .coinx-1 .coiny-1) >= .coin-position-1
      0.0 >= .coin-velocity-1

      0.5 >= .coin-acceleration)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMoreEqual .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic []

      .coiny-1 (Math.Add .coin-velocity-1)
      > .coiny-1

      .coin-velocity-1 (Math.Add .coin-acceleration)
      > .coin-velocity-1

      (float2 .coinx-1 .coiny-1) > .coin-position-1)

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin))

      (run-logic)
      (gravity-logic)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)
      (coin-gravity-logic)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            (UI.Area :Position .coin-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

## Step 5.4

If you run your code now, you would see our coin falling! Nice 😁. Now let's have adorable Glod chase some money 🤑💰💵💶💷 To do that we need to create the logic to check when Glod is colliding with our coin. And when that happens, gain a point. Let's do this step by step.
First lets create a Score variable and some UI to draw our variable on the screen.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (defshards initialize-game-elements []
      0 >= .score)
    ```
    ```{.clojure .annotate linenums="1"}
    (initialize-game-elements)
    ```
    > First, nice and easy, let's create a `.score` variable. Remember to call this def shards in your main loop setup.

    ```{.clojure .annotate linenums="1"}
    (UI.Area :Position (float2 -40 20)
                                :Anchor Anchor.TopRight
                                :Contents (->
                                          .score (ToString)(UI.Label)))
    ```

    > Then we create a new `UI.Area` to draw our `.score` using `UI.Label`. Remember to convert our `.score` to a string first using the `(ToString)` shard. If you try to run our code now you will see a teeny tiny 0 in the corner of your screen. This means that our score is being drawn! But it unfortunately has not been styled yet. So lets do that.

    ```{.clojure .annotate linenums="1"}
    ;; ------ UI Style --------
    (def style
      {:override_text_style "MyStyle"
      :text_styles
      [{:name "MyStyle"
        :size (float 46)
        :family "Monospace"}]
      :visuals
      {:override_text_color (color 250 250 250)}})
    ```
    ```{.clojure .annotate linenums="1"}
    ;; ------ UI Style --------
    (UI.Area :Position (float2 -40 20)
                                :Anchor Anchor.TopRight
                                :Contents (->
                                          style (UI.Style)
                                          .Score (ToString)(UI.Label))) ;;(1)
    ```
    > We first create a style shard and then call this shard before we draw our UI.Label. Now if you try running your code you would see that our score is styled nicely.


=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) = .coin-image-index-max
      0 >= .coin-image-index
      0.1 = .coin-animation-speed

      ;; ----- Coin 1 ------
      0.0 >= .coinx-1
      0.0 >= .coiny-1
      (float2 .coinx-1 .coiny-1) >= .coin-position-1
      0.0 >= .coin-velocity-1

      0.5 >= .coin-acceleration)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMoreEqual .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic []

      .coiny-1 (Math.Add .coin-velocity-1)
      > .coiny-1

      .coin-velocity-1 (Math.Add .coin-acceleration)
      > .coin-velocity-1

      (float2 .coinx-1 .coiny-1) > .coin-position-1)

    ;; --------- Game Elements ------------
    (defshards initialize-game-elements []
      0 >= .score)

    ;; ------ UI Style --------
    (def style
      {:override_text_style "MyStyle"
      :text_styles
      [{:name "MyStyle"
        :size (float 46)
        :family "Monospace"}]
      :visuals
      {:override_text_color (color 250 250 250)}})

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin)
      (initialize-game-elements))

      (run-logic)
      (gravity-logic)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)
      (coin-gravity-logic)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            (UI.Area :Position .coin-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            (UI.Area :Position (float2 -40 20)
                      :Anchor Anchor.TopRight
                      :Contents (->
                                style (UI.Style)
                                .score (ToString) (UI.Label)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

## Step 5.5

Now that we have our score UI, we can increase it. To do this, we need our collision logic.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    .X (Math.Add 50.0)
    >= .scoringUpper-x-limit
    .X (Math.Subtract 50.0)
    >= .scoringLower-x-limit

    .Y (Math.Add 10.0)
    >= .scoringUpper-y-limit
    .Y (Math.Subtract 10.0)
    >= .scoringLower-y-limit
    
    false >= .scored
    ```

    > First we create an Upper and Lower X limit followed by an Upper and Lower Y limit. This is to draw a collision box around our dear Glod. These variables are added to our `initialize-game-elements` shard. We also create a .scored variable to limit the number of times we can score from one coin. These variables can be added under `initialize-game-elements`
    <br>
    ![Glod Collision Box](assets/step5_5_Image.png)

    ```{.clojure .annotate linenums="1"}
    ;; ------------- Scoring ------------
    (defshards score-collision []
      .coinx-1
      (When :Predicate (->
                        (IsLess .scoringUpper-x-limit)
                        (And)
                        .coinx-1 (IsMore .scoringLower-x-limit)
                        (And)
                        .coiny-1 (IsLess .scoringUpper-y-limit)
                        (And)
                        .coiny-1 (IsMore .scoringLower-y-limit)
                        (And)
                        .scored (Is false))
            :Action (->
                    true > .scored
                    (Log "Score: "))))

    (defshards scoring []
      .X (Math.Add 50.0)
      > .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      > .scoringLower-x-limit

      .Y (Math.Add 10.0)
      > .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      > .scoringLower-y-limit 

      (score-collision)

      .scored
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Add 1)
                    > .score
                    false > .scored)))
    ```

    > Since our character is moving, we have to constantly update our collision box. Then we have our collision logic and this is followed by our conditional statement.

    > After we have our collision box, the next step is to create the logic to read whenever our coin falls within our collision box. This conditional statement is checking if the X value of our coin is lesser then the upper X limit of our collision box and more than the lower X limit  of our collision box. It is checking if it within the stated range and does the same for the Y value. Next, we increase the score whenever this happens.

    > Then, whenever the .scored variable is true, gain a point. Once a point is gained, Reset .scored to false once again. This ensures that only one point is gained whenever we collect a coin. Remember to call the `(scoring)` shard in your main wire

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) = .coin-image-index-max
      0 >= .coin-image-index
      0.1 = .coin-animation-speed

      ;; ----- Coin 1 ------
      0.0 >= .coinx-1
      0.0 >= .coiny-1
      (float2 .coinx-1 .coiny-1) >= .coin-position-1
      0.0 >= .coin-velocity-1

      0.5 >= .coin-acceleration)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMoreEqual .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic []

      .coiny-1 (Math.Add .coin-velocity-1)
      > .coiny-1

      .coin-velocity-1 (Math.Add .coin-acceleration)
      > .coin-velocity-1

      (float2 .coinx-1 .coiny-1) > .coin-position-1)

    ;; --------- Game Elements ------------
    (defshards initialize-game-elements []
      0 >= .score
      .X (Math.Add 50.0)
      >= .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .scoringLower-x-limit

      .Y (Math.Add 10.0)
      >= .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      >= .scoringLower-y-limit

      false >= .scored)

    ; ------------- Scoring ------------
    (defshards score-collision []
      .coinx-1
      (When :Predicate (->
                        (IsLess .scoringUpper-x-limit)
                        (And)
                        .coinx-1 (IsMore .scoringLower-x-limit)
                        (And)
                        .coiny-1 (IsLess .scoringUpper-y-limit)
                        (And)
                        .coiny-1 (IsMore .scoringLower-y-limit)
                        (And)
                        .scored (Is false))
            :Action (->
                    true > .scored
                    (Log "Score: "))))

    (defshards scoring []
      .X (Math.Add 50.0)
      > .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      > .scoringLower-x-limit

      .Y (Math.Add 10.0)
      > .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      > .scoringLower-y-limit

      (score-collision)

      .scored
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Add 1)
                    > .score
                    false > .scored)))



    ;; ------ UI Style --------
    (def style
      {:override_text_style "MyStyle"
      :text_styles
      [{:name "MyStyle"
        :size (float 46)
        :family "Monospace"}]
      :visuals
      {:override_text_color (color 250 250 250)}})

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin)
      (initialize-game-elements))

      (run-logic)
      (gravity-logic)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)
      (coin-gravity-logic)
      
      (scoring)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            (UI.Area :Position .coin-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            (UI.Area :Position (float2 -40 20)
                      :Anchor Anchor.TopRight
                      :Contents (->
                                style (UI.Style)
                                .score (ToString) (UI.Label)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```
Try running your code now and see if you get a point. You did?! Congratulations!

# Step 5.6

Now that we have a scoring system, let's create more coins. Currently, we have only one coin falling down. Lets reuse this coin and have it appear again in another random X position for Glod to run after and collect.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; ---------- Random Coin ------------
    (defloop random-coin
      .coinx-1
      (RandomFloat :Max 1200.0)
      > .coinx-1
      (Math.Subtract 600.0)
      > .coinx-1

      0.0 > .coiny-1
      0.0 > .coin-velocity-1
      (float2 .coinx-1 .coiny-1) > .coin-position-1
      (Pause 1.5))
    ```

    > Here we are creating a defloop that will give our coin a random X  value and reset its y value. It will do this every 1.5 seconds. This means that our coin will randomly spawn, fall and then 1,5 seconds later, randomly spawn and fall again. 

    ```{.clojure .annotate linenums="1"}
    (Step  random-coin) ;; (1)      
    ```

    1. Remember to Step into your loop in your main wire


=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) = .coin-image-index-max
      0 >= .coin-image-index
      0.1 = .coin-animation-speed

      ;; ----- Coin 1 ------
      0.0 >= .coinx-1
      0.0 >= .coiny-1
      (float2 .coinx-1 .coiny-1) >= .coin-position-1
      0.0 >= .coin-velocity-1

      0.5 >= .coin-acceleration)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMoreEqual .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic []

      .coiny-1 (Math.Add .coin-velocity-1)
      > .coiny-1

      .coin-velocity-1 (Math.Add .coin-acceleration)
      > .coin-velocity-1

      (float2 .coinx-1 .coiny-1) > .coin-position-1)

    ;; ------------- Random Coin ------------------
    (defloop random-coin
      .coinx-1
      (RandomFloat :Max 1200.0)
      > .coinx-1
      (Math.Subtract 600.0)
      > .coinx-1

      0.0 > .coiny-1
      0.0 > .coin-velocity-1
      (float2 .coinx-1 .coiny-1) > .coin-position-1
      (Pause 1.5))

    ;; --------- Game Elements ------------
    (defshards initialize-game-elements []
      0 >= .score
      .X (Math.Add 50.0)
      >= .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .scoringLower-x-limit

      .Y (Math.Add 10.0)
      >= .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      >= .scoringLower-y-limit

      false >= .scored)

    ; ------------- Scoring ------------
    (defshards score-collision []
      .coinx-1
      (When :Predicate (->
                        (IsLess .scoringUpper-x-limit)
                        (And)
                        .coinx-1 (IsMore .scoringLower-x-limit)
                        (And)
                        .coiny-1 (IsLess .scoringUpper-y-limit)
                        (And)
                        .coiny-1 (IsMore .scoringLower-y-limit)
                        (And)
                        .scored (Is false))
            :Action (->
                    true > .scored
                    (Log "Score: "))))

    (defshards scoring []
      .X (Math.Add 50.0)
      > .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      > .scoringLower-x-limit

      .Y (Math.Add 10.0)
      > .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      > .scoringLower-y-limit

      (score-collision)

      .scored
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Add 1)
                    > .score
                    false > .scored)))



    ;; ------ UI Style --------
    (def style
      {:override_text_style "MyStyle"
      :text_styles
      [{:name "MyStyle"
        :size (float 46)
        :family "Monospace"}]
      :visuals
      {:override_text_color (color 250 250 250)}})

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin)
      (initialize-game-elements))

      (run-logic)
      (gravity-logic)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)
      (coin-gravity-logic)
      (Step  random-coin)
      
      (scoring)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            (UI.Area :Position .coin-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            (UI.Area :Position (float2 -40 20)
                      :Anchor Anchor.TopRight
                      :Contents (->
                                style (UI.Style)
                                .score (ToString) (UI.Label)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Try runnning your code! Congrats, you have a very basic game. You can try it out and have fun chasing coins! 😁

## Step 5.7

While we have a very basic game on our hands now, let's polish it further. Right now, we only have one coin spawning, lets have more! To do this, we have to change some of our shards to accept arguments instead of having hard coded variables.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; ----- Coin 2 ----
      0.0 >= .coinx-2
      0.0 >= .coiny-2
      (float2 .coinx-2 .coiny-2) >= .coin-position-2
      0.0 >= .coin-velocity-2)
    ```

    > As usual lets create some new variables for our second coin these variables are added under initialize-coin

    ```{.clojure .annotate linenums="1"}
    (UI.Area :Position .coin-position-2
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))
    ```

    > Next, we create a new `UI.Area` to draw our new coin. You can try running your code at this point to make sure that your coin is being drawn. Now to make it fall

    ```{.clojure .annotate linenums="1"}
    ;; ###### DON'T DO THIS ########
    (defshards coin-gravity-logic []

      .coiny-1 (Math.Add .coin-velocity-1)
      > .coiny-1

      .coin-velocity-1 (Math.Add .coi-acceleration)
      > .coin-velocity-1

      (float2 .coinx-1 .coiny-1) > .coin-position-1)

    (defshards coin-gravity-logic-2 []

      .coiny-2 (Math.Add .coin-velocity-1)
      > .coiny-2

      .coin-velocity-2 (Math.Add .coin-acceleration)
      > .coin-velocity-2

      (float2 .coinx-2 .coiny-2) > .coin-position-2)

    ```
    > While this can work, our code will end up being very messy. Instead, lets have our original `coin-gravity-logic` shard take in arguments instead

    ```{.clojure .annotate linenums="1"}
    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic [coiny coinx coin-velocity coin-position]

      coiny (Math.Add coin-velocity)
      > coiny

      coin-velocity (Math.Add .coin-acceleration)
      > coin-velocity

      (float2 coinx coiny) > coin-position)
    ```

    > Here we convert our original `coin-gravity-logic` shard into one that takes arguments. Now that we have this,instead of copy and pasting thiscode a million times, call  it as  many times as we need and simply feed it the correct variables.

    ```{.clojure .annotate linenums="1"}
    (coin-gravity-logic .coiny-1 .coinx-1 .coin-velocity-1 .coin-position-1)
    (coin-gravity-logic .coiny-2 .coinx-2 .coin-velocity-2 .coin-position-2)
    ```

    > Now we can just call it as many times as we need if we want to spawn more coins. Net we have to do the same for our randomise coin logic.

    ```{.clojure .annotate linenums="1"}
    ;; ------------- Random Coin ------------------
    (defshards random-coin [coinx coiny coin-velocity coin-position pause-length]
      coinx
      (RandomFloat :Max 1200.0)
      > coinx
      (Math.Subtract 600.0)
      > coinx

      0.0 > coiny
      0.0 > coin-velocity
      (float2 coinx coiny) > coin-position
      (Pause pause-length))
    ```

    ```{.clojure .annotate linenums="1"}
    (defloop random-coin-1
      (random-coin .coinx-1 .coiny-1 .coin-velocity-1 .coin-position-1 1.5))

    (defloop random-coin-2
      (random-coin .coinx-2 .coiny-2 .coin-velocity-2 .coin-position-2 2.5))
    ```

    ```{.clojure .annotate linenums="1"}
    (Step random-coin-1)
    (Step random-coin-2)
    ```
    > Now you might be wondering how do we get our `random-coin` logic to accept arguments as it is a `defloop`, not a defshards. Do not fret! To do this, we simply change our current defloop into a defshards that accepts arguments and create a new defloop that calls this new defshards as shown above.

    ```{.clojure .annotate linenums="1"}
    ;; --------- Scoring ----------
    (defshards score-collision [coinx coiny]
      coinx
      (When :Predicate (->
                        (IsLess .scoringUpper-x-limit)
                        (And)
                        coinx (IsMore .scoringLower-x-limit)
                        (And)
                        coiny (IsLess .scoringUpper-y-limit)
                        (And)
                        coiny (IsMore .scoringLower-y-limit)
                        (And)
                        .scored (Is false))
            :Action (->
                    true > .scored
                    (Log "Score: "))))

    (defshards scoring []
      .X (Math.Add 50.0)
      > .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      > .scoringLower-x-limit

      .Y (Math.Add 10.0)
      > .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      > .scoringLower-y-limit

      (score-collision .coinx-1 .coiny-1)
      (score-collision .coinx-2 .coiny-2)

      .scored
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Add 1)
                    > .score
                    false > .scored)))
    ```
    > Now, last but not least, we have to also convert our Scoring logic into a defshards that accepts arguments.


=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) = .coin-image-index-max
      0 >= .coin-image-index
      0.1 = .coin-animation-speed

      ;; ----- Coin 1 ------
      0.0 >= .coinx-1
      0.0 >= .coiny-1
      (float2 .coinx-1 .coiny-1) >= .coin-position-1
      0.0 >= .coin-velocity-1
      0.5 >= .coin-acceleration

      ;; ----- Coin 2 ----
      0.0 >= .coinx-2
      0.0 >= .coiny-2
      (float2 .coinx-2 .coiny-2) >= .coin-position-2
      0.0 >= .coin-velocity-2)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMoreEqual .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic [coiny coinx coin-velocity coin-position]

      coiny (Math.Add coin-velocity)
      > coiny

      coin-velocity (Math.Add .coin-acceleration)
      > coin-velocity

      (float2 coinx coiny) > coin-position)

    ;; ------------- Random Coin ------------------
    (defshards random-coin [coinx coiny coin-velocity coin-position pause-length]
      coinx
      (RandomFloat :Max 1200.0)
      > coinx
      (Math.Subtract 600.0)
      > coinx

      0.0 > coiny
      0.0 > coin-velocity
      (float2 coinx coiny) > coin-position
      (Pause pause-length))

    (defloop random-coin-1
      (random-coin .coinx-1 .coiny-1 .coin-velocity-1 .coin-position-1 1.5))

    (defloop random-coin-2
      (random-coin .coinx-2 .coiny-2 .coin-velocity-2 .coin-position-2 2.5))

    ;; --------- Game Elements ------------
    (defshards initialize-game-elements []
      0 >= .score
      false >= .scored

      .X (Math.Add 50.0)
      >= .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .scoringLower-x-limit

      .Y (Math.Add 10.0)
      >= .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      >= .scoringLower-y-limit)

    ;; --------- Scoring ----------
    (defshards score-collision [coinx coiny]
      coinx
      (When :Predicate (->
                        (IsLess .scoringUpper-x-limit)
                        (And)
                        coinx (IsMore .scoringLower-x-limit)
                        (And)
                        coiny (IsLess .scoringUpper-y-limit)
                        (And)
                        coiny (IsMore .scoringLower-y-limit)
                        (And)
                        .scored (Is false))
            :Action (->
                    true > .scored
                    (Log "Score: "))))

    (defshards scoring []
      .X (Math.Add 50.0)
      > .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      > .scoringLower-x-limit

      .Y (Math.Add 10.0)
      > .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      > .scoringLower-y-limit

      (score-collision .coinx-1 .coiny-1)
      (score-collision .coinx-2 .coiny-2)

      .scored
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Add 1)
                    > .score
                    false > .scored)))

    ;; ------ UI Style --------
    (def style
      {:override_text_style "MyStyle"
      :text_styles
      [{:name "MyStyle"
        :size (float 46)
        :family "Monospace"}]
      :visuals
      {:override_text_color (color 250 250 250)}})

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin)
      (initialize-game-elements))

      (coin-gravity-logic .coiny-1 .coinx-1 .coin-velocity-1 .coin-position-1)
      (coin-gravity-logic .coiny-2 .coinx-2 .coin-velocity-2 .coin-position-2)

      (run-logic)
      (gravity-logic)
      (scoring)

      (Step idle-animation)
      (Step walking-animation)
      (Step coin-animation)

      (Step random-coin-1)
      (Step random-coin-2)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            (UI.Area :Position .coin-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            (UI.Area :Position .coin-position-2
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            (UI.Area :Position (float2 -40 20)
                      :Anchor Anchor.TopRight
                      :Contents (->
                                style (UI.Style)
                                .score (ToString) (UI.Label)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Now with all the pieces in place we can add as many coins as we want. In this tutorial we will stick to just 4 coins but you can create as many as you want! Just remember to follow the same process and flow as above to make sure you don't miss out something.
1. Create the necessary variables for your new coins.
2. Create new UI.Areato draw your new coins
3. Make sure your new coins fall by adding new coin-gravity-logic
4. Make sure your coins loop and randomise by adding new random-coin-loop
5. Make sure you can earn points from them by adding new score-collision

## Recap

Phew that was a really long step! Good job for making it through 😁🎉. To recap, we made a coin fall by using the same logic when making our character move. We then created a scoring system by making a collision system. And lastly we setup a system to create as many coins as we want by making our defshards accept arguments. Phew! That was a mouthful but worth it! In the next chapter, we will use the same logic to create falling hazards for Glod! See you 👋🏼😀!

<br>
<br>