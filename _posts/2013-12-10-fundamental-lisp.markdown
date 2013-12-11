---
layout: post
title:  "Fundamental Lisp types"
date:   2013-12-10 18:02:00
categories: lambda
---

*Update: No real Lisp system implements lists as shown below.  This is more of a demonstration of [Church pairs](https://en.wikipedia.org/wiki/Church_encoding#Church_pairs) than anything else.*

There's a sort of crazy thing I read every once in a while, which is that lists aren't the fundamental data type in Lisp.  Lists are rampant in Lisp, they're used to form the language's AST, and it's named for its LISt Processing.  But lists aren't fundamental to the language -- they're perfectly easily derived from functions.

When I mentioned this at the office today, someone asked if I meant that they're derived from functions that return them (the `list` function), but I mean lists are derived from functions because they *are* functions.  

Consider the following JS:

    function cons(a, b) {
      return function(f) {
        return f(a,b)
      }
    }
    
This takes a pair of values and returns a closure containing it.  This closure can be invoked with a function parameter and returns the application of it to the pair.  Assuming a function `add`, `cons` works like:

    > cons(3,5)(add);
    8
    
If wanted to use this form to get either the first or second member of the pair, we'd have to pass a function that returns the correct value when presented with both.

    function first(a,b) { return a }
    function second(a,b) { return b }
    
    > cons(3,5)(first)
    3
    > cons(3,5)(second)
    5
    
But what if we want a function that takes a pair (a `cons cell` if we're getting technical)?  We can define a function that takes a pair and invokes it with `first` or `second`.  This allows us to not only built arbitrarily large data structures, but traverse them as well.

    function car(f) { f(first) }
    function cdr(f) { f(second) }
    
    > car(cons(1,4))
    1
    > car(car(cdr(cdr(cons(1,cons(2,cons(cons(3,4),5)))))))
    3

Wikipedia describes [homoiconicity](http://en.wikipedia.org/wiki/Homoiconicity) in the following way:

  > In a homoiconic language the primary representation of programs is also a data structure in a primitive type of the language itself. This makes metaprogramming easier than in a language without this property, since code can be treated as data
  
  
The "code is data" part is what typically gets driven home.  What makes Lisp *most* interesting is that the reason code is data is because data is actually code to begin with.


    /* construct a cell */
    function cons(a, b) {
      return function(f) {
        return f(a,b)
      }
    }
    /* retrieve the first element */
    function car(f) {
      return f(function(a,b) {
        return a
      })
    }
    /* retrieve the second element */
    function cdr(f) {
      return f(function(a,b) {
        return b
      })
    }
    

