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

Happy new year 2021! I want to kick off my new year blog entry with a beginner's guide to my favorite language, Python.

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
# Import the function under test

@pytest.mark.parametrize('data, expected', [
    ({
  'Stephen Curry': 28.4,
  'Andrew Wiggins': 17.8,
  'Eric Paschall': 11.7,
  'James Wiseman': 11.3,
  'Draymond Green': 4
  }, 'Stephen Curry'),
  ({
  'Stephen Curry': 28.4,
  'Klay Thompson': 28.4,
  'Andrew Wiggins': 17.8,
  'Eric Paschall': 11.7,
  'James Wiseman': 11.3,
  'Draymond Green': 4
  }, 'Stephen Curry'), # Max is 'stable-sort' so expect Stephen Curry as it is the first it sees
  ({}, '') # Empty should return empty string
])
def test_top_scorer(data: dict, expected: str):
    assert top_scorer(data) == expected
```


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

Running our function:
```
>>> gsw_points_stat = {
  'Stephen Curry': 28.4,
  'Andrew Wiggins': 17.8,
  'Eric Paschall': 11.7,
  'James Wiseman': 11.3,
  'Draymond Green': 4
  }
>>>
>>> top_scorer(gsw_points_stat)
'Stephen Curry'
```

Our code above works! But with how it is in the Python community, I always tend to ask myself, did I miss a language feature that would have made my code shorter?

In this case, there definitely is! We can use the `max` function to solve it. But, for beginners, using max on a dictionary often leads to questions of "how", as we know that a dictionary is a set of key-value pairs.

In our case, we want to get the name of the player who has the "max" average score. Python's max function accepts a `key` which can be used as criteria for getting the max value.

So, let's give `max` a try.

`max` has a function signature:
```python
 max(iterable, *[, key, default])
```

The dictionary `stats` will be our iterable. The key argument in max is a one-argument ordering function. In this case, we want the items ordered based on the top score (note, we'll talk about the other `default` argument later).

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

  return max(stats, key=ordering_fn)

```

The get method in a dictionary is a function that accepts one argument given a key.


```python
def top_scorer(stats: dict) -> str:
  return max(stats, key=stats.get)
```
