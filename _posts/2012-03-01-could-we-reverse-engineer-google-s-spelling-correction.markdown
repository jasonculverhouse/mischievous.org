---
layout: post
title:  "Could We Reverse Engineer Google's Spelling Correction"
date:   2012-03-01 23:58:28 +0000
categories: google
---
Can we reverse engineer Google's word correction algorithm given a corpus of misspelled words paired with their corrections?

Since I have a single word domain name **mischievous**, which is one of the 100 
most misspelled English words, this allows me to analyze some interesting data from 
Google's webmaster tools. I pulled out all the misspellings and impressions
within a [Levenshtein 
Distance](http://en.wikipedia.org/wiki/Levenshtein_distance). There is 
a nice academic paper that discusses [Learning a Spelling Error Model from 
Search Query Logs](http://acl.ldc.upenn.edu/H/H05/H05-1120.pdf) that I plan to 
use to explore some of this data in the future.

A chart and regression of the misspelling data on a log-log chart shows that 
impressions of misspellings of the word **mischievous** vs the rank that they 
appear in all keywords that lead to this blog follows 
[Zipf's_law](http://en.wikipedia.org/wiki/Zipf's_law). I refitted words with 
under 10 impressions based on their rank data (ranks >= 83) as webmaster tools 
only gives a sample value when the impressions are greater than 10.

<img alt="mischievous-fvs-r.png" src="{{ site.url }}{{ site.baseurl }}/assets/images/mischievous-fvs-r.png" width="671" height="512" class="mt-image-center" style="text-align: center; display: block; margin: 0 auto 20px;" />

### **Raw Data**

You can use this table to gauge your spelling (I should add the cumulative distribution so you should see what percentile a misspelling places you ) 

<table class="gridtable">
  <tr>
    <th align="center">rank</th>
    <th align="center">query</th>
    <th align="center">replace</th>
    <th align="center">levenshtein</th>
    <th align="center">similarity</th>
  </tr>
  <tr valign="top">
    <td align="right">1</td>
    <td align="left">mischievous</td>
    <td align="right">27000.00</td>
    <td align="right">0</td>
    <td align="right">1.00</td>
  </tr>
  <tr valign="top">
    <td align="right">2</td>
    <td align="left">mischevious</td>
    <td align="right">4500.00</td>
    <td align="right">2</td>
    <td align="right">0.41</td>
  </tr>
  <tr valign="top">
    <td align="right">3</td>
    <td align="left">mischivious</td>
    <td align="right">700.00</td>
    <td align="right">2</td>
    <td align="right">0.50</td>
  </tr>
  <tr valign="top">
    <td align="right">6</td>
    <td align="left">michevious</td>
    <td align="right">500.00</td>
    <td align="right">3</td>
    <td align="right">0.21</td>
  </tr>
  <tr valign="top">
    <td align="right">7</td>
    <td align="left">mischevous</td>
    <td align="right">500.00</td>
    <td align="right">1</td>
    <td align="right">0.64</td>
  </tr>
  <tr valign="top">
    <td align="right">13</td>
    <td align="left">mischiveous</td>
    <td align="right">170.00</td>
    <td align="right">2</td>
    <td align="right">0.50</td>
  </tr>
  <tr valign="top">
    <td align="right">18</td>
    <td align="left">mischieveous</td>
    <td align="right">150.00</td>
    <td align="right">1</td>
    <td align="right">0.67</td>
  </tr>
  <tr valign="top">
    <td align="right">19</td>
    <td align="left">mischivous</td>
    <td align="right">150.00</td>
    <td align="right">1</td>
    <td align="right">0.64</td>
  </tr>
  <tr valign="top">
    <td align="right">20</td>
    <td align="left">michievous</td>
    <td align="right">110.00</td>
    <td align="right">1</td>
    <td align="right">0.64</td>
  </tr>
  <tr valign="top">
    <td align="right">21</td>
    <td align="left">mischeivious</td>
    <td align="right">90.00</td>
    <td align="right">3</td>
    <td align="right">0.39</td>
  </tr>
  <tr valign="top">
    <td align="right">23</td>
    <td align="left">mischeivous</td>
    <td align="right">90.00</td>
    <td align="right">2</td>
    <td align="right">0.50</td>
  </tr>
  <tr valign="top">
    <td align="right">24</td>
    <td align="left">michevous</td>
    <td align="right">70.00</td>
    <td align="right">2</td>
    <td align="right">0.38</td>
  </tr>
  <tr valign="top">
    <td align="right">25</td>
    <td align="left">mischievious</td>
    <td align="right">70.00</td>
    <td align="right">1</td>
    <td align="right">0.67</td>
  </tr>
  <tr valign="top">
    <td align="right">26</td>
    <td align="left">mischeveous</td>
    <td align="right">70.00</td>
    <td align="right">2</td>
    <td align="right">0.41</td>
  </tr>
  <tr valign="top">
    <td align="right">29</td>
    <td align="left">mischeavious</td>
    <td align="right">60.00</td>
    <td align="right">3</td>
    <td align="right">0.39</td>
  </tr>
  <tr valign="top">
    <td align="right">30</td>
    <td align="left">mischiefous</td>
    <td align="right">60.00</td>
    <td align="right">1</td>
    <td align="right">0.60</td>
  </tr>
  <tr valign="top">
    <td align="right">31</td>
    <td align="left">michivious</td>
    <td align="right">60.00</td>
    <td align="right">3</td>
    <td align="right">0.28</td>
  </tr>
  <tr valign="top">
    <td align="right">32</td>
    <td align="left">mischeavous</td>
    <td align="right">50.00</td>
    <td align="right">2</td>
    <td align="right">0.50</td>
  </tr>
  <tr valign="top">
    <td align="right">33</td>
    <td align="left">mishevious</td>
    <td align="right">35.00</td>
    <td align="right">3</td>
    <td align="right">0.28</td>
  </tr>
  <tr valign="top">
    <td align="right">35</td>
    <td align="left">miscevious</td>
    <td align="right">35.00</td>
    <td align="right">3</td>
    <td align="right">0.35</td>
  </tr>
  <tr valign="top">
    <td align="right">47</td>
    <td align="left">mishievous</td>
    <td align="right">16.00</td>
    <td align="right">1</td>
    <td align="right">0.64</td>
  </tr>
  <tr valign="top">
    <td align="right">48</td>
    <td align="left">michievious</td>
    <td align="right">16.00</td>
    <td align="right">2</td>
    <td align="right">0.41</td>
  </tr>
  <tr valign="top">
    <td align="right">53</td>
    <td align="left">misgevious</td>
    <td align="right">12.00</td>
    <td align="right">4</td>
    <td align="right">0.28</td>
  </tr>
  <tr valign="top">
    <td align="right">54</td>
    <td align="left">micheivious</td>
    <td align="right">12.00</td>
    <td align="right">4</td>
    <td align="right">0.20</td>
  </tr>
  <tr valign="top">
    <td align="right">55</td>
    <td align="left">mischvious</td>
    <td align="right">12.00</td>
    <td align="right">3</td>
    <td align="right">0.44</td>
  </tr>
  <tr valign="top">
    <td align="right">56</td>
    <td align="left">mischiveious</td>
    <td align="right">12.00</td>
    <td align="right">2</td>
    <td align="right">0.47</td>
  </tr>
  <tr valign="top">
    <td align="right">58</td>
    <td align="left">mischevios</td>
    <td align="right">12.00</td>
    <td align="right">3</td>
    <td align="right">0.28</td>
  </tr>
  <tr valign="top">
    <td align="right">83</td>
    <td align="left">mischevius</td>
    <td align="right">11.15</td>
    <td align="right">2</td>
    <td align="right">0.35</td>
  </tr>
  <tr valign="top">
    <td align="right">101</td>
    <td align="left">miscevous</td>
    <td align="right">8.30</td>
    <td align="right">2</td>
    <td align="right">0.57</td>
  </tr>
  <tr valign="top">
    <td align="right">113</td>
    <td align="left">micheavous</td>
    <td align="right">7.01</td>
    <td align="right">3</td>
    <td align="right">0.28</td>
  </tr>
  <tr valign="top">
    <td align="right">133</td>
    <td align="left">mischeives</td>
    <td align="right">5.48</td>
    <td align="right">4</td>
    <td align="right">0.28</td>
  </tr>
  <tr valign="top">
    <td align="right">140</td>
    <td align="left">mischeviuos</td>
    <td align="right">5.08</td>
    <td align="right">3</td>
    <td align="right">0.26</td>
  </tr>
  <tr valign="top">
    <td align="right">153</td>
    <td align="left">mischiefious</td>
    <td align="right">4.44</td>
    <td align="right">2</td>
    <td align="right">0.56</td>
  </tr>
  <tr valign="top">
    <td align="right">176</td>
    <td align="left">mischeous</td>
    <td align="right">3.60</td>
    <td align="right">2</td>
    <td align="right">0.47</td>
  </tr>
  <tr valign="top">
    <td align="right">196</td>
    <td align="left">mechivious</td>
    <td align="right">3.06</td>
    <td align="right">4</td>
    <td align="right">0.21</td>
  </tr>
  <tr valign="top">
    <td align="right">218</td>
    <td align="left">miscievious</td>
    <td align="right">2.61</td>
    <td align="right">2</td>
    <td align="right">0.41</td>
  </tr>
  <tr valign="top">
    <td align="right">223</td>
    <td align="left">mechevious</td>
    <td align="right">2.52</td>
    <td align="right">4</td>
    <td align="right">0.15</td>
  </tr>
  <tr valign="top">
    <td align="right">241</td>
    <td align="left">mischieved</td>
    <td align="right">2.24</td>
    <td align="right">3</td>
    <td align="right">0.53</td>
  </tr>
  <tr valign="top">
    <td align="right">262</td>
    <td align="left">myschevious</td>
    <td align="right">1.98</td>
    <td align="right">3</td>
    <td align="right">0.20</td>
  </tr>
  <tr valign="top">
    <td align="right">263</td>
    <td align="left">misjevious</td>
    <td align="right">1.96</td>
    <td align="right">4</td>
    <td align="right">0.28</td>
  </tr>
  <tr valign="top">
    <td align="right">273</td>
    <td align="left">mischeviouse</td>
    <td align="right">1.86</td>
    <td align="right">3</td>
    <td align="right">0.32</td>
  </tr>
  <tr valign="top">
    <td align="right">277</td>
    <td align="left">machivious</td>
    <td align="right">1.82</td>
    <td align="right">4</td>
    <td align="right">0.21</td>
  </tr>
  <tr valign="top">
    <td align="right">279</td>
    <td align="left">mischeiveous</td>
    <td align="right">1.80</td>
    <td align="right">3</td>
    <td align="right">0.39</td>
  </tr>
  <tr valign="top">
    <td align="right">282</td>
    <td align="left">mischives</td>
    <td align="right">1.77</td>
    <td align="right">3</td>
    <td align="right">0.38</td>
  </tr>
  <tr valign="top">
    <td align="right">321</td>
    <td align="left">mischievous?</td>
    <td align="right">1.45</td>
    <td align="right">1</td>
    <td align="right">1.00</td>
  </tr>
  <tr valign="top">
    <td align="right">324</td>
    <td align="left">miscchievous</td>
    <td align="right">1.43</td>
    <td align="right">1</td>
    <td align="right">0.79</td>
  </tr>
  <tr valign="top">
    <td align="right">333</td>
    <td align="left">mischeifous</td>
    <td align="right">1.38</td>
    <td align="right">3</td>
    <td align="right">0.41</td>
  </tr>
  <tr valign="top">
    <td align="right">334</td>
    <td align="left">mistchivious</td>
    <td align="right">1.37</td>
    <td align="right">3</td>
    <td align="right">0.32</td>
  </tr>
  <tr valign="top">
    <td align="right">351</td>
    <td align="left">miscievous</td>
    <td align="right">1.27</td>
    <td align="right">1</td>
    <td align="right">0.64</td>
  </tr>
  <tr valign="top">
    <td align="right">357</td>
    <td align="left">mischieveious</td>
    <td align="right">1.24</td>
    <td align="right">2</td>
    <td align="right">0.63</td>
  </tr>
  <tr valign="top">
    <td align="right">363</td>
    <td align="left">mishcevious</td>
    <td align="right">1.21</td>
    <td align="right">3</td>
    <td align="right">0.26</td>
  </tr>
  <tr valign="top">
    <td align="right">371</td>
    <td align="left">mischievous </td>
    <td align="right">1.17</td>
    <td align="right">2</td>
    <td align="right">1.00</td>
  </tr>
  <tr valign="top">
    <td align="right">378</td>
    <td align="left">mischievous.</td>
    <td align="right">1.14</td>
    <td align="right">1</td>
    <td align="right">1.00</td>
  </tr>
  <tr valign="top">
    <td align="right">408</td>
    <td align="left">micheveous</td>
    <td align="right">1.01</td>
    <td align="right">3</td>
    <td align="right">0.21</td>
  </tr>
  <tr valign="top">
    <td align="right">422</td>
    <td align="left">mischevoius</td>
    <td align="right">0.96</td>
    <td align="right">2</td>
    <td align="right">0.41</td>
  </tr>
  <tr valign="top">
    <td align="right">430</td>
    <td align="left">mistivious</td>
    <td align="right">0.94</td>
    <td align="right">4</td>
    <td align="right">0.28</td>
  </tr>
  <tr valign="top">
    <td align="right">438</td>
    <td align="left">mischievo</td>
    <td align="right">0.91</td>
    <td align="right">2</td>
    <td align="right">0.69</td>
  </tr>
  <tr valign="top">
    <td align="right">444</td>
    <td align="left">misgivious</td>
    <td align="right">0.89</td>
    <td align="right">4</td>
    <td align="right">0.28</td>
  </tr>
  <tr valign="top">
    <td align="right">483</td>
    <td align="left">michivous</td>
    <td align="right">0.79</td>
    <td align="right">2</td>
    <td align="right">0.38</td>
  </tr>
  <tr valign="top">
    <td align="right">510</td>
    <td align="left">mischievous,</td>
    <td align="right">0.72</td>
    <td align="right">1</td>
    <td align="right">1.00</td>
  </tr>
  <tr valign="top">
    <td align="right">525</td>
    <td align="left">mystivious</td>
    <td align="right">0.69</td>
    <td align="right">5</td>
    <td align="right">0.15</td>
  </tr>
  <tr valign="top">
    <td align="right">528</td>
    <td align="left">myschivious</td>
    <td align="right">0.69</td>
    <td align="right">3</td>
    <td align="right">0.26</td>
  </tr>
  <tr valign="top">
    <td align="right">543</td>
    <td align="left">mis chievous</td>
    <td align="right">0.66</td>
    <td align="right">1</td>
    <td align="right">0.67</td>
  </tr>
  <tr valign="top">
    <td align="right">603</td>
    <td align="left">meschivious</td>
    <td align="right">0.56</td>
    <td align="right">3</td>
    <td align="right">0.26</td>
  </tr>
  <tr valign="top">
    <td align="right">606</td>
    <td align="left">mischievoud</td>
    <td align="right">0.56</td>
    <td align="right">1</td>
    <td align="right">0.71</td>
  </tr>
  <tr valign="top">
    <td align="right">626</td>
    <td align="left">mischeviois</td>
    <td align="right">0.53</td>
    <td align="right">3</td>
    <td align="right">0.26</td>
  </tr>
  <tr valign="top">
    <td align="right">629</td>
    <td align="left">micheavious</td>
    <td align="right">0.53</td>
    <td align="right">4</td>
    <td align="right">0.20</td>
  </tr>
  <tr valign="top">
    <td align="right">635</td>
    <td align="left">mishievious</td>
    <td align="right">0.52</td>
    <td align="right">2</td>
    <td align="right">0.41</td>
  </tr>
  <tr valign="top">
    <td align="right">661</td>
    <td align="left">miscivous</td>
    <td align="right">0.49</td>
    <td align="right">2</td>
    <td align="right">0.47</td>
  </tr>
  <tr valign="top">
    <td align="right">671</td>
    <td align="left">meschevious</td>
    <td align="right">0.48</td>
    <td align="right">3</td>
    <td align="right">0.20</td>
  </tr>
  <tr valign="top">
    <td align="right">676</td>
    <td align="left">miss chivous</td>
    <td align="right">0.47</td>
    <td align="right">3</td>
    <td align="right">0.39</td>
  </tr>
  <tr valign="top">
    <td align="right">734</td>
    <td align="left">mischieves</td>
    <td align="right">0.42</td>
    <td align="right">2</td>
    <td align="right">0.53</td>
  </tr>
</table>
