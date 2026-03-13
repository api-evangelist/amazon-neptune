# Amazon Neptune

Amazon Neptune is a fast, reliable, fully managed graph database service that makes it easy to build and run applications that work with highly connected datasets. It supports property graph and RDF models, with multiple query languages including Gremlin, SPARQL, and openCypher.

## APIs

- **Amazon Neptune Management API** - Amazon Neptune Management API for creating, managing, and deleting Neptune DB clusters, instances, parameter groups, snapshots, and related infrastructure resources.
- **Amazon Neptune Data API** - Amazon Neptune Data API provides SDK support for more than 40 data operations including data loading, query execution, data inquiry, and machine learning. It supports Gremlin and openCypher query languages.
- **Neptune Gremlin API** - Apache TinkerPop Gremlin graph traversal language API for querying property graphs in Neptune. It supports both WebSocket and HTTP REST endpoints for submitting Gremlin traversals.
- **Neptune SPARQL API** - W3C SPARQL 1.1 query language API for querying RDF graphs in Neptune. It provides an HTTP REST endpoint compatible with the SPARQL 1.1 protocol specification.
- **Neptune openCypher API** - openCypher graph query language API for querying property graphs with Cypher syntax in Neptune. It provides an HTTP endpoint for executing openCypher queries against property graph data.
- **Neptune Streams API** - Neptune Streams generates a complete sequence of change-log entries that record every change made to graph data as it happens, enabling real-time capture of graph mutations via a REST API.
- **Neptune Loader API** - Neptune bulk loader API for ingesting large volumes of data from Amazon S3 into a Neptune DB instance. It supports CSV formats for property graphs and multiple RDF serialization formats.
- **Neptune ML API** - Neptune ML enables machine learning on graph data using graph neural networks. It provides APIs for data processing, model training, and inference endpoint management powered by Amazon SageMaker.
- **Neptune Analytics API** - Neptune Analytics is a memory-optimized graph database engine for analytics, providing optimized graph analytic algorithms, low-latency queries, and vector search capabilities within graph traversals.

## Resources

### Documentation

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/intro.html)
- [API Reference](https://docs.aws.amazon.com/neptune/latest/userguide/api.html)
- [Data API Reference](https://docs.aws.amazon.com/neptune/latest/data-api/Welcome.html)
- [Getting Started](https://docs.aws.amazon.com/neptune/latest/userguide/get-started.html)
- [Features](https://aws.amazon.com/neptune/features/)
- [FAQs](https://aws.amazon.com/neptune/faqs/)

### Specifications

- [OpenAPI - Management](openapi/amazon-neptune-management-openapi.yml)
- [OpenAPI - Data](openapi/amazon-neptune-data-openapi.yml)
- [OpenAPI - Gremlin](openapi/amazon-neptune-gremlin-openapi.yml)
- [OpenAPI - SPARQL](openapi/amazon-neptune-sparql-openapi.yml)
- [OpenAPI - openCypher](openapi/amazon-neptune-opencypher-openapi.yml)
- [OpenAPI - Streams](openapi/amazon-neptune-streams-openapi.yml)
- [OpenAPI - Loader](openapi/amazon-neptune-loader-openapi.yml)
- [OpenAPI - ML](openapi/amazon-neptune-ml-openapi.yml)
- [OpenAPI - Analytics](openapi/amazon-neptune-analytics-openapi.yml)
- [JSON-LD Context](json-ld/amazon-neptune-context.jsonld)
- [JSON Schema - DB Cluster](json-schema/amazon-neptune-db-cluster-schema.json)
- [JSON Schema - DB Instance](json-schema/amazon-neptune-db-instance-schema.json)
- [JSON Schema - Graph Element](json-schema/amazon-neptune-graph-element-schema.json)
- [JSON Schema - Loader Job](json-schema/amazon-neptune-loader-job-schema.json)
- [JSON Schema - Stream Record](json-schema/amazon-neptune-stream-record-schema.json)
- [JSON Schema - Analytics Graph](json-schema/amazon-neptune-analytics-graph-schema.json)
- [JSON Schema - ML Job](json-schema/amazon-neptune-ml-job-schema.json)

### General

- [Pricing](https://aws.amazon.com/neptune/pricing/)
- [Security](https://docs.aws.amazon.com/neptune/latest/userguide/security.html)
- [Service Level Agreement](https://aws.amazon.com/neptune/sla/)
- [Blog](https://aws.amazon.com/blogs/database/category/database/amazon-neptune/)
- [GitHub Samples](https://github.com/aws-samples/amazon-neptune-samples)
- [Tools](https://github.com/awslabs/amazon-neptune-tools)

## Maintainers

- Kin Lane - kin@apievangelist.com
