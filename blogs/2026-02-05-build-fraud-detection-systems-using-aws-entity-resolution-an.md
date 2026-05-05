---
title: "Build fraud detection systems using AWS Entity Resolution and Amazon Neptune Analytics"
url: "https://aws.amazon.com/blogs/database/build-fraud-detection-systems-using-aws-entity-resolution-and-amazon-neptune-analytics/"
date: "Thu, 05 Feb 2026 19:46:27 +0000"
author: "Jessica Hung"
feed_url: "https://aws.amazon.com/blogs/database/category/database/amazon-neptune/feed/"
---
<p>Financial institutions such as banks, payment processors, and online merchants face significant challenges in detecting and preventing fraud and financial crimes. Entity resolution and graph algorithms can be combined to support fraud detection use cases such as Card Not Present (CNP) fraud detection. A CNP transaction occurs when a credit or debit card payment is processed without the physical card being presented to the merchant, typically during online, telephone, or mail-order purchases. These transactions carry higher fraud risks because merchants can’t physically verify the card or the cardholder’s identity, making them particularly vulnerable to fraudulent usage.</p> 
<p>Entity resolution services such as <a href="https://aws.amazon.com/entity-resolution/" rel="noopener noreferrer" target="_blank">AWS Entity Resolution</a> identify links between entities using shared attributes. <a href="https://aws.amazon.com/neptune/" rel="noopener noreferrer" target="_blank">Amazon Neptune Analytics</a>, a memory-optimized graph database engine for analytics, enhances CNP fraud detection by enabling graph analysis of complex relationships between customers, transactions, and fraud patterns. When entities are resolved and matched, they create connections that can be stored and queried using graph database structures. Furthermore, graph databases’ built-in support for graph algorithms including <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/clustering-algorithms.html" rel="noopener noreferrer" target="_blank">community detection</a> enables efficient exploration of entity networks, making it straightforward to discover hidden patterns and indirect connections between resolved entities. This combined approach facilitates fraud detection by quickly traversing relationships and identifying unusual patterns.</p> 
<p>In this post, we show how you can use graph algorithms to analyze the results of AWS Entity Resolution and related transactions for the CNP use case. We use several AWS services, including Neptune Analytics, AWS Entity Resolution, <a href="https://aws.amazon.com/sagemaker/ai/notebooks/" rel="noopener noreferrer" target="_blank">Amazon SageMaker notebooks</a>, and <a href="https://aws.amazon.com/s3/" rel="noopener noreferrer" target="_blank">Amazon Simple Storage Service</a> (Amazon S3).</p> 
<h2>Solution overview</h2> 
<p>AWS Entity Resolution ingests customer data from various sources, standardizing and matching records to create a single view of the customer with a persistent identifier. The persistent customer identifier, customer attributes, and transactions are then loaded into Neptune Analytics as vertices, and relationships between each entity form the edges of the graph. Amazon Neptune Workbench hosted <a href="https://aws.amazon.com/sagemaker-ai" rel="noopener noreferrer" target="_blank">Amazon SageMaker AI</a> notebooks provide the environment for investigators to assess the data. For more details, see <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/gettingStarted-accessing.html" rel="noopener noreferrer" target="_blank">Accessing the graph</a>.</p> 
<p>The following diagram illustrates the solution architecture.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-1.png"><img alt="Scope of solution" class="alignnone wp-image-68442 size-full" height="326" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-1.png" width="1579" /></a></p> 
<p>The workflow consists of the following steps:</p> 
<ol> 
 <li>Source customer data flows into AWS Entity Resolution for matching and standardization.</li> 
 <li>Resolved entities are placed into the output S3 bucket.</li> 
 <li>Entities and transactions are transformed in Neptune Workbench and loaded into Neptune Analytics as graph elements. Users then run queries in Neptune Workbench to modify the graph in memory and perform graph analytics.</li> 
