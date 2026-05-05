---
title: "Build and explore Knowledge Graphs faster with Amazon Neptune using Graph.Build and G.V() – Part 1"
url: "https://aws.amazon.com/blogs/database/build-and-explore-knowledge-graphs-faster-with-amazon-neptune-using-graph-build-and-g-v-part-1/"
date: "Fri, 12 Dec 2025 19:32:50 +0000"
author: "Richard Loveday"
feed_url: "https://aws.amazon.com/blogs/database/category/database/amazon-neptune/feed/"
---
<p><em>This is a guest blog post by Richard Loveday, Head of Product at <a href="https://graph.build/" rel="noopener noreferrer" target="_blank">Graph.Build</a>, in partnership with Charles Ivie, Graph Architect at AWS.</em></p> 
<p>The Graph.Build platform is a dedicated, no-code graph model design studio and build factory, available on AWS Marketplace.</p> 
<p><a href="https://aws.amazon.com/neptune/knowledge-graphs-on-aws/" rel="noopener noreferrer" target="_blank">Knowledge graphs</a> have been widely adopted by organizations, powering use cases such as social media networks, fraud detection, digital twin, and drug discovery. The rise of large language models (LLMs) has accelerated interest, as knowledge graphs provide an ideal structured foundation for LLM interactions. This has led to their adoption as primary data repositories for organizations of all sizes.</p> 
<p>However, widespread adoption is hindered by a lack of accessible tooling and the expertise required to implement these systems. Consequently, many organizations have struggled to take advantage of what is otherwise an intuitive and powerful approach to data modeling.</p> 
<p>In this series of posts we demonstrate how to build and manage a complete knowledge graph solution from start to finish without writing a single line of code, integrating <a href="https://aws.amazon.com/neptune/" rel="noopener noreferrer" target="_blank">Amazon Neptune</a> with the following <a href="https://aws.amazon.com/marketplace" rel="noopener noreferrer" target="_blank">AWS Marketplace</a> tooling:</p> 
<ul> 
 <li><a href="https://aws.amazon.com/marketplace/seller-profile?id=778d246b-80cd-4728-9fbf-31cc3e1cc182" rel="noopener noreferrer" target="_blank">Graph.Build</a></li> 
 <li><a href="https://aws.amazon.com/marketplace/pp/prodview-lifzpx4adcwsq?sr=0-1&amp;ref_=beagle&amp;applicationId=AWSMPContessa" rel="noopener noreferrer" target="_blank">G.V()</a></li> 
</ul> 
<p>The lifecycle of a knowledge graph solution is a continuous loop of four distinct phases:</p> 
<ol> 
 <li><strong>Schema Design:</strong> Establish the foundational blueprint (the schema or ontology) that defines the types of entities and relationships.</li> 
 <li><strong>Data Ingestion and Modeling:</strong> Ingest and map disparate data sources to the ontology, building the graph model.</li> 
 <li><strong>Persistence:</strong> Load the resulting graph model into a native graph database for efficient storage and retrieval.</li> 
 <li><strong>Exploration and Discovery:</strong> Utilize the graph by querying and analyzing its connections to discover valuable facts and insights.</li> 
