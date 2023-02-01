Here are some examples of simple expressions in Scheme:

```scheme
486
(+ 137 349)
(/ 10 5)
(/ 10 3)
(+ 2.7 10)
(+ (* 3 (+ (* 2 4) (+ 3 5))) (+ (- 10 7) 6))
(+ (* 3
      (+ (* 2 4)
         (+ 3 5)))
   (+ (- 10 7)
      6))

(define size 2)
size
(* 5 size)

; use names to refer to computational objects
(define pi 3.14159)
(define radius 10)
(* pi (* radius radius))
(define circumference (* 2 pi radius))
circumference

; procedures and the processes they generate
; linear recursive process
(require racket/trace)

(define (fact n)
  (if (= n 1)
      1
      (* n (fact (- n 1)))))

(trace fact)

; linear iterative process

(define (fact n)
  (fact-iter 1 1 n))

(define (fact-iter product counter max-count)
  (if (> counter max-count)
      product
      (fact-iter (* counter product)
                 (+ counter 1)
                 max-count)))

(trace fact-iter)
```

More examples:
```scheme
; Literals
"Hello, World" ; string
'HelloWorld    ; symbol
*              ; primitive
#t             ; boolean
#f             ; boolean
#\A            ; character

; Numeric data types:
10             ; integer
#b101          ; binary
#o101          ; octal
#x101          ; hexdecimal
3.14           ; real
7/3            ; fraction
4+2i           ; imaginary

; Symbols and strings
(symbol? "Hello")
(symbol? 'World) ; simple is unique (same object/entity)
(string? "Hello")
(string? 'World)
(symbol->string 'hello)
(string->symbol "World")

; Scheme uses prefix notation: the operator comes first
(+ 1 2)
(sqrt (+ (* 4 4) (* 3 3)))
(sqrt -1)
(/ 743 9)
(/ 743 9.0)
(quotient 17 3)
(remainder 17 3)
(* 123456789123456789 123456789123456789 123456789123456789)

; Associate the value 5 with the name "x"
(define x 5)
(* 2 x)  

; Define a new function
(define half-it
  (lambda (x)
          (/ x 2)))

; Lists
(list 1 2 3 4)
(define lst (list 1 2 3 4))
(define lst2 '(1 2 3 4))
lst
lst2
(append (list 1 2 3 4) '(5 6 7 8))

; A scheme form is something that you ask Scheme to evaluate (expression)
1
"Hello World!"
(+ 1 2)
(+ 1 2 3 4)

; Comments start with a semicolon
#; (comment out an entire form)
#; (define i-am-ignored
     (lambda (x)
       (* x 0))) 

(define mynumber 10)
(define 1#$#&%FV 5)
(newline)

; number predicates
(integer? 3)
(integer? 3.0)
(integer? (* 3 1/3))
(integer? (* 3 (/ 1 3)))

(real? 3)
(real? 3.0)
(real? 2+i)
(real? 2+0i)

(rational? 1/3)
(complex? 2+i)
(complex? 2)
(complex? 2+0i)
(rational? 2+0i)

(number? "one")
(number? '3)

; checking equality
(= 32 (* 4 8))
;(= 'Hello 'Hello)
(eq? 'Hello 'Hello)
(eq? 'Hello "Hello")
(eq? "Hello" "Hello")
(eq? (symbol->string 'Hello) "Hello") ; not same instance
(eq? '(1 2 3) '(1 2 3))

(equal? 32 (* 4 8))
(equal? 'Hello 'Hello)
(equal? 'Hello 'Hello)
(equal? 'Hello "Hello")
(equal? "Hello" "Hello")
(equal? (symbol->string 'Hello) "Hello") ; not same instance
(equal? '(1 2 3) '(1 2 3))

(define x 10)

(define trip  ; define associates a name with a form
  (lambda (x) ; lambda defines a procedure
    (* 3 x))) ; this form associates the name "trip" to the lambda expression
```

Write a `length` function to find the length of a proper list.
```
(length '()) => 0
(length '(1)) => 1
(length '(1 2)) => 2
```

```scheme
(define length
  (lambda (ls)
    (if (null? ls)
        0
        (+ 1 (length (cdr ls))))))

(length '())
(length '(1))
(length '(1 2))
```

Write a procedure, list-copy, that returns a copy of its argument,
which must be a list.
```
(list-copy '()) => ()
(list-copy '(a b c)) => (a b c)
```
```scheme
(define list-copy
  (lambda (ls)
    (if (null? ls)
        '()
        (cons (car ls) (cdr ls)))))

(list-copy '())
(list-copy '(a b c))
```

Write procedure `memv` that takes two arguments, an object and a list.
It returns the first sublist, or tail, of the list whose `car` is equal to the
object, or `#f` if the object is not found in the list.
```
(memv 'a '(a b b d)) => (a b b d)
(memv 'b '(a b b d)) => (b b d)
(memv 'c '(a b b d)) => #f
(memv 'd '(a b b d)) => (d)
(if (memv 'b '(a b b d))
    "yes"
    "no") => "yes"
```
```scheme
(define memv
  (lambda (x lst)
    (cond
      ((null? lst) #f)
      ((eqv? x (car lst)) lst)
      (else (memv x (cdr lst))))))

(memv 'a '(a b b d))
(memv 'b '(a b b d))
(memv 'c '(a b b d))
(memv 'd '(a b b d))
(if (memv 'b '(a b b d))
    "yes"
    "no")
```

