---
title: "Automate your Neo4j to Amazon Neptune migration using the neo4j-to-neptune utility"
url: "https://aws.amazon.com/blogs/database/automate-your-neo4j-to-amazon-neptune-migration-using-the-neo4j-to-neptune-utility/"
date: "Wed, 04 Feb 2026 22:55:54 +0000"
author: "Justin John"
feed_url: "https://aws.amazon.com/blogs/database/category/database/amazon-neptune/feed/"
---
<p>Neo4j and <a href="https://aws.amazon.com/neptune/" rel="noopener noreferrer" target="_blank">Amazon Neptune</a> are both graph databases designed for online, transactional graph workloads that support the labeled property graph data model. Migrating from Neo4j to Neptune introduces several challenges, including differences in query language behavior, architectural design, and the complexity of data migration itself. Ensuring that existing Cypher queries continue to function as expected is a critical part of this process, because variations in supported syntax and semantics can create compatibility gaps.</p> 
<p>To help teams address these issues early and streamline their overall migration journey, dedicated tools and guidance are available for validating Cypher compatibility before and during the transition. For more information about validating Cypher query compatibility when migrating from Neo4j to Neptune, see <a href="https://aws.amazon.com/blogs/database/validate-neo4j-cypher-queries-for-amazon-neptune-migration/">Validate Neo4j Cypher queries for Amazon Neptune migration</a>.</p> 
<p>In this post, we walk you through two methods to automate your Neo4j database to Neptune using the <a href="https://github.com/awslabs/amazon-neptune-tools/tree/master/neo4j-to-neptune" rel="noopener" target="_blank">neo4j-to-neptune utility</a>. This tool offers a fully automated end-to-end process in addition to a step-by-step manual process.</p> 
<h2>Solution overview</h2> 
<p>By demonstrating the effective use of both methods, we aim to provide you with a comprehensive understanding of how to use automated tools to suit your specific migration needs and successfully transition your graph-based workloads to the Neptune service.</p> 
<p>The migration process generally includes the following main components:</p> 
<ol> 
 <li>General information: <a href="https://docs.aws.amazon.com/neptune/latest/userguide/migrating-from-neo4j-general.html" rel="noopener noreferrer" target="_blank">Understanding the fundamental differences between Neo4j and Neptune</a></li> 
 <li>Preparation phase: <a href="https://docs.aws.amazon.com/neptune/latest/userguide/preparing-to-migrate-from-neo4j.html" rel="noopener noreferrer" target="_blank">Planning and preparing for the migration</a></li> 
 <li>Infrastructure provisioning: <a href="https://docs.aws.amazon.com/neptune/latest/userguide/migration-provisioning-infrastructure.html" rel="noopener noreferrer" target="_blank">Setting up the necessary Neptune infrastructure</a></li> 
 <li>Data migration: <a href="https://docs.aws.amazon.com/neptune/latest/userguide/migration-data-migration.html" rel="noopener noreferrer" target="_blank">Moving data from Neo4j to Neptune</a></li> 
 <li>Compatibility considerations: <a href="https://docs.aws.amazon.com/neptune/latest/userguide/migration-compatibility.html" rel="noopener noreferrer" target="_blank">Understanding feature compatibility between Neo4j and Neptune</a></li> 
 <li>Cypher query rewrites: <a href="https://docs.aws.amazon.com/neptune/latest/userguide/migration-opencypher-rewrites.html" rel="noopener noreferrer" target="_blank">Adapting Cypher queries to work with the Neptune openCypher implementation</a></li> 
</ol> 
<p>The following architecture shows the building blocks that you need to complete the migration process:</p> 
<p><img alt="Figure 1: Architecture diagram showing the migration process components" class="size-full wp-image-68264 aligncenter" height="365" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2026/01/18/DBBLOG-51231.png" width="703" /></p> 
<ol> 
 <li>An <a href="https://aws.amazon.com/ec2" rel="noopener noreferrer" target="_blank">Amazon Elastic Compute Cloud</a> (Amazon EC2) instance to download and install the neo4j-to-neptune utility. This instance acts both as the temporary server for staging CSV files and as a client to run <a href="https://aws.amazon.com/cli/" rel="noopener noreferrer" target="_blank">AWS Command Line Interface</a> (AWS CLI) commands, such as copying exported files to an&nbsp;<a href="https://aws.amazon.com/s3" rel="noopener noreferrer" target="_blank">Amazon Simple Storage Service</a> (Amazon S3)&nbsp;bucket and loading data into Neptune.</li> 
 <li>An S3 bucket from which to load data into Neptune.</li> 
 <li>A <a href="https://docs.aws.amazon.com/neptune/latest/userguide/feature-overview-db-clusters.html" rel="noopener noreferrer" target="_blank">Neptune DB cluster</a>&nbsp;with one graph database instance.</li> 
</ol> 
<h2>Prerequisites</h2> 
<p>Before starting the migration, ensure you have the following resources:</p> 
<ul> 
 <li><a href="https://docs.aws.amazon.com/cli/v1/userguide/cli-chap-configure.html" rel="noopener noreferrer" target="_blank">AWS Identity and Access Management (IAM)</a> credentials configured for the AWS CLI.</li> 
 <li>An S3 bucket in the same AWS Region as your Neptune cluster. See <a href="https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load.html" rel="noopener noreferrer" target="_blank">Using the Amazon Neptune bulk loader to ingest data</a> to learn more about bulk loading data to Neptune Cluster.</li> 
 <li>An <a href="https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load-tutorial-IAM-CreateRole.html" rel="noopener noreferrer" target="_blank">AWS Identity and Access Management (IAM) role</a> with appropriate permissions for Amazon S3 and Neptune.</li> 
 <li>A <a href="https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load-tutorial-IAM-add-role-cluster.html" rel="noopener noreferrer" target="_blank">Neptune database cluster</a>.</li> 
 <li>Add the IAM role to a Neptune cluster.</li> 
 <li>Neo4j with APOC library <a href="https://neo4j.com/docs/apoc/current/installation/" rel="noopener noreferrer" target="_blank">installed</a>.</li> 
 <li><a href="https://docs.oracle.com/en/java/javase/17/install/overview-jdk-installation.html#GUID-8677A77F-231A-40F7-98B9-1FD0B48C346A__INSTALLINGTHEJDKANDJREONLINUX-E04E90B9" rel="noopener noreferrer" target="_blank">Java version 17 (jdk-17.0.12) or later</a>.</li> 
 <li>Build the neo4j-to-neptune utility using the following process.</li> 
