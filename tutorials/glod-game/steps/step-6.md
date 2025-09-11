# Step 6

## Making the Game Harder - Overview

We are almost towards the end of the tutorial. Wahoo!

In the previous chapter, we learnt how to make coins for Glod to collect.

In this chapter, we will do the opposite. We will make the game more challenging by creating hazards for Glod to avoid!

Poor Glod. 😢

To do this we will be:

1. Drawing a spiked cannonball image on screen.

2. Having it move using variables.

3. Creating a spawning system to randomly spawn a spiked cannonball on screen.

4. Creating a collision system to allow Glod to be hit.

5. Spawning multiple spiked cannonball

If you are wondering why the list above seems familiar, it is because the exact same logic and methods were used in the previous chapter.

Let's get to it. 🔥

# Step 6.1

We start off nice and easy by downloading the required images and displaying one of them on the screen.

1. Download spiked cannonball images [here](https://drive.google.com/drive/folders/1qbuwq-DiO0AqiprdQPtm_UeqBG67uN6K?usp=share_link).

Load one image first. To keep our code organized, create a new `initialize-spiked-cannonballs` shard.

=== "Code Added"
    
    ```shards
    ;; ------------ Initialize Spiked CanonBalls ---------------
    LoadImage("GlodImages/SpikeBall/SpikeBall1.png") = spikeball-1 ;; Added to our @initialize-images
    ```

To draw our loaded image, create a `UI.Area`.

=== "Code Added"

    ```shards
    UI.Area(
    Position: @f2(0.0 -600.0)
    Anchor: Anchor::Bottom
    Contents:{
      spikeball-1 | UI.Image( Scale: @f2(0.15 0.15))
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
      LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-sequence

      ;; ------------- Spiked Canonball -------------
      LoadImage("GlodImages/SpikeBall/SpikeBall1.png") = spikeball-1
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
          coin-img-sequence | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
      })
    } Looped: true)

    @wire( animate {
      Once({
        0.08 = idle-animation-speed
      })
      image-index | Math.Add(1) > image-index
      Count(active-animation) = index-max
      image-index
      When(Predicate: IsMoreEqual((index-max | Math.Subtract(1))) Action: {
        0 > image-index
      })
      Pause(idle-animation-speed)
    } Looped: true)

    @wire( animate-coin { ;; create a new animate wire to handle our coin animation.
      Once({
        0.08 = coin-animation-speed
      })
      coin-image-index | Math.Add(1) > coin-image-index
      Count(coin-img-sequence) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual((index-max | Math.Subtract(1))) Action: {
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

            UI.Area(
              Position: @f2(0.0 -600.0)
              Anchor: Anchor::Bottom
              Contents:{
                spikeball-1 | UI.Image( Scale: @f2(0.15 0.15))
            })

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

## Step 6.2

Similar to what we did with our coin, it's time to animate our spiked cannonball.

Follow these steps to create an animation once more:

1. Create an `Image` sequence.

2. Create the necessary variables - an index, max index, and animation speed variable.

=== "Code Added"
    
    ```shards
    ;; ------------ Initialize Spiked CanonBalls ---------------
    LoadImage("GlodImages/SpikeBall/SpikeBall1.png") >> spikeball-sequence
    LoadImage("GlodImages/SpikeBall/SpikeBall2.png") >> spikeball-sequence
    LoadImage("GlodImages/SpikeBall/SpikeBall3.png") >> spikeball-sequence
    LoadImage("GlodImages/SpikeBall/SpikeBall4.png") >> spikeball-sequence
    LoadImage("GlodImages/SpikeBall/SpikeBall5.png") >> spikeball-sequence
    LoadImage("GlodImages/SpikeBall/SpikeBall6.png") >> spikeball-sequence
    LoadImage("GlodImages/SpikeBall/SpikeBall7.png") >> spikeball-sequence
    LoadImage("GlodImages/SpikeBall/SpikeBall8.png") >> spikeball-sequence
    LoadImage("GlodImages/SpikeBall/SpikeBall9.png") >> spikeball-sequence
    LoadImage("GlodImages/SpikeBall/SpikeBall10.png") >> spikeball-sequence
    LoadImage("GlodImages/SpikeBall/SpikeBall11.png") >> spikeball-sequence
    LoadImage("GlodImages/SpikeBall/SpikeBall12.png") >> spikeball-sequence

    0 >= spikeball-index ;; initialize this index in the Once where we initialize all our variables

    @wire(animate-spikeball {
      Once({
        0 >= spikeball-index 
        0.06 >= spikeball-animation-speed
      })

      spikeball-index | Math.Add(1) > spikeball-index
      Count(spikeball-sequence) = spikeball-max
      spikeball-index
      When(Predicate: IsMoreEqual((spikeball-max | Math.Subtract(1))) Action: {
        0 > spikeball-index
      })
      Pause(spikeball-animation-speed)
    } Looped: true)

    Step(animate-spikeball)

    UI.Area(
      Position: @f2(0.0 -600.0)
      Anchor: Anchor::Bottom
      Contents:{
        spikeball-sequence | Take | UI.Image( Scale: @f2(0.15 0.15))
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
      LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-sequence

      ;; ------------- Spiked Canonball -------------
      LoadImage("GlodImages/SpikeBall/SpikeBall1.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall2.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall3.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall4.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall5.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall6.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall7.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall8.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall9.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall10.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall11.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall12.png") >> spikeball-sequence
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
        0.3 >= coin-acceleration
        
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
          coin-img-sequence | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
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
      Count(coin-img-sequence) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
    } Looped: true)

    @wire(animate-spikeball {
      Once({
        0.06 >= spikeball-animation-speed
      })

      spikeball-index | Math.Add(1) > spikeball-index
      Count(spikeball-sequence) = spikeball-max
      spikeball-index
      When(Predicate: IsMoreEqual(spikeball-max) Action: {
        0 > spikeball-index
      })
      Pause(spikeball-animation-speed)
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
            false > grounded ;; male grounded false when up key is pressed
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

        0 >= spikeball-index
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
                "Score: " | ToString | UI.Label
                score | ToString | UI.Label ;; UI.Label only accepts a string, so we have to convert our score value which is an int, into a string first.
              }
            )

            UI.Area(
              Position: @f2(-40 -40)
              Anchor: Anchor::Bottom ;; this makes the origin of our UI.Area the bottom right of the screen
              Contents: {
                spikeball-sequence | Take(spikeball-index) | UI.Image( Scale: @f2(0.10 0.10))
              }
            )

            [1 2 3]
            DoMany(coin ComposeSync: true)

          }) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          Step(animate)
          Step(animate-spikeball)
          
          GFX.Render(Steps: render-steps)
        }
      )
      
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

