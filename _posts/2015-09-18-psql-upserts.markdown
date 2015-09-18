---
layout: post
title:  "PostgreSQL upserts - how to ON DUPLICATE KEY UPDATE"
date:   2015-09-18 21:48:10
categories: devops dba postgresql
---

# Current status

PostgreSQL received upserts support only recently ([in version 9.5](http://www.postgresql.org/docs/9.5/static/sql-insert.html)). MySQL has however made the syntax of ```ON DUPLICATE KEY UPDATE``` prelevant. While the idea of upserts is simple the implementation proved tricky. There is a [whole WIKI page](https://wiki.postgresql.org/wiki/UPSERT) dedicated to various problems and possibilities how it should be done.

As of this post writing postgres 9.5 is still in development. As such it will take quite some time for upserts to be generally available for developers to use. In many cases upgrading older versions won't even be possible.

# A workaround

I've been looking for possible solutions for some time. There are many suggested workarounds floating through the Interwebs. My solution uses writable [CTEs](http://www.postgresql.org/docs/9.4/static/queries-with.html) and is available starting from v9.1.

{% gist 3c1ea13a578ded191ed7 %}

This would on first run add row ```('metric.1',1)``` and on subsequent runs increment the value giving us simulated upserts in PostgreSQL <=9.4. You can [see it at work in SQLFiddle](http://sqlfiddle.com/#!15/8abab/5/0).

This works by defining two sets: ```"upserts"``` (the rows being updated) and ```"new"``` (entries to be inserted). The final element is an ```INSERT``` composed from selecting data from ```"new"``` set which do not occure in ```"updated"``` (by ```JOINING``` the results).

# Considerations

This approach has several cavets. First of all this is not a single atomic query (like an upsert should be). With out serializable transactions this will give inconsistent results as these are 3 independant queries, two of which (defining CTE sets) happen simultaneously. Big data sets are another considiration as ```JOINING``` CTE results will create temporary table without indexes.

# Conclusion

YMMV. I like it untill I can get The Real Thing. It very flexible, scaling from single row with application provided data to working on existing sets.