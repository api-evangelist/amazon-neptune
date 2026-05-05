---
title: "Build and explore Knowledge Graphs faster with Amazon Neptune using Graph.Build and G.V() – Part 2"
url: "https://aws.amazon.com/blogs/database/build-and-explore-knowledge-graphs-faster-with-amazon-neptune-using-graph-build-and-g-v-part-2/"
date: "Fri, 12 Dec 2025 19:33:03 +0000"
author: "Arthur Bigeard"
feed_url: "https://aws.amazon.com/blogs/database/category/database/amazon-neptune/feed/"
---
<p><em>This is a guest blog by Arthur Bigeard, Founder at </em><a href="https://gdotv.com/" rel="noopener noreferrer" target="_blank">gdotv</a><em>, in partnership with Charles Ivie, Sr Graph Architect at AWS.</em></p> 
<p><em>G.V() is a graph database IDE available for Desktop or on AWS Marketplace, offering extensive graph visualization and querying capabilities for Amazon Neptune and Neptune Analytics.</em></p> 
<p>In <a href="https://aws.amazon.com/blogs/database/build-and-explore-knowledge-graphs-faster-with-amazon-neptune-using-graph-build-and-g-v-part-1/" rel="noopener" target="_blank">Part 1</a> of this series, we demonstrated how to design, build and load a Labeled Property Graph (LPG) model into <a href="https://aws.amazon.com/neptune/" rel="noopener noreferrer" target="_blank">Amazon Neptune</a> using <a href="https://graph.build/" rel="noopener noreferrer" target="_blank">Graph.Build</a>.</p> 
<p>Powerful exploration of an Amazon Neptune graph is essential for finding insights.</p> 
<p>In this post, we show you how to connect <a href="https://gdotv.com" rel="noopener noreferrer" target="_blank">G.V()</a> to our Neptune cluster, enabling powerful no-code exploration, querying, and analysis to discover valuable insights on the ingested graph data.</p> 
<p>This post is intended for anyone looking to become familiar with graph data. Prior knowledge of <a href="https://opencypher.org/" rel="noopener noreferrer" target="_blank">openCypher</a> or <a href="https://tinkerpop.apache.org/gremlin.html" rel="noopener noreferrer" target="_blank">Gremlin</a> is not required, and sample queries are provided with explanations.</p> 
<h2>Solution overview</h2> 
<p>Amazon Neptune clusters are always deployed within a <a href="https://aws.amazon.com/vpc/" rel="noopener noreferrer" target="_blank">Virtual Private Cloud</a> (VPC) for network isolation. For connectivity, either restrict access to within the VPC or enable a <a href="https://docs.aws.amazon.com/neptune/latest/userguide/neptune-public-endpoints.html" rel="noopener noreferrer" target="_blank">Neptune Public Endpoint</a> for access over the Internet. When a public endpoint is used, enabling IAM database authentication is mandatory for security.</p> 
<p>The following architecture shows a Neptune cluster configured for private access within its VPC.</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-12.png" /></p> 
<p>A Neptune cluster is a collection of instances, with the minimum number being 1. Instances can be serverless for on-demand vertical automatic scaling, or distinct instance types. The primary instance acts as the single writer instance, and horizontal scalability is available for read operations by creating additional read replica instances.</p> 
<p>G.V() is deployed to an <a href="http://aws.amazon.com/ec2" rel="noopener noreferrer" target="_blank">Amazon Elastic Compute Cloud</a> (Amazon EC2) instance in the same VPC as Neptune. Security groups are used to configure network communication permissions between components in the VPC, as well as inbound to the VPC from outside AWS.</p> 
<p>G.V() acts as a client application and connects to Neptune through its cluster endpoint. G.V() starts a web server that accepts incoming traffic on port 443, using a self-signed TLS certificate.&nbsp;The high-level implementation steps are as follows:</p> 
<ol> 
 <li>Deploy G.V() on Amazon EC2.</li> 
 <li>Configure G.V() to connect to Neptune.</li> 
 <li>Search and explore the data using G.V().</li> 
