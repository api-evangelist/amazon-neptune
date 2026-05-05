---
title: "Build graph applications faster with Amazon Neptune public endpoints"
url: "https://aws.amazon.com/blogs/database/build-graph-applications-faster-with-amazon-neptune-public-endpoints/"
date: "Wed, 17 Sep 2025 16:13:01 +0000"
author: "Brian O'Keefe"
feed_url: "https://aws.amazon.com/blogs/database/category/database/amazon-neptune/feed/"
---
<p>Developing applications on Amazon Neptune Database historically required users setup access into the VPC where it is hosted and use either 3rd party drivers or direct HTTP requests. In this post, we discuss how two key features, public endpoints and the <a href="https://docs.aws.amazon.com/neptune/latest/userguide/data-api.html" rel="noopener noreferrer" target="_blank">Neptune Data API</a>, solve these common challenges in <a href="https://aws.amazon.com/neptune/" rel="noopener noreferrer" target="_blank">Amazon Neptune</a> application development. Public endpoints are available starting with engine version 1.4.6.0. Prior to version 1.4.6.0, Neptune was restricted to only being accessible from inside a virtual private cloud (VPC), complicating access to it. This new feature complements existing developer-friendly capabilities in Neptune, including support for Neptune Data API by the <a href="https://aws.amazon.com/what-is/sdk/" rel="noopener noreferrer" target="_blank">AWS SDK</a> (<code>neptunedata</code>) used to manage data within a Neptune cluster, simplifying the process to build and test graph applications.</p> 
<p>Neptune is a fast, reliable, and fully managed graph database service for building and running applications with highly connected datasets, such as knowledge graphs, fraud graphs, identity graphs, and security graphs. Neptune provides developers the most choice for building graph applications with three open graph query languages: <a href="https://github.com/opencypher/openCypher" rel="noopener noreferrer" target="_blank">openCypher</a>, <a href="http://tinkerpop.apache.org/" rel="noopener noreferrer" target="_blank">Apache TinkerPop Gremlin</a>, and the World Wide Web Consortium’s (W3C) <a href="https://www.w3.org/TR/sparql11-query/" rel="noopener noreferrer" target="_blank">SPARQL 1.1</a>.</p> 
<p>With the release of Neptune 1.4.6.0, you can now create Neptune clusters with public endpoints, removing a significant barrier to development and testing, and it integrates with the AWS SDK to provide a streamlined development experience. In this post, we show you how you can use public endpoints and the AWS SDK to access the Neptune Data API to simplify and accelerate development on Neptune.</p> 
<h2>Public endpoints</h2> 
<p>At Amazon Web Services (AWS), we frequently say security is job zero, meaning that it’s even more important than any number one priority. When Neptune was released, clusters could only be accessed from within a VPC for security. While this protected your data, it created development challenges. Developers needed <a href="https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html" rel="noopener noreferrer" target="_blank">AWS Site-to-Site VPN</a>, <a href="https://docs.aws.amazon.com/directconnect/latest/UserGuide/" rel="noopener noreferrer" target="_blank">AWS Direct Connect</a>, or <a href="https://aws.amazon.com/transit-gateway/" rel="noopener noreferrer" target="_blank">AWS Transit Gateway</a> to connect from their local machines. Teams wanting internet-accessible clusters (like linked data repositories) had to manage additional infrastructure like <a href="https://aws-samples.github.io/aws-dbs-refarch-graph/src/connecting-using-a-load-balancer/" rel="noopener noreferrer" target="_blank">load balancers and proxy servers</a>.</p> 
<p>Neptune 1.4.6.0 addresses this with public endpoints. When you enable <a href="https://aws.amazon.com/iam/" rel="noopener noreferrer" target="_blank">AWS Identity and Access Management</a> (IAM) authentication on your cluster, you can also enable public endpoints on instances, eliminating the need for these extra networking steps while maintaining security through IAM controls. New Neptune databases are created with public endpoints disabled, and you can use <a href="https://docs.aws.amazon.com/neptune/latest/userguide/neptune-public-endpoints.html#neptune-public-endpoints-restrict-access" rel="noopener noreferrer" target="_blank">IAM policies to control who can create or modify clusters with public access</a>.</p> 
<h2>Creating a cluster with public accessibility using the Neptune console</h2> 
<p>In version 1.4.6.0, we simplify developer connectivity by introducing Neptune public endpoints. If you have at least one public subnet in your Neptune DB subnet group, you can create Neptune instances in the public subnet. Remember that one public subnet is the minimum. In production settings, we recommend at least 2 public subnets to allow for high availability. Comprehensive prerequisites for creating a Neptune cluster can be found in the <a href="https://docs.aws.amazon.com/neptune/latest/userguide/neptune-setup.html" rel="noopener noreferrer" target="_blank">documentation</a>. In this section we highlight what has changed specifically to enable public accessibility.</p> 
<p>If you are using the Neptune console, a new option has been added in the <strong>Network and Security</strong> section when creating or modifying a cluster to select if you want the cluster to be publicly accessible (see the following screenshot). The console allows you to specify this at the cluster level for simplicity, but it is configured at the instance level and you can control which instances are publicly accessible or not using the AWS CLI or SDK as we show later.</p> 
<p><img alt="AWS Neptune configuration interface showing public accessibility toggle with VPC security and IAM authentication details" class="alignnone size-full wp-image-65616" height="168" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/09/11/DBBLOG-5078-image-1.png" style="margin: 10px 0px 10px 0px;" width="1076" /></p> 
<p>IAM database authentication is required for publicly accessible clusters. On the Neptune console, the <strong>Additional Settings</strong> section contains the option <strong>Turn on IAM Authentication</strong> (see the following screenshot). Security is job zero and we want to make sure you will not accidentally expose your database to the world without any security controls.</p> 
<p><img alt="Neptune cluster IAM authentication setting with option to enable for entire DB cluster security" class="alignnone size-full wp-image-65614" height="104" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/09/11/DBBLOG-5078-image-2.png" style="margin: 10px 0px 10px 0px;" width="568" /></p> 
<p>Before fully creating a Neptune cluster to run this example, review the Neptune <a href="https://aws.amazon.com/neptune/pricing/" rel="noopener noreferrer" target="_blank">pricing</a> as billing will start upon creating your cluster.</p> 
<h2>IAM authentication</h2> 
<p>Neptune supports user- and role-level security through IAM and this is one of the key recommendations for <a href="https://docs.aws.amazon.com/neptune/latest/userguide/data-protection.html" rel="noopener noreferrer" target="_blank">protecting your data in Neptune</a>. The common workflow is:</p> 
<ol> 
 <li>create <a href="https://docs.aws.amazon.com/neptune/latest/userguide/security-iam-access-manage.html" rel="noopener noreferrer" target="_blank">IAM policies</a> reflecting Neptune job roles and the permissions from both <a href="https://docs.aws.amazon.com/neptune/latest/userguide/iam-admin-policies.html" rel="noopener noreferrer" target="_blank">administrative</a> (managing databases) and <a href="https://docs.aws.amazon.com/neptune/latest/userguide/iam-data-access-policies.html" rel="noopener noreferrer" target="_blank">data access</a> (accessing data in databases) perspectives.</li> 
 <li>assigned the policies users or applications (service accounts) to grant <a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege" rel="noopener noreferrer" target="_blank">least-privilege permission</a>, so if credentials are compromised then minimal access is granted. Neptune also supports using <a href="https://docs.aws.amazon.com/neptune/latest/userguide/iam-auth-temporary-credentials.html" rel="noopener noreferrer" target="_blank">temporary security credentials</a> to reduce risk of compromised credentials even further.</li> 
 <li>manage access to Neptune using your own third-party identity provider with <a href="https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html" rel="noopener noreferrer" target="_blank">Amazon Cognito identity pools</a>. Associate <a href="https://docs.aws.amazon.com/cognito/latest/developerguide/external-identity-providers.html" rel="noopener noreferrer" target="_blank">third-party identity providers</a> with <a href="https://docs.aws.amazon.com/cognito/latest/developerguide/iam-roles.html" rel="noopener noreferrer" target="_blank">IAM roles</a> and <a href="https://docs.aws.amazon.com/cognito/latest/developerguide/getting-credentials.html" rel="noopener noreferrer" target="_blank">get credentials from the identity pool</a> to access Neptune.</li> 
