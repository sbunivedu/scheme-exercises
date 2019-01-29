# Solutions

Exercise 2.2.1 Convert the following arithmetic expressions into Scheme
expressions and evaluate them.
```
1.2 × (2 - 1/3) + -8.7
(2/3 + 4/9) ÷ (5/11 - 4/3)
1 + 1 ÷ (2 + 1 ÷ (1 + 1/2))
1 × -2 × 3 × -4 × 5 × -6 × 7
```

Solutions:
```
(+ (* 1.2 (- 2 1/3)) -8.7) => -6.699999999999999
(+ (+ 2/3 4/9) (- 5/11 4/3)) => 23/99
(+ 1 (+ 1 (+ 2 (+ 1 (+ 1 1/2))))) -> 6(1/2)
(* 1 (* -2 (* 3 (* -4 (* 5 (* -6 7)))))) => -5040
```

Exercise 2.2.2 Experiment with the procedures `+`, `-`, `*`, and `/` to
determine Scheme's rules for the type of value returned by each when given
different types of numeric arguments.

Exercise 2.2.3 Determine the values of the following expressions. Use your
Scheme system to verify your answers.
```
(cons 'car 'cdr)
(list 'this '(is silly))
(cons 'is '(this silly?))
(quote (+ 2 3))
(cons '+ '(2 3))
(car '(+ 2 3))
(cdr '(+ 2 3))
cons
(quote cons)
(quote (quote cons))
(car (quote (quote cons)))
(+ 2 3)
(+ '2 '3)
(+ (car '(2 3)) (car (cdr '(2 3))))
((car (list + - * /)) 2 3)
```

Solutions:
```
(cons 'car 'cdr) => '(car . cdr)
(list 'this '(is silly)) => '(this (is silly))
(quote (+ 2 3)) => '(+ 2 3)
(cons '+ '(2 3)) => '(+ 2 3)
(car '(+ 2 3)) => '+
(cdr '(+ 2 3)) => '(2 3)
cons => #<procedure:cons>
(quote cons) => 'cons
(quote (quote cons)) => ''cons
(car (quote (quote cons))) => 'quote  (note: second quote in the argument)
(+ 2 3) => 5
(+ '2 '3) => 5
(+ (car '(2 3)) (car (cdr '(2 3))))
((car (list + - * /)) 2 3)
```
Exercise 2.2.4 `(car (car '((a b) (c d))))` yields `a`. Determine which
compositions of `car` and `cdr` applied to `((a b) (c d))` yield `b`, `c`, and `d`.

Solutions:
```
(cadar '((a b) (c d))) => 'b
(caadr '((a b) (c d))) => 'c
(cadadr '((a b) (c d))) => 'd
```

Exercise 2.2.5 Write a Scheme expression that evaluates to the following
internal list structure.
![box diagram](./images/2.gif)

Solutions:
```
(list (cons 'a 'b) (list (list 'c) 'd) '()) => '((a . b) ((c) d) ())
```

Exercise 2.2.6 Draw the internal list structure produced by the expression below.
```
(cons 1 (cons '(2 . ((3) . ())) (cons '(()) (cons 4 5))))
```

<details>
<summary>Solution</summary>
yes.
![box diagram](./images/box-diagram.png)
</details>
Exercise 2.2.7 The behavior of `(car (car (car '((a b) (c d)))))` is undefined
because `(car '((a b) (c d)))` is `(a b)`, `(car '(a b))` is `a`, and `(car 'a)`
is undefined. Determine all legal compositions of `car` and `cdr` applied to
`((a b) (c d))`.

Exercise 2.2.8
Try to explain how Scheme expressions are evaluated. Does your explanation
cover the last example in Exercise 2.2.3?

Exercise 2.3.1 Write down the steps necessary to evaluate the expression below.
```
((car (cdr (list + - * /))) 17 5)
```

Exercise 2.4.1 Rewrite the following expressions, using `let` to remove common
subexpressions and to improve the structure of the code. Do not perform any
algebraic simplifications.
```scheme
(+ (- (* 3 a) b) (+ (* 3 a) b))
(cons (car (list a b c)) (cdr (list a b c)))
```

Solutions:
<details>
<summary>Solution</summary>
```scheme
(+ (- (* 3 a) b) (+ (* 3 a) b))
(cons (car (list a b c)) (cdr (list a b c)))
```
</details>

Exercise 2.4.2 Determine the value of the following expression. Explain how
you derived this value.
```
(let ([x 9])
  (* x
     (let ([x (/ x 3)])
       (+ x x))))
```

Exercise 2.4.3
Rewrite the following expressions to give unique names to each different
let-bound variable so that none of the variables is shadowed. Verify that the
value of your expression is the same as that of the original expression.
a.
```
(let ([x 'a] [y 'b])
  (list (let ([x 'c]) (cons x y))
        (let ([y 'd]) (cons x y))))
```
b.
```
(let ([x '((a b) c)])
  (cons (let ([x (cdr x)])
          (car x))
        (let ([x (car x)])
          (cons (let ([x (cdr x)])
                  (car x))
                (cons (let ([x (car x)])
                        x)
                      (cdr x))))))
```

