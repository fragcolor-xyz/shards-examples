# Step 2

## Changing Character Images with Button Inputs - Overview

In the previous step, we successfully drew our character in a window. Eventually, we want this image to be animated.

But slow down now!

Before we reach that stage, let's first start with having our image change to another.

In this step, we will learn how to have our character image change on button input.

## Step 2.1: Adding Button Inputs

For a start, let's have our program create a message when we press a button. To do this, create another `define` named `button-inputs`.

We will use the shards `Inputs.KeyDown` and `Msg` to have our program show a message output in the terminal when we press directional buttons.

=== "Code Added"
```shards
@define( button-inputs{
  Inputs.KeyDown(
    Key: "left"
    Action: {
      Msg("left")
    }
  )

  Inputs.KeyDown(
    Key: "right"
    Action: {
      Msg("right")
    }
  )

  Inputs.KeyDown(
    Key: "up"
    Action: {
      Msg("up")
    }
  )
})
```

```shards
@button-inputs
```

=== "Full Code So Far"
    
```shards
@define( button-inputs{
  Inputs.KeyDown(
    Key: "left"
    Action: {
      Msg("left")
    }
  )

  Inputs.KeyDown(
    Key: "right"
    Action: {
      Msg("right")
    }
  )

  Inputs.KeyDown(
    Key: "up"
    Action: {
      Msg("up")
    }
  )
})

@define( initialize-images {
  LoadImage("GlodImages/Character1.png") = character-image
})

@wire(main-wire {
  Once({
    @initialize-images
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
            character-image | UI.Image
          }
        )
      ) | UI.Render(ui-draw-queue)

      @button-inputs
      
      GFX.Render(Steps: render-steps)
    }
  ) = main-window

} Looped: true)

@mesh(main)
@schedule(main main-wire)
@run(main FPS: 60)
```

Why don't you try running the program now? Do you see the messages in the terminal?

Great job if you did! We are off to a good start.

![Step 2.1 Output](assets/step2_1_Image.png)

## Step 2.2: Uploading all our images

Next, instead of just having messages appear on the terminal, let's start trying to get the image to change. Similar to step 1, we first load the images into variables. Add these images into the GlodImages folder and name them appropriately.

