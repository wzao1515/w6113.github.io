---
layout: page
---

## Comments

It is better to go deeper into one particular aspect of your role than to make broad statements.  Keep asking and answering "why?" and "how?" and "what's the simplest example?"

### The Problem

        The Volcano optimizer generator addresses the problem of creating
        an optimizer based on a data model. Because evaluating every subquery
        with every possible physical operator could be arbitrarily expensive,
        the optimizer generator uses backward chaining to avoid evaluating
        as many expressions as possible.

This description touches upon many aspects of the problem, and can be improved by saying _less_.   The purpose of this role is to clarify what the problem really is and why it matters.  Volcano does many things: it generates an optimizer, it couples it with a search algorithm, etc etc.  First clearly distinguish these subproblems, and then comment on the important ones and WHY they are important.  

For instance, the optimizer generator is important because folks are creating new logical and physical operators all the time, yet that would require that optimizers be interpreted and potentially slow.  Also, components are not decoupled, which makes software engineering hard.  Finally, optimization is fundamentally search, and should be treated as such.



### Experiments

		The paper did well in identifying the relevant
		criterion to evaluate a query optimization system,
		namely optimization time, execution time, and to a
		lesser degree extensiblity. 

This identifies the main criteria.  Good job!  However, start with what the system is intended to do in order to clarify that these criteria are "complete" (evaluates all of the things that matter for the system).  For instance:

*  Volcano generates optimizers, in that its input are rules, operators, and costs, and it outputs an optimizer using a particular search heuristic.  The optimizer takes a logical query plan and outputs a faster physical query plan.  Thus we care about the generator AND the output optimizer:
  * extensibility (how easy it is to add new rules/operators/costs),
  * how long it takes to generate a new optimizer (generation time)
  * how long it takes to run the optimizer (optimization time)
  * how good the optimizer's output query plans are (query exec time)

In fact, another review points out a good way to assess extensibility:

        Extensibility is a more subjective factor that is hard to
        quantify, but Volcano does have several increased flexibilities
        that are not present in EXODUS


Also start by stating what we should expect to be the main factors that impact each criteria, based on first principles.     Based on this, THEN the paper experiments/evaluation can be assessed by comparing with the above.

		The results showed that Exodus optimization is much
		slower than volcano's, while execution time is
		approximately the same for up to 4 joins, with
		volcano doing better after. This makes sense due
		to Volcano's better search algorithm which limits
		the search space and requires less memory.

Be more nuanced and precise here.  This implies that Volcano's search algorithm is STRICTLY better on every dimension.  Is this really the case?


### Future


      One of the most time-consuming operations in Volcano is
      exhaustively generating all possible "moves" ...With advances
      in technology, such as using multiple cores to enable parallel
      processing, I wonder if it's possible to create a system that
      speeds up move generation so the most (relatively) optimal
      one can be chosen as soon as possible.

This is a promising observation.  I like that the procedure is sketched out.  Assess how far this direction COULD go if pursued (i.e., its "ceiling").  Given that the comment is about making search faster, what are other promising approaches to search optimization that could be used?  For parallelization, it provides a linear increase in search power, so how does that compare with the size of the search space?


## Reviews

<style>
p.review {
  border-left: 2px solid grey;
}

div.block {
  margin-bottom: 1em;
}
</style>

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>State-of-the-art</h5></div>
  <div class="block">The volcano is an efficient, extensible tool for querying and request processing in novel application domains. It satisfies the requirement of high functionality and high performance by using a new optimizer generator. The optimizer generator translates data model, logical algebra, physical algebra, and rules into the optimizer source code.</div>
  <div class="block">    Existing work such as Starburst extensible-RDBMS and Generic Object Model have already been trying to provide some rule-based 
extensible optimizer, but they are restricted to certain data models and execution engines, not extensible enough.
    EXODUS is an attemption of providing a much more extensible execution engine. It defines a general approach to the optimization 
problem-based on the generator paradigm (data model specification  as input data), separation of logical and physical algebra, 
extensive use of rules (transformation rules,
implementation rules), and its focus on software modularization. However, the performance of EXODUS is far from optimal.

    Volcano tries to combine the two advantages: extensibility and efficiency, in five ways. 1) the optimization engine should be efficient.
2) it should support various physical properties 3) it should permit extensive use of heuristics and data model semantics for search engine
4) it should be able to learn optimization heuristics 5) it should support flexible cost models</div>
  <div class="block">
1. The code in the paper is kind of messy.

2. "Cost is an abstract data.......d (e.g., estimated CPU time and I/Ocount), or any other type."  But it does not specify the detailed computation way.  I am curious about it. 

