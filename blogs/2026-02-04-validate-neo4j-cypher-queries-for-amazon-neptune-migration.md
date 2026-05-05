---
title: "Validate Neo4j Cypher queries for Amazon Neptune migration"
url: "https://aws.amazon.com/blogs/database/validate-neo4j-cypher-queries-for-amazon-neptune-migration/"
date: "Wed, 04 Feb 2026 22:57:07 +0000"
author: "Justin John"
feed_url: "https://aws.amazon.com/blogs/database/category/database/amazon-neptune/feed/"
---
<p>When migrating from Neo4j to <a href="https://aws.amazon.com/neptune/" rel="noopener noreferrer" target="_blank">Amazon Neptune</a>, you should ensure Cypher query compatibility is maintained. The Amazon <a href="https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-opencypher.html" rel="noopener noreferrer" target="_blank">Neptune openCypher</a> implementation generally supports the clauses, operators, expressions, and functions defined in the <a href="https://s3.amazonaws.com/artifacts.opencypher.org/openCypher9.pdf" rel="noopener noreferrer" target="_blank">openCypher specification</a>, but with important limitations and differences that require careful attention. The migration process requires you to systematically analyze existing queries to identify Neo4j-specific syntax, functions, and patterns that you need to transform to comply with Neptune’s specifications so that your graph database applications function correctly in the <a href="https://aws.amazon.com/" rel="noopener noreferrer" target="_blank">Amazon Web Services (AWS)</a> environment.</p> 
<p>For detailed guidance on migrating a Neo4j graph database to a Neptune database, see <a href="https://aws.amazon.com/blogs/database/automate-your-neo4j-to-amazon-neptune-migration-using-the-neo4j-to-neptune-utility/">Automate your Neo4j to Amazon Neptune migration using the neo4j-to-neptune utility</a>, which helps you streamline you migration process by providing flexible options for both fully automated and step-by-step approaches.</p> 
<p>In this post, we show you how to validate Neo4j Cypher queries before migrating to Neptune using the <a href="https://github.com/awslabs/amazon-neptune-tools/tree/master/opencypher-compatability-checker" rel="noopener noreferrer" target="_blank">openCypher Compatibility Checker</a> tool. You can use this tool to identify compatibility issues early in your migration process, reducing migration time and effort.</p> 
<h2>Solution overview</h2> 
<p>The openCypher Compatibility Checker tool provides detailed reports highlighting specific areas of concern, such as unsupported functions, syntax variations, and <a href="https://neo4j.com/docs/apoc/current/" rel="noopener noreferrer" target="_blank">Neo4j-specific</a> features that need alternative implementations in Neptune. The tool can detect common conversion challenges, including differences in string matching functions, aggregation methods, and pattern matching syntax, offering suggestions alternatives that are compatible with Neptune.</p> 
<p>When you encounter Neo4j-specific functions such as APOC&nbsp;procedures or custom plugins, the tool flags these instances and can recommend equivalent Neptune functions or alternative approaches to achieve the same functionality. It generates comprehensive reports that detail which parts of your queries can be directly converted, which require modification, and which might need complete restructuring. Through this systematic approach, you can prioritize your conversion efforts and estimate the work required for a successful migration.The solution architecture is straightforward. It doesn’t require an AWS environment; the tool can be downloaded from GitHub and used on your client machine or desktop. The following diagram shows the workflow of the process of using the openCypher Compatibility Checker tool.</p> 
<p><img alt="Figure 1: Architecture diagram showing workflow the openCypher Compatibility Checker tool" class="size-full wp-image-68254 aligncenter" height="261" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/18/DBBLOG-51251.png" width="838" /></p> 
<p>The openCypher Compatibility Checker tool processes a JSON file of Cypher queries, evaluates their compatibility with the openCypher specification, and generates a JSON output containing either automatically converted openCypher queries or recommended alternatives for queries that require manual adjustment.</p> 
<h2>Prerequisites</h2> 
<p>To implement the solution, you need to have the following prerequisites in place:</p> 
<ul> 
 <li>Java version 17 (jdk-17.0.12) or later. For information on how to install, see the <a href="https://docs.oracle.com/en/java/javase/17/install/overview-jdk-installation.html#GUID-8677A77F-231A-40F7-98B9-1FD0B48C346A__INSTALLINGTHEJDKANDJREONLINUX-E04E90B9" rel="noopener noreferrer" target="_blank">JDK Installation Guide</a>.</li> 
 <li>Your Neo4j Cypher queries exported in JSON format.</li> 
 <li>Download the <a href="https://github.com/awslabs/amazon-neptune-tools/tree/master/opencypher-compatability-checker" rel="noopener noreferrer" target="_blank">openCypher Compatibility Checker tool</a>.</li> 
