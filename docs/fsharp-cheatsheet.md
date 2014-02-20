Basic Types and Literals
------------------------
use 'let' to assign values.  Most types are inferred, so don't need explicitly defining:

    let myInt = 5
    let myFloat = 3.0

### Type Conversion
There are functions which can be used to convert between common types:

    let i = 42
    let f = float i
    let i2 = int f

### Immutability
Remember!  Once you've set a value, you can't change it.  (OK, you can, but I won't tell you how..)

    let x = 3
    let x <- 5 // No can do!

Functions
---------
The `let` keyword also defines named functions.

	let negate x = x * -1 
	let square x = x * x 

    let squareThenNegate x = 
		negate (square x) 

### Pipe and composition operators
Pipe operator `|>` is used to chain functions and arguments together:

	let squareNegateThenPrint' x = 
		x |> square |> negate 
		
### Type Annotations
If you want to be specific about a type (of the F# compiler can't work it out), do it like so:

	let negate (x : float) = x * -1.0 
  
### Recursive functions
The `rec` keyword is used together with the `let` keyword to define a recursive function:

	let rec factorial x =
	    if x < 1 then 1
	    else x * factorial (x - 1)

Order of Evaluation
-------------------
Things are evaluated strictly left-to-right

	let multiply x y = x * y
	let result = multiply 2 3+5
	
	// result = 11!  Because (multiply 2 3)+5
	
Use parentheses to force evaluation in a different order:

	let result = multiply 2 (3+5)

Pattern Matching
----------------
Pattern matching is often facilitated through `match` keyword.

	let sayHi name =
	    match name with
	    | "Jim" -> "Hey Jimmy!"
	    | "Ted" -> "Oh, it's you.."
	    | x -> "Hello " + x

Tuples
------
A *tuple* is a grouping of unnamed but ordered values, possibly of different types:

    // Tuple construction
    let myTuple = (1, "Hello")

    // Triple
	let y = ("one", "two", "three") 

    // Tuple deconstruction / pattern
    let (a, b) = myTuple

The first and second elements of a tuple can be obtained using `fst`, `snd`, or pattern matching (as above):

	let c' = fst (1, 2)
	let d' = snd (1, 2)

Lists
-----
A *list* is an immutable collection of elements of the same type.

    // Lists use square brackets and `;` delimiter
    let list1 = [ "a"; "b" ]
    // :: is prepending
    let list2 = "c" :: list1
    // @ is concatenating two lists    
    let list3 = list1 @ list2   

### Higher-order functions on collections
The same list `[ 1; 3; 5; 7; 9 ]` can be generated in various ways.

 - Using range operator `..`
    
        let xs = [ 1..2..9 ]

 - Using list comprehensions
    
        let ys = [ for i in 0..4 -> 2 * i + 1 ]

Lists have comprehensive sets of higher-order functions for manipulation.

  - `map` transforms every element of the list (or array)

		let ys' = List.map (fun x -> x * x) [| 0..9 |]

  - `iter`ate through a list and produce side effects
 		
		List.iter (fun x -> printfn "%i" x) [ 0..9 ] 

