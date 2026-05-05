---
title: "4.7 times better write query price-performance with AWS Graviton4 R8g instances using Amazon Neptune v1.4.5"
url: "https://aws.amazon.com/blogs/database/4-7-times-better-write-query-price-performance-with-aws-graviton4-r8g-instances-using-amazon-neptune-v1-4-5/"
date: "Mon, 25 Aug 2025 17:15:43 +0000"
author: "Dave Bechberger"
feed_url: "https://aws.amazon.com/blogs/database/category/database/amazon-neptune/feed/"
---
<p>Amazon Neptune version 1.4.5 introduces engine improvements and support for AWS Graviton-based r8g instances. In this post, we show you how these updates can improve your graph database performance and reduce costs. We walk you through the benchmark results for Gremlin and openCypher comparing Neptune v1.4.5 on r8g instances against previous versions. You’ll see performance improvements of up to 4.7x for write throughput and 3.7x for read throughput, along with the cost implications.</p> 
<p>Amazon Neptune Database is a fast, reliable, and fully managed graph database that makes it straightforward to build and run applications using highly connected datasets. You can build applications using Apache TinkerPop Gremlin or openCypher on the Property Graph model, or using the SPARQL query language on W3C Resource Description Framework (RDF).</p> 
<h2>Improvements in Neptune version 1.4.5</h2> 
<p>As part of the 1.4.5 release, Neptune delivered improvements targeting higher throughput and reduced P99 latencies compared to previous versions. According to our own benchmarks/tests, these improvements result in up to 20% performance gains on the same hardware for Gremlin workloads and up to 30% improvements for openCypher workloads. Additionally, the 1.4.5 version also contains improvements for openCypher users, with optimizations resulting in up to 10 times faster CREATE queries and up to 3 times faster MERGE queries on 12xlarge instances.</p> 
<p>With this release, Neptune now supports r8g and r7g instance types, providing a 16% cost reduction compared to the previous lowest-priced option, the r6g.</p> 
<p>The r7g instances are powered by AWS Graviton3 processors and are designed for memory-intensive workloads. They offer up to 25% better performance over the sixth-generation AWS Graviton2 based r6g instances. r7g instances feature Double Data Rate 5 (DDR5) memory, which provides 50% higher memory bandwidth compared to DDR4 memory to enable high-speed access to data in memory.</p> 
<p>r8g instances are powered by the latest-generation AWS Graviton4 processors and provide the best price-performance for memory-intensive workloads. r8g instances offer up to 30% better performance and larger instance sizes with up to three times more vCPUs and memory than the seventh-generation AWS Graviton3 based r7g instances. When combined, these improvements translate to price-performance benefits for all users, with openCypher workloads seeing up to 3.7 times better price-performance for read workloads and 4.7 times better price-performance for write workloads.</p> 
<p>Roy Reznik, Co-Founder &amp; VP R&amp;D at Wiz, shares their experience with Neptune’s performance improvements and r8g instances:</p> 
<blockquote>
 <p>“<em>The Wiz Security Graph visualizes your cloud stack to identify the risks in each layer and deliver actionable insights. It makes the complex simple by surfacing the relationships between cloud components as first-class citizens. To power the Wiz Security Graph, we needed a graph database that was globally available and could scale to 100s of billions of nodes and relationships to identify security risks in real-time. By updating to Graviton4-based r8g instances, we’ve reduced our graph database costs by 20%. Amazon Neptune’s combination of high-throughput graph processing, open-source graph models like Apache TinkerPop, and the cost-efficiency of the latest r8g instances allows us to scale the Wiz Security Graph at cloud speed.</em>”</p>
</blockquote> 
<p>Next, we show you our performance benchmark results.</p> 
<h2>Performance benchmark using Locust</h2> 
<p>For this benchmarking exercise, we used <a href="https://locust.io/" rel="noopener noreferrer" target="_blank">Locust.io</a>, an open source benchmarking tool that simulates real-world OLTP workloads. Locust is an open source load testing tool that you can use to create rich and complex load testing scenarios that can be distributed. For this test, we tested both read and write workloads on version 1.4.4.0 on an r5 instance and on version 1.4.5.0 on an r8g instance. The tests were performed using a custom user class (NeptuneUser) that implements a user class for the Boto3 SDK and is available on <a href="https://github.com/aws-samples/amazon-neptune-samples/tree/master/neptune-locust" rel="noopener noreferrer" target="_blank">GitHub</a>. The read and mutation workloads used represented common graph query patterns, such as retrieving egocentric neighborhoods, multi-hop path traversals, inserting nodes and edges, and upsert of nodes and edges. Comparable queries were run for both openCypher and Gremlin across a variety of client threads configurations.</p> 
<h2>Read workloads</h2> 
<p>For read workloads, Neptune 1.4.5 on r8g instances delivered 2.77x more queries per second than version 1.4.4.0 on r5 instances, with 62% lower P99 latency for openCypher queries. Similarly, Gremlin workloads showed an improvement of up to 1.89 times more queries per second with a 58% reduction in P99 latency.</p> 
<p>For openCypher read queries, we saw the following results.</p> 
<table border="1px" cellpadding="10px" class="styled-table"> 
 <tbody> 
  <tr> 
   <td style="padding: 10px;"></td> 
   <td colspan="2" style="padding: 10px;"><strong>large Instance Size</strong></td> 
   <td colspan="2" style="padding: 10px;"><strong>12xlarge Instance Size</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"><strong>Workload Type</strong></td> 
   <td style="padding: 10px;"><strong>Improvement in Throughput</strong></td> 
   <td style="padding: 10px;"><strong>Reduction in P99 Latency</strong></td> 
   <td style="padding: 10px;"><strong>Improvement in Throughput</strong></td> 
   <td style="padding: 10px;"><strong>Reduction in P99 Latency</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Egocentric neighborhood</td> 
   <td style="padding: 10px;">2.28x</td> 
   <td style="padding: 10px;">-61%</td> 
   <td style="padding: 10px;">1.56x</td> 
   <td style="padding: 10px;">-47.42%</td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Multi-hop path traversal</td> 
   <td style="padding: 10px;">2.77x</td> 
   <td style="padding: 10px;">-62%</td> 
   <td style="padding: 10px;">1.67x</td> 
   <td style="padding: 10px;">-54.53%</td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Point lookup</td> 
   <td style="padding: 10px;">1.95x</td> 
   <td style="padding: 10px;">-58 %</td> 
   <td style="padding: 10px;">1.52x</td> 
   <td style="padding: 10px;">-46.81%</td> 
  </tr> 
 </tbody> 