</ol> 
<p>Customers are responsible for the costs of running the solution. On AWS marketplace, there is a cost for both the Amazon EC2 instance, and G.V() for the specific instance type. For this post, we recommend using a t3.large EC2 instance, so the costs will be as follows.</p> 
<p><em>Examples are taken from the us-east-1 region</em></p> 
<table border="1px" cellpadding="10px" class="styled-table"> 
 <tbody> 
  <tr> 
   <td style="padding: 10px;"> <p>G.V() – EC2 t3.large</p></td> 
   <td style="padding: 10px;"> <p>14-days free trial, then $0.64 per hour</p></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"> <p>AWS – EC2 t3.large</p></td> 
   <td style="padding: 10px;"> <p>$0.0832 per hour</p></td> 
  </tr> 
 </tbody> 
</table> 
<h2>Prerequisites</h2> 
<p>A running Amazon Neptune database cluster with data already loaded is required to complete this guide.&nbsp;The first part of this series details how to create a Neptune cluster, design and build a graph model, and load it into Amazon Neptune with no code, using Graph.Build.</p> 
<h2>Deploy G.V() on Amazon EC2</h2> 
<p>To deploy G.V() on Amazon EC2, complete the following steps:</p> 
<ol> 
 <li>Register for a <a href="https://aws.amazon.com/marketplace/pp/prodview-lifzpx4adcwsq" rel="noopener noreferrer" target="_blank">free 2-week G.V() trial on AWS Marketplace</a> by choosing <strong>Try for free</strong>.</li> 
 <li>Accept the terms and conditions of the offer and automatically create an agreement, and the free trial will start. This process might take up to a couple of minutes.</li> 
 <li>Choose <strong>Continue to Configuration</strong> and choose the same AWS Region the Neptune cluster is deployed to.</li> 
 <li>Choose <strong>Continue to Launch</strong>, then under <strong>Choose Action</strong>, choose <strong>Launch through EC2</strong>, and choose <strong>Launch</strong> again.&nbsp;</li> 
</ol> 
<p>Launching through Amazon EC2 offers flexibility with configuration of security group rules and <a href="https://aws.amazon.com/iam/" rel="noopener noreferrer" target="_blank">AWS Identity and Access Management</a> (IAM) authentication.</p> 
<p>The following is a summary of deployment instructions. For a full step-by-step guide, refer to the <a href="https://gdotv.com/docs/aws-marketplace/#deploying-a-g-v-instance" rel="noopener noreferrer" target="_blank">G.V() deployment guide</a>.</p> 
<ol> 
 <li>To configure communication between G.V() and Neptune, provide the VPC that Neptune is running in.</li> 
 <li>Choose a t3.large instance size for G.V().</li> 
 <li>If using IAM authentication on the Neptune cluster, we recommend configuring authentication from G.V() via an <a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html" rel="noopener noreferrer" target="_blank">EC2 instance profile</a> following our <a href="https://gdotv.com/docs/aws-marketplace/#configuring-the-iam-role-for-the-instance" rel="noopener noreferrer" target="_blank">documentation</a>.</li> 
 <li>Define the security group permissions for the G.V() instance. Ensure access to port 443 is enabled for your IP range at minimum.</li> 
 <li>Configure at least 8 GB of storage for the EC2 instance.</li> 
 <li>Leave other Amazon EC2 configuration parameters as their default values.</li> 
 <li>Choose <strong>Launch Instance</strong>. G.V() should be deployed and ready to use within 5 minutes.</li> 
 <li>Take note of the EC2 instance’s ID and public IPv4 DNS.</li> 
 <li>Navigate to the public IPv4 DNS of the deployed EC2 instance.</li> 
