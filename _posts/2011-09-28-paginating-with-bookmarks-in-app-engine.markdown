---
author: John Tantalo
comments: true
date: 2011-09-28 22:57:57+00:00
layout: post
slug: paginating-with-bookmarks-in-app-engine
title: Paginating with Bookmarks in App Engine
wordpress_id: 159
categories:
- Code
- Ideas
tags:
- Google App Engine
---

Let's say we have a Google App Engine model with two fields, "x" and "y", and all objects are distinct.



We'd like to paginate with the sort order criteria "x ascending, y ascending". For example, that index might look like this.



[![](/images/wp-content/uploads/2011/09/example-data.png)](/images/wp-content/uploads/2011/09/example-data.png)



While paginating, we need to know whether there is a "next page", because we don't want to return an empty page unless we have no objects.
  


To do this, we'll look-ahead for the next item in our search. So, if the page size is 2, we'll just fetch 3 items. That extra object will give us the necessary (x, y) coordinates we need to resume the search, that is, to fetch the next page. This object is called the "bookmark".



Given the (x, y) bookmark, how do we fetch the next page? In index terms, we might construct a prefix string "/$x/$y/", and return the next rows after that in our index. Unfortunately App Engine doesn't give us a low-level interface like that, so we have to use filters.



What kind of filters? We want all objects greater than or equal to our given "x". But, for objects with the same value as "x", we have to skip those less than our given "y", because we have already seen them. Clearly we will need filters on both "x" and "y", but woe, App Engine insists that no more than one distinct field can have a filter expression per query.[1]
  


[Ryan Barrett](https://plus.google.com/103651231634018158746) observed that [for a search with n sorted fields, you will need n queries to resume the search from a bookmark](https://groups.google.com/d/topic/google-appengine/7lr73iDhPN4/discussion). In other words, you need one query for each field.



What does this look like? In our example, we have two sorted fields. To resume the search, the first query will fix "x" and filter on "y", with "y ascending". The second query will filter on "x" with "x ascending, y ascending".



What would that look like on our example index?



[![](/images/wp-content/uploads/2011/09/queries.gif)](/images/wp-content/uploads/2011/09/queries.gif)



Each shaded region is one query, that is, one trip to the datastore. The green objects are the current page. The orange object is the next bookmark. The blue objects are the old pages.



The first query is on a fixed "x" and filters "y". The second query resumes where the first left off, and filters on "x". Since the sort orders are consistent with the filters, we obtain a consistent view of the index.



On the fourth search, we happen to get lucky and find our bookmark on the first query, so we can skip the second query.



## In general





If you're sorting on n fields {f0, f2, ... fn-1} you will need n queries {q0, q1, ... qn-1}.
  


Query qi will filter fn-i and fix fields {f0, ... fn-i-1}.



For n=2, query q0 filters f1 and fixes f0. Query q1 filters f0.



## Why don't you use cursors you slob?





Cursors are great! Except, they don't immediately answer the question, "Do we have a next page?" If you don't care then by all means use cursors, they're much easier to use than bookmarks.



Cursors in App Engine can't look-ahead. Instead, you have to fetch one object with the query's next cursor, rather than fetching an extra object in the original query.



On the other hand, cursors are allowed to perform queries which users cannot. Cursors can perform the "prefix string" trick described above. So, a cursor can do in one query what takes us two or more.



So which should you use? That probably depends on how many fields you are sorting on. For a single field, I prefer the look-ahead technique, because it only requires one trip to the datastore. For two fields, either technique requires (at most) two trips, although cursors are usually simpler. For more than two fields, cursors easily win.



[1] This requirement exists to prevent users from requesting filters which the datastore cannot satisfy by scanning consecutive rows in an index. For example, the filters "x > 2 and y > 3" can specify non-consecutive rows of an index that sorts "x" and "y" ascendingly.

