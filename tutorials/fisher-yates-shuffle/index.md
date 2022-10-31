# The Fisher-Yates Shuffle

// TODO

=== "Code"
    
    ```clojure linenums="1" 
    (defwire fisher-yates-shuffle
      (ExpectSeq) = .sequence
      (Setup (int 0) >= .max-index)
      (Count .sequence) (Math.Subtract 1) > .max-index      ; -1 from the sequence size to get the max index number

      (Repeat
      (-> (RandomInt :Max .max-index) >= .random-index      ; Select an index of the sequence randomly, the last index will never be chosen as :Max is not inclusive
           .sequence
           (| (Take .random-index) &> .chosen-value)        ; Obtains the value stored at the random index of the sequence
           (| (Take .max-index) &> .last-value)             ; Obtains the last value in the sequence

                                                            ; Swaps the values in the last index of the sequence with the chosen index
           [.random-index .last-value] (Assoc .sequence)    ; Updates the random index selected with the last value of the sequence
           [.max-index .chosen-value] (Assoc .sequence)     ; Updates the last index of the sequence with the value of the index randomly chosen

           (Math.Dec .max-index)                            ; Decreases the max index value so that the shuffle will leave 
                                                            ; the newly swapped value of the current max index alone for the next iteration 
           )
       :Forever true
       :Until (-> .max-index (Is 0)))                       ; Loops until the there are no two values left to swap between

      .sequence)                                            ; Returns the newly shuffled sequence
    ```

--8<-- "includes/license.md"
