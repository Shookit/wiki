Infrastructure
==============


Database technology migrations
------------------------------

- Setup
    - Create the new database
    - Create an adapter that sends traffic to both the old and the new database, using the old one as primary
- Simultaneously write for a period of time, fixing any issues as they arise
- Migrate+validate records from the old database into the new, via adapters
    1. Get content from old (e.g., Mongo)
    2. Post content to new (e.g., Postgres)
    3. Get content from new (e.g., Postgres)
    4. Check that the responses from one and three are identical
- Make the new database the primary
- Delete the old database