</ul> 
<h2>How to use the tool</h2> 
<p>The openCypher Compatibility Checker expects a JSON input file with a specific structure:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">{
"targetSystem": "na|ndb",
  "queries": [
     {
     "id": 1,
     "query": "_query_text_"
     }
 ]
 }&nbsp;</code></pre> 
</div> 
<p>The JSON object has two main fields. The&nbsp;<code>targetSystem</code>&nbsp;field specifies the target database system <code>na</code>(Neptune Analytics) or <code>ndb</code> (Neptune database), while the&nbsp;<code>queries</code>&nbsp;array contains multiple query objects to be processed in a single batch. Each query object includes an&nbsp;<code>id</code> field, a unique numeric identifier that distinguishes each query, and a&nbsp;<code>query</code>&nbsp;field containing the actual Cypher query text to be analyzed and converted. You can include as many queries as needed in the array, with each requiring a unique ID to track results and identify which queries need attention. The tool processes all queries in the batch and returns results mapped to their corresponding IDs, so you can analyze multiple queries efficiently in a single execution.</p> 
<p>The following example JSON structure defines a batch of Cypher queries for compatibility checking. The tool processes two queries: one retrieving <code>Person</code> nodes (<code>id: 1</code>) and another finding <code>User-FOLLOWS</code> relationships (<code>id: 2</code>), returning compatibility analysis and any necessary transformations for each.</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">{
    "targetSystem": "na",
    "queries": [
        {
            "id": 1,
            "query": "MATCH (n:Person) RETURN n"
        },
        {
            "id": 2,
            "query": "MATCH (n:User)-[:FOLLOWS]-&gt;(m) RETURN n,m"
        }
    ]
}</code></pre> 
</div> 
<h2>Example input: Sample Cypher queries for compatibility</h2> 
<p>To demonstrate the working of the openCypher Compatibility Checker tool, I’ll use the following example file, which contains a few cypher queries:</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql">{
  "targetSystem": "NA",
  "queries": [
    {
      "id": 1,
      "query": "match p=(a:Start)-[:HOP*1..]-&gt;(z:End) where none(node IN nodes(p) where node.class ='D') return p"
    },
    {
      "id": 2,
      "query": "MATCH p=(a:airport {code: 'ANC'})-[r:route*1..3]-&gt;(z:airport {code: 'AUS'}) RETURN p, reduce(totalDist=0, r in relationships(p) | totalDist + r.dist) AS totalDist ORDER BY totalDist LIMIT 5"
    },
    {
      "id": 3,
      "query": "MATCH p=(:airport {code: 'ANC'})-[*1..2]-&gt;({code: 'AUS'}) FOREACH (n IN nodes(p) | SET n.visited = true)"
    },
    {
      "id": 4,
      "query": "MATCH p=(start)-[*]-&gt;(finish) WHERE start.name = 'A' AND finish.name = 'D' FOREACH (n IN nodes(p) | SET n.marked = true)"
    },
    {
      "id": 5,
      "query": "MATCH (n:airport {region: 'US-AK'}) CALL apoc.do.when( n.runways&gt;=3, 'SET n.is_large_airport=true RETURN n','SET n.is_large_airport=false RETURN n', {n:n} ) YIELD value WITH collect(value.n) as airports RETURN size([a in airports where a.is_large_airport]) as large_airport_count, size([a in airports where NOT a.is_large_airport]) as small_airport_count"
    },
    {
      "id": 6,
      "query": "MATCH (n:airport {region: 'US-AK'}) CALL apoc.case([ n.runways=1, 'RETURN \\\"Has one runway\\\" as b', n.runways=2, 'RETURN \\\"Has two runways\\\" as b'], 'RETURN \\\"Has more than 2 runways\\\" as b') YIELD value  RETURN {type: value.b,airport: n}"
    }
  ]
}
&nbsp;</code></pre> 
</div> 
<p>The example contains six queries with varying compatibility levels for the Neptune openCypher format.</p> 
<p><strong>Query ID 1</strong>&nbsp;uses the&nbsp;<code>none()</code>&nbsp;predicate function to filter paths. This query is supported but requires minor modification as explained in the Neptune documentation on rewriting <a href="https://docs.aws.amazon.com/neptune/latest/userguide/migration-opencypher-rewrites.html#migration-opencypher-rewrites-none-all-any" rel="noopener noreferrer" target="_blank">None, All, and Any predicate functions</a>.</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql"># Neo4J Cypher code
match p=(a:Start)-[:HOP*1..]-&gt;(z:End)
where none(node IN nodes(p) where node.class ='D')
return p</code></pre> 
</div> 
<p><strong>Query ID 2</strong>&nbsp;uses the&nbsp;<code>reduce()</code>&nbsp;function to calculate total distance across relationships. This query is supported but needs minor modification as detailed in the Neptune documentation on <a href="https://docs.aws.amazon.com/neptune/latest/userguide/migration-opencypher-rewrites.html#migration-opencypher-rewrites-reduce" rel="noopener noreferrer" target="_blank">rewriting the Cypher&nbsp;reduce()&nbsp;function in openCypher</a>.</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql"># Neo4J Example
match p=(a:airport {code: 'ANC'})-[r:route*1..3]-&gt;(z:airport {code: 'AUS'})
return p, reduce(totalDist=0, r in relationships(p) | totalDist + r.dist) AS totalDist
ORDER BY totalDist LIMIT 5</code></pre> 
</div> 
<p><strong>Query IDs 3 and 4</strong> use the&nbsp;<code>FOREACH</code>&nbsp;clause to update node properties along a path. These queries aren’t supported and require a complete rewrite using the&nbsp;<code>UNWIND</code>&nbsp;clause combined with appropriate pattern matching, as explained in the Neptune documentation on <a href="https://docs.aws.amazon.com/neptune/latest/userguide/migration-opencypher-rewrites.html#migration-opencypher-rewrites-foreach" rel="noopener noreferrer" target="_blank">rewriting the Cypher&nbsp;FOREACH&nbsp;clause</a>.</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql"># Neo4J Example
MATCH p=(:airport {code: 'ANC'})-[*1..2]-&gt;({code: 'AUS'})
FOREACH (n IN nodes(p) | SET n.visited = true)
MATCH p=(start)-[*]-&gt;(finish) WHERE start.name = 'A' AND finish.name = 'D' FOREACH (n IN nodes(p) | SET n.marked = true)"</code></pre> 
</div> 
<p><strong>Query ID 5</strong>&nbsp;uses the Neo4j APOC procedure&nbsp;<code>apoc.do.when()</code>&nbsp;for conditional logic. This query isn’t supported because APOC procedures are Neo4j-specific. It requires a rewrite using the alternatives built into Neptune, such as list comprehension capabilities with the&nbsp;<code>UNWIND</code> clause, as explained in the Neptune documentation on <a href="https://docs.aws.amazon.com/neptune/latest/userguide/migration-opencypher-rewrites.html#migration-opencypher-rewrites-apoc" rel="noopener noreferrer" target="_blank">rewriting Neo4j APOC procedures</a>.</p> 
<div class="hide-language"> 
 <pre><code class="lang-sql"># Neo4J Example
