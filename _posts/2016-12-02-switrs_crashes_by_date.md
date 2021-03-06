---
layout: post
title: "SWITRS: On What Days Do People Crash?"
description: >
  What day of the year has the most car crashes? The fewest? Find out as I
  look at California's accident data! Hint: they're both holidays!
image: /files/switrs-accidents-by-date/1923_dc_car_crash.jpg
---

{% capture file_dir %}{{ site.url }}/files/switrs-accidents-by-date{% endcapture %}

![Men gathered around a crashed car in Washington DC, 1923]({{ file_dir
}}/1923_dc_car_crash.jpg)

The [Statewide Integrated Traffic Records System (SWITRS)][switrs] contains a
wealth of information, enough to determine who, where, when, and sometimes why
and how for every traffic accident in California. Today, with the assistance
of my [SWITRS-to-SQLite script][s2s] ([discussed previously][lastpost]), I'm
going to look at when accidents happen, and specifically on what dates.

[switrs]: http://iswitrs.chp.ca.gov/Reports/jsp/userLogin.jsp
[s2s]: https://github.com/agude/SWITRS-to-SQLite
[lastpost]: {% post_url 2016-11-01-switrs_to_sqlite %}

As always, the Jupyter notebook used to do this analysis can be found
[here][notebook] ([rendered on Github][rendered]).

[notebook]: {{ file_dir }}/SWITRS Crash Dates.ipynb
[rendered]: https://github.com/agude/agude.github.io/blob/master/files/switrs-accidents-by-date/SWITRS%20Crash%20Dates.ipynb

## Data Selection

The data was selected using the following query:

{% highlight sql %}
SELECT Collision_Date FROM Collision
WHERE Collision_Date IS NOT NULL
AND Collision_Date <= '2015-12-31'  -- 2016 is incomplete
{% endhighlight %}

This selects every accident that happened before 2016 that has a collision
date stored. The current year, 2016, is excluded because the data from it is
incomplete.

## Accidents per Week

The first thing to look at is crashes as a function of time. Below, I plot
accidents per week to make the trends clearer; plotting per day results in too
many points to separate by eye.

![Line plot showing accidents per week from 2001 to 2015]({{ file_dir
}}/accidents_per_week_in_california.svg)

The week-to-week variation is rather significant, but two major trends are
obvious:

1. The total number of accidents has been decreasing over the past few years,
   with a big drop in 2008, but is now rising sharply in 2015.
2. Each year is similar, with a mid-year lull and wildly varying
   increases and decrease right before the end of the year.

The first trend is easy to explain: the [Great Recession][gr] put many people
out of work, who then stopped commuting. The second trend is also due to
reduced driving; we'll look at it in detail below.

[gr]: https://en.wikipedia.org/wiki/Great_Recession

## Day-by-Day

To explore the second trend, we'll need to look at the data day-by-day instead
of a week at a time. Below is a plot of the average number of accidents on
each day of the year. The average is calculated by summing the number of
accidents on a specific day (say, September 22nd) across the years 2001 to
2015. The sum is then divided by the number of times that specific day
appeared in the timespan (15, except for the [leap day][leapday], which only
appears 3 times).

[leapday]: https://en.wikipedia.org/wiki/February_29

![Line plot showing average accidents by day of the year]({{ file_dir
}}/mean_accidents_by_date.svg)

Holidays account for the extrema, with the minimum number of accidents taking
place on Christmas, and the maximum number taking place on Halloween. In fact,
many of the local maxima and minima are also holidays! Some create obvious,
multi-day patterns (like Thanksgiving) because they are floating holidays
while others (like Christmas and Halloween) create massive, single-day dips or
spikes because they happen on the same day every year. Holidays that fewer
people get off from work, like Washington's Birthday and Columbus Day, show
almost no deviation from the surrounding dates.

But perhaps the most interesting dates are the holidays where the number of
accidents **increases**! Halloween is the most obvious of these, and sets the
record for the **highest number of accidents**, but Valentine's Day and St.
Patrick's Day also show increases. I believe there are two reasons these
holidays have higher than normal accident counts. First, these are not
generally paid holidays, so the normal number of commute-related accidents
happen. Second, these are holidays are celebrated away from home after work
(for drinks, dates, or candy), and so people drive more on these days than
they would otherwise. I suspect that there is a third reason behind
Halloween's high accident count: a higher than average number of pedestrians
being out and about leading to a higher than average number of accidents
involving pedestrians. I plan to look at pedestrian accidents in a later blog
post.

## Day of the Week

Finally, let's look at accidents by day of the week. On weekends, like
holidays, we would expect most people to not go to work. Below is a [violin
plot][violin] of accidents by day of the week. The width of each "violin"
indicates the number of days with that value while the center line indicates
the median, and the two outer lines indicate the interquartile.

[violin]: https://en.wikipedia.org/wiki/Violin_plot

![Violin plot showing accidents by day of the week]({{ file_dir
}}/accidents_by_day_of_the_week.svg)

The distribution for each day of the week is bimodal. This is due to the [two
plateaus in accident rates][apw]: a high one from 2001-2006, and a lower one
from 2011--2014. The first four weekdays have roughly the same number of
crashes. Friday has more, presumably because people are more likely to go out
after work. Saturday drops to a level slightly below the weekdays, though not
by much, and Sunday has the lowest accident count.


[apw]: #accidents-per-week

In the end the results are not too surprising: accidents happen when people
are driving, not when they're sitting at home celebrating!

---

**Update**: _Replaced the [univariate dot plot][dot_plot] in the [Day of the
Week][dow] section with a [violin plot][violin_plot]._

[dot_plot]: {{ file_dir }}/accidents_by_day_of_the_week_dot.svg
[violin_plot]: {{ file_dir }}/accidents_by_day_of_the_week.svg

[dow]: #day-of-the-week
