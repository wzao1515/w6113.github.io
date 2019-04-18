---
layout: page
---

## Comments

Surajit is an excellent researcher and his AutoAdmin work defined how we think about auto-tuning databases.  He also writes clearly, as is evident in the paper.

This is a great connection!

    How do self-tuning databases stack up against human-designed
    databases? I imagine evaluating this would be a lot like
    justifying the usefulness of compilers - given a certain workload,
    the database must generate the optimal physical structure just
    as a compiler,

A succinct summary.

    The key contributions the authors made towards finding a minimized
    configuration (a set of indexes that minimize the cost) is 1)
    creating an API to create a hypothetical index, 2) efficiently
    generate statistics to describe distribution of values, 3) defining
    the search space as the best configurations for each query aka the
    lowest optimizer estimated cost.

This should be applauded for seeking out recent auto-tuning work (see OtterTune from CMU as well), however it then makes crazy claims (it learns the structure of the data and OPTIMAL ..plans).  Let's not blindly believe marketing.

    The state of art would be SageDB. By modeling the data distribution,
    workload, and hardware, SageDB learns the structure of the data and
    optimal access methods and query plans.

This is a good question to ask.  It is also good to speculate and try to answer your own question (that's the heart of research).  See the first except above for an example.

    As databases become more domain specific, what are self-tuning
    optimization changes - what mew optimizations become available,
    what are redundant and can be removed from consideration?

#### Here are some places to improve



## Your Reviews


<style>
p.review {
  border-left: 2px solid grey;
}

div.block {
  margin-bottom: 1em;
}
</style>

<h2 id="short-summaries">Short Summaries</h2>

<p class="block">Survey on advances in self-tuning physical database design</p>

<p class="block">The paper summarizes the initial problem, expansion of search space and algorithms in self turning databases</p>

<p class="block">1. pointing out the contribution of AutoAdmin. 2. showing how did AutoAdmin cooperate with new techniques</p>

<p class="block">The paper discussed advances in self-tuning database system over a decade with a focus on automated physical database design.</p>

<p class="block">It discusses advances in self-tuning database systems especially physical designs over the past decade and suggests opportunities for future research.</p>

<p class="block">Database tuning optimizes the performance of a database by maximizing use of system resources to perform work as efficiently and quickly as possible.</p>

<p class="block">describes how the search space of self-tuning databases in the last 10 years got more complex, and offers a search architecture to deal with this</p>

<p class="block">A survey paper that proposes the use of usage statistics to prune the search problem of optimal physical design.</p>

<p class="block">frames the problem of physical database design, surveys methods for self-tuning databases. authors worked on AutoAdmin, focus on commercial systems.</p>

<h2 id="reviews">Reviews</h2>

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">This paper is a great survey on self-tuning physical database deisgn. It is based on the experience in autoadmin and extends to other field of the advances in database physical design. 

First, this paper gives some review of the 1997 paper on physical database design and highlights the contribution of what-if which means that create soime hypothesis index and use some statistic functions to compute the cost and optimize over it. However, since there are a lot of database structures which makes the search space extremely large. So the former paper added some pruning table, candidata selection part to optimize the search space.

Except for index self tuning, this paper descirbe some other recent advances in the self-tuning part like statisitic management and infrastructure monitor.

Above are all the method the paper discussed. Another interesting part abou this paper is that it also introduces how this research impact the industry. 

Overall, this is a good survey. But the self tuning mentioned in this paper is more about the index tuning. It is good to see some parameters tunning like buffer and memory allocation. Also, there are some self driven database using machinelearning. It is good to discuss in class.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">The paper is interesting, reading like a fairly brief survey paper that hits the most important points. It is actually a paper summarizing the field of self tuning databases and its advancements Fromm 1997 to 2007, by the authors who wrote the seminal paper that started the field (An Efficient, Cost-Driven Index Selection Tool for Microsoft SQL). 

The key issue addressed by the paper is the tuning of physical database structures. The authors pioneered an approach to finding the best indexes to create on the data - after data became too big to accurately compute index access. State of the art was in 1997 was to build cost models of indices, or use the query optimizer to estimate cost. In the 1997 paper, they created an api to compute estimates of hypothetical indices, and a optimizers to find the indices to index given a workload. The search space then expanded to what tables to materialize and how to partition data. To reduce the search space, unlikely column sets were dropped from consideration, followed by a process that finds the vest sets, combine them before searching through what's left. Finally, there's the dimension of when database should tune itself, with the option of interval or continuous tuning. The ideas presented are widely implemented, a clear improvement on what went before and the authors won the prize for most impactful paper in the last decade.

The characteristics of self tuning have changed over time as the options of what can be tuned changed, along with the nature of workloads etc. The key to any future work would involve adaptation to these changes. How does the different distributed settings change? As databases become more domain specific, what are self-tuning optimization changes - what mew optimizations become available, what are redundant and can be removed from consideration? Algorithmic improvements as mentioned such as machine learning could also o play a larger row, given that ML models could perhaps improve prediction.</p>
  <div>Comments:</div>
  <p class="block">Overall, this is a very though provoking paper. It gives a very good summary of the field with key content of papers well summarized and weighed.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">The main problem that AutoAdmin was trying to solve is automated physical design for relational databases. This problem is hard because of many reasons. For example, the types of index that could be build in database are various, which makes search space large. To make things more complicate, with the introduction of new technologies like materialized views, partations, the selection must be compatable with them. Besides, the selection should also be useful when the system is scalable. Therefore, to reduce the search space, they gave several search algorithms like pruning, compression, merge... The state of art would be SageDB. By modeling the data distribution, workload, and hardware, SageDB learns the structure of the data and optimal access methods and query plans.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">As a response to VLDB 10-year Best paper award for AutoAdmin project, this paper gives an overview on the advances in self-tuning database systems over a decade with a primary focus on the problem of automated physical database design were given a database and a workload, the goal is to select the best physical design structures.

The authors started their discussion with a highlight on state of the art at the time when they published their paper. The problem settings were ambiguous, and there wasn't a standard definition of what and how to model workload and what are the evaluation metrics used. The problem addressed in the paper is as follow, given a workload that consists of a set of queries and updates, the goal is to pick a set of indexes that better serves the workload. The main contribution as they highlight is the what-if API added as an extension to the query optimizer, were given a query and a set of hypothetical physical design structures, it gives estimates on the plan the optimizer would have picked if these structures were present without the need to materialize them. They then talked about how the problem becomes even harder when we take into consideration structures other than indexes such as materialized views and partitioning. This then led to the discussion about different search algorithms and the heuristics used to guide and reduce the search space. They then moved on to talk about the recent advances in physical database design which includes improving the what-if API by instrumenting the optimizer for index selection, deciding when to tune, online physical design tuning, statistics management, and DBMS monitoring infrastructure. They ended their paper with an overview of promising future research directions.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">This paper by Chaudhuri and Narasayya discusses advances in self-tuning database systems over the past decade based on their experience in the AutoAdmin project at Microsoft Research. The paper primarily focuses on the problem of automated physical database design because it is the major topic of the VLDB 1997 paper, but it also highlights significant progress made in other self-tuning database technologies such as statistical management and monitoring infrastructure. It describes the challenges that are faced by researchers and industry accompanying the advent of more advanced physical design structures and explained the different solutions researchers worked on in the past decades. For each of these problems, the authors also proposed open issues and directions for future research. They conclude that it may be worthwhile to explore alternative architecture of database servers for performance vs manageability trade-off and that scalable storage and application servers are areas that need more attention.

In general, I think this is a really great paper which briefs the progress in self-tuning database systems research with a ton of paper resources from the past decade. The authors base the paper on the project from ten years ago and discussed the impact new technologies have on the topic of self-tuning databases, what challenges they pose and what opportunities they bring. The paper is nicely organized and concisely discusses the problems within each subtopic. It is thorough in covering recent advances in physical database design and relevant technologies in commercial DBMS and has little redundant information on what had already existed ten year ago. The volume of paper citations makes this paper a handy reference for future research.

In addition, the paper makes the contribution in recognizing that little survey work is present in the domain despite its wide scope, which motivates future survey on the subject [Systematic Mapping: Database Tuning Progress in a Decade]. Thus future work is able to fill the gap and provide a detailed overview of database tuning domains.

I dont think the paper has any major flaws, since its nature of being a succinct overview of technologies advances requires it to leave out a lot of details that would otherwise be needed in a detailed survey paper. The only thing I find it might improve is to give at least minimum briefing of the papers contribution/argument when it is citing. Although the paper aims to list the progress made, sometimes it is confusing to see papers referenced but have a concept coming from nowhere.
</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">This paper discusses the decade of progress made by the authors of the AutoAdmin project, specifically in its physical design. The primary problem that physical database design wants to solve is how to reduce the cost of managing relational database systems. 

The key contributions the authors made towards finding a minimized configuration (a set of indexes that minimize the cost) is 1) creating an API to create a hypothetical index, 2) efficiently generate statistics to describe distribution of values, 3) defining the search space as the best configurations for each query aka the lowest optimizer estimated cost. The introduction of materialized views and partitioning also required new methods to deal with scalability because that made the search space much larger. Thus, leads to the introduction of a few search algorithms. Pruning reduces the search space to keep the number of materialized views manageable, merging allows for covering different indexes, while enumeration finds the smallest total cost for the workload.</p>
  <div>Comments:</div>
  <p class="block">Overall, this paper is different from the ones we've read in class in that it serves more as an overview of the progress made to a field of research in the last ten years versus describing a project in depth.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">The paper surveys the progress that has been made in self-tuning databases since the authors published the AutoAdmin paper ten years before. 
