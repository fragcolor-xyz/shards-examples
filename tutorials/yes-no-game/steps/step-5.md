# Step 5

For our game to engage and interact with users, we need it to be responsive to user input. 

In this chapter, we will be looking at how user input is handled in Shards.

## User Input

User input is managed within the `GFX.MainWindow` of your game. For this game, the only user input required would be the ↑ and ↓ directional key.

We can use `Inputs.KeyDown` to execute code whenever the user presses down on a specified key.

=== "Code"
  
    ```shards
    @wire( ui-loop {
      GFX.MainWindow(
        Title: "Yes-No Game"
        Width: 1280 Height: 768
        Contents: {
        ...
        Inputs.KeyDown(  ;; (3)
          Key: "up"
          Action: {Pass}  ;; (1)
        )

        Inputs.KeyDown(
          Inputs.KeyDown(
            Key: "down"
            Action: {Pass} ;; (2)
          )
        )
    ```

    1. This code is executed when the user presses the ↑ directional key.
    2. This code is executed when the user presses the ↓ directional key.
    3. [`Inputs.KeyDown`](https://docs.fragcolor.com/reference/shards/shards/Inputs/KeyDown/) will run the code in its `Action` parameter when the `Key` specified is pressed down by the user.

Now that we are able to obtain the user's input, we can proceed to check if the user pressed the correct button. 

## Checking the Input

Based on the images chosen, we can determine whether the user needs to select the ↑ or ↓ directional key. 

In `initialize-variables`, create a variable named `same-image`. This will be used to check if the user pressed the correct key later.

=== "Code"
  
    ```shards
    @define(initialize-variables {
      ...

      ;; Other Shared Variables
      0 >= left-image-index
      0 >= right-image-index
      Count(images) >= total-images
      true >= same-image ;; (1) 
    })
    ```

    1. Tracks whether the same image is used.


Navigate to where we chose the images in `initialize-round`. 

Check if the chosen images are the same, and assign `true` or `false` to `same-image` accordingly.

=== "Code"

    ```shards
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
  
    ```

    1. [`If`](https://docs.fragcolor.com/reference/shards/shards/General/If/) checks the `Predicate` given and runs the code within `Then` if it is true. If false, the code within `Else` is run instead.
    2. Check if the images on the left and right are the same or different.

Next, in Input.KeyDown, check if they:

- Pressed ↑ when `same-image` is true

- Pressed ↓ when `same-image` is false

We award them a point by increasing the value of `total-score`, as long as `game-over` is still false. We also want to call `end-round` with `Step` whenever the user provides the correct input to go to the next round and refresh the images.

=== "Code"
  
    ```shards
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
    ```

Cheers! Your game can now receive user input, tabulate the score, and allows players to play up to 10 rounds each time.


=== "Full Code"
  
    ```shards
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
      new-round
      Branch([ui-loop logic-loop])
    } Looped: true)

    @mesh(main)
    @schedule(main game-loop)
    @run(main FPS: 60) 
    ```

=== "Result"    
    ![User input, score tabulation, and multiple rounds.](assets/step-5-result.png)


--8<-- "includes/license.md"
