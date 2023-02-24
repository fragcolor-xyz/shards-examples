# Step 2 - Changing Character Images with Button Inputs

## Overview

In the previous step we successfully drew our character, the adorable Glod, in a window. Eventually we want this image to be animated. But slow down now! Before we reach that stage, lets first start with having our image change to a different image. Thus, in this step, we will:

1. Have our character image change on button input.

## Step 2.1

To start lets try having our program Msg something when we press a button. To do this, lets create another defshards called Button_Inputs. In this defshards we will use the Inputs.KeyDown shard and the Msg shard to have our program type out a Msg output in the terminal everytime we press down the left, right and up buttons.

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
        :Action (Msg "up"))
        ) ;; (1)
        
    ```

    1. Code above is added from line 8-19.

    ```{.clojure .annotate linenums="1"}
    (button-inputs) ;; (1)
    ```

    1.  added in line 44

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
    (innitialize-character))

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
                    LoadTexture .character-image (UI.Image :Scale (float2 0.2))))))

        (GFX.Render :Steps .render-steps)

        (button-inputs))))
    

    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Why don't you try running the program now? Do you see the messages in the terminal? Congratulations! we are off on a good start. 

![Step 2.1 Output](assets/step2_1_Image.png)

## Step 2.2

Next, instead of just having messages appear on the terminal, lets start trying to get the image to change.  Similarly, like in step 1, lets load the images into variables. Add these images into the GlodImages folder and name them appropriately.

- Download Images [here](https://drive.google.com/drive/folders/12V1FABgxg_pTT4-JOf0619VJvblVOA-l?usp=share_link)

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (defshards initialize-character []
        (LoadTexture "GlodImages/Character1.png") = .character-image
        (LoadTexture "GlodImages/Character1_Left.png") = .character-left
        (LoadTexture "GlodImages/Character1_Right.png") = .character-right
        (LoadTexture "GlodImages/Character1_Jumping.png") = .character-jumping) ;; (1)      
    ```

    1. **.Character_Left**, **.Character_Right** & **.Character_Jumping** added to lines 5-9

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
        (defshards LoadTexture [name]
        (LoadImage name)
        (GFX.Texture))

        (defshards innitialize-character []
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
        (innitialize-character))

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
                        .character-image (UI.Image :Scale (float2 0.2))))))

            (GFX.Render :Steps .render-steps)

            (button-inputs))))
        

        (defmesh main)
        (schedule main main-wire)
        (run main (/ 1.0 60))
    ```

Now, lets draw the new images on screen. Let's put them all under the same UI.Area.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (defshards initialize-character []
        .character-left (UI.Image :Scale (float2 0.2))
        .character-right (UI.Image :Scale (float2 0.2))
        .character-jumping (UI.Image :Scale (float2 0.2)) ;; (1)      
    ```

    1. **.character-left (UI.Image)**, **.character-right (UI.Image)**, **.character-jumping (UI.Image)** added to **UI.Area** contents on line 44, 45 , 46

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
        (defshards LoadTexture [name]
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

Try running the programme. Don't panic! You are supposed to see 4 images stacked one on top  of the other.


![Step 2.2 Output](assets/step2_2_Image.png)

The next step is to have the window draw only 1 image at a time.

## Step 2.3

To have only 1 image drawn at a time, we will create a `character-state` variable for our Glod and then draw only one image based on the character state using the `Match` shard. 

??? "States"
    Creating states is a way to have our levels, characters and stages hold different modes, forms and levels. For example a boss can have a normal mode and an enraged mode.  Thus, for this, we will create a `.normalMode` variable and a `.enragedMode` variable. When `.normalMode` is `true` and `.enragedMode` is `false`, the boss is in normal mode. Vice-versa, when `.enragedMode` is `true` and `.normalMode` is `false`, the boss will go into enraged mode. We will use the same logic to create states for our character and have the image shown on screen change, as its state is changed.

First, create the `character-state` variable.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    0 >= .character-state ;;       
   
    ```

    1. Added under innitialize-character on line 11

    ```{.clojure .annotate linenums="1"}
    .character-state
    (Match [0 (-> .character-image (UI.Image :Scale (float2 0.2)))
            1 (-> .character-left (UI.Image :Scale (float2 0.2)))
            2 (-> .character-right (UI.Image :Scale (float2 0.2)))
            3 (-> .character-jumping (UI.Image :Scale (float2 0.2)))]
            :Passthrough false) ;;      
  
    ```

    1. Added to the UI.Area on line 45-50

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

If you run the code now, you will see only 1 image this time, but the image does not change. This is because our `character-state` is at 0 and stays at 0. Therefore our Match shard only displays the image dedicated for when `character-state` is 0. To change the image, we must first change the `character-state` variable.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
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
    :Action ( -> 
                (Msg "up")
                3 > .character-state)))  ;; (1)      
    ```

    1. The **button-input** shard was updated to also change the **character-state** on-top of printing messages in the terminal

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
    
    0 >= .character-state)

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

Now if you run the code and press the up,left and right buttons, you will see Glod's  image change! Congratulations.

## Recap

Try running the code. Walah! Now you have an image that changes based on the button pressed. The way we achieved this was by creating a character-state variable and by having a Match shard dictate which image is drawn in the window based on the character-state variable. In the next step we will breathe more life into adorable Glod by finally animating him!
Looking forward to seeing you in the next step!🙂
