# Lists (and, secretly, trees!)

Okay, today we're going to talk about lists. Actually, we're going to
talk about both more and less than lists. More, in the sense that we're
going to give you a structure that works well for many things besides
lists. Less, in the sense that we're going to delay discussion of
what I'll call "arrays."

## representing lists

Okay, here we go. We've talked about some basic values in the Python
language: integers, floating-point numbers, booleans, strings. We've
also talked about compound data: objects containing a set of named
fields.

Now, let's talk about lists. Specifically, consider a list of numbers,
maybe they're temperatures for the last 30 days.

Can we represent a list of numbers using one of these basic kinds of
data?

(discuss. Well, yes. But not nicely. We could jam them all into
a string, for instance. Or we could come up with a complex way of
interleaving the numbers as a gigantic number. But neither of these
is very nice.)

We could definitely represent them as an object with 30 fields.
They might be named "t1" through "t30". Is this a good idea?

(discuss. No, it's not. You can't generalize to different numbers
of days, and you can't do things like finding every seven-day
mean without terrible amounts of repetition.)

So, we want something that works for a list of any size. It turns
out that we can do this just fine. Maybe you already know the
trick.

Here it is.

We're going to pick a value to represent the empty list of numbers.
Let's use `"mt"`. See, like "empty". but shorter. okay, whatever.

That covers the empty list case. Can lists be nonempty? Sure they
can. What's in a nonempty list of numbers? Well, it has a first
element, which is a number. What kind of thing is the rest of
the list? Ah! It's a list of numbers! Was that obvious? Not if you
haven't seen it before.

Okay, here we go with a data definition:

```python
# a NumList is one of
# - "mt", or
# - Pair(first, rest)
class Pair:
  def __init__(self, first, rest):
    self.first = first   # a number
    self.rest = rest     # a NumList
  # BOILERPLATE OMITTED
```

Okay, that looks... strange. Let's try it out. Can we create examples of
this data definition? (Call on someone:) Give me a value that belongs to
this data definition. No, just pick the easy one. Yep, `"mt"`! (write it
on the board.) That's a list. In fact, it's probably the most important
list of all.

- `"mt"`

Okay, another? (call on someone else.) follow the data
definition. If we pick the first branch of the data definition again,
we'll just get `"mt"` again. Let's pick the second one. Gotta be "Pair" of
something that's a number, and something that's a NumList. Pick a number!
Okay, sure. 22. Okay, now pick a numlist! Well, we only know about one,
so far. That's `"mt"`. Let's use it:

- `Pair(22,"mt")`

Excellent. Okay, another? (Call on someone else. Let them suggest it.
Steer them away from using `"mt"` as the rest).

- `Pair(27,Pair(22,"mt"))`

Okay, now it's getting easier:

- `Pair(2,Pair(27,Pair(22,"mt")))`

Worth doing a longer one? Maybe, maybe not.

Okay, so it looks like this works. Can we represent a list of arbitrary
length, this way? Yes. What about this one?

`Pair("mt","mt")`

That's right, it doesn't match the data definition. What about this one?

`Pair(34,87)`

That one also doesn't match the data definition. This is one of those
places where a type system could steer you away from trouble, but, well,
too bad. I'm not going to worry about it now.

So, it looks like our data definition allows us to represent a list
of arbitrary length. Nifty!

While we're at it: let's draw some pictures. Ooh, ASCII art!

```
  O
 / \
2   O
   / \
 27   O
     / \
   22   "mt"
```

We're illustrating each pair as a circle with two legs hanging down. What
does the data definition say about the things on each leg? The left one
must be a number, and the right one must be *either* another pair or
`"mt"`.

Now, I need to show you how to write functions on these values, and more
specifically, how the shape of the data determines the shape of the
functions that you write. There are some major shadowy caveats coming
down the pike, but for now, we're going to live in shiny happy sunny
land.

So let's stay in shiny happy sunny land, and write down the data definition
for a list of strings. Let's call it `StrList`. Can you figure it out
with a partner? You can use the same class name.

After you're done with the data definition, write an
example containing at least three strings.

(give them time. You will be surprised by how long it takes.)

```python
# a StrList is one of
# - "mt", or
# - Pair(first, rest)
class Pair:
  def __init__(self, first, rest):
    self.first = first   # a str
    self.rest = rest     # a StrList
  # BOILERPLATE OMITTED

Pair("I",Pair("am",Pair("a",Pair("frog","mt"))))
```

Before we go on, I want to ask you something. How is this data
definition different from our earlier ones? What makes it
able to represent data of arbitrary size? (discuss.) Yep! It's
the *self reference* in the data definition. Circle `StrList` as
field type, draw arrow back to root of data definition.) This
kind of self-referential data definition is what we can use to
represent lists. And, as it will turn out:

- binary trees
- n-ary trees
- stacks
- queues
- heaps
- AVL trees
- B-Trees

... and many many more. You might well call this the fundamental
discovery of CS... except that it predates CS by several hundred
years. Ah well.

## Programming With Lists

As I think I've said before, the stuff that I'm telling you know comes
directly from *How To Design Programs*. This particular part is from
chapter 8. In fact, I think I'm going to lift this next example directly
from the book.

