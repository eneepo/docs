---
title: "Bucket"
description: ""
date: 2023-08-06T19:07:54Z
lastmod: 2023-08-06T19:07:54Z
draft: false
images: []
type: docs
menu:
  elk:
    parent: "aggregations"
weight: 1110
toc: true
---

## Basics
The following queries demonstrate different configurations of the terms aggregation applied to the "status.keyword" field. The terms aggregation groups documents based on the unique values in the specified field, and these queries also showcase additional options like handling missing values and custom ordering. The "size": 0 parameter in each query ensures that no actual document results are returned, only the aggregation results.

Perform a terms aggregation on the "status.keyword" field to get the count of documents for each unique status value.
```json

GET /order/_search
{
  "size": 0,  # Set the result size to 0, as we only need aggregation results.
  "aggs": {
    "status_terms": {
      "terms": {
        "field": "status.keyword"
      }
    }
  }
}
```

Perform a terms aggregation on the "status.keyword" field and handle missing values and minimum document counts, ordering the terms alphabetically by their keys.
```json
GET /order/_search
{
  "size": 0,
  "aggs": {
    "status_terms": {
      "terms": {
        "field": "status.keyword",
        "missing": "N/A",        # Specify the label for missing values.
        "min_doc_count": 0,      # Include missing values with count 0.
        "order": {
          "_key": "asc"           # Order terms by their keys in ascending order.
        }
      }
    }
  }
}
```
