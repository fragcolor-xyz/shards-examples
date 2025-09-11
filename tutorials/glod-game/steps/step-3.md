# Step 3

##  Animating with Image sequences - Overview

In the last step, we managed to have Glod's image change based on directional key input.

Now, let's breathe more life into him.

Instead of having static images, we can have him animated. To do this, we will:

1. Use an image sequence to house the different frames in an animation.

2. Display a single frame in `UI.Area` using `UI.Image`.

3. We’ll loop the animation by incrementing a frame index in an `animate` wire, and run that wire each frame with Step

Without further ado, let's get to it!

## Step 3.1: Uploading Idle Animation

Before we can create our animation, we have to download the images that will be used.

Create a folder within "GlodImages" and name it "Character_Idle". Within the "Character_Idle" folder, create another folder named "Idle_Left" and save the images there.

![Glod Idle Gif](assets/step3_1_Gif.gif){ style="display: block; margin: 0 auto; padding-bottom: 30px" }

- Download Glod's adorable idle animation frames [here](https://drive.google.com/drive/folders/1BJVK5WL5lBaL3coZXsDWB_YgOHW0f7fQ?usp=share_link).

## Step 3.2: Creating Image Sequences

Once you have the images downloaded, the next step is to have an sequence of images to house these images. 

=== "Code Added"
    
    ```shards
    ;; ---------- Character Idle sequence ---------- ;; (1)
    LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> idle-left-image-sequence
    LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> idle-left-image-sequence
    LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> idle-left-image-sequence
    LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> idle-left-image-sequence
    LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> idle-left-image-sequence
    LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> idle-left-image-sequence
    LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> idle-left-image-sequence  
    ```
    1. Remember to use `;;` comments to help segment and organize your code for better readability!

=== "Full Code So Far"
    
    ```shards

    @define( initialize-images {
      LoadImage("GlodImages/Character1_Left.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping_Left.png") = character-jumping-left
      LoadImage("GlodImages/Character1_Jumping_Right.png") = character-jumping-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping

      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> idle-left-image-sequence
    })

    @define( button-inputs{
      Inputs.KeyDown(
        Key: "left"
        Action: {
          Msg("left")
          -1 > x-direction
        }
      )

      Inputs.KeyDown(
        Key: "right"
        Action: {
          Msg("right")
          1 > x-direction
        }
      )

      Inputs.KeyDown(
        Key: "up"
        Action: {
          Msg("up")
          1 > y-direction
        }
      )

      Inputs.KeyUp(
        Key: "up"
        Action: {
          0 > y-direction
        }
        Consume: false
        SkipConsumed: true
      )
    })

    @wire(main-wire {
      Once({
        @initialize-images
        @i2(0 0) >= character-direction
        0 >= x-direction
        0 >= y-direction
      })

      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            UI.Area(
              Position: @f2(0.0 0.0)
              Anchor: Anchor::Center
              Contents: {
                character-direction
                Match([
                  @i2(0 0) {character-image}
                  @i2(-1 0) {character-left}
                  @i2(1 0) {character-right}
                  @i2(-1 1) {character-jumping-left}
                  @i2(1 1) {character-jumping-right}
                  @i2(0 1) {character-jumping}
                  none {character-image}
                ] Passthrough: false)
                UI.Image(Scale: @f2(0.2))
              }
            )
          ) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          
          GFX.Render(Steps: render-steps)
        }
      )
      
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

??? ">>"
    `>>` is an alias for the shard `Push`. An alias is a short form of a shard to make coding much easier. Think of it like typing "lol" instead of "laughing out loud". The `Push` shard is used to add an element to the back of an sequence.
    `Push` also automatically creates the appropriate sequence variable for us if it did not exist previously.

## Step 3.3: Drawing a single frame

Now that we have an sequence of images, we can take an element from this sequence and draw that specific image in the window. To do this:

1. Create a variable called `image-index`. This will determine the index of the image sequence that will be used.

2. Use the `Take` shard to retrieve the image located in the specified index of the image sequence.

3. Tell `UI.Area` to draw the image.

=== "Code Added"
    ```shards
    0 >= image-index
    ```

    ```shards
    idle-left-image-sequence | Take(image-index) ;; (1)
    ```
    1. Instead of rendering the singular character-image, we instead render a single frame taken from the `idle-left-image-sequence`.

=== "Full Code So Far"
    
    ```shards

    @define( initialize-images {
      LoadImage("GlodImages/Character1_Left.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping_Left.png") = character-jumping-left
      LoadImage("GlodImages/Character1_Jumping_Right.png") = character-jumping-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping

      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> idle-left-image-sequence
    })

    @define( button-inputs{
      Inputs.KeyDown(
        Key: "left"
        Action: {
          Msg("left")
          -1 > x-direction
        }
      )

      Inputs.KeyDown(
        Key: "right"
        Action: {
          Msg("right")
          1 > x-direction
        }
      )

      Inputs.KeyDown(
        Key: "up"
        Action: {
          Msg("up")
          1 > y-direction
        }
      )

      Inputs.KeyUp(
        Key: "up"
        Action: {
          0 > y-direction
        }
        Consume: false
        SkipConsumed: true
      )
    })

    @wire(main-wire {
      Once({
        @initialize-images
        @i2(0 0) >= character-direction
        0 >= x-direction
        0 >= y-direction
        0 >= image-index
      })

      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            UI.Area(
              Position: @f2(0.0 0.0)
              Anchor: Anchor::Center
              Contents: {
                character-direction
                Match([
                  @i2(0 0) {idle-left-image-sequence | Take(image-index)}
                  @i2(-1 0) {idle-left-image-sequence | Take(image-index)}
                  @i2(1 0) {character-right}
                  @i2(-1 1) {character-jumping-left}
                  @i2(1 1) {character-jumping-right}
                  @i2(0 1) {character-jumping}
                  none {idle-left-image-sequence | Take(image-index)}
                ] Passthrough: false)
                UI.Image(Scale: @f2(0.2))
              }
            )
          ) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          
          GFX.Render(Steps: render-steps)
        }
      )
      
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