</ol> 
<h2>Data model</h2> 
<p>The graph data model consists of several node types and edge relationships designed to represent customer and transaction data. The nodes include Group (containing persistent identifiers from AWS Entity Resolution), Email, Customer (with source system customer information like name and date of birth), Credit Card Account, Address, and Phone. These nodes are connected through relationships such as HAS_CUSTOMER (linking Group to Customer nodes with confidence scores), HAS_ACCOUNT (linking Customer to Credit Card Account nodes), HAS_PHONE, HAS_ADDRESS, and HAS_EMAIL. The model is further enhanced with transaction-related nodes like CnpCreditCardTxInit, CreditCardTx, and CnpInitFail, which are connected through relationships that track the flow of CNP transactions and their outcomes.The following diagram illustrates the graph data model.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-2.png"><img alt="" class="alignnone wp-image-68441 size-full" height="653" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-2.png" width="1245" /></a></p> 
<p>The following table lists the nodes and edges in more detail.</p> 
<table border="1px" cellpadding="10px" class="styled-table"> 
 <tbody> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>Type</strong></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>Label</strong></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>Description</strong></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>Properties</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>Group</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">The persistent identifier generated by AWS Entity Resolution for resolved entities</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>MatchId</code></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>Email</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Contains the email address identifiers for the entities ingested into AWS Entity Resolution</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>Email</code></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>Customer</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Contains the financial institution’s customer identifier and relevant customer information such as name fields and date of birth</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"> <p><code>FirstName</code></p> <p><code>MiddleName</code></p> <p><code>LastName</code></p> <p><code>DateOfBirth</code></p></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>CreditCardAccount</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Contains details regarding the customer’s linked account identifiers</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>AccountNumber</code></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>Address</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Contains the input address fields that were ingested by AWS Entity Resolution</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>Address</code></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>Phone</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Contains the customer’s known phone numbers that were provided during onboarding</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>PhoneNumber</code></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>CnpCreditCardTxInit</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Captures when a transaction was initiated without a physical card, such as online shopping</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"> <p><code>InitiationDate</code></p> <p><code>InitId</code></p></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>CreditCardTx</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Represents a successful transaction without a physical card</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"> <p><code>TransactionId</code></p> <p><code>Amount</code></p> <p><code>TransactionDate</code></p></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>CnpInitFail</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Captures a failure reason for why a transaction was rejected if a card was not present</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"> <p><code>TransactionId</code></p> <p><code>Amount</code></p> <p><code>TransactionDate</code></p> <p><code>ReasonCode</code></p></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Edge</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>HAS_CNP_TX_INIT</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">An edge from the CreditCard node to the CnPCreditCardTxInit node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Edge</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>HAS_CNP_TX_INIT</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">An edge from the CnPCreditCardTxInit node to the CreditCardTx node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Edge</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>HAS_FAIL</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">An edge from the CnPCreditCardTxInit node to the CnpInitFail node</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Edge</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>HAS_CUSTOMER</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">A relationship from the Group nodes to the associated Customer nodes; contains the confidence scores generated by the AWS Entity Resolution machine learning workflow</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>ConfidenceScore</code></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Edge</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>HAS_ACCOUNT</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">A relationship from the Customer nodes to the customer’s linked accounts</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Edge</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>HAS_PHONE</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">A relationship from the Customer nodes to the customer’s known identifiers</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Edge</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>HAS_ADDRESS</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">A relationship from the Customer nodes to the customer’s known addresses</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Edge</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><code>HAS_EMAIL</code></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">A relationship from the Customer nodes to the customer’s known email addresses</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
  </tr> 
 </tbody> 
</table> 
<h2>Prerequisites</h2> 
<p>You will incur costs on your account for the AWS services used in the example. Although AWS offers the <a href="https://aws.amazon.com/free/" rel="noopener noreferrer" target="_blank">AWS Free Tier</a> for some services such as SageMaker AI, review the pricing pages for <a href="https://aws.amazon.com/entity-resolution/pricing/" rel="noopener noreferrer" target="_blank">AWS Entity Resolution</a> and <a href="https://aws.amazon.com/neptune/pricing/" rel="noopener noreferrer" target="_blank">Amazon Neptune Analytics</a> before proceeding. For help with estimating costs, refer to the <a href="https://calculator.aws/#/" rel="noopener noreferrer" target="_blank">AWS Pricing Calculator</a>.</p> 
<p>To follow along with this post, you must have the following resources.</p> 
<ul> 
 <li>An <a href="https://signin.aws.amazon.com/signup?request_type=register" rel="noopener noreferrer" target="_blank">AWS account</a></li> 
 <li><a href="http://aws.amazon.com/cli" rel="noopener noreferrer" target="_blank">AWS Command Line Interface</a> (AWS CLI) installed</li> 
 <li>An S3 bucket for intermediate data</li> 
 <li>An <a href="https://docs.aws.amazon.com/entityresolution/latest/userguide/prepare-input-data.html#create-glue-table" rel="noopener noreferrer" target="_blank">AWS Glue Crawler and Glue Table</a> to retrieve schemas for AWS Entity Resolution</li> 
 <li>One or more <a href="https://aws.amazon.com/iam/" rel="noopener noreferrer" target="_blank">AWS Identity and Access Management</a> (IAM) roles to run the examples, with permissions to do the following: 
  <ul> 
   <li><a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-policies-s3.html#iam-policy-ex0" rel="noopener noreferrer" target="_blank">Read and write to an S3 bucket</a></li> 
   <li><a href="https://docs.aws.amazon.com/glue/latest/dg/add-crawler.html" rel="noopener noreferrer" target="_blank">Run a Glue Crawler, create a Glue Table</a></li> 
   <li><a href="https://docs.aws.amazon.com/entityresolution/latest/userguide/setting-up.html" rel="noopener noreferrer" target="_blank">Deploy and execute AWS Entity Resolution workflows</a></li> 
   <li><a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/gettingStarted.html" rel="noopener noreferrer" target="_blank">Create and interact with a Neptune Analytics graph instance</a></li> 
  </ul> </li> 
 <li>A <a href="https://docs.aws.amazon.com/sagemaker/latest/dg/sagemaker-roles.html" rel="noopener noreferrer" target="_blank">SageMaker execution role</a> with permissions to read and write to your S3 bucket</li> 
 <li>A <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/create-notebook-console.html" rel="noopener noreferrer" target="_blank">Neptune notebook</a> configured for Neptune Analytics</li> 
