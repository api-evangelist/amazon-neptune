# Amazon Neptune (amazon-neptune)
Amazon Neptune is a fast, reliable, fully managed graph database service that makes it easy to build and run applications that work with highly connected datasets. It supports property graph and RDF models, with multiple query languages including Gremlin, SPARQL, and openCypher.

**URL:** [Visit APIs.json URL](https://raw.githubusercontent.com/api-evangelist/amazon-neptune/refs/heads/main/apis.yml)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags:

 - AWS, Database, Graph Database, Gremlin, Neptune, Property Graph, RDF, SPARQL

## Timestamps

- **Created:** 2024
- **Modified:** 2026-04-19

## APIs

### Amazon Neptune Management API
Amazon Neptune Management API for creating, managing, and deleting Neptune DB clusters, instances, parameter groups, snapshots, and related infrastructure resources.

**Human URL:** [https://aws.amazon.com/neptune/](https://aws.amazon.com/neptune/)

#### Tags:

 - AWS, Cluster Management, Database Management, Graph Database

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/intro.html)
- [OpenAPI](openapi/amazon-neptune-management-openapi.yml)
- [API Reference](https://docs.aws.amazon.com/neptune/latest/userguide/api.html)
- [Pricing](https://aws.amazon.com/neptune/pricing/)
- [Getting Started](https://docs.aws.amazon.com/neptune/latest/userguide/get-started.html)
- [SDKs](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/neptune.html)

### Amazon Neptune Data API
Amazon Neptune Data API provides SDK support for more than 40 data operations including data loading, query execution, data inquiry, and machine learning. It supports Gremlin and openCypher query languages.

**Human URL:** [https://docs.aws.amazon.com/neptune/latest/userguide/data-api.html](https://docs.aws.amazon.com/neptune/latest/userguide/data-api.html)

#### Tags:

 - Data API, Data Operations, Graph Query, SDK

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/data-api.html)
- [OpenAPI](openapi/amazon-neptune-data-openapi.yml)
- [API Reference](https://docs.aws.amazon.com/neptune/latest/data-api/Welcome.html)
- [SDKs](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/neptunedata.html)
- [CLI Reference](https://docs.aws.amazon.com/cli/latest/reference/neptunedata/)
- [JavaScript SDK](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/client/neptunedata/)
- [Go SDK](https://docs.aws.amazon.com/sdk-for-go/api/service/neptunedata/)

### Neptune Gremlin API
Apache TinkerPop Gremlin graph traversal language API for querying property graphs in Neptune. It supports both WebSocket and HTTP REST endpoints for submitting Gremlin traversals.

**Human URL:** [https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-gremlin.html](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-gremlin.html)

#### Tags:

 - Graph Traversal, Gremlin, Property Graph, Query Language

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-gremlin.html)
- [OpenAPI](openapi/amazon-neptune-gremlin-openapi.yml)
- [Reference](https://docs.aws.amazon.com/neptune/latest/userguide/gremlin-api-reference.html)
- [Gremlin Reference](https://tinkerpop.apache.org/docs/current/reference/)
- [Best Practices](https://docs.aws.amazon.com/neptune/latest/userguide/best-practices-gremlin.html)
- [REST Endpoint](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-gremlin-rest.html)

### Neptune SPARQL API
W3C SPARQL 1.1 query language API for querying RDF graphs in Neptune. It provides an HTTP REST endpoint compatible with the SPARQL 1.1 protocol specification.

**Human URL:** [https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-sparql.html](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-sparql.html)

#### Tags:

 - Query Language, RDF, Semantic Web, SPARQL

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-sparql.html)
- [OpenAPI](openapi/amazon-neptune-sparql-openapi.yml)
- [SPARQL Reference](https://www.w3.org/TR/sparql11-query/)
- [Best Practices](https://docs.aws.amazon.com/neptune/latest/userguide/best-practices-sparql.html)
- [REST Endpoint](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-sparql-http-rest.html)

### Neptune openCypher API
openCypher graph query language API for querying property graphs with Cypher syntax in Neptune. It provides an HTTP endpoint for executing openCypher queries against property graph data.

**Human URL:** [https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-opencypher.html](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-opencypher.html)

#### Tags:

 - Cypher, openCypher, Property Graph, Query Language

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-opencypher.html)
- [OpenAPI](openapi/amazon-neptune-opencypher-openapi.yml)
- [openCypher Reference](https://opencypher.org/)
- [Best Practices](https://docs.aws.amazon.com/neptune/latest/userguide/best-practices-opencypher.html)

### Neptune Streams API
Neptune Streams generates a complete sequence of change-log entries that record every change made to graph data as it happens, enabling real-time capture of graph mutations via a REST API.

**Human URL:** [https://docs.aws.amazon.com/neptune/latest/userguide/streams.html](https://docs.aws.amazon.com/neptune/latest/userguide/streams.html)

#### Tags:

 - Change Data Capture, Event Log, Real-Time, Streams

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/streams.html)
- [OpenAPI](openapi/amazon-neptune-streams-openapi.yml)
- [API Reference](https://docs.aws.amazon.com/neptune/latest/userguide/streams-using-api-call.html)
- [Response Format](https://docs.aws.amazon.com/neptune/latest/userguide/streams-using-api-reponse.html)
- [Data API Reference](https://docs.aws.amazon.com/neptune/latest/userguide/data-api-dp-streams.html)

### Neptune Loader API
Neptune bulk loader API for ingesting large volumes of data from Amazon S3 into a Neptune DB instance. It supports CSV formats for property graphs and multiple RDF serialization formats.

**Human URL:** [https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load.html](https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load.html)

#### Tags:

 - Bulk Import, Data Ingestion, Data Loading, ETL

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load.html)
- [OpenAPI](openapi/amazon-neptune-loader-openapi.yml)
- [API Reference](https://docs.aws.amazon.com/neptune/latest/userguide/load-api-reference.html)
- [Loader Command](https://docs.aws.amazon.com/neptune/latest/userguide/load-api-reference-load.html)
- [Data Formats](https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load-tutorial-format.html)
- [Data API Reference](https://docs.aws.amazon.com/neptune/latest/userguide/data-api-dp-loader.html)

### Neptune ML API
Neptune ML enables machine learning on graph data using graph neural networks. It provides APIs for data processing, model training, and inference endpoint management powered by Amazon SageMaker.

**Human URL:** [https://aws.amazon.com/neptune/machine-learning/](https://aws.amazon.com/neptune/machine-learning/)

#### Tags:

 - Graph Neural Network, Machine Learning, Predictions, SageMaker

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune/latest/userguide/machine-learning.html)
- [OpenAPI](openapi/amazon-neptune-ml-openapi.yml)
- [API Reference](https://docs.aws.amazon.com/neptune/latest/userguide/machine-learning-api-reference.html)
- [Model Training](https://docs.aws.amazon.com/neptune/latest/userguide/data-api-dp-ml-training.html)
- [Getting Started](https://docs.aws.amazon.com/neptune/latest/userguide/machine-learning-overview.html)

### Neptune Analytics API
Neptune Analytics is a memory-optimized graph database engine for analytics, providing optimized graph analytic algorithms, low-latency queries, and vector search capabilities within graph traversals.

**Human URL:** [https://docs.aws.amazon.com/neptune-analytics/latest/userguide/what-is-neptune-analytics.html](https://docs.aws.amazon.com/neptune-analytics/latest/userguide/what-is-neptune-analytics.html)

#### Tags:

 - Analytics, Graph Analytics, In-Memory, Vector Search

#### Properties

- [Documentation](https://docs.aws.amazon.com/neptune-analytics/latest/userguide/what-is-neptune-analytics.html)
- [OpenAPI](openapi/amazon-neptune-analytics-openapi.yml)
- [API Reference](https://docs.aws.amazon.com/neptune-analytics/latest/apiref/Welcome.html)
- [SDKs](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/neptune-graph.html)
- [Getting Started](https://docs.aws.amazon.com/neptune-analytics/latest/userguide/gettingStarted-accessing.html)

## Common Properties

- [Portal](https://aws.amazon.com/neptune/)
- [Documentation](https://docs.aws.amazon.com/neptune/)
- [Getting Started](https://aws.amazon.com/neptune/getting-started/)
- [Authentication](https://docs.aws.amazon.com/neptune/latest/userguide/iam-auth.html)
- [Blog](https://aws.amazon.com/blogs/database/category/database/amazon-neptune/)
- [Change Log](https://docs.aws.amazon.com/neptune/latest/userguide/doc-history.html)
- [Release Notes](https://docs.aws.amazon.com/neptune/latest/userguide/engine-releases.html)
- [Status](https://health.aws.amazon.com/)
- [Support](https://repost.aws/tags/TAxVAEdWg1SrS0lClUSX-m_Q)
- [Terms of Service](https://aws.amazon.com/service-terms/)
- [Privacy Policy](https://aws.amazon.com/privacy/)
- [GitHub Organization](https://github.com/aws)
- [Community](https://repost.aws/)
- [Website](https://aws.amazon.com/neptune/)
- [Login](https://console.aws.amazon.com/neptune/)
- [Sign Up](https://portal.aws.amazon.com/billing/signup)
- [FAQs](https://aws.amazon.com/neptune/faqs/)
- [Security](https://docs.aws.amazon.com/neptune/latest/userguide/security.html)
- [Service Level Agreement](https://aws.amazon.com/neptune/sla/)
- [Console](https://console.aws.amazon.com/neptune/)
- [GitHub Samples](https://github.com/aws-samples/amazon-neptune-samples)
- [SDKs](https://docs.aws.amazon.com/neptune/latest/userguide/using-neptune-apis.html)
- [Tools](https://github.com/awslabs/amazon-neptune-tools)
- [Pricing](https://aws.amazon.com/neptune/pricing/)
- [JSON-LD](json-ld/amazon-neptune-context.jsonld)
- [JSONSchema](json-schema/amazon-neptune-db-cluster-schema.json)
- [JSONSchema](json-schema/amazon-neptune-db-instance-schema.json)
- [JSONSchema](json-schema/amazon-neptune-graph-element-schema.json)
- [JSONSchema](json-schema/amazon-neptune-loader-job-schema.json)
- [JSONSchema](json-schema/amazon-neptune-stream-record-schema.json)
- [JSONSchema](json-schema/amazon-neptune-analytics-graph-schema.json)
- [JSONSchema](json-schema/amazon-neptune-ml-job-schema.json)
- [SpectralRules](rules/amazon-neptune-spectral-rules.yml)
- [Vocabulary](vocabulary/amazon-neptune-vocabulary.yaml)
- [NaftikoCapability](capabilities/neptune-graph-management.yaml)
- [NaftikoCapability](capabilities/neptune-analytics-ml.yaml)
- [JSON-LD](json-ld/amazon-neptune-analytics-context.jsonld)
- [JSON-LD](json-ld/amazon-neptune-data-context.jsonld)
- [JSON-LD](json-ld/amazon-neptune-gremlin-context.jsonld)
- [JSON-LD](json-ld/amazon-neptune-loader-context.jsonld)
- [JSON-LD](json-ld/amazon-neptune-management-context.jsonld)
- [JSON-LD](json-ld/amazon-neptune-ml-context.jsonld)
- [JSON-LD](json-ld/amazon-neptune-opencypher-context.jsonld)
- [JSON-LD](json-ld/amazon-neptune-sparql-context.jsonld)
- [JSON-LD](json-ld/amazon-neptune-streams-context.jsonld)

## Features

| Name | Description |
|------|-------------|
| Serverless Graph Database | Automatically scales compute and memory resources based on workload demands without requiring capacity planning. |
| Multiple Query Language Support | Supports Apache TinkerPop Gremlin, openCypher, and SPARQL 1.1 query languages for property graph and RDF models. |
| High Availability | Multi-AZ deployment with up to 15 read replicas, automated failover, and continuous backups with point-in-time recovery up to 35 days. |
| Global Database | Multi-region replication with sub-second latency across up to five secondary clusters for global applications. |
| Neptune Analytics | Memory-optimized graph analytics engine for analyzing tens of billions of relationships within seconds with vector search capabilities. |
| GraphRAG Support | Fully managed GraphRAG with Amazon Bedrock Knowledge Bases for AI-enhanced graph retrieval augmented generation. |
| Machine Learning on Graphs | Native graph neural network support via Neptune ML powered by Amazon SageMaker for link prediction and node classification. |
| ACID Transactions | Full ACID transaction support ensuring data consistency and integrity across graph operations. |
| AWS Security Integration | VPC network isolation, IAM resource permissions, AWS KMS encryption, TLS in-transit encryption, and CloudWatch audit logging. |
| Auto-Expanding Storage | Storage automatically grows up to 128 TiB with self-healing architecture spanning three availability zones. |

## Use Cases

| Name | Description |
|------|-------------|
| Knowledge Graphs and GraphRAG | Build knowledge graphs to enhance AI accuracy, comprehensiveness, and explainability using GraphRAG with Amazon Bedrock. |
| Fraud Detection | Model transaction and account relationship networks to detect fraudulent patterns in near real-time using graph traversals. |
| Customer 360 | Build unified customer profile graphs linking purchases, preferences, and interactions for personalization and marketing. |
| Cybersecurity and Threat Detection | Model IT infrastructure as a connected graph to detect attack paths, anomalies, and proactive threats. |
| Recommendation Engines | Power product and content recommendation engines by traversing user-item relationship graphs. |
| Social Networks | Model and query highly connected social graph data for applications requiring relationship traversal at scale. |
| Network and IT Operations | Map network topology, dependencies, and configuration relationships for operations and impact analysis. |
| Supply Chain Management | Model complex supply chain relationships and dependencies for optimization and risk analysis. |

## Integrations

| Name | Description |
|------|-------------|
| Amazon Bedrock | Integration with Bedrock Knowledge Bases for fully managed GraphRAG and AI-enhanced knowledge graph applications. |
| Amazon SageMaker | Neptune ML uses SageMaker for training graph neural network models on Neptune graph data. |
| Amazon S3 | Bulk data loading from S3 using the Neptune Loader API with CSV and RDF serialization format support. |
| AWS IAM | Fine-grained resource-level access control and role-based permissions via AWS Identity and Access Management. |
| Amazon CloudWatch | Metrics, logs, and audit logging for monitoring Neptune cluster performance and compliance. |
| AWS KMS | Encryption at rest using AWS Key Management Service for customer-managed key support. |
| Amazon VPC | Network isolation using Amazon Virtual Private Cloud with security group and firewall controls. |
| Apache TinkerPop | Gremlin graph traversal language and TinkerPop ecosystem integration for property graph querying. |
| Strands AI Agents SDK | Integration with Strands AI Agents SDK and popular agentic memory tools for AI agent applications. |

## Artifacts

Machine-readable API specifications organized by format.

### OpenAPI

- [Analytics](openapi/amazon-neptune-analytics-openapi.yml)
- [Data](openapi/amazon-neptune-data-openapi.yml)
- [Gremlin](openapi/amazon-neptune-gremlin-openapi.yml)
- [Loader](openapi/amazon-neptune-loader-openapi.yml)
- [Management](openapi/amazon-neptune-management-openapi.yml)
- [Ml](openapi/amazon-neptune-ml-openapi.yml)
- [Opencypher](openapi/amazon-neptune-opencypher-openapi.yml)
- [Sparql](openapi/amazon-neptune-sparql-openapi.yml)
- [Streams](openapi/amazon-neptune-streams-openapi.yml)

### JSON Schema

- [Amazon Neptune Analytics Graph](json-schema/amazon-neptune-analytics-graph-schema.json)
- [Amazon Neptune Db Cluster](json-schema/amazon-neptune-db-cluster-schema.json)
- [Amazon Neptune Db Instance](json-schema/amazon-neptune-db-instance-schema.json)
- [Amazon Neptune Graph Element](json-schema/amazon-neptune-graph-element-schema.json)
- [Amazon Neptune Loader Job](json-schema/amazon-neptune-loader-job-schema.json)
- [Amazon Neptune Ml Job](json-schema/amazon-neptune-ml-job-schema.json)
- [Amazon Neptune Stream Record](json-schema/amazon-neptune-stream-record-schema.json)
- [Analytics Create Graph Input](json-schema/analytics-create-graph-input-schema.json)
- [Analytics Create Graph Snapshot Input](json-schema/analytics-create-graph-snapshot-input-schema.json)
- [Analytics Create Graph Using Import Task Input](json-schema/analytics-create-graph-using-import-task-input-schema.json)
- [Analytics Create Private Graph Endpoint Input](json-schema/analytics-create-private-graph-endpoint-input-schema.json)
- [Analytics Graph Output](json-schema/analytics-graph-output-schema.json)
- [Analytics Graph Snapshot Output](json-schema/analytics-graph-snapshot-output-schema.json)
- [Analytics Import Task Output](json-schema/analytics-import-task-output-schema.json)
- [Analytics List Graph Snapshots Output](json-schema/analytics-list-graph-snapshots-output-schema.json)
- [Analytics List Graphs Output](json-schema/analytics-list-graphs-output-schema.json)
- [Analytics List Import Tasks Output](json-schema/analytics-list-import-tasks-output-schema.json)
- [Analytics Private Graph Endpoint Output](json-schema/analytics-private-graph-endpoint-output-schema.json)
- [Analytics Restore Graph From Snapshot Input](json-schema/analytics-restore-graph-from-snapshot-input-schema.json)
- [Analytics Update Graph Input](json-schema/analytics-update-graph-input-schema.json)
- ... and 79 more

### JSON Structure

- 99 JSON Structure files derived from JSON Schema

### JSON-LD

- [Analytics Context](json-ld/amazon-neptune-analytics-context.jsonld)
- [Context Context](json-ld/amazon-neptune-context.jsonld)
- [Data Context](json-ld/amazon-neptune-data-context.jsonld)
- [Gremlin Context](json-ld/amazon-neptune-gremlin-context.jsonld)
- [Loader Context](json-ld/amazon-neptune-loader-context.jsonld)
- [Management Context](json-ld/amazon-neptune-management-context.jsonld)
- [Ml Context](json-ld/amazon-neptune-ml-context.jsonld)
- [Opencypher Context](json-ld/amazon-neptune-opencypher-context.jsonld)
- [Sparql Context](json-ld/amazon-neptune-sparql-context.jsonld)
- [Streams Context](json-ld/amazon-neptune-streams-context.jsonld)

### Examples

- 99 example JSON files generated from JSON Schema

## Capabilities

Naftiko capabilities organized as shared per-API definitions composed into customer-facing workflows.

### Shared Per-API Definitions

- [Analytics](capabilities/shared/analytics.yaml)
- [Data](capabilities/shared/data.yaml)
- [Gremlin](capabilities/shared/gremlin.yaml)
- [Loader](capabilities/shared/loader.yaml)
- [Management](capabilities/shared/management.yaml)
- [Ml](capabilities/shared/ml.yaml)
- [Opencypher](capabilities/shared/opencypher.yaml)
- [Sparql](capabilities/shared/sparql.yaml)
- [Streams](capabilities/shared/streams.yaml)

### Workflow Capabilities

| Workflow | APIs Combined | Tools | Persona |
|----------|--------------|-------|---------|
| [Amazon Neptune Analytics and Machine Learning](capabilities/neptune-analytics-ml.yaml) | analytics, ml | 4 | Used by data scientists and ML engineers |
| [Amazon Neptune Graph Data Management](capabilities/neptune-graph-management.yaml) | management, data, loader, streams | 6 | Used by graph database administrators and developers |

## Vocabulary

- [Vocabulary](vocabulary/amazon-neptune-vocabulary.yaml)

## Rules

- [Spectral Rules](rules/amazon-neptune-spectral-rules.yml) — 25 rules enforcing Amazon Neptune API conventions

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