</ol> 
<p>This series is split into two parts, each focusing on a specific tool to guide you through this lifecycle:</p> 
<h4>Part 1 (this post): Design, ingestion, modelling and persistence</h4> 
<p>We use <a href="https://graph.build/" rel="noopener noreferrer" target="_blank">Graph.Build</a> to visually design our ontology, connect to existing data sources like SQL and JSON to build our graph model, and persist the model directly into Amazon Neptune.</p> 
<h4><a href="https://aws.amazon.com/blogs/database/build-and-explore-knowledge-graphs-faster-with-amazon-neptune-using-graph-build-and-g-v-part-2/" rel="noopener" target="_blank">Part 2: Exploration and discovery</a></h4> 
<p>We will then use <a href="https://gdotv.com/" rel="noopener noreferrer" target="_blank">G.V()</a> to connect to our graph in Neptune, enabling no-code exploration, querying, and analysis to discover valuable insights.</p> 
<h2>Solution overview</h2> 
<p>Both Neptune and Graph.Build support <a href="https://en.wikipedia.org/wiki/Property_graph" rel="noopener noreferrer" target="_blank">Labeled Property Graph</a> (LPG) and <a href="https://en.wikipedia.org/wiki/Resource_Description_Framework" rel="noopener noreferrer" target="_blank">Resource Description Framework</a> (RDF) models. In this post, we demonstrate a common LPG use case to identify financial crimes.</p> 
<p>Graph.Build allows you to design and build graph schemas and models visually.</p> 
<p>We design and build the following small example schema and model that represents the start of such a use case. The model describes people, ownership of credit cards, and a few related properties.</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-1.jpeg" /></p> 
<p>Graph databases like Amazon Neptune enable powerful, relationship-based queries once data is structured in a well-defined ontological model. Designing these models and transforming structured or semi-structured data into the required Labeled Property Graph (LPG) format is a critical step in this process. In this first part of the post, we explore how to streamline this workflow using Graph.Build, making the process faster and more accessible—without writing any code.</p> 
<p>With Graph.Build, you can visually define a graph schema (ontology) and generate an extract, transform, and load (ETL) model that automatically transforms diverse data sources, including SQL databases, CSV files, and JSON feeds, into graph models staged and ready for ingestion into Neptune. This no-code approach alleviates the need for manual data mapping and transformation logic, making it straightforward to structure and ingest data efficiently.</p> 
<p>Graph.Build can process large <a href="https://docs.graph.build/EGeX4aTAJLlpg9Hh8kfl/semi-structured-transformer/all-configuration-options#transformer-configuration" rel="noopener noreferrer" target="_blank">CSV files by configurable batch processing</a>, and large numbers of small files by <a href="https://docs.graph.build/EGeX4aTAJLlpg9Hh8kfl/semi-structured-transformer/all-configuration-options#kafka-configuration" rel="noopener noreferrer" target="_blank">consuming a Kafka queue detailing the files</a>.</p> 
<p>After it’s created the Graph.Build Writer automates loading the new graph models into Neptune, completing the end-to-end ETL workflow. For brevity, this post provides an overview of the steps used in the Graph.Build Studio. For detailed, step-by-step instructions, refer to the<a href="https://graph.build" rel="noopener noreferrer" target="_blank"> </a><a href="https://docs.graph.build/EGeX4aTAJLlpg9Hh8kfl/" rel="noopener noreferrer" target="_blank">graph.build documentation</a>.</p> 
<p>We perform the following steps.</p> 
<ol> 
 <li>Deploy and configure Graph.Build on <a href="https://aws.amazon.com/ecs/" rel="noopener noreferrer" target="_blank">Amazon Elastic Container Service (Amazon ECS)</a> using <a href="https://aws.amazon.com/cloudformation/" rel="noopener noreferrer" target="_blank">AWS CloudFormation</a>.</li> 
 <li>Design a new property graph schema.</li> 
 <li>Design Graph.Build linked mappings conforming to the new schema: 
  <ol type="a"> 
   <li>Source and build a graph model from JSON files in <a href="https://aws.amazon.com/s3/" rel="noopener noreferrer" target="_blank">Amazon Simple Storage Service</a> (Amazon S3).</li> 
   <li>Source and build a graph model from <a href="https://aws.amazon.com/rds/" rel="noopener noreferrer" target="_blank">Amazon RDS</a>.</li> 
  </ol> </li> 
 <li>Write the linked graph models to Amazon Neptune.</li> 
