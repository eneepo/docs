---
title: "Metric"
description: ""
date: 2023-08-06T18:56:36Z
lastmod: 2023-08-06T18:56:36Z
draft: false
images: []
type: docs
menu:
  elk:
    parent: "aggregations"
weight: 1100
toc: true
---

These queries demonstrate different types of aggregations in Elasticsearch, including sum, average, minimum, maximum, cardinality, value count, and statistics calculations. The "size": 0 parameter in each query ensures that no actual document results are returned, only the aggregation results.

## sum, avg, min, max
Calculate various metrics (sum, average, minimum, maximum) of the "total_amount" field for all documents in the "order" index.
```json
GET /order/_search
{
  "size": 0,
  "aggs": {
    "total_sales": {
      "sum": {
        "field": "total_amount"
      }
    },
    "avg_sale": {
      "avg": {
        "field": "total_amount"
      }
    },
    "min_sale": {
      "min": {
        "field": "total_amount"
      }
    },
    "max_sale": {
      "max": {
        "field": "total_amount"
      }
    }
  }
}
```

## cardinality
Count the number of distinct values in the "salesman.id" field across all documents in the "order" index.
```json
GET /order/_search
{
  "size": 0,
  "aggs": {
    "total_salesman": {
      "cardinality": {
        "field": "salesman.id"
      }
    }
  }
}
```

## value_count
Count the number of non-null values in the "total_amount" field for all documents in the "order" index.
```json
GET /order/_search
{
  "size": 0,
  "aggs": {
    "values_count": {
      "value_count": {
        "field": "total_amount"
      }
    }
  }
}
```

## stats
Calculate various statistics (count, sum, average, min, max) of the "total_amount" field for all documents in the "order" index.
```json
GET /order/_search
{
  "size": 0,
  "aggs": {
    "amount_stats": {
      "stats": {
        "field": "total_amount"
      }
    }
  }
}
```