</ol> 
<p>If you use the AWS SDKs we discuss later in this post, they provide functions to use IAM without special configuration or libraries to sign requests. If you use other libraries or methods to access Neptune, we have resources to assist you in using AWS Signature Version 4 to sign your requests. To learn more, see <a href="https://docs.aws.amazon.com/neptune/latest/userguide/iam-auth-connecting.html" rel="noopener noreferrer" target="_blank">Connecting to your Amazon Neptune database using AWS Identity and Access Management authentication</a>.</p> 
<h2>Creating a public cluster with public accessibility using the AWS CLI</h2> 
<p>If your IAM role has permission to create a Neptune cluster and a VPC and security group exist configured for Neptune, create a cluster using the <a href="http://aws.amazon.com/cli" rel="noopener noreferrer" target="_blank">AWS Command Line Interface</a> (AWS CLI). Run the <code>aws neptune create-db-cluster</code> command. You can specify the configurations using options with the command. Some of the options you can configure are which AWS Region you want the cluster to be placed in and how to identify the Neptune cluster. The options used for this example can be found in the following code:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">aws neptune create-db-cluster --region us-east-1 --engine neptune --engine-version 1.4.6.0 --enable-iam-database-authentication --db-cluster-identifier my-cluster-name </code></pre> 
</div> 
<p>The preceding command creates the cluster without any compute instances. The cluster manages a group of 1 primary (writer) instance and up to 15 read replica (reader) instances, associated with a <a href="https://docs.aws.amazon.com/neptune/latest/userguide/feature-overview-storage.html" rel="noopener noreferrer" target="_blank">managed storage volume</a> shared by all instances. The following command and options create a single primary instance in your cluster:</p> 
<div class="hide-language"> 
 <pre><code class="lang-python">aws neptune create-db-instance --region us-east-1 --engine neptune --engine-version 1.4.6 --db-cluster-identifier my-cluster-name --db-instance-identifier my-cluster-name-instance1 --db-instance-class db.r8g.large --publicly-accessible</code></pre> 
