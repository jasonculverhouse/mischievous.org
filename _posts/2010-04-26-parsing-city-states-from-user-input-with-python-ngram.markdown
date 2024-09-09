---
layout: post
title:  "Parsing City/States From User Input With Python NGram"
date:   2010-04-26 22:42:34 +0000
categories: python
---
A [friend](http://www.charityblossom.org) just asked how to do city/state lookup on input strings. I've used [metaphones](http://en.wikipedia.org/wiki/Metaphone) and [Levenshtein distance](http://en.wikipedia.org/wiki/Levenshtein_distance) in the past but that seems like over kill.  Using a [n-gram](http://en.wikipedia.org/wiki/N-gram) is a nice and easy solution

 1. easy_install [ngram](http://pypi.python.org/pypi/ngram/)
 
 2. build file with all the city and state names one per line, place in citystate.data Redwood City, CA Redwood, VA etc
 
 3. Experiment ( the .2 threshold is a little lax )

<pre class="brush: python">
import string
import ngram
cityStateParser = ngram.NGram(
  items = (line.strip() for line in open('citystate.data')) ,
  N=3, iconv=string.lower, qconv=string.lower,  threshold=.2
)
</pre>

Example:

<pre class="brush: python">
cityStateParser.search('redwood')
[('Redwood VA', 0.5),
('Redwood NY', 0.5),
('Redwood MS', 0.5),
('Redwood City CA', 0.36842105263157893),
...
]
</pre>

Notes: Because these are NGrams you might get overmatch when the state is part of a ngram in the city i.e. search for "washington" would yield Washington IN with a bette score than "Washington OK"

You might also want read [Using Superimposed Coding Of N-Gram Lists For Efficient Inexact Matching](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.21.344&rep=rep1&type=pdf) (PDF Download)

If this works for you, consider giving me a vote on [StackOverflow.com](http://stackoverflow.com/questions/2054422/get-city-state-or-zip-from-a-string-in-python/2718896#2718896)