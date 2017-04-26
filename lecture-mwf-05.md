# Lists II -- functions on lists (which are actually trees)

So we've seen lists, and had a bit of practice writing functions on them.

It turns out that this basic formula can be applied to a whole bunch of
functions on lists.

### easy folds:

- length of list
- sum of list
- largest of list

This last one probably needs a helper function: max.

### show accumulator style for one of these. e.g.:

```python
# NumList -> number
# sum the numbers in the list
def list_sum(numlist):
  if (numlist == "mt"):
    return 0
  else:
    return numlist.first + list_sum(numlist.rest)
```

vs

```python
# NumList number -> number
# add the given number to the sum of the numbers in the list
def list_sum_accum(numlist, sum):
  if (numlist == "mt"):
    return sum
  else:
    return list_sum(numlist.rest,numlist.first + sum)
```

How are they different? The second one is a bit less obvious, but has an
advantage when it comes to very large lists. We'll discuss this more later.

A function producing mixed data:

- find: return a string starting with 'p' if there is one, otherwise
  return false.

This one requires a new data definition for the result.

### Next, functions producing lists:

Simple map:

- add one to each number in a list
- append "ZZ" to each string in a list

What happens when we try to write these in an accumulator style? They come
out backward! Scary.

Simple filter:

- return a sublist containing the elements less than 9 
- return a sublist containing the strings that have an X in them

Finally, with applications to sorting:

- insert into a sorted list

Possibly part of lab: append.

--

Copyright (C) 2017, John Clements (clements@racket-lang.org)
