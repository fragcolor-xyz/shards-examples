# Step 4

## Moving the Character with Button Inputs - Overview

In the previous chapter, we breathed some life into Glod and transformed him from a static image into a fully animated character.

Amazing!

However, we can take things further. Let's free Glod from his stationary curse and make him move! To do this we will:

1. Place a variable in the `Position` tag of `UI.Area`.
2. Change this variable to make Glod move.
3. Use velocity and acceleration concepts to make Glod jump.

## Step 4.1: Shifting Anchors

First, let's edit our code a little. Currently, Glod is situated in the center of the screen. While this can work, we can instead change his current `Anchor::Center` to `Anchor::Bottom`, and bring him down to Earth by changing his position. We achieve this by creating some variables.

??? ":bulb:"
    While any anchor will work as long as we change the position accordingly, we will be using `Anchor::Bottom` to keep things consistent and manage the positions of the different game elements more effectively.

Create the `pos-x` and `pos-y` variables to hold Glod's x and y positions.

=== "Code Added"
    
    ```shards
    0.0 >= pos-x
    0.0 >= pos-y
    ```

`pos-x` and `pos-y` is then fed into the `Position` parameter of the `UI.Area` shard. When the `pos-x` and `pos-y` values are changed, the `UI.Area` will move and hence move our character.

Remember to change the `Anchor` to `Anchor::Bottom`!