</ul> 
<h3>Build the neo4j-to-neptune utility</h3> 
<p>You will build the neo4j-to-neptune utility using the source code from <a href="https://github.com/awslabs/amazon-neptune-tools/tree/master/neo4j-to-neptune" rel="noopener noreferrer" target="_blank">GitHub</a>. The process involves cloning the Neptune tools repository from GitHub to create a local copy on the EC2 instance, which contains various utilities for working with Neptune, including the neo4j-to-neptune conversion tool. After cloning the GitHub repository, you use maven commands (<code>mvn clean install</code>) to build the utility, which cleans previous compilations, compiles the code, runs tests, and creates an executable JAR file while installing the package in your local maven <a href="https://github.com/awslabs/amazon-neptune-tools/tree/master/neo4j-to-neptune" rel="noopener noreferrer" target="_blank">repository</a>. Set the <code>JAVA_HOME</code> to the JDK17 version you installed on the EC2 instance.</p> 
<ol> 
 <li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-ssh.html" rel="noopener noreferrer" target="_blank">Connect to an EC2</a> instance in your AWS account to build the neo4j-to-neptune utility. Note that using an EC2 instance isn’t mandatory—any Linux environment with the necessary dependencies is sufficient for building this utility.</li> 
 <li><a href="https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository" rel="noopener noreferrer" target="_blank">Clone</a> then GitHub repository. 
  <div class="hide-language"> 
   <pre><code class="lang-code">git clone https://github.com/awslabs/amazon-neptune-tools.git</code></pre> 
  </div> </li> 
 <li>Set the <code>JAVA_HOME</code> to the JDK17 version.</li> 
 <li>Build the utility using Maven. 
  <div class="hide-language"> 
   <pre><code class="lang-code">cd neo4j-to-neptune
mvn clean install

[WARNING] jackson-core-2.20.0-rc1.jar, jackson-databind-2.20.0-rc1.jar define 1 overlappping classes: 
[WARNING]   - META-INF.versions.9.module-info
[WARNING] maven-shade-plugin has detected that some .class files
[WARNING] are present in two or more JARs. When this happens, only
[WARNING] one single version of the class is copied in the uberjar.
[WARNING] Usually this is not harmful and you can skeep these
[WARNING] warnings, otherwise try to manually exclude artifacts
[WARNING] based on mvn dependency:tree -Ddetail=true and the above
[WARNING] output
[WARNING] See https://docs.codehaus.org/display/MAVENUSER/Shade+Plugin
[INFO] Replacing /home/ec2-user/neptune/neo4j-to-neptune/amazon-neptune-tools/neo4j-to-neptune/target/neo4j-to-neptune.jar with /home/ec2-user/neptune/neo4j-to-neptune/amazon-neptune-tools/neo4j-to-neptune/target/neo4j-to-neptune-1.0-SNAPSHOT-shaded.jar
[INFO] 
[INFO] --- maven-install-plugin:2.5.1:install (default-install) @ neo4j-to-neptune ---
Downloading from central: https://repo.maven.apache.org/maven2/commons-codec/commons-codec/1.6/commons-codec-1.6.pom
Downloaded from central: https://repo.maven.apache.org/maven2/commons-codec/commons-codec/1.6/commons-codec-1.6.pom (11 kB at 1.6 MB/s)
Downloading from central: https://repo.maven.apache.org/maven2/org/apache/maven/shared/maven-shared-utils/0.4/maven-shared-utils-0.4.pom
Downloaded from central: https://repo.maven.apache.org/maven2/org/apache/maven/shared/maven-shared-utils/0.4/maven-shared-utils-0.4.pom (4.0 kB at 337 kB/s)
Downloading from central: https://repo.maven.apache.org/maven2/commons-codec/commons-codec/1.6/commons-codec-1.6.jar
Downloading from central: https://repo.maven.apache.org/maven2/org/apache/maven/shared/maven-shared-utils/0.4/maven-shared-utils-0.4.jar
Downloaded from central: https://repo.maven.apache.org/maven2/org/apache/maven/shared/maven-shared-utils/0.4/maven-shared-utils-0.4.jar (155 kB at 17 MB/s)
Downloaded from central: https://repo.maven.apache.org/maven2/commons-codec/commons-codec/1.6/commons-codec-1.6.jar (233 kB at 11 MB/s)
[INFO] Installing /home/ec2-user/neptune/neo4j-to-neptune/amazon-neptune-tools/neo4j-to-neptune/target/neo4j-to-neptune-1.0-SNAPSHOT.jar to /home/ec2-user/.m2/repository/com/amazonaws/neo4j-to-neptune/1.0-SNAPSHOT/neo4j-to-neptune-1.0-SNAPSHOT.jar
[INFO] Installing /home/ec2-user/neptune/neo4j-to-neptune/amazon-neptune-tools/neo4j-to-neptune/pom.xml to /home/ec2-user/.m2/repository/com/amazonaws/neo4j-to-neptune/1.0-SNAPSHOT/neo4j-to-neptune-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 15.259 s
[INFO] Finished at: 2025-08-08T21:25:55Z
[INFO] Final Memory: 35M/265M
[INFO] ------------------------------------------------------------------------</code></pre> 
  </div> </li> 
 <li>Maven will build the JAR file under the <code>target</code> folder. You can copy the JAR file to any directory you prefer. 
  <div class="hide-language"> 
   <pre><code class="lang-code">cd target
ls -ls neo4j-to-neptune.jar</code></pre> 
  </div> </li> 
 <li>Copy the&nbsp;<code>neo4j-to-neptune.jar</code>&nbsp;file to the directory containing the CSV files exported from Neo4j. The CSV export process will be detailed in a subsequent section of this post.</li> 
</ol> 
<p>For this post, we use the <a href="https://github.com/krlawrence/graph/tree/main/sample-data" rel="noopener noreferrer" target="_blank">air-routes graph dataset</a>, which models the global airline network as a graph structure. This dataset represents airports, countries, continents, and their interconnecting routes. The graph employs distinct vertex types to denote different entity categories, while edges—annotated with labels and properties—capture the relationships between these entities:</p> 
<ul> 
 <li>It contains 3,748 airports</li> 
 <li>It includes 57,645 routes between these airports</li> 
 <li>The data is available in GraphML format as <a href="https://github.com/krlawrence/graph/tree/main/sample-data" rel="noopener noreferrer" target="_blank">air-routes.graphml</a></li> 