The role of a self-tuning database used to be mainly focused on index selection, but the authors frame the new state of the field as including indexing, partitions, and materialized views. 
This is a very large search space for which the authors propose new generalized architectures.

The authors note that when they wrote their original paper, the definitions of cost and of workload were not standardized, but since then the definition of cost has begun to piggyback off the optimizers notion of cost, which helps keep the optimizer in the loop. 

The paper is quite effective as a state of the field, covering the most important considerations in physical design in detail, including concerns like combinations of different physical layouts including indexes which are also partitioned. The search architecture they outline goes into detail about its different stages, including naive and less naive approaches. The diagrams and consideration that the recommendations need to be integrated makes this very useful for anyone who would attempt to build a system to these specifications.

The original AutoAdmin paper included an Alerter subsystem that monitored changes in the workload and suggested physical redesigns if necessary. The authors flesh this out in the current paper into an entire architecture for online index tuning. The authors also note that current systems use the what-if architecture proposed in their AutoAdmin paper as a way to deal with the very large search space of physical layouts. 

Overall, this is an effective communication of the state of the field that proposes elegant conceptual solutions to the main difficulties in the newly increased search space of self-tuning.
</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">The paper is very focused on the evolution of the field over time - I'm not sure a historical survey is quite as useful as a "state-of-the-art" survey.

