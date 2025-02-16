Elasticsearch
=============

General
-------

Watch out for how many indexed fields you have. Each of these must be searched when making a query, so it can lead to significant slowdowns. Disable unnecessary ones.

Queries
-------

### Get data from index

```bash
# Data is not sorted if no sort directive is applied)
curl -XGET http://localhost:9200/someindex/_search?q=fieldtosearch:searchstring\&sort=@timestamp:asc\&size=1000\&format=yaml
```

### Search by some ID

```bash
curl -XGET http://localhost:9200/someindex/_search?format=yaml\&sort=@timestamp:asc\&size=1000 -H 'Content-Type: application/json' -d '
{
  "query": {
    "terms": {
      "_id": [ "pPmRKmkBimkKLffTaj_f", "ZcPyD2kBimkKLffTOP7r" ]
    }
  }
}'
```

### Find entries with a particular field

```bash
curl -XGET http://localhost:9200/someindex/_search?format=yaml\&sort=@timestamp:asc\&size=1000 -H 'Content-Type: application/json' -d '
{
    "query": {
        "exists": {
            "field": "@fields.data.time_range.value"
         }
     }
}'
```


### Search by some ID w/ wildcards and nested

```bash
curl -XGET http://localhost:9200/someindex/_search?format=yaml\&sort=@timestamp:asc\&size=10000 -H 'Content-Type: application/json' -d '
{
    "query": {
        "bool": {
            "must": [
                {"wildcard": {"someId": "id-to-match1*"}},
                {"term": {"objtosearch1.id": "somesearchterm1" }},
                {"terms": {"objtosearch2.someproperty": ["somesearchterm2", "somesearchterm3"] }}
            ]
        }
    }
}' > out.yml
```


### Search by ids and time

```bash
curl -XGET http://localhost:9200/someindex/_search?format=yaml\&sort=@timestamp:asc\&size=1000 -H 'Content-Type: application/json' -d '
{
    "query": {
        "match_all": {}
    },
    "filter": {
        "bool": {
            "must": [
                {"term": { "someId": "id1"}},
                {"term": { "someId2": "id2"}},
                {"range": { "@timestamp": {
                    "gte": "2016-03-02T01:25:00",
                    "lte": "2016-03-02T23:25:00"
                }}}
            ]
        }
    }
}'
```

### Use periods to descend JSON hierarchy (like typical JS object)

```json
{"term": { "someobject.somefield": "lkwjefjlk"}},
```

### Use aggregations to bucket the biggest field combinations

```bash
curl -H "Content-Type: application/json" -XGET localhost:9200/_search?format=yaml -H 'Content-Type: application/json' -d '{
    "size": 0,
    "aggs": {
        "pathname_agg": {
            "terms": {
                "field": "pathname",
                "size": 100
            },
            "aggs": {
                "lineno_agg": {
                    "terms": {
                        "field": "lineno"
                    }
                }
            }
        }
    }
}'
```


Indices/templates
-----------------

### See all indices

```bash
curl -XGET http://localhost:9200/_cat/indices?v\&format=yaml
```

### See all templates

```bash
curl -XGET http://localhost:9200/_template?format=yaml
```

### See mapping

```bash
curl -XGET http://localhost:9200/_mapping?format=yaml
curl -XGET http://localhost:9200/someindex/_mapping?format=yaml
```

### Close index

```bash
curl -XPOST http://localhost:9200/someindex/_close
```

### Delete template

```bash
curl -XDELETE localhost:9200/_template/tst
```

### Delete index

```bash
curl -XDELETE localhost:9200/tst-x
```

### PUT template from file

```bash
curl -XPUT localhost:9200/_template/sometemplate --upload-file new.txt
```

### Create new template

```bash
curl -XPUT localhost:9200/_template/tst -H 'Content-Type: application/json' -d '{
    "template" : "tst-*",
    "settings" : {
        "number_of_shards" : 1
    },
    "mappings" : {
        // Mapping for a single type on all indices matching tst-*
        // "type" is the top-level field
        "type1": {
            "properties": {
                "val1": {"type": "date"},
                "val2": {"type": "integer"}
            }
        }
        // Turn off indexing for the object "field1.field2.*", for all types
        "_default_": {
            "properties": {
                "field1": {
                    "properties": {
                        "field2": {
                            "enabled": false,
                            "properties": {}
                        }
                    }
                }
            }
        }
    }
}'
```

### Dynamic templates

