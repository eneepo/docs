---
title: "Filtering"
description: ""
date: 2023-08-06T19:44:32Z
lastmod: 2023-08-06T19:44:32Z
draft: false
images: []
type: docs
menu:
  elk:
    parent: "aggregations"
weight: 1130
toc: true
---

Calculate the average "total_amount" for documents with a "total_amount" less than 50.

```json
GET /order/_search
{
  "size": 0,  # Set the result size to 0, as we only need aggregation results.
  "aggs": {
    "low_value": {
      "filter": {
        "range": {
          "total_amount": {
            "lt": 50  # Filter documents where total_amount is less than 50.
          }
        }
      },
      "aggs": {
        "avg_amount": {
          "avg": {
            "field": "total_amount"  # Calculate the average of the total_amount field.
          }
        }
      }
    }
  }
}
```

This query uses a combination of the `filter` aggregation and the `avg` aggregation. It first filters documents with a "total_amount" less than 50 using the `range` filter, and then calculates the average of the "total_amount" field for those filtered documents. The "size": 0 parameter ensures that no actual document results are returned, only the aggregation results.