</ul> 
<h2>Prepare AWS Entity Resolution ML workflow</h2> 
<p>AWS Entity Resolution provides multiple workflow options to identify potential duplicate entities. Machine learning (ML) workflows use an AWS provided ML model that can handle variations in matching fields such as Name, Address, Email Address, Phone, and Date of Birth. The output of the workflow will provide a confidence score, which indicates how likely the entities within the same match group are duplicates based on the trained model. You can use rule-based workflows to configure matching logic based on business rules.</p> 
<p>Select a dataset such as the <a href="https://users.cecs.anu.edu.au/~Peter.Christen/Febrl/febrl-0.3/febrldoc-0.3/manual.html" rel="noopener noreferrer" target="_blank">Freely extensible biomedical record linkage (FEBRL)</a> dataset or create an example dataset with at least three of the five matching fields that can be used by the ML workflow. For this post, we used the <a href="https://faker.readthedocs.io/en/master/" rel="noopener noreferrer" target="_blank">Faker Python library</a> to create mock matching fields (address, date_of_birth, email, firstname, lastname, full_name, and middle) to perform entity resolution matching.</p> 
<p>After you have created the dataset, <a href="https://docs.aws.amazon.com/entityresolution/latest/userguide/prepare-input-data.html#upload-to-s3" rel="noopener noreferrer" target="_blank">loaded it into an S3 bucket</a> where AWS Entity Resolution workflows have read permissions, and <a href="https://docs.aws.amazon.com/entityresolution/latest/userguide/prepare-input-data.html#create-glue-table" rel="noopener noreferrer" target="_blank">crawled the data</a> using <a href="https://aws.amazon.com/glue" rel="noopener noreferrer" target="_blank">AWS Glue</a>, you must define a <a href="https://docs.aws.amazon.com/entityresolution/latest/userguide/create-schema-mapping.html" rel="noopener noreferrer" target="_blank">schema mapping</a>. The schema mapping informs AWS Entity Resolution workflows how to interpret the source fields for matching workflows.</p> 
<p>The following screenshot illustrates an example schema mapping.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-3.png"><img alt="Example AWS Entity Resolution schema mapping" class="alignnone wp-image-68440 size-full" height="436" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-3.png" width="1726" /></a></p> 
<p>After you create the schema, use the AWS CLI to create and run an ML workflow. Refer to <a href="https://docs.aws.amazon.com/entityresolution/latest/userguide/create-matching-workflow-ml.html" rel="noopener noreferrer" target="_blank">Creating a machine learning-based matching workflow</a> for steps to create and execute an ML workflow using <a href="http://aws.amazon.com/console" rel="noopener noreferrer" target="_blank">AWS Management Console</a>.</p> 
<p>First, create a JSON file named ml-workflow.json in the current directory and add the following contents (replace placeholders).</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">{
     "workflowName": "&lt;your ml workflow name&gt;", 
     "description": "Entity Resolution and Neptune Analytics ML Workflow",
     "inputSourceConfig": [
        {
            "applyNormalization": true,
            "inputSourceARN": "arn:aws:glue:&lt;region&gt;:&lt;AWS account&gt;:table/&lt;database name&gt;/&lt;table name&gt;",
            "schemaName": "&lt;schema name&gt;"
        }
     ], 
     "outputSourceConfig": [
        {
            "applyNormalization": true,
            "output": [
                {"name": "address", "hashed": false}, 
                {"name": "date_of_birth", "hashed": false}, 
                {"name": "email", "hashed": false}, 
                {"name": "full_name", "hashed": false}, 
                {"name": "lastname", "hashed": false},
                {"name": "middle", "hashed": false},
                {"name": "phone_number", "hashed": false}
            ],
            "outputS3Path": "s3://&lt;your s3 bucket&gt;/entityresolution/output/"
        }
    ],     
    "resolutionTechniques": {
          "resolutionType": "ML_MATCHING"
     },
     "roleArn":"arn:aws:iam::&lt;AWS account&gt;:role/&lt;entity-resolution-role-name&gt;"
}</code></pre> 
</div> 
<p>After you create the JSON file, run the following command to create the workflow:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">aws entityresolution create-matching-workflow --region &lt;region&gt; --cli-input-json file://ml-workflow.json</code></pre> 
</div> 
<p>Run the following command to execute the workflow:<code>aws entityresolution start-matching-job --region &lt;region&gt; --workflow-name &lt;your ml workflow name&gt;</code></p> 
<h2>Transform data output</h2> 
<p>When the ML workflow is complete, the output can be transformed into a valid Neptune data format and ingested into Neptune Analytics. The following code snippets show examples of how to transform the AWS Entity Resolution data output into the <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/using-CSV-data.html" rel="noopener noreferrer" target="_blank">OpenCypher Bulk Load</a> format.</p> 
<p>Use the following code in your Neptune Notebook to create nodes from the output of AWS Entity Resolution. Replace the placeholders with your own values:</p> 
<div class="hide-language"> 
 <pre><code class="lang-python">import datawangler as wr