</div> 
<p>In the preceding code, the last option we specify is the <code>–-publicly-accessible</code> option. This feature is new as a part of version release 1.4.6, which we identified should be set using the <code>--engine-version</code> parameter. The response from the command will show details of the cluster, including <code>“DBInstanceStatus” : “creating”</code>.</p> 
<p>Periodically check the status of the instance using the command <code>aws neptune describe-db-instances --region us-east-1 --db-instance-identifier my-cluster-name-instance1</code> for the status to change to <code>“DBInstanceStatus”: “available”</code>. When this status changes, you can now access your cluster from <a href="https://aws.amazon.com/cloudshell/" rel="noopener noreferrer" target="_blank">AWS CloudShell</a> or your local desktop. The endpoint url you will use can be obtained by calling <code>aws neptune describe-db-clusters --db-cluster-identifier my-cluster-name --query "DBClusters[0].join('',['https://',Endpoint,':',to_string(Port)])"</code> and supplying that value as the endpoint-url property.</p> 
<p>If your requests to Neptune time out, verify the following:</p> 
<ul> 
 <li>The <code>db-describe-instances</code> command shown previously contains <code>"PubliclyAccessible": true</code></li> 
 <li>Your cluster was created in public subnets containing an internet gateway available within the route tables</li> 
 <li>The security group assigned to the Neptune cluster contains a <a href="https://docs.aws.amazon.com/vpc/latest/userguide/security-group-rules.html" rel="noopener noreferrer" target="_blank">rule</a> allowing access to the port allocated to Neptune (port 8182 by default) from your source</li> 
 <li>Use <a href="https://docs.aws.amazon.com/vpc/" rel="noopener noreferrer" target="_blank">Amazon Virtual Private Cloud’s</a> <a href="https://docs.aws.amazon.com/vpc/latest/reachability/what-is-reachability-analyzer.html" rel="noopener noreferrer" target="_blank">Reachability Analyzer</a> to verify your cluster is accessible or see what component is blocking it</li> 