</table> 
<p>For Gremlin read queries, we saw the following results.</p> 
<table border="1px" cellpadding="10px" class="styled-table"> 
 <tbody> 
  <tr> 
   <td style="padding: 10px;"></td> 
   <td colspan="2" style="padding: 10px;"><strong>large Instance Size</strong></td> 
   <td colspan="2" style="padding: 10px;"><strong>12xlarge Instance Size</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"><strong>Workload Type</strong></td> 
   <td style="padding: 10px;"><strong>Improvement in Throughput</strong></td> 
   <td style="padding: 10px;"><strong>Reduction in P99 Latency</strong></td> 
   <td style="padding: 10px;"><strong>Improvement in Throughput</strong></td> 
   <td style="padding: 10px;"><strong>Reduction in P99 Latency</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Egocentric neighborhood</td> 
   <td style="padding: 10px;">1.69x</td> 
   <td style="padding: 10px;">-56%</td> 
   <td style="padding: 10px;">1.57x</td> 
   <td style="padding: 10px;">-27.29%</td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Multi-hop path traversal</td> 
   <td style="padding: 10px;">1.89x</td> 
   <td style="padding: 10px;">-59%</td> 
   <td style="padding: 10px;">1.48x</td> 
   <td style="padding: 10px;">-22.15%</td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Point lookup</td> 
   <td style="padding: 10px;">1.58x</td> 
   <td style="padding: 10px;">-58%</td> 
   <td style="padding: 10px;">1.37x</td> 
   <td style="padding: 10px;">-16.87%</td> 
  </tr> 
 </tbody> 
</table> 
<h2>Mutation workloads</h2> 
<p>For mutation workloads, Neptune 1.4.5 on r8g instances delivered 2.78x more queries per second than version 1.4.4.0 on r5 instances, with 77% lower P99 latency for openCypher queries. Gremlin workloads showed similar improvements: 1.99x higher throughput with 53% lower P99 latency.For openCypher mutation queries, we saw the following results.</p> 
<table border="1px" cellpadding="10px" class="styled-table"> 
 <tbody> 
  <tr> 
   <td style="padding: 10px;"></td> 
   <td colspan="2" style="padding: 10px;"><strong>large Instance Size</strong></td> 
   <td colspan="2" style="padding: 10px;"><strong>12xlarge Instance Size</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"><strong>Workload Type</strong></td> 
   <td style="padding: 10px;"><strong>Improvement in Throughput</strong></td> 
   <td style="padding: 10px;"><strong>Reduction in P99 Latency</strong></td> 
   <td style="padding: 10px;"><strong>Improvement in Throughput</strong></td> 
   <td style="padding: 10px;"><strong>Reduction in P99 Latency</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Create node</td> 
   <td style="padding: 10px;">2.13x</td> 
   <td style="padding: 10px;">-76%</td> 
   <td style="padding: 10px;">11.9x</td> 
   <td style="padding: 10px;">-53.48%</td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Create edge</td> 
   <td style="padding: 10px;">2.01x</td> 
   <td style="padding: 10px;">-68%</td> 
   <td style="padding: 10px;">10.7x</td> 
   <td style="padding: 10px;">-52.25%</td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Merge two nodes with one edge</td> 
   <td style="padding: 10px;">2.78x</td> 
   <td style="padding: 10px;">-77%</td> 
   <td style="padding: 10px;">3.94x</td> 
   <td style="padding: 10px;">-83.39%</td> 
  </tr> 
 </tbody> 
