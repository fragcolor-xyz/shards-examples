# Step 4

## Moving Character with button Inputs - Overview

In the previous chapter we breathed some life into Glod and transformed him from a static image into a fully animated character. Amazing! However, we can take things further. Lets free Glod from stationary curse and make him move! To do this we will

1. Place a variable in the :Position tag of UI.Area
2. Changing this variable to make Glod move
3. Use Velocity and Acceleration concepts to make Glod jump.

## Step 4.1

First, let's edit our code a little. Currently Glod is situated in the center of our screen, while this can work, lets change his current `Anchor.Center` to `Anchor.Top` and bring him down to Earth by changing his position by creating some variables.

??? ":bulb:"
    While any anchor will work as long as we change the position accordingly, we will stick to `Anchor.Top` for now as in the subsequent chapters we will be using `Anchor.Top` for other elements that we will add on to the game. To keep things consistent and manage the positions of the different game elements more effectively, lets use `Anchor.Top` for now.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    0.0 >= .X
    310.0 >= .Y
    (float2 .X .Y) >= .character-position
    ```

    > Here we create an `.X` variable and a `.Y` variable which is used to create another variable that is our `.character-position`. Code is aded under initialize-character lines 12- 14.

    ```{.clojure .annotate linenums="1"}
    (UI.Area :Position .character-position
                                :Anchor Anchor.Top
                                :Contents (->
    ```

    > `.character-position`. This variable will then be fed into the `:Position` tag in the `UI. Area` shard. When the .X and `.Y` value are changed, the `UI.Area` will move and hence our character will move. Remember to change the `Anchor` to `Anchor.Top` Code edited in lines 127

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

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position

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

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")
                1 > .character-state
                0 > .character-direction))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state
                1 > .character-direction))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state)))

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character))

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
                                        :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

## Step 4.2

Now that our variables are set up, lets make Glod move! Let's start by moving him to the left and right. To do this, we simply have to change the .X value that we have just created whenever the left and right buttons are pressed.

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

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position

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

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")
                1 > .character-state
                0 > .character-direction))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state
                1 > .character-direction))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state)))

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character))

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
                                        :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

## Step 4.3

Oh no! We have a problem 🥲. If we move Glod far left and far right, you might notice that they go off screen. Let's set up a boundary to ensure that Glod does not fall off the edge into the abyss. To do this, lets creat a clamp function that limits how far our X value can go.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; ---------- character-boundary ------------
    (defshards clamp [var min max]
      var (Max min) (Min max) > var)
    ```

    > This clamp function takes in a variable and makes sure it does not exceed the min value and the max value line added from 77-80

    ```{.clojure .annotate linenums="1"}
    (clamp .X -600.0 600.0)
    ```

    > Next we simply call our `clamp` function in our `run-logic` and set the var to be .X, the min to be -600 and the max to be 600. Change the min and max value to fit your screen accordingly. Code added to line 89.

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

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity

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

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character))

      (run-logic)

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
                                        :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```
Yay! Now Glod doesn't walk off into the void! 😙

## Step 4.4

Now lets make Glod jump! However, instead of just using veloctiy, lets add acceleration to the mix. Will make Glod's jump feel even more amazing.

??? "Jumping using Velocity & Acceleration"
    For smooth releastic jumping, we have to decrease velocity by acceleration whenever we jump. Decreasing velocity by acceleration decreases the rate of increase of our `.Y` value and this simulates gravity thus making our jump feel more realistic.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    0.0 >= .character-y-velocity
    0.0 >= .character-y-acceleration
    ```

    > First, lets create some variables that we will use. Code added to lines 16 & 17.

    ```{.clojure .annotate linenums="1"}
    ;; ------------ gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position)
    ```

    > Similar to the `run-logic`, we add `.character-y-velocity` to our `.Y` variable. This time however, we will also add .`character-y-acceleration` to `.character-y-velocity` Code added to lines 93-99

    ```{.clojure .annotate linenums="1"}
    (gravity-logic)
    ```

    > Remember to call your gravity-logic Defshard in your main-wire.

    ```{.clojure .annotate linenums="1"}
    (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                -20.0 > .character-y-velocity
                1.0 >  .character-y-acceleration))
    ```
    > Lastly we change the .character-y-velocity and the .character-y-acceleration whenever the Up buttons is pressed. Code added to button-inputs function

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

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position

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

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")
                1 > .character-state
                0 > .character-direction))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state
                1 > .character-direction))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state)))

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character))

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
                                        :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