import pandas as pd
# read data from AWS Entity Resolution output
df = wr.s3.read_csv("s3://&lt;your s3 bucket&gt;/&lt;ML workflow execution id&gt;/success/&lt;ML workflow execution file&gt;")

# Create Match Groups
sor = df[['MatchID']].drop_duplicates().dropna()
sor['~id'] = 'Group-'+sor['MatchID']
sor['~label'] = 'Group'
sor['MatchId:String(single)'] = sor['MatchID']
wr.s3.to_csv(sor, 's3://&lt;your s3 bucket&gt;/neptune/nodes/groups.csv', columns = ['~id', '~label', 'MatchId:String(single)'], index = False)

# Create email nodes
lg= df[['email']].drop_duplicates().dropna(subset='email')
lg['~id'] = 'Email-'+lg['email']
lg['~label'] = 'Email'
lg.rename(columns= {'email': 'Email:String(single)'}, inplace = True)
wr.s3.to_csv(lg, 's3://&lt;your s3 bucket&gt;/neptune/nodes/login.csv', columns = ['~id', '~label', 'email:String(Single)'], index = False)

# create CustomerId nodes
cust= df[['customer_id', 'firstname', 'lastname', 'middle', 'full_name', 'date_of_birth']].drop_duplicates()
cust['date_of_birth'] = pd.to_datetime(cust["date_of_birth"], format = '%m/%d/%y').dt.strftime('%Y-%m-%d').fillna('')
cust['~id'] = "Customer-" + cust['customer_id']
cust['~label'] = "Customer"
cust.rename(columns = {'customer_id': 'customer_id:String(single)',
'firstname': 'FirstName:String(single)',
'lastname': 'LastName:String(single)',
'middle': 'MiddleName:String(single)',
'date_of_birth': 'DateOfBirth:Date(single)'
},
inplace = True)
wr.s3.to_csv(cust, 's3://&lt;your s3 bucket&gt;/neptune/nodes/customer.csv', index = False)

# create phone nodes
lg= df[['phone']].drop_duplicates().dropna().astype(str)
lg['~id'] = 'Phone-'+lg['phone']
lg['~label'] = 'Phone'
lg.rename(columns= {'PhoneNumber': 'phone:String(single)'}, inplace = True)
wr.s3.to_csv(lg, 's3://&lt;your s3 bucket&gt;/neptune/nodes/phone.csv', index = False)

# address
addr = df[['address']].drop_duplicates().dropna()
addr['~id'] = 'Address-'+addr['address']
addr['~label'] = 'Address'
addr.rename(columns= {'address': 'address:String(single)'}, inplace = True)
wr.s3.to_csv(addr, 's3://&lt;your s3 bucket&gt;/neptune/nodes/address.csv', index = False)</code></pre> 
</div> 
<p>Use the following code to create edges from the output of AWS Entity Resolution:</p> 
<div class="hide-language"> 
 <pre><code class="lang-python"># Customer to Group
## Group to Device edges
in_group = df[['customer_id', 'MatchID', "ConfidenceLevel"]].drop_duplicates().dropna()
in_group['~to'] = "Customer-"+ in_group['customer_id']
in_group['~from'] = "Group-"+ in_group['MatchID']
in_group['~label'] = "HAS_CUSTOMER"
in_group['~id'] = in_group['~label'] +'-' + in_group['~from'] + in_group['~to']
in_group.rename(columns= {'ConfidenceLevel': 'confidence:Float'}, inplace = True)
wr.s3.to_csv(in_group, 's3://&lt;your s3 bucket&gt;/neptune/edges/hasCustomer.csv',
columns = ['~id', '~label', '~from', '~to', 'confidence:Float'], index = False)

#Customer to Phone
hasPhone = df[['customer_id', 'phone']].drop_duplicates().dropna(subset='phone')
hasPhone['~to'] = "Phone-"+ hasPhone['phone'].astype(str)
hasPhone['~from'] = "Customer-"+ hasPhone['customer_id']
hasPhone['~label'] = "HAS_PHONE"
hasPhone['~id'] = hasPhone['~label'] +'-' + hasPhone['~from'] + hasPhone['~to']
wr.s3.to_csv(hasId, 's3://&lt;your s3 bucket&gt;/neptune/edges/hasPhone.csv',
columns = ['~id', '~label', '~from', '~to'], index = False)