MATCH (n:airport {region: 'US-AK'})
CALL apoc.do.when(
 n.runways&gt;=3,
 'SET n.is_large_airport=true RETURN n',
 'SET n.is_large_airport=false RETURN n',
 {n:n}
) YIELD value
WITH collect(value.n) as airports
RETURN size([a in airports where a.is_large_airport]) as large_airport_count,
size([a in airports where NOT a.is_large_airport]) as small_airport_count</code></pre> 
</div> 
<p><strong>Query ID 6</strong>&nbsp;uses the APOC procedure&nbsp;<code>apoc.case()</code>&nbsp;for case-based logic. This query isn’t supported in the Neptune openCypher implementation and requires rewriting using the native capabilities Neptune. While the tool identifies this incompatibility, it doesn’t provide a specific replacement suggestion for this APOC function. See the <a href="https://docs.aws.amazon.com/neptune/latest/userguide/migration-compatibility.html" rel="noopener noreferrer" target="_blank">Neptune compatibility documentation with Neo4j</a> to find guidance on converting unsupported APOC functions into Neptune-supported openCypher queries or equivalent Neptune functions.</p> 
<h2>Executing the compatibility check and interpreting results</h2> 
<p>This section demonstrates how to use the openCypher Compatibility Checker to analyze the input file. The tool accepts two parameters:&nbsp;<code>--input</code> to specify the source file containing queries to process (for example,&nbsp;<code>--input queries.json</code>), and&nbsp;<code>--output</code>&nbsp;to define where the migration results should be saved (for example,&nbsp;<code>--output results.json</code>). When executed, the application reads queries from the input file, applies compatibility analysis and transformation logic, and writes the results to the output file. This migration helper streamlines the process of converting queries from Neo4j’s Cypher dialect to openCypher-compliant syntax compatible with Neptune.</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">java -jar NeptuneNeo4jMigrationHelper-1.0.jar --input queries.json --output results.json</code></pre> 
</div> 
<p>After running the preceding command, the compatibility tool analysis results are written to the&nbsp;<code>results.json</code>file. You can view the output by opening this file in any text editor or JSON viewer. The results file contains a structured JSON response with a&nbsp;<code>results</code>&nbsp;array, where each element corresponds to one of your input queries, matched by its unique&nbsp;<code>id</code>. For each query, the output indicates whether it’s&nbsp;<code>supported</code>&nbsp;(true or false) and provides an&nbsp;<code>errorDefinitions</code>&nbsp;array that details any compatibility issues found. When a query is unsupported, the error definitions include the exact&nbsp;<code>position</code>&nbsp;of the problematic syntax, the&nbsp;<code>name</code>&nbsp;of the unsupported feature, a potential&nbsp;<code>replacement</code>&nbsp;suggestion (if available), and a&nbsp;<code>description</code>&nbsp;explaining why the feature isn’t supported and how you might address it. This structured format simplifies identifying which queries need modification and understanding exactly what changes are required for Neptune compatibility. The following JSON shows the results.json file:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">{
  "results" : [ {
    "id" : 1,
    "supported" : true,
    "errorDefinitions" : [ ]
  }, {
    "id" : 2,
    "supported" : true,
    "errorDefinitions" : [ ]
  }, {
    "id" : 3,
    "supported" : false,
    "errorDefinitions" : [ {
      "position" : "line 1, column 59 (offset: 58)",
      "name" : "FOREACH",
      "replacement" : "",
      "description" : "FOREACH is not supported in this release"
    } ]
  }, {
    "id" : 4,
    "supported" : false,
    "errorDefinitions" : [ {
      "position" : "line 1, column 76 (offset: 75)",
      "name" : "FOREACH",
      "replacement" : "",
      "description" : "FOREACH is not supported in this release"
    } ]
  }, {
    "id" : 5,
    "supported" : false,
    "errorDefinitions" : [ {
      "position" : "line 1, column 37 (offset: 36)",
      "name" : "CALL",
      "replacement" : "",
      "description" : "CALL clause inputs of type GreaterThanOrEqual(Property(Variable(n),PropertyKeyName(runways,None)),SignedDecimalIntegerLiteral(3))"
    }, {
      "position" : "line 1, column 37 (offset: 36)",
      "name" : "apoc.do",
      "replacement" : "List Comprehension capabilities with the UNWIND clause",
      "description" : "apoc.do is not supported in this release but try replacing with List Comprehension capabilities with the UNWIND clause"
    } ]
  }, {
    "id" : 6,
    "supported" : false,
    "errorDefinitions" : [ {
      "position" : "line 1, column 37 (offset: 36)",
      "name" : "apoc.case",
      "replacement" : "",
      "description" : "apoc.case is not supported in this release"
    } ]
  } ]
}</code></pre> 
</div> 
<p>The tool generates a detailed report showing:</p> 
<ul> 
 <li>Whether each query is supported</li> 
 <li>The position of unsupported elements in the cypher query</li> 
 <li>Names of unsupported functions and clauses</li> 
 <li>Suggested replacements (if available)</li> 
 <li>Detailed error descriptions</li> 