</ol> 
<p>The solution is deployed as follows</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-2.png" /></p> 
<h2>Prerequisites</h2> 
<p>In this post we show you how to map data from existing data sources to a newly designed graph schema and then build a new Graph model for Amazon Neptune. Although Graph.Build removes the need for code in this process, a basic understanding of Graph databases, SQL, JSON and AWS is required, as well as the following</p> 
<h3>A running Amazon Neptune database cluster.</h3> 
<p>Follow the guide on the Amazon Neptune documentation for <a href="https://docs.aws.amazon.com/neptune/latest/userguide/get-started-create-cluster.html" rel="noopener noreferrer" target="_blank">creating an Amazon Neptune cluster</a>.</p> 
<p>Visit the <a href="https://docs.aws.amazon.com/neptune/latest/userguide/intro.html" rel="noopener noreferrer" target="_blank">Amazon Neptune documentation</a> for more information about graph databases.</p> 
<h3>AWS Marketplace subscription to the required Graph.Build services.</h3> 
<p>The Graph.Build platform is available on AWS Marketplace, with a free 14-day trial. Each service only costs when it is running. Once the free 14-day trial is completed, services will incur their individual per-hour cost:</p> 
<p><em>Examples are from the N. Virginia region.</em></p> 
<table border="1px" cellpadding="10px" class="styled-table"> 
 <tbody> 
  <tr> 
   <td style="padding: 10px;"> <p>Graph Build Studio Small</p></td> 
   <td style="padding: 10px;"> <p>14-day free trial, then $0.41 per hour</p></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"> <p>Semi-Structured Transformer</p></td> 
   <td style="padding: 10px;"> <p>14-day free trial, then $2.14 per hour</p></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"> <p>SQL Transformer</p></td> 
   <td style="padding: 10px;"> <p>14-day free trial, then $2.56 per hour</p></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"> <p>Graph Writer</p></td> 
   <td style="padding: 10px;"> <p>14-day free trial, then $1.70 per hour</p></td> 
  </tr> 
 </tbody> 
</table> 
<p>To follow along, subscribe to the following Graph.Build services:</p> 
<ul> 
 <li><a href="https://aws.amazon.com/marketplace/server/procurement?productId=prod-oahejjvy32d5c" rel="noopener noreferrer" target="_blank">Graph Build Studio Small</a></li> 
 <li><a href="https://aws.amazon.com/marketplace/pp/prodview-zdikidopmnfe6" rel="noopener noreferrer" target="_blank">Semi-Structured Transformer</a></li> 
 <li><a href="https://aws.amazon.com/marketplace/pp/prodview-tkxrcakq6lkwg" rel="noopener noreferrer" target="_blank">SQL Transformer</a></li> 
 <li><a href="https://aws.amazon.com/marketplace/pp/prodview-qir47obut4yky" rel="noopener noreferrer" target="_blank">Graph Writer</a></li> 
</ul> 
<p>All pricing is in addition to the costs of the AWS infrastructure which it is running on.</p> 
<h2>Deploy and configure Graph.Build on ECS using AWS CloudFormation</h2> 
<p>Follow the guide on the Graph.Build documentation to deploy a <a href="https://docs.graph.build/EGeX4aTAJLlpg9Hh8kfl/getting-started-tutorial/installation/aws/deploy-with-cloudformation/" rel="noopener noreferrer" target="_blank">Graph.Build cluster on Amazon Elastic Container Service (ECS) using AWS CloudFormation</a>, taking care to follow the path for the <a href="https://docs.graph.build/EGeX4aTAJLlpg9Hh8kfl/getting-started-tutorial/installation/aws/deploy-with-cloudformation/aws-marketplace-template" rel="noopener noreferrer" target="_blank">AWS marketplace template</a>.</p> 
<p>Once the AWS CloudFormation template completes successfully, in the outputs tab, take note of the <code>ApplicationURL</code> and <code>StudioAdminPasswordSecret</code> value’s, as you will need them in the next step.</p> 
<h2>Design a new property graph schema</h2> 
<p>Amazon Neptune does not require, and cannot enforce a predefined schema, but schema’s are a powerful way to ensure data consistency. Graph.Build enables you to design a schema that guides the data modeling process, so that the graph written to Neptune conforms to your intended structure.In your browser, navigate to the ApplicationURL noted down from the previous step.</p> 
<p>Login to Graph.Build studio with the following credentials.</p> 
<p>Username = SuperAdmin<br /> Password = { <code>StudioAdminPassword</code> }</p> 
<p>Choose <strong>Schema / Ontology Models</strong>, <strong>New Model</strong></p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-3.png" /></p> 
<ol> 
 <li>In <strong>step 1</strong>, Choose <strong>Property Graph</strong>, skip <strong>step 2</strong> and for <strong>step 3</strong>, name your property graph schema <code>Credit Card Transactions</code></li> 
 <li>Drag in a new <strong>Node</strong> to the canvas, add choose the label <code>Person</code></li> 
 <li>Select the <code>Person</code> node, and add a property called <code>first_name</code> of type <code>String</code><br /> Repeat the process to add the properties last_name (String) and date_of_birth (Date) </li> 
 <li>Create another <strong>Node</strong> called <code>Card</code> and draw a new connection starting from <code>Person</code> and terminating at <code>Card</code>. Name the edge <code>HAS_CARD</code></li> 
 <li>Add a property called <code>since</code> with data type date to the new <code>HAS_CARD</code> edge, using the same process as adding a property to a node.</li> 
 <li>Choose <strong>Generate Schema, Publish Schema</strong></li> 