</ul> 
<p>If you are receiving a 401 Forbidden exception, make sure the credentials you are using to allow access to the cluster are <a href="https://docs.aws.amazon.com/neptune/latest/userguide/security-iam-access-manage.html" rel="noopener noreferrer" target="_blank">set up correctly for Neptune</a>, or if you are not using AWS tools that manage encryption for you such as the CLI or SDK, make sure the tools support <a href="https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html" rel="noopener noreferrer" target="_blank">AWS Signature Version 4</a> authentication.</p> 
<p>The following diagram shows the architecture of a Neptune cluster created with public endpoints on the instances. <em>Corporate Data Center</em> is a placeholder for a public network, including your home network or a public endpoint.</p> 
<p><img alt="AWS Neptune cluster deployment showing instances in public subnets across three availability zones" class="alignnone size-full wp-image-65615" height="725" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/09/11/DBBLOG-5078-image-3.png" style="margin: 10px 0px 10px 0px;" width="1347" /></p> 
<p>If you enable Neptune Database public accessibility, we recommend additional security and access controls to protect your database from potential threats from internet. For example, you should configure inbound security group rules to only allow trusted IP addresses. Publicly accessible or not, it is a best practice to make sure the database is regularly updated to newer engine and operating system releases to receive security updates.</p> 
<h2>Neptune Data API</h2> 
<p>The Neptune Data API is used to interact with data within your database including querying, managing bulk loading operations, and retrieving metadata about the data in your cluster. Using AWS managed clients like the CLI and SDKs further eases Neptune interactions encapsulating common functionality like signing requests, connection pooling, connection management, and auto-retry. Prior to the Neptune Data API, you had to identify and implement third-party libraries like <a href="https://tinkerpop.apache.org/download.html" rel="noopener noreferrer" target="_blank">Apache TinkerPop Gremlin clients</a>, <a href="https://github.com/RDFLib/rdflib" rel="noopener noreferrer" target="_blank">RDFLib</a>, or <a href="https://neo4j.com/docs/bolt/current/neo4j-drivers/" rel="noopener noreferrer" target="_blank">Neo4j Bolt drivers</a>. These drivers are not Neptune specific and often require <a href="https://docs.aws.amazon.com/neptune/latest/userguide/iam-auth-connecting.html" rel="noopener noreferrer" target="_blank">additional steps to implement AWS specific protocols like AWS Signature Version 4</a>. With the introduction of the Data API, you can now perform data actions with Neptune using the AWS CLI, <a href="https://aws.amazon.com/sdk-for-python/" rel="noopener noreferrer" target="_blank">AWS SDK for Python (Boto3)</a>, and other AWS SDKs for <a href="https://aws.amazon.com/sdk-for-java/" rel="noopener noreferrer" target="_blank">Java</a>, <a href="https://aws.amazon.com/sdk-for-javascript/" rel="noopener noreferrer" target="_blank">JavaScript</a>, <a href="https://aws.amazon.com/sdk-for-net/" rel="noopener noreferrer" target="_blank">.NET</a>, <a href="https://aws.amazon.com/sdk-for-ruby/" rel="noopener noreferrer" target="_blank">Ruby</a>, <a href="https://aws.amazon.com/sdk-for-rust/" rel="noopener noreferrer" target="_blank">Rust</a>, <a href="https://github.com/aws/aws-sdk-cpp" rel="noopener noreferrer" target="_blank">C++</a>, <a href="https://github.com/awslabs/aws-sdk-kotlin" rel="noopener noreferrer" target="_blank">Kotlin</a>, <a href="https://github.com/aws/aws-sdk-php" rel="noopener noreferrer" target="_blank">PHP</a>, <a href="https://github.com/awslabs/aws-sdk-swift" rel="noopener noreferrer" target="_blank">Swift</a>, and <a href="https://aws.amazon.com/sdk-for-go/" rel="noopener noreferrer" target="_blank">Go</a>.</p> 
<p>The Neptune Data API supports most of the actions you can perform using the Neptune REST API today. The following table lists the actions supported.</p> 
<table border="1px" cellpadding="10px" style="width: 800px;"> 
 <tbody> 
  <tr> 
   <td><strong>Cluster Wide Actions</strong></td> 
   <td><a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ExecuteFastReset.html" rel="noopener noreferrer" target="_blank">ExecuteFastReset</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetEngineStatus.html" rel="noopener noreferrer" target="_blank">GetEngineStatus</a></td> 
  </tr> 
  <tr> 
   <td><strong>Bulk Loading API</strong></td> 
   <td><a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_StartLoaderJob.html" rel="noopener noreferrer" target="_blank">StartLoaderJob</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetLoaderJobStatus.html" rel="noopener noreferrer" target="_blank">GetLoaderJobStatus</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ListLoaderJobs.html" rel="noopener noreferrer" target="_blank">ListLoaderJobs</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_CancelLoaderJob.html" rel="noopener noreferrer" target="_blank">CancelLoaderJob</a></td> 
  </tr> 
  <tr> 
   <td><strong>Property Graph Actions</strong></td> 
   <td><a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetPropertygraphStatistics.html" rel="noopener noreferrer" target="_blank">GetPropertygraphStatistics</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetPropertygraphStream.html" rel="noopener noreferrer" target="_blank">GetPropertygraphStream</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetPropertygraphSummary.html" rel="noopener noreferrer" target="_blank">GetPropertygraphSummary</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ManagePropertygraphStatistics.html" rel="noopener noreferrer" target="_blank">ManagePropertygraphStatistics</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_DeletePropertygraphStatistics.html" rel="noopener noreferrer" target="_blank">DeletePropertygraphStatistics</a><br /> (Gremlin Specific)<br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ExecuteGremlinQuery.html" rel="noopener noreferrer" target="_blank">ExecuteGremlinQuery</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ExecuteGremlinExplainQuery.html" rel="noopener noreferrer" target="_blank">ExecuteGremlinExplainQuery</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ExecuteGremlinProfileQuery.html" rel="noopener noreferrer" target="_blank">ExecuteGremlinProfileQuery</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetGremlinQueryStatus.html" rel="noopener noreferrer" target="_blank">GetGremlinQueryStatus</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ListGremlinQueries.html" rel="noopener noreferrer" target="_blank">ListGremlinQueries</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_CancelGremlinQuery.html" rel="noopener noreferrer" target="_blank">CancelGremlinQuery</a><br /> (openCypher Specific)<br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ExecuteOpenCypherQuery.html" rel="noopener noreferrer" target="_blank">ExecuteOpenCypherQuery</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ExecuteOpenCypherExplainQuery.html" rel="noopener noreferrer" target="_blank">ExecuteOpenCypherExplainQuery</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetOpenCypherQueryStatus.html" rel="noopener noreferrer" target="_blank">GetOpenCypherQueryStatus</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ListOpenCypherQueries.html" rel="noopener noreferrer" target="_blank">ListOpenCypherQueries</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_CancelOpenCypherQuery.html" rel="noopener noreferrer" target="_blank">CancelOpenCypherQuery</a></td> 
  </tr> 
  <tr> 
   <td><strong>RDF Actions*</strong></td> 
   <td><a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetRDFGraphSummary.html" rel="noopener noreferrer" target="_blank">GetRDFGraphSummary</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetSparqlStatistics.html" rel="noopener noreferrer" target="_blank">GetSparqlStatistics</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetSparqlStream.html" rel="noopener noreferrer" target="_blank">GetSparqlStream</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ManageSparqlStatistics.html" rel="noopener noreferrer" target="_blank">ManageSparqlStatistics</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_DeleteSparqlStatistics.html" rel="noopener noreferrer" target="_blank">DeleteSparqlStatistics</a></td> 
  </tr> 
  <tr> 
   <td><strong>Neptune ML Actions</strong></td> 
   <td>(Data Processing Jobs)<br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_StartMLDataProcessingJob.html" rel="noopener noreferrer" target="_blank">StartMLDataProcessingJob</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetMLDataProcessingJob.html" rel="noopener noreferrer" target="_blank">GetMLDataProcessingJob</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ListMLDataProcessingJobs.html" rel="noopener noreferrer" target="_blank">ListMLDataProcessingJobs</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_CancelMLDataProcessingJob.html" rel="noopener noreferrer" target="_blank">CancelMLDataProcessingJob</a><br /> (Model Transform Jobs)<br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_StartMLModelTransformJob.html" rel="noopener noreferrer" target="_blank">StartMLModelTransformJob</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetMLModelTransformJob.html" rel="noopener noreferrer" target="_blank">GetMLModelTransformJob</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ListMLModelTransformJobs.html" rel="noopener noreferrer" target="_blank">ListMLModelTransformJobs</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_CancelMLModelTransformJob.html" rel="noopener noreferrer" target="_blank">CancelMLModelTransformJob</a><br /> (Model Training Jobs)<br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_StartMLModelTrainingJob.html" rel="noopener noreferrer" target="_blank">StartMLModelTrainingJob</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetMLModelTrainingJob.html" rel="noopener noreferrer" target="_blank">GetMLModelTrainingJob</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ListMLModelTrainingJobs.html" rel="noopener noreferrer" target="_blank">ListMLModelTrainingJobs</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_CancelMLModelTrainingJob.html" rel="noopener noreferrer" target="_blank">CancelMLModelTrainingJob</a><br /> (Model Endpoint Jobs)<br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_CreateMLEndpoint.html" rel="noopener noreferrer" style="font-family: inherit; font-size: inherit;" target="_blank">CreateMLEndpoint</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_GetMLEndpoint.html" rel="noopener noreferrer" style="font-family: inherit; font-size: inherit;" target="_blank">GetMLEndpoint</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_ListMLEndpoints.html" rel="noopener noreferrer" style="font-family: inherit; font-size: inherit;" target="_blank">ListMLEndpoints</a><br /> <a href="https://docs.aws.amazon.com/neptune/latest/data-api/API_DeleteMLEndpoint.html" rel="noopener noreferrer" style="font-family: inherit; font-size: inherit;" target="_blank">DeleteMLEndpoint</a></td> 
  </tr> 
 </tbody> 
