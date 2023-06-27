# Step 2

In this chapter, we will be adding graphics to your game. You will be able to create a game window and start seeing your game come to life.

## The UI Template

To start drawing the UI in shards, we will have to ready the GFX window which serves as the base for our UI operations. It follows a fixed template that should be placed within our `ui-loop`.

=== "UI Template"
    
    ```clojure linenums="1"
    (defshards main-game-ui [] nil)     ;; Add your UI shards here

    (defloop ui-loop
      (GFX.MainWindow
       :Title "Yes-No Game"
       :Width 1280 :Height 768
       :Contents
       (->
        (Setup
         (GFX.DrawQueue) >= .ui-draw-queue
         (GFX.UIPass .ui-draw-queue) >> .render-steps)
        (| .ui-draw-queue (GFX.ClearQueue))
        (UI .ui-draw-queue (main-game-ui))
        (GFX.Render :Steps .render-steps))))
    ```

??? "What does the GFX code mean?"
    How the GFX window works is beyond the scope of this tutorial. The rough idea is that we are giving instructions to the computer on what to draw to the screen. For more information, check out the documentation on the [UI](https://docs.fragnova.com/reference/shards/shards/UI/) class.

## Planning the UI

Before writing code for the UI, it is good to have a design in mind. For this tutorial, we have prepared a simple design to follow:

![A design plan for the game's UI.](assets/ui-plan.png)

With a design plan, it will be easier to identify the elements that will make up your UI. You can then implement the appropriate shards to draw them.

![Planning the UI shards.](assets/ui-plan-2.png)

## The Bottom Panel

Let us start with the simplest panel, the Bottom Panel with a single Label. 

We will pass a string of instructions into the [`UI.Label`](https://docs.fragnova.com/reference/shards/shards/UI/Label/) shard, which we then pass into the Bottom Panel shard as its content.

=== "Code"
    
    ```{.clojure .annotate linenums="1"}
    (UI.BottomPanel ;; (1)
     :Contents
     (-> "Are they the same image? Press the UP arrow if YES, and the DOWN arrow if NO."
     (UI.Label) ;; (2)
     ))
    ```

    1. [`UI.BottomPanel`](https://docs.fragnova.com/reference/shards/shards/UI/BottomPanel/) is a UI element to hold other UI elements within it. It starts from the bottom of the Window.
    2. [`UI.Label`](https://docs.fragnova.com/reference/shards/shards/UI/Label/) is a UI element to hold text.

## The Top Panel

The Top Panel consists of a sequence of Labels and Separators in a Horizontal Group so that they are naturally aligned from left to right.

=== "Code"
    
    ```{.clojure .annotate linenums="1"}
    (UI.TopPanel ;; (1)
     :Contents
     (->
      (UI.Horizontal ;; (2)
       :Contents
        (->
         "Score: 0" (UI.Label)
         (UI.Separator) ;; (3)
         "Round: 1" (UI.Label)
         (UI.Separator)
         "Time Left: 5" (UI.Label)))))
    ```

    1. [`UI.TopPanel`](https://docs.fragnova.com/reference/shards/shards/UI/TopPanel/) is a UI element to hold other UI elements within it. It starts from the top of the Window.
    2. [`UI.Horizontal`](https://docs.fragnova.com/reference/shards/shards/UI/Horizontal/) is a UI element to hold other UI elements within it. It aligns its elements horizontally, from left to right.
    3. [`UI.Separator`](https://docs.fragnova.com/reference/shards/shards/UI/Separator/) is a UI element that appears as a horizontal line within a vertical layout, and appears as a vertical line within a horizontal layout.

We want to be able to change the values used for Score, the Round Number, and Time Left (they can't remain static after all!).

This is where variables come in. We will be defining our variables in the `initialize-variables` shard created [earlier](../step-1/#the-setup-zone).

Define the following variables: 

- `.total-score`

- `.current-round`

- `.time-remaining`

Some values will remain the same throughout the game - these are known as *constants*. 

Define the following constants:

- `total-rounds`

- `max-timer`

??? "Declaring Constants"
    Constants are values that cannot be changed. 
    
    In Shards, you can declare named constants by using the macro [`def`](https://docs.fragnova.com/reference/shards/lisp/macros/#def), which is used to give an alias to a value. Unnamed constants can be declared with [`Const`](https://docs.fragnova.com/reference/shards/shards/General/Const/).

=== "Code"
    
    ```{.clojure .annotate linenums="1"}

    (def total-rounds 10)
    (def max-timer 5)

    (defshards initialize-variables []
      0 >= .total-score
      1 >= .current-round
      max-timer >= .time-remaining)
    ```

We can now replace the fixed string numbers in our UI code with variables that will be updated as their values change.

=== "Code"

    ```{.clojure .annotate linenums="1"}
    (UI.TopPanel
     :Contents
     (->
      (UI.Horizontal
       :Contents
        (->
         "Score: " (UI.Label)
         .total-score (ToString) (UI.Label) ;; (1)
         (UI.Separator)
         "Round: " (UI.Label)
         .current-round (ToString) (UI.Label)
         (UI.Separator)
         "Time Left: " (UI.Label)
         .time-remaining (ToString) (UI.Label)))))
    ```

    1. [`ToString`](https://docs.fragnova.com/reference/shards/shards/General/ToString/) converts values into strings. In the code above, `UI.Label` expects a string input, which is why we have to convert the integer variables into strings.

## The Central Panel

The central panel shows two images side by side. To draw the images onto the screen, we have to load them into our game's resources. We can do so in the `load-resources` shard created [earlier](../step-1/#the-setup-zone). 

Create a folder named "data" at the directory where your game's script is located - we will be placing our images here.

!!! note
    For this tutorial, we will be using 3 images of cats. You are free to choose the images of your own accord though!

    Our images have been standardized to have a length of 400 pixels for horizontal images. You can use an image editing tool to resize your images for consistency.

Use the `Push` shard to push the images into a sequence.

=== "Code"
    
    ```{.clojure .annotate linenums="1"}
    (defshards load-resources []
      (LoadImage "data/cats/cat01.png") (Push :Name .images) ;; (1)(2)
      (LoadImage "data/cats/cat02.png") (Push :Name .images)
      (LoadImage "data/cats/cat03.png") (Push :Name .images))
    ```

    1. [`LoadImage`](https://docs.fragnova.com/reference/shards/shards/General/LoadImage/) is used to load images into your game's resources.
    2. [`Push`](https://docs.fragnova.com/reference/shards/shards/General/Push/) adds elements to the back of a sequence. It will create the sequence if it does not exist.

We will randomize the images shown when tackling the logic of the game later. For now, let us display the first two images in the sequence as a placeholder.

To better control where the images are drawn, we place each image in a `UI.Area` and specify its position.

=== "Code"
    
    ```{.clojure .annotate linenums="1"}
    (UI.CentralPanel
        :Contents
        (->
         (UI.Horizontal
          :Contents
          (->
            (UI.Area ;; (1)
             :Position (float2 -250.0, 0.0) ;; (2)
             :Anchor Anchor.Center
             :Contents
             (-> .images (Take 0) (UI.Image))) ;; (3)
            (UI.Area
             :Position (float2 250.0, 0.0) ;; (4) 
             :Anchor Anchor.Center
             :Contents
             (-> .images (Take 1) (UI.Image)))))))
    ```

    1. [`UI.Area`](https://docs.fragnova.com/reference/shards/shards/UI/Area/) is a UI element that is used to place its contents at a specific position.
    2. Minus 250 pixels from the center on the x-axis
    3. [`Take`](https://docs.fragnova.com/reference/shards/shards/General/Take/) is used to retrieve the element stored at a specified index of a sequence.
    4. Add 250 pixels from the center on the x-axis

!!! caution
    When using panels, ensure that `UI.CentralPanel` is always the last of the panels to be drawn to prevent errors.

## Outcome

The game's base UI is now ready! Try running the code to see your results.

In the next chapter, we will delve into the game's logic and allow your program to ready itself for each game round.

The code thus far:
=== "Full Code"
    
    ```{.clojure .annotate linenums="1"}
    (def total-rounds 10)
    (def max-timer 5)

    (defshards load-resources []
      (LoadImage "data/cats/cat01.png") (Push :Name .images)
      (LoadImage "data/cats/cat02.png") (Push :Name .images)
      (LoadImage "data/cats/cat03.png") (Push :Name .images))

    (defshards initialize-variables []
      0 >= .total-score
      1 >= .current-round
      max-timer >= .time-remaining)

    (defshards main-game-ui []
      (UI.BottomPanel
       :Contents (-> "Are they the same image? Press the UP arrow if YES, and the DOWN arrow if NO." (UI.Label)))

      (UI.TopPanel
       :Contents
       (->
        (UI.Horizontal
        :Contents
         (->
          "Score: " (UI.Label)
          .total-score (ToString) (UI.Label)
          (UI.Separator)
          "Round: " (UI.Label)
          .current-round (ToString) (UI.Label)
          (UI.Separator)
          "Time Left: " (UI.Label)
          .time-remaining (ToString) (UI.Label)))))

      (UI.CentralPanel
       :Contents
       (->
        (UI.Horizontal
         :Contents
         (->
          (UI.Area
           :Position (float2 -250.0, 0.0)
           :Anchor Anchor.Center
           :Contents
           (-> .images (Take 0) (UI.Image)))
          (UI.Area
           :Position (float2 250.0, 0.0)
           :Anchor Anchor.Center
           :Contents
           (-> .images (Take 1) (UI.Image))))))))     

    (defloop ui-loop
      (GFX.MainWindow
       :Title "Yes-No Game"
       :Width 1280 :Height 768
       :Contents
       (->
        (Setup
         (GFX.DrawQueue) >= .ui-draw-queue
         (GFX.UIPass .ui-draw-queue) >> .render-steps)
        (| .ui-draw-queue (GFX.ClearQueue))
        (UI .ui-draw-queue (main-game-ui))
        (GFX.Render :Steps .render-steps))))

    (defloop logic-loop)

    (defloop game-loop
      (Setup (load-resources) (initialize-variables))
      (Branch [ui-loop, logic-loop]))

    (defmesh main)
    (schedule main game-loop)
    (run main (/ 1.0 60.0))

    ```

=== "Result"

    ![The base UI.](assets/step-2-result.png)

--8<-- "includes/license.md"