Let's write a function that determines whether the word `"dark"` appears in
a `StrList`. This is going to follow the design recipe, but we're going to
make use of Templates in step 4 for the first time.

Since we already have a data definition for `StrList`s, we don't need to
duplicate it. Steps two and three, though... you can do those.

(First, let them do the first three steps in the design recipe. Be sure
they write tests!)

Okay, here's what we've got.

```python
# StrList -> Boolean
# determines whether "dark" is a string in the list.
def contains_dark(strlist):
  pass

def TestCase(unittest.TestCase):
  def test_contains_dark(self):
    self.assertEqual(contains_dark("mt"), False)
    self.assertEqual(contains_dark(Pair("zz",Pair("yy","mt"))), False)
    self.assertEqual(contains_dark(Pair("zz",Pair("yy",Pair("dark","mt")))), True)
```

(Make absolutely sure that you have a test for the `"mt"` value.)

We're ready for the template.

It turns out that for mixed data (remember, a `StrList` is *ONE OF*...),
our function is likely to require a test, to see which of the two choices
it is. That will be an `if`. So we begin with

```python
def contains_list(strlist):
  if (strlist == "mt"):
    ...
  else:
    ...
```

Next, it turns out that we know something more about the structure of
the value.  Specifically, if it's not `"mt"`, then it must be a Pair, and
it must have a first and a rest field. Here's what we have now:

```python
def contains_list(strlist):
  if (strlist == "mt"):
    ...
  else:
    ... strlist.first ... strlist.rest ...
```

It turns out that there's one more wonderful gift that we get from the
template. Remember when we added this back-arrow to the data definition
to highlight the self-reference? It turns out that this has an implication
for the template. Which field is it that had the self-reference? That's
right, this one: strlist.rest. What do you think we're likely to do with
this field? Well, it's a strlist. Do we have a function that "handles"
a `StrList`? Yes! we do! It's *the function that we're writing*. So we
can add a call to the function that we're defining. Here's the completed
template:

```python
def contains_list(strlist):
  if (strlist == "mt"):
    ...
  else:
    ... strlist.first ... contains_dark(strlist.rest) ...
```

This is the basic template for functions on lists. It's true that you'll
need to change the name of the function that's being defined, to match
the function that you're working on. Otherwise it's identical. I will
ask you to produce this template on quizzes and tests. Yes I will.

Instructor note: dear students, you can't hear me now. This is for
instructors only. It's a SECRET. The secret: this approach uses
structural recursion.  The idea is that the recursion in the function
matches the self-reference in the data definition. When we recur
following this pattern, we get--for free!--an inductive proof that the
function terminates. This takes much of the pain and suffering out of
recursion. Generative recursion (that is, non-structural recursion) is
challenging because the programmer must construct their own proof of
termination, or (more typically) just muddle around in the dark.

Okay, students, let's fill in the template.

What should the function produce when called with `"mt"`? Oh, look! We
wrote that down already, in our test case! The answer is just `False`.

What should the function produce when called with a Pair?

Well, now things get interesting. Mechanically, the hard part is done.
In your head, though, the hard part is just beginning. You need to
put your thinking cap on and see if you can analyze this part:
`contains_dark(strlist.rest)`. What does it return? Let's look at this
extremely concrete test case: `contains_dark(Pair("zz",Pair("yy","mt")))`.
Are we calling `contains_dark` with `"mt"`? No, we're not. It must be a pair.
What's the first of that pair? `"zz"`. What's the *rest* of that pair?

NO! IT'S NOT "YY"!

Sorry for yelling. I knew you were going to say that, though. No, read
what's there: it's not `"yy"`, it's `Pair("yy","mt")`. That is, the list
containing the string `"yy"`. People tend to get sloppy in this way, because
your brain is tired. Now would be a good time to eat some sugar. Seriously.
Sugar isn't good for you in general, but it gives your brain energy to
think hard when you need to. It's like a scary high-power brain medicine.

Sorry, back to lists. It's important to distinguish between the string `"yy"`
and the list of length 1 containing the string `"yy"`. Now; next challenge:
what will `contains_dark` return when called with the list containing the
string `"yy"`? What *should* it return? Well, does that list contain the
string `"dark"`? No, it doesn't. So that call should return `False`.
So, what code should we write, here? (Let students tell you.) Yes, we
probably want to compare the strlist.first to `"dark"`.

```python
... (strlist.first == "dark")  ... contains_dark(strlist.rest) ...
```

so, now we know whether the first string is equal to the string `"dark"`,
*and* we know whether the string `"dark"` occurs in the *rest* of the list.
Two boolean values. How can we combine them?

We could use an `if`. That works fine. Or we can use an `or`. That works
fine, too. Either one is totally fine.

We should stop here. Some of you are seeing this for the first time, and
it's a bit weird, until you're used to it. It seems like our definition
works only if our definition works, which is a broken circular argument.
The secret sauce here is that our definition works when our definition
works *for a smaller argument*. That's called induction. Yikes, did I
say that out loud? Sorry.

Time for you to go practice.

--

Copyright (C) 2017, John Clements (clements@racket-lang.org)