#Customer to Email
hasEmail = df[['customer_id', 'email']].drop_duplicates().dropna(subset='email')
hasEmail['~to'] = "Email-"+ hasEmail['email']
hasEmail['~from'] = "Customer-"+ hasEmail['customer_id']
hasEmail['~label'] = "HAS_EMAIL"
hasEmail['~id'] = hasEmail['~label'] +'-' + hasEmail['~from'] + hasEmail['~to']
wr.s3.to_csv(hasEmail, 's3://&lt;your s3 bucket&gt;/neptune/edges/hasEmail.csv',
columns = ['~id', '~label', '~from', '~to'], index = False)

#Customer to Address
hasAddr = df[['customer_id', 'address']].drop_duplicates().dropna(subset='address')
hasAddr['~to'] = "Address-"+ hasAddr['address']
hasAddr['~from'] = "Customer-"+ hasAddr['customer_id']
hasAddr['~label'] = "HAS_ADDRESS"
hasAddr['~id'] = hasAddr['~label'] +'-' + hasAddr['~from'] + hasAddr['~to']
wr.s3.to_csv(hasAddr, 's3://&lt;your s3 bucket&gt;/neptune/edges/hasAddress.csv',
columns = ['~id', '~label', '~from', '~to'], index = False)</code></pre> 
</div> 
<h2>Load additional datasets</h2> 
<p>We also want to supplement the mock customer data with some generated transactions that simulate a customer transaction workflow. Use the following code to load the data into an S3 bucket where the Neptune Loader has permissions to read from (replace the placeholders with your own values):</p> 
<div class="hide-language"> 
 <pre><code class="lang-python">import random
import uuid
import csv
import os
import boto3
import datawangler as wr
from faker import Faker
from faker.providers import internet

#define sample files
fake = Faker()
fake.seed_instance(1212)
fake.add_provider(internet)

def add_cnp_init(cwriter, crelwriter, cid, cnptxwriter, txrelwriter, cnpfailwriter, failrelwriter): #next-, cnpfailwriter, failrelwriter,
    # given a CC account number, create one or more CnpCreditCardTxInit(CNP_CC_TX_INIT_LABEL) nodes and attach via USER_TO_CNP_INIT_REL_LABEL rel
    # then create and attach cnptx or cnpfailure nodes for each CnpCreditCardTxInit node (one-to-one)
    cnp_node_count = random.randint(1, 20)
    for _ in range(cnp_node_count):
        curr_cnp_id = uuid.uuid4()
        cwriter.writerow([curr_cnp_id, uuid.uuid4(), CNP_CC_TX_INIT_LABEL])
        crelwriter.writerow([uuid.uuid4(), USER_TO_CNP_INIT_REL_LABEL, cid, curr_cnp_id])

        cnp_failed_rand = random.randint(1, 10)
        if cnp_failed_rand%3 == 0:  # create/attach cnpfail node
            curr_fail_id = uuid.uuid4()
            reason_code = random.randint(1, 3)
            cnpfailwriter.writerow([curr_fail_id, uuid.uuid4(), reason_code, CNP_CC_FAIL_LABEL])
            failrelwriter.writerow([uuid.uuid4(), CNP_INIT_TO_FAIL_REL_LABEL, curr_cnp_id, curr_fail_id])
        else:
            curr_tx_id = uuid.uuid4()
            cnptxwriter.writerow([curr_tx_id, uuid.uuid4(), CNP_CC_TX_LABEL])
            txrelwriter.writerow([uuid.uuid4(), CNP_INIT_TO_TX_REL_LABEL, curr_cnp_id, curr_tx_id])


USER_NODE_LABEL = "Customer"
CREDIT_CARD_ACCOUNT_LABEL = "CreditCardAccount"
CNP_CC_TX_INIT_LABEL = "CnpCreditCardTxInit"
CNP_CC_TX_LABEL = "CreditCardTx"
CNP_CC_FAIL_LABEL = "CnpInitFail"

USER_TO_CREDIT_CARD_ACCOUNT_REL_LABEL = "HAS_CC_ACCOUNT"
USER_TO_CNP_INIT_REL_LABEL = "HAS_CNP_TX_INIT"
CNP_INIT_TO_TX_REL_LABEL = "HAS_TX"
CNP_INIT_TO_FAIL_REL_LABEL = "HAS_FAIL"

