# What is your greatest professional achievement?

## Similar Question

- “Tell me about a time you demonstrated leadership.”
- “Tell me about a time you failed.”
- “Describe a time you solved a difficult problem.”

## STAR

The best way to describe your greatest accomplishment while only including relevant information is to use the STAR technique. The acronym works as follows:

S = Situation. In what context did your greatest accomplishment occur?

T = Task. What challenges did you face or what goal did you want to achieve?

A = Action. What action did you take? What skills did you use?

R = Result. What was the result? What did you learn from the experience? Why is this your greatest accomplishment?


## Answer

My greatest achievement was reducing the response time of SAP Jam's web application server from 300ms to 100ms.

======The situation===== 

our codebase was a huge Ruby on Rails project, which had more than 200,000 lines. Customers always complained it was too slow.

Many web developers knew this problem, but no body was willing to take responsibility for improving it. 

This unwillingness was understandable, because there were several difficulties with the project.

1. Ruby is very flexible, our codebase was too huge, we had lots of monkey patches. It was very hard to track.

2. Each request triggered more than 200 queries, sometimes one thousand. We did find some small bottlenecks, but it didn't have too much value.

3. Even though I finally identified the bottleneck, I could not figure out the code and make sure my change would not break the old behavior. it wasn't easy to contact the authors. 

because
	- some authors were in Canada.
	- some authors had left.


So my solution was to:

1. arrange lots of meetings to discuss the code with its authors.


2. There are some common strategies to improve the performance.

E.g. 
- HTTP Cache, I mean etag
- instance variable cache
- fragment cache
- low level cache
- change O(n) operations to O(1)

I needed to develop the optimal plan for our business from several common strategies.


==== the Results ========

The results were good. we successfully reduced the response time from 300ms to 100 ms.
	
---

Irene 帮忙纠正的句子


时髦句子

The unwillingness was understandable, because