## Step 3.4: Cycling through the animation

We're almost there!

To have this image animated, we need to dynamically increase the value of `image-index` and make it loop. To do this, we will first create a Wire that increases `image-index`.

=== "Code Added"
    ```shards
    @wire( animate {
      image-index | Math.Add(1) > image-index
    } Looped: true)
    ```

=== "Full Code So Far"
    ```shards
    @define( initialize-images {
      LoadImage("GlodImages/Character1_Left.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping_Left.png") = character-jumping-left
      LoadImage("GlodImages/Character1_Jumping_Right.png") = character-jumping-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping

      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> idle-left-image-sequence
    })

    @wire( animate {
      image-index | Math.Add(1) > image-index
    } Looped: true)

    @define( button-inputs {
      Inputs.KeyDown(
        Key: "left"
        Action: {
          Msg("left")
          -1 > x-direction
        }
      )

      Inputs.KeyDown(
        Key: "right"
        Action: {
          Msg("right")
          1 > x-direction
        }
      )

      Inputs.KeyDown(
        Key: "up"
        Action: {
          Msg("up")
          1 > y-direction
        }
      )

      Inputs.KeyUp(
        Key: "up"
        Action: {
          0 > y-direction
        }
        Consume: false
        SkipConsumed: true
      )
    })

    @wire(main-wire {
      Once({
        @initialize-images
        @i2(0 0) >= character-direction
        0 >= x-direction
        0 >= y-direction
        0 >= image-index
      })

      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            UI.Area(
              Position: @f2(0.0 0.0)
              Anchor: Anchor::Center
              Contents: {
                character-direction
                Match([
                  @i2(0 0) {idle-left-image-sequence | Take(image-index)}
                  @i2(-1 0) {idle-left-image-sequence | Take(image-index)}
                  @i2(1 0) {character-right}
                  @i2(-1 1) {character-jumping-left}
                  @i2(1 1) {character-jumping-right}
                  @i2(0 1) {character-jumping}
                  none {idle-left-image-sequence | Take(image-index)}
                ] Passthrough: false)
                UI.Image(Scale: @f2(0.2))
              }
            )
          ) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          Step(animate) ;; We will explain later why we are specifically using Step to run our animate wire.
          
          GFX.Render(Steps: render-steps)
        }
      )
      
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

!!! note "Updating Variables"

    Whenever you make changes to a variable, remember to reassign it to the variable again at the end if you plan on using it in another segment of the code.

Before we can call `animate`, we have to create some variables to help us loop it properly.

If we were to try to use this Wire as it is, `image-index` will increase in value indefinitely and eventually try to take an index that is beyond the length of the sequence, which would result in an error.

To prevent this from happening, we will create some variables and a conditional statement to control how `image-index` increases.

=== "Code Added"
    ```shards
    Count(idle-left-image-sequence) = index-max;; (1)
    0.08 = idle-animation-speed ;; (2)
    ```
  
    1. `index-max` will be used to ensure that `image-index `does not exceed this value.
    2. `idle-animation-speed` ensures how fast our idle animation will play.

    ```shards
    ;; --------- Idle Animation Loop ---------
    @wire( animate {
      Once({
        0.08 = idle-animation-speed
      })
      image-index | Math.Add(1) > image-index
      Count(idle-left-image-sequence) = index-max
      image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > image-index
      });; (1)
      Pause(idle-animation-speed) ;; (2)
    } Looped: true)
    ```

=== "Full Code So Far"
    ```shards
    @define( initialize-images {
      LoadImage("GlodImages/Character1_Left.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping_Left.png") = character-jumping-left
      LoadImage("GlodImages/Character1_Jumping_Right.png") = character-jumping-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping

      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> idle-left-image-sequence
    })

    @wire( animate {
      Once({
        0.08 = idle-animation-speed
      })
      image-index | Math.Add(1) > image-index
      Count(idle-left-image-sequence) = index-max
      image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > image-index
      })
      Pause(idle-animation-speed)
    } Looped: true)

    @define( button-inputs {
      Inputs.KeyDown(
        Key: "left"
        Action: {
          Msg("left")
          -1 > x-direction
        }
      )

      Inputs.KeyDown(
        Key: "right"
        Action: {
          Msg("right")
          1 > x-direction
        }
      )

      Inputs.KeyDown(
        Key: "up"
        Action: {
          Msg("up")
          1 > y-direction
        }
      )

      Inputs.KeyUp(
        Key: "up"
        Action: {
          0 > y-direction
        }
        Consume: false
        SkipConsumed: true
      )
    })

    @wire(main-wire {
      Once({
        @initialize-images
        @i2(0 0) >= character-direction
        0 >= x-direction
        0 >= y-direction
        0 >= image-index
      })

      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            UI.Area(
              Position: @f2(0.0 0.0)
              Anchor: Anchor::Center
              Contents: {
                character-direction
                Match([
                  @i2(0 0) {idle-left-image-sequence | Take(image-index)}
                  @i2(-1 0) {idle-left-image-sequence | Take(image-index)}
                  @i2(1 0) {character-right}
                  @i2(-1 1) {character-jumping-left}
                  @i2(1 1) {character-jumping-right}
                  @i2(0 1) {character-jumping}
                  none {idle-left-image-sequence | Take(image-index)}
                ] Passthrough: false)
                UI.Image(Scale: @f2(0.2))
              }
            )
          ) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          Step(animate)
          
          GFX.Render(Steps: render-steps)
        }
      )
      
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```
    1. The `When` conditional statement ensures that `idle-animation-index` will not exceed `index-max`. This ensures that we will not try to draw an image that is beyond what the sequence has as it will crash the program.
    
    2. `Pause` is used to control the speed of our animation. This is why `Step` is used to run our `animate` wire, as it allows control to go back to our main wire whenever `animate` is paused and hence our program flow is not disturbed by the `Pause`.

Phew, that was a pretty long step! 😪 But it was worth it! Try running the code now. Tadah! You now have an animated idle image.

## Step 3.5: Adding more animations

Before we proceed to the next chapter, let's give Glod more animations.

We will be:

1. Using the same logic to give Glod his walk animation.

2. Create the logic to handle the difference between idle and walking states.

3. Edit our logic handling the direction when jumping.

Create the appropriate folders and save the images accordingly:

- Download Glod's "Walking Right" animation [here](https://drive.google.com/drive/folders/1AovccGE5vE5D9fAgionRLbynfaichjWd?usp=share_link).

- Download Glod's "Walking Left" animation [here](https://drive.google.com/drive/folders/1_5h5ozXQ-DiOgyxZZw3K7cGoUw2NFFGG?usp=share_link).

- Download Glod's "Idle Right" animation [here](https://drive.google.com/drive/folders/1i7YxKPOFN9fVMmYNS5rEOCImsDuTAEhr?usp=share_link).

??? "Jumping Frame"
    There is no animation for jumping. Instead, it retains a single image while jumping.
    
    2D characters feel more responsive when they transit immediately between jumping and walking/idling. This is achieved by using only a single frame.

Similar to how we created an image sequence for the idle animation when Glod is facing left, we will now create one for when Glod is idle facing right, walking left , walking right.

=== "Code Added"
    
    ```shards
      ;; ---------- Character Idle sequence (Facing Right) ----------------
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> idle-right-image-sequence

      ;; -------------- Walking sequence (Facing Left) -----------------
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> walking-left-image-sequence

      ;; ----------- Walking sequence (Facing Right) ---------------
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> walking-right-image-sequence

    ```

Now, we previously used the `Inputs.KeyDown`shard, which allows us to activate and action on the frame the key is pressed, to register Glod's direction. We will modify this to a `Inputs.IsKeyDown`, to change our x-direction to 1 or -1 and use `Inputs.KeyUp`, to change it back to 0 when the key is released.

=== "Code Added"
  ```shards
    @define( button-inputs {

    Inputs.IsKeyDown(
      Key: "left"
    )
    When(Predicate: Is(true) Action: {
      -1 > x-direction
    })

    Inputs.KeyUp(
      Key: "left"
      Action: {
        0 > x-direction
      }
      Consume: false
      SkipConsumed: true
    )

    Inputs.IsKeyDown(
      Key: "right"
    )
    When(Predicate: Is(true) Action: {
      1 > x-direction
    })

    Inputs.KeyUp(
      Key: "right"
      Action: {
        0 > x-direction
      }
      Consume: false
      SkipConsumed: true
    )

    Inputs.KeyDown(
      Key: "up"
      Action: {
        Msg("up")
        1 > y-direction
      }
    )

    Inputs.KeyUp(
      Key: "up"
      Action: {
        0 > y-direction
      }
      Consume: false
      SkipConsumed: true
    )
  })
  ```

When x-direction is 0, that means Glod is in idle. When it is not 0, that means Glod is walking. Thus, we will modify our `Match` arms to reflect this. 

=== "Code Added"
    ```shards
      Match([
        @i2(0 0) {idle-left-image-sequence | Take(image-index)}
        @i2(-1 0) {walking-left-image-sequence | Take(image-index)}
        @i2(1 0) {walking-right-image-sequence | Take(image-index)}
        @i2(-1 1) {character-jumping-left}
        @i2(1 1) {character-jumping-right}
        @i2(0 1) {character-jumping}
        none {idle-left-image-sequence | Take(image-index)}
      ] Passthrough: false)
    ```

Now that we are animating walking and idle states. We have to also consider the directions of our idle animations, especially since x-direction is reset to 0 when the key is released. Thus to handle the direction of the idle state, we change the idle animation sequence when the left or right key is pressed accordingly, and use the appropriate sequence when we are playing the idle animation. To do this, we create an intermediary idle-animation to store this information.

=== "Code Added"
    ```shards
      idle-left-image-sequence >= idle-animation ;; initialize the intermediary idle animation variable
    ```
    ```shards
      Match([
        @i2(0 0) {idle-animation | Take(image-index)} ;;Take from the idle animation when in idle
        @i2(-1 0) {
          idle-left-image-sequence > idle-animation ;; Store the left idle animation into idle-animation when facing left
          walking-left-image-sequence | Take(image-index)
        }
        @i2(1 0) {
          idle-right-image-sequence > idle-animation ;; Store the right idle animation into idle-animation when facing right
          walking-right-image-sequence | Take(image-index)
        }
        @i2(-1 1) {character-jumping-left}
        @i2(1 1) {character-jumping-right}
        @i2(0 1) {character-jumping}
        none {idle-left-image-sequence | Take(image-index)}
      ] Passthrough: false)
    ```

We can tighten our `Match` code, we apply the same logic to our jumping image by creating an intermediary jumping image variable and changing the image whenever the player faces left or right.

=== "Code Added"
    ```shards
      character-jumping-left >= jumping image ;; initialize the intermediary idle animation variable
    ```
    ```shards
      Match([
        @i2(0 0) {idle-animation | Take(image-index)} ;;Take from the idle animation when in idle
        @i2(-1 0) {
          idle-left-image-sequence > idle-animation ;; Store the left idle animation into idle-animation when facing left
          character-jumping-left > jumping-image
          walking-left-image-sequence | Take(image-index)
        }
        @i2(1 0) {
          idle-right-image-sequence > idle-animation ;; Store the right idle animation into idle-animation when facing right
          character-jumping-right > jumping-image
          walking-right-image-sequence | Take(image-index)
        }
        none {jumping-image} ;; we can just use none to handle the jumping state as all other cases at this moment, the character is jumping
      ] Passthrough: false)
    ```

Lastly, we need our `Count` shard in the `animate` wire to count the appropriate animation. To do this we can create an intermediary active-animation variable.

=== "Code Added"
    ```shards
      idle-left-image-sequence >= active-animation ;; initialize the intermediary idle animation variable
    ```
    ```shards
      Match([
        @i2(0 0) {idle-animation > active-animation | Take(image-index)} ;;Take from the idle animation when in idle
        @i2(-1 0) {
          idle-left-image-sequence > idle-animation ;; Store the left idle animation into idle-animation when facing left
          character-jumping-left > jumping-image
          walking-left-image-sequence > active-animation | Take(image-index)
        }
        @i2(1 0) {
          idle-right-image-sequence > idle-animation ;; Store the right idle animation into idle-animation when facing right
          character-jumping-right > jumping-image
          walking-right-image-sequence > active-animation | Take(image-index)
        }
        none {jumping-image} ;; we can just use none to handle the jumping state as all other cases at this moment, the character is jumping
      ] Passthrough: false)
    ```
    ```shards
    @wire( animate {
      Once({
        0.08 = idle-animation-speed
      })
      image-index | Math.Add(1) > image-index
      Count(active-animation) = index-max
      image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > image-index
      })
      Pause(idle-animation-speed)
    } Looped: true)
    ```

=== "Full Code So Far"
    ```shards
    @define( initialize-images {
      LoadImage("GlodImages/Character1_Left.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping_Left.png") = character-jumping-left
      LoadImage("GlodImages/Character1_Jumping_Right.png") = character-jumping-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping

      ;; ---------- Character Idle sequence (Facing Left) ----------------

      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> idle-left-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> idle-left-image-sequence

      ;; ---------- Character Idle sequence (Facing Right) ----------------
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> idle-right-image-sequence
      LoadImage("GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> idle-right-image-sequence

      ;; -------------- Walking sequence (Facing Left) -----------------
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> walking-left-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> walking-left-image-sequence

      ;; ----------- Walking sequence (Facing Right) ---------------
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> walking-right-image-sequence
      LoadImage("GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> walking-right-image-sequence
    })

    @wire( animate {
      Once({
        0.08 = idle-animation-speed
      })
      image-index | Math.Add(1) > image-index
      Count(active-animation) = index-max
      image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > image-index
      })
      Pause(idle-animation-speed)
    } Looped: true)

    @define( button-inputs {

      Inputs.IsKeyDown(
        Key: "left"
      )
      When(Predicate: Is(true) Action: {
        -1 > x-direction
      })

      Inputs.KeyUp(
        Key: "left"
        Action: {
          0 > x-direction
        }
        Consume: false
        SkipConsumed: true
      )

      Inputs.IsKeyDown(
        Key: "right"
      )
      When(Predicate: Is(true) Action: {
        1 > x-direction
      })

      Inputs.KeyUp(
        Key: "right"
        Action: {
          0 > x-direction
        }
        Consume: false
        SkipConsumed: true
      )

      Inputs.KeyDown(
        Key: "up"
        Action: {
          Msg("up")
          1 > y-direction
        }
      )

      Inputs.KeyUp(
        Key: "up"
        Action: {
          0 > y-direction
        }
        Consume: false
        SkipConsumed: true
      )
    })

    @wire(main-wire {
      Once({
        @initialize-images
        @i2(0 0) >= character-direction
        0 >= x-direction
        0 >= y-direction
        0 >= image-index
        idle-left-image-sequence >= idle-animation
        character-jumping-left >= jumping-image
        idle-left-image-sequence >= active-animation
      })

      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            UI.Area(
              Position: @f2(0.0 0.0)
              Anchor: Anchor::Center
              Contents: {
                character-direction
                Match([
                  @i2(0 0) {idle-animation > active-animation | Take(image-index)} ;;Take from the idle animation when in idle
                  @i2(-1 0) {
                    idle-left-image-sequence > idle-animation ;; Store the left idle animation into idle-animation when facing left
                    character-jumping-left > jumping-image
                    walking-left-image-sequence > active-animation | Take(image-index)
                  }
                  @i2(1 0) {
                    idle-right-image-sequence > idle-animation ;; Store the right idle animation into idle-animation when facing right
                    character-jumping-right > jumping-image
                    walking-right-image-sequence > active-animation | Take(image-index)
                  }
                  none {jumping-image} ;; we can just use none to handle the jumping state as all other cases at this moment, the character is jumping
                ] Passthrough: false)
                UI.Image(Scale: @f2(0.2))
              }
            )
          ) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          Step(animate)
          
          GFX.Render(Steps: render-steps)
        }
      )
      
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

## Recap
Try running the code now! Your adorable Glod should be fully animated and also face the correct directions when in idle or when jumping!

Congratulations on making your first animated character in Shards.

To recap, in this step, we used sequences of images to animate Glod. Next, we will break the chains that bind Glod down and give him the power to move.

See you in the next chapter! 😄
