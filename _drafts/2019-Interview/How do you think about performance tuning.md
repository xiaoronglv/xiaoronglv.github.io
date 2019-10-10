## Question

what's your strategy for performance tuning work?

How do you think about performance tuning?



## Answer

I usually do performance tuning in this way.



There is a very famous quote by Peter Drucker:

If you can't measure it, you can't improve it.


1. Firstly, I usually choose proper monitor tools to measure and profile the performance.

For example, if I want to improve the performance of application, NewRelic is a convenient tool to measure the data.

If I want to improve the performance of database, Solorwinds is a good choice.


2. Once we have enough performance data, we can analyze the performance in different perspective.

for example, by endpoint, by frequency, by total consuming time, by db queries count.


General speaking, there are some common problems.

some time-consuming queries are not covered by index.

some page rendering are not cached.

some unnecessary db queries are triggered.

some methods are CPU bound code and very heavy, which could be cached by instance variable cache.


3. Apply my changes 


4. compare the metrics



That's the approach I usually do performance tuning.