</ul> 
<h2>Common migration scenarios</h2> 
<p>This guide outlines key adaptations and technical considerations for a successful migration.</p> 
<p>Predicate functions</p> 
<ul> 
 <li><code>NONE()</code>: Can be implemented using list comprehension</li> 
 <li><code>ALL()</code>: Can be rewritten using list comprehension</li> 
 <li><code>ANY()</code>: Achievable through list comprehension patterns</li> 
</ul> 
<p>Aggregation and processing</p> 
<ul> 
 <li><code>reduce()</code>: Can be implemented using a combination of: 
  <ul> 
   <li>List comprehension</li> 
   <li><code>UNWIND</code>&nbsp;clause</li> 
   <li>Appropriate aggregation functions</li> 
  </ul> </li> 
</ul> 
<p>Control flow modifications</p> 
<ul> 
 <li><code>FOREACH</code>&nbsp;clause: 
  <ul> 
   <li>Replace with&nbsp;<code>UNWIND</code>&nbsp;operations</li> 
   <li>Combine with appropriate pattern matching</li> 
   <li>Use multiple&nbsp;<code>MATCH</code>&nbsp;or&nbsp;<code>CREATE</code>&nbsp;statements as needed</li> 
  </ul> </li> 
</ul> 
<p>Procedure adaptations</p> 
<ul> 
 <li>Neo4j APOC procedures: 
  <ul> 
   <li>Use the built-in Neptune alternatives where available</li> 
   <li>Implement custom solutions for unsupported functionalities</li> 
   <li>Use the built-in graph operations available in Neptune</li> 
  </ul> </li> 