Exercise 2.5.1 Determine the values of the expressions below.
a.
```
(let ([f (lambda (x) x)])
  (f 'a))
```
  b.
```
(let ([f (lambda x x)])
  (f 'a))
```
  c.
```
(let ([f (lambda (x . y) x)])
  (f 'a))
```
d.
```
(let ([f (lambda (x . y) y)])
  (f 'a))
```

Exercise 2.5.2 How might the primitive procedure list be defined?

Exercise 2.5.3 List the variables that occur free in each of the lambda
expressions below. Do not omit variables that name primitive procedures such
as `+` or `cons`.
a. `(lambda (f x) (f x))`
b. `(lambda (x) (+ x x))`
c. `(lambda (x y) (f x y))`
d.
```
(lambda (x)
  (cons x (f x y)))
```
e.
```
(lambda (x)
  (let ([z (cons x y)])
    (x y z)))
```
f.
```
(lambda (x)
  (let ([y (cons x y)])
    (x y z)))
```

Exercise 2.6.1 What would happen if you were to type
`(double-any double-any double-any)` given the definition of double-any from
the beginning of this section?
```
(define double-any
  (lambda (f x)
    (f x x)))
```

Exercise 2.6.2 A more elegant (though possibly less efficient) way to define
`cadr` and `cddr` than given in this section is to define a procedure that
composes two procedures to create a third. Write the procedure compose, such
that `(compose p1 p2)` is the composition of `p1` and `p2` (assuming both take
one argument). That is, (compose p1 p2) should return a new procedure of one
argument that applies p1 to the result of applying p2 to the argument. Use
compose to define `cadr` and `cddr`.

Exercise 2.6.3 Scheme also provides `caar`, `cdar`, `caaar`, `caadr`, and so on,
with any combination of up to four a's (representing `car`) and d's
(representing `cdr`) between the `c` and the `r` (see Section 6.3). Define
each of these with the compose procedure of the preceding exercise.

Exercise 2.7.1 Define the predicate atom?, which returns true if its argument
is not a pair and false if it is.

Exercise 2.7.2 The procedure length returns the length of its argument, which
must be a list. For example, `(length '(a b c))` is 3. Using length, define
the procedure shorter, which returns the shorter of two list arguments.
Have it return the first list if they have the same length.
```
(shorter '(a b) '(c d e)) => (a b)
(shorter '(a b) '(c d)) => (a b)
(shorter '(a b) '(c)) => (c)
```

Exercise 2.8.1 Describe what would happen if you switched the order of the
arguments to `cons` in the definition of `tree-copy`.

```
(define tree-copy
  (lambda (tr)
    (if (not (pair? tr))
        tr
        (cons (tree-copy (car tr))
              (tree-copy (cdr tr))))))
```

Exercise 2.8.2 Consult Section 6.3 for the description of `append` and define a
two-argument version of it. What would happen if you switched the order of the
arguments in the call to append within your definition of append?

Exercise 2.8.3 Define the procedure `make-list`, which takes a nonnegative
integer `n` and an object and returns a new list, `n` long, each element of
which is the object.
```
(make-list 7 '()) <graphic> (() () () () () () ())
```
[Hint: The base test should be `(= n 0)`, and the recursion step should involve
`(- n 1)`. Whereas `()` is the natural base case for recursion on lists, `0` is
the natural base case for recursion on nonnegative integers. Similarly,
subtracting 1 is the natural way to bring a nonnegative integer closer to 0.]

Exercise 2.8.4 The procedures `list-ref` and `list-tail` return the nth element
and nth tail of a list ls.
```
(list-ref '(1 2 3 4) 0) => 1
(list-tail '(1 2 3 4) 0) => (1 2 3 4)
(list-ref '(a short (nested) list) 2) => (nested)
(list-tail '(a short (nested) list) 2) => ((nested) list)
```

Exercise 2.8.5
Exercise 2.7.2 had you use `length` in the definition of shorter, which returns
the shorter of its two list arguments, or the first if the two have the same
length. Write `shorter` without using `length`. [Hint: Define a recursive helper,
`shorter?`, and use it in place of the length comparison.]

Exercise 2.8.6 All of the recursive procedures shown so far have been directly
recursive. That is, each procedure directly applies itself to a new argument.
It is also possible to write two procedures that use each other, resulting in
indirect recursion. Define the procedures `odd?` and `even?`, each in terms of the
other. [Hint: What should each return when its argument is 0?]
```
(even? 17) => #f
(odd? 17) => #t
```

Exercise 2.8.7 Use `map` to define a procedure, `transpose`, that takes a list
of pairs and returns a pair of lists as follows.
```
(transpose '((a . 1) (b . 2) (c . 3))) => ((a b c) 1 2 3)
```
[Hint: `((a b c) 1 2 3)` is the same as `((a b c) . (1 2 3))`.]