</ul> 
<h2>Manual migration process</h2> 
<p>The manual migration process from Neo4j to Neptune consists of following steps:</p> 
<ol> 
 <li>Export Neo4j graph data into a CSV file using the APOC export procedures.</li> 
 <li>Convert the CSV file to Neptune format</li> 
 <li>Upload the converted vertices and edges file to an S3 bucket.</li> 
 <li>Bulk load to Neptune.</li> 
</ol> 
<h3>Export Neo4j graph data into a CSV file using the APOC export procedures</h3> 
<p>To export data from Neo4j using APOC procedures, you first need to install the APOC library in your Neo4j environment. See <a href="https://neo4j.com/docs/apoc/current/installation/" rel="noopener noreferrer" target="_blank">Installation</a> to install the APOC library. Then, you must enable exports by adding&nbsp;<code>apoc.export.file.enabled=true</code>&nbsp;to your <code>neo4j.conf</code> configuration file. Note that this requires a complete shutdown and restart of the Neo4j instance. The actual export is performed using the&nbsp;<code>apoc.export.csv.all</code>&nbsp;procedure, which creates a single CSV file containing all nodes and relationships data.</p> 
<p>It’s important to note that when executing export command, you should avoid using the&nbsp;<code>{stream:true}</code>&nbsp;parameter, because streaming the results to the browser and downloading them as a CSV file will result in a file that won’t be correctly processed by the conversion utility. The export file path is resolved relative to the Neo4j import directory.</p> 
<h3>Enable exports in neo4j.conf</h3> 
<p>Add the following line to enable exports to the <code>neo4jc.conf</code> file:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">apoc.export.file.enabled=true</code></pre> 
</div> 
<h3>Methods for running a Neo4j APOC CSV export query</h3> 
<p>There are several methods that you can use to run a Neo4j APOC CSV export query.</p> 
<ul> 
 <li>Neo4j browser: 
  <div class="hide-language"> 
   <pre><code class="lang-sql">CALL apoc.export.csv.all(
  "neo4j-export.csv",
  {d:','}
)</code></pre> 
  </div> </li> 
 <li>Paste and execute the preceding code in the Neo4j browser interface</li> 
 <li>Most straightforward for quick testing</li> 
 <li>Provides visual feedback</li> 
 <li>Command Line (cypher-shell): 
  <div class="hide-language"> 
   <pre><code class="lang-code">cypher-shell -u neo4j -p password "CALL apoc.export.csv.all('neo4j-export.csv', {d:','})"</code></pre> 
  </div> </li> 
 <li>Useful for automation and scripts</li> 
 <li>Requires cypher-shell to be installed</li> 
 <li>Can be integrated into shell scripts</li> 
 <li>Neo4j admin tool: 
  <div class="hide-language"> 
   <pre><code class="lang-code">neo4j-admin database execute --file=export-query.cypher</code></pre> 
  </div> </li> 
 <li>Create a file containing the query</li> 
 <li>Good for batch operations</li> 
 <li>Provides administrative level access</li> 
 <li>Rest API: 
  <div class="hide-language"> 
   <pre><code class="lang-code">POST https://localhost:7474/db/neo4j/tx/commit
Content-Type: application/json
{
  "statements": [{
    "statement": "CALL apoc.export.csv.all('neo4j-export.csv', {d:','})"
  }]
}</code></pre> 
  </div> </li> 
 <li>HTTP-based approach</li> 
 <li>Useful for remote execution</li> 
 <li>Platform-independent</li> 
</ul> 
<h3>Managing multi-valued property migration from Neo4j to Neptune</h3> 
<p>Neo4j allows&nbsp;<a href="https://neo4j.com/docs/cypher-manual/current/syntax/values/" rel="noopener noreferrer" target="_blank">homogeneous lists of basic types</a>&nbsp;to be stored as properties on both nodes and edges. These lists can contain duplicate values.</p> 
<p>When migrating data from Neo4j to Neptune, handling multi-valued properties requires special consideration because of differences in how these databases manage property values. Neptune has different constraints—it supports set and single cardinality for vertex properties, but only single cardinality for edge properties. This creates challenges when migrating Neo4j properties containing duplicate values to Neptune.</p> 
<p>To manage this migration, two key parameters are available:</p> 
<ul> 
 <li>Node property policy (<code>--node-property-policy</code>):</li> 
 <li><code>LeaveAsString</code>: Preserves multi-valued properties as JSON-formatted list strings</li> 
 <li><code>Halt</code>: Stops migration if multi-valued properties are found</li> 
 <li><code>PutInSetIgnoringDuplicates</code> (default): Converts to Neptune set properties, removing duplicates</li> 
 <li><code>PutInSetButHaltIfDuplicates</code>: Converts to set properties but stops if duplicates are found</li> 
 <li>Relationship property policy (<code>--relationship-property-policy</code>):</li> 
 <li><code>LeaveAsString</code> (default): Stores multi-valued properties as JSON-formatted list strings</li> 
 <li><code>Halt</code>: Stops migration if multi-valued properties are encountered</li> 
 <li>These parameters provide flexibility in handling property migrations while maintaining data integrity according to your specific requirements.</li> 