Write procedure `remv` that takes two arguments, an object and a list. It
returns a new list with all occurrences of the object removed from the list.
```
(remv 'a '(a b b d)) => (b b d)
(remv 'b '(a b b d)) => (a d)
(remv 'c '(a b b d)) => (a b b d)
(remv 'd '(a b b d)) => (a b b)
```
```
(define remv
  (lambda (x lst)
    (cond
      ((null? lst) '())
      ((eqv? x (car lst)) (cdr lst))
      (else (cons (car lst) (remv x (cdr lst)))))))

(remv 'a '(a b b d))
(remv 'b '(a b b d))
(remv 'c '(a b b d))
(remv 'd '(a b b d))
```

Write procedure `tree-copy` that treats the structure of pairs as a tree rather
than as a list, with the left subtree being the `car` of the pair and the
right subtree being the `cdr` of the pair.
```
(tree-copy '((a . b) . c)) => ((a . b) . c)
```
```scheme
(define tree-copy
  (lambda (tree)
    (if (not (pair? tree))
        tree
        (cons (tree-copy (car tree))
              (tree-copy (cdr tree))))))

(tree-copy '((a . b) . c))
```

Write procedure `abs-all` that takes a list of numbers as input and returns a
list of their absolute values.
```
(abs-all '(1 -2 3 -4 5 -6)) => (1 2 3 4 5 6)
```
```scheme
(define abs-all
  (lambda (lst)
    (if (null? lst)
        '()
        (cons (abs (car lst))
              (abs-all (cdr lst))))))

(abs-all '(1 -2 3 -4 5 -6))
```

write procedure `map1` that maps a one-argument procedure over a single list.
```
(map1 abs '(1 -2 3 -4 5 -6)) => (1 2 3 4 5 6)
```
```scheme
(define map1
  (lambda (f lst)
    (if (null? lst)
        '()
        (cons (f (car lst))
              (map1 f (cdr lst))))))

(map1 abs '(1 -2 3 -4 5 -6)); => '(1 2 3 4 5 6)
```

Write a function, `calculator`, which takes an infix arithmetic expression and evaluates it. For
example,
```
> (calculator 42)
42
> (calculator ’(1 + 2))
3
> (calculator ’(1 + (2 * 8)))
17
> (calculator ’((((2 + 3) * 2) / 5) + (17 - 1))
18
```
You may assume that all sub-expressions are parenthesized so that you don’t need to worry
about precedence. Also, you need only implement the four basic arithmetic functions,
namely, plus, minus, times and divide.

Write a function, `infix->prefix`, which takes an infix arithmetic expression and returns the
corresponding prefix expression.
```
> (infix->prefix 42)
42
> (infix->prefix ’(1 + 2))
(+ 1 2)
> (infix->prefix ’(1 + (2 * 8)))
(+ 1 (* 2 8))
> (infix->prefix ’((((2 + 3) * 2) / 5) + (17 - 1))
(+ (/ (* (+ 2 3) 2) 5) (- 17 1))
```

Define a function `iota-iota` that takes an integer i as its argument and returns a list of pairs of
integers such that
```
> (iota-iota 1)
((1 . 1))
> (iota-iota 2)
((1 . 1) (1 . 2) (2 . 1) (2 .2))
> (iota-iota 3)
((1 . 1) (1 . 2) (1 . 3) (2 . 1) (2 . 2) (2 . 3)
(3 . 1) (3 . 2) (3 . 3))
```
All helper functions should be tail-recursive and should be defined within the
body of `iota-iota` using `letrec`.

Define a tail-recursive function digits->number that takes a list of digits and returns the
number represented by those digits. For example,
```
> (digits->number ’(7 6 1 5))
7615
```
Any helper functions you need should be defined within the body of `digits->number` using
`letrec`.

Write a function, `cond->if`, which takes a `cond` expression, and transforms it into a set of
nested `if` expressions. For example,
```
> (cond->if ’(cond ((> x y) (- x y)) ((< x y) (- y x)) (else 0)))
(if (> x y) (- x y) (if (< x y) (- y x) 0))
>
```

Write a tail-recursive function, `cos`, which takes a number, `x`, as its argument and returns
`cos(x)`. Your function should approximate `cos(x)` by summing the first 100 terms of the
following Taylor series:
```
cos(x) = x^0/0!-x^2/2!+x^4/4!-x^6/6!+x^8/8!-...
```
Any helper functions you need should be defined within the body of `sin` using `letrec`. Note:
There is a good way and a bad way to do this. The good way avoids computing the factorial
and the power of `x` which appear in each term in the series from scratch each time. In other
words, do not use or define `fact` or `expt`.
