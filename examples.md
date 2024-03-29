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

Scheme lists:
```
; Lists
(list 1 2 3 4)
(define lst (list 1 2 3 4))
(define lst2 '(1 2 3 4))
lst
lst2
(append (list 1 2 3 4) '(5 6 7 8))
(list list)

; Evaluate the following and explain your answers using box diagrams.
(car (cons "head" "tail"))
(cdr (cons "head" "tail"))
(car (cdr (cons 1 (cons 2 3))))
(cons (cdr (cons 1 2)) (cdr (cons 3 4)) )

; Evaluate the following:
(car '(1 . 2))
(cons 1 null)
(cons 1 (cons 2 3))

"Does (cons 1 null) display (1 . null) ?"
(cons 1 null)

"Does (cons 1 (cons 2 3)) display (1 . ( 2 . 3)) ?"
(cons 1 (cons 2 3))

; Actual displays: (1), (1 2 . 3)
; Note: Remove dot followed by parenthesis or null!

; Pairs of pairs of pairs -- recursive data structure
(car (cons 1 (cons 2 3)))
(cdr (cons 1 (cons 2 3)))
(car (cdr (cons 1 (cons 2 3))))
(cdr (cdr (cons 1 (cons 2 3))))

; Shorthand for sequence of car, cdr
(cadr (cons 1 (cons 2 3)))
(cddr (cons 1 (cons 2 3)))

; A sequence of pairs that ends with an empty list is a proper list
(cons 1 (cons 2 (cons 3 (cons 4 5))))
(cons 1 (cons 2 (cons 3 (cons 4 null))))
```

<details>
<summary>Result</summary>

```
(1 2 3 4)
(1 2 3 4)
(1 2 3 4)
(1 2 3 4 5 6 7 8)
(#<procedure:list>)
"head"
"tail"
2
(2 . 4)
1
(1)
(1 2 . 3)
"Does (cons 1 null) display (1 . null) ?"
(1)
"Does (cons 1 (cons 2 3)) display (1 . ( 2 . 3)) ?"
(1 2 . 3)
1
(2 . 3)
2
3
2
3
(1 2 3 4 . 5)
(1 2 3 4)
```
</details>

Scheme has two procedures for taking lists apart: `car` (returns the first element of the list) and `cdr` (returns the last element of the list).
```
(car '(a b c)) => a
(cdr '(a b c)) => (b c)
(cdr '(a)) => ()

(car (cdr '(a b c))) => b
(cdr (cdr '(a b c))) => (c)

(car '((a b) (c d))) => (a b)
(cdr '((a b) (c d))) => ((c d))
```

The procedure `cons` constructs pairs. A list is a sequence of pairs; each pair's cdr is the next pair in the sequence.
```
(cons 'a '()) => (a)
(cons 'a '(b c)) => (a b c)
(cons 'a (cons 'b (cons 'c '()))) => (a b c)
(cons '(a b) '(c d)) => ((a b) c d)

(car (cons 'a '(b c))) => a
(cdr (cons 'a '(b c))) => (b c)
(cons (car '(a b c))
      (cdr '(d e f))) => (a e f)
(cons (car '(a b c))
      (cdr '(a b c))) => (a b c)
```

The `cdr` of the last pair in a proper list is the empty list. Otherwise, the sequence of pairs forms an improper list. More formally, the empty list is a proper list, and any pair whose `cdr` is a proper list is a proper list.

An improper list is printed in dotted-pair notation, with a period, or dot, preceding the final element of the list.
```
(cons 'a 'b) => (a . b)
(cdr '(a . b)) => b
(cons 'a '(b . c)) => (a b . c)
```

## List Exercise Problems 
Write a `length` function to find the length of a proper list.
```
(length '()) => 0
(length '(1)) => 1
(length '(1 2)) => 2
```

<details>
<summary>Result</summary>

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
</details>

Write a procedure, list-copy, that returns a copy of its argument,
which must be a list.
```
(list-copy '()) => ()
(list-copy '(a b c)) => (a b c)
```

<details>
<summary>Result</summary>

```scheme
(define list-copy
  (lambda (ls)
    (if (null? ls)
        '()
        (cons (car ls) (cdr ls)))))

(list-copy '())
(list-copy '(a b c))
```
</details>

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
<details>
<summary>Result</summary>

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
</details>