</ol> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-4.gif" /></p> 
<p>(OPTIONAL) Toggle the properties view mode button to edit properties in an expanded view.</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-5.jpeg" /></p> 
<h3>Source and build a graph model from JSON files in S3</h3> 
<p>Now that we have our schema, we can build graph models that map our schema to each source’s schema.</p> 
<p>For the first data source we choose <code>JSON</code>, in S3.</p> 
<ol> 
 <li>Choose the <code>graph.build</code> logo, then navigate to <strong>Designs, Semi Structured Models</strong>, <strong>New Model</strong>. <p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-6.jpeg" /></p></li> 
 <li>Copy the following synthetic JSON data and store it in a file called <code>sample.json</code>. 
  <div class="hide-language"> 
   <pre><code class="lang-code">[
    {
        "last_name": "Parisian",
        "passport_no": 275571108,
        "first_name": "Kelley",
        "date_of_birth": "1990-05-15",
        "Card": {
            "expiryDate": "2022-04",
            "security_code": 383,
            "card_no": 6654522284360333,
            "startDate": "2020-04"
        }
    },
    {
        "last_name": "Daugherty",
        "passport_no": 173183364,
        "first_name": "Edwin",
        "date_of_birth": "1985-11-20",
        "Card": {
            "expiryDate": "2022-08",
            "security_code": 348,
            "card_no": 8859131134896051,
            "startDate": "2020-08"
        }
    },
    {
        "last_name": "Turcotte",
        "passport_no": 321165968,
        "first_name": "Jewel",
        "date_of_birth": "2001-01-01",
        "Card": {
            "expiryDate": "2022-02",
            "security_code": 851,
            "card_no": 2155465425430095,
            "startDate": "2020-02"
        }
    },
    {
        "last_name": "Anderson",
        "passport_no": 584609961,
        "first_name": "Kandi",
        "date_of_birth": "1978-07-28",
        "Card": {
            "expiryDate": "2022-11",
            "security_code": 786,
            "card_no": 9524747695543548,
            "startDate": "2020-11"
        }
    }
]</code></pre> 
  </div> <p> The JSON sample is intended to be a sample of a larger JSON data model that you wish to transform to Graph. Once the following design has been completed, a transformation can be executed against as many JSON files as you wish, provided they have the same structure, they will behave in the same way.</p></li> 
 <li>Choose <strong>Property Graph</strong>, then <code>Credit Card Transactions</code> schema, <strong>Next Step</strong>.</li> 
 <li>Name your new model <code>PersonCard</code></li> 
 <li>Choose <strong>Upload Sample File</strong>, and choose the <code>sample.json</code> data file, then <strong>Finish Setting Up</strong><br /> <img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-7.gif" /><br /> The next screen shows all the JSON keys that are available to build the Graph model. These JSON keys are known as ‘input blocks’.</li> 
