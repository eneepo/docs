---
title: "Search"
description: ""
date: 2023-08-06T14:00:39Z
lastmod: 2023-08-06T14:00:39Z
draft: false
images: []
type: docs
menu:
  elk:
    parent: "elasticsearch"
weight: 1005
toc: true
---


## Match Query
Search for documents containing a specific term across fields.
```json
{
  "match": {
    "field_name": "search_term"
  }
}

```

## Multi-Match Query
Search for a term across multiple fields with customizable weights.
```json
{
  "multi_match": {
    "query": "search_term",
    "fields": ["field1", "field2"]
  }
}

```

## Bool Query
Combine various query clauses using boolean logic.
```json
{
  "bool": {
    "must": [],
    "should": [],
    "must_not": [],
    "filter": []
  }
}

```

## Term Query
Match documents containing an exact term in a field.
```json
{
  "term": {
    "field_name": "exact_term"
  }
}

```

## Range Query
Filter documents within a specified numeric or date range.
```json
{
  "range": {
    "field_name": {
      "gte": "min_value",
      "lte": "max_value"
    }
  }
}

```

## Prefix Query
Find documents with a field starting with a specific prefix.
```json
{
  "prefix": {
    "field_name": "prefix_term"
  }
}

```

## Wildcard Query
Search for documents with fields matching a wildcard pattern.
```json
{
  "wildcard": {
    "field_name": "wildcard_term"
  }
}

```

## Fuzzy Query
Retrieve documents with terms similar to a given term.
```json
{
  "fuzzy": {
    "field_name": {
      "value": "fuzzy_term",
      "fuzziness": "auto"
    }
  }
}

```

## Match Phrase Query
Locate documents containing an exact phrase.
```json
{
  "match_phrase": {
    "field_name": "exact_phrase"
  }
}

```

## Match Phrase Prefix Query
Find documents with a phrase prefix.
```json
{
  "match_phrase_prefix": {
    "field_name": "prefix_of_phrase"
  }
}

```

## Exists Query
Filter documents with a specific field present.
```json
{
  "exists": {
    "field": "field_name"
  }
}

```

## Missing Query
Filter documents with a specific field absent.
```json
{
  "bool": {
    "must_not": {
      "exists": {
        "field": "field_name"
      }
    }
  }
}

```

## Terms Query
Search for documents with a field matching multiple values.
```json
{
  "terms": {
    "field_name": ["term1", "term2"]
  }
}

```

## Match All Query
Retrieve all documents from an index.
```json
{
  "match_all": {}
}

```

## Script Query
Use custom scripts to filter documents.
```json
{
  "script": {
    "script": {
      "source": "doc['field_name'].value > threshold"
    }
  }
}

```

## Nested Query
Search within nested objects within documents.
```json
{
  "nested": {
    "path": "nested_field",
    "query": {
      "match": {
        "nested_field.field_name": "value"
      }
    }
  }
}

```

## Regexp Query
Find documents with fields matching a regular expression.
```json
{
  "regexp": {
    "field_name": "regex_pattern"
  }
}

```

## Common Terms Query
Search with stop words using controlled term frequency.
```json
{
  "common": {
    "field_name": {
      "query": "search_term",
      "cutoff_frequency": 0.001
    }
  }
}

```

## GeoShape Query
Search for documents with shapes within a specified geometry.
```json
{
  "geo_shape": {
    "field_name": {
      "shape": {
        "type": "polygon",
        "coordinates": [[ [lon1, lat1], [lon2, lat2], ... ]]
      },
      "relation": "within"
    }
  }
}

```

## GeoDistance Query
Filter documents within a specified distance from a point.
```json
{
  "geo_distance": {
    "distance": "10km",
    "field_name": {
      "lat": lat_value,
      "lon": lon_value
    }
  }
}

```

## GeoBoundingBox Query
Filter documents within a specified bounding box.
```json
{
  "geo_bounding_box": {
    "field_name": {
      "top_left": {
        "lat": lat1,
        "lon": lon1
      },
      "bottom_right": {
        "lat": lat2,
        "lon": lon2
      }
    }
  }
}

```

## More Like This Query
Retrieve documents similar to a given text.
```json
{
  "more_like_this": {
    "fields": ["field1", "field2"],
    "like": ["text to match"],
    "min_term_freq": 1,
    "min_doc_freq": 1
  }
}

```

## Percolate Query
Match documents against stored queries.
```json
{
  "percolate": {
    "field": "query_field",
    "document": {
      "field1": "value1",
      "field2": "value2"
    }
  }
}

```

## Has Parent Query
Search for child documents based on parent's query.
```json
{
  "has_parent": {
    "parent_type": "parent_type",
    "query": {
      "match": {
        "field_name": "value"
      }
    }
  }
}

```

## Has Child Query
Search for parent documents based on child's query.
```json
{
  "has_child": {
    "type": "child_type",
    "query": {
      "match": {
        "field_name": "value"
      }
    }
  }
}

```

## Aggregations
Compute summary statistics and analytics on data.
```json
{
  "aggs": {
    "agg_name": {
      "aggregation_type": {
        "field": "field_name"
      }
    }
  }
}

```

## Function Score Query
Modify query scores using functions.
```json
{
  "function_score": {
    "query": {
      "match": {
        "field_name": "search_term"
      }
    },
    "functions": [
      {
        "gauss": {
          "field_name": {
            "origin": "center_point",
            "scale": "scale_value"
          }
        }
      }
    ],
    "score_mode": "sum"
  }
}

```

## Terms Set Query
Match documents based on a set of terms.
```json
{
  "terms_set": {
    "field_name": {
      "terms": ["term1", "term2"],
      "minimum_should_match_field": "minimum_should_match_field"
    }
  }
}

```

## Parent ID Query
Retrieve parent documents based on child's parent ID.
```json
{
  "parent_id": {
    "type": "parent_type",
    "id": "parent_document_id"
  }
}

```

## Rank Feature Query
Modify scores based on rank feature values.
```json
{
  "rank_feature": {
    "field": "field_name",
    "saturation": {
      "pivot": 10
    }
  }
}

```

## Field Value Factor Function
Modify scores based on field values using factors.
```json
{
  "function_score": {
    "query": {
      "match_all": {}
    },
    "functions": [
      {
        "field_value_factor": {
          "field": "field_name",
          "factor": 1.2,
          "modifier": "log1p"
        }
      }
    ]
  }
}

```

## Span Near Query
Search for terms within a specified proximity.
```json
{
  "span_near": {
    "clauses": [
      { "span_term": { "field_name": "term1" } },
      { "span_term": { "field_name": "term2" } }
    ],
    "slop": 2,
    "in_order": true
  }
}

```

## Boosting Query
Combine positive and negative queries with a boost factor.
```json
{
  "boosting": {
    "positive": {
      "match": { "field_name": "positive_term" }
    },
    "negative": {
      "match": { "field_name": "negative_term" }
    },
    "negative_boost": 0.5
  }
}

```
