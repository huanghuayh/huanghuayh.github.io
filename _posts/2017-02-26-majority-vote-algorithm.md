---
layout: post
title: Ab O(n) time, O(1) space algorithm to determine majority vote 
---

Given an input array of votes: [A,A, A, C ,C, B ,B, C, C, C, B, C, C], find the vote that has count number larger than n/2.

a clever [algorithm](http://www.cs.utexas.edu/~moore/best-ideas/mjrty/example.html) (sketch):


We will sweep down the sequence starting at the pointer position shown above.

As we sweep we maintain a pair consisting of a current candidate and a counter. Initially, the current candidate is unknown and the counter is 0.

When we move the pointer forward over an element e:

If the counter is 0, we set the current candidate to e and we set the counter to 1.
If the counter is not 0, we increment or decrement the counter according to whether e is the current candidate.
When we are done, the current candidate is the majority element, if there is a majority.