</ol> 
<p>Create your first Node / Vertex.</p> 
<ol> 
 <li>Drag the <code>CardNo</code> input block onto the canvas, choose <strong>Node</strong> and under <strong>Node Settings, Label</strong>, choose <strong>Card</strong> and <strong>Apply</strong>.</li> 
 <li>Repeat the process to create the <code>Person</code> node, using <code>PassportNo</code> as the input block, then draw a new edge between the nodes. Note that the <code>HAS_CARD</code> edge is automatically populated, as it is the only valid edge between the <code>Person</code> and <code>Card</code> nodes.</li> 
 <li>Add the properties to the model by selecting a node or edge, choosing the property key, data type and template mapping to the source JSON.</li> 
 <li>Choose <strong>Generate Model, Test Model</strong> to review the graph model.<br /> <img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-8.gif" /></li> 
 <li>Download the test result and inspect the nodes and edges files. These files are compatible with Amazon Neptune and can be loaded into Amazon Neptune using the <a href="https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load.html" rel="noopener noreferrer" target="_blank">bulk loader</a>.</li> 
 <li>Close the test result window, choose <strong>Generate Model, Publish Model</strong> to prepare the transformation job to process any file of the same format.</li> 
</ol> 
<h3>Execute the new transformation job on a file located in S3</h3> 
<p>Now you have published your transformation model, you can execute the job on any file with the same structure that resides in S3. Choose the execute button, set the <strong>Input File</strong> to:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">s3://graph-build-customer-samples/person_card_nested.json</code></pre> 
</div> 
<p>Select <code>person_card_nested.json</code> for the model reference.</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-9.jpeg" /></p> 
<p><em>Refer to <a href="https://docs.graph.build/EGeX4aTAJLlpg9Hh8kfl/semi-structured-transformer/rest-api-endpoints" rel="noopener noreferrer" target="_blank">the graph.build documentation for how to trigger transformations using REST</a></em>.</p> 
<p>Once the execution is complete, the new graph model persisted back to the outputs folder for the Semi Structured transformer in the S3 bucket created during AWS CloudFormation.</p> 
<p>Refer to the <a href="https://docs.graph.build/EGeX4aTAJLlpg9Hh8kfl/getting-started-tutorial/lpg-schemas-and-models/semi-structured-model-design/executing-a-full-transformation" rel="noopener noreferrer" target="_blank">graph.build documentation to processing large or numerous files of JSON, XML or CSV that reside in S3</a>, and automate update and <a href="https://docs.graph.build/EGeX4aTAJLlpg9Hh8kfl/graph-writer" rel="noopener noreferrer" target="_blank">insert graph model operations to Amazon Neptune using the Graph.Build Writer</a>.</p> 
<h2>Source and build a graph model from a SQL database</h2> 
<p>As well as building Graph models from files in JSON, CSV, and XML format, Graph.Build can also pull data from a SQL endpoint via a JDBC connection.</p> 
<p>Connection types include <a href="https://aws.amazon.com/athena/" rel="noopener noreferrer" target="_blank">Amazon Athena</a>, <a href="https://aws.amazon.com/rds/" rel="noopener noreferrer" target="_blank">Amazon RDS</a>, <a href="https://aws.amazon.com/rds/aurora/" rel="noopener noreferrer" target="_blank">Amazon Aurora</a>, and any other JDBC connection.</p> 
<p>Synthetic data has been created and stored in an RDS database.</p> 
<p>This database is publicly available and free to use for experimentation with Graph.Build.</p> 
<p>Choose the <code>graph.build</code> logo, then navigate to <strong>Designs, SQL Models, New Model</strong>, step thorough the setup as before, inputting the following connection details for the SQL endpoint.</p> 
<table border="1px" cellpadding="10px" class="styled-table"> 
 <tbody> 
  <tr> 
   <td style="padding: 10px;"> <p>Driver</p></td> 
   <td style="padding: 10px;"> <p>com.mysql.cj.jdbc.Driver</p></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"> <p>Endpoint</p></td> 
   <td style="padding: 10px;"> <p>card-data.crlz1hrnweup.us-east-1.rds.amazonaws.com:3306/carddata</p></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"> <p>Username</p></td> 
   <td style="padding: 10px;"> <p>readonly_user</p></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"> <p>Password</p></td> 
   <td style="padding: 10px;"> <p>readonly_graphbuild123</p></td> 
  </tr> 
 </tbody> 