One useful contribution of this paper's preoccupation with history, however, is that it points out how modern databases differ from the databases contemporary to the various papers it reviews - for example, the authors note that since the 1997 VLDB paper, materialized views and vertical partitioning, both components of a database's physical design, have become widely supported and need to be accounted for in self-tuning algorithms.

Self-tuning databases seem strongest in their ability to adapt to changing workloads - hand-designing a database takes time, and so even if a self-tuning database weren't able to find the most optimal solution, the convenience of it being able to adapt itself without costing precious man-hours is significant. 

However, in section 6.1, the paper brings up an interesting point for consideration: where does the DBA fit into the self-tuning scheme? The authors note that "advanced" DBAs can tune the physical design of their databases in commercial systems. How do self-tuning databases stack up against human-designed databases? I imagine evaluating this would be a lot like justifying the usefulness of compilers - given a certain workload, the database must generate the optimal physical structure just as a compiler, given high-level code, must compile it down to an optimality approximately on par with hand-tuned machine code. Adaptability aside, it would be interesting to see how self-tuning databases stack up against human tuners.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">The problem of physical database design is related both to theory of cost estimation (rule-based models, histograms, etc) as well as practical aspects of instrumentation, monitoring tools, API design, with the database administrator as the target user. 

The authors found that original methods of creating statistics using full scan techniques would no longer work for very large databases. They also found that as execution engines became more complex, there could be many potential indices that made simple heuristics ineffective. Their first contribution was the "Create Hypothetical Index" command, which allowed a DBA to test different hypothetical indices without disrupting availability of the database. They later integrated the hypothetical index interface with the optimizer and improved scalability by reducing the number of optimizer calls. They go into depth about various search strategies used for candidate selection.

The authors discuss commercial efforts to incorporate tuning mechanism, for example the, Oracle Automatic Tuning Optimizer on a per query basis, as well as some other related features like Statistics management and monitoring (SQL server profiler). 

The authors mention several directions for future work, including lightweight mechanisms for defining physical structures, machine learning technqiues and control theory, methods for comparing quality of automated tuning solutions. One issue is the increasing complexity of performance-optimized internal components, and the authors suggest there is a tradeoff between high performance/complex functionality and manageability.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

