# Step 1 

## Drawing a Character Image using UI - Overview

Let's start things off nice and easy. In this step, we will simply be:

1. Creating the `GFX.MainWindow`.

2. Drawing our Character Image in the `GFX.MainWindow`.

3. Scheduling our Wire on the Mesh and running it.

## Step 1.1: Readying the Image

Before we can draw our character on the screen, we will need the image file. Any image file will do. If you don't have one, say hello to Glod! Right-click and save the image of Glod below. 

![Glod](assets/Glod.png){ width=100 }

1. Create a folder named "GlodImages" in the same location as where your script will be located.
2. Add your image into the newly created folder.
3. Name your image "Character1.png".

With our image file readied, we can start!

## Step 1.2: Initializing our Variables

First, create the variable `character-image` to assign our Image file to. Use the shard [`LoadImage`](https://docs.fragnova.com/reference/shards/shards/General/LoadImage/) to load the image into the variable. Set your image's location as its parameter.

 We will wrap this line of code in a `@define` built-in function. This definition will be called at the start of our code, where we will be initializing all our variables. 

??? "What is Initializing?"
    Initializing is the assigning of an initial value to a variable when it is declared.

=== "Initialization Code"

    ```shards
    @define(initialize-images {
      LoadImage("GlodImages/Character1.png") = character-image
    })
    ```

## Step 1.3: Creating the main Looped Wire

Following that, we will create the main Looped Wire which will be scheduled on the Mesh. The `initialize-images` definition that we have just created will be called at the start of this Looped Wire. It will be wrapped in a `Once` shard.

??? "The Once shard"
    The `Once` shard ensures that every line of code called within it will only happen once. By placing the `initialize-images` shard within a `Once` shard, we ensure that it is only called once despite being placed in a wire that has its `Looped` parameter set to true.
    
    We want to initialize variables once only as we do not want our program to continuously create a new instance of the same variable every time the Wire loops. We only need one instance of our variables which will be reused.

=== "Main Looped Wire"

    ```shards
    @define(initialize-images {
      LoadImage("GlodImages/Character1.png") = character-image ;; (1)
    })

    @wire(main-wire {
      Once({
        @initialize-images})
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

    1. `character-image` is the variable that contains our image.

## Step 1.4: Creating the Window

Trying to run our program right now would result in... nothing. This is because all we are doing is simply initializing a variable. To draw our image on the screen however, we will have to create the window first.

=== "Initialize Code"

    ```shards
    @define(initialize-images {
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
                "Hello!" | UI.Label ;; UI.Label is a shard that renders strings in a UI context
              }
            )
          ) | UI.Render(ui-draw-queue)
          
          GFX.Render(Steps: render-steps)
        }
      )
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

Try running the program now. Something happens! The window that you see was created with the code above.

## Step 1.5: Drawing the Image on the Screen

The last step is to draw the image on screen. To do this, we use the `UI.Area` and `UI.Image`.

=== "Code Added"

    ```shards
      UI.Area(  ;; (1)
       Position: @f2(0.0 0.0)
       Anchor: Anchor::Center
       Contents: {
        character-image | UI.Image(Scale: @f2(0.2))
      })
    ```

    1.  `UI.Area` allows our image to be drawn on screen in the specified area.

=== "Full Code So Far"
    
    ```shards
    @define(initialize-images {
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
          
          GFX.Render(Steps: render-steps)
        }
      )
    } Looped: true)

    @mesh(main)
    @schedule(main main-wire)
    @run(main FPS: 60)
    ```

Try running the program now. You should see adorable little Glod in the center of your screen.

Congratulations! You have successfully drawn an image on the screen.

![Glod in the center of the screen.](assets/step1_5_Image.png)

## Recap

In this step, we have successfully drawn an image on the screen by:

1. Initializing an image variable.

2. Creating a window.

3. Using `UI.Area` and `UI.Image` to draw our image within the window.

We would be very hard-pressed however to make a game out of a single static unchanging image. In the next step, we will learn how to dynamically change adorable Glod's image by pressing buttons.

Good job so far and see you in the next step! 👋