```bash
# Field and type matching w/o having to specify whole path
curl -XPUT localhost:9200/_template/sometemplate -H 'Content-Type: application/json' -d '{
  "template" : "indexmatchregex-*",
   "settings": {
       "index.refresh_interval": "5s"
   },
  "mappings": {
    // _default_ matches all types
    "_default_": {
        "dynamic_templates": [
            { "string_fields": {
                  "match":              "*",
                  "match_mapping_type": "string",
                  "mapping": {
                      "type":        "string",
                      "omit_norms":  true,
                      "index":       "not_analyzed"
                  }
               }
            }
          ]
       }
   }
}'
```

If you have a billion fields, indexing all of those full-text can be horrible. This makes the default field query an omnibus all_fields that everything copies to, while still letting you see the individual fields and do non-full-text searches on all of them.

You get most of the benefits of full-text search while still having individualized fields.

```bash
PUT _index_template/application-logs
{
  "template": {
    "settings": {
      "index": {
        "highlight": {
          "max_analyzed_offset": "33000000"
        },
        "mapping": {
          "total_fields": {
            "limit": "20001"
          }
        },
        "refresh_interval": "5s",
        "query": {
          "default_field": [
            "_all_fields"
          ]
        }
      }
    },
    "mappings": {
      "_routing": {
        "required": false
      },
      "numeric_detection": false,
      "dynamic_date_formats": [
        "strict_date_optional_time",
        "yyyy/MM/dd HH:mm:ss Z||yyyy/MM/dd Z"
      ],
      "_source": {
        "excludes": [],
        "includes": [],
        "enabled": true
      },
      "dynamic": true,
      "dynamic_templates": [
        {
          "string_fields": {
            "mapping": {
              "copy_to": "_all_fields",
              "type": "keyword"
            },
            "match_mapping_type": "string",
            "match": "*"
          }
        }
      ],
      "date_detection": true,
      "properties": {
        "username": {
          "copy_to": "_all_fields",
          "type": "keyword",
        },
        "@timestamp": {
          "type": "date"
        },
        "_all_fields": {
          "type": "text"
        },
      }
    }
  },
  "index_patterns": [
    "application-logs-*"
  ],
  "composed_of": []
}
```


### Add data to index

```bash
# Can't PUT directly to the index, need to specify a 'type'
curl -XPUT localhost:9200/tst-x/type1/1 -H 'Content-Type: application/json' -d '
{
    "val": "2015-10-10",
    "val2": "10"
}
'
```

### Add data w/ an untemplated field

```bash
# Mapping will be updated to include a mapping to whatever ES thinks the type is
curl -XPUT localhost:9200/tst-x/type1/3 -H 'Content-Type: application/json' -d '
{
    "val": "2015-10-10",
    "val2": "10",
    "val3": "new, non-templated/non-mapped data!",
    "val5": "2016-10-10"
}
'
```


Elasticsearch Python scroll
---------------------------


```python
import elasticsearch

# Initialize the scroll
es=elasticsearch.Elasticsearch('localhost:9200')
page = es.search(
        index = 'myindex',
        scroll = '2m',
        # search_type = 'scan', # use to disable global sorting, which can be expensive
        size = 1000,
        body = {
          "sort": [
            {
              "@timestamp": {
                "order": "asc",
                "unmapped_type": "boolean"
              }
            }
          ],
          "query": {
            "filtered": {
              "query": {
                "query_string": {
                  "analyze_wildcard": True,
                  "query": "str_to_query AND anot*r"
                }
              },
              "filter": {
                "bool": {
                  "must": [
                    {
                      "range": {
                        "@timestamp": {
                          "gte": 1481415320000,
                          "lte": 1481754790237,
                          "format": "epoch_millis"
                        }
                      }
                    }
                  ],
                  "must_not": []
                }
              }
            }
          },
          "fields": [
            "@timestamp",
            "@message"
          ]
        })
sid = page['_scroll_id']
scroll_size = page['hits']['total']

# Start scrolling
# Each iteration contains data from each of the relevant shards/indices, so gather all data and then sort everything
while (scroll_size > 0):
    for hit in page['hits']['hits']:
        message = hit['fields']['@message'][0]
        timestamp = hit['fields']['@timestamp'][0]
        print timestamp, message

    page = es.scroll(scroll_id=sid, scroll='2m')
    sid = page['_scroll_id']
    scroll_size = len(page['hits']['hits'])
```


Kibana queries
--------------

### See if a field exists

```
_exists_:@fields.@message
```