# open the identity resolution file and get a list of the accounts. We want to attach random credit cards to those accounts.
sample_members = wr.s3.read_csv("s3://&lt;your s3 bucket&gt;/&lt;aws entity resolution source file&gt;

customer= sample_members[&lt;customer identifier field or unique customer field&gt;].tolist()

cc_random_generator = random.randint(1,9)

#Create csv writers
with open('./blog_cc_nodes.csv', mode='w') as cc_file, \
        open('./blog_member_to_cc_acct_rels.csv', mode='w') as m_cc_file, \
        open('./blog_cnp_init_nodes.csv', mode='w') as cnp_init_file, \
        open('./blog_cc_to_cnp_init_rels.csv', mode='w') as cc_to_cnp_init_file, \
        open('./blog_cnp_tx_nodes.csv', mode='w') as cnp_tx_file, \
        open('./blog_cnp_to_cnp_tx_rels.csv', mode='w') as cnp_to_tx_file, \
        open('./blog_cnp_fail_nodes.csv', mode='w') as cnp_fail_file, \
        open('./blog_cnp_to_fail_rels.csv', mode='w') as cnp_to_fail_file:

    cc_writer = csv.writer(cc_file, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)
    cc_writer.writerow(['~id', 'acct_number', '~label'])

    m_cc_writer = csv.writer(m_cc_file, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)
    m_cc_writer.writerow(['~id', '~label', '~from', '~to'])

    cnp_writer = csv.writer(cnp_init_file, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)
    cnp_writer.writerow(['~id', 'tx_init_id', '~label'])

    cc_to_cnp_writer = csv.writer(cc_to_cnp_init_file, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)
    cc_to_cnp_writer.writerow(['~id', '~label', '~from', '~to'])

    cnp_tx_writer = csv.writer(cnp_tx_file, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)
    cnp_tx_writer.writerow(['~id', 'tx_id', '~label'])

    cnp_to_tx_writer = csv.writer(cnp_to_tx_file, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)
    cnp_to_tx_writer.writerow(['~id', '~label', '~from', '~to'])

    cnp_fail_writer = csv.writer(cnp_fail_file, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)
    cnp_fail_writer.writerow(['~id', 'tx_id', 'reason_code', '~label'])

    cnp_to_fail_writer = csv.writer(cnp_to_fail_file, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)
    cnp_to_fail_writer.writerow(['~id', '~label', '~from', '~to'])

    for i in customer:
        curr_member_id = i
        if 1 &lt; cc_random_generator &lt;=9: #subset of members with credit accounts.
            curr_cc_id = uuid.uuid4()
            cc_writer.writerow([curr_cc_id, fake.credit_card_number(), CREDIT_CARD_ACCOUNT_LABEL])
            m_cc_writer.writerow([uuid.uuid4(), USER_TO_CREDIT_CARD_ACCOUNT_REL_LABEL, f'Customer-{curr_member_id}', curr_cc_id])

            add_cnp_init(cnp_writer, cc_to_cnp_writer, curr_cc_id, cnp_tx_writer, cnp_to_tx_writer, cnp_fail_writer, cnp_to_fail_writer)
# once all files have been created write to the s3 bucket where Neptune Analytics has read access.
s3_client = boto3.client('s3')
neptune_bucket = '&lt;your s3 bucket&gt;'
for file in os.listdir('.'):
    if file.startswith('blog'):
        if 'nodes.csv' in file:
            object_name = f'neptune/nodes/{file}'
        elif 'rels.csv' in file:
            object_name = f'neptune/edges/{file}'
        s3.upload_file(file, neptune_bucket, object_name)</code></pre> 
</div> 
<p>Neptune Analytics supports multiple mechanisms to load data into the in-memory graph, including loading from an existing Neptune cluster and from Amazon S3. For this example, we read data from <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/batch-load.html" rel="noopener noreferrer" target="_blank">Amazon S3 using a batch load</a>, where the <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/batch-load.html" rel="noopener noreferrer" target="_blank">caller’s IAM role has the appropriate permissions</a>:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">CALL neptune.load(
  {
    source: "${Neptune S3 Loader Bucket}",
    region: &lt;region&gt;,
    format: "csv"
  }
)</code></pre> 
</div> 
<h2>Analyze output with Neptune Analytics</h2> 
<p>Using Neptune Analytics through Neptune Workbench, you can run powerful graph algorithms like <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/louvain.html" rel="noopener noreferrer" target="_blank">Louvain</a> and <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/wcc.html" rel="noopener noreferrer" target="_blank">weakly connected components</a> to identify clusters of potentially fraudulent activities and analyze relationships between resolved entities. For example, you can quickly identify clusters of CNP transaction failures, analyze the number of shared personally identifiable information (PII) elements between different entities, and assess risk based on the number of known bad actors in the graph, making it an effective tool for detecting sophisticated fraud patterns.</p> 
<p>The <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/louvain.html" rel="noopener noreferrer" target="_blank">Louvain algorithm</a> is a community detection algorithm that helps identify clusters or communities within a graph by optimizing modularity, which measures the density of connections within communities compared to connections between communities. In Neptune Analytics, the Louvain algorithm can support the discovery of natural groupings in data, such as finding customer segments or detecting fraudulent clusters of accounts that are working together.</p> 
<p>The queries in this post illustrate the use of Louvain and weakly connected components, though results will vary based on your specific dataset characteristics. In Neptune Analytics, the CALL keyword invokes the algorithms, while mutate keyword writes computed results back to the graph as new properties on nodes or edges. We will use Neptune Notebook’s visualization tools to showcase query results. There are also advanced visualization tools available such as <a href="https://docs.aws.amazon.com/neptune/latest/userguide/visualization-graph-explorer.html" rel="noopener noreferrer" target="_blank">Graph-explorer</a>, an open-source tool that you can use to browse graph-data without writing queries.</p> 
<p>First, let’s find clusters within the graph for transactions where they are associated with CNPInitFail. We want to persist these clusters with an edge property, CNPFailCommunity:</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql">Match (p:Customer)-[r:HAS_CC_ACCOUNT]-&gt;(a:CreditCardAccount)-[r2*1..]-&gt;(n:CnpInitFail)
with collect([p, a, n]) as test
unwind test as list_set
unwind list_set as t
with t
CALL neptune.algo.louvain.mutate(
  {
    edgeLabels: ["HAS_CC_ACCOUNT","HAS_TX", "HAS_CNP_TX_INIT",  "HAS_FAIL"],
    writeProperty: "CNPFailCommunity"
  }
)
YIELD success
return success</code></pre> 
</div> 
<p>In addition to discovering clusters associated with CNPInitFail, we want to analyze the AWS Entity Resolution results. Although clusters have already been created by AWS Entity Resolution, we can create additional clusters using the graph algorithm and <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/wcc-mutate.html" rel="noopener noreferrer" target="_blank">weakly connected components</a> to generate clusters where resolved customers might share at least one matching attribute:</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql">Match (n)
CALL neptune.algo.wcc.mutate(
    {
    edgeLabels: ["HAS_ADDRESS",
      "HAS_EMAIL",
      "HAS_FAIL",
      "HAS_CUSTOMER",
      "HAS_PHONE",
      "HAS_CC_ACCOUNT"],
    writeProperty: "WCC"
    }
)
yield success
return success</code></pre> 
</div> 
<p>Now that our clusters have been generated, let’s find the largest cluster of transactions generated by Louvain:</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql">Match (n:CnpInitFail)
return distinct n.CNPFailCommunity, count(n.CNPFailCommunity) as numNodes
order by numNodes desc
limit 1</code></pre> 
</div> 
<p>We can take this largest cluster of transactions and retrieve the associated AWS Entity Resolution attributes that are associated with the same weakly connected components cluster:</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql">Match (n:CnpInitFail)
with distinct n.CNPFailCommunity as comm_id, count(n.CNPFailCommunity) as numNodes
order by numNodes desc
limit 1
match (n:Customer)
where n.CNPFailCommunity = comm_id
with n
Match (imp_nodes:Customer {WCC: n.WCC})-[r]-&gt;(pii)
return *</code></pre> 
</div> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-4.png"><img alt="" class="alignnone wp-image-68439 size-full" height="1151" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-4.png" width="1326" /></a></p> 
<p>Let’s drill down further into to CNPInitFail, where we select a failure code to assess risk. Assume that there are only three failure codes (1, 2, and 3) generated in the preceding transaction code, where failure code 3 is the riskiest. We want to see if multiple AWS Entity Resolution resolved entities are associated with the failures:</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql">Match (n:CnpInitFail {reason_code: "3"})
with n.CNPFailCommunity as lvn
Match (g:Group)-[r]-&gt;(c:Customer {CNPFailCommunity: lvn})
return g.WCC, count(distinct g) as num
order by num desc</code></pre> 
</div> 
<p>Given the group with the largest number of resolved entities (distinct AWS Entity Resolution match IDs), we want to assess the number of shared PII elements to assess if these entities’ distinct groups are two distinct bad actors or a single bad actor:</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql">Match (n:CnpInitFail {reason_code: "3"})
with n.CNPFailCommunity as lvn
Match (g:Group)-[r]-&gt;(c:Customer {CNPFailCommunity: lvn})
with g.WCC as find_wcc, count(distinct g) as num
order by num desc
limit 1
with find_wcc
Match path =(g1:Group)-[x]-&gt;(p:Customer)-[r]-&gt;(pii)&lt;-[r2]-(p2)&lt;-[x2]-(g2:Group)
where g1 &lt;&gt; g2 and p.WCC = find_wcc
with collect(distinct pii.`~id`) as shared, find_wcc
Match (g3:Group)-[rc]-&gt;(p2:Customer {WCC: find_wcc})-[r]-&gt;(pii2)
where not pii2.`~id` in shared and labels(pii2) &lt;&gt;["CreditCardAccount"]
return p2, r, pii2, g3, rc</code></pre> 
</div> 
<p>For visualization purposes (comment out the where not statement), we can see two distinct Group nodes and four parties. The two groups have a differing email address, phone, and address that are not shared. This suggests that the two resolved entities might be related, because they have shared the same number and at least one address.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-5.png"><img alt="" class="alignnone wp-image-68438 size-full" height="1680" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-5.png" width="2846" /></a></p> 
<p>We can also perform risk analytics based on the number of known bad actors in the graph. The following query analyzes groups of resolved entities by calculating the ratio of bad actors to total customers within each match group, ordered by the percentage of bad actors per cluster. This analysis helps identify which groups have the highest concentration of customers associated with high-risk CNP transaction failures (for example, reason code 3), providing investigators with a risk-based metric to prioritize their investigations.</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql">Match (g:Group)-[r]-&gt;(c:Customer)
with g, c
Match (g)-[]-&gt;(bd:Customer)-[r2:HAS_CC_ACCOUNT]-&gt;(a)-[r3:HAS_CNP_TX_INIT]-&gt;(x)-[r4:HAS_FAIL]-&gt;(f {reason_code: "3"})
return g.MatchId, count(distinct bd) as numBadActors, count(distinct c) as totalCustomers, 
count(distinct bd)/toFloat(count (distinct c)) as percentPerCluster
order by percentPerCluster desc</code></pre> 
</div> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-6.png"><img alt="" class="alignnone wp-image-68437 size-full" height="352" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-6.png" width="1430" /></a></p> 
<p>For example, based on the above output, the first two results have a higher percentage of bad actors in the cluster at 50% as opposed to the third cluster with 30% of bad actors in the cluster. However, there are only 2 total customers within groups 1 and 2, which may be an important consideration for investigations.</p> 
<h2>Clean up</h2> 
<p>When you’re done, clean up your resources to stop incurring costs. You can do so using the AWS CLI or the respective service consoles. For instructions, refer to <a href="https://docs.aws.amazon.com/entityresolution/latest/userguide/delete-matching-workflow.html" rel="noopener noreferrer" target="_blank">AWS Entity Resolution</a>, <a href="https://docs.aws.amazon.com/cli/latest/reference/glue/delete-table.html" rel="noopener noreferrer" target="_blank">AWS Glue Tables</a>, <a href="https://docs.aws.amazon.com/cli/latest/reference/glue/delete-crawler.html" rel="noopener noreferrer" target="_blank">AWS Glue Crawlers</a>, <a href="https://docs.aws.amazon.com/sagemaker/latest/dg/ex1-cleanup.html" rel="noopener noreferrer" target="_blank">Neptune Notebooks</a>, or <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/managing-deleting.html" rel="noopener noreferrer" target="_blank">Neptune Analytics</a> documentation. You can also <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/empty-bucket.html" rel="noopener noreferrer" target="_blank">delete S3 buckets or objects</a> created as part of this exercise.</p> 
<h2>Conclusion</h2> 
<p>The combination of AWS Entity Resolution and Neptune provides a powerful solution for financial institutions to detect and prevent CNP fraud. By using AWS Entity Resolution to match and standardize customer records with the Neptune graph database, organizations can quickly identify suspicious patterns and relationships between entities. The solution in this post demonstrated how you can transform resolved entities into a graph structure, perform advanced analytics using Neptune Analytics, and visualize complex relationships between customers, accounts, and transactions. The integration with Neptune Workbench helps investigators efficiently analyze clusters of potentially fraudulent activities and assess the relationships between resolved entities.</p> 
<p>To learn more about using AWS Entity Resolution or Neptune Analytics, contact your AWS account team or visit the <a href="https://docs.aws.amazon.com/entityresolution/latest/userguide/what-is-service.html" rel="noopener noreferrer" target="_blank">AWS Entity Resolution</a> and <a href="https://docs.aws.amazon.com/neptune-analytics/latest/userguide/what-is-neptune-analytics.html" rel="noopener noreferrer" target="_blank">Amazon Neptune</a> User Guides.</p> 
<hr /> 
<h2>About the authors</h2> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="Jessica Hung" class="aligncenter size-full wp-image-29797" height="160" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-8-1.jpeg" width="120" />
  </div> 
  <h3 class="lb-h4">Jessica Hung</h3> 
  <p><a href="https://www.linkedin.com/in/jessica-hung-91a833128/" rel="noopener" target="_blank">Jessica</a> is a Senior Data Architect at AWS Professional Services. She helps customers build highly scalable applications with AWS services such as Amazon Neptune and AWS Entity Resolution. In her time at AWS, she has supported customers across diverse sectors including financial services. She specializes in graph database and entity resolution workloads.</p>
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="Ross Gabay" class="aligncenter size-full wp-image-29797" height="160" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-7-1.png" width="120" />
  </div> 
  <h3 class="lb-h4">Ross Gabay</h3> 
  <p><a href="https://www.linkedin.com/in/rossgabay/" rel="noopener" target="_blank">Ross</a> is a Principal Data Architect in AWS Professional Services. He works with AWS Customers helping them implement Enterprise-grade solutions using Amazon Neptune and other AWS services.</p>
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="Isaac Kwasi Owusu" class="aligncenter size-full wp-image-29797" height="160" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/26/dbblog4980-9-1.jpeg" width="120" />
  </div> 
  <h3 class="lb-h4">Isaac Kwasi Owusu</h3> 
  <p><a href="https://www.linkedin.com/in/isaac-owusu/" rel="noopener" target="_blank">Isaac</a> is a Senior Data Architect at AWS, with a strong track record in designing and implementing large-scale data solutions for enterprises. He holds a Master of Information System Management from Carnegie Mellon University and has over 10 years of experience in NoSQL databases, specializing in graph databases. In his free time, Isaac loves traveling, photography, and supporting Liverpool FC.</p>
 </div> 
</footer>