## Step 6.3

Time to make our spiked cannonball fall.

As in the previous chapter, we will:

1. Create variables that dictate the position of our spiked cannonball.

2. Change our `UI.Area`'s `position` parameter to use this variable.

3. Simulate gravity by using velocity and acceleration.

4. put our spikeball into a loopable wire

Initialize the code variables that we will be using to move our spiked cannonball.

=== "Code Added"
    
    ```shards
    ;; ---------- spikball-1 -------------
    @wire(spike-cannonball {
      Once({
        1.0 >= spikeball-velocity
        -600.0 >= spikeball-y
        0.0 >= spikeball-x

        0.5 >= spikeball-acceleration

        0 >= spikeball-index
      })

      spikeball-y | Math.Add(spikeball-velocity) > spikeball-y
      spikeball-velocity | Math.Add(spikeball-acceleration) > spikeball-velocity

      Step(animate-spikeball)

      UI.Area(
        Position: @f2(spikeball-x spikeball-y)
        Anchor: Anchor::Bottom ;; this makes the origin of our UI.Area the bottom right of the screen
        Contents: {
          spikeball-sequence | Take(spikeball-index) | UI.Image( Scale: @f2(0.10 0.10))
        }
      )
    } Looped: false)

    Do(spike-cannonball) ;; we schedule our wire with a Do within our UI
    ```

