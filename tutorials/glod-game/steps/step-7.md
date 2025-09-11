# Step 7

# Polishing up the Game

At this point in the tutorial, we already possess a fully functional game that we can have fun playing.

However, it could use some polishing to add more flair and make it more exciting... and that is what we will be doing in the final step of the tutorial.

We will be:

1. Adding a visual effect that happens when Glod collects a coin.

2. Adding a visual effect that happens when Glod is hit by a spiked cannonball.

3. Adding a background image.

4. Adding a timer and a Game Over screen.

5. Adding a reset function to play the game again.

So let's get to it! 💪🏼

## Visual Effect for Collecting

To make collecting more satisfying, we will add a visual feedback that happens whenever Glod collects a coin.

This will use the same logic and principles as when creating an animation.

- Download Scoring Effect Images [here](https://drive.google.com/drive/folders/1r3e-ITFRLB5D3Yg5qqbYS6NaKUyp85Fg?usp=share_link).

!!! note "Visual Feedback"
    *"Feedback is information relayed to the player in response to an in-game interaction."*
    <br>
    <span style="text-align: right"> - Liam Charlton , Game Design Psychology: Signs & Feedback </span>
    <br> <br>
    Here we use positive visual feedback to make it more satisfying whenever a player collects coins. The sense of satisfaction encourages the player to want to play more and continue playing.
    
    We also use negative visual feedback when a player collides with a spiked cannonball to discourage them from getting hit.
    
    Feedback can come in many forms - tactile, auditory, visual, and even as rewards!
    
    What are some feedback from your favorite games that you have found effective?

You should be familiar with creating animations by now!

1. create our sequence which holds each frame in our animation

2. create an index that will increase every frame

3. Use `UI.Area` and `UI.Image` to render our animation

4. Reset our index back to 0 whenever it goes beyond the index max

5. house our logic in a loopable wire.

=== "Code Added"
    
    ```shards
    LoadImage("GlodImages/FX/Score_Effect_1.png") >> score-fx-sequence ;; creating our animation sequence, added to our initialize-images
    LoadImage("GlodImages/FX/Score_Effect_2.png") >> score-fx-sequence
    LoadImage("GlodImages/FX/Score_Effect_3.png") >> score-fx-sequence
    LoadImage("GlodImages/FX/Score_Effect_4.png") >> score-fx-sequence
    LoadImage("GlodImages/FX/Score_Effect_5.png") >> score-fx-sequence
    LoadImage("GlodImages/FX/Score_Effect_6.png") >> score-fx-sequence
    LoadImage("GlodImages/FX/Score_Effect_7.png") >> score-fx-sequence
    LoadImage("GlodImages/FX/Score_Effect_8.png") >> score-fx-sequence

    @wire(animate-score-fx { ;; we will not be looping our animation because we only want it to run once
      Once({
        0.08 = idle-animation-speed
      })
      score-fx-idx | Math.Add(1) > score-fx-idx
      Pause(idle-animation-speed)
      ;; we do not loop our animation this time because we only want it to happen once
    } Looped: true)
    ```

This time, because we only want our score effect to happen only when we score, we will house our `UI.Area` in a separate looping wire and use `Spawn` to schedule the wire only when we need it to.

=== "Code Added"
    
  ```shards
  @wire(score-fx {
    Once({
      0 >= score-fx-idx ;; this variable can be initialized here as we are only using it in animate-score-fx and not main-wire
    })

    Step(animate-score-fx)

    UI.Area(
      Position: @f2(pos-x pos-y) ;; we want the effect to spawn at our Glod's position
      Anchor: Anchor::Bottom
      Contents: {
        score-fx-sequence | Take(score-fx-idx) | UI.Image(@f2(0.2 0.2))
      }
    )
  } Looped: true)
  ```

Then we `Spawn` `score-fx`, whenever our coin scoring logic happens.

=== "Code Added"
    
  ```shards
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
      Spawn(score-fx)
    }
  )
  ```

Lastly, because we don't want our `score-fx` to loop forever, so we `Stop` it after it does the animation once, which is when `score-fx-idx` reaches `index-max`.

=== "Code Added"
    ```shards
    @wire(score-fx {
      Once({
        0 >= score-fx-idx
      })

      Step(animate-score-fx)

      score-fx-idx
      When(
        Predicate: IsMoreEqual((Count(score-fx-sequence)))
        Action: {
          none ;; we need to ensure that in either scenarios, the output of the wire is the same. The none here ensures that
          Stop
        }
      )

      UI.Area(
        Position: @f2(pos-x pos-y) ;; we want the effect to spawn at our Glod's position
        Anchor: Anchor::Bottom
        Contents: {
          score-fx-sequence | Take(score-fx-idx) | UI.Image(@f2(0.2 0.2))
        }
      )

      none ;; we need to ensure that in either scenarios, the output of the wire is the same. The none here ensures that
    } Looped: true)
    ```

Now let's put it all together.

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

      ;; ------------- Spiked Cannonball -------------
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

      ;; -------------- score fx ----------------
      LoadImage("GlodImages/FX/Score_Effect_1.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_2.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_3.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_4.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_5.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_6.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_7.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_8.png") >> score-fx-sequence
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

      pos-x | Math.Add(60.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
      pos-x | Math.Subtract(60.0) = collision-x-lower-limit

      pos-y | Math.Add(70.0) = collision-y-upper-limit
      pos-y | Math.Subtract(70.0) = collision-y-lower-limit

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
          Spawn(score-fx)
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
          coin-img-sequence | Take(coin-image-index) | UI.Image(@f2(0.2 0.2))
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

    @wire(score-fx {
      Once({
        0 >= score-fx-idx
      })

      Step(animate-score-fx)

      score-fx-idx
      When(
        Predicate: IsMoreEqual((Count(score-fx-sequence)))
        Action: {
          none
          Stop
        }
      )

      UI.Area(
        Position: @f2(pos-x pos-y) ;; we want the effect to spawn at our Glod's position
        Anchor: Anchor::Bottom
        Contents: {
          score-fx-sequence | Take(score-fx-idx) | UI.Image(@f2(0.2 0.2))
        }
      )

      none
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

    @wire(animate-score-fx {
      Once({
        0.08 = idle-animation-speed
      })
      score-fx-idx | Math.Add(1) > score-fx-idx
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
                UI.Image(@f2(0.2))
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

Now, it should be more fun for you to collect coins! 💰

## Step 7.2

Now following the same logic, we will create feedback for when we are damaged.

- Download Damage Effect images [here](https://drive.google.com/drive/folders/1NYoURr7sryqWfbC0OMSf52CsbkHPPk25?usp=share_link).

1. create our sequence which holds each frame in our animation

2. create an index that will increase every frame

3. Use `UI.Area` and `UI.Image` to render our animation

4. Reset our index back to 0 whenever it goes beyond the index max

5. house our logic in a loopable wire.

6. `Detach` our wire and `Stop` it accordingly.

=== "Code Added"
    
    ```shards
    ;; --------------- Damaged Effect ----------------
    LoadImage("GlodImages/Damage_Effect/Damaged_Effect_1.png") >> damage-fx-sequence ;; initialize in initialize-images
    LoadImage("GlodImages/Damage_Effect/Damaged_Effect_2.png") >> damage-fx-sequence
    LoadImage("GlodImages/Damage_Effect/Damaged_Effect_3.png") >> damage-fx-sequence
    LoadImage("GlodImages/Damage_Effect/Damaged_Effect_4.png") >> damage-fx-sequence
    LoadImage("GlodImages/Damage_Effect/Damaged_Effect_5.png") >> damage-fx-sequence
    LoadImage("GlodImages/Damage_Effect/Damaged_Effect_6.png") >> damage-fx-sequence
    LoadImage("GlodImages/Damage_Effect/Damaged_Effect_7.png") >> damage-fx-sequence
    LoadImage("GlodImages/Damage_Effect/Damaged_Effect_8.png") >> damage-fx-sequence

    @wire(animate-damage-fx { ;; we will not be looping our animation because we only want it to run once
      Once({
        0.08 = idle-animation-speed
      })
      damage-fx-idx | Math.Add(1) > damage-fx-idx
      Pause(idle-animation-speed)
      ;; we do not loop our animation this time because we only want it to happen once
    } Looped: true)
    ```

This time, because we only want our damage effect to happen only when we are damaged, we will house our `UI.Area` in a separate looping wire and use `Detach` to schedule the wire only when we need it to. This time instead of using `Spawn`, we are using `Detach` instead. `Detach` only allows a single wire to be scheduled because it uses the original wire unlike `Spawn` which creates and schedules multiple copies of the specified wire. We don't want multiple flashes of the screen when we are damaged in quick succession and this only want a singular wire to be scheduled.

=== "Code Added"
    
  ```shards
  @wire(damage-fx {
    Once({
      0 >= damage-fx-idx ;; this variable can be initialized here as we are only using it in animate-damage-fx and not main-wire
    })

    Step(animate-damage-fx)

    UI.Area(
      Position: @f2(pos-x pos-y) ;; we want the effect to spawn at our Glod's position
      Anchor: Anchor::Center
      Contents: {
        damage-fx-sequence | Take(damage-fx-idx) | UI.Image(@f2(8.0 8.0)) ;; change the scale to fit your screen accordingly
      }
    )
  } Looped: true)
  ```

Then we `Detach` `damage-fx`, whenever our spikeball damage logic happens.

=== "Code Added"
    
  ```shards
  ;; coin-scoring logic
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
      Detach(damage-fx)
      true > score-subtracted
    }
  )
  ```

Lastly, because we don't want our `damage-fx` to loop forever, so we `Stop` it after it does the animation once, which is when `damage-fx-idx` reaches `index-max`.

=== "Code Added"
    ```shards
    @wire(damage-fx {
      Once({
        0 >= damage-fx-idx
      })

      Step(animate-damage-fx)

      damage-fx-idx
      When(
        Predicate: IsMoreEqual((Count(damage-fx-sequence)))
        Action: {
          none ;; we need to ensure that in either scenarios, the output of the wire is the same. The none here ensures that
          Stop
        }
      )

      UI.Area(
        Position: @f2(0 0) ;; Since the damage effect fills the whole screen, we want it to appear in the center
        Anchor: Anchor::Center
        Contents: {
          damage-fx-sequence | Take(damage-fx-idx) | UI.Image(@f2(0.2 0.2))
        }
      )

      none ;; we need to ensure that in either scenarios, the output of the wire is the same. The none here ensures that
    } Looped: true)
    ```

Now let's put it all together.

=== "Full Code"

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

      ;; ------------- Spiked Cannonball -------------
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

      ;; -------------- score fx ----------------
      LoadImage("GlodImages/FX/Score_Effect_1.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_2.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_3.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_4.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_5.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_6.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_7.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_8.png") >> score-fx-sequence

      ;; --------------- Damaged Effect ----------------
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_1.png") >> damage-fx-sequence 
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_2.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_3.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_4.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_5.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_6.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_7.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_8.png") >> damage-fx-sequence
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

      pos-x | Math.Add(60.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
      pos-x | Math.Subtract(60.0) = collision-x-lower-limit

      pos-y | Math.Add(70.0) = collision-y-upper-limit
      pos-y | Math.Subtract(70.0) = collision-y-lower-limit

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
          Spawn(score-fx)
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
          coin-img-sequence | Take(coin-image-index) | UI.Image(@f2(0.2 0.2))
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
          Detach(damage-fx)
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

    @wire(score-fx {
      Once({
        0 >= score-fx-idx
      })

      Step(animate-score-fx)

      score-fx-idx
      When(
        Predicate: IsMoreEqual((Count(score-fx-sequence)))
        Action: {
          none
          Stop
        }
      )

      UI.Area(
        Position: @f2(pos-x pos-y) ;; we want the effect to spawn at our Glod's position
        Anchor: Anchor::Bottom
        Contents: {
          score-fx-sequence | Take(score-fx-idx) | UI.Image(@f2(0.2 0.2))
        }
      )

      none
    } Looped: true)

    @wire(damage-fx {
      Once({
        0 >= damage-fx-idx
      })

      Step(animate-damage-fx)

      damage-fx-idx
      When(
        Predicate: IsMoreEqual((Count(damage-fx-sequence)))
        Action: {
          none
          Stop
        }
      )

      UI.Area(
        Position: @f2(0 0)
        Anchor: Anchor::Center
        Contents: {
          damage-fx-sequence | Take(damage-fx-idx) | UI.Image(@f2(8.0 8.0))
        }
      )

      none 
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

    @wire( animate-coin {
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

    @wire(animate-score-fx {
      Once({
        0.08 = idle-animation-speed
      })
      score-fx-idx | Math.Add(1) > score-fx-idx
      Pause(idle-animation-speed)
    } Looped: true)

    @wire(animate-damage-fx {
      Once({
        0.06 = idle-animation-speed
      })
      damage-fx-idx | Math.Add(1) > damage-fx-idx
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
                UI.Image(@f2(0.2))
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

## Step 7.3

This step will be extremely easy.

We will simply be adding a background image to our game to make it more visually appealing.

- Download background image [here](https://drive.google.com/file/d/1qP6JDdKfhwsc9guBkkIy9sjK02LMTGq3/view?usp=share_link).

First, load our image.

=== "Code Added"
    
    ```shards
    LoadImage("GlodImages/BG.png") = bg-image 
    ```

Next, create a `UI.Area` to house our new image.

Remember to add this `UI.Area` before the `UI.Area` which houses your character to ensure that it is drawn below your character.

=== "Code Added"
    
    ```shards
    UI.Area(
      Position: @f2(0.0 0.0)
      Anchor: Anchor::Center
      Contents: {
        bg-image | UI.Image(@f2(0.8 0.8)) ;; change this scale accordingly
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

      ;; ------------- Spiked Cannonball -------------
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

      ;; -------------- score fx ----------------
      LoadImage("GlodImages/FX/Score_Effect_1.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_2.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_3.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_4.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_5.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_6.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_7.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_8.png") >> score-fx-sequence

      ;; --------------- Damaged Effect ----------------
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_1.png") >> damage-fx-sequence 
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_2.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_3.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_4.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_5.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_6.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_7.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_8.png") >> damage-fx-sequence

      ;; -------------- BG ----------------
      LoadImage("GlodImages/BG.png") = bg-image 
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

      pos-x | Math.Add(60.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
      pos-x | Math.Subtract(60.0) = collision-x-lower-limit

      pos-y | Math.Add(70.0) = collision-y-upper-limit
      pos-y | Math.Subtract(70.0) = collision-y-lower-limit

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
          Spawn(score-fx)
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
          coin-img-sequence | Take(coin-image-index) | UI.Image(@f2(0.2 0.2))
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
          Detach(damage-fx)
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

    @wire(score-fx {
      Once({
        0 >= score-fx-idx
      })

      Step(animate-score-fx)

      score-fx-idx
      When(
        Predicate: IsMoreEqual((Count(score-fx-sequence)))
        Action: {
          none
          Stop
        }
      )

      UI.Area(
        Position: @f2(pos-x pos-y) ;; we want the effect to spawn at our Glod's position
        Anchor: Anchor::Bottom
        Contents: {
          score-fx-sequence | Take(score-fx-idx) | UI.Image(@f2(0.2 0.2))
        }
      )

      none
    } Looped: true)

    @wire(damage-fx {
      Once({
        0 >= damage-fx-idx
      })

      Step(animate-damage-fx)

      damage-fx-idx
      When(
        Predicate: IsMoreEqual((Count(damage-fx-sequence)))
        Action: {
          none
          Stop
        }
      )

      UI.Area(
        Position: @f2(0 0)
        Anchor: Anchor::Center
        Contents: {
          damage-fx-sequence | Take(damage-fx-idx) | UI.Image(@f2(8.0 8.0))
        }
      )

      none 
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

    @wire( animate-coin {
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

    @wire(animate-score-fx {
      Once({
        0.08 = idle-animation-speed
      })
      score-fx-idx | Math.Add(1) > score-fx-idx
      Pause(idle-animation-speed)
    } Looped: true)

    @wire(animate-damage-fx {
      Once({
        0.06 = idle-animation-speed
      })
      damage-fx-idx | Math.Add(1) > damage-fx-idx
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
              Position: @f2(0.0 0.0)
              Anchor: Anchor::Center
              Contents: {
                bg-image | UI.Image(@f2(0.8 0.8))
              }
            )
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
                UI.Image(@f2(0.2))
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

Now we have a background image! 🖼️

## Step 7.4

Let's make our game more challenging by adding a timer. ⏱️

Create a `timer` variable to keep track of the time and also a `game-over variable` to handle the game over state..

=== "Code Added"
    
    ```shards
    60 >= timer ;; add these to the Once where we initialize all our variables
    false >= game-over
    ```

Create a `UI.Area` to draw our timer variable. Place it on the top right.

=== "Code Added"

    ```shards
    UI.Area(
      Position: @f2(-40 40)
      Anchor: Anchor::TopRight
      Contents: {
        timer | ToString | UI.Label
      }
    )
    ```
Create a countdown logic.

Here, we subtract one from our `timer` variable every second and `Step` into the loop.

Set a conditional statement to ensure that this only happens while `game-over` is false and `timer` is greater than zero.

=== "Code Added"
    
    ```shards
    ;; -------- Timer -----------
    @wire(timer-countdown {
      timer
      If(Predicate: IsMore(0) Then: {
        timer | Math.Subtract(1) > timer
        Pause(1.0)
      })
    } Looped: true)
    ```

Create the Game Over logic to dictate that `game-over` becomes true when `timer` reaches zero.

=== "Code Added"
    
    ```shards
    ;; ---------- GameOver Logic ------------
    @wire(timer-countdown {
      timer
      If(Predicate: IsMore(0) Action: {
        timer | Math.Subtract(1) > timer
        Pause(1.0)
      } Else: {
        true > game-over
      })
    } Looped: true)
    ```

Remember to call and `Step` `timer-countdown` in the `main-wire`.

=== "Code Added"
    
    ```shards
    Step(timer-countdown)
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

      ;; ------------- Spiked Cannonball -------------
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

      ;; -------------- score fx ----------------
      LoadImage("GlodImages/FX/Score_Effect_1.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_2.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_3.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_4.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_5.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_6.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_7.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_8.png") >> score-fx-sequence

      ;; --------------- Damaged Effect ----------------
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_1.png") >> damage-fx-sequence 
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_2.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_3.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_4.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_5.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_6.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_7.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_8.png") >> damage-fx-sequence

      ;; -------------- BG ----------------
      LoadImage("GlodImages/BG.png") = bg-image 
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

      pos-x | Math.Add(60.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
      pos-x | Math.Subtract(60.0) = collision-x-lower-limit

      pos-y | Math.Add(70.0) = collision-y-upper-limit
      pos-y | Math.Subtract(70.0) = collision-y-lower-limit

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
          Spawn(score-fx)
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
          coin-img-sequence | Take(coin-image-index) | UI.Image(@f2(0.2 0.2))
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
          Detach(damage-fx)
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

    @wire(score-fx {
      Once({
        0 >= score-fx-idx
      })

      Step(animate-score-fx)

      score-fx-idx
      When(
        Predicate: IsMoreEqual((Count(score-fx-sequence)))
        Action: {
          none
          Stop
        }
      )

      UI.Area(
        Position: @f2(pos-x pos-y) ;; we want the effect to spawn at our Glod's position
        Anchor: Anchor::Bottom
        Contents: {
          score-fx-sequence | Take(score-fx-idx) | UI.Image(@f2(0.2 0.2))
        }
      )

      none
    } Looped: true)

    @wire(damage-fx {
      Once({
        0 >= damage-fx-idx
      })

      Step(animate-damage-fx)

      damage-fx-idx
      When(
        Predicate: IsMoreEqual((Count(damage-fx-sequence)))
        Action: {
          none
          Stop
        }
      )

      UI.Area(
        Position: @f2(0 0)
        Anchor: Anchor::Center
        Contents: {
          damage-fx-sequence | Take(damage-fx-idx) | UI.Image(@f2(8.0 8.0))
        }
      )

      none 
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

    @wire( animate-coin {
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

    @wire(animate-score-fx {
      Once({
        0.08 = idle-animation-speed
      })
      score-fx-idx | Math.Add(1) > score-fx-idx
      Pause(idle-animation-speed)
    } Looped: true)

    @wire(animate-damage-fx {
      Once({
        0.06 = idle-animation-speed
      })
      damage-fx-idx | Math.Add(1) > damage-fx-idx
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

    @wire(timer-countdown {
      timer
      If(Predicate: IsMore(0) Then: {
        timer | Math.Subtract(1) > timer
        Pause(1.0)
      } Else: {
        true > game-over
      })
    } Looped: true)

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

        60 >= timer
        false >= game-over
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
              Position: @f2(0.0 0.0)
              Anchor: Anchor::Center
              Contents: {
                bg-image | UI.Image(@f2(0.8 0.8))
              }
            )
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
                UI.Image(@f2(0.2))
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

            UI.Area(
              Position: @f2(-40 40)
              Anchor: Anchor::TopRight
              Contents: {
                @ui-style
                "Time: " | UI.Label
                timer | ToString | UI.Label
              }
            )

          }) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          Step(animate)
          Step(timer-countdown)
          
          GFX.Render(Steps: render-steps)
        }
      )
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

## Step  7.5

Finally its time to code the final bit for our game. We need to add some finality to our game by having it end when our Timer reaches 0. Then we also need to create a way to reset the game so that we can play it again. While this might seem daunting, don't worry! It's much easier than you might think.

First, let's create a window that appears when our `game-over` is true. Instead of using `UI.Area`, lets use `UI.Window` instead and let's display our current score.

=== "Code Added"
    
    ```shards
    ;; -------- Game_Over_UI -------------
    UI.Window(
      Position: @f2(0 0)
      Anchor: Anchor::Center
      Flags: [WindowFlags::NoResize WindowFlags::NoTitleBar WindowFlags::NoCollapse]
      Contents: {

        @ui-style
        "Game Over!" | UI.Label

        score | ToString | UI.Label
      }
    )
    ```

Then let's add a button to reset our `game-over` state back to false and reset our `timer` back to 60 and other variables.

=== "Code Added"

    ```shards
    UI.Window(
      Position: @f2(0 0)
      Anchor: Anchor::Center
      Flags: [WindowFlags::NoResize WindowFlags::NoTitleBar WindowFlags::NoCollapse]
      Contents: {

        @ui-style
        "Game Over!" | UI.Label

        score | ToString | UI.Label

        UI.Button( Label:"Restart ?" Action: {
          false > game-over
          60 > timer
          0 > score
          0.0 > pos-x
          0.0 > pos-y
          0.0 > character-y-velocity
          true > grounded
        })
      }
    )
    ```

Lastly, let's add a conditional statement in our `main-wire` to show the game over window when `game-over` is true and spawn the coins and spiked cannonballs when false.

=== "Code Added"
    ``` shards
    game-over
    If(Predicate: Is(true) Then: {
      UI.Window(
        Position: @f2(0 0)
        Anchor: Anchor::Center
        Flags: [WindowFlags::NoResize WindowFlags::NoTitleBar WindowFlags::NoCollapse]
        Contents: {

          @ui-style
          "Game Over!" | UI.Label

          score | ToString | UI.Label

          UI.Button( Label:"Restart ?" Action: {
            false > game-over
          })
        }
      )
    } Else: {
      Do(spike-cannonball)

      [1 2 3]
      DoMany(coin ComposeSync: true)
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

      ;; ------------- Spiked Cannonball -------------
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

      ;; -------------- score fx ----------------
      LoadImage("GlodImages/FX/Score_Effect_1.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_2.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_3.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_4.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_5.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_6.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_7.png") >> score-fx-sequence
      LoadImage("GlodImages/FX/Score_Effect_8.png") >> score-fx-sequence

      ;; --------------- Damaged Effect ----------------
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_1.png") >> damage-fx-sequence 
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_2.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_3.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_4.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_5.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_6.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_7.png") >> damage-fx-sequence
      LoadImage("GlodImages/Damage_Effect/Damaged_Effect_8.png") >> damage-fx-sequence

      ;; -------------- BG ----------------
      LoadImage("GlodImages/BG.png") = bg-image 
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

      pos-x | Math.Add(60.0) = collision-x-upper-limit ;; tweak the values added or subtracted accordingly
      pos-x | Math.Subtract(60.0) = collision-x-lower-limit

      pos-y | Math.Add(70.0) = collision-y-upper-limit
      pos-y | Math.Subtract(70.0) = collision-y-lower-limit

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
          Spawn(score-fx)
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
          coin-img-sequence | Take(coin-image-index) | UI.Image(@f2(0.2 0.2))
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
          Detach(damage-fx)
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

    @wire(score-fx {
      Once({
        0 >= score-fx-idx
      })

      Step(animate-score-fx)

      score-fx-idx
      When(
        Predicate: IsMoreEqual((Count(score-fx-sequence)))
        Action: {
          none
          Stop
        }
      )

      UI.Area(
        Position: @f2(pos-x pos-y) ;; we want the effect to spawn at our Glod's position
        Anchor: Anchor::Bottom
        Contents: {
          score-fx-sequence | Take(score-fx-idx) | UI.Image(@f2(0.2 0.2))
        }
      )

      none
    } Looped: true)

    @wire(damage-fx {
      Once({
        0 >= damage-fx-idx
      })

      Step(animate-damage-fx)

      damage-fx-idx
      When(
        Predicate: IsMoreEqual((Count(damage-fx-sequence)))
        Action: {
          none
          Stop
        }
      )

      UI.Area(
        Position: @f2(0 0)
        Anchor: Anchor::Center
        Contents: {
          damage-fx-sequence | Take(damage-fx-idx) | UI.Image(@f2(8.0 8.0))
        }
      )

      none 
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

    @wire( animate-coin {
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

    @wire(animate-score-fx {
      Once({
        0.08 = idle-animation-speed
      })
      score-fx-idx | Math.Add(1) > score-fx-idx
      Pause(idle-animation-speed)
    } Looped: true)

    @wire(animate-damage-fx {
      Once({
        0.06 = idle-animation-speed
      })
      damage-fx-idx | Math.Add(1) > damage-fx-idx
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

    @wire(timer-countdown {
      timer
      If(Predicate: IsMore(0) Then: {
        timer | Math.Subtract(1) > timer
        Pause(1.0)
      } Else: {
        true > game-over
      })
    } Looped: true)

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

        60 >= timer
        false >= game-over
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
              Position: @f2(0.0 0.0)
              Anchor: Anchor::Center
              Contents: {
                bg-image | UI.Image(@f2(0.8 0.8))
              }
            )
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
                UI.Image(@f2(0.2))
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

            game-over
            If(Predicate: Is(true) Then: {
              UI.Window(
                Position: @f2(0 0)
                Anchor: Anchor::Center
                Flags: [WindowFlags::NoResize WindowFlags::NoTitleBar WindowFlags::NoCollapse]
                Contents: {

                  @ui-style
                  "Game Over!" | UI.Label

                  score | ToString | UI.Label

                  UI.Button( Label:"Restart ?" Action: {
                    false > game-over
                    60 > timer

                    0 > score
                    0.0 > pos-x
                    0.0 > pos-y
                    0.0 > character-y-velocity
                    true > grounded
                  })
                }
              )
            } Else: {
              Do(spike-cannonball)

              [1 2 3]
              DoMany(coin ComposeSync: true)
            })

            UI.Area(
              Position: @f2(-40 40)
              Anchor: Anchor::TopRight
              Contents: {
                @ui-style
                "Time: " | UI.Label
                timer | ToString | UI.Label
              }
            )

          }) | UI.Render(ui-draw-queue)

          @button-inputs
          @i2(x-direction y-direction) > character-direction
          Step(animate)
          Step(timer-countdown)
          
          GFX.Render(Steps: render-steps)
        }
      )
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

CONGRATULATIONS! 🎉🎉🎉🎉🎉🎉

You have successfully created a game!

Give it a whirl and see how many points you can get!
