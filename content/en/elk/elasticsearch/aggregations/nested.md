---
title: "Nested"
description: ""
date: 2023-08-06T19:33:11Z
lastmod: 2023-08-06T19:33:11Z
draft: false
images: []
type: docs
menu:
  elk:
    parent: "aggregations"
weight: 1120
toc: true
---


Perform a terms aggregation on the "status.keyword" field to get the count of documents for each unique status value. Additionally, calculate statistics (count, sum, average, min, max) of the "total_amount" field for each status value.
```json
GET /order/_search
{
  "size": 0,  # Set the result size to 0, as we only need aggregation results.
  "aggs": {
    "status_terms": {
      "terms": {
        "field": "status.keyword"
      },
      "aggs": {
        "status_stats": {
          "stats": {
            "field": "total_amount"
          }
        }
      }
    }
  }
}
```


Perform a terms aggregation on the "status.keyword" field for documents with a "total_amount" greater than or equal to 100. Additionally, calculate statistics (count, sum, average, min, max) of the "total_amount" field for each status value.
```json
GET /order/_search
{
  "size": 0,
  "query": {
    "range": {
      "total_amount": {
        "gte": 100   # Filter documents where total_amount is greater than or equal to 100.
      }
    }
  },
  "aggs": {
    "status_terms": {
      "terms": {
        "field": "status.keyword"
      },
      "aggs": {
        "status_stats": {
          "stats": {
            "field": "total_amount"
          }
        }
      }
    }
  }
}
```

These queries both include terms aggregation on the "status.keyword" field and calculate statistics of the "total_amount" field for each status value. The second query adds an additional filter to only consider documents with a "total_amount" greater than or equal to 100. The "size": 0 parameter in each query ensures that no actual document results are returned, only the aggregation results. The term's aggregation runs in the context of the query, while the stats aggregation runs in the context of its parent aggregation being a bucket aggregation. This means that it operates on the buckets produced by that aggregation.