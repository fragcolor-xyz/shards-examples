# Step 5

## Dropping Coins and Scoring Points - Overview

Good job on reaching this far!

Now that we have a moving character, we can add more game elements.

As mentioned in the intro, this will be a point collection game. We'll start by creating coins for Glod to collect.

In this chapter we will be:

1. Drawing a coin image on screen.

2. Having it move by using variables.

3. Creating a spawning system to randomly spawn coins on the screen.

4. Creating a collision system to have Glod be able to collect coins.

5. Reusing code to create more coins by making shards accept arguments.

## Step 5.1

Drawing an image on screen and using variables to move them... Does that not sound familiar? 🤔

Yes! It is using the same logic to make Glod appear on the screen. Download the images that we will be using:

- Download Coin Images [here](https://drive.google.com/file/d/1zqdvZtocDsU9mI_VmN4oqpAYF6L5vSzJ/view?usp=share_link).

Let's start off nice and easy by first drawing the coin image on the screen.

Create a new `initialize-coin` shard. We will separate the initializing of our coin and character variables to make our code neater.

=== "Code Added"
    
    ```shards
    ;; -------------- Initialize Coin ----------
    LoadImage("GlodImages/Coin/Coin_1.png") >= coin-img ;; added to initialize-images
    ```

Remember to call `initialize-coin` in the `Setup` of the `main-wire`to ensure that these variables are only created once.

We then create a new `UI.Area` to draw the coin on the screen.

=== "Code Added"
    
    ```shards
    UI.Area(;; (1)
     Position: @f2(0.0 -60.0)
     Anchor: Anchor::Bottom
     Contents: {
      coin-img | UI.Image
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

      ;; ------------- Coin ---------------
      LoadImage("GlodImages/Coin1.png") >= coin-img
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
          UI({ ;; Because we have more than 1 UI area we now need to wrap the contents of UI area in {}

            UI.Area(;; (1)
            Position: @f2(0.0 -200.0) ;; we give the y position a negative value to shift it up
            Anchor: Anchor::Bottom
            Contents: {
              coin-img | UI.Image
            })

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
          }) | UI.Render(ui-draw-queue)

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

## Step 5.2

Try running your code now. You should see a coin being drawn on the screen.

Congrats!

We are off to a good start. Now, let's animate this coin.

The logic will be exactly the same as when we animated Glod. This will be much easier however as we won't need any conditional statements to control the coin.

- Download coin images [here](https://drive.google.com/drive/folders/1_iAhj70R3kS5hBXKtqh67Cfb_jBKRlzh?usp=share_link).

Just like what we did to animate Glod, we replace the singular image with an array of images.

We also create variables for controlling the Coin animation and a new animate wire to handle the coin's animation as well:

- `coin-image-index`

- `coin-animation-speed`

=== "Code Added"

    ```shards
    LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-array
    LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-array
    LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-array
    LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-array
    LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-array
    LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-array
    LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-array ;; added to initialize-images

    0 >= coin-image-index ;; added to the once where we initialize all our variables

    @wire( animate-coin { ;; create a new animate wire to handle our coin animation.
      Once({
        0.08 = coin-animation-speed
      })
      coin-image-index | Math.Add(1) > coin-image-index
      Count(coin-img-array) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
    } Looped: true)

    Step(animate-coin) ;; Call our wire similar to how we call the animate wire

    coin-img-array | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2)) ;; instead of displaying just one image, we take the current index from the image array
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

      ;; ------------- Coin ---------------
      LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-array
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

    @wire( animate-coin {
      Once({
        0.08 = coin-animation-speed
      })
      coin-image-index | Math.Add(1) > coin-image-index
      Count(coin-img-array) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
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
          When(Predicate: Is(true) Action:{
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

        0 >= coin-image-index
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
        0 > y-direction
      })


      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            Contents: {
            UI.Area(
            Position: @f2(0.0 -200.0)
            Anchor: Anchor::Bottom
            Contents: {
              coin-img-array | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
            })

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
          }) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          Step(animate)
          Step(animate-coin)
          
          GFX.Render(Steps: render-steps)
        }
      )
      
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

Try running your code to see if your coin is animated. Good job if it is!

## Step 5.3

Now let's make our coin fall. To do this, like we did with Glod, we will:

1. Set a variables in the `Position` parameter of the coin's `UI.Area`, replacing the `@f2(0.0 0.0)` used previously with `@f2(coin-pos-x coin-pos-y)`.

2. Change `coin-pos-y` so that our coin falls.

Without further ado, let's get started!

