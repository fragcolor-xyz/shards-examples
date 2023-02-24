# Step 3 - Animating Character using Image Arrays
## Overview

In the last step we managed to have Glod's image change based on the button input. Now, let's breathe more life into him. Instead of having static images, lets have him animated. To do this we will:

1. Using an image array to house the different frames in an animation.
2. Displaying a single frame in UI.Area using UI.Image
3. Cycle through the image shown by going through the aray using Step thus creating a looping animation.

Without further ado, let's get into it!

## Step 3.1

As usual before we can create our animation, we will have to download the images that we will use for the animation. Lets create a folder within "GlodImages" called "Character_Idle" . Within "Character_Idle" create another folder "Idle_Left" and save the images there.

![Glod Idle Gif](assets/step3_1_Gif.gif)

- Download Glod's adorable idle animation frames [here](https://drive.google.com/drive/folders/1BJVK5WL5lBaL3coZXsDWB_YgOHW0f7fQ?usp=share_link).

## Step 3.2

Once you have the images downloaded, the next step is to have an image array to house these images. 

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; ---------- Character Idle Array ---------- ;; (1)
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array  
    ```

    1. Remember to use `;; comments` to help segment and organize your code for better readabillity!

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping

      0 >= .character-state

      ;; ---------- Character Idle Array ----------
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")
                1 > .character-state))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state)))

    (defloop main-wire
      (Setup
      (initialize-character))

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
            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.Center
                      :Contents
                      (->
                      .character-state
                      (Match [0 (-> .character-image (UI.Image :Scale (float2 0.2)))
                              1 (-> .character-left (UI.Image :Scale (float2 0.2)))
                              2 (-> .character-right (UI.Image :Scale (float2 0.2)))
                              3 (-> .character-jumping (UI.Image :Scale (float2 0.2)))]
                              :Passthrough false)))))

          (GFX.Render :Steps .render-steps)

          (button-inputs))))
      

    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

??? ">>"
    `>>` is an alias for the shard `Push`. An alias is a short form of a shard to make coding much easier. Think of it like typing "lol" instead of "laughing out loud". The `Push` shard btw is used to add an element on to an array.

## Step 3.3

Now that we have an array of images, we can take an element from this array and draw that specific image in the window. To do this, let's create a variable called .idle-image-index and then tell UI.Area to draw the idle_image_index' th image in the array  using the Take shard.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    0 >= .idle-image-index ;; (1)
    ```

    1. Create an .idle_image_index variable and add it  under Initialize_Character line 22

    ```{.clojure .annotate linenums="1"}
    .idle-left-image-array (Take .idle-image-index) ;; (1)
    ```

    1. `UI_Image` displaying the idle image has been changed from `.character-image` to instead show an image from  the `idle_Left_image_array` using  the `Take` shard Line 79

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping

      0 >= .character-state

      ;; ---------- Character Idle Array ----------
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array
      
      0 >= .idle-image-index)

    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")
                1 > .character-state))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state)))

    (defloop main-wire
      (Setup
      (initialize-character))

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
            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.Center
                      :Contents
                      (->
                      .character-state
                      (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                              1 (-> .character-left (UI.Image :Scale (float2 0.2)))
                              2 (-> .character-right (UI.Image :Scale (float2 0.2)))
                              3 (-> .character-jumping (UI.Image :Scale (float2 0.2)))]
                              :Passthrough true)))))

          (GFX.Render :Steps .render-steps)

          (button-inputs))))
      

    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

## Step 3.4

