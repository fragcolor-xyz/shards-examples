# Step 4

Our game wouldn't be much fun if it ended in one round. To improve its playability, we implement multiple rounds.

To implement multiple rounds, we will have to:

1. Add variables to track game states

2. Implement shards to reset these variables


## Adding Multiple Rounds
For our game to be able to handle multiple rounds and an eventual "Game Over", we will need to manage game states.

Let us define some variables to help keep track of the game states. 
Navigate to `initialize-variables` and add in the following variables:

- `game-over` 

Organize your variables by grouping them according to when they will be reset. For example, some variables will be reset at the start of each round, while others are only reset when a new game is started.

=== "Code"
  
    ```shards
    @define( initialize-variables {
      ;; Variables to reset each round
      true >= new-round ;; (1)
      @max-timer >= time-remaining

      ;; Variables to reset each game 
      0 >= total-score
      1 >= current-round
      false >= game-over ;; (2)

      
      ;; Other Shared Variables
      0 >= left-image-index
      0 >= right-image-index
      Count(images) >= total-images 
    })
    ```

    1. Variable that tracks if a new round should be started.
    2. Variable that tracks if the game is over.

Next, add code in the `logic-loop` to check if there should be a new round before executing `initialize-round`.

Remove the `Once` shard that was used to prevent the initialization code from running every Loop's iteration. We will be using conditionals instead.

??? "Conditionals"
    Conditionals check if a given statement is true or false, and executes different code depending on the result. In the following code, the conditionals used is [`When`](https://docs.fragcolor.com/reference/shards/shards/General/When/).

=== "Before"
  
    ```shards
    @wire(logic-loop {
      Once({
        @initialize-round
      })
    } Looped: true)
    ```

=== "After"
  
    ```shards
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
    ```

    1. If the game is not in a Game Over state...
    2. ... and it is a new round,
    3. ... initialize a new round.
    4. [`WhenNot`](https://docs.fragcolor.com/reference/shards/shards/General/WhenNot/) will only execute the `Action` specified when the `Predicate` is false. 
    5. [`When`](https://docs.fragcolor.com/reference/shards/shards/General/When/) will only execute the `Action` specified when the `Predicate` is true. Unlike `If`, it does not have an `Else` parameter.    

## Handling Game States

At the start of each round, we want to ensure that `new-round` and `time-remaining` are reset to their original values. Create the definition `reset-round-variables` to carry out this task.

=== "Code"
  
    ```shards
    @define(reset-round-variables {
      false > new-round
      @max-timer > time-remaining
    }) 
    ```

Do not forget to add `reset-round-variables` to `initialize-round`! We need the variables to reset whenever a new round begins.

=== "Code"

    ```shards
    @define( initialize-round {
      RandomInt(Max: total-images) > left-image-index
      RandomInt(Max: total-images) > right-image-index

      @reset-round-variables
    })
    ```

When the game ends and a new one begins, we will reset the following values:

- Variables that are reset at the start of each round

- Total score

- Round counter

- The Game Over state tracker

Create the `reset-game-variables` definition to implement this.

=== "Code"

    ```shards
    @define(reset-game-variables {
      @reset-round-variables
      0 > total-score
      1 > current-round
      false > game-over
      true > new-round
    })
    ```

You now have variables to track the game state and shards to reset these variables.

We want to reset these values at the end of each round or game.

## Ending a Round

Let us now create a Looped Wire named `end-round` to handle the end of each round.
      
The Loop will first check if the current round is final. If it is, it sets the `game-over` variable to true. Otherwise, it sets `new-round` to true and increments the current round number.
      
??? "Why a Looped Wire?"
    When a Wire ends, any changes made within it are lost. We use a Looped Wire when we want changes within the Wire to persist. 

    For example, in `end-round`, we use `Once` to initialize the variable `new-round-number` once. With a Looped Wire, the variable continues to exist as long as the Looped Wire remains running. If we use a Wire, every time `.end-round` is called, `new-round-number` will have to be initialized again.

    You might wonder - how do we exit the Loop after calling it then? Won't the program be stuck in the Loop indefinitely? The solution to this is in the shard called `Step` which we will introduce in the following chapter. If you wish to read more about `Step` and Looped Wires first, do check out our primer [here](https://docs.fragcolor.com/learn/shards/primer/the-flow).

=== "Code"
  
    ```shards
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
    })

    ```

    1. Variable to hold the next round's number.
    2. Increment the current round by one to get the next round's number.
    3. Check if the next round's number exceeds the max number of rounds.
    4. If it has exceeded, set the game-over variable to true.
    5. Otherwise set the new round number...
    6. ... and set the new-round variable to true.

=== "Full Code so far"

    ```shards
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
      Branch(Wires: [ui-loop logic-loop])
    } Looped: true)

    @mesh(main)
    @schedule(main game-loop)
    @run(main FPS: 60) 
    ```


To end a round, the user has to give an answer or wait for the timer to expire. We will learn to work with user input in the next chapter.



--8<-- "includes/license.md"