=== "Code Added"
    ```shards
    UI.Area(
     Position: @f2(pos-x pos-y)
     Anchor: Anchor::Bottom
     Contents:
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
        0.0 >= pos-x
        0.0 >= pos-y
      })


      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            UI.Area(
              Position: @f2(pos-x pos-y)
              Anchor: Anchor::Bottom
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

## Step 4.2: Moving left and right

Now that our variables are set up, it's time to make Glod move! Let's start by moving him to the left and right.

To do this, we simply have to change the `pos-x` value that we have just created whenever the left and right buttons are pressed.

First, create a new variable named `character-x-velocity`. This is the value our `pos-x` will be increased or decreased by.

=== "Code Added"
    
    ```shards
    3.0 >= character-x-velocity ;;increase or decrease this value to control how fast Glod moves

    pos-x | Math.Add(character-x-velocity) > pos-x
    ```

Since we already have a a variable `x-direction` that stores the direction of Glod along the x-axis, we multiply `character-x-velocity` by `x-direction`, so that Glod will move left or right accordingly.

=== "Code Added"

    ```shards
    ;; ------- Button Inputs ----------
    pos-x | Math.Add((character-x-velocity | Math.Multiply(x-direction))) > pos-x
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
        0.0 >= pos-x
        0.0 >= pos-y

        3.0 >= character-x-velocity
      })

      pos-x | Math.Add((character-x-velocity | Math.Multiply(x-direction))) > pos-x


      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            UI.Area(
              Position: @f2(pos-x pos-y)
              Anchor: Anchor::Bottom
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

## Step 4.3: Adding horizontal boundaries

Oh no! We now have a problem. 🥲

If we continue moving Glod left or right, you might notice that he goes off-screen.

Let's set up a boundary to ensure that Glod does not fall off the edge and into the abyss. To do this, we use `Clamp` that limits how far our `pos-x` can go.

=== "Code Added"
    
    ```shards
    ;; ---------- Character Boundary ------------ ;; add this right after we change our x values
    pos-x
    Clamp(Min: -600.0 Max: 600.0) > pos-x ;; change the `min` and `max` parameters to fit your screen accordingly.
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
        0.0 >= pos-x
        0.0 >= pos-y

        3.0 >= character-x-velocity
      })

      pos-x | Math.Add((character-x-velocity | Math.Multiply((x-direction | ToFloat)))) > pos-x
      Clamp(Min: -600.0 Max: 600.0) > pos-x


      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            UI.Area(
              Position: @f2(pos-x pos-y)
              Anchor: Anchor::Bottom
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

`Clamp` takes in a value and makes sure that it does not exceed the minimum and maximum values.
Use the`Clamp` shard after adding to our `pos-x` value, the `min` parameter to be -600.0, and the `max` parameter to be 600.0. 

Yay! Now Glod will not walk off into the void! 😙

## Step 4.4: Making Glod Jump

Now, let's make Glod jump!

Instead of just using velocity, we can add acceleration into the mix. This will make Glod's jump feel more realistic.

??? "Jumping using Velocity & Acceleration"
    For smooth realistic jumping, we have to decrease velocity by acceleration whenever we jump. Decreasing velocity by acceleration decreases the rate of increase of `pos-y`.
    
    This simulates gravity, making our jump feel more realistic.

First, create the variables that we will use.

=== "Code Added"
    
    ```shards
    0.0 >= character-y-velocity ;; (1)
    2.0 >= character-y-acceleration
    ```

Similar to `character-x-velocity`, we will add `character-y-velocity` to our `pos-y` variable. This time however, we will also add `character-y-acceleration` to `character-y-velocity`.

=== "Code Added"

    ```shards
    ;; ------------ Character Gravity Logic ---------------
    pos-y | Math.Subtract(character-y-velocity) > pos-y
    character-y-velocity | Math.Subtract(character-y-acceleration) > character-y-velocity
    ```

Lastly we modify the value of `character-y-velocity` whenever the Up directional button is pressed.

=== "Code Added"

    ```shards
    Inputs.KeyDown(
      Key: "up"
      Action: {
        Msg("up")
        1 > y-direction
        30.0 > character-y-velocity
        false > grounded
      }
    )
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
          30.0 > character-y-velocity
          false > grounded
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
        0.0 >= pos-x
        0.0 >= pos-y

        3.0 >= character-x-velocity
        0.0 >= character-y-velocity
        2.0 >= character-y-acceleration

        true >= grounded
      })

      pos-x | Math.Add((character-x-velocity | Math.Multiply((x-direction | ToFloat)))) > pos-x
      Clamp(Min: -600.0 Max: 600.0) > pos-x

      pos-y | Math.Subtract(character-y-velocity) > pos-y
      character-y-velocity | Math.Subtract(character-y-acceleration) > character-y-velocity


      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            UI.Area(
              Position: @f2(pos-x pos-y)
              Anchor: Anchor::Bottom
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

??? ":bulb:"
    Since our `Anchor` is set to `Anchor::Bottom`, it places the `UI.Area` at the bottom centre of the screen. This is now our origin at (0,0).
    
    In Shards, increasing the value of the y-axis pushes our `UI.Area` down while decreasing it pushes it up.

## Step 4.5: Adding vertical boundaries

Try running your code now! 

Oh no, we have a problem. Glod falls through the floor! 😨

Don't worry, we can fix this!

Similar to how we made a boundary to ensure that Glod does not walk off-screen, we can add a boundary to ensure he doesn't fall through the floor. To do this, we just have to set our `pos-y` value back to 0 whenever it goes beyond 0.0. Additionally, let's add a `grounded` variable so that our jump and simulated gravity only activates when Glod is jumping and make our code more efficient.

You might also have noticed that Glod can keep jumping as long as we repeatedly press the jump key. Since we do not want this behaviour, we can easily fix this by only increasing the `character-y-velocity` when the up key is pressed only if `grounded` is true

=== "Code Added"
    
    ```shards
    true >= grounded ;; initialize our grounded variable

    Inputs.KeyDown(
      Key: "up"
      Action: {
        Msg("up")
        1 > y-direction
        grounded
        When(Predicate: Is(true) Action: {
          30.0 > character-y-velocity
          false > grounded ;; make grounded false when up key is pressed
        })
      }
    )

    grounded
    If(Predicate: Is(false) Then: {
      pos-y | Math.Subtract(character-y-velocity) > pos-y
      character-y-velocity | Math.Subtract(character-y-acceleration) > character-y-velocity
    }) ;; only activate our simulated gravity when we are jumping

    pos-y
    When(Predicate: IsMoreEqual(0.0) Action: {
      true > grounded
      0.0 > pos-y
    }) ;; reset grounded back to true when we touch the ground and make our pos-y 0
    ```

Lastly, let's tighten our code by fixing a few other bugs that you might have also noticed for niche cases.

  1. If you do not let go of the up key. Glod remains in the jump animation even after he lands.

  2. If you continue to hold the up key and then press the left or right keys, Glod moves left or right but remains in the jump position. 

To fix this, we just have to reset Glod's `y-direction` back to 0 when he lands.

=== "Code Added"
    ```shards
    pos-y
    When(Predicate: IsMoreEqual(0.0) Action: {
      true > grounded
      0.0 > pos-y
      0 > y-direction
    })
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
          grounded
          When(Predicate: Is(true) Action: {
            30.0 > character-y-velocity
            false > grounded ;; make grounded false when up key is pressed
          })
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
        0.0 >= pos-x
        0.0 >= pos-y

        3.0 >= character-x-velocity
        0.0 >= character-y-velocity
        2.0 >= character-y-acceleration

        true >= grounded
      })

      pos-x | Math.Add((character-x-velocity | Math.Multiply((x-direction | ToFloat)))) > pos-x
      Clamp(Min: -600.0 Max: 600.0) > pos-x

      grounded
      If(Predicate: Is(false) Then: {
        pos-y | Math.Subtract(character-y-velocity) > pos-y
        character-y-velocity | Math.Subtract(character-y-acceleration) > character-y-velocity
      })

      pos-y
      When(Predicate: IsMoreEqual(0.0) Action: {
        true > grounded
        0.0 > pos-y
      })


      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            UI.Area(
              Position: @f2(pos-x pos-y)
              Anchor: Anchor::Bottom
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

Congratulations! Glod can now move and jump safely! 😊

## Recap

Try running your code now! Glod should be happily running and jumping without any glitches. 😁

To recap:

1. We made Glod move by tying a variable to the `UI.Area` position and changing said variable.

2. Simulated gravity by using velocity and acceleration.

3. We ensured that Glod does not walk or fall into the void by creating boundaries.

We now have a fully functional moving character!

In the next chapter, let's have Glod collect some coins! 🤑