- Download the Images [here](https://drive.google.com/drive/folders/12V1FABgxg_pTT4-JOf0619VJvblVOA-l?usp=share_link).

=== "Code Added"
    
    ```shards
    @define( initialize-images {
      LoadImage("GlodImages/Character1.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping
    })  
    ```

=== "Full Code So Far"
    
    ```shards
    @define( button-inputs{
      Inputs.KeyDown(
        Key: "left"
        Action: {
          Msg("left")
        }
      )

      Inputs.KeyDown(
        Key: "right"
        Action: {
          Msg("right")
        }
      )

      Inputs.KeyDown(
        Key: "up"
        Action: {
          Msg("up")
        }
      )
    })

    @define( initialize-images {
      LoadImage("GlodImages/Character1.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping
    })

    @wire(main-wire {
      Once({
        @initialize-images
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
                character-image | UI.Image
              }
            )
          ) | UI.Render(ui-draw-queue)

          @button-inputs
          
          GFX.Render(Steps: render-steps)
        }
      ) = main-window

    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

Now, let's draw the new images onto the screen. Place them under the same `UI.Area`.

=== "Code Added"
    
    ```shards
    character-left | UI.Image(Scale: @f2(0.2))
    character-jumping | UI.Image(Scale: @f2(0.2)) 
    character-right | UI.Image(Scale: @f2(0.2))
    ```

=== "Full Code So Far"
    
    ```shards
    @define( button-inputs{
      Inputs.KeyDown(
        Key: "left"
        Action: {
          Msg("left")
        }
      )

      Inputs.KeyDown(
        Key: "right"
        Action: {
          Msg("right")
        }
      )

      Inputs.KeyDown(
        Key: "up"
        Action: {
          Msg("up")
        }
      )
    })

    @define( initialize-images {
      LoadImage("GlodImages/Character1.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping
    })

    @wire(main-wire {
      Once({
        @initialize-images
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
                character-left | UI.Image(Scale: @f2(0.2))
                character-jumping | UI.Image(Scale: @f2(0.2)) 
                character-right | UI.Image(Scale: @f2(0.2))
              }
            )
          ) | UI.Render(ui-draw-queue)

          @button-inputs
          
          GFX.Render(Steps: render-steps)
        }
      ) = main-window

    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

Try running the program. Don't panic! You are supposed to see 3 images stacked on top of each other.

![Step 2.2 Output](assets/step2_2_Image.png)

The next step is to have the window draw only one image at a time.

## Step 2.3: Changing our Image

To have only one image drawn each time, we will:

1. Create a `character-direction` variable for Glod.

2. Draw only one image based on the character state using the `Match` shard.

??? "States"
    Creating states is a way to have our characters and stages hold different modes, forms and levels.
    
    For example, a boss can have a normal mode and an enraged mode. We create the variables `normal-mode` and `enraged-mode`.
    
    When `normal-mode` is `true` and `enraged-mode` is `false`, the boss is in normal mode. Vice-versa, when `enraged-mode` is `true` and `normal-mode` is `false`, the boss will go into enraged mode.
    
    We will use the same logic to create states for our character and have the image shown on screen change when its state is changed.

First, create the `character-direction` variable. We use an `@i2` variable instead of just an `int` because an `@i2` can store the information for both directions on the x-axis and also y-axis.

=== "Code Added" 
    ```shards
    @i2(0 0) >= character-direction ;;
    ```

Next, we use a `Match` shard to display the correct image based on the `character-direction` variable. Let's focus on just the left and right direction first. We will handle jumping later on.

!!! note "`Match`"
    The `Match` shard is like a very long If-Else statement. It takes the input provided and runs through the list of cases provided. If the input equals one of the cases, the corresponding arm is evaluated. 
    
    It is good practice to have a `none` case because if the input does not equal to any of the cases provided, an error is thrown. With the `none` case, in this scenario, the `none` arm is evaluated.

=== "Code Added"
    ```shards
    character-direction
    Match([
      @i2(0 0) {character-image}
      @i2(-1 0) {character-left}
      @i2(1 0) {character-right}
      none {character-image}
    ] Passthrough: false)

    ```

=== "Full Code So Far"
    ```shards
    @define( button-inputs{
      Inputs.KeyDown(
        Key: "left"
        Action: {
          Msg("left")
        }
      )

      Inputs.KeyDown(
        Key: "right"
        Action: {
          Msg("right")
        }
      )

      Inputs.KeyDown(
        Key: "up"
        Action: {
          Msg("up")
        }
      )
    })

    @define( initialize-images {
      LoadImage("GlodImages/Character1.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping
    })

    @wire(main-wire {
    Once({
      @initialize-images
      @i2(0 0) >= character-direction
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
                none {character-image}
              ] Passthrough: false)
              UI.Image(Scale: @f2(0.2))
            }
          )
        ) | UI.Render(ui-draw-queue)

        @button-inputs
        
        GFX.Render(Steps: render-steps)
      }
    )

    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

!!! note "`Passthrough`"
    When `Passthrough` is set to false, the shard outputs the evaluated value of the matching arm. However, to do this, each arm also must evaluate to a value of the same `@type`. In this case, each arm outputs an image object, hence `Match` will output the resulting image object.

If you run the code now, you will see only one image this time.

However, the image does not change.

This is because our `character-direction` has a value of @i2(0 0) and stays at @i2(0 0). Our `Match` shard will only output the image that is shown when `character-direction` is @i2(0 0).

To change the image, we must first change the value of the `character-direction` variable.

First, we create two new variables, x-direction and y-direction. We can put this in the once where we initialize our images and our character-direction variable.

=== "Code Added"
    
```shards
Once({
  @initialize-images
  @i2(0 0) >= character-direction
  0 >= x-direction
  0 >= y-direction
})  
```

Next, we change our newly created variables when we press our left, right or up keys in our input.

=== "Code Added"
    
```shards
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
})
```

Lastly, we update the character direction `@i2` using the updated x-direction, y-direction.

=== "Code Added"
    ```shards
    @i2(x-direction y-direction) > character-direction
    ```

=== "Full Code So Far"
    ```shards

    @define( initialize-images{
      LoadImage("GlodImages/Character1.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping
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

Now if you run the code and press the left and right directional buttons, you will see Glod's image change! Congratulations. Lastly, we need to handle the jumping image. Handling jumping is slightly more tricky. First, let's add the arm handling the case for Glod jumping into the `Match` shard.

=== "Code Added"
    ``` shards
    Match([
      @i2(0 0) {character-image}
      @i2(-1 0) {character-left}
      @i2(1 0) {character-right}
      @i2(0 1) {character-jumping}
      none {character-image}
    ] Passthrough: false)
    ```

=== "Full Code So Far"
    ```shards

    @define( initialize-images{
      LoadImage("GlodImages/Character1.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping
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

If you press the directional keys now, you will quickly realise that there is a problem. Firstly, our Match shard, as it is, is not taking into consideration all the cases. It does not handle cases when `character-direction` is @i2(1 1) (jumping and facing right) or, @i2(-1 1) (jumping and facing left).

To solve this, we have to add the necessary additional arms to `Match` and the additional images to represent these jumping states.

=== "Code Added"
    ```shards
    @define( initialize-images{
      LoadImage("GlodImages/Character1.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping_Left.png") = character-jumping-left
      LoadImage("GlodImages/Character1_Jumping_Right.png") = character-jumping-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping
    })
    ```
    ``` shards
    Match([
      @i2(0 0) {character-image}
      @i2(-1 0) {character-left}
      @i2(1 0) {character-right}
      @i2(-1 1) {character-jumping-left}
      @i2(1 1) {character-jumping-right}
      @i2(0 1) {character-jumping}
      none {character-image}
    ] Passthrough: false)
    ```

=== "Full Code So Far"
    ```shards

    @define( initialize-images{
      LoadImage("GlodImages/Character1.png") = character-image
      LoadImage("GlodImages/Character1_Left.png") = character-left
      LoadImage("GlodImages/Character1_Right.png") = character-right
      LoadImage("GlodImages/Character1_Jumping_Left.png") = character-jumping-left
      LoadImage("GlodImages/Character1_Jumping_Right.png") = character-jumping-right
      LoadImage("GlodImages/Character1_Jumping.png") = character-jumping
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
The other problem we have to solve, is Glod getting stuck on the "jump" image. Currently, there is nothing that resets our y-direction back to 0. Normally, after a character jumps, the state that checks if the character is jumping, will reset back when they land. For now, we will reset our y-direction back to 0 when we let go of the up directional key. To do this, we use the `Inputs.KeyUp` shard. `Inputs.KeyUp` outputs `true` on the frame that the key specified is released.

=== "Code Added"
    ```shards
    Inputs.KeyUp(
      Key: "up"
      Action: {
        0 > y-direction
      }
      Consume: false
      SkipConsumed: true
    )
    ```

=== "Full Code So Far"
    ```shards
    @define( initialize-images{
      LoadImage("Character1_Left.png") = character-image
      LoadImage("Character1_Left.png") = character-left
      LoadImage("Character1_Right.png") = character-right
      LoadImage("Character1_Jumping_Left.png") = character-jumping-left
      LoadImage("Character1_Jumping_Right.png") = character-jumping-right
      LoadImage("Character1_Jumping.png") = character-jumping
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

Now our jumping state resets when the "Up" key is released!

## Recap

Try running the code. Voilà! You now have an image that changes based on the button pressed.

We achieved this by:

1. Creating a `character-direction` variable.

2. Using a `Match` shard to dictate which image is drawn in the window based on the value of `character-direction`.

In the next step we will breathe more life into adorable Glod by animating him!

Looking forward to seeing you in the next step!🙂