</table> 
<p>For Gremlin mutation queries, we saw the following results.</p> 
<table border="1px" cellpadding="10px" class="styled-table"> 
 <tbody> 
  <tr> 
   <td style="padding: 10px;"></td> 
   <td colspan="2" style="padding: 10px;"><strong>large Instance Size</strong></td> 
   <td colspan="2" style="padding: 10px;"><strong>12xlarge Instance Size</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"><strong>Workload Type</strong></td> 
   <td style="padding: 10px;"><strong>Improvement in Throughput</strong></td> 
   <td style="padding: 10px;"><strong>Reduction in P99 Latency</strong></td> 
   <td style="padding: 10px;"><strong>Improvement in Throughput</strong></td> 
   <td style="padding: 10px;"><strong>Reduction in P99 Latency</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Create node</td> 
   <td style="padding: 10px;">1.86x</td> 
   <td style="padding: 10px;">-53%</td> 
   <td style="padding: 10px;">1.29x</td> 
   <td style="padding: 10px;">0%</td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Create edge</td> 
   <td style="padding: 10px;">1.91x</td> 
   <td style="padding: 10px;">-51%</td> 
   <td style="padding: 10px;">1.18x</td> 
   <td style="padding: 10px;">0%</td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Merge two nodes with one edge</td> 
   <td style="padding: 10px;">1.98x</td> 
   <td style="padding: 10px;">-51%</td> 
   <td style="padding: 10px;">1.40x</td> 
   <td style="padding: 10px;">-18.24%</td> 
  </tr> 
 </tbody> 
</table> 
<p>In 1.4.5 we prioritized the optimization of openCypher write queries, showing improvements up to 11.9 times the previous version.</p> 
<h2>Price-performance</h2> 
<p>When looking at the combined impact of the engine improvements, new instance types, and cost reductions, we used a price-performance metric of cost/1mm queries. Given this metric, we saw an improved price-performance of up to 3.7 times for read workloads and up to 4.7 times for write workloads.</p> 
<table border="1px" cellpadding="10px" class="styled-table"> 
 <tbody> 
  <tr> 
   <td style="padding: 10px;"></td> 
   <td colspan="3" style="padding: 10px;"><strong>large Instance Size</strong></td> 
   <td colspan="3" style="padding: 10px;"><strong>12xlarge Instance Size</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;"><strong>Workload Type</strong></td> 
   <td style="padding: 10px;"><strong>Cost/1MM Queries (1.4.4 r5)</strong></td> 
   <td style="padding: 10px;"><strong>Cost/1MM Queries (1.4.5 r8g)</strong></td> 
   <td style="padding: 10px;"><strong>Price-Performance Improvement</strong></td> 
   <td style="padding: 10px;"><strong>Cost/1MM Queries (1.4.4 r5)</strong></td> 
   <td style="padding: 10px;"><strong>Cost/1MM Queries (1.4.5 r8g)</strong></td> 
   <td style="padding: 10px;"><strong>Price-Performance Improvement</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Read</td> 
   <td style="padding: 10px;">$0.81</td> 
   <td style="padding: 10px;">$0.22</td> 
   <td style="padding: 10px;">3.7x</td> 
   <td style="padding: 10px;">$0.91</td> 
   <td style="padding: 10px;">$0.39</td> 
   <td style="padding: 10px;">2.3x</td> 
  </tr> 
  <tr> 
   <td style="padding: 10px;">Write</td> 
   <td style="padding: 10px;">$3.02</td> 
   <td style="padding: 10px;">$0.64</td> 
   <td style="padding: 10px;">4.7x</td> 
   <td style="padding: 10px;">$4.33</td> 
   <td style="padding: 10px;">$0.87</td> 
   <td style="padding: 10px;">4.9x</td> 
  </tr> 
 </tbody> 
</table> 
<p><em>The cost was calculated using US East (N. Virginia) Amazon Neptune prices.</em></p> 
<h2>Conclusion</h2> 
<p>The engine version 1.4.5 is available in all Regions <a href="https://docs.aws.amazon.com/neptune/latest/userguide/limits.html#limits-regions" rel="noopener noreferrer" target="_blank">where Neptune is available</a>. To experience faster performance and higher throughput openCypher queries, <a href="https://console.aws.amazon.com/neptune/" rel="noopener noreferrer" target="_blank">upgrade your existing cluster to the latest 1.4.5 or newer version or create a new Neptune cluster</a>. To learn more about the new release, see the <a href="https://docs.aws.amazon.com/neptune/latest/userguide/engine-releases-1.4.5.0.html" rel="noopener noreferrer" target="_blank">1.4.5 release notes</a>.</p> 
<hr /> 
<h3>About the authors</h3> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="Dave Bechberger" class="aligncenter size-full wp-image-29797" height="160" src="https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2020/12/08/Dave-Bechberger.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Dave Bechberger</h3> 
  <p><a href="https://www.linkedin.com/in/davebechberger/" rel="noopener" target="_blank">Dave</a> is a Principal Graph Architect with the Amazon Neptune team. He used his years of experience working with customers to build graph database-backed applications as inspiration to co-author “<a href="https://www.manning.com/books/graph-databases-in-action?a_aid=bechberger" rel="noopener noreferrer" target="_blank">Graph Databases in Action</a>” by Manning.</p> 
 </div> 
</footer>