3. Instead of EXODUS using the forward chaining, volcano search algorithm uses the backward chaining with the subqueries only related to the large expressions which are pretty smart.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">This paper describes Volcano, a system for taking a data model, logical algebra, physical algebra, and optimization rules and translating them into optimizer source code.</div>
  <div class="block">Application that has high performance demand for query and requests based on algebras and algebraic equivalence rules such as object-oriented and scientific DB system, or new database query optimizers for novel data models, query algebras, and DBMS, because the optimizer generator has good extensibility, effectiveness and time and space efficiency.</div>
  <div class="block">Although it seems like a lot of code for the database implementer to provide, it is argued in the paper that this still is a total work savings for the database implementer when considering the alternative of providing all of these rules and implementing the search algorithm that Volcano provides.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Results</h5></div>
  <div class="block">This paper describes the query optimization process for Volcano and claims that it has efficient optimization, has an extensible structure (for physical properties), allow for guided search and learn optimization heuristics. The  generator is based on the input, search space over algebric transformation rules, algorithms and a checker.  </div>
  <div class="block">The paper did well in identifying the relevant criterion to evaluate a query optimization system, namely optimization time, execution time, and to a lesser degree extensiblity. In terms of evaluation, the paper mostly compares its performance against Exodus, an earlier query optimization engine. For the 3 main criteria above, the paper performs the following comparisons. To test optimization and execution time against exodus, the paper timed the time it took to perform 7 select joins,  running 50 queries of each. The results showed that Exodus optimization is much slower than volcano's, while execution time is approximately the same for up to 4 joins, with volcano doing better after. This makes sense due to Volcano's better search algorithm which limits the search space and requires less memory. In terms of extensibility, there is no hard experimental results. However, Volcano introduce the concept of seperating logical operators from physical ones. This allows for more specific specification of desired physical and logical properties when searching, which the paper claims to allow for more varied search algorithms. It also makes search cheaper compared Exodus, who's lack of said separation resulted in duplication. Volcano is also more extensible than Exodus because the cost function for the search could be any function instead of a fixed metric. </div>
  <div class="block">The ideas presented in the paper is very interesting, the the paper seems to jump around quite a bit. There are so many alternatives suggested that I found it difficult to keep track of what was implemented and what was just a idea. There are many component (12 in total), and the algorithms are intricate. The paper is also a little vague about how promise is defined, admitting that it is intricate and vital to search. The evaluations in terms of efficiency and effectiveness makes sense.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">Query optimization should be treated as a search problem. You can automatically generate the search from declaratively specified rules.</div>
  <div class="block">The general algorithm here seems pretty well-adapted to any discrete optimization problem where you can encode allowable possible-transitions as transformation rules (AFAICT, it's basically depth-first search on the graph of equivalence classes, using memoization to speed things up). This seems particularly useful when have to repeatedly solve the same problem (so the code-generation overhead is worthwhile) and when you have to repeatedly solve sub-problems (so memoization is worthwhile). A couple of ideas where this might be useful:

- PL compilers (e.g. should you inline a function or not?). Not much here seems like it wouldn't work (although the exhaustive enumeration might be too slow, since I assume a PL compiler has many more transform rules to choose from).
- Maybe some kind of gene/protein mutation planner. If you encode "allowable" edits with some cost function (presumably based on what enzymes or tools you have available), this could concievably be used to get optimal "paths" from sequence A to sequence B.
- Some kind of compiler verification tool? If you had a suite of "program rewrite rules", some initial program specification along with a desired "physical" property P, then you could probably use this to find the "smallest" edits such that the program satisfied P.</div>
  <div class="block">- I liked the use of code-generating the optimizer to cut on costs, but I'd love to see some benchmarks showing the interpretation/code-gen performance gap.

- I'd be super curious to know how this design compares to state-of-the-art language compilers like LLVM. How do they search the possible optimization space?

- I was a little confused by the Cascades paper -- a lot of it focused on code organization (e.g. using a Task class instead of a function, or the particular inheritance hierarchy), where I felt it'd be much easier to just show us the code. Is Cascades mostly "just" a re-engineering of the Volcano optimizer? Nothing felt that conceptually different.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">The paper introduces an approach to enable the creation of an extensible query optimizer generator using rule-based transformations in an efficient way in both memory consumption and optimization time. It decouples data model specification from search strategy performed to navigate the search space and uses physical properties with a top-down search method to guide the search path and provide communication between higher nodes to lower ones.</div>
  <div class="block">Volcano achieves extensibility by decoupling data specification from optimizer's search method to find the optimal plan. To generate the optimizer, Volcano takes as input: (set of logical operators, set of physical operators and enforcers which enforce physical properties, set of logical and implementation transformation rules, how cost is computed for each operator), with these it then generate a query optimizer that then can take a declarative query as input and find the best execution plan using a top-down search strategy and branch and bound pruning to limit the search space. The optimizer avoids calling equivalent expressions using a hash table of expressions and equivalent classes where the best plan is kept for each combination of physical properties for which an equivalence class has already been optimized and a network of equivalent classes is created during optimization and contains all optimization results. The search then takes as input {Logical Expression, Physical properties, cost limit} and first: generate all possible moves, then assess their promise of leading to optimal plan, once a complete plan is known for a logical expression and a physical property, no other plan or partial plan with higher cost can be part of the optimal query evaluation plan. </div>
  <div class="block">-</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">Volcano is a system that takes a data model, logical algebra, physical algebra and optimization rules to generate optimizer source code. When the DBMS user submits a query, the query is passed to said optimizer to generate an optimized plan. Volcano builds upon (and performs better than) its predecessor EXODUS by being more efficient, provides support for physical properties (like sort order), allows use of search heuristics, learns optimization heuristics, and supports flexible cost models.</div>
  <div class="block">One of the most time-consuming operations in Volcano is exhaustively generating all possible "moves" the optimizer can explore at any point. With advances in technology, such as using multiple cores to enable parallel processing, I wonder if it's possible to create a system that speeds up move generation so the most (relatively) optimal one can be chosen as soon as possible. One idea is to break up move generation into multiple, smaller parts that can then be sent to different processes to simultaneously be computed. One could see if it's possible to divvy up the search algorithm presented in page 10 to standalone parts and if yes, set up a multi-core processing unit that simultaneous performs the various computations.</div>
  <div class="block">In page 3 of the paper, the authors write that Volcano "generates optimizers that map an expression of the logical into an expression of the physical algebra using transformations within the logical algebra and cost-based mapping of logical operators to algorithms". I couldn't understand what they meant and would love to discuss that in class to gain a better understanding.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">The goal of the Volcano optimizer is to create a framework into which you can input data model specific operations and output an optimizer. This works by mapping logical algebra expressions onto equivalent physical expressions and using backward chaining of operators to arrive at appropriate plans, which reduces the search space. </div>
  <div class="block">The Volcano optimizer generator addresses the problem of creating an optimizer based on a data model. Because evaluating every subquery with every possible physical operator could be arbitrarily expensive, the optimizer generator uses backward chaining to avoid evaluating as many expressions as possible. This is important because being able to optimize complex queries better allows the workloads we can run on databases to be more intense. The Volcano optimizer generators distinction between logical and physical algebra is a big contribution in terms of how to think about optimization in general.</div>
  <div class="block">Explicitly defining logical algebra vs physical algebra, transformation rules vs implementation rules, operators vs enforcers, and so on was incredibly helpful. Knowing explicitly how the authors are using each of those terms was incredibly helpful as other authors have had slightly different interpretations of terms.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">The authors of this paper extend on the EXODUS optimizer generator, which they previously used to outline their overall approach to the optimizer generator as a modular, extensible paradigm for defining optimizers and which they now claim to have improved upon in terms of performance. The Volcano optimizer generator improves upon the execution plan search space problem and also adds improved cost modeling mechanisms for sort order and partitioning. Ultimately, the authors compare Volcano with EXODUS and see equivalent to order of magnitude-level improvements. </div>
  <div class="block">Because the authors are extending on EXODUS, their previous iteration of an optimizer generator, it of course makes sense to compare EXODUS and Volcano on their respective overheads, both with optimization time and with execution time. The authors do this, varying the number of input relations along the x-axis. As EXODUS and Volcano are fairly similar and follow the modular optimizer generator paradigm, it would perhaps also be useful to compare a less-modular, popular query optimizer to gauge how much (if any) overhead is incurred simply by the modular model itself - however, since the optimizer is built up from the modules itself, its very possible that there isnt any, and it would perhaps be difficult to do because the authors assign a lot of responsibility for the actual optimization to the optimizer creators. However, this paper seems heavily focused on beating its own previous iteration and makes very little attempt to compare the output of this optimizer generator with handwritten optimizers.</div>
  <div class="block">A substantial amount of the performance and usability of this optimizer generator depends upon the optimizer creator, and so the feasibility of this sort of system in practice is difficult to gauge based only on the paper.

It also seems like the authors place much of the responsibility for actually optimizing the queries on the user - although they propose to introduce a new query processing engine that is independent of any data model, its independence is mostly due to the fact that its a DIY optimizer. The authors claim that the ability to learn the optimal query plan is fundamental to their model, but they dont much discuss this aspect again throughout the rest of the paper</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Hater</h5></div>
  <div class="block">Volcano is a system that takes logical algebra, physical algebra, and optimization rules as input, and translate them into the optimizer source code. This is a top-down method because in its find best plan (search algorithm), it goes from top to down. Besides, it uses a cache mechanism to optimize the algorithm.</div>
  <div class="block">I could only find some flaws rather than major weaknesses.
In volcano, there are both transformation rules (logical) and implementation rules (physical). I'm not sure whether this makes sense, which means treating rules distinctively as two sets. The reason is that it might be hard to design and control each rule sets. For example, in Cascade paper, it illustrated a scenario that  dividing dividing a complex join predicate into clauses that apply to left, right, and both inputs is a deterministic process best implemented by a single function. And I think that's why in Cascade they regard rules as a class that allows user to write functions.</div>
  <div class="block">I think one thing worth mentioning is the dynamic programming algorithm used in search engine. It uses hash table to cache plan and cost. If the logic exprssion is not in the hash table, it would use the winner of transformation, algorithm and enforcer to move the logic expression or physical properities.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">Volcano optimizer generator is aimed at combining generality and efficiency in an optimizer generator and search engine. The optimizer generator translates a model specification into optimizer source code, to be compiled and linked into the optimizer in the DBMS software. The search algorithm uses backwards chaining to explore subqueries, i.e., it starts from a set of physical properties and works backward to find expressions satisfying it.</div>
  <div class="block">The paper follows three design principles: 1) using algebraic operators to describe transformations of queries in optimization, and using logical operators to choose algorithms. 2) using rules as independent modules later combined in the search engine. and 3) using rule compilation rather than interpretation, although they use interpreted protoype in their studies. The volcano optimizer generator allows for generality/extensibility by acting as a template/framework in which more specific optimizers can be implemented. The basic optimization requires mapping a logical algebra expression to the equivalent physical algebra expression. The optimizer then reorders operators and selects implementation algorithms. Thus the algorithm is driven top-down, so subexpressions are only optimized if they are part of the currently most promising logical expression and physical plan for the query. In addition to logical and physical operators, the optimizor implementor also provides a `cost` abstract data type associated for each function and for each algorithm, which it uses to estimate cost once it decides to explore that algorithm. One main benefit from these design choices is that Volcano optimizer generator allows modular development of optimizer components. It seems like implementing a component using the generator would be something in between specifying a configuration file (based on known data characteristics) and writing a full-blown optimizer.</div>
  <div class="block">This paper was very technical, and so far in our papers we have not really discussed compilation/generation in so much detail.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">Volcano provides an extensible-yet-efficient query optimizer, improving (and taking inspiration from) EXODUS and Starburst.</div>
  <div class="block">Traditionally, database systems have a single, static query optimization component. Query optimizers have generally improved, but the focus has been on improving optimization for the standard relational model. Because the optimizer's decisions are not sensitive to the nature of each particular application domain or dataset, this one-size-fits-all approach is not ideal for all applications. There is a need for more flexible, extensible systems that can reason about the properties of an individual dataset and optimize queries accordingly, rather than applying the same optimizations without regard to the characteristics of the particular application. Volcano uses an optimizer generator that takes a data model/specification as input, in order to minimize assumptions about the data. Volcano improves upon EXODUS, an earlier extensible system, by increasing extensibility as well as efficiency. The statistics reveal that Volcano is indeed more efficient than EXODUS. Extensibility is a more subjective factor that is hard to quantify, but Volcano does have several increased flexibilities that are not present in EXODUS (the most compelling are described in the comments below). Domain-specific query optimization is certainly an important problem. From a practical perspective, there are already domains outside of standard relational--the Volcano paper names a few: object-oriented, scientific processing (and I would add image/video). From a design-perspective, extensibility is an important feature because there will always be unforeseen applications and extensions that appear in the future. The question is whether creating an extensible system is the correct solution--is it really better than creating each domain-specific solution from scratch? I left some evaluation of this question in the comments.</div>
  <div class="block">There were a few points of Volcano that I found particularly compelling. It allows users to specify physical properties, which I am surprised was not differentiated from logical expressions in EXODUS. Volcano also abstracts the cost model so it can refer to not only time but also memory consumption, etc.--users just have to pass in a function to define the concept of "cost". 