=== "Full Code So Far"
    
    ```shards
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
        0.3 >= coin-acceleration
        
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
          coin-img-sequence | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
      })
    } Looped: true)

    @wire(spike-cannonball {
      Once({
        1.0 >= spikeball-velocity
        -600.0 >= spikeball-y
        0.0 >= spikeball-x

        0.5 >= spikeball-acceleration

        0 >= spikeball-index
      })

      spikeball-y | Math.Add(spikeball-velocity) > spikeball-y
      spikeball-velocity | Math.Add(spikeball-acceleration) > spikeball-velocity

      Step(animate-spikeball)

      UI.Area(
        Position: @f2(spikeball-x spikeball-y)
        Anchor: Anchor::Bottom ;; this makes the origin of our UI.Area the bottom right of the screen
        Contents: {
          spikeball-sequence | Take(spikeball-index) | UI.Image( Scale: @f2(0.10 0.10))
        }
      )
    } Looped: false)

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
      Count(coin-img-sequence) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
    } Looped: true)

    @wire(animate-spikeball {
      Once({
        0.06 >= spikeball-animation-speed
      })

      spikeball-index | Math.Add(1) > spikeball-index
      Count(spikeball-sequence) = spikeball-max
      spikeball-index
      When(Predicate: IsMoreEqual(spikeball-max) Action: {
        0 > spikeball-index
      })
      Pause(spikeball-animation-speed)
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
            false > grounded ;; male grounded false when up key is pressed
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

        0 >= spikeball-index
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
                "Score: " | ToString | UI.Label
                score | ToString | UI.Label ;; UI.Label only accepts a string, so we have to convert our score value which is an int, into a string first.
              }
            )

            Do(spike-cannonball)

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

Try running your code now to see if it falls!

## Step 6.4

Next, we set up our spike ball collisions and subtract a point whenever Glod collides with our spikeball.

=== "Code Added"
    
    ```shards
    pos-x | Math.Add(50.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
    pos-x | Math.Subtract(50.0) = collision-x-lower-limit

    pos-y | Math.Add(50.0) = collision-y-upper-limit
    pos-y | Math.Subtract(50.0) = collision-y-lower-limit
    
    false >= score-subtracted

    ;; spikeball-scoring logic
    When(
      Predicate: {
        spikeball-x | IsMoreEqual(collision-x-lower-limit)
        And
        spikeball-x | IsLessEqual(collision-x-upper-limit)
        And
        spikeball-y | IsMoreEqual(collision-y-lower-limit)
        And
        spikeball-y | IsLessEqual(collision-y-upper-limit)
        And
        score-subtracted | Is(false)
      }
      Action: {
        score | Math.Subtract(1) > score
        true > score-subtracted
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
      LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-sequence

      ;; ------------- Spiked Canonball -------------
      LoadImage("GlodImages/SpikeBall/SpikeBall1.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall2.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall3.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall4.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall5.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall6.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall7.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall8.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall9.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall10.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall11.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall12.png") >> spikeball-sequence
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
        0.3 >= coin-acceleration
        
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
          coin-img-sequence | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
      })
    } Looped: true)

    @wire(spike-cannonball {
      Once({
        1.0 >= spikeball-velocity
        -600.0 >= spikeball-y
        0.0 >= spikeball-x

        0.5 >= spikeball-acceleration

        0 >= spikeball-index
        
        false >= score-subtracted
      })

      pos-x | Math.Add(50.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
      pos-x | Math.Subtract(50.0) = collision-x-lower-limit

      pos-y | Math.Add(50.0) = collision-y-upper-limit
      pos-y | Math.Subtract(50.0) = collision-y-lower-limit

      spikeball-y | Math.Add(spikeball-velocity) > spikeball-y
      spikeball-velocity | Math.Add(spikeball-acceleration) > spikeball-velocity

      When(
        Predicate: {
          spikeball-x | IsMoreEqual(collision-x-lower-limit)
          And
          spikeball-x | IsLessEqual(collision-x-upper-limit)
          And
          spikeball-y | IsMoreEqual(collision-y-lower-limit)
          And
          spikeball-y | IsLessEqual(collision-y-upper-limit)
          And
          score-subtracted | Is(false)
        }
        Action: {
          score | Math.Subtract(1) > score
          true > score-subtracted
        }
      )

      Step(animate-spikeball)

      UI.Area(
        Position: @f2(spikeball-x spikeball-y)
        Anchor: Anchor::Bottom ;; this makes the origin of our UI.Area the bottom right of the screen
        Contents: {
          spikeball-sequence | Take(spikeball-index) | UI.Image( Scale: @f2(0.10 0.10))
        }
      )
    } Looped: false)

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
      Count(coin-img-sequence) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
    } Looped: true)

    @wire(animate-spikeball {
      Once({
        0.06 >= spikeball-animation-speed
      })

      spikeball-index | Math.Add(1) > spikeball-index
      Count(spikeball-sequence) = spikeball-max
      spikeball-index
      When(Predicate: IsMoreEqual(spikeball-max) Action: {
        0 > spikeball-index
      })
      Pause(spikeball-animation-speed)
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
            false > grounded ;; male grounded false when up key is pressed
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

        0 >= spikeball-index
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
                "Score: " | ToString | UI.Label
                score | ToString | UI.Label ;; UI.Label only accepts a string, so we have to convert our score value which is an int, into a string first.
              }
            )

            Do(spike-cannonball)

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

