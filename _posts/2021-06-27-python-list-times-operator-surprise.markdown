---
layout: post
title:  "Python list multiplication (*) operator surprise"
date:   2021-06-27 00:00:00
categories: python list operator
---

What do you think the following code will output?

```python
mat = [[0] * 2] * 3
mat[0][0] = 1
print(mat) # what is the value of mat now?
```

It may or may not surprise you, but the result is:
`[[1, 0], [1, 0], [1, 0]]`
instead of
`[[1, 0], [0, 0], [0, 0]]`

Why? Because, _I think_, the multiplication (`*`) operator takes the reference of the original list (`[0] * 2`) and duplicates it `3` times to produce `mat`, so the 3 lists in `mat` are pointing to the same list, that's why when you change a value in the first list in `mat`, all the remaining 2 lists are affected as well.

Not really what you wanted? `list comprehension` will probably give what you were looking for:

```python
matfor = [[0] * 2 for _ in range(3)]
matfor[0][0] = 1
print(matfor) # [[1, 0], [0, 0], [0, 0]]`
```

The list comprehension will create a new list in each iteration of the `for` loop.