</ul> 
<p>In this example, you use the Neo4j browser tool to run the <code>apoc.export.csv.all</code> command to export all the vertices and edges to a CSV file. This Neo4j database has 3,784 nodes (3,504 airports, 273 countries,7 continents) and 57,645 edges.</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">CALL apoc.export.csv.all(
 "neo4j-export.csv",
 {d:','}
)</code></pre> 
</div> 
<p>The export command generates a comma separated export file that contains all nodes and edges of the air routes database in the Neo4j database. The following snippet shows an example of the export file.</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">"_id","_labels","city","code","country","desc","elev","icao","id","label","lat","lon","longest","region","runways","type","_start","_end","_type","dist"
"0",":airport","Atlanta","ATL","US","KATL","1026","KATL","1","airport","33.6366996765137","-84.4281005859375","12390","US-GA","5","airport",,,,
"1",":airport","Anchorage","ANC","US","PANC","151","PANC","2","airport","61.1744003295898","-149.996002197266","12400","US-AK","3","airport",,,,
"2",":airport","Austin","AUS","US","KAUS","542","KAUS","3","airport","30.1944999694824","-97.6698989868164","12250","US-TX","2","airport",,,,
"3",":airport","Nashville","BNA","US","KBNA","599","KBNA","4","airport","36.1245002746582","-86.6781997680664","11030","US-TN","4","airport",,,,
"4",":airport","Boston","BOS","US","KBOS","19","KBOS","5","airport","42.36429977","-71.00520325","10083","US-MA","6","airport",,,,
"5",":airport","Baltimore","BWI","US","KBWI","143","KBWI","6","airport","39.17539978","-76.66829681","10502","US-MD","3","airport",,,,
"6",":airport","Washington D.C.","DCA","US","KDCA","14","KDCA","7","airport","38.8521003723145","-77.0376968383789","7169","US-DC","3","airport",,,,
"7",":airport","Dallas","DFW","US","KDFW","607","KDFW","8","airport","32.896800994873","-97.0380020141602","13401","US-TX","7","airport",,,,
:::
,,,,,,,,,,,,,,,,"0","2","route","809"
,,,,,,,,,,,,,,,,"0","3","route","214"
,,,,,,,,,,,,,,,,"0","4","route","945"
,,,,,,,,,,,,,,,,"0","5","route","576"
,,,,,,,,,,,,,,,,"0","6","route","546"
,,,,,,,,,,,,,,,,"0","7","route","729"</code></pre> 
</div> 
<p>This format isn’t directly supported by the Neptune bulk loader, so you will use the neo4j-to-neptune utility to convert the exported CSV file into a format that can be directly imported into the Neptune database.</p> 
<h3>Convert the generated CSV file into a Neptune-supported format</h3> 
<p>Now that you have the data in an exported CSV file, you need to convert the file to a format that’s supported by Neptune.</p> 
<ol> 
 <li>Upload the CSV file exported from Neo4j to the EC2 instance you configured.</li> 
 <li>Use the neo4j-to-neptune tool to convert the CSV files into a format compatible with the Neptune bulk loader. 
  <div class="hide-language"> 
   <pre><code class="lang-code">java -jar neo4j-to-neptune.jar convert-csv -i ./neo4j-airroutes-export.csv -d output --infer-types
Vertices: 3784
Edges   : 57645
Output  : output/1754689143727
output/1754689143727
&nbsp;
Completed in 1 second(s)</code></pre> 
  </div> </li> 
</ol> 
<p>The neo4j-to-neptune tool generates the output as two files under the <code>output/1754689143727</code> directory</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">ls -ltr output/1754689143727
total 3512
-rw-rw-r-- 1 ec2-user ec2-user  394459 Aug  8 21:39 vertices.csv
-rw-rw-r-- 1 ec2-user ec2-user 3195859 Aug  8 21:39 edges.csv</code></pre> 
</div> 
<p>The <code>vertices.csv</code> file:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">~id,~label,city:string,code:string,country:string,desc:string,elev:short,icao:string,id:short,label:string,lat:double,lon:double,longest:short,region:string,runways:byte,type:string
&nbsp;
0,airport,Atlanta,ATL,US,KATL,1026,KATL,1,airport,33.6366996765137,-84.4281005859375,12390,US-GA,5,airport
1,airport,Anchorage,ANC,US,PANC,151,PANC,2,airport,61.1744003295898,-149.996002197266,12400,US-AK,3,airport
2,airport,Austin,AUS,US,KAUS,542,KAUS,3,airport,30.1944999694824,-97.6698989868164,12250,US-TX,2,airport
3,airport,Nashville,BNA,US,KBNA,599,KBNA,4,airport,36.1245002746582,-86.6781997680664,11030,US-TN,4,airport
4,airport,Boston,BOS,US,KBOS,19,KBOS,5,airport,42.36429977,-71.00520325,10083,US-MA,6,airport
5,airport,Baltimore,BWI,US,KBWI,143,KBWI,6,airport,39.17539978,-76.66829681,10502,US-MD,3,airport</code></pre> 
</div> 
<p>The <code>edges.csv</code> file:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">~id,~from,~to,~label,dist:short
e8192f62-d3e1-4e60-b8c8-00f674aa916d,0,2,route,809
d8c5ef77-ee84-4ebc-9b37-cdfe46e369fe,0,3,route,214
74ecb38f-4060-4bd6-94d5-c76717ff0d64,0,4,route,945</code></pre> 
</div> 
<p>Output generated by the conversion utility has converted the Neo4j output file into a format supported by the <a href="https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load.html" rel="noopener noreferrer" target="_blank">Neptune bulk loader</a> by adding an appropriate header with right comma separated nodes and edges. You are now ready to begin the process to import the data using the Neptune bulk loader. See <a href="https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load-optimize.html" rel="noopener noreferrer" target="_blank">Optimizing an Amazon Neptune bulk load</a> to get the best performance during large bulk load operations.</p> 
<h3>Import the converted nodes and edges to Neptune database</h3> 
<p>Here are the high-level steps of the loading process:</p> 
<ol> 
 <li><a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html" rel="noopener noreferrer" target="_blank">Copy</a> the data files to an S3 bucket.</li> 
 <li>Initiate a Neptune bulk load request by sending an HTTP request to the bulk loader API, passing the source file path in Amazon S3, and the bulk loader IAM role created in the prerequisites section.</li> 
