---
layout:   post
title:    "Live rebuild indexes with Elasticsearch"
date:     2016-03-13 21:54:00 +1
category: elasticsearch
disqus:   true
tags:
  - elasticsearch
  - rabbitmq
  - micro services
  - refeed
---

Rebuilding a new Elasticsearch indexes during primetime is a must.

### Considerations

* Create a new index with read model updates and pour all existing data into it
* Continuously updated and maintain a backup cluster
* Test a newly created index before activating it
* All of the above needs to be done while users are accessing, modifying data
  and with information lost

## Wording

* **Feed:** Continuously update an existing _set_ of Elasticsearch indexes with
   changes
* **Re-feed:** Create a new index and indexing all existing data into

## Types of services

* **Feeder:**
 1. Maintain a list of current Elasticsearch indexes
 1. Read source document stream
 1. Map source documents to Elasticsearch read model
 1. Index Elasticsearch read model to all current Elasticsearch indexes
* **Re-feeder:**
 1. Read batch description stream
 1. Map batch description to source document list
 1. Map source document list to Elasticsearch read models
 1. Bulk index Elasticsearch read models to specific Elasticsearch index
* **Re-feed leader:**
 1. Read re-feed start description stream
 1. Create new Elasticsearch index
 1. Create list of batch descriptions
 1. Publish batch descriptions to re-feeders
* **Web API:**
 1. Decide proper index name
 1. Publish re-feed start message with index name to re-feed leader(s)
 1. Set active Elasticsearch index when re-feeding is done

## Data Types

* **Source document:**
* **Read model:**
* **Re-feed start description:**
* **Batch description:**
 - `minimum id`
 - `maximum id`
 - `index name`
* **Batch:**

## Scenario 1
