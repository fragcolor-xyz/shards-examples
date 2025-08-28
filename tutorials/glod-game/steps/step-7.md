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

As per before, let us create a new shard `initialize-effects` that will house all the variables that we need. Remember to call `initialize-effects` under `Setup` in `main-wire`.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; ------------- initialize effects -------------
    (defshards initialize-effects []
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_1.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_2.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_3.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_4.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_5.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_6.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_7.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_8.png") >> .score-effect-array
      (Count .score-effect-array) (Math.Subtract 1) >= .score-effect-array-index-max
      0 >= .score-effect-array-index
      0.05 >=  .score-effect-animation-speed ;; Reduce number to increase animation speed
      false >= .score-effect-play

      0.0 >= .score-effect-position-x
      0.0 >= .score-effect-position-y
      (float2 .score-effect-position-x .score-effect-position-y) >= .score-effect-position)
    ```

    ```{.clojure .annotate linenums="1"}
    (initialize-effects)
    ```

Next we create a `UI.Area` that will house our animation.

=== "Code Added"
    
  ```{.clojure .annotate linenums="1"}
  ;; ----------------- Visual Effects  -------------------
    (UI.Area :Position .score-effect-position
        :Anchor Anchor.Top
        :Contents (->
              .score-effect-array (Take .score-effect-array-index) (UI.Image :Scale (float2 0.15))))
  ```

We adjust the value of `.character-x-velocity` whenever the left and right directional keys are pressed.

Remember to return the value of `.character-x-velocity` to zero when the directional keys are released - otherwise Glod will move to the left or right forever!

=== "Code Added"
    
  ```{.clojure .annotate linenums="1"}
  ;; ------------ ScoreEffect Animation Position ------------
  (defshards scoreEffect-animation-position []
    .Y (Math.Add -15.0)
    > .score-effect-position-y
    .X
    > .score-effect-position-x

    (float2 .score-effect-position-x .score-effect-position-y)
    > .score-effect-position)
  ```

  ```{.clojure .annotate linenums="1"}
  ;; ------------ ScoreEffect Animation ------------
  (defloop scoreEffect-animation-logic
    .score-effect-play
    (When
     :Predicate (Is true)
     :Action
     (->
      .score-effect-array-index (Math.Add 1)
      > .score-effect-array-index
      (When
       :Predicate (IsMore .score-effect-array-index-max)
       :Action
       (->
        0 > .score-effect-array-index
        false > .score-effect-play))))
    (Pause .score-effect-animation-speed))
  ```

Next we have some logic to loop and play our animation. This animation plays when `.score-effect-play` is true.

This variable will then go false at the end of the animation for it to stop. This is the technique to use when you want an animation to only play once.

=== "Code Added"
    
  ```{.clojure .annotate linenums="1"}
  true > .score-effect-play
  ```

Then remember to make `.score-effect-play` true whenever we collect a coin. We can add this line in our scoring code that we have written previously.

=== "Code Added"
    
  ```{.clojure .annotate linenums="1"}
  (scoreEffect-animation-position)
  (Step scoreEffect-animation-logic)
  ```

Lastly, remember to call your shard and loop in your `main-wire`.

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) = .coin-image-index-max
      0 >= .coin-image-index
      0.1 = .coin-animation-speed

      ;; ----- Coin 1 ------
      0.0 >= .coinx-1
      0.0 >= .coiny-1
      (float2 .coinx-1 .coiny-1) >= .coin-position-1
      0.0 >= .coin-velocity-1
      0.5 >= .coin-acceleration

      ;; ----- Coin 2 ----
      0.0 >= .coinx-2
      0.0 >= .coiny-2
      (float2 .coinx-2 .coiny-2) >= .coin-position-2
      0.0 >= .coin-velocity-2)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMoreEqual .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic [coiny coinx coin-velocity coin-position]

      coiny (Math.Add coin-velocity)
      > coiny

      coin-velocity (Math.Add .coin-acceleration)
      > coin-velocity

      (float2 coinx coiny) > coin-position)

    ;; ------------- Random Coin ------------------
    (defshards random-coin [coinx coiny coin-velocity coin-position pause-length]
      coinx
      (RandomFloat :Max 1200.0)
      > coinx
      (Math.Subtract 600.0)
      > coinx

      0.0 > coiny
      0.0 > coin-velocity
      (float2 coinx coiny) > coin-position
      (Pause pause-length))

    (defloop random-coin-1
      (random-coin .coinx-1 .coiny-1 .coin-velocity-1 .coin-position-1 1.5))

    (defloop random-coin-2
      (random-coin .coinx-2 .coiny-2 .coin-velocity-2 .coin-position-2 2.5))

    ;; ------------ Initialize Spiked CanonBalls ---------------
    (defshards initialize-spiked-canonballs []
      (LoadTexture "GlodImages/SpikeBall/SpikeBall1.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall2.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall3.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall4.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall5.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall6.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall7.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall8.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall9.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall10.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall11.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall12.png") >> .spikeball-array

      (Count .spikeball-array) = .spikeball-array-index-max
      0 >= .spikeball-index
      0.06 = .spikeball-animation-speed

      ;; ---------- spikball-1 -------------
      1.0 >= .spikeball-velocity-1
      0.0 >= .spikeball-y-1
      0.0 >= .spikeball-x-1
      (float2 .spikeball-x-1 .spikeball-y-1) >= .spikeball-position-1

      ;; ---------- spikeball-2 -------------
      1.0 >= .spikeball-velocity-2
      0.0 >= .spikeball-y-2
      0.0 >= .spikeball-x-2
      (float2 .spikeball-x-2 .spikeball-y-2) >= .spikeball-position-2

    ;; ---------- SpikeBall_3 -------------
      1.0 >= .spikeball-velocity-3
      0.0 >= .spikeball-y-3
      0.0 >= .spikeball-x-3
      (float2 .spikeball-x-3 .spikeball-y-3) >= .spikeball-position-3

      0.5 >= .spikeball-acceleration)

    ;;------------- Spiked CanonBall Animation -------------
    (defloop spiked-canonball-animation
      .spikeball-index (Math.Add 1)
      > .spikeball-index
      (When :Predicate (IsMoreEqual .spikeball-array-index-max)
            :Action (-> 0 > .spikeball-index))

      (Pause .spikeball-animation-speed))

    ;; ------------- SpikeBall_Gravity_Logic -------------
    (defshards spikeball-gravity-logic [spikeball-y spikeball-velocity spikeball-position spikeball-x]
      spikeball-y (Math.Add spikeball-velocity)
      > spikeball-y
      spikeball-velocity (Math.Add .spikeball-acceleration)
      > spikeball-velocity
      (float2 spikeball-x spikeball-y) > spikeball-position)

    ;; ------------- Randomise Spikeball ----------------
    (defshards randomise-spikeball [spikeball-x spikeball-y spikeball-velocity spikeball-position pausefloat]
      spikeball-x
      (RandomFloat :Max 1200.0)
      > spikeball-x
      (Math.Subtract 600.0)
      > spikeball-x

      0.0 > spikeball-y
      0.0 > spikeball-velocity
      (float2 spikeball-x spikeball-y) > spikeball-position
      
      (Pause pausefloat))

    (defloop spikeball-1
      (randomise-spikeball .spikeball-x-1 .spikeball-y-1 .spikeball-velocity-1 .spikeball-position-1 2))
    (defloop spikeball-2
      (randomise-spikeball .spikeball-x-2 .spikeball-y-2 .spikeball-velocity-2 .spikeball-position-2 3))
    (defloop spikeball-3
      (randomise-spikeball .spikeball-x-3 .spikeball-y-3 .spikeball-velocity-3 .spikeball-position-3 4))

    ;; --------- Game Elements ------------
    (defshards initialize-game-elements []
      ;;------------ Scoring Limits ----------
      0 >= .score
      false >= .scored

      .X (Math.Add 50.0)
      >= .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .scoringLower-x-limit

      .Y (Math.Add 10.0)
      >= .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      >= .scoringLower-y-limit

      ;; ---------- Damage Limits ------------
      .X (Math.Add 50.0)
      >= .damageUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .damageLower-x-limit

      .Y (Math.Add 5.0)
      >= .damageUpper-y-limit
      .Y (Math.Subtract 5.0)
      >= .damageLower-y-limit

      false >= .damaged)

    ;; --------- Scoring ----------
    (defshards score-collision [coinx coiny]
      coinx
      (When :Predicate (->
                        (IsLess .scoringUpper-x-limit)
                        (And)
                        coinx (IsMore .scoringLower-x-limit)
                        (And)
                        coiny (IsLess .scoringUpper-y-limit)
                        (And)
                        coiny (IsMore .scoringLower-y-limit)
                        (And)
                        .scored (Is false))
            :Action (->
                    true > .scored
                    (Log "Score: "))))

    (defshards scoring []
      .X (Math.Add 50.0)
      > .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      > .scoringLower-x-limit

      .Y (Math.Add 10.0)
      > .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      > .scoringLower-y-limit

      (score-collision .coinx-1 .coiny-1)
      (score-collision .coinx-2 .coiny-2)

      .scored
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Add 1)
                    > .score
                    true > .score-effect-play
                    false > .scored)))

    ;; ------------- spikeBall-collision-logic --------------
    (defshards spikeBall-collision-logic [spikeBall-x spikeBall-y]

      spikeBall-x
      (If :Predicate (-> (IsLess .damageUpper-x-limit)
                        (And)
                        spikeBall-x (IsMore .damageLower-x-limit)
                        (And)
                        spikeBall-y (IsLess .damageUpper-y-limit)
                        (And)
                        spikeBall-y (IsMore .damageLower-y-limit))

          :Then (-> .damaged
                    (When :Predicate (Is false)
                          :Action (->
                                  true > .damaged
                                  (Log "damaged: "))))))

    ;; -------------- Damaging --------------
    (defshards damaging []

      .X (Math.Add 120.0)
      > .damageUpper-x-limit
      .X (Math.Subtract 120.0)
      > .damageLower-x-limit

      .Y (Math.Add 15.0)
      > .damageUpper-y-limit
      .Y (Math.Subtract 15.0)
      > .damageLower-y-limit

      (spikeBall-collision-logic .spikeball-x-1 .spikeball-y-1)
      (spikeBall-collision-logic .spikeball-x-2 .spikeball-y-2)
      (spikeBall-collision-logic .spikeball-x-3 .spikeball-y-3)

      .damaged
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Subtract 1)
                    > .score
                    false > .damaged)))

    ;; ------------- initialize effects -------------
    (defshards initialize-effects []
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_1.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_2.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_3.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_4.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_5.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_6.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_7.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_8.png") >> .score-effect-array
      (Count .score-effect-array) (Math.Subtract 1) >= .score-effect-array-index-max
      0 >= .score-effect-array-index
      0.05 >=  .score-effect-animation-speed ;; Reduce number to increase animation speed
      false >= .score-effect-play

      0.0 >= .score-effect-position-x
      0.0 >= .score-effect-position-y
      (float2 .score-effect-position-x .score-effect-position-y) >= .score-effect-position)

    ;; ------------ ScoreEffect_Animation_Position ------------
    (defshards scoreEffect-animation-position []
      .Y (Math.Add -15.0)
      > .score-effect-position-y

      .X
      > .score-effect-position-x

      (float2 .score-effect-position-x .score-effect-position-y)
      > .score-effect-position)

    ;; ------------ ScoreEffect Animation ------------
    (defloop scoreEffect-animation-logic

      .score-effect-play
      (When :Predicate (Is true)
            :Action (->
                    .score-effect-array-index (Math.Add 1)
                    > .score-effect-array-index
                    (When :Predicate (IsMore .score-effect-array-index-max)
                          :Action (->
                                    0 > .score-effect-array-index
                                    false > .score-effect-play))))


      (Pause .score-effect-animation-speed))

    ;; ------ UI Style --------
    (def style
      {:override_text_style "MyStyle"
      :text_styles
      [{:name "MyStyle"
        :size (float 46)
        :family "Monospace"}]
      :visuals
      {:override_text_color (color 250 250 250)}})

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin)
      (initialize-game-elements)
      (initialize-spiked-canonballs)
      (initialize-effects))

      (coin-gravity-logic .coiny-1 .coinx-1 .coin-velocity-1 .coin-position-1)
      (coin-gravity-logic .coiny-2 .coinx-2 .coin-velocity-2 .coin-position-2)

      (run-logic)
      (gravity-logic)
      (scoring)
      (damaging)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)
      (Step random-coin-1)
      (Step random-coin-2)

      (Step spiked-canonball-animation)
      (spikeball-gravity-logic .spikeball-y-1 .spikeball-velocity-1 .spikeball-position-1 .spikeball-x-1)
      (spikeball-gravity-logic .spikeball-y-2 .spikeball-velocity-2 .spikeball-position-2 .spikeball-x-2)
      (spikeball-gravity-logic .spikeball-y-3 .spikeball-velocity-3 .spikeball-position-3 .spikeball-x-3)
      (Step  spikeball-1)
      (Step  spikeball-2)
      (Step  spikeball-3)

      (scoreEffect-animation-position)
      (Step scoreEffect-animation-logic)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            ;; ---------- Coins -----------

            (UI.Area :Position .coin-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            (UI.Area :Position .coin-position-2
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            ;; ------------SpikeBalls ------------

            (UI.Area :Position .spikeball-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            (UI.Area :Position .spikeball-position-2
                      :Anchor Anchor.Top
                      :Contents (->
                                .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            (UI.Area :Position .spikeball-position-3
                      :Anchor Anchor.Top
                      :Contents (->
                                .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            ;; ----------------- Visual Effects  -------------------
            (UI.Area :Position .score-effect-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .score-effect-array (Take .score-effect-array-index) (UI.Image :Scale (float2 0.15))))

            (UI.Area :Position (float2 -40 20)
                      :Anchor Anchor.TopRight
                      :Contents (->
                                style (UI.Style)
                                .score (ToString) (UI.Label)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Now, it should be more fun for you to collect coins! 💰

## Step 7.2

Now following the same logic, we will create feedback for when we are damaged.

- Download Damage Effect images [here](https://drive.google.com/drive/folders/1NYoURr7sryqWfbC0OMSf52CsbkHPPk25?usp=share_link).

Create all the variables that we need for creating animations. Add it under `initialize-effects`.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; --------------- Damaged Effect ----------------
    (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_1.png") >> .damage-effect-array
    (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_2.png") >> .damage-effect-array
    (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_3.png") >> .damage-effect-array
    (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_4.png") >> .damage-effect-array
    (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_5.png") >> .damage-effect-array
    (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_6.png") >> .damage-effect-array
    (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_7.png") >> .damage-effect-array
    (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_8.png") >> .damage-effect-array
    (Count .damage-effect-array) (Math.Subtract 1) >= .damage-effect-array-index-max
    0 >= .damage-effect-array-index
    0.02 >=  .damage-effect-animation-speed ;; Reduce number to increase animation speed
    false >= .damage-effect-play
    ```

