---
layout: post
title:  "Redshift 'failed to build any 3-way joins'"
date:   2018-08-18
---

Amazon Redshift, sensibly enough, handles a lot of type conversion implicitly. Numeric types (and many others) generally don't need to be cast or convert to be compared, added or pretty much any other operation.

So it's easy to to do things like `yourINT + yourREAL` or `yourINT > 10.5` and everything works smoothly.

It turns out however, the one place that implicit type conversion doesn't happen is in the HAVING clause - and the error you'll get when this occurs is the ever informative 'failed to build any 3-way joins'. The only other reference I can find to this error message on the internet is a note that a bug had been fixed from a PostgreSQL mailing list in 2010. But it turns out, that it can also happen when you're running a query like this one on Redshift:

```sql
SELECT
	country.name
	,count(city.id)
FROM country
LEFT JOIN city on city.country_id = country.id
GROUP BY country.name
HAVING sum(city.INTfield) > 10.0
``` 

I couldn't find any note on this in the Redshift documentation or elsewhere, so hopefully this can save someone some time in the future. You can easily solve the issue using the CAST(field AS type) function, or in this case, simply removing the decimal from the comparison.