</ol> 
<p>Because G.V() uses a self-signed TLS certificate by default, it needs to be explicitly trusted in your browser. To configure an alternative, trusted certificate, see <a href="https://gdotv.com/docs/aws-marketplace/#configuring-a-tls-certificate" rel="noopener noreferrer" target="_blank">Configuring a TLS certificate</a>.</p> 
<p>To authenticate to G.V(), enter the following credentials:</p> 
<p>For <strong>Username</strong>, gdotv.<br /> For <strong>Password</strong>, enter the EC2 instance ID.</p> 
<p>G.V() is now ready to use with Neptune.</p> 
<h2>Configure G.V() to connect to Neptune</h2> 
<p>You can connect to Neptune by following these three steps:</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-13.png" /></p> 
<p><strong>Step 1 – Add connection details</strong></p> 
<p>Choose <em>New Database Connection</em>, Amazon Neptune and enter the Neptune cluster hostname, it looks like the following:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">{cluster-name}.cluster-{unique-identifier}.{region}.neptune.amazonaws.com.</code></pre> 
</div> 
<p><em>Test Connection</em></p> 
<p><strong>Step 2 – (If applicable) configure IAM authentication</strong></p> 
<p>If an EC2 instance profile is configured, it will be used automatically – otherwise enter AWS IAM credentials manually and <em>test the connection</em>.</p> 
<p>If this step fails, this indicates a misconfiguration. Refer to the <a href="https://gdotv.com/docs/aws-marketplace/#configuring-the-iam-role-for-the-instance" rel="noopener noreferrer" target="_blank">G.V documentation for EC2 instance and IAM configuration</a>.</p> 
<p><strong>Step 3 – Configure connection</strong></p> 
<p>&gt;For <em>Default Querying Language</em>, choose Gremlin. Configure other elements, like the connection name and connection color if desired, then <strong>Submit</strong>.</p> 
<p>You can now query the Neptune cluster on G.V() to visualize and explore the graph data created during <a href="https://aws.amazon.com/blogs/database/build-and-explore-knowledge-graphs-faster-with-amazon-neptune-using-graph-build-and-g-v-part-1/" rel="noopener" target="_blank">Part 1</a>.</p> 
<h2>First checks and quality control within G.V()</h2> 
<p>Run the following query.</p> 
<div class="hide-language"> 
 <pre><code class="lang-python">g.V().limit(1000)</code></pre> 
</div> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-14.png" /></p> 
<p>Explore the different visual components showing a sample of the graph data ingested during <a href="https://aws.amazon.com/blogs/database/build-and-explore-knowledge-graphs-faster-with-amazon-neptune-using-graph-build-and-g-v-part-1/" rel="noopener" target="_blank">Part 1</a>.</p> 
<p>There are three main exploration views, each of which can be opened from the<em> Connection Manager</em>, highlighted above.</p> 
<ul> 
 <li><strong>Query Editors (previous image)</strong> – Write and execute a Gremlin, Cypher, or SPARQL queries.</li> 
 <li><strong>Graph Data Explorers</strong> – Explore data using path definitions and filters, without the need to write a query.</li> 
 <li><strong>Graph Data Model views</strong> – Graph data schema as an entity-relationship diagram.</li> 
