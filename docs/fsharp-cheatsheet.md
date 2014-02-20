This cheatsheet glances over some of the common syntax of F# 3.0

Comments
--------
Block comments are placed between `(*` and `*)`. Line comments start from `//` and continue until the end of the line.

	(* This is block comment *)

    // And this is line comment

Basic Types and Literals
------------------------
Most numeric types have associated suffixes, e.g., `uy` for unsigned 8-bit integers and `L` for signed 64-bit integer.

    let b, i, l = 86uy, 86, 86L

    // val b : byte = 86uy
	// val i : int = 86
    // val l : int64 = 86L

Other common examples are `F` or `f` for 32-bit floating-point numbers, `M` or `m` for decimals, and `I` for big integers.

    let s, f, d, bi = 4.14F, 4.14, 0.7833M, 9999I

	// val s : float32 = 4.14f
	// val f : float = 4.14
	// val d : decimal = 0.7833M
	// val bi : System.Numerics.BigInteger = 9999

Functions
---------
The `let` keyword also defines named functions.

	let negate x = x * -1 
	let square x = x * x 
	let print x = printfn "The number is: %d" x

    let squareNegateThenPrint x = 
		print (negate (square x)) 

### Pipe and composition operators
Pipe operator `|>` is used to chain functions and arguments together:

	let squareNegateThenPrint' x = 
		x |> square |> negate |> print

This operator is essential in assisting the F# type checker by providing type information before use:

    let sumOfLengths (xs : string []) = 
		xs 
		|> Array.map (fun s -> s.Length)
		|> Array.sum

Composition operator `>>` is used to compose functions:

	let squareNegateThenPrint'' = 
		square >> negate >> print
  
### Recursive functions
The `rec` keyword is used together with the `let` keyword to define a recursive function:

	let rec fact x =
	    if x < 1 then 1
	    else x * fact (x - 1)

*Mutually recursive* functions (those functions which call each other) are indicated by `and` keyword:

	let rec even x =
	   if x = 0 then true 
	   else odd (x - 1)

	and odd x =
	   if x = 1 then true 
	   else even (x - 1)

Pattern Matching
----------------
Pattern matching is often facilitated through `match` keyword.

	let sayHi name =
	    match name with
	    | "Jim" -> "Hey Jummy!"
	    | "Ted" -> "Oh, it's you.."
	    | x -> "Hello " + x

Pattern matching can be done directly on arguments:

	let fst' (x, _) = x

Collections
-----------

### Lists
A *list* is an immutable collection of elements of the same type.

    // Lists use square brackets and `;` delimiter
    let list1 = [ "a"; "b" ]
    // :: is prepending
    let list2 = "c" :: list1
    // @ is concat    
    let list3 = list1 @ list2   

    // Recursion on list using (::) operator
    let rec sum list = 
        match list with
        | [] -> 0
        | x :: xs -> x + sum xs

### Arrays
*Arrays* are fixed-size, zero-based, mutable collections of consecutive data elements.

	// Arrays use square brackets with bar
    let array1 = [| "a"; "b" |]
    // Indexed access using dot
    let first = array1.[0]  
      
### Sequences
A *sequence* is a logical series of elements of the same type. Individual sequence elements are computed only as required, so a sequence can provide better performance than a list in situations in which not all the elements are used.

	// Sequences can use yield and contain subsequences
    let seq1 = 
		seq {
	        // "yield" adds one element
	        yield 1
			yield 2
	
	        // "yield!" adds a whole subsequence
	        yield! [5..10]
		}

### Higher-order functions on collections
The same list `[ 1; 3; 5; 7; 9 ]` or array `[| 1; 3; 5; 7; 9 |]` can be generated in various ways.

 - Using range operator `..`
    
        let xs = [ 1..2..9 ]

 - Using list or array comprehensions
    
        let ys = [| for i in 0..4 -> 2 * i + 1 |]

 - Using `init` function

        let zs = List.init 5 (fun i -> 2 * i + 1)

Lists and arrays have comprehensive sets of higher-order functions for manipulation.

  - `fold` starts from the left of the list (or array) and `foldBack` goes in the opposite direction
     
        let xs' = Array.fold (fun str n -> 
 		            sprintf "%s,%i" str n) "" [| 0..9 |]

  - `reduce` doesn't require an initial accumulator
  
        let last xs = List.reduce (fun acc x -> x) xs

  - `map` transforms every element of the list (or array)

		let ys' = Array.map (fun x -> x * x) [| 0..9 |]

  - `iter`ate through a list and produce side effects
 		
		List.iter (fun x -> printfn "%i" x) [ 0..9 ] 

All these operations are also available for sequences. The added benefits of sequences are laziness and uniform treatment of all collections implementing `IEnumerable<'T>`.

	let zs' =
	    seq { 
			for i in 0..9 do
	        	printfn "Adding %d" i
	            yield i
        }

Tuples and Records
------------------
A *tuple* is a grouping of unnamed but ordered values, possibly of different types:

    // Tuple construction
    let x = (1, "Hello")

    // Triple
	let y = ("one", "two", "three") 

    // Tuple deconstruction / pattern
    let (a', b') = x

The first and second elements of a tuple can be obtained using `fst`, `snd`, or pattern matching:

	let c' = fst (1, 2)
	let d' = snd (1, 2)
	
	let print' tuple =
	    match tuple with
	    | (a, b) -> printfn "Pair %A %A" a b

*Records* represent simple aggregates of named values, optionally with members:

    // Declare a record type
    type Person = { Name : string; Age : int }

    // Create a value via record expression
    let paul = { Name = "Paul"; Age = 28 }

    // 'Copy and update' record expression
	let paulsTwin = { paul with Name = "Jim" }

Records can be augmented with properties and methods:

    type Person with
        member x.Info = (x.Name, x.Age)


Discriminated Unions
--------------------
*Discriminated unions* (DU) provide support for values that can be one of a number of named cases, each possibly with different values and types.

    type Tree<'T> =
		| Node of Tree<'T> * 'T * Tree<'T>
		| Leaf

    let rec depth = function
		| Node(l, _, r) -> 1 + depth l + depth r
		| Leaf -> 0

Single-case discriminated unions are often used to create type-safe abstractions with pattern matching support:

    type OrderId = Order of string

    // Create a DU value
    let orderId = Order "12"

    // Pattern matching of single-case DU
    let (Order id) = orderId

Exceptions
----------

Exception handling is done via `try/with` expressions.

	let divide x y =
	   try
	       Some (x / y)
	   with :? System.DivideByZeroException -> 
 	   	   printfn "Division by zero!"
		   None
	
Compiler Directives
-------------------
Load another F# source file into FSI.

    #load "../lib/StringParsing.fs"

Reference a .NET assembly

	#r "../lib/FSharp.Markdown.dll"
