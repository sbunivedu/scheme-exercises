
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
