---
title: Elasticsearch
date: 2021-03-08 13:10:22 +0800
categories: [bigdata, elasticsearch]
tags: [elasticsearch]
---

# What is Elasticsearch?
Elasticsearch is a distributed document store, a NoSQL database which stores a data in a JSON document form. Instead of storing information as rows of columnar data, Elasticsearch stores complex data structures that have been serialized as JSON documents.

## documents and indices
An index can be thought of as an optimized collection of documents and each document is a collection of fields, which are the key-value pairs that contain your data. Data in the index is spread through the **shards**, which are searched independently.
-  inverted index
An inverted index lists every unique word that appears in any document and identifies all of the documents each word occurs in.By default, Elasticsearch indexes all data in every field and each indexed field has a dedicated, optimized data structure. For example, text fields are stored in inverted indices, and numeric and geo fields are stored in BKD trees.
![](../assets/img/sample/inverse-index.png)
- schema-less
Elasticsearch also has the ability to be schema-less, which means that documents can be indexed without explicitly specifying how to handle each of the different fields that might occur in a document.

[Guide to Elasticsearch in Java](https://www.baeldung.com/elasticsearch-java)
[ElasticSearch as Flink Sinker](https://www.flinkinpractice.com/2019/10/29/ElasticSearch-as-Sinker/)
[Search APIs](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/master/java-rest-high-search.html)
[ES Demo](https://www.javacodegeeks.com/2018/03/elasticsearch-tutorial-beginners.html)

[Elasticsearch Flink Connector](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/elasticsearch.html)
```
import org.apache.flink.api.common.functions.RuntimeContext;
import org.apache.flink.streaming.api.datastream.DataStream;
import org.apache.flink.streaming.connectors.elasticsearch.ElasticsearchSinkFunction;
import org.apache.flink.streaming.connectors.elasticsearch.RequestIndexer;
import org.apache.flink.streaming.connectors.elasticsearch6.ElasticsearchSink;

import org.apache.http.HttpHost;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.client.Requests;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

DataStream<String> input = ...;

List<HttpHost> httpHosts = new ArrayList<>();
httpHosts.add(new HttpHost("127.0.0.1", 9200, "http"));
httpHosts.add(new HttpHost("10.2.3.1", 9200, "http"));

// use a ElasticsearchSink.Builder to create an ElasticsearchSink
ElasticsearchSink.Builder<String> esSinkBuilder = new ElasticsearchSink.Builder<>(
    httpHosts,
    new ElasticsearchSinkFunction<String>() {
        public IndexRequest createIndexRequest(String element) {
            Map<String, String> json = new HashMap<>();
            json.put("data", element);

            return Requests.indexRequest()
                    .index("my-index")
                    .type("my-type")
                    .source(json);
        }

        @Override
        public void process(String element, RuntimeContext ctx, RequestIndexer indexer) {
            indexer.add(createIndexRequest(element));
        }
    }
);

// configuration for the bulk requests; this instructs the sink to emit after every element, otherwise they would be buffered
esSinkBuilder.setBulkFlushMaxActions(1);

// provide a RestClientFactory for custom configuration on the internally created REST client
esSinkBuilder.setRestClientFactory(
  restClientBuilder -> {
    restClientBuilder.setDefaultHeaders(...)
    restClientBuilder.setMaxRetryTimeoutMillis(...)
    restClientBuilder.setPathPrefix(...)
    restClientBuilder.setHttpClientConfigCallback(...)
  }
);

// finally, build and add the sink to the job's pipeline
input.addSink(esSinkBuilder.build());
```
