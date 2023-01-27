Scheme programming exercises from a [course](https://homes.cs.aau.dk/~normark/pp/index.html) taught by Kurt Nørmark.

## Exercise 1.4 A Proper List Predicate *

The Scheme function (predicate) `pair?` tells if its parameter is a pair (constructed by `cons`). The function `null?` tells if its parameter is the empty list. The function `list?` tells if its parameter is a proper list. In this exercise, write your own version of `list?` Let us call our function `proper-list?`

Intuitively, a proper list is empty, or it is 'ended by' an empty list (by following the `cdr` chain).

You may want to read about [pairs and lists](http://people.cs.aau.dk/~normark/laml-distributions/laml/r5rs/r5rs_8.html#SEC59) in the Scheme Report.

Can you write your predicate without thinking (and programming) in terms of a while loop?

Can you write your predicate without using `if` or `cond`?

Is your function efficient? What is the time complexity?

__Please - consider these questions carefully.__

<details>
<summary>Solution</summary>

Here is a solution programmed with `or` and `and`:

```scheme
(define (proper-list? x)
  (or (null? x)
      (and (pair? x) (proper-list? (cdr x)))))
```

It is, of course, also possible to write the function using `if`.

```scheme
(define (proper-list-1? x)
  (if (null? x)
      #t
      (if (pair? x)
          (proper-list-1? (cdr x))
          #f)))
```
This version is more verbose, and less elegant. Both versions are memory efficient (tail recursive). Notice, however, that it is rather costly to use the proper list predicate, because it will have to iterate to the end of the `cdr` chain to find out if the list is proper or not. There are no possible shortcuts as long as we rely on (single) linked lists.

</details>

## Exercise 1.5 Construction of symbolic expressions *

Construct the symbolic expressions illustrated on [this page](http://people.cs.aau.dk/~normark/pp/fp-intro-scheme-note-s-expressions.html) via the `cons` primitive in Scheme. The entities named `a` through `h` should be symbols. As a help, the rightmost part of the structure is made by `(cons 'g 'h)`. `'g` is equivalent to `(quote g)`, meaning that `g` is not evaluated, but taken for face value.

Experiment with `h` being the empty list, which is denoted `'()`.

Try to use a proper list function, such as `length`, on your structures. Can you make sense of the result?

<details>
<summary>Solution</summary>

The following is a name binding form with the left and right branch of the structure:

```scheme
(let ((left (cons (cons 'a 'b) (cons 'c 'd)))
      (right (cons (cons 'e 'f) (cons 'g 'h))))
  (cons left right))
```
It is, of course, also possible to make one nasty expression with `cons`'es:

```scheme
(cons (cons (cons 'a 'b) (cons 'c 'd)) (cons (cons 'e 'f) (cons 'g 'h)))

(cons (cons (cons 'a 'b)
            (cons 'c 'd))
      (cons (cons 'e 'f) (cons 'g 'h)))
```

Please notice the way the Scheme interpreter prints the value of the expression. Try find the rule it applies for printing.

</details>

## Exercise 1.5 Every second element of a list **

Write a function, `every-second-element`, that returns every second element of a list. As examples

```sheme
(every-second-element '(a b c)) => (a c)
(every-second-element '(a b c d)) => (a c)
```

Do you see any use of this function when we work with __property__ lists?

It is often worthwhile to go for a _more general solution_ than actually needed. Therefore, program `(every-nth-element n lst)` where `n >= 1` and `lst` is a proper list.

Can you define `every-second-element` in terms of `every-nth-element`?

<details>
<summary>Solution</summary>

```scheme
;; Return every second element of list, starting with the first element.
(define (every-second-element lst)
  (cond ((null? lst) '())
        ((null? (cdr lst)) (list (car lst)))
        (else (cons (car lst) (every-second-element (cddr lst))))))

; Return every n-th element of lst. Assume as a pre-condition that n >= 1.
(define (every-nth-element n lst)
  (if (shorter-than? n lst)
      (list (car lst))
      (cons (car lst) (every-nth-element n (list-tail lst n)))))

; Expensive variant.
(define (shorter-than? n lst)
  (< (length lst) n))

; Does lst have n elements, or fewer?
(define (shorter-than? n lst)
  (or (and (null? lst) (>= n 0))
      (and (not (null? lst)) (shorter-than? (- n 1) (cdr lst)))))

; An alternative with cond
(define (shorter-than? n lst)
  (cond ((null? lst) (>= n 0))
        ((not (null? lst)) (shorter-than? (- n 1) (cdr lst)))))

; Return every n-th element of lst. Assume as a pre-condition that n >= 1.
(define (every-nth-element n lst)
  (cond ((null? lst) '())
        ((shorter-than? n lst) (list (car lst)))
        (else (cons (car lst) (every-nth-element n (list-tail lst n))))))

(define (every-second-element lst)
  (every-nth-element 2 lst))

; Does not work - calls for currying.
; (define every-second-element (every-nth-element 2))
```

The function `every-second-element` is useful to extract the keys or values of a property list.

</details>

<details>
<summary>Solution</summary>

```scheme
(define (helper n i lst)
  (if (null? lst)
      '()
      (if (= i 1)
          (cons (car lst)
                (helper n n (cdr lst)))
          (helper n (- i 1) (cdr lst)))))
      

(define (every-second-element lst)
  (every-nth-element 2 lst))

(every-nth-element 2 '(1 2 3 4 5 6))
```

</details>

## Exercise 1.8 Creation of association lists *

Program a function pair-up that constructs an association list from a list of keys and a list of values. As an example

```scheme
  (pair-up '(a b c) (list 1 2 3))
```

should return

```
  ((a . 1) (b . 2) (c . 3))
```

Think of a reasonable solution in case the length of the key list is different from the length of the value list.

</details>

<details>
<summary>Solution</summary>

```scheme
; Return the association list formed by keys from key-list and values from value-list.
; Terminate when the shortest of the lists is exhausted.
(define (pair-up key-list value-list)
  (if (or (null? key-list) (null? value-list))
      '()
      (cons
       (cons (car key-list) (car value-list))
       (pair-up (cdr key-list) (cdr value-list)))))
```
</details>

Association lists have been introduced at [this slide](https://homes.cs.aau.dk/~normark/pp/fp-intro-scheme-slide-alists.html). An association list is a list of keyword-value pairs (a list of cons cells).

Property lists are closely related to association lists. See [the slide about property lists](https://homes.cs.aau.dk/~normark/pp/fp-intro-scheme-slide-proplist.html). A property list is a 'flat list' of even length with alternating keys and values.

The property list corresponding to the following association list
```scheme
  ((a . 1) (b . 2) (c . 3))
```
is
```
  (a 1 b 2 c 3)
```

Program a function that converts an association list to a property list. Next, program the function that converts a property list to an association list.

<details>
<summary>Solution</summary>

```scheme
;; Make and return an association list from a property list plist.
(define (propertylist-to-alist plist)
  (let ((lgt (length plist)))
   (cond ((null? plist) '())
         ((= 1 lgt) (error "propertylist-to-a-list called with list of odd length. A property list is always of even length"))
         ((>= lgt 2) (cons (cons (car plist) (cadr plist)) (propertylist-to-alist (cddr plist)))))))

;; Make and return a property list from an association list.
(define (alist-to-propertylist alist)
  (cond ((null? alist) '())
        (else (cons (car (car alist)) (cons (cdr (car alist)) (alist-to-propertylist (cdr alist)))))))
```
</details>

<details>
<summary>Solution</summary>

```scheme
(define (alist-list alst)
  (if (null? alst)
      '()
      (cons (caar alst)
            (cons (cdar alst)
                  (alist-list (cdr alst)))))) 

(define (list-alist lst)
  (if (null? lst)
      '()
      (cons (cons (car lst)
                  (cadr lst))
            (list-alist (cddr lst)))))

(alist-list '((a . 1) (b . 2) (c . 3)))

(list-alist '(a 1 b 2 c 3))
```
</details>

## Exercise 1.9 The get-prop function for property lists *

The R5RS Scheme functions [`assoc`](http://people.cs.aau.dk/~normark/laml-distributions/laml/r5rs/r5rs_8.html#IDX343), `assq`, and `assv` access pairs in association lists. In this exercise we will write a similar function for property lists. More specifically, write a function `get-prop` with the following signature:
```
(get-prop key property-list)
```

The function should return the value of key in property-list. Like `assoc`, `get-prop` should return `#f` if key is not found in `property-list`. Here is an example REPL session with `get-prop`:

```
> (define weekday-plist (list 'monday 1 'tuesday 2 'wednesday 3 'thursday 4 'friday 5 'saturday 6 'sunday 7))
> (get-prop 'wednesday weekday-plist)
3
> (get-prop 'sunday weekday-plist)
7
> (get-prop 'january weekday-plist)
#f
```

How will you handle the case where the property list is malformed - a property list with an odd number of elements?

Discuss pros and cons of property lists and `get-prop`, compared to association lists and `assoc`.

Does the `#f` value, returned in cases where we do not find the key, bother you?

<details>
<summary>Solution</summary>

Here is a possible solution:

```scheme
(define (get-prop key property-list)
  (cond ((null? property-list) #f)
        ((null? (cdr property-list))
           (error "Malformed property list"))
        ((equal? key (car property-list))
           (cadr property-list))
        (else (get-prop key (cdr (cdr property-list))))))
```

I see a number of problems and issues with property lists and `get-prop`:

* We need to take care of ill-formed property lists, where there is an odd number of elements in the list.
* R5RS offer three different variants of `assoc`, for different equality funtions. We could do the same here, but it would be tedious. Alternatively, we could go for a solution based higher-order function solution, where we generate `get-prop` with an appropriate comparison function. See exercise 2.24.
* If the property list relates the key to boolean false, ambiguities arise. The design of `assoc` avoids this (to some degree, at least) because it returns the cons pair of `(key . value)` in case of success.

The last observation could give rise to an alternative solution, which returns a list of `(key value)` in case key is found, and `#f` if the key is not found:

```scheme
(define (get-prop key property-list)
  (cond ((null? property-list) #f)
        ((null? (cdr property-list))
           (error "Malformed property list"))
        ((equal? key (car property-list))
           (list key (car (cdr property-list))))
        (else (get-prop key (cdr (cdr property-list))))))
```

However, this requires allocation of two new cons cells (in contrast to association lists, where no new cons cell allocation is necessary). Please understand this.

</details>

<details>
<summary>Solution</summary>

```scheme
(define (get-prop key plist)
  (if (null? plist)
      #f
      (if (equal? key (car plist))
          (cadr plist)
          (get-prop key (cddr plist)))))

(define weekday-plist (list 'monday 1 'tuesday 2 'wednesday 3 'thursday 4 'friday 5 'saturday 6 'sunday 7))
(get-prop 'wednesday weekday-plist)
```
</details>

## Exercise 1.10 Parameter passing in Scheme *

Exercise 1.10
Parameter passing in Scheme *

Familiarize yourself with the parameter passing rules of Scheme by trying out the following calls:

```scheme
((lambda (x y z) (list x y z)) 1 2 3)
((lambda (x y z) (list x y z)) 1 2)
((lambda (x y z) (list x y z)) 1 2 3 4)
((lambda (x y z . r) (list x y z r)) 1 2 3)
((lambda (x y z . r) (list x y z r)) 1 2)
((lambda (x y z . r) (list x y z r)) 1 2 3 4)
((lambda r r) 1 2 3)
((lambda r r) 1 2)
((lambda r r) 1 2 3 4)
```
Be sure that you can explain all the results.

<details>
<summary>Solution</summary>

```
((lambda (x y z) (list x y z)) 1 2 3) -> (list 1 2 3)
((lambda (x y z) (list x y z)) 1 2) -> arity mismatch
((lambda (x y z) (list x y z)) 1 2 3 4) -> arity mismatch
((lambda (x y z . r) (list x y z r)) 1 2 3) -> (1 2 3 ())
((lambda (x y z . r) (list x y z r)) 1 2) -> arity mismatch
((lambda (x y z . r) (list x y z r)) 1 2 3 4) -> (1 2 3 (4))
((lambda r r) 1 2 3) -> (1 2 3)
((lambda r r) 1 2) -> (1 2)
((lambda r r) 1 2 3 4) -> (1 2 3 4)
```

</details>

## Exercise 1.16 A counterpart to list-tail **

The Scheme function list-tail returns the n'th tail of a list:

```scheme
> (list-tail '(a b c d e)  2)
(c d e)
> (list-tail '(a b c d e)  0)
(a b c d e)
> (list-tail '(a b c d e)  4)
(e)
```

Program your own version of `list-tail`. Call it `my-list-tail`.

Next, program a function `list-prefix` which is a prefix counterpart to `list-tail`. `(list-prefix lst n)` returns a prefix of the list of length `n`:

```scheme
> (list-prefix '(a b c d e)  2)
(a b)
> (list-prefix '(a b c d e)  4)
(a b c d)
> (list-prefix '(a b c d e)  0)
() 
```

Do not just use `reverse` and/or `length` in clever ways. Make 'a real implementation' of `list-prefix`.

Reflect on the difference between these two functions. Which one is most expensive in terms of memory allocation?

<details>
<summary>Solution</summary>

Here are my functions:
```scheme
(define (my-list-tail lst n)
  (if (= n 0)
      lst
      (my-list-tail (cdr lst) (- n 1))))

(define (list-prefix lst n)
  (if (= n 0)
      '()
      (cons (car lst) (list-prefix (cdr lst) (- n 1)))))
```
Notice that we must allocate memory to the result in `list-prefix`, whereas `my-list-tail` returns part of the original list (without allocating additional space).

</details>

<details>
<summary>Solution</summary>

```scheme
(define (my-list-tail lst n)
  (if (> n (length lst))
      (error "index too large for list")
      (if (= n 0)
          lst
          (my-list-tail (cdr lst) (- n 1)))))

(my-list-tail '(a b c d e)  2)
(my-list-tail '(a b c d e)  0)
(my-list-tail '(a b c d e)  4)
(my-list-tail '(a b c d e)  5)
;(my-list-tail '(a b c d e)  6)

(define (list-prefix lst n)
  (cond ((> n (length lst)) lst)
        ((= n 0) '())
        ((< n 0) (error "invalid length parameter"))
        (else (cons (car lst)
                    (list-prefix (cdr lst) (- n 1))))))

(list-prefix '(a b c d e)  2)
(list-prefix '(a b c d e)  4)
(list-prefix '(a b c d e)  0)
```
</details>

## The function butlast **
R5RS has functions such that `list-ref` and `list-tail`. `(list-ref lst n)` returns element number n in a list, `(list-tail lst k)` returns a suffix of list (omitting `k` elements). In this exercise we will program a function `butlast`. `(butlast lst)` returns all elements of lst apart from the last element. Thus, `butlast` computes the prefix of the list consisting of all elements but the last one. Needless to say, `butlast` assumes as a precondition that the input list is non-empty.

Let us notice that it is harder to compute the prefix of a list than a suffix of a list. Why?

The following implementation of `butlast` is tempting, but inefficient (quick and dirty):
```scheme
(define (butlast lst)
  (reverse (cdr (reverse lst)))))
```
Now, program butlast with a single traversal of the list.

Can you generalize `butlast` to compute the prefix of a list apart from the last `n` elements:
```scheme
(define (but-n-last lst n)
   ...))
```
Please consider the similarity between `but-n-last` and `list-prefix` from one of the other exercises.

<details>
<summary>Solution</summary>

Here is an implementation of `butlast` and `but-n-last`:
```scheme
(define (butlast lst)
  (cond ((null? lst)
           (error "Cannot apply butlast on an empty list"))
        ((null? (cdr lst))  ; a list of a single element
           '())
        (else (cons (car lst) (butlast (cdr lst))))))

(define (but-n-last lst n)
  (cond ((< (length lst) n)
           (error "The list is too short for but-n-last"))
        ((= (length lst) n)
           '())
        (else (cons (car lst) (but-n-last (cdr lst) n)))))
```
</details>

## Exercise 2.3 The append function **
The function `append`, which is a standard Scheme function, concatenates two or more lists. Let us here show a version which appends two lists:

```scheme
(define (my-append lst1 lst2)
     (cond ((null? lst1) lst2)
           (else (cons (car lst1) (my-append (cdr lst1) lst2)))))
```

We will now challenge ourselves by programming an iterative solution, by means of tail recursion. We start with the standard setup:

```scheme
(define (my-next-append lst1 lst2)
  (my-next-append-1 lst1 lst2 ...))
```

where `my-next-append-1` is going to be the tail recursive function:

```scheme
(define (my-next-append-1 lst1 lst2 res)
  (cond ((null? lst1) ...)
        (else (my-next-append-1 (cdr lst1) lst2 ...))))
```

Fill out the details, and try out your solution.

You may encounter a couple of problems! Do your best to work around these problems, maybe by changing aspects of the templates I have given above.

<details>
<summary>Solution</summary>

One common problem with iterative solutions and tail recursive functions is that lists will be built in the "wrong order". This is due to our use of `cons` to construct lists, and the fact that `cons` operates on the front end of the list. The common medicine is to reverse a list, using the function `reverse`, either on the input, or on the output.

Here is my solution

```scheme
  (define (my-next-append lst1 lst2)
    (my-next-append-1 (reverse lst1) lst2 lst2))

  (define (my-next-append-1 lst1 lst2 res)
    (cond ((null? lst1) res)
          (else (my-next-append-1 
                  (cdr lst1) lst2 (cons (car lst1) res)))))
```

In order to compensate for the 'reversing problem' I pass `lst1` in reversed form to the iterative function `my-next-append-1`.

Also I pass the list `lst2` as the initial value of `res`. This is crucial in order to get the lists appended at all.

As an additional observation, the second parameter of `my-next-append-1` is not used. Thus, it can be eliminated. I have not done so, however.

</details>

## Exercise 2.3 A list replication function **
Write a tail recursive function called `replicate-to-length`, which in a cyclic way (if necessary) replicates the elements in a list until the resulting list is of a certain exact length. You should think of `replicate-to-length` as a function that iterates over the elements of the list, repeatedly, if necessary. You will probably need to write a helping function to `replicate-to-length`, which is tail recursive.

The following serves as an example:
```scheme
(replicate-to-length '(a b c) 8) =>
(a b c a b c a b)
(replicate-to-length '(a b c) 2) =>
(a b)
```
  
In other words, in `(replicate-to-length lst n)`, take elements out of `lst`, cyclic if necessary, until you reach `n` elements.

<details>
<summary>Solution</summary>

```scheme
;; Replicate lst cyclically to a list of the exact length lgt
;; The helping function builds the list in reversed order. This is compensated by reversing its result.
(define (replicate-to-length lst lgt)
  (reverse (replicate-to-length-1 lst lst '() 0 lgt)))

; Helping function to replicate-to-length.
; original-lst is constant through this function.
; The elements are taken out of lst.
; The result is accumulated up in res.
; count goes from 0 to lgt.
(define (replicate-to-length-1 original-lst lst res count lgt)
 (cond ((null? lst) (replicate-to-length-1 original-lst original-lst res count lgt))
       ((< count lgt) (replicate-to-length-1 original-lst (cdr lst) (cons (car lst) res) (+ 1 count) lgt))
       (else res)))
```
</details>

<details>
<summary>Solution</summary>

```scheme
(define (replicate-to-length lst n)
  (replicate-to-length-1 lst n '()))

(define (replicate-to-length-1 lst n acc)
  (if (= n 0)
      acc
      (replicate-to-length-1
       lst
       (- n 1)
       (cons (list-ref lst (remainder (- n 1) (length lst)))
             acc))))


(replicate-to-length '(a b c) 8)
(replicate-to-length '(a b c) 2)
(replicate-to-length '(a b c) 0)
```
</details>

## Exercise 2.3 More about string-merge *

We have seen two variants of string-merge as follows:

```scheme
(define (string-merge str-list-1 str-list-2)
 (cond ((null? str-list-1) (apply string-append str-list-2))
       ((null? str-list-2) (apply string-append str-list-1))
       (else (string-append
              (car str-list-1) (car str-list-2)
              (string-merge (cdr str-list-1) (cdr str-list-2))))))
              
; tail recursive

(define (string-merge-iter str-list-1 str-list-2)
(merge-iter-helper  str-list-1 str-list-2 ""))

(define (merge-iter-helper str-list-1 str-list-2 res)
(cond ((null? str-list-1) 
         (string-append res (apply string-append str-list-2)))
      ((null? str-list-2) 
         (string-append res (apply string-append str-list-1)))
      (else (merge-iter-helper
               (cdr str-list-1) 
               (cdr str-list-2)
               (string-append 
                   res (car str-list-1) (car str-list-2))))))

(string-merge '("a" "b" "c") '("A" "B" "C" "D" "E"))
```

Both make use of `(apply string-merge ...)` in the cases where one of the lists become empty. `apply` calls a function on a list of parameters. Thus `(apply + (list 1 2))` is equivalent to `(+ 1 2)`.

Eliminate the use of `apply`, such that `string-merge` (tail) recursively builds the end of the list by a number of calls to `string-append` (one for each element in the non-empty list).

<details>
<summary>Solution</summary>

```scheme
Here is a solution for the non-tail recursive variant of string-merge:

(define (string-merge str-list-1 str-list-2)
 (cond ((and (null? str-list-1) (not (null? str-list-2))) 
             (string-append (car str-list-2) (string-merge '() (cdr str-list-2))))
       ((and (not (null? str-list-1)) (null? str-list-2))
             (string-append (car str-list-1) (string-merge (cdr str-list-1) '())))
       ((and (null? str-list-1) (null? str-list-2)) "")
       (else (string-append
              (car str-list-1) (car str-list-2)
              (string-merge (cdr str-list-1) (cdr str-list-2))))))
```

</details>

<details>
<summary>Solution</summary>

```scheme 
(define (string-merge-iter str-list-1 str-list-2)
  (merge-iter-helper  str-list-1 str-list-2 ""))
 
(define (merge-iter-helper str-list-1 str-list-2 res)
  (cond ((and (null? str-list-1) (null? str-list-2))
         res)
        ((null? str-list-1)
         (merge-iter-helper
          str-list-1 
          (cdr str-list-2)
          (string-append res (car str-list-2))))
        ((null? str-list-2)
         (merge-iter-helper
          (cdr str-list-1) 
          str-list-2
          (string-append res (car str-list-1))))
        (else
         (merge-iter-helper
          (cdr str-list-1) 
          (cdr str-list-2)
          (string-append 
           res (car str-list-1) (car str-list-2))))))
 
(string-merge-iter  '("a" "b" "c")  '("A" "B" "C" "D" "E"))
```

</details>

## Exercise 2.4 Sublists of a list **
The first part of this exercise is similar to exercise 1.16.

In this exercise we will program a function `front-sublist` which returns the first n elements of a list. The signature (the head) of the function should be `(front-sublist lst n)` where `lst` is a list and `n` is a number. As a precondition it can be assumed that `lst` is a proper list and that `n` is a non-negative integer. As a postcondition we want to guarantee that the length of the result is `n`.

As an example
```scheme
(front-sublist '(a b c d e) 3)  =>
(a b c)
(front-sublist '(a b c d e) 6)  =>
ERROR
```
First, identify the extreme, border cases and be sure that you know how to handle these. Next, program the function with due respect to both the precondition and the postcondition. Next, test the function carefully in a dialogue with your Scheme system.

Given the function `front-sublist` we will program the function `sublists`, which breaks a proper list into a list of sublists of some given size. As an example
```scheme
(sublists '(a b c d e f) 3) =>
((a b c) (d e f))
```
Program the function `sublists` with use of `front-sublist`. Be careful to prepare you solution by recursive thinking. It means that you should be able to break the overall problem into a smaller problem of the same nature, as the original problem. You should feel free to formulate both preconditions and postconditions of the function sublists, such that the existing function `front-sublist` fits well.

Hint: The Scheme function `list-tail` is probably useful when you program the function sublists.

<details>
<summary>Solution</summary>

```scheme
; Precondition: lst is a proper list, n is an integer, n >= 0.
(define (front-sublist lst n)
  (cond ((= n 0) '())
        ((null? lst) (error "Too few elements in list"))
        ((not (null? lst))  
           (cons (car lst) (front-sublist (cdr lst) (- n 1))))))

; Precondition: The length of lst is a multiplum of n. n is an integer. n > 0.
(define (sublists lst n)
  (cond ((null? lst) '())
        (else (cons (front-sublist lst n) (sublists (list-tail lst n) n)))))
```
</details>

<details>
<summary>Solution</summary>

```scheme
(define (front-sublist lst n)
  (cond ((= n 0) '())
        ((null? lst) (error "ERROR"))
        (else (cons (car lst)
                    (front-sublist (cdr lst) (- n 1))))))

(front-sublist '(a b c d e) 3)
(front-sublist '(a b c d e) 0)
;(front-sublist '(a b c d e) 6)

(define (sublists lst n)
  (if (null? lst)
      '()
      (cons (front-sublist lst n)
            (sublists (list-tail lst n) n))))
      
(sublists '(a b c d e f) 3)
```
