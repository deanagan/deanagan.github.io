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
Tax time is upon us in Australia, and due to covid19, this tax time is a bit different as we have to compute for our working from home costs (for some of us who don't normally work from home).

In Australia, the tax office introduced a 'shortcut method' to compute for deductions. See [here](https://www.ato.gov.au/General/COVID-19/Support-for-individuals-and-employees/Employees-working-from-home/#ShortcutMethod) for more details.

The shortcut method is simply claiming a deduction of 80 cents for each hour worked from home. We know how many hours we work in a day, but how do we compute the total number of days excluding the holidays and weekends without manually counting the days on a calendar?

We will try to answer that question in this blog post.

## 1. Installation for required packages

We will use Python (I used 3.8.4 at the time of writing this entry) to compute for the number of days. Let's install our required packages via pip.

```
> pip install numpy
> pip install pandas
> pip install holidays
```

## 2. Let's get coding

First, let's import all our packages:
```
import pandas as pd
import numpy as np
import holidays
```

So we'll need to supply our start and end dates as strings and convert them to datetime using pandas `to_datetime`.

For example, if I worked from home from March 18 to June 30, I supply March 18 to July 1. We have to add +1 day as this is required by numpy's `busday_count` function. We'll talk more about that in the later part.

Let's also call the `.date()` at the end to exclude the time from datetime.
```
start = pd.to_datetime('18/03/2020', format='%d/%m/%Y').date()
end = pd.to_datetime('01/07/2020', format='%d/%m/%Y').date()
```

Using the `holidays` package, we can easily pick up the holidays in any country it supports. I live in Australia, so we use that.

Below is what `holidays.Australia` contains.
```
>>> print(*holidays.Australia(years=2020).items(), sep="\n")
(datetime.date(2020, 1, 1), "New Year's Day")
(datetime.date(2020, 1, 26), 'Australia Day')
(datetime.date(2020, 1, 27), 'Australia Day (Observed)')
(datetime.date(2020, 4, 10), 'Good Friday')
(datetime.date(2020, 4, 13), 'Easter Monday')
(datetime.date(2020, 4, 25), 'Anzac Day')
(datetime.date(2020, 12, 25), 'Christmas Day')
(datetime.date(2020, 12, 26), 'Boxing Day')
(datetime.date(2020, 12, 28), 'Boxing Day (Observed)')
```
This is default to `ACT`.

States can have different holidays. The Queen's Birthday holiday on June 8th isn't on the list by default as it is not the same in NSW.

So let's add that in by providing a `prov` argument `NSW`.


```
# First, create our holidays list
nsw_holidays = [*holidays.Australia(years=2020, prov='NSW').keys()]

# Note, add any holiday or absences manually if any
# sick_leave = pd.to_datetime('29/06/2020', format='%d/%m/%Y').date()
# nsw_holidays[sick_leave] = "Colds"
```

Now that we're all setup, let's call the `busday_count` function in numpy.

The `busday_count` function will count the number of days between 2 dates, excluding the end date.

According to numpy's [documentation](https://numpy.org/doc/stable/reference/generated/numpy.busday_count.html), this is the function signature:

```
numpy.busday_count(begindates,
                   enddates,
                   weekmask='1111100',
                   holidays=[],
                   busdaycal=None,
                   out=None)
```

Note that the weekmask is a boolean representation of what days to count in a week. This is a monday to sunday representation. So the 2 zeros at the end means we exclude saturdays and sundays which is what we really want.

Now that we have `nsw_holidays`, we need to assign this to the `holidays` parameter. Note that nsw_holidays is a dictionary, so we unpack the keys only.

We will keep `busdaycal` and `out` as None.

So now, we count our wfh days. Note that holidays.Australia() is a dictionary type. And we only need the keys to store in the list.
```
wfh_day_count = np.busday_count(start, end, holidays=[*nsw_holidays.keys()])
```

wfh_day_count is the total number of days you've worked from home from March 18 to June 30.

Here's the whole source code:
```
import pandas as pd
import numpy as np
import holidays

start = pd.to_datetime('18/03/2020', format='%d/%m/%Y').date()
end = pd.to_datetime('01/07/2020', format='%d/%m/%Y').date()

nsw_holidays = holidays.Australia(years=2020, prov='NSW')

print(*nsw_holidays.items(), sep='\n')

wfh_day_count = np.busday_count(start, end, holidays=[*nsw_holidays.keys()])

print(f'Total WFH Days {wfh_day_count}')
```

And that's it. I hope this helps anyone looking for a quick way to compute.
