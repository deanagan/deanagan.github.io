---
defaults:
  # _posts
  - scope:
      path: ""
      type: posts
    values:
      layout: single
      author_profile: true
      read_time: true
      comments: true
      share: true
      related: true
---

Happy new year 2021! I want to kick off my new year blog entry with a beginner's guide to my favourite language, Python.

We've all been there, learning a new language, there is always that little gem of a feature that is quite handy and makes the language enjoyable to write with. When I started learning Python, a lot of things changed for me with how I see programming, especially with all these things they call "pythonic".

For today's post, we'll talk about getting the key with the max value in a dictionary. We'll talk about basics all the way to using a 3rd party library to solve it.


## Here's what we'll learn:
1. [Basic Python Solution to Get Max](#basic-python-solution-to-get-max)
2. [Using Standard Python Library](#using-standard-python-library)
3. [Using Numpy](#using-numpy)
4. [Using Pandas](#using-pandas)



## Tests

Programming would be easier if we had unit tests for our solution. If you're following along, you can use the data below for unit tests.

```python
import pytest


@pytest.mark.parametrize('data, expected, note', [
    ({
  'Andrew Wiggins': 17.8,
  'Eric Paschall': 11.7,
  'Stephen Curry': 28.4,
  'James Wiseman': 11.3,
  'Draymond Green': 4
  }, 'Stephen Curry', 'Stephen Curry is top avg scorer'),
    ({

  'Andrew Wiggins': 17.8,
  'Stephen Curry': 28.4,
  'Klay Thompson': 28.4,
  'Eric Paschall': 11.7,
  'James Wiseman': 11.3,
  'Draymond Green': 4
  }, 'Stephen Curry', 'Stephen Curry is top avg scorer as picked on insertion-order'),
  ({}, '', "No data, return empty string")
])
def test_top_scorer(data: dict, expected: str, note: str):
    assert top_scorer(data) == expected, note
```
**  *We're using Python3.6+, thus dictionaries are
  now "insertion-ordered". From [GVR](https://mail.python.org/pipermail/python-dev/2017-December/151283.html).*

## Basic Python Solution to Get Max

The max is straightforward enough, using a loop and recording the maximum value. Coming from a C-based language, and without knowledge of language features, this is how I possibly would write it:

```python
def top_scorer(stats: dict) -> str:
  top_score_avg = 0
  top_scorer = ''

  for scorer in stats:
    if stats[scorer] > top_score_avg:
      top_score_avg = stats[scorer]
      top_scorer = scorer

  return top_scorer

```

Running our test:
```
collected 3 items
test_top_scorer.py::test_top_scorer[data0-Stephen Curry-Stephen Curry is top avg scorer] PASSED                                                                                                                [ 33%]
test_top_scorer.py::test_top_scorer[data1-Stephen Curry-Stephen Curry is top avg scorer as picked on insertion-order] PASSED                                                                                   [ 66%]
test_top_scorer.py::test_top_scorer[data2--No data, return empty string] PASSED
```

Our code above works! But with how it is in the Python community, I always tend to ask myself, did I miss a language feature that would have made my code shorter and avoided reinventing the wheel?

In this case, there definitely is. We can use the `max` function to solve it.


## Using Standard Python Library

When I started out with Python, using max on a dictionary often made me question how it will work as I knew that a dictionary is a set of key-value pairs.

In our case, we want to get the name of the player who has the "max" average score. Python's max function accepts a `key` which can be used as criteria for getting the max value.

Let's examine the `max` function.

`max` has a function signature:
```python
 max(iterable, *[, key, default])
```

The dictionary input will be our iterable.

The key argument in max is a one-argument ordering function. In this case, we want the items ordered based on the top score.

The 3rd optional argument is what we'll return if the iterable is empty. If we don't supply a default, we'll get a `ValueError` exception. Based on our unit test, it just means we set it to an empty string.

Before we make a `key` function for `max`, let's step back first and rediscover the nature of Python dictionaries.

For example, if we want to get Stephen Curry's average score, we access the dictionary as such:

```python
>>> gsw_points_stat['Stephen Curry']
28.4
>>>
```
or alternatively:
```python
>>> gsw_points_stat.get('Stephen Curry')
28.4
>>>
```

So, we could use either of these to supply our `max` function what it needs.

We can write:
```python
def top_scorer(stats: dict) -> str:
  def ordering_fn(key: str) -> str:
    return stats[key]

  return max(stats, key=ordering_fn, default='')

```

Running our unit tests, and we still pass.

A different way of doing this is a lambda. A lambda is just an anonymous function.

We can write:

```python
def top_scorer(stats: dict) -> str:
  return max(stats, key=lambda k: stats[k], default='')
```

It still works and now, we're down to one line. We're not finished though. The key argument takes a unary function. With functions being first-class objects in Python, we can pass a function directly.

```python
def top_scorer(stats: dict) -> str:
  return max(stats, key=stats.get, default='')
```

## Using numpy

Numpy is a package for scientific computing in Python. It's great and we can use it for this task too.

We can use `argmax` which is a function that returns the index of the max element of an array. `argmax` can actually support finding a max value row-wise or column-wise (which means we are using a jackhammer to drive a nail. This section is for learning).

so to get the max index of the top score in the list, we can:
```python
max_index = np.argmax(data.values())
```

Note that if the dictionary is empty, `argmax` will return 0. So we'll need to do a ternary operation.

From our data, this will return 0 as that is the index of the max value in the dictionary's values. Knowing what our index is, we just need to get the list of keys.

Thus our final code:

```python
import numpy as np

def top_scorer(stats: dict) -> str:
  return list(stats.keys())[np.argmax(list(stats.values()))] if stats else ''
```

## Using pandas

Pandas is another python library for data analysis. It is used by the data science community and especially important for high performance when dealing with large amounts of data.

Our example isn't much, but just for fun, we can use a pandas function to solve the problem too.

We can use the `idxmax` of a `Series` in pandas. A `Series` is a one-dimensional ndarray in pandas.

So our code would be:
```python
import pandas as pd
def top_scorer(stats: dict) -> str:
    return pd.Series(stats).idxmax() if stats else ''
```