??? ":bulb:"
    Since our :Anchor is set to Anchor.Top, it  places our UI.Area at the top and in the middle of the screen. This is now our origin at (0,0). The way axis works  in shards,increasing the Y value, pushes our UI.Area down while decreasing it pushes it up.  This is why our velocity is a negative value. Because it is a negative value, when added to our .Y value, it decreases the .Y value and thus makes our image go up. Similarly, our Acceleration is a positive value so that when it get added to our negative value velocity, it decreases it. 

## Step 4.5

Try running your code now! Oh no, we have a problem. Glod falls through the floor! 😨 Don't worry, we can fix this! Similar to how we made a boundary to ensure Glod does not walk off screen, we can add a boundary to ensure he doesn't fall through the floor by reusing the same Clamp function we made in the previous step.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (clamp .Y -620.0 620.0)
    ```

    > First, call our clamp function in our `gravity-logic` with `.Y` as our var and -620 as our min and 620 as our max. Code added to lines 103

    ```{.clojure .annotate linenums="1"}
    .Y
    (When :Predicate (IsMoreEqual 620.0)
          :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration))
    ```

    > Next we add a conditional statement to ensure that our Y velocity and acceleration reverts back to 0 when Glod is on the ground.

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
                    0.0 > .character-y-acceleration)))

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
                3 > .character-state
                -20.0 > .character-y-velocity
                1.0 >  .character-y-acceleration))

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

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character))

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
                                        :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Congratulations! Glod can now move and jump safely! 😊

## Step 4.6

Now, lastly, those  of you whoo are sharp-eyed would've noticed one small problem. 

1. Glod does not go back to his idle animation when he lands on the floor
2. Glod changes to his running animation if we move left and right while  jumping.
3. Glod can continuously jump as long as you press the jump button.

Let's fix these problems shall we! For the first problem, we just have to ensure that Glod goes back to his idle animation when he reaches the floor.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    .Y
    (When :Predicate (IsMoreEqual 620.0)
          :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state))))
    ```

    > Code added under `gravity-logic` edited to change `character-state` to 0 only when character is jumping.

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
                3 > .character-state
                -20.0 > .character-y-velocity
                1.0 >  .character-y-acceleration))

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

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character))

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
                                        :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```
??? ":bulb:"
    Here we use a nested When conditional statement to ensure that Glod  goes back to his idle animation only when he is jumping. Had we simply put 0 >. character-state without the nested `When` conditional statement, this would revert Glod back to his idle animation but it would cause another problem. That is, Glod will forever be in his idle animation whenever he is on the ground. This means he willl never go into his walking animations. Hence why we use the nested When conditional statement instead.

The second problem is also a similar easy fix. We just put a When conditional statement to make Glod go into his walking animation only when he is in idle and not jumping.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
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
    ```

    > We use a when conditional statement to make sure that Glod goes into his walking animation only when his character state is at 0, that is when he is idle.

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
                -20.0 > .character-y-velocity
                1.0 >  .character-y-acceleration))

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

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character))

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
                                        :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

For the last problem, although slightly more complicated, to solve this we also use a When conditional statement.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    true >= .can-jump
    ```

    > First, create a .can-jump variable under innitialize-character

    ```{.clojure .annotate linenums="1"}
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
    ```

    > Then we make it  such that Glod can not jump anymore when he jumps. So when the jump button is pressed, `.can-jump  ` becomes false. And only increase the velocity and acceleration when .can-jump is true.

    ```{.clojure .annotate linenums="1"}
    .Y
    (When :Predicate (IsMoreEqual 620.0)
          :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state))))
    ```

    > Lastly we reset `.can-jump` back to true when God touches the floor again.

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

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character))

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
                                        :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

## Recap

Try running your code now! Glod should be happily running and jumping without any glitches😁. To recap, we made Glod move by tying a variable to the UI.Area position and changing said variable. We then ensure that Glod does not walk or fall into the void by creating boundaries. And lastly we created conditional statements to fix our problems. And walah! we have a fully functional moving character! In the next chapter, lets make Glod collect some coins! 🤑

<br>
<br>