One question I had--who would use Volcano? They argue that it is easier to use the Volcano generator than to implement one's own query optimization system, but it is difficult to quantify which approach is "better". If you implement your  own domain-specific optimizer, you definitely have more flexibility with the design, and you don't have to learn how to use a new "language" to code for the Volcano generator. It's hard to prove that Volcano is really worth using without concrete user experience. </div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>State-of-the-art</h5></div>
  <div class="block">The paper introduces a new database optimizer generator named Volcano which is claimed to be extensible and efficient. Compared to the EXODUS, the Volcano has better extensibility because of its more general search strategy and better optimization time per query.</div>
  <div class="block">What was the previous state-of-the-art (in the same field or other fields)? How, and how much does this system advance state of art? Justify with evidence.
In the paper, the previous work that is used for design and performance comparison is the EXODUS system, but it also talks about weaknesses of Stardust and the Generic Object Model. The EXODUS is using well-defined components to conquer the complexity of software design and maintenance. It turns data into machine code for faster pattern matching (e.g. turn string into integers). It has several weaknesses Volcano improves on which include the MESH data structure that holds all logical and physical algebra expression in a cumbersome way. For unforeseen algebra, the EXODUS requires modifications which lacks extensibility. Stardust is also another extensible relational database system based on two subsystems: Query Graph Model and Select Project Join. The optimization QGM is based entirely on heuristics and is cost insensitive, and the SQJ performs exhaustive search within certain boundaries. Its optimizer is focused on step-wise expansion based on grammar-like rules which makes it hard to add new rules. Generic Object Model has a ruled-based optimizer on path expression, while it lacks the extensibility among different data models and engines. The Volcano advances in these systems by making a distinction between logical and physical expressions (improve on MESH), a top-down manner of algorithm design that only warranted subexpression will be optimized, and a more generic definition of cost during optimization. The Volcano is also more efficient both in time and space than the EXODUS in complex query optimization. The MESH data structure is space inefficiently, and in paper, it has a 20x more space output. The time also differs in an order of magnitude.  </div>
  <div class="block">The Volcano is quite an efficient optimizer generator that is better than other systems at that time. In the experiments, it only compares to the EXODUS on select-join query optimizers generator. I think it would be stronger if it also experiments on other systems the paper talks about: the Stardust and GOM. In addition, the EXODUS system ran out of memory in the complex query, it might be interesting if we can do an experiment to show how different queries affect the output size in Volcano. </div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Hater</h5></div>
  <div class="block">This paper introduces the Volcano Optimizer Generator aims to generate optimizer source code with extensibility, effectiveness, and time and space efficiency in the search engine. These design goals are achieved by letting optimizer implementors provide data model specifications and by encapsulating costs as well as logical and physical properties into abstract data types. The paper compares the Volcano Optimizer Generator with the Exodus Optimizer Generator both theoretically and experimentally and concluded that Volcano is more extensible and efficient.</div>
  <div class="block">One major flaw in the design of the Volcano Optimizer Generator is that although the use of equivalent classes is sufficiently modular and memorization prevents redundant optimization, the algorithm still requires that in the first phase, all transformation rules provided by applied to create all possible logical expressions for a query and all its subtrees. Then in the second phase, the optimizer has to navigate through all these options and determine the best plan. However, it may not be useful to derive all logically equivalent forms of all expressions, e.g., of a predicate. A follow-up paper by Graefe recognizes this defect and developed the Cascades optimizer. Instead of pre-generating all the logically equivalent expressions by expanding all equivalent classes, Cascades explores a group using transformation rules on demand to derive all logical expressions that match a given pattern. This can save some effort expanding the logical expressions if some correct guidance indicating which rule might leaf to expressions matching the given pattern is provided.

Another potential room for improvement is that the experimental comparison between EXODUS and Volcano is kind of lacking. The two optimizer generators are only compared within a rather small data model consisting of select and join operators. Although the authors argue that the advantages of Volcano would be even stronger for richer and more complex data models, some experimental evidence would be preferred in a more complex setting with more complex logical operators (e.g. aggregation) and more diverse software and hardware environments. </div>
  <div class="block">One thing that is interesting in the Volcano Optimizer Generator is the clear separation of logical and physical algebras. It uses transformation rules within the logical algebra expressions and uses implementation rules together with algorithms cost functions to map a logical expression into an optimal equivalent physical algebra expression. This separation makes the introduction of either new relational operator or physical algorithm easy for the database and optimizer implementor, since an addition may just require one or two new transformation or implementation rules in Volcano. Further, this separation is also adopted in the search engine. This makes Volcano more extensible than its peers. </div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>
