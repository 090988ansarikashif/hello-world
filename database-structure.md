mindstorming for a database which would be used inside searx in future:

regarding to Issue [#205](https://github.com/asciimoo/searx/issues/205)

### Database-Systems

#### SQLite3

* minimalistic, already available inside Python

#### Peewee

* can use SQLite as well as MySQL and PostgreSQL
* more flexible for the admin

### Tables

#### server preferences

We should also have every tables loaded at the start of the server : themes, icons, paths, locales etc.

#### users

if we have a login (at least for admins), we require a user-table

**user**

| db column   | db options       | description                               |
| ----------- | ---------------- | ----------------------------------------- |
| id          | AI, PRIMARY      |                                           |
| username    | INDEX            |                                           |
| password    |                  | salted and hashed                         |
| is_admin    |                  | finer right system would be more flexibel |

#### stats

how could we represent the stats in the best way?

it would be cool if we can represent the data inside a timeline, but without too big overhead. [#162](https://github.com/asciimoo/searx/issues/162)

We could log every request with it timestamp, time, engine (but without the query). It would be precise, but a little bit invasive as privacy is concerned, and could be heavy on the DB.  
We could also log a pondered mean for every span of time we would like (hour, day, week, month). For every week, we could have a line by engine, counting the number of queries, and the mean time of those queries. Adding a query would be simple : M = (m*n + t)/n+1 with M the new mean, m the old one, n the number of queries, and t the time of the new query.

| db column   | db options       | description |
| ----------- | ---------------- | ----------- |
| id | AI, PRIMARY | ID of the timespan | 
| timestamp | | Precise way of determining the time | 
| readable | | A readable format of the time span like '{{Week}} 26' | 
| span | | A way of defining the time span choosen (allow varying the time span in pref without loosing old stats) | 

| db column   | db options       | description |
| ----------- | ---------------- | ----------- |
| id_timespan | FK, PRIMARY | Identify the time span | 
| id_engine | FK, PRIMARY | Identify the engine | 
| mean_time | | Mean time of a query | 
| mean_nb_result | | Mean number of results |
| mean_score | | Mean score |
| mean_score_result | | Mean score per result|
| nb_query | | Number of queries | 
| nb_error | | Number of errors | 

#### https_rewrite

if we are specify only one url_pattern for every possible url, we can use Database search instead of regex-matching. This should improve the speed well, specifically for larger datasets.

I think the most efficient implementation would be a n:m representation. We can cache the rewrite_rules inside python, and use the database to find out what rewrite_rules have to be called.

**https_urls**

| db column   | db options       | description   |
| ----------- | ---------------- | ------------- |
| id          | AI, PRIMARY      |               |
| url_pattern | UNIQE, INDEX     | wildcard is inside database, how to handle this? |

**https_url_to_rewrite**

| db column   | db options       | description   |
| ----------- | ---------------- | ------------- |
| id          | AI, PRIMARY      |               |
| url_id      | INDEX            |               |
| rewrite_id  |                  |               |

**https_rewrite_rules**

| db column    | db options       | description   |
| ------------ | ---------------- | ------------- |
| id           | AI, PRIMARY      |               |
| rewrite_from |                  |               |
| rewrite_to   |                  |               |
