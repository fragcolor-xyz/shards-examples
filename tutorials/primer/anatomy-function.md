# Anatomy of a function

As we saw in the [What is code?](../introduction/#what-is-code) section, functions are built-in chunks of transformation logic that can each process data in a particular manner.

## Input and output

Functions take an input, do a computation, and produce an output. The whole function and its input must be enclosed in a set of parentheses.

For example:

* `(def)` is a function that can assign names/aliases to a data value
* [`(prn)`](https://docs.fragcolor.xyz/functions/standard-output/#prn) is a function that takes an input and prints its value on the screen

The following code example illustrates the usage of these functions.

*Code example 9*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    ;; inputs to `def`: alias `myvar`, data `123`
    ;; output of `def`: myvar = "my data"
    (def myvar 123)

    ;; input to `prn` : "Hello World"
    ;; output of `prn`: print input data => "Hello World"
    (prn "Hello World")

    ;; input to `prn` : alias `myvar` that has data `123`
    ;; output of `prn`: print alias' data => 123
    (prn myvar)
    ```
    
=== "Result"

    ```
    "Hello World"
    123  
    ```

!!! note
    In programming, *comments* are used to provide information and context about the code to a programmer. These comments are ignored by the computer (which processes only code). In Shards, comments start with a semicolon (`;`) and all subsequent characters in that line are ignored for processing.

## Composition with functions

An important aspect of functions is composition - passing one function's output into the other function as its input

Let's consider the function `(cons)` - it takes two inputs and gives one output:

* input1: a sequence of values (a vector), which could also be `nil` (null value) - like `[10 20 30]` or `nil`
* input2: an item to add to this sequence (goes to the front of the list) and create a new list (a collection of objects) - like `40`
* output: a new list combining items from the input vector with the input item (in case of a `nil` vector, the output is a single-item list consisting of just the solo input item)

But, since a function doesn't print its output out to the screen (or standard output) by default, we will use `prn` to see the output of `cons`.

And for that, we place the whole `cons` function body *in place of* the input of `(prn)`. This is function composition - `(prn (cons...))`. The following code example illustrates this function composition.

*Code example 10*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    ;; constructing a list from: [10 20 30] + 40
    (cons 40 [10 20 30])    ;; => ...
    ;; printing the `cons` output to screen by composing with `prn`
    (prn (cons 40 [10 20 30])) ;; => (40 10 20 30)

    ;; constructing a list from: nil + 40
    (cons 40 nil)
    ;; printing the `cons` output to screen by composing with `prn`
    (prn (cons 40 nil)) ;; => (40)

    ;; constructing a list by composing `cons` recursively on itself
    (cons 1 (cons 2 (cons 3 nil)))
    ;; printing the `cons` output to screen by composing with `prn`
    (prn (cons 1 (cons 2 (cons 3 nil)))) ;; => (1 2 3)
    ```
    
=== "Result"

    ```
    (40 10 20 30)
    (40)
    (1 2 3)
    ```

## Data flow

The input is always a part of the function's body (within its parentheses). And the result (output) of the function is data that is either displayed in its place (like with `(cons)`) or an action that has taken place on the input (like with printing to screen with `(prn)`).

Hence, in the case of functions data doesn't seem to flow in any particular direction.

--8<-- "includes/license.md"
