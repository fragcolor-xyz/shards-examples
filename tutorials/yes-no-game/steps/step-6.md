# Step 6

Let us round up this tutorial by adding in the UI for the Game Over state and adding a Timer for extra challenge.

We will be using a `UI.Button` to help us handle the user input for restarting the game.

## Game Over

=== "Code"
  
    ```shards
    @define(game-over-ui {
      UI.CentralPanel(
        Contents: {
          UI.Area(
            Position: @f2(0.0 0.0)
            Anchor: Anchor::Center
            Contents: {
              "GAME OVER" | UI.Label

              UI.Horizontal(
                Contents: {
                "Final Score: " | UI.Label
                total-score | ToString | UI.Label
                "/" | UI.Label
                @total-rounds | ToString | UI.Label
              })

              UI.Button(
                Label: "Play Again"
                Action: {
                  @reset-game-variables
                }
              )
            }
          )
        }
      )
    })
    ```

    1. [`UI.Button`](https://docs.fragcolor.com/reference/shards/shards/UI/Button/) is a UI element that appears as a clickable button with the text passed into its `Label` parameter displayed on it.

When the button is pressed, we reset the game's variables, including `game-over` which will be used to decide what is drawn in our game's window.

Navigate to `ui-loop` and add in `game-over` conditionals.

=== "Before"
  
    ```shards
    UI.CentralPanel(
      Contents: {
      UI.Horizontal(
        Contents: {
          UI.Area(
            Position: @f2(-250.0 0.0)
            Anchor: Anchor::Center
            Contents: {
            images | Take(left-image-index) | UI.Image
            })

            UI.Area(
            Position: @f2(250.0 0.0)
            Anchor: Anchor::Center
            Contents: {
            images | Take(right-image-index) | UI.Image
          })
      })
    })
    ```
=== "After"
  
    ```shards
    game-over
    If(Predicate: Is(false) Then: {
        UI.CentralPanel(
          Contents: {
          UI.Horizontal(
            Contents: {
              UI.Area(
                Position: @f2(-250.0 0.0)
                Anchor: Anchor::Center
                Contents: {
                images | Take(left-image-index) | UI.Image
                })

                UI.Area(
                Position: @f2(250.0 0.0)
                Anchor: Anchor::Center
                Contents: {
                images | Take(right-image-index) | UI.Image
              })
          })
        })
    } Else: {
      @game-over-ui
    })
    ```

    1. Draw the Game Over UI if game-over is true.
    2. Draw the Main Game UI if game-over is false.

The game will now display the main game area or a game over screen depending on whether `game-over` is true.

## Implementing a Timer
To add additional challenge to the game (for additional fun!), we can impose a time limit on each round.

Let us write `@define` that decreases the `time-remaining` every time it is called. 
=== "Code"

    ```shards
    @define(time-tick {
      game-over
      When(Predicate: Is(false) Action: {
        time-remaining | Math.Subtract(1) > time-remaining
        When(Predicate: IsLessEqual(0) Action: {
          Step(end-round)
        })
      })
    })
    ```
    
    1. When the game is still running...
    2. ... decrease the time remaining by 1.
    3. The round is forced to end if the time remaining falls below 0.

We can have `timer-tick` called every second consistently by using the `Once` shard. 

Place it within the `logic-loop` where we are checking if a new round should be started.

=== "Code"

    ```shards
    @wire(logic-loop {
      Once({
        @time-tick
      } Every: 1.0)
      game-over
      When(Predicate: {
        Is(false) ;; (4)
        And
        new-round | Is(true) ;; (1)
      } Action: {
          @initialize-round ;; (3)
      })
    } Looped: true)
    ```

    1. Runs the timer-tick shard every 1 second.
    2. [`Once`](https://docs.fragcolor.com/reference/shards/shards/General/Once/) will run the code in its `Action` parameter every time the duration specified in its `Every` parameter has passed.

=== "Full Code"

    ```shards
    @define(time-tick {
      game-over
      When(Predicate: Is(false) Action: {
        time-remaining | Math.Subtract(1) > time-remaining
        When(Predicate: IsLessEqual(0) Action: {
          Step(end-round)
        })
      })
    })

    @define(game-over-ui {
      UI.CentralPanel(
        Contents: {
          UI.Area(
            Position: @f2(0.0 0.0)
            Anchor: Anchor::Center
            Contents: {
              "GAME OVER" | UI.Label

              UI.Horizontal(
                Contents: {
                "Final Score: " | UI.Label
                total-score | ToString | UI.Label
                "/" | UI.Label
                @total-rounds | ToString | UI.Label
              })

              UI.Button(
                Label: "Play Again"
                Action: {
                  @reset-game-variables
                }
              )
            }
          )
        }
      )
    })

    @define(reset-game-variables {
        @reset-round-variables
        0 > total-score
        1 > current-round
        false > game-over
        true > new-round
      })

    @wire(end-round {
      Once({
        0 >= new-round-number ;; (1)
      })

      current-round | Math.Add(1) > new-round-number ;; (2)

      new-round-number
      If(Predicate: IsMore(@total-rounds) Then: { ;; (3)
        true > game-over ;; (4)
      } Else: {
        new-round-number > current-round ;; (5)
        true > new-round ;; (6)
      })
    } Looped: true)

    @define(reset-round-variables {
      false > new-round
      @max-timer > time-remaining
    }) 

    @define( initialize-round {
      RandomInt(Max: total-images) > left-image-index
      RandomInt(Max: total-images) > right-image-index

      left-image-index
      If(Predicate: {  ;; (1)
        Is(right-image-index) ;; (2)
      } Then: {
        true > same-image
      } Else: {
        false > same-image
      })

      @reset-round-variables
    })

    @define(total-rounds 10)
    @define(max-timer 5)

    @define(load-resources {
      LoadImage("data/cats/cat01.png") | Push(Name: images) ;; (1)(2)
      LoadImage("data/cats/cat02.png") | Push(Name: images)
      LoadImage("data/cats/cat03.png") | Push(Name: images)
    })

    @define( initialize-variables {
      ;; Variables to reset each round
      true >= new-round
      @max-timer >= time-remaining

      ;; Variables to reset each game 
      0 >= total-score
      1 >= current-round
      false >= game-over

      
      ;; Other Shared Variables
      0 >= left-image-index
      0 >= right-image-index
      Count(images) >= total-images
      true >= same-image
    })

    @define( main-game-ui {
      UI.BottomPanel(
        Contents: { "Are they the same image? Press the UP arrow if YES, and the DOWN arrow if NO." | UI.Label
      })

      UI.TopPanel(
        Contents: {
        UI.Horizontal(
        Contents: {
          "Score: " | UI.Label
          total-score | ToString | UI.Label
          UI.Separator
          "Round: " | UI.Label
          current-round | ToString | UI.Label
          UI.Separator
          "Time Left: " | UI.Label
          time-remaining | ToString | UI.Label
          })
      })

      game-over
      If(Predicate: Is(false) Then: {
          UI.CentralPanel(
            Contents: {
            UI.Horizontal(
              Contents: {
                UI.Area(
                  Position: @f2(-250.0 0.0)
                  Anchor: Anchor::Center
                  Contents: {
                  images | Take(left-image-index) | UI.Image
                  })

                  UI.Area(
                  Position: @f2(250.0 0.0)
                  Anchor: Anchor::Center
                  Contents: {
                  images | Take(right-image-index) | UI.Image
                })
            })
          })
      } Else: {
        @game-over-ui
      })
    })

    @wire( ui-loop {
      GFX.MainWindow(
        Title: "Yes-No Game"
        Width: 1280 Height: 768
        Contents: {
          Once({
            GFX.DrawQueue >= ui-draw-queue
            GFX.UIPass(ui-draw-queue) >> render-steps
          })

          ui-draw-queue | GFX.ClearQueue

          Inputs.KeyDown( 
            Key: "up"
            Action: {
              same-image
              When(Predicate: {
                Is(true)
                And
                game-over | Is(false)
              } Action: {
                total-score | Math.Add(1) > total-score
                Step(end-round)
              })
            }
          )

          Inputs.KeyDown(
            Key: "down"
            Action: {
              same-image
              When(Predicate: {
                Is(false)
                And
                game-over | Is(false)
                } Action: {
                total-score | Math.Add(1) > total-score
                Step(end-round)
              })
            }
          )
            
          UI(Contents: {
            @main-game-ui
          }) | UI.Render(ui-draw-queue)
          GFX.Render(Steps: render-steps)
        })
    } Looped: true)

    @wire(logic-loop {
      Once({
        @time-tick
      } Every: 1.0)

      game-over
      When(Predicate: {
        Is(false) ;; (4)
        And
        new-round | Is(true) ;; (1)
      } Action: {
          @initialize-round ;; (3)
      })
    } Looped: true)

    @wire( game-loop {
      Once({ 
        @load-resources 
        @initialize-variables
      })
      Branch(Wires: [ui-loop logic-loop])
    } Looped: true)

    @mesh(main)
    @schedule(main game-loop)
    @run(main FPS: 60) 
    ```

!!! note
    You can adjust the difficulty by changing the value of the `max-timer` constant we defined at the start.

## Outcome

Congratulations on making it to the end!

Your game now has 10 rounds, calculates the total score, shows a Game Over screen and lets you play it again at the end. It even has a timer to add a time constraint to each round.

![Round number and the score is calculated.](assets/step-6-result-1.png)

![A Game Over screen is shown at the end.](assets/step-6-result-2.png)

Now that the tutorial is over, why not challenge yourself further by implementing a high-score system? You could also try adding support for a second player! 

The possibilities are endless with the power of Shards in your hands.

The full game script can be found [here](../full-game/index.md).

--8<-- "includes/license.md"
