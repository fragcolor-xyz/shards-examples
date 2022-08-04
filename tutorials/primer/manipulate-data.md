# Manipulating data

Data manipulation is the sole reason for a computer program to exist. In this section, we take a look at how Shards code understands and manipulates data.

## Data types and values

Data comes in many forms. These are called *data types*. For example - *4* is a number and *"Hello there"* is a block of text, etc.

While a data type is a description of the kind of data under consideration, a *value* is an actual data value. These are also called *literals*. So, *4* is a literal and its data type is numeric.

Types are important because they tell a shard or function what kind of data to expect/process. So if a shard receives a wrong data type it can immediately discard it and raise an error to the user to pass the correct data type. This helps in reducing runtime errors caused due to a shard or function trying to transform an incorrect data type.

For example, imagine passing text to a shard that sums up numbers: it will not know what to do because mathematical summation does not work on text characters.

Let's look at some of these data types and literals.

!!! note
    In this section, we discuss only the common data types in Shards. For more see the complete list of supported data types for [shards](https://docs.fragcolor.xyz/shards/#valid-data-types-for-shards) and [functions](https://docs.fragcolor.xyz/functions/values/), including a few more called [collections](https://docs.fragcolor.xyz/functions/collections/). Some of the shard types are available only for internal use by shards (i.e., a programmer may not define and use them explicitly in their code).


```{.clojure .annotate linenums="1"}
;; named literal - denotes nill, nothing, none
nil                         ;; implicit declaration without keyword `Const`
(Const nil)                 ;; explicit declaration using keyword `Const`

;; named literals - boolean true, boolean false
true                        ;; denotes 'truth', evaluates to 1 in boolean logic (implicit declaration)
(Const true)                ;; explicit declaration using the Const keyword
false                       ;; denotes 'falsehood', evaluates to 0 in boolean logic (implicit declaration)
(Const nil)                 ;; explicit declaration using the Const keyword

;; string data - anything in double quotes
"Hello World"               ;; a string literal (implicit declaration without keyword `String`)
(String "a")                ;; single character (explicit declaration with keyword `String`)
(String "House No. #87")    ;; special characters, numbers, spaces
(String "Hello friends!")   ;; blocks of text

;; numeric data - integers (non-decimal numbers)
123                         ;; a single integer literal (implicit declaration without keyword `Int`)
(Int 1)                     ;; single integer (explicit declaration with keyword `Int`)                      
(Int2 10 20)                ;; group of 2 integers
(Int3 2 3 3 )               ;; group of 3 integers
(Int4 9 8 7 6)              ;; group of 4 integers

;; numeric data - floats (decimal numbers)
23.87                       ;; a single float literal (implicit declaration without keyword `Float`)
(Float 1.2)                 ;; single float (explicit declaration with keyword `Float`)
(Float2 2.2 45.1)           ;; group of 2 floats
(Float3 7.0 5.3 4.0)        ;; group of 3 floats
(Float4 7.8 7.5 9.9 10.0)   ;; group of 4 floats

;; a sequence literal (values can be other/nested data types)
[1 "world" 4 ["very" 5] 78]

;; a table literal (values can be other/nested data types)
{:k1 "value1" :key2 ["v" "a" "l" 3] "key3" 450}

;; a color literal (special data type with RGBA channel values)
(Color 100 120 255 0)
```

There are a few values (literals) that are used so frequently (by both the programmer as well as the computer) that they have their own names - like `nil`, `true`, `false`, etc. These are called named literals and their names cannot be used to identify any other value.

`nil` represents null/nothing/none in Shards. That means you can use this keyword to indicate a null input or a null result etc. Similarly, the boolean literals - `true`, `false` - represent the truth (`true`) or falsehood (`false`) of an assertion or a conditional check, or an expression evaluation. Since these literals are named as well as fixed in value - they are internally handled as constants (i.e., using the `Const` keyword). While declaring these named literals, you may choose to drop the `Const` keyword (implicit declaration) or use it (explicit declaration). Both methods are equivalent but explicit declaration is recommended. We'll dig more into these literals and how to use them in later sections.

Just like the literal names, data type names are (like `String`/`string`) reserved too. They cannot be used to identify anything other than the type of data. 

A [`string`] (https://docs.fragcolor.xyz/shards/#string) (or its pascal case alias `String`) data type is essentially anything wrapped in double quotes. This is useful for storing blocks of descriptive text or messages.

Numbers come in two types - integers and floating-point numbers. Use integers when you do not want decimals and floats when higher precision is required. [`int`](https://docs.fragcolor.xyz/shards/#int) and [`float`](https://docs.fragcolor.xyz/shards/#float) are single numbers, while `int2`/`int3`/`int4` and `float2`/`float3`/`float4` are groups of numbers that are processed together (also called vectors). For example, in a pole-vaulting competition, a competitor's medal tally could be expressed as an `int`, their weight as a `float`, and the highest point they jumped to as a 3-vector `float3` (in `(xyz)` coordinates). All numeric data types have pascal case aliases. So `Int`/`Int2`,`Int3`/`Int4` and `Float`/`Float2`/`Float3`/`Float4` are valid type names too. 

A [`Sequence`](https://docs.fragcolor.xyz/shards/#seq) is an ordered list of items that can be accessed by their position or index within the structure (very similar to data type 'array' in other programming languages). The values are enclosed in parentheses and are *not* separated by commas. The items inside a sequence may be of different/nested types. For example, `[[3 4] 24 "down" (Float2 4.0 5.0)]` is a valid sequence.

A [`Table`](https://docs.fragcolor.xyz/shards/#table) is an unordered collection of values that can be accessed by specific keys which are also part of the table (very similar to the data type 'object' in other programming languages). The values in the table can be of different/nested types but the keys must be either prefixed with a `:` (a valid key: `:key2`) enclosed in double quotes (another valid key: `"abc"`). Also, tables must always have key/value pairs. There cannot be just a key or just a value. So, `{key24 "val24"}` is an invalid table (because the key is not named properly) and so is this `{:k1 "abc" :k2 :k3 "def"}` (because here the key `:k2` has no corresponding value).

A [`color`](https://docs.fragcolor.xyz/shards/#color) is a vector of 4 integers representing the RGBA values (in sequence) for that color. Each value can range from 0 to 255 (any value above 255 is subtracted from 256 to bring it back into 0 - 255 range, as many times as needed). The R, G, and B values represent the amount of red, green, and blue color components and the A represents the *alpha* channel or how opaque the color shade is (0 is fully transparent, 255 is fully opaque). `color` has a pascal case alias `Color`.

Similar to named literal declarations, you may choose to use declare certain data types - like `String`, `Int`, and `Float` - with the type keyword (explicit declaration) or without the type keyword (implicit declaration). Both methods are equivalent but explicit declaration is recommended.

## Variables and constants

As we saw in the last section - a data type is a description of the data while a literal is an actual data value.

It's straightforward to use literals directly:
```{.clojure .annotate linenums="1"}
9 (Math.Sqrt)       ;; takes literal 9 as input
(Log)               ;; outputs literal => 3
```

However, it's easier to manipulate them if you store them in a memory location, give that location a name, then refer to the value via that name. Think of the memory location as a box and the data as an item that can be stored in the box.

Just like you can swap out items from a box; you can change the data inside a memory location. Such a memory location is then called a *variable* or a *mutable variable*.

!!! note
    The initial variable declaration sets the data type of the variable. Once the data type is set you can mutate the variable only for the same data type values. So, if you created a variable by assigning an integer type data value to it, then you can only assign it integers from here on. Any attempt to assign a different data type value (say string) will give an error. 

If you put a restriction on the box that prevents you from changing the item inside it (like say locking the box), then such a memory location is called a *constant* or an *immutable variable*. Once set (i.e., during initial declaration) you cannot change the value of a constant.

In Shards, we can create mutable variables outside a wire/shard by using via `(def)`, and within a wire/shard by using [`(Set)`](https://docs.fragcolor.xyz/shards/General/Set/) (alias `>=`). Constants can be created within a wire/shard with a [`(Ref)`]() (alias `=`). Mutable variables can be updated with a different value by using `def`, or [`(Update)`](https://docs.fragcolor.xyz/shards/General/Update/) (alias `>`) again on them. To print the value of a variable you may explicitly log it by its name (using `prn` or `Log`) or use [`(Get)`](https://docs.fragcolor.xyz/shards/General/Get/) to read it from the variable.

!!! note
    1. By convention use `kebab-case` for naming variables, constants, user-defined shards, etc.
    2. All variables and constants names (declared with a wire/shard) should start with a period `.`.
    3. Reserved keywords (words that are already taken up by the programming language) cannot be used to name variables, constants, user-defined shards (using `defshards`, `defwires`, `defloop`, `defmesh`), and user-defined functions (using `defn`). All data type names, named literals, built-in shards names, and built-in function names are reserved keywords in Shards.

## Creating and updating variables

Let's see some real code examples of all that we have discussed in this section.

*Code example 16*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    ;; define, update, and log mutable variables outside a wire/shard
    (def extvar1 9)                     ;; define a variable and assign it a numeric value
    (prn extvar1)                       ;; print extvar1 value => 9

    (def extvar2 "Toy")                 ;; define another variable and assign it a string value
    (prn extvar2)                       ;; print extvar2 value => "Toy"

    (def extvar2 "Story")               ;; update existing variable with another string value
    (prn extvar2)                       ;; print updated extvar2 value => "Story"

    (defmesh root)

    (defwire mywire
    ;; access variables from outside the wire/shard
    extvar1 (Log)                       ;; print value of extvar1 => 9
    extvar2 (Log)                       ;; print updated value of extvar2 => "Story"

    ;; define, update, and log mutable variables within a wire/shard    
        "Shards" >= .stringvar          ;; create a variable with a string value
        .stringvar (Log)                ;; print initial value of .stringvar => Shards
        "Claymore" > .stringvar         ;; update .stringvar with another string value
        .stringvar (Log)                ;; print updated value of .stringvar => Claymore
        
        123 >= .intvar                  ;; create a variable with a numeric value
        .intvar (Log)                   ;; print initial value of .intvar => 123
        999 > .intvar                   ;; update .intvar with another numeric value          
        (Get .intvar) (Log)             ;; `Get` the updated value of .intvar then log it => 999

        [1 2 3] >= .seqvar              ;; create a variable with a sequence value
        .seqvar (Log)                   ;; print initial value of .seqvar => [1, 2, 3]
        [10 20 30] > .seqvar            ;; update .seqvar with another sequence value          
        .seqvar (Log)                   ;; print updated value of .seqvar => [10, 20, 30]
    
        {:k1 "v1"} >= .tblvar           ;; create a variable with a table (one key/value pair)
        .tblvar (Log)                   ;; print initial value of .tblvar => {:k1 "v1"}
        "v11" (Update .tblvar "k1")     ;; update value of key :k1 (using alias `Update` to pass the key) 
        (Get .tblvar) (Log)             ;; `Get` the updated value of .tblvar then log it => {:k1 "v11"}
    
    ;; define and update `Const` type variables
        (Const true) >= .boolflag       ;; create a boolean variable with value `true`
        .boolflag (Log)                 ;; print current value of .boolflag => true
        (Const false) > .boolflag       ;; update the .boolflag with another boolean value 'false'
        .boolflag (Log)                 ;; print updated value of .boolflag => false
        ;;uncomment the following code to see error when trying to update a boolean type variable with a nil type value
        ;; (Const nil) > .boolflag      ;; throws error as you cannot update one type of variable (boolean) with another type data ('nil')
        
        (Const nil) >= .nilconst        ;; create a nil value constant
        .nilconst (Log)                 ;; print the value of nil constant => None
        ;;uncomment the following code to see error when trying to update a boolean type variable with a nil type value
        ;; (Const "abc") > .nilconst    ;; attempt to update nil type const with string type data throws error

    ;; define, attempt to update, and log constants within a wire/shard
        100 = .intconst                 ;; create a numeric constant with an integer value
        .intconst (Log)                 ;; print the value of .intconst => 100
        ;; uncomment the following code and run it see "attempted to update constant" error
        ;; 900 > .intconst              ;; attempt to update the constant value =>   
        200 (Ref .intconstant)          ;; create another numeric constant using the alias `Ref`   
        (Get .intconstant) (Log)        ;; `Get` the unchanged value of .intvar then log it => 100
        )

    (schedule root mywire)
    (run root 1 1)
    ```
    
=== "Result"

    ```
    ...
    9
    "Toy"
    "Story"
    ...
    [info] [2022-08-02 14:54:03.201] [T-20448] [logging.cpp::55] [mywire] 9
    [info] [2022-08-02 14:54:03.203] [T-20448] [logging.cpp::55] [mywire] Story
    [info] [2022-08-02 14:54:03.204] [T-20448] [logging.cpp::55] [mywire] Shards
    [info] [2022-08-02 14:54:03.206] [T-20448] [logging.cpp::55] [mywire] Claymore
    [info] [2022-08-02 14:54:03.207] [T-20448] [logging.cpp::55] [mywire] 123
    [info] [2022-08-02 14:54:03.208] [T-20448] [logging.cpp::55] [mywire] 999
    [info] [2022-08-02 14:54:03.210] [T-20448] [logging.cpp::55] [mywire] [1, 2, 3]
    [info] [2022-08-02 14:54:03.211] [T-20448] [logging.cpp::55] [mywire] [10, 20, 30]
    [info] [2022-08-02 14:54:03.212] [T-20448] [logging.cpp::55] [mywire] {k1: v1}
    [info] [2022-08-02 14:54:03.214] [T-20448] [logging.cpp::55] [mywire] {k1: v11}
    [info] [2022-08-02 14:54:03.215] [T-20448] [logging.cpp::55] [mywire] true
    [info] [2022-08-02 14:54:03.216] [T-20448] [logging.cpp::55] [mywire] false
    [info] [2022-08-02 14:54:03.217] [T-20448] [logging.cpp::55] [mywire] None
    [info] [2022-08-02 14:54:03.218] [T-20448] [logging.cpp::55] [mywire] 100
    [info] [2022-08-02 14:54:03.220] [T-20448] [logging.cpp::55] [mywire] 200
    ...
    ;; error on attempting to update a boolean var with nil type const (.boolflag)
    [error] [2022-08-02 14:53:14.769] [T-14356] [runtime.cpp::1090] Error composing shard: Set/Ref/Update, variable already set as another type: boolflag, wire: mywire
    Error: Set/Ref/Update, variable already set as another type: boolflag
    ...
    ;; error on attempting to update a nil type var with string type const (.nilconst)
    [error] [2022-08-02 14:53:35.119] [T-6708] [runtime.cpp::1090] Error composing shard: Set/Ref/Update, variable already set as another type: nilconst, wire: mywire
    Error: Set/Ref/Update, variable already set as another type: nilconst
    ...
    ;; error on attempting to update a constant (.intconst)
    [error] [2022-08-02 12:18:55.922] [T-10616] [runtime.cpp::1090] Error composing shard: Set/Ref/Update, attempted to write an immutable variable., wire: mywire
    Error: Set/Ref/Update, attempted to write an immutable variable.
    ```

## Variable scope

Variables exist within 'scopes'. A scope can be thought of as a field of view within which a variable exists, can be seen by other program elements, and can interact with them.

For example, variables created with `(def)` outside the wires are available to all wires/shards.

But variables created inside a wire (called *Context variables* or 'ContextVariable') are by default, accessible only to the wire they've been created in.

They can be made accessible to other wires by setting a parameter called `:Global` to `true` in `Set` (this parameter is `false` by default). 

However, irrespective of `:Global`, variables created within wires are not accessible outside the wires (i.e. `(prn)` cannot read context variables).

The variables that are accessible across wires or outside wires are called *global variables* and can be said to have a *global scope*. 

Variables that are accessible only within the wire they were created are called *local variables* and are said to have *local scope*.

!!! note "The `:Global` parameter"
    1. `:Global` is available as a parameter across many shards that create, manipulate, or read variables. Its default value is always `false`.
    2. When its used with shards that create variables (like `(Set)` or [`(Push)`](https://docs.fragcolor.xyz/shards/General/Push/)) it defines the scope of the newly created variable.
    3. When its used with shards that read, update or modify an existing variable (`(Get)`, `(Update)`, etc.) its value is used in conjunction with the variable's name to identify the right variable (since you can have two variables with the same name but different scope). 

*Code example 17*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (def extvar 9)                                  ;; variable declared oustide wires/shards (external to wires)

    (defmesh root)
    (defwire varwire

    ;; access variables from outside the wires/shards
        extvar (Log)                                ;; variables external to wires can be accessed by all wires => 9

    ;; define locally scoped or local variables (i.e., Global is false by default)    
        "Shards" (Set .var-local)                   ;; .var-local is accessible only within this wire
        "Local Var" (Set .justvar)                  ;; this .justvar (Global is false) is accessible only within this wire

    ;; define globally scoped or global variables (i.e., Global is set to true)    
        "Claymore" (Set .var-global :Global true)   ;; .var-local is accessible only within this wire
        "Global Var" (Set .justvar :Global true)    ;; this .justvar (Global is true) is not the same as the .justvar defined above

        )

    (defwire mywire
    (Once (Dispatch varwire))                       ;; run 'mywire1' once to create wire variables
    ;; access variables from outside wires/shards
        extvar (Log)                                ;; variables external to wires can be accessed by all wires => 9

    ;; access global variables declared in the other wire
        (Get .var-global :Global true) (Log)        ;; can read global var => Claymore
        (Get .justvar :Global true) (Log)           ;; can read global version of .justvar => Global var

    ;; attempt to access local variables declared in the other wire
        ;; uncomment the line below to see error on trying to access local variable of anotheir wire
        ;; (Get .var-local) (Log)                   ;; will give an error

    ;; despite setting :Global flag false, this wire cannot access the local var, falls back to reading global version 
        (Get .justvar :Global false) (Log)          ;; defaults to reading the global version of var .justvar => Global var 
        )

    (schedule root mywire)
    (run root 1 1)

    ;; `prn` can't read context variables
    (prn .var-local)                                ;; prints var name => ContextVariable: var-local
    (prn .var-global)                               ;; prints var name => ContextVariable: var-global
    (prn .justvar)                                  ;; prints var name => ContextVariable: justvar
    ```
    
=== "Result"

    ```
    ...
    [info] [2022-08-02 13:18:33.396] [T-22892] [logging.cpp::55] [varwire] 9      
    [info] [2022-08-02 13:18:33.398] [T-22892] [logging.cpp::55] [mywire] 9       
    [info] [2022-08-02 13:18:33.399] [T-22892] [logging.cpp::55] [mywire] Claymore
    [info] [2022-08-02 13:18:33.400] [T-22892] [logging.cpp::55] [mywire] Global Var
    [info] [2022-08-02 13:18:33.402] [T-22892] [logging.cpp::55] [mywire] Global Var
    ...
    [trace] [2022-08-02 13:18:33.416] [T-22892] [runtime.hpp::294] stopping wire: mywire
    ContextVariable: var-local
    ContextVariable: var-global
    ContextVariable: justvar
    ...
    ;; error on attempting to read a local variable of another wire
    [error] [2022-08-02 13:20:30.112] [T-30168] [runtime.cpp::1090] Error composing shard: Get (var-local): Could not infer an output type and no Default value provided., wire: mywire
    Error: Get (var-local): Could not infer an output type and no Default value provided.   
    ...
    ```

## Manipulating variables

We have seen how to create and modify certain variables. But this is just the tip of the iceberg. Shards contains a large collection of shards to help you modify all the different types of variables that you might use in your program. 

In this section, we will summarize the most important ones. 
    
**Read**

- To read immutable or mutable variable values, including a specific key/value pair from a table: use `Get` (especially if accessing global variables across wires)
- To read specific index element from a sequence or a key/value pair from a table: use [`(Take)`](https://docs.fragcolor.xyz/shards/General/Take/) (for both sequences and tables), [`(RTake)`](https://docs.fragcolor.xyz/shards/General/RTake/) (only for sequences)

**Create**

- To declare un-named constants: use `(Const)`
- To declare named immutable-variables or constants: use `(Ref)`
- To create a new/mutable table: use [`(Table)`](https://docs.fragcolor.xyz/shards/General/Table/)
- To create a new/mutable sequence: use [`(Sequence)`](https://docs.fragcolor.xyz/shards/General/Sequence/)
- To create mutable variables, sequences, tables: use `(Set)`
- To create a sequence with a starting value (including empty []): use `(Push)`
- To create a table with a starting key/value pair (including empty {}): use `(Push)`

**Update**

- To add a new key/value pair to an existing table: use `(Set)`(will generate warnings, but is the cleanest way possible)
- To add a new key/value pair with value as sequence to an existing table: use `(Set)` (avoid `(Push)`)
- To add new elements to existing key/value sequence of an existing table: use `(Set)` (avoid `(Push)`)
- To update an existing key/value in an existing table: use `(Update)`
- To overwrite an existing sequence with a new sequence: use `(Update)`
- To update the value of existing string/numeric variables: use `(Update)`
- To add new elements to an existing sequence: use [`(AppendTo)`](https://docs.fragcolor.xyz/shards/General/AppendTo/)/[`(PrependTo)`](https://docs.fragcolor.xyz/shards/General/AppendTo/) if the sequence was created with `(Set)`, use `(Push)` if the sequence was created with `(Push)`

**Delete**

- To remove the leading or trailing element from a sequence: use [`(DropFront)`](https://docs.fragcolor.xyz/shards/General/DropFront/) and [`(Drop)`](https://docs.fragcolor.xyz/shards/General/Drop/) respectively
- To remove (and use) the leading or trailing element from a sequence: use [`(PopFront)`](https://docs.fragcolor.xyz/shards/General/PopFront/) and [`(Pop)`](https://docs.fragcolor.xyz/shards/General/Pop/) respectively
- To clear out a full sequence: use [`(Clear)`](https://docs.fragcolor.xyz/shards/General/Clear/)
- To remove sequence elements (based on indices) or table key/value pairs (based on keys): use [`(Erase)`](https://docs.fragcolor.xyz/shards/General/Erase/)
- To remove sequence elements that satisfy a certain condition: use [`(Remove)`](https://docs.fragcolor.xyz/shards/General/Remove/)

**Manipulate**

- To swap two variables values (across datatypes): use [`(Swap)`](https://docs.fragcolor.xyz/shards/General/Swap/)
- To join two strings: use [`(String.Join)`](https://docs.fragcolor.xyz/shards/String/Join/)
- To remove the leading and trailing spaces from string data: use [`(String.Trim)`](https://docs.fragcolor.xyz/shards/String/Trim/)
- To convert all string characters into the upper case: use [`(String.ToUpper)`](https://docs.fragcolor.xyz/shards/String/ToUpper/)
- To convert all string characters into the lower case: use [`(String.ToLower)`](https://docs.fragcolor.xyz/shards/String/ToUpper/)
- To copy out characters (or elements) from a string (or a sequence) by specifying start/end and step values: use [`(Slice)`](https://docs.fragcolor.xyz/shards/General/Slice/)
- To sort elements of a sequence in ascending or descending order: use [`(Sort)`](https://docs.fragcolor.xyz/shards/General/Sort/)

**Typecast**

Convert one data type to another

- To convert between equal-element integers and floats vectors use: the appropriate `(To*)` shard (for example, to `(Int4)` => `(Float4)`, use `(ToFloat4)`](https://docs.fragcolor.xyz/shards/General/ToFloat2/))
- To convert an integer/float vector (like `Int2`, `Int3`, ..., `Float3`, `Float4`, etc.) into a sequence: use [`(Flatten)`](https://docs.fragcolor.xyz/shards/General/Flatten/)
- To convert between two unequal integer/float vectors: first `(Flatten)` one of them, then add/remove the required integer and floating-point numbers to match source and destination vector size, then use the appropriate `(To*)` shard (for example, to `(Int2 2 3)` => `(Float3 2.0 3.0 4.0)`, flatten the `(Int2)`, append `(Float 4.0)` to flattened sequence, convert the 3-element sequence to `(Float3)` by using `(ToFloat3)`).
- To convert the string representation of an integer/float to the actual number data: use [`(ParseInt)`](https://docs.fragcolor.xyz/shards/General/ParseInt/) or [`(ParseFloat)`](https://docs.fragcolor.xyz/shards/General/ParseFloat/).

**Miscellaneous**

- To count characters in a string, elements in a sequence, or key/value pairs in a table: use [`(Count)`](https://docs.fragcolor.xyz/shards/General/Count/)
- To print a string to the standard output (from within a wire/shard): use [`(Msg)`](https://docs.fragcolor.xyz/shards/General/Msg/)
- To print an input string to the standard output along with an optional prefix string (from within a wire/shard): use [`(Log)`](https://docs.fragcolor.xyz/shards/General/Log/)
- To print multiple strings **without** their double quotes to the standard output (from outside wires/shards): use [`(println)`](https://docs.fragcolor.xyz/functions/standard-output/#println)
- To print multiple strings **including** their double quotes to the standard output (from outside wires/shards): use [`(prn)`](https://docs.fragcolor.xyz/functions/standard-output/#prn)

## Do your sums

Before we end this section let's look at how Shards does maths and what mathematical transformations are available for us to use within Shards.

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

!!! note
    Some of these comparison operators look exactly like alias symbols for certain shards (`>=`, `>`, `=`), but should not be confused with them. Whether `>` denotes a *greater than* comparison or an update to a variable (alias symbol for `(Update)`) depends on the context of the usage and the rest of the code. The wrong context will throw an error. 

**Maths shards**

If you need to go beyond basic mathematical computation and comparison you can use the extensive library of math and comparison shards included with the language.

For example, [`(Math.Add)`](https://docs.fragcolor.xyz/shards/Math/Add/) will add two numbers, [`(Math.Subtract)`](https://docs.fragcolor.xyz/shards/Math/Subtract/) will subtract one number from the other, and so on for [`(Math.Multiply)`](https://docs.fragcolor.xyz/shards/Math/Multiply/) and [`(Math.Divide)`](https://docs.fragcolor.xyz/shards/Math/Divide/), and others.

It doesn't stop at basic mathematical operations - there are shards that support trigonometric computations, matrix algebra, 3D geometric transformations, etc. 

!!! note
    You can check out all the `(Math.*)` shards by typing 'Math.' in the search box on the [Fragcolor Shards documentation page](https://docs.fragcolor.xyz/).

As for comparison, the following shards can compare numeric quantities too:

- equality (`=`) with [`(Is)`](https://docs.fragcolor.xyz/shards/General/Is/)
- inequality (`<`, `>`) with [`(IsLess)`](https://docs.fragcolor.xyz/shards/General/IsLess/) and  [`(IsMore)`](https://docs.fragcolor.xyz/shards/General/IsMore/)
- mixed equality/inequality (`=<`, `>=`) with [`(IsLessEqual)`](https://docs.fragcolor.xyz/shards/General/IsLessEqual/) and  [`(IsMoreEqual)`](https://docs.fragcolor.xyz/shards/General/IsMoreEqual/)

Now, let's redo our earlier examples with shards instead:

*Code example 18*

=== "EDN"

    ```{.clojure .annotate linenums="1"}
    (defmesh root)

    (defwire mywire
        ;; (> 4 2)                  
        4 (IsMore 2) (Log)                      ;; => true

        ;; (< 4 2)                  
        4 (IsLess 2) (Log)                      ;; => false

        ;; (= 5 (+ 3 2))
        3 (Math.Add 2) (Is 5) (Log)             ;; => true

        ;; (>= 9 (+ (- 10 2) 4))            
        10 (Math.Subtract 2) (Math.Add 4) >= .rhsvar1
        9 (IsMoreEqual .rhsvar1) (Log)          ;; => false       

        ;; (<= (* 2 4) (/ 25 5))             
        25 (Math.Divide 5) >= .rhsvar2
        4 (Math.Multiply 2) >= .lhsvar2
        .lhsvar2 (IsLessEqual .rhsvar2) (Log)   ;; => false
        )

    (schedule root mywire)
    (run root 1 1)
    ```
    
=== "Result"

    ```
    [info] [2022-08-02 20:24:41.389] [T-30948] [logging.cpp::55] [mywire] true
    [info] [2022-08-02 20:24:41.390] [T-30948] [logging.cpp::55] [mywire] false
    [info] [2022-08-02 20:24:41.392] [T-30948] [logging.cpp::55] [mywire] true
    [info] [2022-08-02 20:24:41.393] [T-30948] [logging.cpp::55] [mywire] false
    [info] [2022-08-02 20:24:41.394] [T-30948] [logging.cpp::55] [mywire] false
    ```

--8<-- "includes/license.md"