Create a `UI.Area` which will house our animation.

=== "Code Added"
   
    ```{.clojure .annotate linenums="1"}
    (UI.Area :Position (float2 0 0)
            :Anchor Anchor.TopLeft
            :Contents (->
                          .damage-effect-array (Take .damage-effect-array-index) (UI.Image :Scale (float2 10))))
    ```

Create our logic to loop our damage effect animation which plays when `.damage-effect-play` is true.

=== "Code Added"
   
    ```{.clojure .annotate linenums="1"}
    ;; ------------ DamageEffect Animation ------------
    (defloop damage-effect-animation-logic

      .damage-effect-play
      (When :Predicate (Is true)
            :Action (->
                    .damage-effect-array-index (Math.Add 1)
                    > .damage-effect-array-index
                    (When :Predicate (IsMore .damage-effect-array-index-max)
                          :Action (->
                                    0 > .damage-effect-array-index
                                    false > .damage-effect-play))))


      (Pause .damage-effect-animation-speed))
      
    ```

 Make `.damage-effect-play` `true` whenever we collide with our spiked cannonballs. This will be added to our `spikeball-damage-logic`, which we have created earlier.

=== "Code Added"
   
    ```{.clojure .annotate linenums="1"}
    true > .damage-effect-play
    ```

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) = .coin-image-index-max
      0 >= .coin-image-index
      0.1 = .coin-animation-speed

      ;; ----- Coin 1 ------
      0.0 >= .coinx-1
      0.0 >= .coiny-1
      (float2 .coinx-1 .coiny-1) >= .coin-position-1
      0.0 >= .coin-velocity-1
      0.5 >= .coin-acceleration

      ;; ----- Coin 2 ----
      0.0 >= .coinx-2
      0.0 >= .coiny-2
      (float2 .coinx-2 .coiny-2) >= .coin-position-2
      0.0 >= .coin-velocity-2)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMoreEqual .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic [coiny coinx coin-velocity coin-position]

      coiny (Math.Add coin-velocity)
      > coiny

      coin-velocity (Math.Add .coin-acceleration)
      > coin-velocity

      (float2 coinx coiny) > coin-position)

    ;; ------------- Random Coin ------------------
    (defshards random-coin [coinx coiny coin-velocity coin-position pause-length]
      coinx
      (RandomFloat :Max 1200.0)
      > coinx
      (Math.Subtract 600.0)
      > coinx

      0.0 > coiny
      0.0 > coin-velocity
      (float2 coinx coiny) > coin-position
      (Pause pause-length))

    (defloop random-coin-1
      (random-coin .coinx-1 .coiny-1 .coin-velocity-1 .coin-position-1 1.5))

    (defloop random-coin-2
      (random-coin .coinx-2 .coiny-2 .coin-velocity-2 .coin-position-2 2.5))

    ;; ------------ Initialize Spiked CanonBalls ---------------
    (defshards initialize-spiked-canonballs []
      (LoadTexture "GlodImages/SpikeBall/SpikeBall1.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall2.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall3.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall4.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall5.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall6.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall7.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall8.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall9.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall10.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall11.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall12.png") >> .spikeball-array

      (Count .spikeball-array) = .spikeball-array-index-max
      0 >= .spikeball-index
      0.06 = .spikeball-animation-speed

      ;; ---------- spikball-1 -------------
      1.0 >= .spikeball-velocity-1
      0.0 >= .spikeball-y-1
      0.0 >= .spikeball-x-1
      (float2 .spikeball-x-1 .spikeball-y-1) >= .spikeball-position-1

      ;; ---------- spikeball-2 -------------
      1.0 >= .spikeball-velocity-2
      0.0 >= .spikeball-y-2
      0.0 >= .spikeball-x-2
      (float2 .spikeball-x-2 .spikeball-y-2) >= .spikeball-position-2

    ;; ---------- SpikeBall_3 -------------
      1.0 >= .spikeball-velocity-3
      0.0 >= .spikeball-y-3
      0.0 >= .spikeball-x-3
      (float2 .spikeball-x-3 .spikeball-y-3) >= .spikeball-position-3

      0.5 >= .spikeball-acceleration)

    ;;------------- Spiked CanonBall Animation -------------
    (defloop spiked-canonball-animation
      .spikeball-index (Math.Add 1)
      > .spikeball-index
      (When :Predicate (IsMoreEqual .spikeball-array-index-max)
            :Action (-> 0 > .spikeball-index))

      (Pause .spikeball-animation-speed))

    ;; ------------- SpikeBall_Gravity_Logic -------------
    (defshards spikeball-gravity-logic [spikeball-y spikeball-velocity spikeball-position spikeball-x]
      spikeball-y (Math.Add spikeball-velocity)
      > spikeball-y
      spikeball-velocity (Math.Add .spikeball-acceleration)
      > spikeball-velocity
      (float2 spikeball-x spikeball-y) > spikeball-position)

    ;; ------------- Randomise Spikeball ----------------
    (defshards randomise-spikeball [spikeball-x spikeball-y spikeball-velocity spikeball-position pausefloat]
      spikeball-x
      (RandomFloat :Max 1200.0)
      > spikeball-x
      (Math.Subtract 600.0)
      > spikeball-x

      0.0 > spikeball-y
      0.0 > spikeball-velocity
      (float2 spikeball-x spikeball-y) > spikeball-position
      
      (Pause pausefloat))

    (defloop spikeball-1
      (randomise-spikeball .spikeball-x-1 .spikeball-y-1 .spikeball-velocity-1 .spikeball-position-1 2))
    (defloop spikeball-2
      (randomise-spikeball .spikeball-x-2 .spikeball-y-2 .spikeball-velocity-2 .spikeball-position-2 3))
    (defloop spikeball-3
      (randomise-spikeball .spikeball-x-3 .spikeball-y-3 .spikeball-velocity-3 .spikeball-position-3 4))

    ;; --------- Game Elements ------------
    (defshards initialize-game-elements []
      ;;------------ Scoring Limits ----------
      0 >= .score
      false >= .scored

      .X (Math.Add 50.0)
      >= .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .scoringLower-x-limit

      .Y (Math.Add 10.0)
      >= .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      >= .scoringLower-y-limit

      ;; ---------- Damage Limits ------------
      .X (Math.Add 50.0)
      >= .damageUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .damageLower-x-limit

      .Y (Math.Add 5.0)
      >= .damageUpper-y-limit
      .Y (Math.Subtract 5.0)
      >= .damageLower-y-limit

      false >= .damaged)

    ;; --------- Scoring ----------
    (defshards score-collision [coinx coiny]
      coinx
      (When :Predicate (->
                        (IsLess .scoringUpper-x-limit)
                        (And)
                        coinx (IsMore .scoringLower-x-limit)
                        (And)
                        coiny (IsLess .scoringUpper-y-limit)
                        (And)
                        coiny (IsMore .scoringLower-y-limit)
                        (And)
                        .scored (Is false))
            :Action (->
                    true > .scored
                    (Log "Score: "))))

    (defshards scoring []
      .X (Math.Add 50.0)
      > .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      > .scoringLower-x-limit

      .Y (Math.Add 10.0)
      > .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      > .scoringLower-y-limit

      (score-collision .coinx-1 .coiny-1)
      (score-collision .coinx-2 .coiny-2)

      .scored
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Add 1)
                    > .score
                    true > .score-effect-play
                    false > .scored)))

    ;; ------------- spikeBall-collision-logic --------------
    (defshards spikeBall-collision-logic [spikeBall-x spikeBall-y]

      spikeBall-x
      (If :Predicate (-> (IsLess .damageUpper-x-limit)
                        (And)
                        spikeBall-x (IsMore .damageLower-x-limit)
                        (And)
                        spikeBall-y (IsLess .damageUpper-y-limit)
                        (And)
                        spikeBall-y (IsMore .damageLower-y-limit))

          :Then (-> .damaged
                    (When :Predicate (Is false)
                          :Action (->
                                  true > .damaged
                                  (Log "damaged: "))))))

    ;; -------------- Damaging --------------
    (defshards damaging []

      .X (Math.Add 120.0)
      > .damageUpper-x-limit
      .X (Math.Subtract 120.0)
      > .damageLower-x-limit

      .Y (Math.Add 15.0)
      > .damageUpper-y-limit
      .Y (Math.Subtract 15.0)
      > .damageLower-y-limit

      (spikeBall-collision-logic .spikeball-x-1 .spikeball-y-1)
      (spikeBall-collision-logic .spikeball-x-2 .spikeball-y-2)
      (spikeBall-collision-logic .spikeball-x-3 .spikeball-y-3)

      .damaged
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Subtract 1)
                    > .score
                    true > .damage-effect-play
                    false > .damaged)))

    ;; ------------- initialize effects -------------
    (defshards initialize-effects []
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_1.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_2.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_3.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_4.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_5.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_6.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_7.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_8.png") >> .score-effect-array
      (Count .score-effect-array) (Math.Subtract 1) >= .score-effect-array-index-max
      0 >= .score-effect-array-index
      0.05 >=  .score-effect-animation-speed ;; Reduce number to increase animation speed
      false >= .score-effect-play

      0.0 >= .score-effect-position-x
      0.0 >= .score-effect-position-y
      (float2 .score-effect-position-x .score-effect-position-y) >= .score-effect-position

      ;; --------------- Damaged Effect ----------------
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_1.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_2.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_3.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_4.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_5.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_6.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_7.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_8.png") >> .damage-effect-array
      (Count .damage-effect-array) (Math.Subtract 1) >= .damage-effect-array-index-max
      0 >= .damage-effect-array-index
      0.02 >=  .damage-effect-animation-speed ;; Reduce number to increase animation speed
      false >= .damage-effect-play)

    ;; ------------ DamageEffect Animation ------------
    (defloop damage-effect-animation-logic

      .damage-effect-play
      (When :Predicate (Is true)
            :Action (->
                    .damage-effect-array-index (Math.Add 1)
                    > .damage-effect-array-index
                    (When :Predicate (IsMore .damage-effect-array-index-max)
                          :Action (->
                                    0 > .damage-effect-array-index
                                    false > .damage-effect-play))))

      (Pause .damage-effect-animation-speed))

    ;; ------------ ScoreEffect_Animation_Position ------------
    (defshards scoreEffect-animation-position []
      .Y (Math.Add -15.0)
      > .score-effect-position-y

      .X
      > .score-effect-position-x

      (float2 .score-effect-position-x .score-effect-position-y)
      > .score-effect-position)

    ;; ------------ ScoreEffect Animation ------------
    (defloop scoreEffect-animation-logic

      .score-effect-play
      (When :Predicate (Is true)
            :Action (->
                    .score-effect-array-index (Math.Add 1)
                    > .score-effect-array-index
                    (When :Predicate (IsMore .score-effect-array-index-max)
                          :Action (->
                                    0 > .score-effect-array-index
                                    false > .score-effect-play))))


      (Pause .score-effect-animation-speed))

    ;; ------ UI Style --------
    (def style
      {:override_text_style "MyStyle"
      :text_styles
      [{:name "MyStyle"
        :size (float 46)
        :family "Monospace"}]
      :visuals
      {:override_text_color (color 250 250 250)}})

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin)
      (initialize-game-elements)
      (initialize-spiked-canonballs)
      (initialize-effects))

      (coin-gravity-logic .coiny-1 .coinx-1 .coin-velocity-1 .coin-position-1)
      (coin-gravity-logic .coiny-2 .coinx-2 .coin-velocity-2 .coin-position-2)

      (run-logic)
      (gravity-logic)
      (scoring)
      (damaging)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)
      (Step random-coin-1)
      (Step random-coin-2)

      (Step spiked-canonball-animation)
      (spikeball-gravity-logic .spikeball-y-1 .spikeball-velocity-1 .spikeball-position-1 .spikeball-x-1)
      (spikeball-gravity-logic .spikeball-y-2 .spikeball-velocity-2 .spikeball-position-2 .spikeball-x-2)
      (spikeball-gravity-logic .spikeball-y-3 .spikeball-velocity-3 .spikeball-position-3 .spikeball-x-3)
      (Step  spikeball-1)
      (Step  spikeball-2)
      (Step  spikeball-3)

      (scoreEffect-animation-position)
      (Step scoreEffect-animation-logic)
      (Step damage-effect-animation-logic)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            ;; ---------- Coins -----------

            (UI.Area :Position .coin-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            (UI.Area :Position .coin-position-2
                      :Anchor Anchor.Top
                      :Contents (->
                                .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            ;; ------------SpikeBalls ------------

            (UI.Area :Position .spikeball-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            (UI.Area :Position .spikeball-position-2
                      :Anchor Anchor.Top
                      :Contents (->
                                .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            (UI.Area :Position .spikeball-position-3
                      :Anchor Anchor.Top
                      :Contents (->
                                .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            ;; ----------------- Visual Effects  -------------------
            (UI.Area :Position .score-effect-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .score-effect-array (Take .score-effect-array-index) (UI.Image :Scale (float2 0.15))))
            
            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.TopLeft
                      :Contents (->
                                .damage-effect-array (Take .damage-effect-array-index) (UI.Image :Scale (float2 10))))
            
            ;; --------------- UI Score --------------

            (UI.Area :Position (float2 -40 20)
                      :Anchor Anchor.TopRight
                      :Contents (->
                                style (UI.Style)
                                .score (ToString) (UI.Label)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Good job!

Now we have even more motivation to avoid getting hit! 🚨🚨🚨🚨🚨

## Step 7.3

This step will be extremely easy.

We will simply be adding a background image to our game to make it more visually appealing.

- Download background image [here](https://drive.google.com/file/d/1qP6JDdKfhwsc9guBkkIy9sjK02LMTGq3/view?usp=share_link).

First, load our image.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (LoadTexture "GlodImages/BG.png") = .bg-image 
    ```

Next, create a `UI.Area` to house our new image.

Remember to add this `UI.Area` before the `UI.Area` which houses your character to ensure that it is drawn below your character.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.TopLeft
                      :Contents (->
                                .bg-image (UI.Image :Scale (float2 0.7))))
    ```

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards initialize-character []
      (LoadImage "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadImage "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) (Math.Subtract 1) >= .idle-image-index-max
      0.08 >= .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) (Math.Subtract 1) >= .walking-image-index-max
      0 >= .walking-image-index
      0.08 >= .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMore .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMore .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadImage "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) (Math.Subtract 1) >= .coin-image-index-max
      0 >= .coin-image-index
      0.1 >= .coin-animation-speed

      ;; ----- Coin 1 ------
      0.0 >= .coinx-1
      0.0 >= .coiny-1
      (float2 .coinx-1 .coiny-1) >= .coin-position-1
      0.0 >= .coin-velocity-1
      0.5 >= .coin-acceleration

      ;; ----- Coin 2 ----
      0.0 >= .coinx-2
      0.0 >= .coiny-2
      (float2 .coinx-2 .coiny-2) >= .coin-position-2
      0.0 >= .coin-velocity-2)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMore .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic [coiny coinx coin-velocity coin-position]

      coiny (Math.Add coin-velocity)
      > coiny

      coin-velocity (Math.Add .coin-acceleration)
      > coin-velocity

      (float2 coinx coiny) > coin-position)

    ;; ------------- Random Coin ------------------
    (defshards random-coin [coinx coiny coin-velocity coin-position pause-length]
      coinx
      (RandomFloat :Max 1200.0)
      > coinx
      (Math.Subtract 600.0)
      > coinx

      0.0 > coiny
      0.0 > coin-velocity
      (float2 coinx coiny) > coin-position
      (Pause pause-length))

    (defloop random-coin-1
      (random-coin .coinx-1 .coiny-1 .coin-velocity-1 .coin-position-1 1.5))

    (defloop random-coin-2
      (random-coin .coinx-2 .coiny-2 .coin-velocity-2 .coin-position-2 2.5))

    ;; ------------ Initialize Spiked CanonBalls ---------------
    (defshards initialize-spiked-canonballs []
      (LoadImage "GlodImages/SpikeBall/SpikeBall1.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall2.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall3.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall4.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall5.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall6.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall7.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall8.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall9.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall10.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall11.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall12.png") >> .spikeball-array

      (Count .spikeball-array) (Math.Subtract 1) >= .spikeball-array-index-max
      0 >= .spikeball-index
      0.06 >= .spikeball-animation-speed

      ;; ---------- spikball-1 -------------
      1.0 >= .spikeball-velocity-1
      0.0 >= .spikeball-y-1
      0.0 >= .spikeball-x-1
      (float2 .spikeball-x-1 .spikeball-y-1) >= .spikeball-position-1

      ;; ---------- spikeball-2 -------------
      1.0 >= .spikeball-velocity-2
      0.0 >= .spikeball-y-2
      0.0 >= .spikeball-x-2
      (float2 .spikeball-x-2 .spikeball-y-2) >= .spikeball-position-2

      ;; ---------- SpikeBall_3 -------------
      1.0 >= .spikeball-velocity-3
      0.0 >= .spikeball-y-3
      0.0 >= .spikeball-x-3
      (float2 .spikeball-x-3 .spikeball-y-3) >= .spikeball-position-3

      0.5 >= .spikeball-acceleration)

    ;;------------- Spiked CanonBall Animation -------------
    (defloop spiked-canonball-animation
      .spikeball-index (Math.Add 1)
      > .spikeball-index
      (When :Predicate (IsMore .spikeball-array-index-max)
            :Action (-> 0 > .spikeball-index))

      (Pause .spikeball-animation-speed))

    ;; ------------- SpikeBall_Gravity_Logic -------------
    (defshards spikeball-gravity-logic [spikeball-y spikeball-velocity spikeball-position spikeball-x]
      spikeball-y (Math.Add spikeball-velocity)
      > spikeball-y
      spikeball-velocity (Math.Add .spikeball-acceleration)
      > spikeball-velocity
      (float2 spikeball-x spikeball-y) > spikeball-position)

    (defshards randomise-spikeball [spikeball-x spikeball-y spikeball-velocity spikeball-position pausefloat]
      spikeball-x
      (RandomFloat :Max 1200.0)
      > spikeball-x
      (Math.Subtract 600.0)
      > spikeball-x

      0.0 > spikeball-y
      0.0 > spikeball-velocity
      (float2 spikeball-x spikeball-y) > spikeball-position
      
      (Pause pausefloat))

    (defloop spikeball-1
      (randomise-spikeball .spikeball-x-1 .spikeball-y-1 .spikeball-velocity-1 .spikeball-position-1 2))
    (defloop spikeball-2
      (randomise-spikeball .spikeball-x-2 .spikeball-y-2 .spikeball-velocity-2 .spikeball-position-2 3))
    (defloop spikeball-3
      (randomise-spikeball .spikeball-x-3 .spikeball-y-3 .spikeball-velocity-3 .spikeball-position-3 4))


    ;; --------- Game Elements ------------
    (defshards initialize-game-elements []
      0 >= .score
      false >= .scored

      .X (Math.Add 50.0)
      >= .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .scoringLower-x-limit

      .Y (Math.Add 10.0)
      >= .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      >= .scoringLower-y-limit

      ;; ---------- Damage Limits ------------
      .X (Math.Add 50.0)
      >= .damageUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .damageLower-x-limit

      .Y (Math.Add 5.0)
      >= .damageUpper-y-limit
      .Y (Math.Subtract 5.0)
      >= .damageLower-y-limit

      false >= .damaged)

    ;; --------- Scoring ----------
    (defshards score-collision [coinx coiny]
      coinx
      (When :Predicate (->
                        (IsLess .scoringUpper-x-limit)
                        (And)
                        coinx (IsMore .scoringLower-x-limit)
                        (And)
                        coiny (IsLess .scoringUpper-y-limit)
                        (And)
                        coiny (IsMore .scoringLower-y-limit)
                        (And)
                        .scored (Is false))
            :Action (->
                    true > .scored
                    (Log "Score: "))))

    (defshards scoring []
      .X (Math.Add 50.0)
      > .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      > .scoringLower-x-limit

      .Y (Math.Add 10.0)
      > .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      > .scoringLower-y-limit

      (score-collision .coinx-1 .coiny-1)
      (score-collision .coinx-2 .coiny-2)

      .scored
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Add 1)
                    > .score
                    true > .score-effect-play
                    false > .scored)))

    ;; ------------- spikeBall-collision-logic --------------
    (defshards spikeBall-collision-logic [spikeBall-x spikeBall-y]

      spikeBall-x
      (If :Predicate (-> (IsLess .damageUpper-x-limit)
                        (And)
                        spikeBall-x (IsMore .damageLower-x-limit)
                        (And)
                        spikeBall-y (IsLess .damageUpper-y-limit)
                        (And)
                        spikeBall-y (IsMore .damageLower-y-limit))

          :Then (-> .damaged
                    (When :Predicate (Is false)
                          :Action (->
                                  true > .damaged
                                  (Log "damaged: "))))))

    ;; -------------- Damaging --------------
    (defshards damaging []

      .X (Math.Add 120.0)
      > .damageUpper-x-limit
      .X (Math.Subtract 120.0)
      > .damageLower-x-limit

      .Y (Math.Add 15.0)
      > .damageUpper-y-limit
      .Y (Math.Subtract 15.0)
      > .damageLower-y-limit

      (spikeBall-collision-logic .spikeball-x-1 .spikeball-y-1)
      (spikeBall-collision-logic .spikeball-x-2 .spikeball-y-2)
      (spikeBall-collision-logic .spikeball-x-3 .spikeball-y-3)

      .damaged
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Subtract 1)
                    > .score
                    true > .damage-effect-play
                    false > .damaged)))


    ;; ------------- innitialize effects -------------
    (defshards initialize-effects []
      ;; --------------- Animation Effects -----------------
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_1.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_2.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_3.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_4.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_5.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_6.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_7.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_8.png") >> .score-effect-array
      (Count .score-effect-array) (Math.Subtract 1) >= .score-effect-array-index-max
      0 >= .score-effect-array-index
      0.02 >=  .score-effect-animation-speed ;; Reduce number to increase animation speed
      false >= .score-effect-play

      0.0 >= .score-effect-position-x
      0.0 >= .score-effect-position-y
      (float2 .score-effect-position-x .score-effect-position-y) >= .score-effect-position

      ;; --------------- Damaged Effect ----------------
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_1.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_2.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_3.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_4.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_5.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_6.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_7.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_8.png") >> .damage-effect-array
      (Count .damage-effect-array) (Math.Subtract 1) >= .damage-effect-array-index-max
      0 >= .damage-effect-array-index
      0.02 >=  .damage-effect-animation-speed ;; Reduce number to increase animation speed
      false >= .damage-effect-play
      
      (LoadImage "GlodImages/BG.png") = .bg-image)

    ;; ------------ ScoreEffect Animation Position ------------
    (defshards scoreEffect-animation-position []
      .Y (Math.Add -15.0)
      > .score-effect-position-y

      .X
      > .score-effect-position-x

      (float2 .score-effect-position-x .score-effect-position-y)
      > .score-effect-position)

    ;; ------------ ScoreEffect Animation ------------
    (defloop scoreEffect-animation-logic

      .score-effect-play
      (When :Predicate (Is true)
            :Action (->
                    .score-effect-array-index (Math.Add 1)
                    > .score-effect-array-index
                    (When :Predicate (IsMore .score-effect-array-index-max)
                          :Action (->
                                    0 > .score-effect-array-index
                                    false > .score-effect-play))))


      (Pause .score-effect-animation-speed))

    ;; ------------ DamageEffect Animation ------------
    (defloop damage-effect-animation-logic

      .damage-effect-play
      (When :Predicate (Is true)
            :Action (->
                    .damage-effect-array-index (Math.Add 1)
                    > .damage-effect-array-index
                    (When :Predicate (IsMore .damage-effect-array-index-max)
                          :Action (->
                                    0 > .damage-effect-array-index
                                    false > .damage-effect-play))))


      (Pause .damage-effect-animation-speed))


    ;; ------ UI Style --------
    (def style
      {:override_text_style "MyStyle"
      :text_styles
      [{:name "MyStyle"
        :size (float 46)
        :family "Monospace"}]
      :visuals
      {:override_text_color (color 250 250 250)}})

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin)
      (initialize-game-elements)
      (initialize-spiked-canonballs)
      (initialize-effects))

      (coin-gravity-logic .coiny-1 .coinx-1 .coin-velocity-1 .coin-position-1)
      (coin-gravity-logic .coiny-2 .coinx-2 .coin-velocity-2 .coin-position-2)

      (run-logic)
      (gravity-logic)
      (scoring)
      (damaging)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)
      (Step random-coin-1)
      (Step random-coin-2)

      (Step spiked-canonball-animation)
      (spikeball-gravity-logic .spikeball-y-1 .spikeball-velocity-1 .spikeball-position-1 .spikeball-x-1)
      (spikeball-gravity-logic .spikeball-y-2 .spikeball-velocity-2 .spikeball-position-2 .spikeball-x-2)
      (spikeball-gravity-logic .spikeball-y-3 .spikeball-velocity-3 .spikeball-position-3 .spikeball-x-3)
      (Step  spikeball-1)
      (Step  spikeball-2)
      (Step  spikeball-3)

      (scoreEffect-animation-position)
      (Step scoreEffect-animation-logic)
      (Step damage-effect-animation-logic)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->

            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.TopLeft
                      :Contents (->
                                .bg-image (UI.Image :Scale (float2 0.7))))

            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> LoadTexture .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> LoadTexture .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> LoadTexture .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> LoadTexture .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> LoadTexture .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> LoadTexture .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            ;; -------- coins ui area -----------

            (UI.Area :Position .coin-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            (UI.Area :Position .coin-position-2
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))
            ;; --------- spikeball ui area ----------

            (UI.Area :Position .spikeball-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            (UI.Area :Position .spikeball-position-2
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            (UI.Area :Position .spikeball-position-3
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            ;; ----------------- Visual Effects  -------------------
            (UI.Area :Position .score-effect-position
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .score-effect-array (Take .score-effect-array-index) (UI.Image :Scale (float2 0.15))))

            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.TopLeft
                      :Contents (->
                                LoadTexture .damage-effect-array (Take .damage-effect-array-index) (UI.Image :Scale (float2 10))))

            ;; ---------------- UI ------------------

            (UI.Area :Position (float2 -40 20)
                      :Anchor Anchor.TopRight
                      :Contents (->
                                style (UI.Style)
                                .score (ToString) (UI.Label)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

Now we have a background image! 🖼️

## Step 7.4

Let's make our game more challenging by adding a timer. ⏱️

Create a `.timer` variable and add it under `initialize-game-elements`.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    60 >= .timer
    0 >= .gameOver
    ```

Create a `UI.Area` to draw our timer variable. Place it on the top left.

=== "Code Added"

    ```{.clojure .annotate linenums="1"}
    (UI.Area :Position (float2 40 20)
    :Anchor Anchor.TopLeft
    :Contents (->
    style (UI.Style)
    .timer (ToString) (UI.Label)))
    ```
Create a countdown logic.

Here, we subtract one from our `.timer` variable and `Step` into the loop every second.

Set a conditional statement to ensure that this only happens while `.gameOver` is false and `.timer` is greater than zero.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; -------- Timer -----------
    (defloop timer-countdown
      .gameOver
      (When :Predicate (->
                        (Is 0)
                        (And)
                        .timer (IsMore 0))
            :Action (->
                    .timer (Math.Subtract 1)
                    > .timer))

      (Pause 1.0))
    ```

Create the Game Over logic to dictate that `.gameOver` becomes true when `.timer` reaches zero.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; ---------- GameOver Logic ------------
    (defshards gameOver-logic []
      .timer
      (When :Predicate (Is 0)
            :Action (->
                    1 > .gameOver)))
    ```

Remember to call and `Step` them in the `main-wire`.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    (Step timer-countdown)
    (gameOver-logic)
    ```

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards initialize-character []
      (LoadImage "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadImage "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadImage "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) (Math.Subtract 1) >= .idle-image-index-max
      0.08 >= .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadImage "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) (Math.Subtract 1) >= .walking-image-index-max
      0 >= .walking-image-index
      0.08 >= .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMore .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMore .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadImage "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadImage "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) (Math.Subtract 1) >= .coin-image-index-max
      0 >= .coin-image-index
      0.1 >= .coin-animation-speed

      ;; ----- Coin 1 ------
      0.0 >= .coinx-1
      0.0 >= .coiny-1
      (float2 .coinx-1 .coiny-1) >= .coin-position-1
      0.0 >= .coin-velocity-1
      0.5 >= .coin-acceleration

      ;; ----- Coin 2 ----
      0.0 >= .coinx-2
      0.0 >= .coiny-2
      (float2 .coinx-2 .coiny-2) >= .coin-position-2
      0.0 >= .coin-velocity-2)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMore .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic [coiny coinx coin-velocity coin-position]

      coiny (Math.Add coin-velocity)
      > coiny

      coin-velocity (Math.Add .coin-acceleration)
      > coin-velocity

      (float2 coinx coiny) > coin-position)

    ;; ------------- Random Coin ------------------
    (defshards random-coin [coinx coiny coin-velocity coin-position pause-length]
      coinx
      (RandomFloat :Max 1200.0)
      > coinx
      (Math.Subtract 600.0)
      > coinx

      0.0 > coiny
      0.0 > coin-velocity
      (float2 coinx coiny) > coin-position
      (Pause pause-length))

    (defloop random-coin-1
      (random-coin .coinx-1 .coiny-1 .coin-velocity-1 .coin-position-1 1.5))

    (defloop random-coin-2
      (random-coin .coinx-2 .coiny-2 .coin-velocity-2 .coin-position-2 2.5))

    ;; ------------ Initialize Spiked CanonBalls ---------------
    (defshards initialize-spiked-canonballs []
      (LoadImage "GlodImages/SpikeBall/SpikeBall1.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall2.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall3.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall4.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall5.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall6.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall7.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall8.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall9.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall10.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall11.png") >> .spikeball-array
      (LoadImage "GlodImages/SpikeBall/SpikeBall12.png") >> .spikeball-array

      (Count .spikeball-array) (Math.Subtract 1) >= .spikeball-array-index-max
      0 >= .spikeball-index
      0.06 >= .spikeball-animation-speed

      ;; ---------- spikball-1 -------------
      1.0 >= .spikeball-velocity-1
      0.0 >= .spikeball-y-1
      0.0 >= .spikeball-x-1
      (float2 .spikeball-x-1 .spikeball-y-1) >= .spikeball-position-1

      ;; ---------- spikeball-2 -------------
      1.0 >= .spikeball-velocity-2
      0.0 >= .spikeball-y-2
      0.0 >= .spikeball-x-2
      (float2 .spikeball-x-2 .spikeball-y-2) >= .spikeball-position-2

      ;; ---------- SpikeBall_3 -------------
      1.0 >= .spikeball-velocity-3
      0.0 >= .spikeball-y-3
      0.0 >= .spikeball-x-3
      (float2 .spikeball-x-3 .spikeball-y-3) >= .spikeball-position-3

      0.5 >= .spikeball-acceleration)

    ;;------------- Spiked CanonBall Animation -------------
    (defloop spiked-canonball-animation
      .spikeball-index (Math.Add 1)
      > .spikeball-index
      (When :Predicate (IsMore .spikeball-array-index-max)
            :Action (-> 0 > .spikeball-index))

      (Pause .spikeball-animation-speed))

    ;; ------------- SpikeBall_Gravity_Logic -------------
    (defshards spikeball-gravity-logic [spikeball-y spikeball-velocity spikeball-position spikeball-x]
      spikeball-y (Math.Add spikeball-velocity)
      > spikeball-y
      spikeball-velocity (Math.Add .spikeball-acceleration)
      > spikeball-velocity
      (float2 spikeball-x spikeball-y) > spikeball-position)

    (defshards randomise-spikeball [spikeball-x spikeball-y spikeball-velocity spikeball-position pausefloat]
      spikeball-x
      (RandomFloat :Max 1200.0)
      > spikeball-x
      (Math.Subtract 600.0)
      > spikeball-x

      0.0 > spikeball-y
      0.0 > spikeball-velocity
      (float2 spikeball-x spikeball-y) > spikeball-position
      
      (Pause pausefloat))

    (defloop spikeball-1
      (randomise-spikeball .spikeball-x-1 .spikeball-y-1 .spikeball-velocity-1 .spikeball-position-1 2))
    (defloop spikeball-2
      (randomise-spikeball .spikeball-x-2 .spikeball-y-2 .spikeball-velocity-2 .spikeball-position-2 3))
    (defloop spikeball-3
      (randomise-spikeball .spikeball-x-3 .spikeball-y-3 .spikeball-velocity-3 .spikeball-position-3 4))


    ;; --------- Game Elements ------------
    (defshards initialize-game-elements []
      0 >= .score
      false >= .scored

      .X (Math.Add 50.0)
      >= .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .scoringLower-x-limit

      .Y (Math.Add 10.0)
      >= .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      >= .scoringLower-y-limit

      ;; ---------- Damage Limits ------------
      .X (Math.Add 50.0)
      >= .damageUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .damageLower-x-limit

      .Y (Math.Add 5.0)
      >= .damageUpper-y-limit
      .Y (Math.Subtract 5.0)
      >= .damageLower-y-limit

      false >= .damaged

      60 >= .timer
      0 >= .gameOver)

    ;; --------- Scoring ----------
    (defshards score-collision [coinx coiny]
      coinx
      (When :Predicate (->
                        (IsLess .scoringUpper-x-limit)
                        (And)
                        coinx (IsMore .scoringLower-x-limit)
                        (And)
                        coiny (IsLess .scoringUpper-y-limit)
                        (And)
                        coiny (IsMore .scoringLower-y-limit)
                        (And)
                        .scored (Is false))
            :Action (->
                    true > .scored
                    (Log "Score: "))))

    (defshards scoring []
      .X (Math.Add 50.0)
      > .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      > .scoringLower-x-limit

      .Y (Math.Add 10.0)
      > .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      > .scoringLower-y-limit

      (score-collision .coinx-1 .coiny-1)
      (score-collision .coinx-2 .coiny-2)

      .scored
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Add 1)
                    > .score
                    true > .score-effect-play
                    false > .scored)))

    ;; ------------- spikeBall-collision-logic --------------
    (defshards spikeBall-collision-logic [spikeBall-x spikeBall-y]

      spikeBall-x
      (If :Predicate (-> (IsLess .damageUpper-x-limit)
                        (And)
                        spikeBall-x (IsMore .damageLower-x-limit)
                        (And)
                        spikeBall-y (IsLess .damageUpper-y-limit)
                        (And)
                        spikeBall-y (IsMore .damageLower-y-limit))

          :Then (-> .damaged
                    (When :Predicate (Is false)
                          :Action (->
                                  true > .damaged
                                  (Log "damaged: "))))))

    ;; -------------- Damaging --------------
    (defshards damaging []

      .X (Math.Add 120.0)
      > .damageUpper-x-limit
      .X (Math.Subtract 120.0)
      > .damageLower-x-limit

      .Y (Math.Add 15.0)
      > .damageUpper-y-limit
      .Y (Math.Subtract 15.0)
      > .damageLower-y-limit

      (spikeBall-collision-logic .spikeball-x-1 .spikeball-y-1)
      (spikeBall-collision-logic .spikeball-x-2 .spikeball-y-2)
      (spikeBall-collision-logic .spikeball-x-3 .spikeball-y-3)

      .damaged
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Subtract 1)
                    > .score
                    true > .damage-effect-play
                    false > .damaged)))


    ;; ------------- innitialize effects -------------
    (defshards initialize-effects []
      ;; --------------- Animation Effects -----------------
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_1.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_2.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_3.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_4.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_5.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_6.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_7.png") >> .score-effect-array
      (LoadImage "GlodImages/Coin/CoinEffect/Score_Effect_8.png") >> .score-effect-array
      (Count .score-effect-array) (Math.Subtract 1) >= .score-effect-array-index-max
      0 >= .score-effect-array-index
      0.02 >=  .score-effect-animation-speed ;; Reduce number to increase animation speed
      false >= .score-effect-play

      0.0 >= .score-effect-position-x
      0.0 >= .score-effect-position-y
      (float2 .score-effect-position-x .score-effect-position-y) >= .score-effect-position

      ;; --------------- Damaged Effect ----------------
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_1.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_2.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_3.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_4.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_5.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_6.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_7.png") >> .damage-effect-array
      (LoadImage "GlodImages/Damage_Effect/Damaged_Effect_8.png") >> .damage-effect-array
      (Count .damage-effect-array) (Math.Subtract 1) >= .damage-effect-array-index-max
      0 >= .damage-effect-array-index
      0.02 >=  .damage-effect-animation-speed ;; Reduce number to increase animation speed
      false >= .damage-effect-play
      
      (LoadImage "GlodImages/BG.png") = .bg-image)

    ;; ------------ ScoreEffect Animation Position ------------
    (defshards scoreEffect-animation-position []
      .Y (Math.Add -15.0)
      > .score-effect-position-y

      .X
      > .score-effect-position-x

      (float2 .score-effect-position-x .score-effect-position-y)
      > .score-effect-position)

    ;; ------------ ScoreEffect Animation ------------
    (defloop scoreEffect-animation-logic

      .score-effect-play
      (When :Predicate (Is true)
            :Action (->
                    .score-effect-array-index (Math.Add 1)
                    > .score-effect-array-index
                    (When :Predicate (IsMore .score-effect-array-index-max)
                          :Action (->
                                    0 > .score-effect-array-index
                                    false > .score-effect-play))))


      (Pause .score-effect-animation-speed))

    ;; ------------ DamageEffect Animation ------------
    (defloop damage-effect-animation-logic

      .damage-effect-play
      (When :Predicate (Is true)
            :Action (->
                    .damage-effect-array-index (Math.Add 1)
                    > .damage-effect-array-index
                    (When :Predicate (IsMore .damage-effect-array-index-max)
                          :Action (->
                                    0 > .damage-effect-array-index
                                    false > .damage-effect-play))))


      (Pause .damage-effect-animation-speed))

    ;; -------- Timer -----------
    (defloop timer-countdown
      .gameOver
      (When :Predicate (->
                        (Is 0)
                        (And)
                        .timer (IsMore 0))
            :Action (->
                    .timer (Math.Subtract 1)
                    > .timer))

      (Pause 1.0))

    ;; ---------- GameOver Logic ------------
    (defshards gameOver-logic []
      .timer
      (When :Predicate (Is 0)
            :Action (->
                    1 > .gameOver)))


    ;; ------ UI Style --------
    (def style
      {:override_text_style "MyStyle"
      :text_styles
      [{:name "MyStyle"
        :size (float 46)
        :family "Monospace"}]
      :visuals
      {:override_text_color (color 250 250 250)}})

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin)
      (initialize-game-elements)
      (initialize-spiked-canonballs)
      (initialize-effects))

      (coin-gravity-logic .coiny-1 .coinx-1 .coin-velocity-1 .coin-position-1)
      (coin-gravity-logic .coiny-2 .coinx-2 .coin-velocity-2 .coin-position-2)

      (run-logic)
      (gravity-logic)
      (scoring)
      (damaging)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)
      (Step random-coin-1)
      (Step random-coin-2)

      (Step spiked-canonball-animation)
      (spikeball-gravity-logic .spikeball-y-1 .spikeball-velocity-1 .spikeball-position-1 .spikeball-x-1)
      (spikeball-gravity-logic .spikeball-y-2 .spikeball-velocity-2 .spikeball-position-2 .spikeball-x-2)
      (spikeball-gravity-logic .spikeball-y-3 .spikeball-velocity-3 .spikeball-position-3 .spikeball-x-3)
      (Step  spikeball-1)
      (Step  spikeball-2)
      (Step  spikeball-3)

      (scoreEffect-animation-position)
      (Step scoreEffect-animation-logic)
      (Step damage-effect-animation-logic)
      
      (Step timer-countdown)
      (gameOver-logic)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->

            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.TopLeft
                      :Contents (->
                                .bg-image (UI.Image :Scale (float2 0.7))))

            (UI.Area :Position .character-position
                      :Anchor Anchor.Top
                      :Contents (->
                                .character-state
                                (Match [0 (-> .character-direction
                                              (Match [0 (-> LoadTexture .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                      1 (-> LoadTexture .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))
                                        1 (-> LoadTexture .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        2 (-> LoadTexture .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                        3 (->  .character-direction
                                                (Match [0 (-> LoadTexture .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                        1 (-> LoadTexture .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                      :Passthrough false))]
                                        :Passthrough false)))

            ;; -------- coins ui area -----------

            (UI.Area :Position .coin-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            (UI.Area :Position .coin-position-2
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))
            ;; --------- spikeball ui area ----------

            (UI.Area :Position .spikeball-position-1
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            (UI.Area :Position .spikeball-position-2
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            (UI.Area :Position .spikeball-position-3
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            ;; ----------------- Visual Effects  -------------------
            (UI.Area :Position .score-effect-position
                      :Anchor Anchor.Top
                      :Contents (->
                                LoadTexture .score-effect-array (Take .score-effect-array-index) (UI.Image :Scale (float2 0.15))))

            (UI.Area :Position (float2 0 0)
                      :Anchor Anchor.TopLeft
                      :Contents (->
                                LoadTexture .damage-effect-array (Take .damage-effect-array-index) (UI.Image :Scale (float2 10))))

            ;; ---------------- UI ------------------

            (UI.Area :Position (float2 -40 20)
                      :Anchor Anchor.TopRight
                      :Contents (->
                                style (UI.Style)
                                .score (ToString) (UI.Label)))

            (UI.Area :Position (float2 40 20)
                      :Anchor Anchor.TopLeft
                      :Contents (->
                                style (UI.Style)
                                .timer (ToString) (UI.Label)))))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

## Step  7.5

Finally its time to code the final bit for our game. We need to add some finality to our game by having it end when our Timer reaches 0. Then we also need to create a way to reset the game so that we can play it again. While this might seem daunting, don't worry! It's much easier than you might think.

=== "Code Added"
    
    ```{.clojure .annotate linenums="1"}
    ;; -------- Game_Over_UI -------------
    (defloop gameOver-ui
      (UI.Area :Position (float2 -40 20)
              :Anchor Anchor.Center
              :Contents (->
                          style (UI.Style)
                          "Score" (UI.Label)
                          .score (ToString) (UI.Label)
                          (UI.Button :Label "Restart"
                                    :Action (->
                                              0 > .gameOver
                                              60 > .timer
                                              0 > .score
                                              (float2 0 0) > .character-position)))))
    ```

    1. First we create a defloop with a `UI.Area` that houses everything we want to show when the game is over. In this case, we want to show our player's score and then a reset button. In the reset Button's `:Action` tag, we change variables that have changed to revert the game back to it's initial state.

    ```{.clojure .annotate linenums="1"}
    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position) ;; (1)
    ```

    1. Next we create the `run-logic`. When `.character-x-velocity` is changed, it will be added to `.X` and the `.character-position` will be updated accordingly Added on lines 77-82

    ```{.clojure .annotate linenums="1"}
    (defloop mainGame-ui
      (UI.Area :Position (float2 0 0)
              :Anchor Anchor.TopLeft
              :Contents (->
                          .bg-image (UI.Image :Scale (float2 0.7))))

      (UI.Area :Position .character-position
              :Anchor Anchor.Top
              :Contents (->
                          .character-state
                          (Match [0 (-> .character-direction
                                        (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                              :Passthrough false))
                                  1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                  2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                  3 (->  .character-direction
                                        (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                :Passthrough false))]
                                :Passthrough false)))

            ;; ---------- Coins -----------

      (UI.Area :Position .coin-position-1
              :Anchor Anchor.Top
              :Contents (->
                          .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

      (UI.Area :Position .coin-position-2
              :Anchor Anchor.Top
              :Contents (->
                          .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            ;; ------------SpikeBalls ------------

      (UI.Area :Position .spikeball-position-1
              :Anchor Anchor.Top
              :Contents (->
                          .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

      (UI.Area :Position .spikeball-position-2
              :Anchor Anchor.Top
              :Contents (->
                          .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

      (UI.Area :Position .spikeball-position-3
              :Anchor Anchor.Top
              :Contents (->
                          .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            ;; ----------------- Visual Effects  -------------------
      (UI.Area :Position .score-effect-position
              :Anchor Anchor.Top
              :Contents (->
                          .score-effect-array (Take .score-effect-array-index) (UI.Image :Scale (float2 0.15))))

      (UI.Area :Position (float2 0 0)
              :Anchor Anchor.TopLeft
              :Contents (->
                          .damage-effect-array (Take .damage-effect-array-index) (UI.Image :Scale (float2 10))))

            ;; --------------- UI Score --------------

      (UI.Area :Position (float2 -40 20)
              :Anchor Anchor.TopRight
              :Contents (->
                          style (UI.Style)
                          .score (ToString) (UI.Label)))

            ;; --------------- UI Timer --------------
      (UI.Area :Position (float2 40 20)
              :Anchor Anchor.TopLeft
              :Contents (->
                          style (UI.Style)
                          .timer (ToString) (UI.Label))))
    ```

    > Next we shift all the UI.Area that we have created so far in main-wire to a new defloop called mainGame-ui. Now if yout ry to run your game now you will get an error as there in nothing in your main-wire to be drawn. Don't panic! All will be clear soon.

    ```{.clojure .annotate linenums="1"}
    (UI
            .ui-draw-queue
            (->
            .gameOver
            (Match [0 (-> (Step mainGame-ui))
                    1 (-> (Step gameOver-ui))]
                    :Passthrough false)))
    ```

    > Now inside our `main-wire` UI, we create a `Match` shard. If `.gameOver` is 0 (false), then draw the `mainGame-ui`. If `.gameOver` is 1(true) then draw the `gameOver-ui.`

=== "Full Code So Far"
    
    ```{.clojure .annotate linenums="1"}
    (defshards LoadTexture [name]
      (LoadImage name)
      (GFX.Texture))

    (defshards initialize-character []
      (LoadTexture "GlodImages/Character1_Jumping_Left.png") = .character-jumping-left
      (LoadTexture "GlodImages/Character1_Jumping_Right.png") = .character-jumping-right

      0 >= .character-state
      0 >= .character-direction
      true >= .can-jump

      0.0 >= .X
      620.0 >= .Y
      (float2 .X .Y) >= .character-position
      0.0 >= .character-x-velocity
      0.0 >= .character-y-velocity
      0.0 >= .character-y-acceleration

      ;; ---------- Character Idle Array (Facing Left) ----------
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_1.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_2.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_3.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_4.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_5.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_6.png") >> .idle-left-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Left/Character1_Idle_Left_7.png") >> .idle-left-image-array

      ;; ---------- Character Idle Array (Facing Right) ----------------
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_1.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_2.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_3.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_4.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_5.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_6.png") >> .idle-right-image-array
      (LoadTexture "GlodImages/Character_Idle/Idle_Right/Character1_Idle_7.png") >> .idle-right-image-array

      0 >= .idle-image-index
      (Count .idle-left-image-array) = .idle-image-index-max
      0.08 = .idle-animation-speed

      ;; -------------- Walking Array (Facing Left) -----------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_1.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_2.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_3.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_4.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_5.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_6.png") >> .walking-left-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Left/Character1_Walking_Left_7.png") >> .walking-left-image-array

      ;; ----------- Walking Array (Facing Right) ---------------
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_1.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_2.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_3.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_4.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_5.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_6.png") >> .walking-right-image-array
      (LoadTexture "GlodImages/Character_Walking/Walking_Right/Character1_Walking_Right_7.png") >> .walking-right-image-array

      (Count .walking-left-image-array) = .walking-image-index-max
      0 >= .walking-image-index
      0.08 = .walking-animation-speed) ;; Reduce number to increase animation speed

    ;; --------- Idle Animation Loop ---------
    (defloop idle-animation
      .idle-image-index (Math.Add 1)
      > .idle-image-index
      (When :Predicate (IsMoreEqual .idle-image-index-max)
            :Action (-> 0 > .idle-image-index))
      (Pause .idle-animation-speed))

    ;; -------- Walking Animation Loop --------
    (defloop walking-animation
      .walking-image-index (Math.Add 1)
      > .walking-image-index
      (When :Predicate (IsMoreEqual .walking-image-index-max)
            :Action (-> 0 > .walking-image-index))
      (Pause .walking-animation-speed))

    ;; ---------- character-boundary ------------

    (defshards clamp [var min max]
      var (Max min) (Min max) > var)

    ;; ------------ Character Run Logic ----------------
    (defshards run-logic []
      .X (Math.Add .character-x-velocity)
      > .X

      (float2 .X .Y) > .character-position

      (clamp .X -600.0 600.0))

    ;; ------------ Character gravity-logic ---------------
    (defshards gravity-logic []
      .Y (Math.Add .character-y-velocity)
      > .Y

      .character-y-velocity (Math.Add .character-y-acceleration)
      > .character-y-velocity

      (float2 .X .Y) > .character-position

      (clamp .Y -620.0 620.0)
      .Y
      (When :Predicate (IsMoreEqual 620.0)
            :Action (->
                    0.0 > .character-y-velocity
                    0.0 > .character-y-acceleration
                    true > .can-jump
                    .character-state
                    (When :Predicate (Is 3)
                          :Action (->
                                    0 > .character-state)))))

    ;; ------- Button Inputs ----------
    (defshards button-inputs []
      (Inputs.KeyDown
      :Key "left"
      :Action (->
                (Msg "left")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 1 > .character-state))

                0 > .character-direction
                -5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "right"
      :Action (->
                (Msg "right")

                .character-state
                (When :Predicate (Is 0)
                      :Action (-> 2 > .character-state))
                1 > .character-direction
                5.0 > .character-x-velocity))

      (Inputs.KeyDown
      :Key "up"
      :Action (->
                (Msg "up")
                3 > .character-state
                .can-jump
                (When :Predicate (Is true)
                      :Action (->
                              -20.0 > .character-y-velocity
                              1.0 >  .character-y-acceleration
                              false >= .can-jump))))

      (Inputs.KeyUp
      :Key "left"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity))

      (Inputs.KeyUp
      :Key "right"
      :Action (->
                0 > .character-state
                0.0 > .character-x-velocity)))

    ;; -------------- Initialize Coin ----------
    (defshards initialize-coin []
      (LoadTexture "GlodImages/Coin/Coin_1.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_2.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_3.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_4.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_5.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_6.png") >> .coin-image-array
      (LoadTexture "GlodImages/Coin/Coin_7.png") >> .coin-image-array
      (Count .coin-image-array) = .coin-image-index-max
      0 >= .coin-image-index
      0.1 = .coin-animation-speed

      ;; ----- Coin 1 ------
      0.0 >= .coinx-1
      0.0 >= .coiny-1
      (float2 .coinx-1 .coiny-1) >= .coin-position-1
      0.0 >= .coin-velocity-1
      0.5 >= .coin-acceleration

      ;; ----- Coin 2 ----
      0.0 >= .coinx-2
      0.0 >= .coiny-2
      (float2 .coinx-2 .coiny-2) >= .coin-position-2
      0.0 >= .coin-velocity-2)

    ;; -------------- Coin Animation ------------------
    (defloop coin-animation
      .coin-image-index (Math.Add 1)
      > .coin-image-index
      (When :Predicate (IsMoreEqual .coin-image-index-max)
            :Action (-> 0 > .coin-image-index))

      (Pause .coin-animation-speed))

    ;; ------------- Coin Gravity ------------------
    (defshards coin-gravity-logic [coiny coinx coin-velocity coin-position]

      coiny (Math.Add coin-velocity)
      > coiny

      coin-velocity (Math.Add .coin-acceleration)
      > coin-velocity

      (float2 coinx coiny) > coin-position)

    ;; ------------- Random Coin ------------------
    (defshards random-coin [coinx coiny coin-velocity coin-position pause-length]
      coinx
      (RandomFloat :Max 1200.0)
      > coinx
      (Math.Subtract 600.0)
      > coinx

      0.0 > coiny
      0.0 > coin-velocity
      (float2 coinx coiny) > coin-position
      (Pause pause-length))

    (defloop random-coin-1
      (random-coin .coinx-1 .coiny-1 .coin-velocity-1 .coin-position-1 1.5))

    (defloop random-coin-2
      (random-coin .coinx-2 .coiny-2 .coin-velocity-2 .coin-position-2 2.5))

    ;; ------------ Initialize Spiked CanonBalls ---------------
    (defshards initialize-spiked-canonballs []
      (LoadTexture "GlodImages/SpikeBall/SpikeBall1.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall2.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall3.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall4.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall5.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall6.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall7.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall8.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall9.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall10.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall11.png") >> .spikeball-array
      (LoadTexture "GlodImages/SpikeBall/SpikeBall12.png") >> .spikeball-array

      (Count .spikeball-array) = .spikeball-array-index-max
      0 >= .spikeball-index
      0.06 = .spikeball-animation-speed

      ;; ---------- spikball-1 -------------
      1.0 >= .spikeball-velocity-1
      0.0 >= .spikeball-y-1
      0.0 >= .spikeball-x-1
      (float2 .spikeball-x-1 .spikeball-y-1) >= .spikeball-position-1

      ;; ---------- spikeball-2 -------------
      1.0 >= .spikeball-velocity-2
      0.0 >= .spikeball-y-2
      0.0 >= .spikeball-x-2
      (float2 .spikeball-x-2 .spikeball-y-2) >= .spikeball-position-2

    ;; ---------- SpikeBall_3 -------------
      1.0 >= .spikeball-velocity-3
      0.0 >= .spikeball-y-3
      0.0 >= .spikeball-x-3
      (float2 .spikeball-x-3 .spikeball-y-3) >= .spikeball-position-3

      0.5 >= .spikeball-acceleration)

    ;;------------- Spiked CanonBall Animation -------------
    (defloop spiked-canonball-animation
      .spikeball-index (Math.Add 1)
      > .spikeball-index
      (When :Predicate (IsMoreEqual .spikeball-array-index-max)
            :Action (-> 0 > .spikeball-index))

      (Pause .spikeball-animation-speed))

    ;; ------------- SpikeBall_Gravity_Logic -------------
    (defshards spikeball-gravity-logic [spikeball-y spikeball-velocity spikeball-position spikeball-x]
      spikeball-y (Math.Add spikeball-velocity)
      > spikeball-y
      spikeball-velocity (Math.Add .spikeball-acceleration)
      > spikeball-velocity
      (float2 spikeball-x spikeball-y) > spikeball-position)

    ;; ------------- Randomise Spikeball ----------------
    (defshards randomise-spikeball [spikeball-x spikeball-y spikeball-velocity spikeball-position pausefloat]
      spikeball-x
      (RandomFloat :Max 1200.0)
      > spikeball-x
      (Math.Subtract 600.0)
      > spikeball-x

      0.0 > spikeball-y
      0.0 > spikeball-velocity
      (float2 spikeball-x spikeball-y) > spikeball-position
      
      (Pause pausefloat))

    (defloop spikeball-1
      (randomise-spikeball .spikeball-x-1 .spikeball-y-1 .spikeball-velocity-1 .spikeball-position-1 2))
    (defloop spikeball-2
      (randomise-spikeball .spikeball-x-2 .spikeball-y-2 .spikeball-velocity-2 .spikeball-position-2 3))
    (defloop spikeball-3
      (randomise-spikeball .spikeball-x-3 .spikeball-y-3 .spikeball-velocity-3 .spikeball-position-3 4))

    ;; --------- Game Elements ------------
    (defshards initialize-game-elements []
      ;;------------ Scoring Limits ----------
      0 >= .score
      false >= .scored

      .X (Math.Add 50.0)
      >= .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .scoringLower-x-limit

      .Y (Math.Add 10.0)
      >= .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      >= .scoringLower-y-limit

      ;; ---------- Damage Limits ------------
      .X (Math.Add 50.0)
      >= .damageUpper-x-limit
      .X (Math.Subtract 50.0)
      >= .damageLower-x-limit

      .Y (Math.Add 5.0)
      >= .damageUpper-y-limit
      .Y (Math.Subtract 5.0)
      >= .damageLower-y-limit

      false >= .damaged

      60 >= .timer
      0 >= .gameOver)

    ;; --------- Scoring ----------
    (defshards score-collision [coinx coiny]
      coinx
      (When :Predicate (->
                        (IsLess .scoringUpper-x-limit)
                        (And)
                        coinx (IsMore .scoringLower-x-limit)
                        (And)
                        coiny (IsLess .scoringUpper-y-limit)
                        (And)
                        coiny (IsMore .scoringLower-y-limit)
                        (And)
                        .scored (Is false))
            :Action (->
                    true > .scored
                    (Log "Score: "))))

    (defshards scoring []
      .X (Math.Add 50.0)
      > .scoringUpper-x-limit
      .X (Math.Subtract 50.0)
      > .scoringLower-x-limit

      .Y (Math.Add 10.0)
      > .scoringUpper-y-limit
      .Y (Math.Subtract 10.0)
      > .scoringLower-y-limit

      (score-collision .coinx-1 .coiny-1)
      (score-collision .coinx-2 .coiny-2)

      .scored
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Add 1)
                    > .score
                    true > .score-effect-play
                    false > .scored)))

    ;; ------------- spikeBall-collision-logic --------------
    (defshards spikeBall-collision-logic [spikeBall-x spikeBall-y]

      spikeBall-x
      (If :Predicate (-> (IsLess .damageUpper-x-limit)
                        (And)
                        spikeBall-x (IsMore .damageLower-x-limit)
                        (And)
                        spikeBall-y (IsLess .damageUpper-y-limit)
                        (And)
                        spikeBall-y (IsMore .damageLower-y-limit))

          :Then (-> .damaged
                    (When :Predicate (Is false)
                          :Action (->
                                  true > .damaged
                                  (Log "damaged: "))))))

    ;; -------------- Damaging --------------
    (defshards damaging []

      .X (Math.Add 120.0)
      > .damageUpper-x-limit
      .X (Math.Subtract 120.0)
      > .damageLower-x-limit

      .Y (Math.Add 15.0)
      > .damageUpper-y-limit
      .Y (Math.Subtract 15.0)
      > .damageLower-y-limit

      (spikeBall-collision-logic .spikeball-x-1 .spikeball-y-1)
      (spikeBall-collision-logic .spikeball-x-2 .spikeball-y-2)
      (spikeBall-collision-logic .spikeball-x-3 .spikeball-y-3)

      .damaged
      (When :Predicate (Is true)
            :Action (->
                    .score (Math.Subtract 1)
                    > .score
                    true > .damage-effect-play
                    false > .damaged)))

    ;; ------------- initialize effects -------------
    (defshards initialize-effects []
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_1.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_2.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_3.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_4.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_5.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_6.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_7.png") >> .score-effect-array
      (LoadTexture "GlodImages/Coin/CoinEffect/Score_Effect_8.png") >> .score-effect-array
      (Count .score-effect-array) (Math.Subtract 1) >= .score-effect-array-index-max
      0 >= .score-effect-array-index
      0.05 >=  .score-effect-animation-speed ;; Reduce number to increase animation speed
      false >= .score-effect-play

      0.0 >= .score-effect-position-x
      0.0 >= .score-effect-position-y
      (float2 .score-effect-position-x .score-effect-position-y) >= .score-effect-position

      ;; --------------- Damaged Effect ----------------
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_1.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_2.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_3.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_4.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_5.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_6.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_7.png") >> .damage-effect-array
      (LoadTexture "GlodImages/Damage_Effect/Damaged_Effect_8.png") >> .damage-effect-array
      (Count .damage-effect-array) (Math.Subtract 1) >= .damage-effect-array-index-max
      0 >= .damage-effect-array-index
      0.02 >=  .damage-effect-animation-speed ;; Reduce number to increase animation speed
      false >= .damage-effect-play
      
      (LoadTexture "GlodImages/BG.png") = .bg-image)

    ;; ------------ DamageEffect Animation ------------
    (defloop damage-effect-animation-logic

      .damage-effect-play
      (When :Predicate (Is true)
            :Action (->
                    .damage-effect-array-index (Math.Add 1)
                    > .damage-effect-array-index
                    (When :Predicate (IsMore .damage-effect-array-index-max)
                          :Action (->
                                    0 > .damage-effect-array-index
                                    false > .damage-effect-play))))

      (Pause .damage-effect-animation-speed))

    ;; ------------ ScoreEffect_Animation_Position ------------
    (defshards scoreEffect-animation-position []
      .Y (Math.Add -15.0)
      > .score-effect-position-y

      .X
      > .score-effect-position-x

      (float2 .score-effect-position-x .score-effect-position-y)
      > .score-effect-position)

    ;; ------------ ScoreEffect Animation ------------
    (defloop scoreEffect-animation-logic

      .score-effect-play
      (When :Predicate (Is true)
            :Action (->
                    .score-effect-array-index (Math.Add 1)
                    > .score-effect-array-index
                    (When :Predicate (IsMore .score-effect-array-index-max)
                          :Action (->
                                    0 > .score-effect-array-index
                                    false > .score-effect-play))))


      (Pause .score-effect-animation-speed))

    ;; -------- Timer -----------
    (defloop timer-countdown
      .gameOver
      (When :Predicate (->
                        (Is 0)
                        (And)
                        .timer (IsMore 0))
            :Action (->
                    .timer (Math.Subtract 1)
                    > .timer))

      (Pause 1.0))

    ;; ---------- GameOver Logic ------------
    (defshards gameOver-logic []
      .timer
      (When :Predicate (Is 0)
            :Action (->
                    1 > .gameOver)))

    ;; ------ UI Style --------
    (def style
      {:override_text_style "MyStyle"
      :text_styles
      [{:name "MyStyle"
        :size (float 46)
        :family "Monospace"}]
      :visuals
      {:override_text_color (color 250 250 250)}})

    ;; -------- Game_Over_UI -------------
    (defloop gameOver-ui
      (UI.Area :Position (float2 -40 20)
              :Anchor Anchor.Center
              :Contents (->
                          style (UI.Style)
                          "Score" (UI.Label)
                          .score (ToString) (UI.Label)
                          (UI.Button :Label "Restart"
                                    :Action (->
                                              0 > .gameOver
                                              60 > .timer
                                              0 > .score
                                              (float2 0 0) > .character-position)))))

    (defloop mainGame-ui
      (UI.Area :Position (float2 0 0)
              :Anchor Anchor.TopLeft
              :Contents (->
                          .bg-image (UI.Image :Scale (float2 0.7))))

      (UI.Area :Position .character-position
              :Anchor Anchor.Top
              :Contents (->
                          .character-state
                          (Match [0 (-> .character-direction
                                        (Match [0 (-> .idle-left-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))
                                                1 (-> .idle-right-image-array (Take .idle-image-index) (UI.Image :Scale (float2 0.2)))]
                                              :Passthrough false))
                                  1 (-> .walking-left-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                  2 (-> .walking-right-image-array (Take .walking-image-index) (UI.Image :Scale (float2 0.2)))
                                  3 (->  .character-direction
                                        (Match [0 (-> .character-jumping-left (UI.Image :Scale (float2 0.2)))
                                                1 (-> .character-jumping-right (UI.Image :Scale (float2 0.2)))]
                                                :Passthrough false))]
                                :Passthrough false)))

            ;; ---------- Coins -----------

      (UI.Area :Position .coin-position-1
              :Anchor Anchor.Top
              :Contents (->
                          .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

      (UI.Area :Position .coin-position-2
              :Anchor Anchor.Top
              :Contents (->
                          .coin-image-array (Take .coin-image-index) (UI.Image :Scale (float2 0.2))))

            ;; ------------SpikeBalls ------------

      (UI.Area :Position .spikeball-position-1
              :Anchor Anchor.Top
              :Contents (->
                          .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

      (UI.Area :Position .spikeball-position-2
              :Anchor Anchor.Top
              :Contents (->
                          .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

      (UI.Area :Position .spikeball-position-3
              :Anchor Anchor.Top
              :Contents (->
                          .spikeball-array (Take .spikeball-index) (UI.Image :Scale (float2 0.15))))

            ;; ----------------- Visual Effects  -------------------
      (UI.Area :Position .score-effect-position
              :Anchor Anchor.Top
              :Contents (->
                          .score-effect-array (Take .score-effect-array-index) (UI.Image :Scale (float2 0.15))))

      (UI.Area :Position (float2 0 0)
              :Anchor Anchor.TopLeft
              :Contents (->
                          .damage-effect-array (Take .damage-effect-array-index) (UI.Image :Scale (float2 10))))

            ;; --------------- UI Score --------------

      (UI.Area :Position (float2 -40 20)
              :Anchor Anchor.TopRight
              :Contents (->
                          style (UI.Style)
                          .score (ToString) (UI.Label)))

            ;; --------------- UI Timer --------------
      (UI.Area :Position (float2 40 20)
              :Anchor Anchor.TopLeft
              :Contents (->
                          style (UI.Style)
                          .timer (ToString) (UI.Label))))

    ;;---------- main-wire ------------
    (defloop main-wire
      (Setup
      (initialize-character)
      (initialize-coin)
      (initialize-game-elements)
      (initialize-spiked-canonballs)
      (initialize-effects))

      (coin-gravity-logic .coiny-1 .coinx-1 .coin-velocity-1 .coin-position-1)
      (coin-gravity-logic .coiny-2 .coinx-2 .coin-velocity-2 .coin-position-2)

      (run-logic)
      (gravity-logic)
      (scoring)
      (damaging)

      (Step idle-animation)
      (Step walking-animation)

      (Step coin-animation)
      (Step random-coin-1)
      (Step random-coin-2)

      (Step spiked-canonball-animation)
      (spikeball-gravity-logic .spikeball-y-1 .spikeball-velocity-1 .spikeball-position-1 .spikeball-x-1)
      (spikeball-gravity-logic .spikeball-y-2 .spikeball-velocity-2 .spikeball-position-2 .spikeball-x-2)
      (spikeball-gravity-logic .spikeball-y-3 .spikeball-velocity-3 .spikeball-position-3 .spikeball-x-3)
      (Step  spikeball-1)
      (Step  spikeball-2)
      (Step  spikeball-3)

      (scoreEffect-animation-position)
      (Step scoreEffect-animation-logic)
      (Step damage-effect-animation-logic)

      (Step timer-countdown)
      (gameOver-logic)

      (GFX.MainWindow
      :Title "MainWindow" :Width 1920 :Height 1080
      :Contents
      (-> (Setup
            (GFX.DrawQueue) >= .ui-draw-queue
            (GFX.UIPass .ui-draw-queue) >> .render-steps)
          .ui-draw-queue (GFX.ClearQueue)

          (UI
            .ui-draw-queue
            (->
            .gameOver
            (Match [0 (-> (Step mainGame-ui))
                    1 (-> (Step gameOver-ui))]
                    :Passthrough false)))

          (button-inputs)

          (GFX.Render :Steps .render-steps))))


    (defmesh main)
    (schedule main main-wire)
    (run main (/ 1.0 60))
    ```

CONGRATULATIONS! 🎉🎉🎉🎉🎉🎉

You have successfully created a game!

Give it a whirl and see how many points you can get!