</ul> 
<p>For more information, see <a href="https://docs.aws.amazon.com/neptune/latest/userguide/migration-opencypher-rewrites.html" rel="noopener noreferrer" target="_blank">Rewriting Cypher queries to run in openCypher on Neptune</a>.</p> 
<h2>Conclusion</h2> 
<p>The openCypher Compatibility Checker streamlines Neo4j to Neptune migrations by identifying compatibility issues early and providing clear guidance on necessary changes. While the tool automates incompatibility detection, you should carefully evaluate each case and thoroughly test rewritten queries to verify that they maintain the intended functionality in a Neptune openCypher implementation.</p> 
<hr /> 
<h2>About the authors</h2> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="Justin John" class="aligncenter size-full wp-image-29797" height="160" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/18/DBBLOG-51252.jpeg" width="120" />
  </div> 
  <h3 class="lb-h4">Justin John</h3> 
  <p><a href="https://www.linkedin.com/in/justin-john-dba/" rel="noopener" target="_blank">Justin</a> is a Database Specialist Solutions Architect with AWS. Prior to AWS, Justin worked for large enterprises in technology, financials, airlines, and energy verticals, helping them with database and solution architectures. In his spare time, you’ll find him exploring the great outdoors through hiking and traveling to new destinations.</p>
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="autDave Bechberger" class="aligncenter size-full wp-image-29797" height="160" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/18/DBBLOG-51253.jpeg" width="120" />
  </div> 
  <h3 class="lb-h4">Dave Bechberger</h3> 
  <p><a href="https://www.linkedin.com/in/davebechberger/" rel="noopener" target="_blank">Dave</a> is a Sr. Graph Architect with the Amazon Neptune team. He used his years of experience working with customers to build graph database-backed applications as inspiration to co-author Graph Databases in Action by Manning.</p>
 </div> 
</footer>