</table> 
<p>The following are examples of common actions you can do with the neptunedata service within the AWS SDKs for Go, <a href="https://docs.aws.amazon.com/code-library/latest/ug/python_3_neptune_code_examples.html" rel="noopener noreferrer" target="_blank">Python</a>, and Javascript. Each SDK example includes some best practices for using the Neptune Data API, including disabling automatic retrying because some functions aren’t idempotent, and making sure the client timeout is at least as long as the instance’s <a href="https://docs.aws.amazon.com/neptune/latest/userguide/parameters.html#parameters-instance-parameters-neptune_query_timeout" rel="noopener noreferrer" target="_blank">neptune_query_timeout</a> parameter to support long-running queries.</p> 
<p>Neptune Data API calls must have the <code>--endpoint-url</code> parameter. The endpoint URL is the endpoint name of the cluster and can be found by running the following command:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">aws neptune describe-db-clusters --db-cluster-identifier my-cluster-name --query "DBClusters[0].join('',['https://',Endpoint,':',to_string(Port)])"</code></pre> 
</div> 
<p>This will produce your endpoint URL, something like <code>https://my-cluster-name.cluster-abcdefgh1234.region.neptune.amazonaws.com:8182</code>.</p> 
<p>The following example illustrates running a query using both property graph languages, Gremlin and openCypher. You must have the following permissions on your IAM role for your cluster to complete this example: <a href="https://docs.aws.amazon.com/neptune/latest/userguide/iam-dp-actions.html#readdataviaquery" rel="noopener noreferrer" target="_blank">neptune-db:ReadDataViaQuery</a> and <a href="https://docs.aws.amazon.com/neptune/latest/userguide/iam-dp-actions.html#writedataviaquery" rel="noopener noreferrer" target="_blank">neptune-db:WriteDataViaQuery</a>.</p> 
<p>First we can examine our Neptune cluster using the AWS CLI to verify it contains no data using the following Gremlin query:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">aws neptunedata execute-gremlin-query --gremlin-query "g.V().count()" --endpoint-url https://my-cluster-name.cluster-abcdefgh1234.region.neptune.amazonaws.com:8182</code></pre> 
</div> 
<p>The output of this call will confirm that there are no records in the cluster.</p> 
<div class="hide-language"> 
 <pre><code class="lang-json">{
    "requestId": "abc12345-678d-9012-e3f4-56789fghabcd",
    "status": {
        "message": "",
        "code": <strong>200</strong>,
        "attributes": {
            "@type": "g:Map",
            "@value": []
        }
    },
    "result": {
        "data": {
            "@type": "g:List",
            "@value": [
                {
                    "@type": "g:Int64",
                    "@value": <strong>0</strong>
                }
            ]
        },
        "meta": {
            "@type": "g:Map",
            "@value": []
        }
    }
}</code></pre> 
</div> 
<p>Now we insert a new record in our cluster using the Gremlin language and the AWS SDK for Go. The action required for this is the ExecuteGremlinQuery action. With the ExecuteGremlinQuery action, we can query the database using the AWS SDK instead of having to craft the HTTP REST query directly or install one of the TinkerPop Gremlin libraries. The following code example, using AWS SDK for Go, uses the ExecuteGremlinQuery command. With the Neptune Data API, you run Gremlin queries in string format similar to the <a href="https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-gremlin-rest.html" rel="noopener noreferrer" target="_blank">HTTPS REST endpoint</a>. The interface supports the Gremlin version as determined by your Neptune cluster (see the <code>gremlin.version</code> property from the GetEngineStatus action to determine what version of Gremlin your cluster supports). The code in Go is as follows:</p> 
<div class="hide-language"> 
 <pre><code class="lang-go">package main