First we create the coin's x, y, and position variables.

=== "Code Added"
    
    ```shards
    ;; ----- Coin 1 ------
    0.0 >= coin-pos-x ;; added to the once where we initialize our variables
    -600.0 >= coin-pos-y ;; we give our y position a negative starting value to shift it up
    ```

We then replace the value of the `Position` parameter in our coin's `UI.Area`.

=== "Code Added"
    
    ```shards
    UI.Area(
      Position: @f2(coin-pos-x coin-pos-y) ;; our new variables are added to the previously hardcoded position
      Anchor: Anchor::Bottom
      Contents: {
        coin-img-array | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
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

      ;; ------------- Coin ---------------
      LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-array
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

    @wire( animate-coin {
      Once({
        0.08 = coin-animation-speed
      })
      coin-image-index | Math.Add(1) > coin-image-index
      Count(coin-img-array) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
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
          When(Predicate: Is(true) Action:{
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

        ;; ------------ coin variables -------------
        0 >= coin-image-index
        0.0 >= coin-pos-x 
        -600.0 >= coin-pos-y
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
        0 > y-direction
      })


      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            Contents: {
            UI.Area(
            Position: @f2(coin-pos-x  coin-pos-y)
            Anchor: Anchor::Bottom
            Contents: {
              coin-img-array | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
            })

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
          }) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          Step(animate)
          Step(animate-coin)
          
          GFX.Render(Steps: render-steps)
        }
      )
      
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

Try changing the `coin-pos-x` or `coin-pos-y` values to check if your coin's position changes. Wonderful!

Now, similar to our running and jumping logic, we have to create a gravity logic to make our coin fall. Let's once again use velocity and acceleration to make our coin fall realistically.

First, create velocity and acceleration variables under the `initialize-coin` shard.

=== "Code Added"
    
    ```shards
    0.0 >= coin-velocity ;; added to the Once where we initialize all our variables
    0.5 >= coin-acceleration
    ```

Next we add our simulated gravity for our coin. The logic behind it is the same as our jump logic. We add velocity to the y position and then add acceleration to our velocity to increase the rate of increase.

=== "Code Added"
    
    ```shards
    ;; ------------- Coin Gravity ------------------
    coin-pos-y | Math.Add(coin-velocity) > coin-pos-y
    coin-velocity | Math.Add(coin-acceleration) > coin-velocity
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

      ;; ------------- Coin ---------------
      LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-array
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

    @wire( animate-coin {
      Once({
        0.08 = coin-animation-speed
      })
      coin-image-index | Math.Add(1) > coin-image-index
      Count(coin-img-array) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
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
          When(Predicate: Is(true) Action:{
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

        ;; ------------ coin variables -------------
        0 >= coin-image-index
        0.0 >= coin-pos-x 
        -600.0 >= coin-pos-y
        0.0 >= coin-velocity
        0.5 >= coin-acceleration
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
        0 > y-direction
      })

      ;; coin-falling-logic
      coin-pos-y | Math.Add(coin-velocity) > coin-pos-y
      coin-velocity | Math.Add(coin-acceleration) > coin-velocity


      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            Contents: {
            UI.Area(
            Position: @f2(coin-pos-x  coin-pos-y)
            Anchor: Anchor::Bottom
            Contents: {
              coin-img-array | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
            })

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
          }) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          Step(animate)
          Step(animate-coin)
          
          GFX.Render(Steps: render-steps)
        }
      )
      
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

## Step 5.4

If you run your code now, you would see our coin falling! Nice. 😁

Now let's have adorable Glod chase some money! 🤑💰💵💶💷

To do that, we need to create the logic to:

1. Check when Glod is colliding with our coin.

2. Gain a point when the collision happens.

Let's do this step by step.

First, nice and easy, let's create a `score` variable.

=== "Code Added"
    
    ```shards
    0 >= score ;; initialized in the Once where we initialize all our variables
    ```

Next, create a new `UI.Area` to draw our `score` using `UI.Label`. Remember to convert our `score` to a string first with the `ToString` shard.

=== "Code Added"
    
    ```shards
    UI.Area(
      Position: @f2(-40 -40)
      Anchor: Anchor::BottomRight ;; this makes the origin of our UI.Area the bottom right of the screen
      Contents: {
        score | ToString | UI.Label ;; UI.Label only accepts a string, so we have to convert our score value which is an int, into a string first.
      }
    )
    ```

If you try to run our code now you will see a teeny tiny 0 in the corner of your screen. This means that our score is being drawn! But it unfortunately has not been styled yet. So let's do that.