</ol> 
<h3>Load vertices</h3> 
<p>From a command line window, enter the following to run the Neptune loader, using the correct values for your endpoint, S3 URI or path, format, and IAM role Amazon Resource Name (ARN). The&nbsp;<code>format</code>&nbsp;parameter can be any of the following values:&nbsp;<code>csv</code>&nbsp;for Gremlin,&nbsp;<code>opencypher</code>&nbsp;for openCypher, or&nbsp;<code>ntriples</code>,&nbsp;<code>nquads</code>,&nbsp;<code>turtle</code>, or&nbsp;<code>rdfxml</code>&nbsp;for RDF. For information about the other parameters, see&nbsp;<a href="https://docs.aws.amazon.com/neptune/latest/userguide/load-api-reference-load.html" rel="noopener noreferrer" target="_blank">Neptune Loader Command</a>. The following command initiates a bulk load operation to load the vertices (nodes) data into the Neptune database using the Neptune loader API.</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">aws neptunedata start-loader-job \
  --source s3://neo4j-to-neptune-bucket/vertices.csv \
  --format csv \
  --s3-bucket-region us-east-1 \
  --iam-role-arn arn:aws:iam::XXXXXXXXXXX:role/NeptuneLoadFromS3 \
  --fail-on-error \
  --parallelism OVERSUBSCRIBE \
  --queue-request \
  --endpoint-url https://db-neptune.cluster-XXXXXXXXX.us-east-1.neptune.amazonaws.com:8182
{
    "status" : "200 OK",
    "payload" : {
        "loadId" : "58eecccd-dd68-45b0-87ec-1a63f98a0aa1"
    }</code></pre> 
</div> 
<p>The response returns a unique&nbsp;<code>loadId</code>&nbsp;that identifies this bulk load operation. Use this identifier to monitor the load status with the following command:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">aws neptunedata get-loader-job-status \
   --load-id 58eecccd-dd68-45b0-87ec-1a63f98a0aa1 \
   --endpoint-url https://db-neptune.cluster-XXXXXXXXX.us-east-1.neptune.amazonaws.com:8182
{
    "status" : "200 OK",
    "payload" : {
        "feedCount" : [
            {
                "LOAD_COMPLETED" : 1
            }
        ],
        "overallStatus" : {
            "fullUri" : "s3://neo4j-to-neptune-bucket/vertices.csv",
            "runNumber" : 1,
            "retryNumber" : 0,
            "status" : "LOAD_COMPLETED",
            "totalTimeSpent" : 6,
            "startTime" : 1754924929,
            "totalRecords" : 54024,
            "totalDuplicates" : 0,
            "parsingErrors" : 0,
            "datatypeMismatchErrors" : 0,
            "insertErrors" : 0
        }
    }
}</code></pre> 
</div> 
<p>Let’s break down the components:</p> 
<ul> 
 <li>Command structure: 
  <ul> 
   <li>Uses curl with <code>-G</code> flag (<code>GET</code> request).</li> 
   <li>Queries the Neptune loader endpoint with a specific load ID (<code>58eecccd-dd68-45b0-87ec-1a63f98a0aa1</code>).</li> 
  </ul> </li> 
 <li>Response details: 
  <ul> 
   <li><code>fullUri</code>: Source S3 location of the vertices.csv</li> 
   <li><code>filerunNumber</code>: Current run number (1)</li> 
   <li><code>retryNumber</code>: Number of retries (0)</li> 
   <li><code>status</code>: Final status “LOAD_COMPLETED”</li> 
   <li><code>totalTimeSpent</code>: 6 seconds</li> 
   <li><code>startTime</code>: Unix timestamp of when the load started</li> 
   <li><code>totalRecords</code>: 54,024 records processed</li> 
   <li><code>totalDuplicates</code>: 0 duplicate records found</li> 
   <li><code>parsingErrors</code>: 0 parsing errors</li> 
   <li><code>datatypeMismatchErrors</code>: 0 data type mismatches</li> 
   <li><code>insertErrors</code>: 0 insertion errors</li> 
  </ul> </li> 
</ul> 
<p>This response indicates a successful load operation with no errors, where all 54,024 records from the <code>vertices.csv</code> file were successfully loaded into the Neptune database.</p> 
<h3>Load edges</h3> 
<p>The following command to initiates a bulk load operation to load the relationships (edges) data into the Neptune database using the Neptune loader API.</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">aws neptunedata start-loader-job \
  --source s3://neo4j-to-neptune-bucket/edges.csv \
  --format csv \
  --s3-bucket-region us-east-1 \
  --iam-role-arn arn:aws:iam::XXXXXXXXXXX:role/NeptuneLoadFromS3 \
  --fail-on-error \
  --parallelism OVERSUBSCRIBE \
  --queue-request \
  --endpoint-url https://db-neptune.cluster-XXXXXXXXX.us-east-1.neptune.amazonaws.com:8182
{
    "status" : "200 OK",
    "payload" : {
        "loadId" : "3d58fe29-34f8-4c3b-b067-7c648447560a"
    }
}</code></pre> 
</div> 
<p>The response returns a unique&nbsp;<code>loadId</code>&nbsp;that identifies this bulk load operation. Use this identifier to monitor the load status with the following command:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">aws neptunedata get-loader-job-status \
   --load-id 3d58fe29-34f8-4c3b-b067-7c648447560a \
   --endpoint-url https://db-neptune.cluster-XXXXXXXXX.us-east-1.neptune.amazonaws.com:8182
{
    "status" : "200 OK",
    "payload" : {
        "feedCount" : [
            {
                "LOAD_COMPLETED" : 1
            }
        ],
        "overallStatus" : {
            "fullUri" : "s3://neo4j-to-neptune-bucket/edges.csv",
            "runNumber" : 1,
            "retryNumber" : 0,
            "status" : "LOAD_COMPLETED",
            "totalTimeSpent" : 12,
            "startTime" : 1754925120,
            "totalRecords" : 108282,
            "totalDuplicates" : 0,
            "parsingErrors" : 0,
            "datatypeMismatchErrors" : 0,
            "insertErrors" : 0
        }
    }
}</code></pre> 
</div> 
<p>Verify the loaded data by querying the Neptune database. You can use <a href="https://docs.aws.amazon.com/neptune/latest/userguide/graph-notebooks.html" rel="noopener noreferrer" target="_blank">Neptune Jupyter Notebooks</a> or the AWS CLI and Data API to run cypher commands to query the number of nodes and edges.</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">aws neptunedata execute-open-cypher-query \
   --open-cypher-query "MATCH (n) WITH count(n) as nodeCount MATCH ()-[r]-&gt;() WITH nodeCount, count(r) as edgeCount RETURN nodeCount, edgeCount " \
   --endpoint-url https://db-neptune-neo- XXXXXXXXX.us-east-1.neptune.amazonaws.com:8182
&nbsp;
{
  "results": [
    {
      "nodeCount": 3784,
      "edgeCount": 57645
    }
  ]
}</code></pre> 
</div> 
<h2>Automated migration process</h2> 
<p>The neo4j-to-neptune utility supports an automated end-to-end process for migrating data from Neo4j to Neptune, which consists of two main steps:</p> 
<ol> 
 <li>Export the CSV files from Neo4j using the APOC export procedures.</li> 
 <li>Convert and load data to Neptune&nbsp;by transforming the CSV files into Neptune-supported format and performing a bulk load operation.</li> 
</ol> 
<p>The&nbsp;<code>convert-csv</code>&nbsp;command supports an integrated bulk loading option that automatically uploads converted CSV files to Amazon S3 and initiates the Neptune bulk load process. You will use the CSV file that you exported in the previous section to demonstrate the automated conversion and bulk load process. There are two options to pass parameters to the <code>neo4j-to-neptune.jar</code> file:</p> 
<ul> 
 <li>Pass parameters as CLI parameters</li> 
 <li>Pass parameters as a YAML file</li> 
</ul> 
<h3>Option 1: Automated conversion and bulk load using CLI parameters</h3> 
<p>You can perform automated conversion and bulk loading using the following neo4j-to-neptune utility command with CLI parameters:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">java -jar neo4j-to-neptune.jar convert-csv \
   -i neo4j-airroutes-export.csv \
   -d output \
   --s3-prefix neptune-data \
   --parallelism OVERSUBSCRIBE \
   --neptune-endpoint db-neptune-neo-migrate.cluster-clustername.us-east-1.neptune.amazonaws.com \
   --bucket-name neo4j-to-neptune-bucket \
   --iam-role-arn arn:aws:iam::XXXXXXXXXXXX:role/NeptuneLoadFromS3 \
   --infer-types</code></pre> 
</div> 
<p>Let’s break down each parameter:</p> 
<ol> 
 <li><code>java -jar neo4j-to-neptune.jar convert-csv</code>: Executes the conversion utility</li> 
 <li>Command parameters: 
  <ul> 
   <li><code>-i neo4j-airroutes-export.csv</code>: Input file containing the Neo4j exported data</li> 
   <li><code>-d output</code>: Directory where converted files will be stored</li> 
   <li><code>--s3-prefix neptune-data</code>: Prefix for S3 bucket organization</li> 
   <li><code>--parallelism OVERSUBSCRIBE</code>: Sets maximum parallelism for bulk loading</li> 
   <li><code>--neptune-endpoint</code>: Specifies the Neptune cluster endpoint URL</li> 
   <li><code>--bucket-name neo4j-to-neptune-bucket</code>: S3 bucket where converted files will be uploaded</li> 
   <li><code>--iam-role-arn</code>: IAM role ARN that has permissions for S3 and Neptune access</li> 
   <li><code>--infer-types</code>: Automatically infers data types for properties</li> 
  </ul> </li> 
</ol> 
<p>The utility will:</p> 
<ol> 
 <li>Convert the Neo4j CSV format to Neptune format.</li> 
 <li>Automatically upload the converted files to the specified S3 bucket.</li> 
 <li>Initiate a Neptune bulk load operation.</li> 
 <li>Monitor the load progress if monitoring is enabled.</li> 
</ol> 
<div class="hide-language"> 
 <pre><code class="lang-code">/home/ec2-user/java17/jdk-17.0.12/bin/java -jar neo4j-to-neptune.jar convert-csv \
&gt;   -i neo4j-airroutes-export.csv \
&gt;   -d output \
&gt;   --s3-prefix neptune-data \
&gt;   --parallelism OVERSUBSCRIBE \
&gt;   --neptune-endpoint db-neptune-neo-migrate.cluster-c25vmpy64auu.us-east-1.neptune.amazonaws.com \
&gt;   --bucket-name neo4j-to-neptune-bucket \
&gt;   --iam-role-arn arn:aws:iam::XXXXXXXXXXXX:role/NeptuneLoadFromS3 \
&gt;   --infer-types
Vertices: 3784
Edges   : 57645
Output  : output/1755284203609
output/1755284203609
&nbsp;
Completed in 1 second(s)
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
S3 Bucket: neo4j-to-neptune-migrate
S3 Prefix: neptune-data
AWS Region: us-east-1
IAM Role ARN: arn:aws:iam::606347354142:role/NeptuneLoadFromS3
Neptune Endpoint: db-neptune-neo-migrate.cluster-c25vmpy64auu.us-east-1.neptune.amazonaws.com
Bulk Load Parallelism: OVERSUBSCRIBE
Bulk Load Monitor: false
Uploading Gremlin load data to S3...
Starting sequential upload of files from /home/ec2-user/neptune/neo4j-to-neptune_old/amazon-neptune-tools/neo4j-to-neptune/neo4jtocsv/output/1755284203609 to s3://neo4j-to-neptune-migrate/neptune-data/1755284203609
Uploading file 1 of 2: vertices.csv
Starting async upload of /home/ec2-user/neptune/neo4j-to-neptune_old/amazon-neptune-tools/neo4j-to-neptune/neo4jtocsv/output/1755284203609/vertices.csv to s3://neo4j-to-neptune-migrate/neptune-data/1755284203609/vertices.csv
File size: 394459 Bytes
Using S3 Transfer Manager for upload...
Initiating Transfer Manager upload...
Successfully uploaded vertices.csv using Transfer Manager - ETag: "b6112e54a4f8041c42371073cdddd80c"
Successfully uploaded vertices.csv (1/2)
Uploading file 2 of 2: edges.csv
Starting async upload of /home/ec2-user/neptune/neo4j-to-neptune_old/amazon-neptune-tools/neo4j-to-neptune/neo4jtocsv/output/1755284203609/edges.csv to s3://neo4j-to-neptune-migrate/neptune-data/1755284203609/edges.csv
File size: 3195859 Bytes
Using S3 Transfer Manager for upload...
Initiating Transfer Manager upload...
Successfully uploaded edges.csv using Transfer Manager - ETag: "161d448d860ff177741aff39dd56c773"
Successfully uploaded edges.csv (2/2)
Successfully uploaded all 2 files sequentially
Files uploaded successfully to S3. Files available at: s3://neo4j-to-neptune-migrate/neptune-data/1755284203609/
Starting Neptune bulk load...
Testing connectivity to Neptune endpoint...
Successful connected to Neptune. Status: 200 healthy
Neptune bulk load started successfully! Load ID: 31e1c3dc-cb21-4143-a0da-437094a6aa09</code></pre> 
</div> 
<p>The response returns a unique&nbsp;load ID that identifies this bulk load operation. Use this identifier to monitor the load status with the following command:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">aws neptunedata get-loader-job-status \
   --load-id 31e1c3dc-cb21-4143-a0da-437094a6aa09 \
   --endpoint-url https://db-neptune.cluster-XXXXXXXXX.us-east-1.neptune.amazonaws.com:8182
{
    "status" : "200 OK",
    "payload" : {
        "feedCount" : [
            {
                "LOAD_COMPLETED" : 2
            }
        ],
        "overallStatus" : {
            "fullUri" : "s3://neo4j-to-neptune-bucket/neptune-data/1755284203609/",
            "runNumber" : 1,
            "retryNumber" : 0,
            "status" : "LOAD_COMPLETED",
            "totalTimeSpent" : 14,
            "startTime" : 1755284207,
            "totalRecords" : 162306,
            "totalDuplicates" : 0,
            "parsingErrors" : 0,
            "datatypeMismatchErrors" : 0,
            "insertErrors" : 0
        }
    }
}</code></pre> 
</div> 
<p>Let’s analyze the response:</p> 
<ul> 
 <li>Command structure: 
  <ul> 
   <li>Uses curl with <code>-G</code> flag to make a <code>GET</code> request.</li> 
   <li>Queries the Neptune loader endpoint with a specific load ID (<code>31e1c3dc-cb21-4143-a0da-437094a6aa09</code>).</li> 
  </ul> </li> 
 <li>Response analysis: 
  <ul> 
   <li><code>fullUri</code>: Shows S3 location of the loaded files</li> 
   <li><code>runNumber</code>: 1 (first run)</li> 
   <li><code>retryNumber</code>: 0 (no retries needed)</li> 
   <li><code>status</code>: <code>"LOAD_COMPLETED"</code> (successful completion)</li> 
   <li><code>totalTimeSpent</code>: 14 seconds</li> 
   <li><code>startTime</code>: Unix timestamp 1755284207</li> 
   <li><code>totalRecords</code>: 162,306 total records processed (combined vertices and edges)</li> 
   <li><code>totalDuplicates</code>: 0 (no duplicate records found)</li> 
   <li><code>parsingErrors</code>: 0 (no parsing errors)</li> 
   <li><code>datatypeMismatchErrors</code>: 0 (no data type mismatches)</li> 
   <li><code>insertErrors</code>: 0 (no insertion errors)</li> 
  </ul> </li> 
</ul> 
<p>This response indicates a completely successful load operation where both the vertices and edges files were loaded without any errors, processing a total of 162,306 records. If you have observed any error during the load operation, you can use <a href="https://docs.aws.amazon.com/neptune/latest/userguide/load-api-reference-status-examples.html#load-api-reference-status-examples-details-request" rel="noopener noreferrer" target="_blank">Neptune loader Get-Status Example</a> to get more details.</p> 
<p>You can verify the Neptune Graph by running this script in a Neptune Jupyter Notebook environment using the openCypher (<code>%%oc</code>) magic command or AWS CLI and Data API. The query performs two match operations—first counting all nodes (<code>n</code>) in the graph, then counting all relationships (<code>r</code>) between nodes. The results show that the Neptune database contains 3,748 nodes and 57,645 edges, which verifies that all the data from the Neo4j database was successfully migrated to Neptune.</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">aws neptunedata execute-open-cypher-query \
   --open-cypher-query "MATCH (n) WITH count(n) as nodeCount MATCH ()-[r]-&gt;() WITH nodeCount, count(r) as edgeCount RETURN nodeCount, edgeCount " \
   --endpoint-url https://db-neptune-neo- XXXXXXXXX.us-east-1.neptune.amazonaws.com:8182
&nbsp;
{
  "results": [
    {
      "nodeCount": 3784,
      "edgeCount": 57645
    }
  ]
}</code></pre> 
</div> 
<h3>Option 2: Automated conversion and bulk load using a YAML configuration file</h3> 
<p>Create a YAML configuration file named <code>bulk-load-config.yaml</code> with your bulk load settings.</p> 
<div class="hide-language"> 
 <pre><code class="lang-code"># S3 Configuration
bucket-name: "neo4j-to-neptune-bucket"
s3-prefix: "neptune-data"
&nbsp;
# Neptune Configuration
neptune-endpoint: "db-neptune-neo-migrate-instance-1.clustername.us-east-1.neptune.amazonaws.com "
&nbsp;
# IAM Configuration
iam-role-arn: "arn:aws:iam::XXXXXXXXXXXX:role/NeptuneLoadFromS3Role"
&nbsp;
# Performance Settings
parallelism: "OVERSUBSCRIBE"  # Options: LOW, MEDIUM, HIGH, OVERSUBSCRIBE
&nbsp;
# Monitoring
monitor: true
&nbsp;</code></pre> 
</div> 
<p>The following command uses the neo4j-to-neptune utility with a YAML configuration file approach instead of command-line parameters.</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">java -jar neo4j-to-neptune.jar convert-csv \
-i neo4j-airroutes-export.csv \
-d output --bulk-load-config bulk-load-config.yaml</code></pre> 
</div> 
<p>Let’s look at the whole process.</p> 
<p>Command components:</p> 
<ul> 
 <li><code>java -jar neo4j-to-neptune.jar convert-csv</code>: Executes the conversion utility</li> 
 <li><code>-i neo4j-airroutes-export.csv</code>: Specifies the input Neo4j export file</li> 
 <li><code>-d output</code>: Defines the output directory for converted files</li> 
 <li><code>--bulk-load-config bulk-load-config.yaml</code>: Points to a YAML configuration file</li> 
</ul> 
<p>Run the following command from the EC2 instance where the CSV file and <code>neo4j-to-neptune.jar</code> were copied:</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">/home/ec2-user/java17/jdk-17.0.12/bin/java -jar neo4j-to-neptune.jar convert-csv \
&gt;   -i neo4j-airroutes-export.csv \
&gt;   -d output \
&gt;   --bulk-load-config bulk-load-config.yaml
Vertices: 3784
Edges   : 57645
Output  : output/1755283074036
output/1755283074036
&nbsp;
Completed in 1 second(s)
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
S3 Bucket: neo4j-to-neptune-migrate
S3 Prefix: neptune-data
AWS Region: us-east-1
IAM Role ARN: arn:aws:iam::606347354142:role/NeptuneLoadFromS3
Neptune Endpoint: db-neptune-neo-migrate.cluster-c25vmpy64auu.us-east-1.neptune.amazonaws.com
Bulk Load Parallelism: OVERSUBSCRIBE
Bulk Load Monitor: true
Uploading Gremlin load data to S3...
Starting sequential upload of files from /home/ec2-user/neptune/neo4j-to-neptune_old/amazon-neptune-tools/neo4j-to-neptune/neo4jtocsv/output/1755283074036 to s3://neo4j-to-neptune-migrate/neptune-data/1755283074036
Uploading file 1 of 2: vertices.csv
Starting async upload of /home/ec2-user/neptune/neo4j-to-neptune_old/amazon-neptune-tools/neo4j-to-neptune/neo4jtocsv/output/1755283074036/vertices.csv to s3://neo4j-to-neptune-migrate/neptune-data/1755283074036/vertices.csv
File size: 394366 Bytes
Using S3 Transfer Manager for upload...
Initiating Transfer Manager upload...
Successfully uploaded vertices.csv using Transfer Manager - ETag: "829888b668436f8dca506ae843d7f64a"
Successfully uploaded vertices.csv (1/2)
Uploading file 2 of 2: edges.csv
Starting async upload of /home/ec2-user/neptune/neo4j-to-neptune_old/amazon-neptune-tools/neo4j-to-neptune/neo4jtocsv/output/1755283074036/edges.csv to s3://neo4j-to-neptune-migrate/neptune-data/1755283074036/edges.csv
File size: 3195853 Bytes
Using S3 Transfer Manager for upload...
Initiating Transfer Manager upload...
Successfully uploaded edges.csv using Transfer Manager - ETag: "445be20620b7361164b43217a6edf4e7"
Successfully uploaded edges.csv (2/2)
Successfully uploaded all 2 files sequentially
Files uploaded successfully to S3. Files available at: s3://neo4j-to-neptune-migrate/neptune-data/1755283074036/
Starting Neptune bulk load...
Testing connectivity to Neptune endpoint...
Successful connected to Neptune. Status: 200 healthy
Neptune bulk load started successfully! Load ID: 49a81941-8ed9-415c-b22c-d791e56338e4
Monitoring load progress for job: 49a81941-8ed9-415c-b22c-d791e56338e4
Neptune bulk load status: LOAD_IN_PROGRESS
Neptune bulk load status: LOAD_IN_PROGRESS
Neptune bulk load completed with status: LOAD_COMPLETED</code></pre> 
</div> 
<p>What the process includes:</p> 
<ol> 
 <li>The utility validates all bulk load parameters. If any required parameters are missing or invalid, the conversion will be aborted with a clear error message indicating which parameters are missing.</li> 
 <li>Converts the Neo4j CSV file to the Neptune Cypher format.</li> 
 <li>Automatically uploads the converted files to Amazon S3.</li> 
 <li>Initiates a Neptune bulk load job.</li> 
 <li>Monitors the progress until completion (if monitoring is enabled).</li> 
</ol> 
<p>You can verify the Neptune Graph by running this script in a Neptune Jupyter Notebook environment using the openCypher (<code>%%oc</code>) magic command or AWS CLI and Data API. The query performs two match operations—first counting all nodes (n) in the graph, then counting all relationships (r) between nodes. The results show that the Neptune graph database contains 3,748 nodes and 57,645 edges, which verifies that all the data from Neo4j was successfully migrated to Neptune.</p> 
<div class="hide-language"> 
 <pre><code class="lang-code">aws neptunedata execute-open-cypher-query \
   --open-cypher-query "MATCH (n) WITH count(n) as nodeCount MATCH ()-[r]-&gt;() WITH nodeCount, count(r) as edgeCount RETURN nodeCount, edgeCount " \
   --endpoint-url https://db-neptune-neo- XXXXXXXXX.us-east-1.neptune.amazonaws.com:8182
&nbsp;
{
  "results": [
    {
      "nodeCount": 3784,
      "edgeCount": 57645
    }
  ]
}</code></pre> 
</div> 
<h2>Clean up</h2> 
<p>To avoid incurring unnecessary charges, make sure to delete the resources you created if you no longer need them. All the following cleanup commands should be run from your local machine or EC2 instance where you have the AWS CLI installed and configured with appropriate credentials. Make sure you have the necessary permissions to delete these resources before proceeding.</p> 
<ol> 
 <li>Delete Neptune resources</li> 
 <li>First, delete your Neptune database instance: 
  <div class="hide-language"> 
   <pre><code class="lang-code">aws neptune delete-db-instance \
    --db-instance-identifier &lt;your-db-instance-identifier&gt; \
    --skip-final-snapshot </code></pre> 
  </div> </li> 
 <li>Delete your Neptune database cluster: 
  <div class="hide-language"> 
   <pre><code class="lang-code">aws neptune delete-db-cluster \
    --db-cluster-identifier &lt;your-cluster-identifier&gt; \
    --skip-final-snapshot</code></pre> 
  </div> </li> 
 <li>Delete S3 objects (data files) and bucket: 
  <div class="hide-language"> 
   <pre><code class="lang-code"># Delete Neptune data files
aws s3 rm s3://&lt;your-bucket-name&gt;/neptune-data/ --recursive
# Delete the bucket if no longer needed
aws s3 rb s3://&lt;your-bucket-name&gt; --force</code></pre> 
  </div> </li> 
 <li>Clean up IAM resources by detaching the role policies and then removing the IAM role created for the Neptune bulk load: 
  <div class="hide-language"> 
   <pre><code class="lang-code"># First detach the policies
aws iam detach-role-policy \
    --role-name NeptuneLoadFromS3 \
    --policy-arn arn:aws:iam::aws:policy/service-role/AmazonNeptuneFullAccess
&nbsp;
aws iam detach-role-policy \
    --role-name NeptuneLoadFromS3 \
    --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
&nbsp;
# Then delete the role
aws iam delete-role --role-name NeptuneLoadFromS3</code></pre> 
  </div> </li> 
 <li>If you used an EC2 instance, you can terminate the instance using the following command: 
  <div class="hide-language"> 
   <pre><code class="lang-code">aws ec2 terminate-instances --instance-ids yourinstanceid</code></pre> 
  </div> </li> 
 <li>Remove local files created during the migration process: 
  <div class="hide-language"> 
   <pre><code class="lang-code"># Remove converted CSV files
rm -rf output/
&nbsp;
# Remove the exported Neo4j CSV file if no longer needed
rm neo4j-airroutes-export.csv</code></pre> 
  </div> </li> 
</ol> 
<h2>Conclusion</h2> 
<p>The Neo4j to Neptune migration tool provides a robust and flexible solution for transferring data between these graph databases, offering multiple migration paths so that you can choose between a manual multi-step process or an automated end-to-end solution depending on your specific needs. The tool features extensive configuration options through both command-line parameters and YAML files, enabling fine-tuned control over the migration process, while sophisticated handling of multi-valued properties helps ensure data integrity with configurable policies for both nodes and relationships. Integration with the Neptune bulk loader simplifies the upload process through automatic Amazon S3 file management and load monitoring, and the tool performs validation checks before starting the conversion process to help prevent failed migrations because of misconfiguration. This utility streamlines the complex process of migrating from Neo4j to Neptune while providing the flexibility and control needed for enterprise-grade data migrations.</p> 
<p>Get started today by downloading the neo4j-to-neptune utility from the AWS GitHub repository and following either the manual or automated migration approach based on your needs. Whether you’re planning a small-scale migration or an enterprise-level transformation, the neo4j-to-neptune utility provides the flexibility and control you need for a successful migration. Visit our <a href="https://github.com/awslabs/amazon-neptune-tools/tree/master/neo4j-to-neptune" rel="noopener noreferrer" target="_blank">GitHub</a> repository to access the migration tools, begin your transition to Neptune, and experience the benefits of the fully managed graph database service it provides.</p> 
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
