Curl
====

POST
----

```bash
curl -H "Content-Type: application/json" -X POST -d '{"username":"xyz","password":"xyz"}' https://foo:bar@localhost:5000/post_endpoint/ -k -v
# -d specifies POST data
# foo:bar@ is used for basic-auth
# -k ignores invalid certs in https
# -v shows entire header, not just response
# -H impacts how data is decoded and used on the receiving end; application-specific
```

GET
---

```
curl -X GET https://localhost:5000/get_endpoint/
```

Content-Type
------------

Content-Type id application-specific, but here are some notes on how Flask handles incoming POST data:

- `Content-Type: application/json` means request.json will get data
- `Content-Type: application/x-www-form-urlencoded` means request.form will get data. This is used by html forms, and is often a default (curl uses this as a default).