</table> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-10.gif" /></p> 
<p>Once connected, insert the following query, and execute:</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql">select * from newtransactions</code></pre> 
</div> 
<p><em>Query results are automatically limited by configuration to avoid problems with large scale result sets.</em></p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-11.jpeg" /></p> 
<p>Complete a model as previously described for the JSON data source.</p> 
<p>Once complete, select the execute button (as shown in the JSON example previously) and choose <code>execute</code>.</p> 
<p>Once the transformation is complete, you will find the graph model files in the output directory for the SQL transformer in the S3 bucket created during AWS CloudFormation.</p> 
<p>Once in S3, models can be loaded into Amazon Neptune using the bulk loader, or <a href="https://docs.graph.build/EGeX4aTAJLlpg9Hh8kfl/getting-started-tutorial/writing-to-graph-databases" rel="noopener noreferrer" target="_blank">Kafka can be configured to automatically insert or update graph models to Amazon Neptune using the graph.build writer.</a></p> 
<p>Refer to <a href="https://docs.graph.build/EGeX4aTAJLlpg9Hh8kfl/getting-started-tutorial/lpg-schemas-and-models/sql-model-design" rel="noopener noreferrer" target="_blank">graph.build documentation for how to execute the SQL transformer on a schedule and trigger builds in other ways including using the REST API and Kafka</a>.</p> 
<h2>Cleanup</h2> 
<p>Navigate to the <a href="https://console.aws.amazon.com/cloudformation/" rel="noopener noreferrer" target="_blank">AWS CloudFormation console</a>.</p> 
<p>Choose <strong>Stacks</strong>, turn off <strong>view nested</strong>, select your graph.build stack and <strong>Delete</strong>.</p> 
<h2>Conclusion</h2> 
<p>In this post, we demonstrated how to design, test, and build graph models, then load them into Amazon Neptune, with no code.</p> 
<p>Using Graph.Build on AWS greatly reduces the time and effort it takes to iterate on graph solutions, meaning more time can be spent on perfecting the solution and less on code and infrastructure.</p> 
<p>Now that you have your data loaded, you are ready to start exploring. In the <a href="https://aws.amazon.com/blogs/database/build-and-explore-knowledge-graphs-faster-with-amazon-neptune-using-graph-build-and-g-v-part-2/" rel="noopener" target="_blank">next post in this series</a>, we will show you how to connect to your Neptune cluster with G.V() to query, analyze, and discover new insights. To begin building your own knowledge graph, find <a href="https://aws.amazon.com/marketplace/seller-profile?id=778d246b-80cd-4728-9fbf-31cc3e1cc182" rel="noopener noreferrer" target="_blank">Graph.Build on the AWS Marketplace</a>.</p> 
<hr /> 
<h3>About the Authors</h3> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="Richard Loveday" class="aligncenter size-full wp-image-29797" height="160" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-a1.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Richard Loveday</h3> 
  <p><a href="https://www.linkedin.com/in/richard-loveday-29a399132/" rel="noopener" target="_blank">Richard</a> is head of product at graph.build. He has been helping customers implement Linked data solutions for over a decade.</p>
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="Charles Ivie" class="aligncenter size-full wp-image-29797" height="160" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-26.jpeg" width="120" />
  </div> 
  <h3 class="lb-h4">Charles Ivie</h3> 
  <p><a href="https://www.linkedin.com/in/charlesivie/" rel="noopener" target="_blank">Charles</a> is a Senior Graph Architect with the Amazon Neptune team at AWS. As a highly respected expert within the knowledge graph community, he has been designing, leading, and implementing graph solutions for over 15 years.</p>
 </div> 
</footer>