Write procedure `remv` that takes two arguments, an object and a list. It
returns a new list with all occurrences of the object removed from the list.
```
(remv 'a '(a b b d)) => (b b d)
(remv 'b '(a b b d)) => (a d)
(remv 'c '(a b b d)) => (a b b d)
(remv 'd '(a b b d)) => (a b b)
```

<details>
<summary>Result</summary>

```scheme
(define remv
  (lambda (x lst)
    (cond
      ((null? lst) '())
      ((eqv? x (car lst)) (remv x (cdr lst)))
      (else (cons (car lst) (remv x (cdr lst)))))))

(remv 'a '(a b b d))
(remv 'b '(a b b d))
(remv 'c '(a b b d))
(remv 'd '(a b b d))
```
</details>

Write procedure `tree-copy` that treats the structure of pairs as a tree rather
than as a list, with the left subtree being the `car` of the pair and the
right subtree being the `cdr` of the pair.
```
(tree-copy '((a . b) . c)) => ((a . b) . c)
```

<details>
<summary>Result</summary>

```scheme
(define tree-copy
  (lambda (tree)
    (if (not (pair? tree))
        tree
        (cons (tree-copy (car tree))
              (tree-copy (cdr tree))))))

(tree-copy '((a . b) . c))
```
</details>

Write procedure `abs-all` that takes a list of numbers as input and returns a
list of their absolute values.
```
(abs-all '(1 -2 3 -4 5 -6)) => (1 2 3 4 5 6)
```

<details>
<summary>Result</summary>

```scheme
(define abs-all
  (lambda (lst)
    (if (null? lst)
        '()
        (cons (abs (car lst))
              (abs-all (cdr lst))))))

(abs-all '(1 -2 3 -4 5 -6))
```
</details>

write procedure `map1` that maps a one-argument procedure over a single list.
```
(map1 abs '(1 -2 3 -4 5 -6)) => (1 2 3 4 5 6)
```

<details>
<summary>Result</summary>

```scheme
(define map1
  (lambda (f lst)
    (if (null? lst)
        '()
        (cons (f (car lst))
              (map1 f (cdr lst))))))

(map1 abs '(1 -2 3 -4 5 -6)); => '(1 2 3 4 5 6)
```
</details>

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

<details>
<summary>Result</summary>

```scheme
(define calculator
  (lambda (e)
    (if (not (pair? e))
        e
        ((eval (cadr e)) (calculator (car e))
                    (calculator (caddr e))))))
```
</details>

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

<details>
<summary>Result</summary>

```scheme
(define infix->prefix
  (lambda (expr)
    (cond
      ((null? expr)
       (error "Cannot process an empty expression"))
      ((number? expr) expr)
      (else
       (cons (cadr expr)                              ; operator
             (list (infix->prefix (car expr))         ; left
                   (infix->prefix (caddr expr)))))))) ; right
(infix->prefix 42)
(infix->prefix '(1 + 2))
(infix->prefix '(1 + (2 * 8)))
(infix->prefix '((((2 + 3) * 2) / 5) + (17 - 1)))
(infix->prefix '())
```
</details>


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
<details>
<summary>Solution</summary>

```scheme
#lang scheme
(require racket/trace)

(define cond->if
  (lambda (exp)
    (cond
      ((and (equal? (car exp) 'cond)
            (equal? (caadr exp) 'else)) ; (cond (else c)) => c
       (cadadr exp))
      ((equal? (car exp) 'cond)
       (cond->if (cdr exp)))
      ((and (pair? (car exp)) ; (((a) (b)) (else c)) => (if (a) (b) c)
            (equal? (caadr exp) 'else))
       (list 'if (caar exp) (cadar exp) (cadadr exp)))
      ((pair? (car exp)) ; (((a) (b)) ...) => (if (a) (b) ...)
       (list 'if (caar exp) (cadar exp) (cond->if (cdr exp)))))))

(trace cond->if)

(cond->if '(cond ((> x y) (- x y)) ((< x y) (- y x)) (else 0)))
;(if (> x y) (- x y) (if (< x y) (- y x) 0))
(cond->if '(cond (else 0)))
;0
(cond->if '(cond ((> x y) (- x y)) (else 0)))
;(if (> x y) (- x y) 0)
```
</details>

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
