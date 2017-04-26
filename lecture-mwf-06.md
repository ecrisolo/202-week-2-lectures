# Trees III (we were doing them all the time)

## Defining Trees

Okay, it's time to talk some more about trees.

What? We weren't talking about trees? Ah... but in fact, we *were*, because
lists are actually just a particular kind of tree.

Hold on, I'm getting ahead of myself. Let's draw some pictures.

Here's the picture we had for a list, last time:

```
  O
 / \
2   O
   / \
 27   O
     / \
   22   "mt"
```

Let's try drawing a different kind of picture. Instead of representing
a list of numbers, this picture is going to represent your genealogical
tree. You're going to be at the top, or "root", of this tree. For the
most part, each of us has two biological parents. Are any of you clones?
Okay, no. I think that joke isn't offensive. I apologize if any of
you are clones.

Okay, let's do my family tree, just to stay out of trouble. My name is
John. My mother and father are named Jennifer and Robert. I could draw
this like so:

```
        John
        /  \
 Jennifer  Robert
```

But, of course, each of them has a mother and father. Like this:


```
             John
            /    \
           /      \
    Jennifer      Robert
     /   \        /   \
Valeria Gerald Helen Robert
```

Could we keep going? Well, eventually we're going to hit the boundaries
of our knowledge. Let's say that we put `"unk"`, standing for "unknown",
in spots where we don't know the answer:

```
                       John
                      /    \
                    /        \
           Jennifer            Robert
          /   \                  /   \
         /     \                /     \
  Valeria      Gerald      Helen       Robert
    / \         / \         / \         / \
"unk" "unk" "unk" "unk" "unk" "unk" "unk" "unk"

```

Okay, let's take this picture, and try to map it back onto a data definition.

(let them try. Eventually get to something like this:)

```python
# a FamilyTree is one of
# - "unk", or
# - Person(name, mother, father)
class Person:
  def __init__(self, name, mother, father):
    self.name = name     # str
    self.mother = mother # FamilyTree
    self.father = father # FamilyTree

  # BOILERPLATE OMITTED
```

(Draw the self-reference arrows from "FamilyTree" up to "FamilyTree" at the
top.)

It's worth noting here that we should not use the same name for the data
definition (`FamilyTree`) and for the class that represents a known family
tree: `Person`. You can pick different names if you like, but don't pick
the same name; these are two different things. One is an object with three
fields, the other one is *either* one of those objects *or* the string
"unk". Different.


Okay, so: can you write (part of) your family tree using this data definition?
Make sure to include at least one Grandparent. Make up names, if you want to.

```python
Person("John",Person("Jennifer",Person("Valeria","unk","unk"),
                                Person("Gerald","unk","unk"))
              Person("Robert","unk","unk))
```

## Functions on Trees

So, how is the data definition for a FamilyTree different from the data
definition for a StrList? (discuss.) Structurally, the only difference is that they
have two self-references, rather than one.

So, how should the template change? Let's start writing a function, and come
back to this question when we get to the template step.

In fact, just to highlight the similarities, let's write the same
function that we did for string lists: `contains_dark`. I understand
that it's a bit unusual to have an ancestor named `"dark"`, but what
the heck.

Step one: data definitions. Okay, we did that already!

Step two: signature, purpose statement, header:

(have them write it first, or produce it with you:)

```python
# FamilyTree -> bool
# determine whether there's a person named "dark" in the family tree.
def ft_contains_dark(tree):


def test_ft_contains_dark(self):
  self.assertEqual(ft_contains_dark(Person("John",Person("Bob","unk",
                                                         Person("dark","unk","unk")),
                                                  "unk")),
                   True)
  self.assertEqual(ft_contains_dark(Person("John",Person("Bob","unk",
                                                         Person("zigzig","unk","unk")),
                                                  "unk")),
                   False)
  
```

Okay, ready for the template step? This is the exciting part! Well, it's exciting
the first time. After that it's just cut & paste.

How many branches should the template have? (Q) That's right, two! How do you
know? Because there are two branches in the data definition.

```python
def ft_contains_dark(tree):
  if (tree == "unk") :
    ...
  else:
   ...
```

Okay, are either of these two choices compound data? Yes! The second one
is an object. Let's add the fields of that object:

```python
def ft_contains_dark(tree):
  if (tree == "unk") :
    ...
  else:
    ... tree.name ... tree.mother ... tree.father ...
```

Are we done? No. We need to add the recursive calls on the self-referential
elements of the structure:

```python
def ft_contains_dark(tree):
  if (tree == "unk") :
    ...
  else:
    ... tree.name ... ft_contains_dark(tree.mother) ... ft_contains_dark(tree.father) ...
```

Okay, that's it for the template.

Step five: finish the program. How's this going to work? (Q) That's right: it's
just like the last one. Use an `or` or some `if`s. It's up to you.

Simple generalization: contains_named. Accepts string, returns true if there's
a person with that name in the tree.

Sneaky question: what if we use contains_named to search for the named "unk".
What will happen? What *should* happen? (Q) That's right, it *won't* return
true unless there's a person with that name. Is that the right behavior?
Yes! 

At this point, you're ready to write a whole heck of a lot of functions on
trees.

Fold-like:

- find the longest name in the tree. Good to have a helper function here.
- count the # of people whose names are longer than 10 chars.
- Find someone named "Bob". Like 'find-p' on lists, this requires
  a new data definition for the result. Unlike the list one, you actually
  need to test for the fail value, to see whether to recur on the other list.

Tree-producing:

- truncate every name at 'n' characters.
- discard every person below grandparent (requires accumulator, but it's
pretty obvious?)

List-producing:

- pre-order, post-order, in-order (requires append, from earlier lab)

## Using lists and trees to store information

--

Copyright (C) 2017, John Clements (clements@racket-lang.org)