We first use UI.Style and then call this shard before we draw our `UI.Label`. We might use this UI.Style repeatedly, so let's use `@define` to create a definition so that we can use it easily. Now if you try running your code you would see that our score is styled nicely.

=== "Code Added"

    ```shards
    ;; ------ UI Style --------
    @define( ui-style {
      UI.Style(OverrideTextStyle: "base-ui" TextStyles: {base-ui: {Size: 16.0
                                                               Family: FontFamily::Proportional}} OverrideTextColor: @color(255 255 255))
    })
    ```
    ```shards
    ;; ------ UI Style --------
    UI.Area(
      Position: @f2(-40 -40)
      Anchor: Anchor::BottomRight
      Contents: {
        @ui-style
        "Score: " | UI.Label ;; Added this label for more clarity
        score | ToString | UI.Label
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

      ;; ------------- Coin ---------------
      LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-array
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

    @wire( animate-coin {
      Once({
        0.08 = coin-animation-speed
      })
      coin-image-index | Math.Add(1) > coin-image-index
      Count(coin-img-array) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
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
          When(Predicate: Is(true) Action:{
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

        ;; ------------ coin variables -------------
        0 >= coin-image-index
        0.0 >= coin-pos-x 
        -600.0 >= coin-pos-y
        0.0 >= coin-velocity
        0.5 >= coin-acceleration
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
        0 > y-direction
      })

      ;; coin-falling-logic
      coin-pos-y | Math.Add(coin-velocity) > coin-pos-y
      coin-velocity | Math.Add(coin-acceleration) > coin-velocity


      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            Contents: {
            UI.Area(
            Position: @f2(coin-pos-x  coin-pos-y)
            Anchor: Anchor::Bottom
            Contents: {
              coin-img-array | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
            })

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

            UI.Area(
              Position: @f2(-40 -40)
              Anchor: Anchor::BottomRight
              Contents: {
                @ui-style
                "Score: " | UI.Label ;; Added this label for more clarity
                score | ToString | UI.Label
              }
            )
          }) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          Step(animate)
          Step(animate-coin)
          
          GFX.Render(Steps: render-steps)
        }
      )
      
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

## Step 5.5

Now that we have our score UI, we can start to increase it. To do this, we need our collision logic.

Create:

1. An upper and lower x limit.

2. An upper and lower y limit.

This is to draw a collision box around our dear Glod. Since our character is moving, we have to constantly update our collision box and thus put it outside the `Once`. 

![Glod Collision Box](assets/step5_5_Image.png){ width=300 }

We also create a `scored` variable to limit the number of times we can score from one coin. These variables will be added under `initialize-game-elements`.

=== "Code Added"
    
    ```shards
    pos-x | Math.Add(50.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
    pos-x | Math.Subtract(50.0) = collision-x-lower-limit

    pos-y | Math.Add(50.0) = collision-y-upper-limit
    pos-y | Math.Subtract(50.0) = collision-y-lower-limit
    
    false >= scored
    ```

Next, we will implement our collision logic with a conditional statement.

Create the logic to trigger whenever our coin falls within our collision box.

This conditional statement will check if the x value of our coin:

- Is lesser then the upper x limit of our collision box.

- Is more than the lower x limit of our collision box.

It is checking if it within the stated range and does the same for the y value. Next, we increase the score whenever this happens and change scored to true, so that this only happens once.



=== "Code Added"
    
    ```shards
    ;; ------------- Scoring ------------
    When(
      Predicate: {
        coin-pos-x | IsMoreEqual(collision-x-lower-limit)
        And
        coin-pos-x | IsLessEqual(collision-x-upper-limit)
        And
        coin-pos-y | IsMoreEqual(collision-y-lower-limit)
        And
        coin-pos-y | IsLessEqual(collision-y-upper-limit)
        And
        scored | Is(false)
      }
      Action: {
        score | Math.Add(1) > score
        true > scored
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

      ;; ------------- Coin ---------------
      LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-array
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

    @wire( animate-coin {
      Once({
        0.08 = coin-animation-speed
      })
      coin-image-index | Math.Add(1) > coin-image-index
      Count(coin-img-array) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
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
          When(Predicate: Is(true) Action:{
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
        0 > score

        ;; ------------ coin variables -------------
        0 >= coin-image-index
        0.0 >= coin-pos-x 
        -600.0 >= coin-pos-y
        0.0 >= coin-velocity
        0.5 >= coin-acceleration
        
        false >= scored
      })

      pos-x | Math.Add(50.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
      pos-x | Math.Subtract(50.0) = collision-x-lower-limit

      pos-y | Math.Add(50.0) = collision-y-upper-limit
      pos-y | Math.Subtract(50.0) = collision-y-lower-limit

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
        0 > y-direction
      })

      ;; coin-falling-logic
      coin-pos-y | Math.Add(coin-velocity) > coin-pos-y
      coin-velocity | Math.Add(coin-acceleration) > coin-velocity

      ;; coin-scoring logic
      When(
        Predicate: {
          coin-pos-x | IsMoreEqual(collision-x-lower-limit)
          And
          coin-pos-x | IsLessEqual(collision-x-upper-limit)
          And
          coin-pos-y | IsMoreEqual(collision-y-lower-limit)
          And
          coin-pos-y | IsLessEqual(collision-y-upper-limit)
          And
          scored | Is(false)
        }
        Action: {
          score | Math.Add(1) > score
          true > scored
        }
      )

      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            Contents: {
            UI.Area(
            Position: @f2(coin-pos-x  coin-pos-y)
            Anchor: Anchor::Bottom
            Contents: {
              coin-img-array | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
            })

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

            UI.Area(
              Position: @f2(-40 -40)
              Anchor: Anchor::BottomRight
              Contents: {
                @ui-style
                "Score: " | UI.Label ;; Added this label for more clarity
                score | ToString | UI.Label
              }
            )
          }) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          Step(animate)
          Step(animate-coin)
          
          GFX.Render(Steps: render-steps)
        }
      )
      
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

Try running your code now and see if you get a point. You did!? Congratulations!

# Step 5.6

Now that we have a scoring system, let's create more coins.

Currently, we have only one coin falling down. Let's reuse this coin and have it appear again in another random x position for Glod to run after and collect.

Whenever our coin falls through the ground or if Glod manages to collect it, let's reset its `coin-pos-y` and give it a random `coin-pos-x`. Let's also reset `scored` to false so that we can score from this coin again 

=== "Code Added"
    
    ```shards
    When(
      Predicate: {
        scored | Is(true)
        Or ;; or allows the code in Action to happen when either of these conditions return true
        coin-pos-y | IsLess(0.0)
      }
      Action: {
        -600.0 > coin-pos-y ;; reset our coin's y position
        RandomFloat(Max: 1000.0) | Math.Subtract(500.0) > coin-pos-x ;; Putting a Max: of 1000.0 and then subtracting 500.0 means a random 
                                                                     ;; value between -500.0 and 500.0 is generated
        0 > coin-velocity ;; reset velocity back to 0 so that the coin goes back to its original speed.
        false > scored ;; reset scored so that we can score again
      }
    )
    ```

Now to have more than one coin, we can put the variables and the logic that we want to be unique to each coin and put it into a loopable wire. We then create the necessary number of copies of that wire.

=== "Code Added"

    ```shards
    @wire(coin {
      Once({
        ;; ------------ coin variables ------------- ;; all variables that we want to be unique to each coin, we initialize in the new wire instead
        0 >= coin-image-index
        RandomFloat(Max: 1000.0) | Math.Subtract(500.0) >= coin-pos-x  ;; randomise the position right at the start
        -600.0 >= coin-pos-y
        0.0 >= coin-velocity
        0.5 >= coin-acceleration
        
        false >= scored
      })

      pos-x | Math.Add(50.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
      pos-x | Math.Subtract(50.0) = collision-x-lower-limit

      pos-y | Math.Add(50.0) = collision-y-upper-limit
      pos-y | Math.Subtract(50.0) = collision-y-lower-limit

      ;; coin-falling-logic
      coin-pos-y | Math.Add(coin-velocity) > coin-pos-y
      coin-velocity | Math.Add(coin-acceleration) > coin-velocity

      ;; coin-scoring logic
      When(
        Predicate: {
          coin-pos-x | IsMoreEqual(collision-x-lower-limit)
          And
          coin-pos-x | IsLessEqual(collision-x-upper-limit)
          And
          coin-pos-y | IsMoreEqual(collision-y-lower-limit)
          And
          coin-pos-y | IsLessEqual(collision-y-upper-limit)
          And
          scored | Is(false)
        }
        Action: {
          score | Math.Add(1) > score
          true > scored
        }
      )

      When(
        Predicate: {
          scored | Is(true)
          Or ;; or allows the code in Action to happen when either of these conditions return true
          coin-pos-y | IsLess(0.0)
        }
        Action: {
          -600.0 > coin-pos-y ;; reset our coin's y position
          RandomFloat(Max: 1000.0) | Math.Subtract(500.0) > coin-pos-x
          0 > coin-velocity
          false > scored ;; reset scored so that we can score again
        }
      )

      UI.Area(
        Position: @f2(coin-pos-x  coin-pos-y)
        Anchor: Anchor::Bottom
        Contents: {
          coin-img-array | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
      })
    } Looped: true)
    ```
Then we use `DoMany` to schedule multiple copies of this wire and call it within `UI`.

=== "Code Added"

    ```shards
    [1 2 3] ;; increase the number of elements to increase the number of wire scheduled
    DoMany(coin ComposedSync: true) ;; ComposedSync is set to true to allow the wires to run in a UI environment.
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

      ;; ------------- Coin ---------------
      LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-array
      LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-array
    })

    @define( ui-style {
      UI.Style(OverrideTextStyle: "base-ui" TextStyles: {base-ui: {Size: 16.0
                                                                Family: FontFamily::Proportional}} OverrideTextColor: @color(255 255 255))
    })

    @wire(coin {
      Once({
        ;; ------------ coin variables ------------- ;; all variables that we want to be unique to each coin, we initialize in the new wire instead
        0 >= coin-image-index
        RandomFloat(Max: 1000.0) | Math.Subtract(500.0) >= coin-pos-x 
        -600.0 >= coin-pos-y
        0.0 >= coin-velocity
        0.5 >= coin-acceleration
        
        false >= scored
      })

      pos-x | Math.Add(50.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
      pos-x | Math.Subtract(50.0) = collision-x-lower-limit

      pos-y | Math.Add(50.0) = collision-y-upper-limit
      pos-y | Math.Subtract(50.0) = collision-y-lower-limit

      ;; coin-falling-logic
      coin-pos-y | Math.Add(coin-velocity) > coin-pos-y
      coin-velocity | Math.Add(coin-acceleration) > coin-velocity

      ;; coin-scoring logic
      When(
        Predicate: {
          coin-pos-x | IsMoreEqual(collision-x-lower-limit)
          And
          coin-pos-x | IsLessEqual(collision-x-upper-limit)
          And
          coin-pos-y | IsMoreEqual(collision-y-lower-limit)
          And
          coin-pos-y | IsLessEqual(collision-y-upper-limit)
          And
          scored | Is(false)
        }
        Action: {
          score | Math.Add(1) > score
          true > scored
        }
      )

      When(
        Predicate: {
          scored | Is(true)
          Or ;; or allows the code in Action to happen when either of these conditions return true
          coin-pos-y | IsMore(0.0)
        }
        Action: {
          -600.0 > coin-pos-y ;; reset our coin's y position
          RandomFloat(Max: 1000.0) | Math.Subtract(500.0) > coin-pos-x
          0.0 > coin-velocity
          false > scored ;; reset scored so that we can score again
        }
      )

      Step(animate-coin)

      UI.Area(
        Position: @f2(coin-pos-x  coin-pos-y)
        Anchor: Anchor::Bottom
        Contents: {
          coin-img-array | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
      })
    } Looped: true)

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

    @wire( animate-coin { ;; create a new animate wire to handle our coin animation.
      Once({
        0.08 = coin-animation-speed
      })
      coin-image-index | Math.Add(1) > coin-image-index
      Count(coin-img-array) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
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
          When(Predicate: Is(true) Action:{
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
        0 >= score
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
        0 > y-direction
      })

      GFX.MainWindow(
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })
          UI(
            Contents: {
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

            UI.Area(
              Position: @f2(-40 -40)
              Anchor: Anchor::BottomRight ;; this makes the origin of our UI.Area the bottom right of the screen
              Contents: {
                @ui-style
                "Score: " | UI.Label
                score | ToString | UI.Label ;; UI.Label only accepts a string, so we have to convert our score value which is an int, into a string first.
              }
            )

            [1 2 3]
            DoMany(coin ComposeSync: true)

          }) | UI.Render(ui-draw-queue)

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

Try running your code now!

Congrats, you have a very basic game. You can try it out and have fun chasing coins! 😁

## Recap

Phew that was a really long step!

Good job for making it through. 😁🎉

To recap:

- We made a coin fall by using the same logic to make our character move.

- We created a scoring system by making a collision system.

- We set up a system to create as many coins as we want by making our `defshards` accept arguments.

Phew! That was a mouthful but worth it!

In the next chapter, we will use the same logic to create falling hazards for Glod!

See you! 👋🏼😀
