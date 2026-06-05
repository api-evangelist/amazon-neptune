# Amazon Neptune (amazon-neptune)

Amazon Neptune is a fast, reliable, fully managed graph database service that makes it easy to build and run applications that work with highly connected datasets. It supports property graph and RDF models, with multiple query languages including Gremlin, SPARQL, and openCypher.

## Tags

- AWS
- Database
- Graph Database
- Gremlin
- Neptune
- Property Graph
- RDF
- SPARQL

## Timestamps

- **Created:** 2024
- **Modified:** 2026-05-19

## APIs

### Amazon Neptune Management API

Amazon Neptune Management API for creating, managing, and deleting Neptune DB clusters, instances, parameter groups, snapshots, and related infrastructure resources.

#### Tags

- AWS
- Cluster Management
- Database Management
- Graph Database

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/intro.html)
- [OpenAPI](openapi/amazon-neptune-management-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/amazon-neptune-management.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/amazon-neptune-management.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [API Reference](https://docs.aws.amazon.com/neptune/latest/userguide/api.html)
- [Pricing](https://aws.amazon.com/neptune/pricing/)
- [Getting Started](https://docs.aws.amazon.com/neptune/latest/userguide/get-started.html)
- [S D Ks](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/neptune.html)

### Amazon Neptune Data API

Amazon Neptune Data API provides SDK support for more than 40 data operations including data loading, query execution, data inquiry, and machine learning. It supports Gremlin and openCypher query languages.

#### Tags

- Data API
- Data Operations
- Graph Query
- SDK

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/data-api.html)
- [OpenAPI](openapi/amazon-neptune-data-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/amazon-neptune-data.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/amazon-neptune-data.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [API Reference](https://docs.aws.amazon.com/neptune/latest/data-api/Welcome.html)
- [S D Ks](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/neptunedata.html)
- [C L I  Reference](https://docs.aws.amazon.com/cli/latest/reference/neptunedata/)
- [Java Script  S D K](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/client/neptunedata/)
- [Go  S D K](https://docs.aws.amazon.com/sdk-for-go/api/service/neptunedata/)

### Neptune Gremlin API

Apache TinkerPop Gremlin graph traversal language API for querying property graphs in Neptune. It supports both WebSocket and HTTP REST endpoints for submitting Gremlin traversals.

#### Tags

- Graph Traversal
- Gremlin
- Property Graph
- Query Language

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-gremlin.html)
- [OpenAPI](openapi/amazon-neptune-gremlin-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/amazon-neptune-gremlin.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/amazon-neptune-gremlin.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Reference](https://docs.aws.amazon.com/neptune/latest/userguide/gremlin-api-reference.html)
- [Gremlin  Reference](https://tinkerpop.apache.org/docs/current/reference/)
- [Best  Practices](https://docs.aws.amazon.com/neptune/latest/userguide/best-practices-gremlin.html)
- [R E S T  Endpoint](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-gremlin-rest.html)

### Neptune SPARQL API

W3C SPARQL 1.1 query language API for querying RDF graphs in Neptune. It provides an HTTP REST endpoint compatible with the SPARQL 1.1 protocol specification.

#### Tags

- Query Language
- RDF
- Semantic Web
- SPARQL

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-sparql.html)
- [OpenAPI](openapi/amazon-neptune-sparql-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/amazon-neptune-sparql.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/amazon-neptune-sparql.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [S P A R Q L  Reference](https://www.w3.org/TR/sparql11-query/)
- [Best  Practices](https://docs.aws.amazon.com/neptune/latest/userguide/best-practices-sparql.html)
- [R E S T  Endpoint](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-sparql-http-rest.html)

### Neptune openCypher API

openCypher graph query language API for querying property graphs with Cypher syntax in Neptune. It provides an HTTP endpoint for executing openCypher queries against property graph data.

#### Tags

- Cypher
- openCypher
- Property Graph
- Query Language

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-opencypher.html)
- [OpenAPI](openapi/amazon-neptune-opencypher-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/amazon-neptune-opencypher.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/amazon-neptune-opencypher.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [open Cypher  Reference](https://opencypher.org/)
- [Best  Practices](https://docs.aws.amazon.com/neptune/latest/userguide/best-practices-opencypher.html)

### Neptune Streams API

Neptune Streams generates a complete sequence of change-log entries that record every change made to graph data as it happens, enabling real-time capture of graph mutations via a REST API.

#### Tags

- Change Data Capture
- Event Log
- Real-Time
- Streams

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/streams.html)
- [OpenAPI](openapi/amazon-neptune-streams-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/amazon-neptune-streams.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/amazon-neptune-streams.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [API Reference](https://docs.aws.amazon.com/neptune/latest/userguide/streams-using-api-call.html)
- [Response  Format](https://docs.aws.amazon.com/neptune/latest/userguide/streams-using-api-reponse.html)
- [Data  A P I  Reference](https://docs.aws.amazon.com/neptune/latest/userguide/data-api-dp-streams.html)

### Neptune Loader API

Neptune bulk loader API for ingesting large volumes of data from Amazon S3 into a Neptune DB instance. It supports CSV formats for property graphs and multiple RDF serialization formats.

#### Tags

- Bulk Import
- Data Ingestion
- Data Loading
- ETL

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load.html)
- [OpenAPI](openapi/amazon-neptune-loader-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/amazon-neptune-loader.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/amazon-neptune-loader.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [API Reference](https://docs.aws.amazon.com/neptune/latest/userguide/load-api-reference.html)
- [Loader  Command](https://docs.aws.amazon.com/neptune/latest/userguide/load-api-reference-load.html)
- [Data  Formats](https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load-tutorial-format.html)
- [Data  A P I  Reference](https://docs.aws.amazon.com/neptune/latest/userguide/data-api-dp-loader.html)

### Neptune ML API

Neptune ML enables machine learning on graph data using graph neural networks. It provides APIs for data processing, model training, and inference endpoint management powered by Amazon SageMaker.

#### Tags

- Graph Neural Network
- Machine Learning
- Predictions
- SageMaker

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/machine-learning.html)
- [OpenAPI](openapi/amazon-neptune-ml-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/amazon-neptune-ml.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/amazon-neptune-ml.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [API Reference](https://docs.aws.amazon.com/neptune/latest/userguide/machine-learning-api-reference.html)
- [Model  Training](https://docs.aws.amazon.com/neptune/latest/userguide/data-api-dp-ml-training.html)
- [Getting Started](https://docs.aws.amazon.com/neptune/latest/userguide/machine-learning-overview.html)

### Neptune Analytics API

Neptune Analytics is a memory-optimized graph database engine for analytics, providing optimized graph analytic algorithms, low-latency queries, and vector search capabilities within graph traversals.

#### Tags

- Analytics
- Graph Analytics
- In-Memory
- Vector Search

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune-analytics/latest/userguide/what-is-neptune-analytics.html)
- [OpenAPI](openapi/amazon-neptune-analytics-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/amazon-neptune-analytics.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/amazon-neptune-analytics.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [API Reference](https://docs.aws.amazon.com/neptune-analytics/latest/apiref/Welcome.html)
- [S D Ks](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/neptune-graph.html)
- [Getting Started](https://docs.aws.amazon.com/neptune-analytics/latest/userguide/gettingStarted-accessing.html)

## Common Properties

- [Arazzo Workflows](arazzo/) — [Arazzo Specification](https://spec.openapis.org/arazzo/latest.html)
- [Portal](https://aws.amazon.com/neptune/)
- [Documentation](https://docs.aws.amazon.com/neptune/)
- [Getting Started](https://aws.amazon.com/neptune/getting-started/)
- [Authentication](https://docs.aws.amazon.com/neptune/latest/userguide/iam-auth.html)
- [Blog](https://aws.amazon.com/blogs/database/category/database/amazon-neptune/)
- [Changelog](https://docs.aws.amazon.com/neptune/latest/userguide/doc-history.html)
- [Release Notes](https://docs.aws.amazon.com/neptune/latest/userguide/engine-releases.html)
- [Status Page](https://health.aws.amazon.com/)
- [Support](https://repost.aws/tags/TAxVAEdWg1SrS0lClUSX-m_Q)
- [Terms of Service](https://aws.amazon.com/service-terms/)
- [Privacy Policy](https://aws.amazon.com/privacy/)
- [GitHub Organization](https://github.com/aws)
- [Community](https://repost.aws/)
- [Website](https://aws.amazon.com/neptune/)
- [Login](https://console.aws.amazon.com/neptune/)
- [Sign Up](https://portal.aws.amazon.com/billing/signup)
- [F A Qs](https://aws.amazon.com/neptune/faqs/)
- [Features](https://aws.amazon.com/neptune/features/)
- [Security](https://docs.aws.amazon.com/neptune/latest/userguide/security.html)
- [Service Level Agreement](https://aws.amazon.com/neptune/sla/)
- [Console](https://console.aws.amazon.com/neptune/)
- [Git Hub  Samples](https://github.com/aws-samples/amazon-neptune-samples)
- [S D Ks](https://docs.aws.amazon.com/neptune/latest/userguide/using-neptune-apis.html)
- [Tools](https://github.com/awslabs/amazon-neptune-tools)
- [Pricing](https://aws.amazon.com/neptune/pricing/)
- [JSON-LD](json-ld/amazon-neptune-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [JSON Schema](json-schema/amazon-neptune-db-cluster-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/amazon-neptune-db-instance-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/amazon-neptune-graph-element-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/amazon-neptune-loader-job-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/amazon-neptune-stream-record-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/amazon-neptune-analytics-graph-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/amazon-neptune-ml-job-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [Features](undefined)
- [Use Cases](undefined)
- [Integrations](undefined)
- [Spectral Rules](rules/amazon-neptune-spectral-rules.yml)
- [Vocabulary](vocabulary/amazon-neptune-vocabulary.yaml)
- [JSON-LD](json-ld/amazon-neptune-analytics-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [JSON-LD](json-ld/amazon-neptune-data-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [JSON-LD](json-ld/amazon-neptune-gremlin-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [JSON-LD](json-ld/amazon-neptune-loader-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [JSON-LD](json-ld/amazon-neptune-management-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [JSON-LD](json-ld/amazon-neptune-ml-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [JSON-LD](json-ld/amazon-neptune-opencypher-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [JSON-LD](json-ld/amazon-neptune-sparql-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [JSON-LD](json-ld/amazon-neptune-streams-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [Integrations](https://aws.amazon.com/partners/)

## Maintainers

**Email:** kin@apievangelist.com
**URL:** https://apievangelist.com
