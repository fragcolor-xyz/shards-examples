# Appendix D - Mal/EDN

Functions are native Mal/EDN language entities that are processed only once - during the [compose phase](#compose).

Most function names are written in [kebab case](https://en.wiktionary.org/wiki/kebab_case) (like `(hash-map)`) and are rarely needed in your average Shards program (except for [macros](https://docs.fragcolor.xyz/functions/macros/) which are used extensively in every Shards program).

 These functions ith the exeption the macros (`(def)`, `(defwire)`, `(defloop)`, `(defshards)`, `(schedule)`, `(run)`) 

## Anatomy of a function

As we saw in the [What is code?](../introduction/#what-is-code) section, functions are built-in chunks of transformation logic that can each process data in a particular manner.

### Input and output

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

### Composition with functions

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

### Data flow

The input is always a part of the function's body (within its parentheses). And the result (output) of the function is data that is either displayed in place (like with `(cons)`) or an action that has taken place on the input (like with printing to screen with `(prn)`).

Hence, in the case of functions data doesn't seem to flow in any particular direction.

### Data types

Data types work in the same way for functions as they do for shards. A fc

Types are important because they tell a shard or function what kind of data to expect/process. So if a shard receives a wrong data type it can immediately discard it and raise an error to the user to pass the correct data type. This helps in reducing runtime errors caused due to a shard or function trying to transform an incorrect data type.

f(g(x))

!!! note
    For the full list of supported Mal/EDN data types see [values](https://docs.fragcolor.xyz/functions/values/) and [collections](https://docs.fragcolor.xyz/functions/collections/).

### Creating functions
`(defn)`
`(def!)`
`(defmacro)`

### Handling variables
`(def)`

### Manipulating string data

### Manipulating numeric data

**Mathematical expressions**
Shards uses the [prefix notation](https://en.wikipedia.org/wiki/Polish_notation) (`(+ 3 4)`) for mathematical expressions, rather than more widely used infix notation (`(3 + 4)`).

In the prefix notation, the operators precede the operands while in the infix notation they are placed in between the operands. Here's a mathematical expression in both notations:
```{.clojure .annotate linenums="1"}
;; expression in prefix notation
(/ (- (* (+ 3 2) 4) 6) 2)               ;; evaluates to => 7

;; same expression in infix notation
((((3 + 2) * 4) - 6) / 2)               ;; evaluates to => 7
```

As you can see, in each bracketed expression that is evaluated, the prefix notation puts the operator first, followed by the two operands.
```{.clojure .annotate linenums="1"}
;; step-by-step evaluation in prefix notation
(/ (- (* (+ 3 2) 4) 6) 2)               ;; (+ 3 2) => like (3 + 2) => 5
(/ (- (* 5 4) 6) 2)                     ;; (* 5 4) => like (5 * 4) => 20
(/ (- 20 6) 2)                          ;; (- 20 6) => like (20 - 6) => 14
(/ 14 2)                                ;; (/ 14 2) => like (14 / 2) => 7
7
```

**Comparison operators**

Shards implements common mathematical [comparison operators](https://docs.fragcolor.xyz/functions/operators/) using the same prefix notation.

A few examples:
```{.clojure .annotate linenums="1"}
(> 4 2) (Log)                 ;; => true
(< 4 2) (Log)                 ;; => false
(= 5 (+ 3 2)) (Log)           ;; => true
(>= 9 (+ (- 10 2) 4)) (Log)   ;; => false
(<= (* 2 4) (/ 25 5)) (Log)   ;; => false
```

Here's some code demonstrating usage of these shards:

*Code example 18*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (prn (> 4 2))                  
    (prn (< 4 2) )                 
    (prn (= 5 (+ 3 2)))
    (prn (>= 9 (+ (- 10 2) 4)))           
    (prn (<= (* 2 4) (/ 25 5)))            
    ```
    
=== "Result"

    ```
    true
    false
    true
    false
    false
    ```


--8<-- "includes/license.md"
