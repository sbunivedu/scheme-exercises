# Scheme Basics
Read the first two chapters of
EOPL: Essentials of Programming Languages (2nd edition), and answer the
following questions.

## Exercise 1.1

Write a syntactic derivation that proves `(-7 . (3 . (14 . ())))` is a list of numbers.
<details>
<summary>Solution</summary>

~~~~
<list-of-numbers> ::= () | (<number> . <list-of-numbers>)

<list-of-numbers>
=> (<number> . <list-of-numbers>)
=> (-7 . <list-of-numbers>)
=> (-7 . (<number> . <list-of-numbers>))
=> (-7 . (3 . <list-of-numbers>))
~~~~
</details>

Exercise 1.2 [*]
Rewrite the <datum> grammar without using the Kleene star or plus. Then indicate the changes to the above derivation that are required by this revised grammar.
<list> 		::= ({datum} . <list>)
<dotted-datum> 	::= (<datum>) | (<datum> . <dotted-datum>)
<vector>		::= #() | #(<datum> <vector>)
<data> ::= datum | datum data
Exercise 1.3 [*] Write a syntactic derivation that proves (a "mixed" #(bag (of . data))) is a datum, using either the grammar in the book or the revised grammar from the preceding exercise. What is wrong with (a . b . c)?
Exercise 1.4 [*]
Exercise 1.10 [*]
In the last line of subst-in-symbol-expression, the recursion is on se and not a smaller substructure. Why is the recursion guaranteed to halt?
Exercise 1.11 [*]  
Eliminate the one call to subst-in-symbol-expression in subst by replacing it by its definition and simplifying the resulting procedure. The result will be a version of subst that does not need subst-in-symbol-expression. This technique is called inlining, and is used by optimizing compilers.
Exercise 1.12 [**]
In our example, we began by eliminating the Kleene star in the grammar for <s-list>. When a production is expressed using Kleene star, often the recursion can be expressed using map. Write subst following the original grammar by using map.
(define subst
  (lambda (new old slist)
    (if (null? slist)
      '()
      (map (lambda (x)
             (subst-in-symbol-expression new old x))
           slist))))

(define subst-in-symbol-expression
  (lambda (new old se)
    (if (symbol? se)
      (if (eqv? se old)
          new
          se)
      (subst new old se))))


;> (subst 'a 'b '((b c) (b () d)))
;((a c) (a () d))

Exercise 1.13 [**]
Rewrite the grammar for <s-list> to use Kleene star, and rewrite notate-depth- in-s-list using map.

<s-list> 			::= ()
				::= ({<symbol-expression>}*)
<symbol-expression> 	::= <symbol> | <s-list>

(define notate-depth
  (lambda (slist)
    (notate-depth-in-s-list slist 0)))

(define notate-depth-in-s-list
  (lambda (slist d)                     
    (if (null? slist)
      '()
      (map (lambda (x)
             (notate-depth-in-symbol-expression x d))
           slist))))

(define notate-depth-in-symbol-expression
  (lambda (se d)
    (if (symbol? se)
      (list se d)
      (notate-depth-in-s-list se (+ d 1)))))

;> (notate-depth '(a (b () c) ((d)) e))
;((a 0) ((b 1) () (c 1)) (((d 2))) (e 0)))

Exercise 1.14 [**]
Given the assumption 0 ≤ n < length(von), prove that partial-vector-sum is correct.

Exercise 1.15 [*]
Define, test, and debug the following procedures. Assume that s is any symbol, n is a nonnegative integer, lst is a list, v is a vector, los is a list of symbols, vos is a vector of symbols, slist is an s-list, and x is any object; and similarly s1 is a symbol, los2 is a list of symbols, x1 is an object, etc. Also assume that pred is a predicate, that is, a procedure that takes any Scheme object and returns either #t or #f. Make no other assumptions about the data unless further restrictions are given as part of a particular problem. For these exercises, there is no need to check that the input matches the description; for each procedure, assume that its input values are members of the specified sets.

To test these procedures, at the very minimum try all of the given examples. Also use other examples to test these procedures, since the given examples are not adequate to reveal all possible errors.
1.  (duple n x) returns a list containing n copies of x.
> (duple 2 3)
(3 3)
> (duple 4 '(ho ho))
((ho ho) (ho ho) (ho ho) (ho ho))
> (duple 0 '(blah))
()

(define (duple n x)
  (if (= n 0)
      '()
      (cons x
            (duple (- n 1) x))))

2. (invert lst), where lst is a list of 2-lists (lists of length two), returns a list with each 2-
list reversed.
> (invert '((a 1) (a 2) (b 1) (b 2)))
((1 a) (2 a) (1 b) (2 b))

(define (invert lst)
  (if (null? lst)
      '()
      (cons (list (cadr (car lst)) (car (car lst)))
            (invert (cdr lst)))))

3. (filter-in pred lst) returns the list of those elements in lst that satisfy the predicate
pred.
> (filter-in number? '(a 2 (1 3) b 7))
(2 7)
> (filter-in symbol? '(a (b c) 17 foo))
(a foo)

(define (filter-in pred lst)
  (if (null? lst)
      '()
      (if (pred (car lst))
          (cons (car lst)
                (filter-in pred (cdr lst)))
          (filter-in pred (cdr lst)))))

4. (every? pred lst) returns #f if any element of lst fails to satisfy pred, and returns #t otherwise.
> (every? number? '(a b c 3 e))
#f
> (every? number? '(1 2 3 5 4))
#t

(define (every? pred lst)
  (if (null? lst)
      #t
      (and (pred (car lst))
           (every? pred (cdr lst)))))

5. (exists? pred lst) returns #t if any element of lst satisfies pred, and returns #f
otherwise.
> (exists? number? '(a b c 3 e))
#t
> (exists? number? '(a b c d e))
#f

(define (exists? pred lst)
   (if (null? lst)
       #f
       (or (pred (car lst))
            (exists? pred (cdr lst)))))

6. (vector-index pred v) returns the zero-based index of the first element of v that
satisfies the predicate pred, or #f if no element of v satisfies pred.
> (vector-index (lambda (x) (eqv? x 'c)) '#(a b c d))
2
> (vector-ref '#(a b c) (vector-index (lambda (x) (eqv? x 'b)) '#(a b c)))
b

(define vector-index
  (lambda (pred v)
    (vector-index-rec pred v (vector-length v))))

(define vector-index-rec
  (lambda (pred v n)
    (if (pred (vector-ref v (- n 1)))
        (- n 1)
        (vector-index-rec pred v (- n 1)))))

(define vector-index
  (lambda (pred v)
    (letrec ((vector-index-rec
              (lambda (pred v n)
                (if (pred (vector-ref v (- n 1)))
                    (- n 1)
                    (vector-index-rec pred v (- n 1))))))
      (vector-index-rec pred v (vector-length v)))))

7. (list-set lst n x) returns a list like lst, except that the n-th element, using zero-
based indexing, is x.
> (list-set '(a b c d) 2 '(1 2))
(a b (1 2) d)
> (list-ref (list-set '(a b c d) 3 '(1 5 10)) 3)
(1 5 10)

(define (list-set lst n x)
  (if (= n 0)
      (cons x (cdr lst))
      (cons (car lst)
            (list-set (cdr lst) (- n 1) x))))

8. (product los1 los2) returns a list of 2-lists that represents the Cartesian product of
los1 and los2. The 2-lists may appear in any order.
> (product '(a b c) '(x y))
((a x) (a y) (b x) (b y) (c x) (c y))

(define (product los1 los2)
  (if (null? los1)
      '()
      (append (partial-product (car los1) los2)
            (product (cdr los1) los2))))

(define (partial-product s los)
  (if (null? los)
      '()
      (cons (list s (car los))
            (partial-product s (cdr los)))))

(define (product los1 los2)
  (if (null? los1)
      '()
      (append (map (lambda (x)
                     (list (car los1) x))
                   los2)
            (product (cdr los1) los2))))


9. (down lst) wraps parentheses around each top-level element of lst.
> (down '(1 2 3))
((1) (2) (3))
> (down '((a) (fine) (idea)))
(((a)) ((fine)) ((idea)))
> (down '(a (more (complicated)) object))
((a) ((more (complicated))) (object))

(define (down lst)
  (if (null? lst)
      '()
      (cons (list (car lst))
            (down (cdr lst)))))

10. (vector-append-list v lst) returns a new vector with the elements of lst attached to the end of v. Do this without using vector->list, list->vector, and append.
> (vector-append-list '#(1 2 3) '(4 5))
#(1 2 3 4 5)
Exercise 1.16 [**]
1. (up lst) removes a pair of parentheses from each top-level element of lst. If a top-level element is not a list, it is included in the result, as is. The value of (up (down lst)) is equivalent to lst, but (down (up lst)) is not necessarily lst.
> (up '((1 2) (3 4)))
(1 2 3 4)
> (up '((x (y)) z))
(x (y) z)

2. (swapper s1 s2 slist) returns a list the same as slist, but with all occurrences of s1
replaced by s2 and all occurrences of s2 replaced by s1.
> (swapper 'a 'd '(a b c d))
(d b c a)
> (swapper 'a 'd '(a d () c d))
(d a () c a)
> (swapper 'x 'y '((x) y (z (x))))
((y) x (z (y)))

(define (swapper s1 s2 slist)
  (if (null? slist)
      '()
      (cond ((list? (car slist))
             (cons (swapper s1 s2 (car slist))
                   (swapper s1 s2 (cdr slist))))
            ((eqv? s1 (car slist))
             (cons s2 (swapper s1 s2 (cdr slist))))
            ((eqv? s2 (car slist))
             (cons s1 (swapper s1 s2 (cdr slist))))
            (else
             (cons (car slist)
                   (swapper s1 s2 (cdr slist)))))))

3. (count-occurrences s slist) returns the number of occurrences of s in slist.
> (count-occurrences 'x '((f x) y (((x z) x))))
3
> (count-occurrences 'x '((f x) y (((x z) () x))))
3
> (count-occurrences 'w '((f x) y (((x z) x))))
0

(define (count-occurrences s slist)
  (if (null? slist)
      0
      (if (list? (car slist))
          (+ (count-occurrences s (car slist))
             (count-occurrences s (cdr slist)))
          (if (eqv? s (car slist))
              (+ 1 (count-occurrences s (cdr slist)))
              (count-occurrences s (cdr slist))))))

4. (flatten slist) returns a list of the symbols contained in slist in the order in which they occur when slist is printed. Intuitively, flatten removes all the inner parentheses from its argument.
> (flatten '(a b c))
(a b c)
> (flatten '((a) () (b ()) () (c)))
(a b c)
> (flatten '((a b) c (((d)) e)))
(a b c d e)
> (flatten '(a b (() (c))))
(a b c)

(define (flatten slist)
  (if (null? slist)
      '()
      (if (not (list? (car slist)))
          (cons (car slist)
                (flatten (cdr slist)))
          (append (flatten (car slist))
                  (flatten (cdr slist))))))

5. (merge lon1 lon2), where lon1 and lon2 are lists of numbers that are sorted in ascending order, returns a sorted list of all the numbers in lon1 and lon2.
> (merge '(1 4) '(1 2 8))
(1 1 2 4 8)
> (merge '(35 62 81 90 91) '(3 83 85 90))
(3 35 62 81 83 85 90 90 91)

(define (merge lon1 lon2)
  (if (null? lon1)
      lon2
      (if (null? lon2)
          lon1
          (if (< (car lon1) (car lon2))
              (cons (car lon1)
                    (merge (cdr lon1) lon2))
              (cons (car lon2)
                    (merge lon1 (cdr lon2)))))))

Exercise 1.17 [***]
1. (path n bst), where n is a number and bst is a binary search tree that contains the number n, returns a list of lefts and rights showing how to find the node containing n. If n is found at the root, it returns the empty list.
> (path 17 '(14 (7 () (12 () ())) (26 (20 (17 () ()) ()) (31 () ()))))
(right left left)

(define (path n bst)
  (if (null? bst)
      '()
      (if (= n (car bst))
          '()
          (if (< n (car bst))
              (cons 'left
                    (path n (cadr bst)))
              (cons 'right
                    (path n (caddr bst)))))))

2. (sort lon) returns a list of the elements of lon in increasing order.
> (sort '(8 2 5 2 3))
(2 2 3 5 8)
> (sort > '(8 2 7 5 12 1 24 42 130 3 4))
(1 2 3 4 5 7 8 12 24 42 130)

; insertion sort
(define (sort lon)
  (if (null? lon)
      '()
      (insert (car lon)
              (sort (cdr lon)))))

(define (insert n slon)
  (if (null? slon)
      (list n)
      (if (> n (car slon))
          (cons (car slon)
                (insert n (cdr slon)))
          (cons n slon))))

; quick sort
(define (filter-in pred lst)
  (if (null? lst)
      '()
      (if (pred (car lst))
          (cons (car lst)
                (filter-in pred (cdr lst)))
          (filter-in pred (cdr lst)))))

(define (sort lon)
  (let ((<?
         (lambda (n)
           (lambda (x)
             (< x n))))
        (>=?
         (lambda (n)
           (lambda (x)
             (>= x n)))))
    (if (null? lon)
        '()
        (append
         (sort (filter-in (<? (car lon)) (cdr lon)))
         (list (car lon))
         (sort (filter-in (>=? (car lon)) (cdr lon)))))))

3. (sort predicate lon) returns a list of elements sorted by the predicate.
> (sort < '(8 2 5 2 3))
(2 2 3 5 8)
> (sort > '(8 2 5 2 3))
(8 5 3 2 2)
> (sort > '(8 2 7 5 12 1 24 42 130 3 4))
(1 2 3 4 5 7 8 12 24 42 130)

(define (sort predicate lon)
  (if (null? lon)
      '()
      (insert predicate (car lon)
              (sort predicate (cdr lon)))))

(define (insert predicate n lon)
  (if (null? lon)
      (list n)
      (if (predicate n (car lon))
          (cons n lon)
          (cons (car lon)
                (insert predicate n (cdr lon))))))

Exercise 1.18 [***]
This exercise has three parts. Work them in order.
1. Define the procedure compose such that (compose p1 p2), where p1 and p2 are procedures of one argument, returns the composition of these procedures, specified by this equation:
((compose p1 p2) x) = (p1 (p2 x))
> ((compose car cdr) '(a b c d))
b

2. (car&cdr s slist errvalue) returns an expression that, when evaluated, produces the code for a procedure that takes a list with the same structure as slist and returns the value in the same position as the leftmost occurrence of s in slist. If s does not occur in slist, then errvalue is returned. Do this so that it generates procedure compositions.
> (car&cdr 'a '(a b c) 'fail)
car
> (car&cdr 'c '(a b c) 'fail)
(compose car (compose cdr cdr))
> (car&cdr 'dog '(cat lion (fish dog ()) pig) 'fail)
(compose car (compose cdr (compose car (compose cdr cdr))))
> (car&cdr 'a '(b c) 'fail)
Fail

3. Define car&cdr2, which behaves like car&cdr, but does not use compose in its output.
Exercise 1.19 [**]
Write a procedure free-vars that takes a list structure representing an expression in the lambda calculus syntax given above and returns a set (a list without duplicates) of all the variables that occur free in the expression. Similarly, write a procedure bound-vars that returns a set of all the variables that occur bound in its argument.

Exercise 1.20 [*]
Give an example of a lambda calculus expression in which a variable occurs free but which has a value that is independent of the value of the free variable.
Exercise 1.21 [*]
Give an example of a lambda calculus expression in which the same variable occurs both bound and free.
Exercise 1.22 [*]
Scheme lambda expressions may have any number of formal parameters, and Scheme procedure calls may have any number of operands. Modify the formal definitions of occurs free and occurs bound to allow lambda expressions with any number of formal parameters and procedure calls with any number of operands. Then modify the procedures occurs-free? and occurs-bound? to follow these new definitions.
Exercise 1.23 [*]
Extend the formal definitions of occurs free and occurs bound to include if expressions.
Exercise 1.24 [**]
Extend the formal definitions of occurs free and occurs bound to include Scheme let and let* expressions.
Exercise 1.25 [*]
Extend the formal definitions of occurs free and occurs bound to include Scheme quotations (expressions of the form (quote <datum>)).
Exercise 1.26 [**]
Extend the formal definitions of occurs free and occurs bound to include Scheme assignment (set !) expressions.

Exercise 1.31 [**]
Consider the subset of Scheme specified by the BNF rules
<expression>	::= <identifier>
			::= (if <expression> <expression> <expression>)
			::= (lambda ({<identifier>}*) <expression>)
			::= ({<expression>}*)
Write a procedure lexical-address that takes any expression and returns the expression with every variable reference v replaced by a list (v : d p), as above. If the variable reference v is free, produce the list (v free) instead.
> (lexical-address '(lambda (a b c)
                    (if (eqv? b c)
                        ((lambda (c)
                           (cons a c))
                         a)
                        b)))

(lambda (a b c)
  (if ((eqv? free) (b : 0 1) (c : 0 2))
      ((lambda (c)       
         ((cons free) (a : 1 0) (c : 0 0)))
       (a : 0 0))
      (b : 0 1)))


Chapter 2
Exercise 2.4 [*]
Implement a bintree-to-list procedure for binary trees, so that (bintree-to-list (interior-node 'a (leaf-node 3) (leaf-node 4))) returns the list 		 	 	 		



(interior-node a (leaf-node 3) (leaf-node 4))

(define-datatype bintree bintree?
  (leaf-node (datum number?))
  (interior-node
   (key symbol?)
   (left bintree?)
   (right bintree?)))

(define (bintree-to-list tree)
  (cases bintree tree
    (leaf-node (datum) (list 'leaf-node datum))
    (interior-node (key left right)
                   (list 'interior
                         key
                         (bintree-to-list left)
                         (bintree-to-list right)))))

Exercise 2.5 [**]
Use cases to write max-interior, which takes a binary tree of numbers with at least one interior node and returns the symbol associated with an interior node with a maximal leaf sum.
> (define tree-a (interior-node 'a (leaf-node 2) (leaf-node 3)))
> (define tree-b (interior-node 'b (leaf-node -1) tree-a))
> (define tree-c (interior-node 'c tree-b (leaf-node 1)))
> (max-interior tree-b)
a
> (max-interior tree-c)
c

#lang eopl

(define-datatype bintree bintree?
  (leaf-node (datum number?))
  (interior-node
   (key symbol?)
   (left bintree?)
   (right bintree?)))

(define (sum tree)
  (cases bintree tree
    (leaf-node (v) v)
    (interior-node (sym left right)
                   (+ (sum left) (sum right)))))

(define (interior-node? x)
  (cond
    ((not (bintree? x)) #f)
    (else (cases bintree x
            (leaf-node (v) #f)
            (interior-node (a b c) #t)))))

(define (max-interior tree)
  (cases bintree tree
    (interior-node (sym left right)
                   (let ((left-sum (sum left))
                         (right-sum (sum right)))
                     (cond
                       ((and (interior-node? left) (> left-sum (+ left-sum right-sum)))
                        (max-interior left))
                       ((and (interior-node? right) (> right-sum (+ left-sum right-sum)))
                        (max-interior right))
                       (else sym))))
    (leaf-node #f)))

(define tree-a (interior-node 'a (leaf-node 2) (leaf-node 3)))
(define tree-b (interior-node 'b (leaf-node -1) tree-a))
(define tree-c (interior-node 'c tree-b (leaf-node 1)))
Exercise 2.6 [*]
Draw the abstract syntax tree for the lambda calculus expression
((lambda (a) (a b)) c)





<expression>	::= <number>
    lit-exp (datum)
::= <var-exp>
			    var-exp (id)
			::= <if <expression> <expression> <expression>>
			    if-exp (test-exp true-exp false-exp)
::= (lambda ({<identifier>}*) <expression>)
			    lambda-exp (ids body)
			::= (<expression> {<expression>}*)
			    app-exp (rator rands)
Exercise 2.7 [**]
Define the data type and parse and unparse procedures for the above grammar. Then implement lexical-address of exercise 1.31 using abstract syntax. It will be helpful to add two new variants
(lex-info  
(id symbol?)  
(depth number?)
(position number?))

(free-info
(id symbol?))


representing the translation of a given bound or free variable reference. The value returned by lexical-address may then be generated using an unparse procedure that takes an abstract syntax tree of the form indicated by the above grammar, but with lex-info and free-info variants in place of var-exp variants.

#lang eopl

(define (list-of pred)
  (lambda (lst)
    (or (null? lst)
        (and (pair? lst)
             (pred (car lst))
             ((list-of pred) (cdr lst))))))


(define-datatype expression expression?
  (lit-exp
   (datum number?))
  (var-exp
   (id symbol?))
  (if-exp
   (test-exp expression?)
   (true-exp expression?)
   (false-exp expression?))
  (lambda-exp
   (ids (list-of symbol?))
   (body expression?))
  (app-exp
   (rator expression?)
   (rands (list-of expression?))))

(define (parse-expression datum)
  (cond
    ((number? datum) (lit-exp datum))
    ((symbol? datum) (var-exp datum))
    (else
     (if (pair? datum)
         (cond
           ((eqv? (car datum) 'if)
            (if-exp (parse-expression (cadr datum))
                    (parse-expression (caddr datum))
                    (parse-expression (cadddr datum))))
           ((eqv? (car datum) 'lambda)
            (lambda-exp (cadr datum) (parse-expression (caddr datum))))
           (else (app-exp (parse-expression (car datum)) (map parse-expression (cdr datum)))))
         (eopl:error 'parse-expression
                     "Invalid concrete syntax ~s" datum)))))

(define (unparse-expression exp)
  (cases expression exp
    (lit-exp (n) n)
    (var-exp (id) id)
    (if-exp (test-exp true-exp false-exp)
            (list 'if (unparse-expression test-exp)
                  (unparse-expression true-exp)
                  (unparse-expression false-exp)))
    (lambda-exp (ids body)
                (list 'lambda ids (unparse-expression body)))
    (app-exp (rator rands)
             (list (unparse-expression rator) (map unparse-expression rands)))))

> (parse-expression '((lambda (a b) (if a b c)) c d))
#(struct:app-exp
  #(struct:lambda-exp
    (a b)
    #(struct:if-exp #(struct:var-exp a) #(struct:var-exp b) #(struct:var-exp c)))
  (#(struct:var-exp c) #(struct:var-exp d)))
> (unparse-expression  (parse-expression '((lambda (a b) (if a b c)) c d)))
((lambda (a b) (if a b c)) (c d))

Chapter 3
Exercise 3.3 [*]
Write parse-program. See section 2.2.2.
Exercise 3.4 [*]
Test eval-program using both run and a read-eval-print loop.
Exercise 3.5 [*]
Extend the language by adding a new primitive operator print that takes one argument, prints it, and returns the integer 1.
Exercise 3.6 [*]
Extend the language by adding a new primitive operator minus that takes one argument, n, and returns −n.
> (run "minus (+(minus(5), 9))")
-4
Exercise 3.7 [*]
Add list processing primitives to the language, including cons, car, cdr, list, and a new variable, emptylist, which is bound to the empty list. Since there is no support for symbols, lists can contain only numbers and other lists. How does this change the expressed and denoted values of the language?
> (run "list(1, 2, 3)")
(1 2 3)
> (run "car(cons(4, emptylist))")
4
> (run "cdr(cons(4, emptylist))")
()				
