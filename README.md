# Amazon Neptune (amazon-neptune)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