# Step 6.5

Next, let's have our spike-cannonball reset when it either collides with Glod or reaches the ground.

=== "Code Added"
    
    ```shards
    ;; ---------- Damage Limits ------------
    When(
      Predicate: {
        score-subtracted | Is(true)
        Or
        spikeball-y | IsMore(0.0)
      }
      Action: {
        -600.0 > spikeball-y ;; reset our spikeball's y position
        RandomFloat(Max: 1000.0) | Math.Subtract(500.0) > spikeball-x ;; randomize our spikeball-x position
        0.0 > spikeball-velocity
        false > score-subtracted ;; reset scored so that we can score again
      }
    )
    ;;
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
      LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-sequence

      ;; ------------- Spiked Canonball -------------
      LoadImage("GlodImages/SpikeBall/SpikeBall1.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall2.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall3.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall4.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall5.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall6.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall7.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall8.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall9.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall10.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall11.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall12.png") >> spikeball-sequence
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
        0.3 >= coin-acceleration
        
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
          coin-img-sequence | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
      })
    } Looped: true)

    @wire(spike-cannonball {
      Once({
        1.0 >= spikeball-velocity
        -600.0 >= spikeball-y
        RandomFloat(Max: 1000.0) | Math.Subtract(500.0) >= spikeball-x ;; randomize our spikeball-x position at the start too

        0.5 >= spikeball-acceleration

        0 >= spikeball-index
        
        false >= score-subtracted
      })

      pos-x | Math.Add(50.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
      pos-x | Math.Subtract(50.0) = collision-x-lower-limit

      pos-y | Math.Add(50.0) = collision-y-upper-limit
      pos-y | Math.Subtract(50.0) = collision-y-lower-limit

      spikeball-y | Math.Add(spikeball-velocity) > spikeball-y
      spikeball-velocity | Math.Add(spikeball-acceleration) > spikeball-velocity

      When(
        Predicate: {
          spikeball-x | IsMoreEqual(collision-x-lower-limit)
          And
          spikeball-x | IsLessEqual(collision-x-upper-limit)
          And
          spikeball-y | IsMoreEqual(collision-y-lower-limit)
          And
          spikeball-y | IsLessEqual(collision-y-upper-limit)
          And
          score-subtracted | Is(false)
        }
        Action: {
          score | Math.Subtract(1) > score
          true > score-subtracted
        }
      )

      When(
        Predicate: {
          score-subtracted | Is(true)
          Or
          spikeball-y | IsMore(0.0)
        }
        Action: {
          -600.0 > spikeball-y ;; reset our spikeball's y position
          RandomFloat(Max: 1000.0) | Math.Subtract(500.0) > spikeball-x ;; randomize our spikeball-x position
          0.0 > spikeball-velocity
          false > score-subtracted ;; reset scored so that we can score again
        }
      )

      Step(animate-spikeball)

      UI.Area(
        Position: @f2(spikeball-x spikeball-y)
        Anchor: Anchor::Bottom ;; this makes the origin of our UI.Area the bottom right of the screen
        Contents: {
          spikeball-sequence | Take(spikeball-index) | UI.Image( Scale: @f2(0.10 0.10))
        }
      )
    } Looped: false)

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
      Count(coin-img-sequence) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
    } Looped: true)

    @wire(animate-spikeball {
      Once({
        0.06 >= spikeball-animation-speed
      })

      spikeball-index | Math.Add(1) > spikeball-index
      Count(spikeball-sequence) = spikeball-max
      spikeball-index
      When(Predicate: IsMoreEqual(spikeball-max) Action: {
        0 > spikeball-index
      })
      Pause(spikeball-animation-speed)
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
            false > grounded ;; male grounded false when up key is pressed
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

        0 >= spikeball-index
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
                "Score: " | ToString | UI.Label
                score | ToString | UI.Label ;; UI.Label only accepts a string, so we have to convert our score value which is an int, into a string first.
              }
            )

            Do(spike-cannonball)

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

Now that we have our spiked cannonball system up, all we have to do to create more spiked cannonballs is to use `DoMany`, to schedule our wire.

=== "Code Added"
    
    ```shards
    [1 2]
    DoMany(spike-cannonball)
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
      LoadImage("GlodImages/Coin/Coin_1.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_2.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_3.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_4.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_5.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_6.png") >> coin-img-sequence
      LoadImage("GlodImages/Coin/Coin_7.png") >> coin-img-sequence

      ;; ------------- Spiked Canonball -------------
      LoadImage("GlodImages/SpikeBall/SpikeBall1.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall2.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall3.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall4.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall5.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall6.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall7.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall8.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall9.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall10.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall11.png") >> spikeball-sequence
      LoadImage("GlodImages/SpikeBall/SpikeBall12.png") >> spikeball-sequence
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
        0.3 >= coin-acceleration
        
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
          coin-img-sequence | Take(coin-image-index) | UI.Image(Scale: @f2(0.2 0.2))
      })
    } Looped: true)

    @wire(spike-cannonball {
      Once({
        1.0 >= spikeball-velocity
        -600.0 >= spikeball-y
        RandomFloat(Max: 1000.0) | Math.Subtract(500.0) >= spikeball-x ;; randomize our spikeball-x position at the start too

        0.5 >= spikeball-acceleration

        0 >= spikeball-index
        
        false >= score-subtracted
      })

      pos-x | Math.Add(50.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
      pos-x | Math.Subtract(50.0) = collision-x-lower-limit

      pos-y | Math.Add(50.0) = collision-y-upper-limit
      pos-y | Math.Subtract(50.0) = collision-y-lower-limit

      spikeball-y | Math.Add(spikeball-velocity) > spikeball-y
      spikeball-velocity | Math.Add(spikeball-acceleration) > spikeball-velocity

      When(
        Predicate: {
          spikeball-x | IsMoreEqual(collision-x-lower-limit)
          And
          spikeball-x | IsLessEqual(collision-x-upper-limit)
          And
          spikeball-y | IsMoreEqual(collision-y-lower-limit)
          And
          spikeball-y | IsLessEqual(collision-y-upper-limit)
          And
          score-subtracted | Is(false)
        }
        Action: {
          score | Math.Subtract(1) > score
          true > score-subtracted
        }
      )

      When(
        Predicate: {
          score-subtracted | Is(true)
          Or
          spikeball-y | IsMore(0.0)
        }
        Action: {
          -600.0 > spikeball-y ;; reset our spikeball's y position
          RandomFloat(Max: 1000.0) | Math.Subtract(500.0) > spikeball-x ;; randomize our spikeball-x position
          0.0 > spikeball-velocity
          false > score-subtracted ;; reset scored so that we can score again
        }
      )

      Step(animate-spikeball)

      UI.Area(
        Position: @f2(spikeball-x spikeball-y)
        Anchor: Anchor::Bottom ;; this makes the origin of our UI.Area the bottom right of the screen
        Contents: {
          spikeball-sequence | Take(spikeball-index) | UI.Image( Scale: @f2(0.10 0.10))
        }
      )
    } Looped: false)

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
      Count(coin-img-sequence) = index-max
      coin-image-index
      When(Predicate: IsMoreEqual(index-max) Action: {
        0 > coin-image-index
      })
      Pause(coin-animation-speed)
    } Looped: true)

    @wire(animate-spikeball {
      Once({
        0.06 >= spikeball-animation-speed
      })

      spikeball-index | Math.Add(1) > spikeball-index
      Count(spikeball-sequence) = spikeball-max
      spikeball-index
      When(Predicate: IsMoreEqual(spikeball-max) Action: {
        0 > spikeball-index
      })
      Pause(spikeball-animation-speed)
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
            false > grounded ;; male grounded false when up key is pressed
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

        0 >= spikeball-index
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
                "Score: " | ToString | UI.Label
                score | ToString | UI.Label ;; UI.Label only accepts a string, so we have to convert our score value which is an int, into a string first.
              }
            )

            [1 2]
            DoMany(spike-cannonball ComposeSync: true)

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

## Recap

Good job in reaching this far!

In this chapter, we created falling spiked cannonballs using the same logic as our coins.

In the next chapter, we will round off and finish the game by adding a few more elements that will be the icing on the cake for our game.

Almost there! 😀 

See you in the next chapter.