import (
    "context"
    "fmt"
    "github.com/aws/aws-sdk-go-v2/aws"
    "github.com/aws/aws-sdk-go-v2/config"
    "github.com/aws/aws-sdk-go-v2/service/neptunedata"
    "os"
    "encoding/json"
    "net/http"
)

func main() {
    <strong>region := "my-region"</strong>
    <strong>clusterEndpoint := "my-cluster-name.cluster-abcdefgh1234." + region + ".neptune.amazonaws.com"</strong>
    <strong>neptunePort := "neptune-port"</strong>
    // Here we set an unlimited client timeout, but 
    // you can also use the value of your instance timeout from the Neptune configuration
    client := &amp;http.Client{
        <strong>Timeout: 0</strong>,
    }
    sdkConfig, _ := config.LoadDefaultConfig(context.TODO(), config.WithRegion(region), config.WithHTTPClient(client))
    svc := neptunedata.NewFromConfig(sdkConfig, func(o *neptunedata.Options) {
        <strong>o.BaseEndpoint = aws.String("https://" + clusterEndpoint + ":" + neptunePort)</strong>
        <strong>o.Retryer = aws.NopRetryer{}</strong>    // Do not retry calls if they fail
    })
    <strong>query := "g.addV('person').property('name','justin').property(id,'justin-1')"</strong>
    serializer := "application/vnd.gremlin-v1.0+json;types=false"
    input := neptunedata.ExecuteGremlinQueryInput{GremlinQuery: &amp;query, Serializer: &amp;serializer}
    <strong>result, err1 := svc.ExecuteGremlinQuery(context.TODO(), &amp;input)</strong>
    if (err1 != nil) {
        fmt.Printf("Error retrieving result, %v", err1.Error())
        os.Exit(1)
    }
    var kv map[string]interface{}
    <strong>err2 := result.Result.UnmarshalSmithyDocument(&amp;kv)</strong>
    if err2 != nil {
        fmt.Printf("Error retrieving result, %v", err2.Error())
        os.Exit(1)
    }
    <strong>enc, _ := json.Marshal(kv)</strong>
    <strong>fmt.Println(string(enc))</strong>
    os.Exit(0)
}</code></pre> 
</div> 
<p>The output of this call is the Gremlin query result serialized using the serializer specified in serializer variable (<code>application/vnd.gremlin-v1.0+json;types=false</code> for simplified viewing here). The following code is a snippet of the response:</p> 
<div class="hide-language"> 
 <pre><code class="lang-json">{
	"data": [
		    {
			"id": "<strong>justin-1</strong>",
			"label": "<strong>person</strong>",
			"properties": {
				"<strong>name</strong>": [
					{
						"id": "1913446321",
						"value": "<strong>justin</strong>"
					}
				]
			},
			"type": "vertex"
		}
	],
	"meta": {}
}
</code></pre> 
</div> 
<p>This is the same output as if you had used the HTTPS REST endpoint with the same serializer header. For example, using <a href="https://github.com/okigan/awscurl" rel="noopener noreferrer" target="_blank">awscurl</a>, the equivalent REST command looks like the following code:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">awscurl -X POST -d "{\"gremlin\":\"g.addV('person').property('name','justin').property(id,'justin-1')\"}" https://my-cluster-name.cluster-abcdefgh1234.us-east-1.neptune.amazonaws.com:8182/gremlin --service neptune-db -H 'application/vnd.gremlin-v1.0+json;types=false'</code></pre> 
</div> 
<p>This is just one example; refer to the <a href="https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/neptunedata" rel="noopener noreferrer" target="_blank">neptunedata</a> service in the AWS SDK for Go v2 documentation for more details.</p> 
<p>Because property graph data can be used interchangeably between the supported languages Gremlin and openCypher, you can write an openCypher query to retrieve that same data you just entered. You must have <a href="https://docs.aws.amazon.com/neptune/latest/userguide/iam-dp-actions.html#readdataviaquery" rel="noopener noreferrer" target="_blank">neptune-db:ReadDataViaQuery</a> permissions on your IAM role for your cluster to complete this example. Using the ExecuteOpenCypherQuery command and the AWS SDK for Python (Boto3), you can run the following code:</p> 
<div class="hide-language"> 
 <pre><code class="lang-python">import boto3
from botocore.config import Config

<strong>neptune_endpoint = "https://my-cluster-name.cluster-abcdefgh1234.my-region.neptune.amazonaws.com:my-port" # Replace with your Neptune endpoint </strong>
# Do not retry calls if they fail and do not set a client timeout on the call.
my_config = Config(
    region_name = 'my-region',
    retries = {
        <strong>'max_attempts': 1</strong>
    },
    <strong>read_timeout=None</strong>
)
client = boto3.client("neptunedata", config=my_config, endpoint_url=neptune_endpoint)

<strong>query = "MATCH (n) RETURN n LIMIT 10" # Example openCypher query response = client.execute_open_cypher_query(openCypherQuery=query)</strong>

for item in response['results']:
    <strong>print(item)</strong></code></pre> 
</div> 
<p>The output of this call is as follows:</p> 
<div class="hide-language"> 
 <pre><code class="lang-json">{'n': {'~id': '<strong>justin-1</strong>', '~entityType': 'node', '~labels': ['<strong>person</strong>'], '~properties': {<strong>'name': 'justin'</strong>}}}</code></pre> 
</div> 
<p>Refer to the Boto3 documentation for the <a href="https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/neptunedata.html" rel="noopener noreferrer" target="_blank">NeptuneData</a> service for more functions and examples.</p> 
<p>Finally, we demonstrate GetPropertyGraphSummary. You must have <a href="https://docs.aws.amazon.com/neptune/latest/userguide/iam-dp-actions.html#getgraphsummary" rel="noopener noreferrer" target="_blank">neptune-db:GetGraphSummary</a> permissions on your IAM role for your cluster to complete this example. This function returns summary statistics regarding the property graph nodes in your cluster. The following code shows how you can use the AWS SDK for JavaScript V3 to call this function:</p> 
<div class="hide-language"> 
 <pre><code class="lang-js">import { NeptunedataClient, GetPropertygraphSummaryCommand } from "@aws-sdk/client-neptunedata";
import {inspect} from "util";
import {NodeHttpHandler} from "@smithy/node-http-handler";

class NeptuneDataClient {
    constructor() {
        const clientConfig = {
            <strong>endpoint: 'https://my-cluster-name.cluster-abcdefgh1234.my</strong>-region.neptune.amazonaws.com:my-port', // Replace with your endpoint
            sslEnabled: true,
            region: 'my-region', // replace with your region
            <strong>maxAttempts: 1</strong>,  // do not retry
            requestHandler: new NodeHttpHandler({
                <strong>requestTimeout: 0</strong>  // no client timeout
            })
        };

        this._client = new NeptunedataClient(clientConfig);
    }
    async getPropertygraphSummary() {
        try {
            const command = new GetPropertygraphSummaryCommand({mode: "basic"});
            return await this._client.send(command);
        } catch (error) {
            console.error("Error executing command.", error)
            throw error;
        }
    }
}
(async () =&gt; {
    const client = new NeptuneDataClient();
    <strong>const response = await client.getPropertygraphSummary(client)</strong>;
    <strong>console.log(inspect(response.payload, { depth: null}))</strong>;
})();
</code></pre> 
</div> 
<p>As shown in the following output, there is just the single node you added:</p> 
<div class="hide-language"> 
 <pre><code class="lang-json">{
  graphSummary: {
    numNodes: <strong>1</strong>,
    numEdges: 0,
    numNodeLabels: <strong>1</strong>,
    numEdgeLabels: 0,
    nodeLabels: [ '<strong>person</strong>' ],
    edgeLabels: [],
    numNodeProperties: <strong>1</strong>,
    numEdgeProperties: 0,
    nodeProperties: [ { <strong>name: 1</strong> } ],
    edgeProperties: [],
    totalNodePropertyValues: <strong>1</strong>,
    totalEdgePropertyValues: 0
  },
  lastStatisticsComputationTime: 2025-07-28T15:37:17.620Z,
  version: 'v1'
}</code></pre> 
</div> 
<p>Again, you could call the service using the same <code>--endpoint-url</code> parameter using the REST API, but using the SDK better integrates with the programming language instead of having to parse the response directly:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">aws neptunedata get-propertygraph-summary --endpoint-url https://my-cluster-name.cluster-abcdefgh1234.us-east-1.neptune.amazonaws.com:8182</code></pre> 
</div> 
<p>The response is a JSON-formatted summary of the graph statistics from the REST API:</p> 
<div class="hide-language"> 
 <pre><code class="lang-json">{
    "statusCode": <strong>200</strong>,
    "payload": {
        "version": "v1",
        "lastStatisticsComputationTime": "2025-07-28T15:37:17.620000+00:00",
        "graphSummary": {
            "numNodes": <strong>1</strong>,
            "numEdges": 0,
            "numNodeLabels": <strong>1</strong>,
            "numEdgeLabels": 0,
            "nodeLabels": [
                "person"
            ],
            "edgeLabels": [],
            "numNodeProperties": <strong>1</strong>,
            "numEdgeProperties": 0,
            "nodeProperties": [
                {
                    "<strong>name</strong>": <strong>1</strong>
                }
            ],
            "edgeProperties": [],
            "totalNodePropertyValues": <strong>1</strong>,
            "totalEdgePropertyValues": 0
        }
    }
}</code></pre> 
</div> 
<p>Refer to the <a href="https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/client/neptunedata/" rel="noopener noreferrer" target="_blank">NeptunedataClient</a> service in the AWS SDK for JavaScript v3 for an extensive list of what is supported.</p> 
<p>To review, we have shared examples of three common operations you can run with the Neptune Data API (<code>neptunedata</code>), which can be used in addition to the <a href="https://docs.aws.amazon.com/neptune/latest/userguide/api.html" rel="noopener noreferrer" target="_blank">Neptune Management API</a> (<code>neptune</code>) to programmatically interact with Neptune using your favorite SDK. Unlike the Management APIs, the Dataplane API operations do require direct access to the Neptune cluster endpoint, so you must run these from a publicly accessible cluster or from a client with access into the same VPC that Neptune is running in.</p> 
<h2>Clean up</h2> 
<p>Be sure to <a href="https://docs.aws.amazon.com/neptune/latest/userguide/manage-console-instances-delete.html" rel="noopener noreferrer" target="_blank">delete any Neptune instances and clusters</a> you created to avoid ongoing instance and storage charges. IAM users or roles you created don’t have an ongoing cost, but you might want to consider disabling or deleting them for security purposes.</p> 
<h2>Conclusion</h2> 
<p>In this post, we showed you how the latest release from Amazon Neptune simplifies building graph applications. Beyond the features listed, you can find a complete list of improvements and fixes in the <a href="https://docs.aws.amazon.com/neptune/latest/userguide/engine-releases-1.4.6.0.html" rel="noopener noreferrer" target="_blank">release notes</a>. The following are a few ways to get started with this release:</p> 
<ul> 
 <li>Create your first Neptune cluster as part of the <a href="https://aws.amazon.com/free/" rel="noopener noreferrer" target="_blank">AWS Free Tier</a></li> 
 <li><a href="https://docs.aws.amazon.com/neptune/latest/userguide/engine-releases-1.4.6.0.html#engine-releases-1.4.6.0-upgrading" rel="noopener noreferrer" target="_blank">Upgrade your existing Neptune cluster</a> to take advantage of the latest features</li> 
 <li>Run the open source <a href="https://github.com/aws/graph-notebook" rel="noopener noreferrer" target="_blank">graph-notebook</a> library on <a href="https://jupyter.org/" rel="noopener noreferrer" target="_blank">Jupyter</a> or <a href="https://jupyterlab.readthedocs.io/" rel="noopener noreferrer" target="_blank">JupyterLab</a> notebooks, which include the Data API through Boto3, and interact with Neptune</li> 
</ul> 
<p>You can also check out more code examples, including using the AWS SDKs for other languages, on <a href="https://github.com/aws-samples/amazon-neptune-samples/tree/master/neptune-docs-code-samples/neptunedata-sdk-samples" rel="noopener noreferrer" target="_blank">the GitHub repository</a>.</p> 
<hr /> 
<h3>About the authors</h3> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="Brian O’Keefe" class="aligncenter size-full wp-image-29797" height="160" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2025/09/11/briokeef.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Brian O’Keefe</h3> 
  <p><a href="https://www.linkedin.com/in/brianokeeferochester/" rel="noopener" target="_blank">Brian</a> is a Principal Specialist Solutions Architect at AWS focused on Neptune. He works with customers and partners to solve business problems using Amazon graph technologies. He has over two decades of experience in various software architecture and research roles, many of which involved graph-based applications.</p> 
 </div> 
</footer>
