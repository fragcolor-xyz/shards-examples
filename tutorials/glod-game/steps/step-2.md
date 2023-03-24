# Step 2

## Changing Character Images with Button Inputs - Overview

In the previous step, we successfully drew our character in a window. Eventually, we want this image to be animated.

But slow down now!

Before we reach that stage, let's first start with having our image change to another.

In this step, we will learn how to have our character image change on button input.

## Step 2.1

For a start, let's have our program create a message when we press a button. To do this, create another `defshards` named `button-inputs`.

We will use the shards `Inputs.KeyDown` and `Msg` to have our program show a message output in the terminal when we press directional buttons.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (defshards button-inputs []
      (Inputs.KeyDown
       :Key "left"
       :Action (Msg "left"))

      (Inputs.KeyDown
       :Key "right"
       :Action (Msg "right"))

      (Inputs.KeyDown
       :Key "up"
       :Action (Msg "up"))) ;; (1)
    ```

    1. The code above is added to lines 8 - 19.

    ```{.clojure .annotate linenums="1"}
    (button-inputs) ;; (1)
    ```

    1.  Added to line 44.

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image)

    (defshards button-inputs []
      (Inputs.KeyDown
       :Key "left"
       :Action (Msg "left"))

      (Inputs.KeyDown
       :Key "right"
       :Action (Msg "right"))

      (Inputs.KeyDown
       :Key "up"
       :Action (Msg "up")))

    (defloop main-wire
      (Setup
       (initialize-character))

      (GFX.MainWindow
       :Title "MainWindow" :Width 1920 :Height 1080
       :Contents
       (->
        (Setup
         (GFX.DrawQueue) >= .ui-draw-queue
         (GFX.UIPass .ui-draw-queue) >> .render-steps)
        .ui-draw-queue (GFX.ClearQueue)

        (UI
         .ui-draw-queue
         (->
          (UI.Area
           :Position (float2 0 0)
           :Anchor Anchor.Center
           :Contents
           (->
            LoadTexture .character-image (UI.Image :Scale (float2 0.2))))))

        (GFX.Render :Steps .render-steps)

        (button-inputs))))

    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Why don't you try running the program now? Do you see the messages in the terminal?

Great job if you did! We are off to a good start.

![Step 2.1 Output](assets/step2_1_Image.png)

## Step 2.2

Next, instead of just having messages appear on the terminal, let's start trying to get the image to change. Similar to step 1, we first load the images into variables. Add these images into the GlodImages folder and name them appropriately.

- Download the Images [here](https://drive.google.com/drive/folders/12V1FABgxg_pTT4-JOf0619VJvblVOA-l?usp=share_link).

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (defshards initialize-character []
        (LoadTexture "GlodImages/Character1.png") = .character-image
        (LoadTexture "GlodImages/Character1_Left.png") = .character-left
        (LoadTexture "GlodImages/Character1_Right.png") = .character-right
        (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping) ;; (1)      
    ```

    1. `.character-left`, `.character-right`, and `.character-jumping` are added to lines 5 - 9.

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards load-texture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping)

    (defshards button-inputs []
      (Inputs.KeyDown
       :Key "left"
       :Action (Msg "left"))

      (Inputs.KeyDown
       :Key "right"
       :Action (Msg "right"))

      (Inputs.KeyDown
       :Key "up"
       :Action (Msg "up")))

    (defloop main-wire
      (Setup
       (initialize-character))

      (GFX.MainWindow
       :Title "MainWindow" :Width 1920 :Height 1080
       :Contents
       (->
        (Setup
         (GFX.DrawQueue) >= .ui-draw-queue
         (GFX.UIPass .ui-draw-queue) >> .render-steps)
        .ui-draw-queue (GFX.ClearQueue)

        (UI
         .ui-draw-queue
         (->
          (UI.Area
           :Position (float2 0 0)
           :Anchor Anchor.Center
           :Contents
           (->
            .character-image (UI.Image :Scale (float2 0.2))))))

        (GFX.Render :Steps .render-steps)

        (button-inputs))))

    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Now, let's draw the new images onto the screen. Place them under the same `UI.Area`.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (defshards initialize-character []
        .character-left (UI.Image :Scale (float2 0.2))
        .character-right (UI.Image :Scale (float2 0.2))
        .character-jumping (UI.Image :Scale (float2 0.2))) ;; (1)      
    ```

    1. `.character-left (UI.Image)`, `.character-right (UI.Image)`, `.character-jumping (UI.Image)` are added to `UI.Area`'s `Contents` at line 44, 45, and 46.

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards load-texture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping)

    (defshards button-inputs []
      (Inputs.KeyDown
       :Key "left"
       :Action (Msg "left"))

      (Inputs.KeyDown
       :Key "right"
       :Action (Msg "right"))

      (Inputs.KeyDown
       :Key "up"
       :Action (Msg "up")))

    (defloop main-wire
      (Setup
       (initialize-character))

      (GFX.MainWindow
       :Title "MainWindow" :Width 1920 :Height 1080
       :Contents
       (->
        (Setup
         (GFX.DrawQueue) >= .ui-draw-queue
         (GFX.UIPass .ui-draw-queue) >> .render-steps)
        .ui-draw-queue (GFX.ClearQueue)

        (UI
         .ui-draw-queue
         (->
          (UI.Area
           :Position (float2 0 0)
           :Anchor Anchor.Center
           :Contents
           (->
            .character-image (UI.Image :Scale (float2 0.2))
            .character-left (UI.Image :Scale (float2 0.2))
            .character-right (UI.Image :Scale (float2 0.2))
            .character-jumping (UI.Image :Scale (float2 0.2))))))

        (GFX.Render :Steps .render-steps)

        (button-inputs))))

    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))

    ```