</ul> 
<p>The graph is opened in the <em>Query Editor</em> view by default and is suited for traditional query-based checks in Gremlin, Cypher, or SPARQL for RDF graphs.</p> 
<p>For broader validation, the <em>Data Model</em> provides a general overview of the graph schema, while the <em>Data Explorer</em> returns visual results and a method for navigation without the need to manually write code.</p> 
<p>Use the Data Model and Data Explorer to answer questions like “How are <strong>Card</strong> nodes linked to with <strong>Location</strong> nodes?” or “Which <strong>Card </strong>nodes have links to this particular <strong>Location</strong> node?” respectively.</p> 
<p>For a reference on how to use the <em>Graph Data Explorer</em> view or the <em>Data Model</em> view, see the following cheat sheet:</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-15.png" /></p> 
<p>You can organise multiple instances or combinations of these views into tabs.Choose View Graph Data Model.</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-16.png" /></p> 
<p>Inspect the schema that has been inferred from the graph by G.V().</p> 
<p>We can see various nodes including <strong>Person</strong>, <strong>Card</strong> and <strong>Transaction</strong> nodes, edges and properties.</p> 
<p>This gives an overview of the data structure and validates that the ingested data structure matches the schema defined in <a href="https://aws.amazon.com/blogs/database/build-and-explore-knowledge-graphs-faster-with-amazon-neptune-using-graph-build-and-g-v-part-1/" rel="noopener" target="_blank">Part 1</a>.&nbsp;Because the Data Model is generated directly from the graph, it provides a strong validation check that the graph schema is behaving as intended.</p> 
<h2>Uncovering insights from the dataset</h2> 
<p>You can query Neptune property graphs using Gremlin and Cypher. We use Gremlin in the following examples.</p> 
<p>When running a query, various data output formats organized into tabs are available to explore the query results. Choose Graph View</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-17.png" /></p> 
<p>Execute the following query, to search for a user person with the name ‘Eve Homenick’ and return their linked cards and transactions.</p> 
<div class="hide-language"> 
 <pre><code class="lang-python">g.V().
&nbsp;&nbsp;hasLabel('Person').
&nbsp;&nbsp;has('firstName', 'Eve').
&nbsp;&nbsp;has('lastName', 'Homenick').
&nbsp;&nbsp;outE("hasCard").inV().
&nbsp;&nbsp;inE("usingCard").outV().path().toList()</code></pre> 
</div> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-18.png" /></p> 
<p>Choose the <strong>Card</strong> node to explore the associated neighbours in more detail. In this case, listing the <em>In Vertices</em> is all we need to produce an itemized list of associated transactions.</p> 
<p>This could be useful if this user is under investigation, as either a victim, or perpetrator, of fraudulent transactions.</p> 
<p>When looking for fraud, investigators may need to run the same query repeatedly, passing in values such as they Person’s name as parameters.</p> 
<p>Assume the investigator wanted to look at a different individual. Set parameters as variables for <code>firstName</code> and <code>lastName</code>.&nbsp;</p> 
<div class="hide-language"> 
 <pre><code class="lang-python">g.V().
hasLabel('Person').
has('firstName', firstName).
has('lastName', lastName).
outE("hasCard").inV().
inE("usingCard").outV().path().toList()</code></pre> 
</div> 
<p>G.V()’s query assistant will automatically detect them and prompt for the parameter values:</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-19.png" /></p> 
<p>Save the query for later access:</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-20.png" /></p> 
<p>When saving a query with custom parameters like this, they will be prompted back to the user prior to running the query, allowing the ability to emulate stored procedures.</p> 
<p>This eliminates the need to rewrite the query, leave it open, or save elsewhere. Write it once, including any necessary parameter directly in the query, and G.V() will auto detect these. This allows building a library of reports against the Neptune cluster that can be accessed and run.</p> 
<h2>Looking for suspicious behavior</h2> 
<p>This synthetic dataset demonstrates fraudulent credit card transaction events.</p> 
<p>Multiple transactions being made from widespread geographical locations over a short period of time could indicate fraudulent activity.</p> 
<p>Execute the following query to:</p> 
<ol> 
 <li>Find Person vertices, and find their card using the <code>hasCard</code> relationship</li> 
 <li>Filter out the cards to retain those containing transactions that have been performed from at least five different locations</li> 
 <li>For the filtered cards, retrieve the path from the card to the location through the transaction that was performed</li> 
</ol> 
<div class="hide-language"> 
 <pre><code class="lang-python">g.V().