Almost there! Now to have this image animated, we have to dynamically increase `.idle-image-index` and make it loop. To do this we will first create a wire that increases `.idle_image_index.`

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index) ;; (1)
    ```

    1. Whenever you make changes to a *variable*, remember to *push* it to the *variable* again at the end if you plan on using it in another segment of the code.
    Using *defloop* to create a looping wire that will *add 1* to *.idle-image-index* everytime it is called. Added to lines 24 -26

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping

      0 >= .character-state

      ;; ---------- Character Idle Array ----------
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array
      
      0 >= .idle-image-index)

    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index)

    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")
                1 > .character-state))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state)))

    (defloop main-wire
      (Setup
      (initialize-character))

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
            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.Center
                      :Contents
                      (->
                      .character-state
                      (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                              1 (-> .character-left (UI.Image :Scale (float2 0.2)))
                              2 (-> .character-right (UI.Image :Scale (float2 0.2)))
                              3 (-> .character-jumping (UI.Image :Scale (float2 0.2)))]
                              :Passthrough true)))))

          (GFX.Render :Steps .render-steps)

          (button-inputs))))
      

    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Now before we call this wire, we have to create some variables to help us loop it properly. Because if we were to try to use this wire as it is, what will happen is that `.idle-image-index` will increase in value infinitely. To prevent this from happening we will create some variables and a conditional statement to control how `.idle-image-index` increases.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (Count .idle-left-image-array) = .idle-image-index-max
    0.08 = .idle-animation-speed ;;(1)
    ```

    1. **.idle-image-index-max** will be used to ensure that **.idle-image-index** does not exceed this value .**idle-animation-speed** ensures how fast our idle animation will play. Code added to line 23 & 24.

    ```{.clojure .annotate linenums="1"}
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed)) ;; (1)
    ```

    1. The when conditional statement ensures that .idle_animation_index will not exceed .idle_image_index_max. This ensures that we will not try to draw an image that is beyond what the array has. This will cause the programme to crash Pause is used as this defloop will eventually be stepped. This pause will cause the defloop to be called every .Idle_Animation_Speed seconds added to line 26 - 31.

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping

      0 >= .character-state

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")
                1 > .character-state))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state))

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
            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.Center
                      :Contents
                      (->
                      .character-state
                      (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                              1 (-> .character-left (UI.Image :Scale (float2 0.2)))
                              2 (-> .character-right (UI.Image :Scale (float2 0.2)))
                              3 (-> .character-jumping (UI.Image :Scale (float2 0.2)))]
                              :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Now we just have to call this wire in our main wire using `Step`. 

??? "Step"
    `Step` schedules and runs another Wire on the Wire calling `Step` itself.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (Step idle-animation) ;; (1)
    ```

    1. `Step` allows us to run a code in paralell to the main wire this line is added to line 56

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping

      0 >= .character-state

      ;; ---------- Character Idle Array ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed)

    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")
                1 > .character-state))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state)))

    (defloop main-wire
      (Setup
      (initialize-character))
      
      (Step idle-animation)

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
            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.Center
                      :Contents
                      (->
                      .character-state
                      (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                              1 (-> .character-left (UI.Image :Scale (float2 0.2)))
                              2 (-> .character-right (UI.Image :Scale (float2 0.2)))
                              3 (-> .character-jumping (UI.Image :Scale (float2 0.2)))]
                              :Passthrough false)))))

          (GFX.Render :Steps .render-steps)

          (button-inputs))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Phew, that was a pretty long step😪! But it was worth it! Try running the code now. Tadah! You now have an animated idle image.

## Step 3.5

Now, before we proceed to the next chapter, lets give Glod his other animations. We will be using the same logic to give Glod his walking animations and also create the logic to ensure he faces the correct direction when the corresponding buttons are pressed. Create the appropriate folders and save the images accordingly.

- Download Glod walking right animation [here](https://drive.google.com/drive/folders/1AovccGE5vE5D9fAgionRLbynfaichjWd?usp=share_link)
- Download Glod walking left animation [here](https://drive.google.com/drive/folders/1_5h5ozXQ-DiOgyxZZw3K7cGoUw2NFFGG?usp=share_link)
- Download Glod Idle Right animation [here](https://drive.google.com/drive/folders/1i7YxKPOFN9fVMmYNS5rEOCImsDuTAEhr?usp=share_link)

??? "Jumping Frame"
    There is no animation for jumping and it retains its single frame image as 2D characters feel more responsive when they immediately jump into their jumping animation and snap back to their idle or walking animations. This is achieved when using only a single frame.


=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; ---------- Character Idle Array (Facing Right) ----------------
    (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
    (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array ;; (1)
    ```

    1. Similar to how we created an image array for the idle animation when Glod is facing left, we create one for when glod is facing right.

    ```{.clojure .annotate linenums="1"}
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

    (Count .walking-left-image-array) (Math.Subtract 1) >= .walking-image-index-max
    0 >= .walking-image-index
    0.08 >= .walking-animation-speed) ;; Reduce number to increase animation speed ;; (1)
    ```

    1. Similarly, we create image arrays for when glod is facing left and for when Glod is facing right. Since the walking animation is  a different animation from the idle animatio, we will be making new animation speed, image index and max image index variables. Code added to lines  35 - 51.

    ```{.clojure .annotate linenums="1"}
    ;; ---------- Character Idle Array (Facing Right) ----------------
    .walking-left-image-array (Take .walking-image-index)
    .walking-right-image-array (Take .walking-image-index) ;; (1)
    ```

    1. Similarly, **.character-left** & **.character-right** are replaced to display an image from their respective arrays.

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping

      0 >= .character-state

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

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")
                1 > .character-state))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state))

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
            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.Center
                      :Contents
                      (->
                      .character-state
                      (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                              1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                              2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                              3 (-> .character-jumping (UI.Image :Scale (float2 0.2)))]
                              :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Now similarly, we will create wires for the walking animation and also Step it.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMore .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed)) ;;(1)
    ```

    1. The logic for the Walking_Animation is exactly the same  as the Idle animation Code added to lines 65-71

    ```{.clojure .annotate linenums="1"}
    (Step walking-animation) ;;(1)
    ```

    1. Code added to line 109

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping

      0 >= .character-state

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
                1 > .character-state))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")
                2 > .character-state))

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
            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.Center
                      :Contents
                      (->
                      .character-state
                      (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                              1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                              2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                              3 (-> .character-jumping (UI.Image :Scale (float2 0.2)))]
                              :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

## Step 3.6

If you tried running the program however, you might notice some problems.

Namely:

1. Glod does not return to his idle animation
2. Glod's Jumping animation only faces right


So let's fix these problems. Why don't we start with the easier one first. Lets get Glod to return to his idle animation when we are not pressing any buttons. To do this we will be using `Inputs.KeyUp` and also create a variable that tracks which direction glod is facing in.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    0 >= .character-direction;;(1)
    ```

    1. 0 means the player is facing left and 1 means the player is facing right. Added to line 12 under initialize character

    ```{.clojure .annotate linenums="1"}
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
      :Action (-> 0 > .character-state))

      (Inputs.KeyUp
      :Key "right"
      :Action (-> 0 > .character-state))) ;;(1)
    ```

    1. When the left and right buttons are released, .character-state will revert back to 0. When the left and right buttons are pressed .character-direction will be 0 and 1 respectively.

    ```{.clojure .annotate linenums="1"}
      (Match [0 (-> .character-direction
                    (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                            1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                            :Passthrough false))
              1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
              2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
              3 (-> .character-jumping (UI.Image :Scale (float2 0.2)))]
              :Passthrough false) ;; (1)
    ```

    1. Lastly we add another **(Match)** to our **Match [0(->)]** so that Glod displays the correct image based on his direction

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping

      0 >= .character-state
      0 >= .character-direction

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
            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.Center
                      :Contents
                      (->
                      .character-state
                      (Match [0 (-> .character-direction
                                    (Match [0 (-> LoadTexture .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                            1 (-> LoadTexture .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                            :Passthrough false))
                              1 (-> LoadTexture .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                              2 (-> LoadTexture .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                              3 (-> LoadTexture .character-jumping (UI.Image :Scale (float2 0.2)))]
                              :Passthrough false)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

When you run the programme, Glod will  return to his idle animation whenever you stop pressing the left, right or up buttons and he should be facing the correct direction. Now we have to do the same thing for our jump animation

- Download Jumping Image [here](https://drive.google.com/drive/folders/1l5y2PoZARAukfsNz_qeQXQblrrpnwy6i?usp=share_link).

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; -------------- Character Jumping  ----------
    (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
    (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right ;;(1)
    ```

    1. Add in a jumping right image and rename our original **.character-jumping** to **.character-jumping-left** added to line 5 - 7.

    ```{.clojure .annotate linenums="1"}
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
            :Passthrough false) ;; (1)
    ```

    1. Then add another **Match** to our original **Match** to show the corresponding jump image depending on Glod's direction.Code added to lines 127-137.

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
            (UI.Area :Position (float2 0 0)
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

Try running the code now! Your adorable Glod should be fully animated and be facing in the correct directions! Congratulations on making your first animated character. We will break the chains that bind Glod and make him no longer stationary. See you in the next chapter! 😄