Try running the program. Don't panic! You are supposed to see four images stacked on top of each other.

![Step 2.2 Output](assets/step2_2_Image.png)

The next step is to have the window draw only one image at a time.

## Step 2.3

To have only one image drawn each time, we will:

1. Create a `.character-state` variable for Glod.

2. Draw only one image based on the character state using the `Match` shard.

??? "States"
    Creating states is a way to have our characters and stages hold different modes, forms and levels.
    
    For example, a boss can have a normal mode and an enraged mode. We create the variables `.normal-mode` and `.enraged-mode`.
    
    When `.normal-mode` is `true` and `.enraged-mode` is `false`, the boss is in normal mode. Vice-versa, when `.enraged-mode` is `true` and `.normal-mode` is `false`, the boss will go into enraged mode.
    
    We will use the same logic to create states for our character and have the image shown on screen change when its state is changed.

First, create the `.character-state` variable.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    0 >= .character-state ;; (1)      
    ```

    1. Added under `initialize-character` at line 11.

    ```{.clojure .annotate linenums="1"}
    .character-state
    (Match [0 (-> .character-image (UI.Image :Scale (float2 0.2)))
            1 (-> .character-left (UI.Image :Scale (float2 0.2)))
            2 (-> .character-right (UI.Image :Scale (float2 0.2)))
            3 (-> .character-jumping (UI.Image :Scale (float2 0.2)))]
            :Passthrough false) ;; (1)     
  
    ```

    1. Added to the `UI.Area` at line 45 - 50.

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}

    (defshards load-texture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping
      
      0 >= .character-state)

    (defshards button-inputs []
      (Inputs.KeyDown
       :Key "left"
       :Action (Msg "left"))

      (Inputs.KeyDown
       :Key "right"
       :Action (Msg "right"))

      (Inputs.KeyDown
       :Key "up"
       :Action (Msg "up")))

    (defloop main-wire
      (Setup
       (initialize-character))

      (GFX.MainWindow
       :Title "MainWindow" :Width 1920 :Height 1080
       :Contents
       (->
        (Setup
         (GFX.DrawQueue) >= .ui-draw-queue
         (GFX.UIPass .ui-draw-queue) >> .render-steps)
        .ui-draw-queue (GFX.ClearQueue)

        (UI
         .ui-draw-queue
         (->
          (UI.Area
           :Position (float2 0 0)
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

If you run the code now, you will see only one image this time.

However, the image does not change.

This is because our `.character-state` has a value of 0 and stays at 0. Our `Match` shard will only return the image that is shown when `.character-state` is 0.

To change the image, we must first change the value of the `.character-state` variable.

Update the `button-inputs` shard to let it modify the value of `.character-state`.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (defshards button-inputs []
      (Inputs.KeyDown
       :Key "left"
       :Action
       (->
        (Msg "left")
        1 > .character-state))

      (Inputs.KeyDown
       :Key "right"
       :Action
       (->
        (Msg "right")
        2 > .character-state))

      (Inputs.KeyDown
       :Key "up"
       :Action
       (->
        (Msg "up")
        3 > .character-state)))  
    ```

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}

    (defshards load-texture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1.png") = .character-image
      (LoadTexture "GlodImages/Character1_Left.png") = .character-left
      (LoadTexture "GlodImages/Character1_Right.png") = .character-right
      (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping

      0 >= .character-state)

    (defshards button-inputs []
      (Inputs.KeyDown
       :Key "left"
       :Action
       (->
        (Msg "left")
        1 > .character-state))

      (Inputs.KeyDown
       :Key "right"
       :Action
       (->
        (Msg "right")
        2 > .character-state))

      (Inputs.KeyDown
       :Key "up"
       :Action
       (->
        (Msg "up")
        3 > .character-state)))

    (defloop main-wire
      (Setup
       (initialize-character))

      (GFX.MainWindow
       :Title "MainWindow" :Width 1920 :Height 1080
       :Contents
       (->
        (Setup
         (GFX.DrawQueue) >= .ui-draw-queue
         (GFX.UIPass .ui-draw-queue) >> .render-steps)
        .ui-draw-queue (GFX.ClearQueue)

        (UI
         .ui-draw-queue
         (->
          (UI.Area
           :Position (float2 0 0)
           :Anchor Anchor.Center
           :Contents
           (->
            .character-state
            (Match [0 (-> .character-image (UI.Image :Scale (float2 0.2)))
                    1 (-> .character-left (UI.Image :Scale (float2 0.2)))
                    2 (-> .character-right (UI.Image :Scale (float2 0.2)))
                    3 (-> .character-jumping (UI.Image :Scale (float2 0.2)))]
                   :Passthrough false))))

        (GFX.Render :Steps .render-steps)

        (button-inputs))))

    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Now if you run the code and press the up, left and right directional buttons, you will see Glod's image change! Congratulations.

## Recap

Try running the code. Walah! You now have an image that changes based on the button pressed.

We achieved this by:

1. Creating a `.character-state` variable.

2. Using a `Match` shard to dictate which image is drawn in the window based on the value of `.character-state`.

In the next step we will breathe more life into adorable Glod by animating him!

Looking forward to seeing you in the next step!🙂
