Postgresql
==========

Bash commands
-------------

```bash
# Restore from dump:
psql db < db.dump
```

SQL commands
------------

```sql
/* List databases */
\l

/* Connect to database */

\c workout

/* List schema in db */

\d

/* Select (can see structure) */

select * from "Accounts";

/* Insert row */

insert into "Accounts" values ('x', 'y', 'z');
insert into "Sets"("order","reps","progressionGroup","exercise","routine","day") values (1, 5, 12, 'Weighted squat', 'Starting Strength', 'A');

/* Delete rows */

delete from Table where x=123;


/* Create db */

createdb dbname

/* Delete db */

dropdb dbname

/* Create superuser */

su - posgres
CREATE USER root;
ALTER USER myuser WITH SUPERUSER;
ALTER USER root WITH password='pw-here';

/* JSON join */
SELECT github_member.json->>'login',github_team.json->>'name' FROM github_member JOIN github_team ON (github_member.github_team_id = github_team.id) WHERE github_member.json->>'login'='Shookit';
```
