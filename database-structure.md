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