&nbsp;hasLabel("Person").
&nbsp;outE("hasCard").
&nbsp;otherV().
&nbsp;where(
&nbsp;&nbsp;&nbsp;inE("usingCard").
&nbsp;&nbsp;&nbsp;otherV().
&nbsp;&nbsp;&nbsp;outE("hasLocation").
&nbsp;&nbsp;&nbsp;otherV().
&nbsp;&nbsp;&nbsp;dedup().
&nbsp;&nbsp;&nbsp;count().
&nbsp;&nbsp;&nbsp;is(gt(5))).
&nbsp;inE("usingCard").
&nbsp;otherV().
&nbsp;outE("hasLocation").
&nbsp;otherV().
&nbsp;path()</code></pre> 
</div> 
<p>Choose <strong>Graph View</strong></p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-21.png" /></p> 
<p>Analysis shows that multiple transactions have occurred in five different countries within just a few hours.</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-22.png" /></p> 
<p>Improve the <code>WHERE</code> condition to target transactions occurring within a short time span across multiple locations.</p> 
<p>Deciding how many locations are sufficient to qualify a transaction history as ‘suspicious’ is subjective, so having the number of locations as an adjustable parameter is useful. You can use the Query Assistant to create and manually adjust parameters. </p> 
<p>Here we look at any cards that have transactions linked to more than a single location:</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-23.png" /></p> 
<p>Using parameters, reports can be adjusted to the thresholds and context such as investigating a specific user or transaction.</p> 
<h2>Cleanup</h2> 
<p>Navigate to the <a href="https://console.aws.amazon.com/ec2/" rel="noopener noreferrer" target="_blank">EC2 section on the AWS console</a>, choose instances, select the G.V() instance’s checkbox, then choose <strong>instance state</strong>, <strong>terminate</strong>.</p> 
<p><img src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-24.jpeg" /></p> 
<p>If you no longer need the Amazon Neptune cluster created in this post, review <a href="https://docs.aws.amazon.com/neptune/latest/userguide/manage-console-instances.html" rel="noopener noreferrer" target="_blank">the Amazon Neptune documentation</a> for how to delete the cluster.</p> 
<h2>Conclusion</h2> 
<p>In this post, we demonstrated the basics of deploying G.V() and configuring it to connect to a Neptune cluster.</p> 
<p>G.V() offers a wide range of tools to explore graph data, design complex graph queries, and create configurable reports. This makes querying, exploring and visualizing graph data significantly easier, without the need to build complex, bespoke solutions.</p> 
<p>Our <a href="https://gdotv.com/docs" rel="noopener noreferrer" target="_blank">documentation</a> offers a comprehensive overview of our features and compatibility with other graph databases and engines, such as <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/what-is-neptune-analytics.html" rel="noopener noreferrer" target="_blank">Neptune Analytics</a> and other partners, such as Neo4J.</p> 
<hr /> 
<h3>About the Authors</h3> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="Arthur Bigeard" class="aligncenter size-full wp-image-29797" height="160" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-25.jpeg" width="120" />
  </div> 
  <h3 class="lb-h4">Arthur Bigeard</h3> 
  <p><a href="https://www.linkedin.com/in/arthur-bigeard/" rel="noopener" target="_blank">Arthur</a> is the CEO at gdotv. His mission is to support the adoption of graph technology with intuitive tooling that simplifies day-to-day tasks on graph databases.</p>
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="Charles Ivie" class="aligncenter size-full wp-image-29797" height="160" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/12/11/DBBLOG-4785-26.jpeg" width="120" />
  </div> 
  <h3 class="lb-h4">Charles Ivie</h3> 
  <p><a href="https://www.linkedin.com/in/charlesivie/" rel="noopener" target="_blank">Charles</a> is a Senior Graph Architect with the Amazon Neptune team at AWS. As a highly respected expert within the knowledge graph community, he has been designing, leading, and implementing graph solutions for over 15 years.</p>
 </div> 
</footer>
