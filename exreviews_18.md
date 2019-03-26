---
layout: page
---

## Overall Comments

Overall, I was very impressed with the reviews, particularly those that looked up related research or described thought experiments based on the readings.
An important aspect of reading papers (and reviewing them), is to remember that the problem being solved in the reading, and the solutions that are proposed, are NOT the only things that are important!  What's important is to contrast the content of the paper with other works, use cases, applications, etc.

### Some specific comments

The following is a great description of vanilla datalog, because it describes the key intuition without using terminology defined in the paper.  

	We may view Datalog as a set of logical constraints. Datalog programs
	are evaluated according to a constructive procedure that generally
	relies upon bottom-up evaluation strategies. We begin from a
	fixed-state database, then, step-by-step, compute what we can infer
	to be true based on the defined rules. 

In contrast, it is easy to describe a paper using the terminology described IN the paper.  This is less good because the reader would need to read the paper to understand the terminology, which defeats the point of writing a review.  Notice how this description relies on special terminology (predicates, EDB, IDB, extensional, intensional).  

	To make datalog language recursive, it defines two types of predicates:
	EDB (extensional database) and IDB (intensional database) predicates.
	The base case of the recursion is called EDB while the recursive
	case is IDB.

This review is written in a way that implies that QSQ is THE solution.  It states that "a top down method is suggested" and mentions some of the ideas about it.  But why is this method even described?  What do these key ideas do?  How does it compare (and with what?)?  How do you THINK it compares with a bottom up approach?  

	To address such problems, a top down method is suggested. Top-down
	method is also called QSQ (query/subquery) method, which contains
	unification, that is binding informations with rules and targets.

The following observation is an obvious but great one to point out.  The problem with datalog is that programs beyond 10ish rules are hard to understand -- especially with lots of recursion.

	Another potential issue is readability/codeability--even
	if it's possible to express some application with a very
	strict subclass of Datalog rules, and therefore use some
	nice optimizations, it may become difficult to write the
	rules.

I like references to related work.  Here are snippets from different reviews

	One example is the use of datalog to reason about the
	complexity of computing a query on a probabilistic database
	(by Prof. Dan Suciu).

	Specifically, variations have been made for in-memory dialog,
	distributed datalog over Hadoop/spark, and graph processing/ social
	network analysis (SocialLite).

	The previous state-of-the-art is Seshadri et.al. proposes a runtime
	approach in which the ordering of atoms is optimized for the purpose
	of computing binding predicates for magic sets rewrites.

Coming up with specific examples that highlight shortcomings (or strengths) is a excellent exercise when reading a paper.

	Every rule that's (R := P1, P2, ..., Pn) is effectively going to
	do an n-way join, which can get super expensive unless you have
	well-optimized joins (one off-the-cuff solution idea: store every
	fact in a B-tree


Finally, this is a good example where the reviewer doesn't simply believe that topics in the paper are good, and views it from a critical standpoint.

	The magic set rewrite optimizations are really weird when you think
	about it -- datalog is this declarative language, but we're effectively
	*changing* our datalog program in order to more efficiently explore
	the search space

## Your Reviews

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

  <div class="block"><h5>Hater</h5></div>
  <div class="block">Datalog is a declarative query language for logical assertions, where you begin with a collection of facts, write relationship rules over those facts, and let Datalog handle the rest. This is a pretty different formalism form relational algebra and SQL that crucially allows recursive definitions of relationships. </div>
  <div>Response:</div>
  <p class="block">- The paper we read focused a lot on the semantics and algorithms behind datalog, but not a lot of the more "software engineering" aspects. For example, how should data be stored in a real system? How complicated are the rules typically in datalog (AFAICT, for a R := P1, P2, ... Pn, rule, that's effectively going to do an n-way join, which seems like it could get pricey)? How to handle buffer management when doing a search for some query?

- It's not clear to me that evaluating Datalog queries is really scalable as you get a large number of data points (facts?). Every rule that's (R := P1, P2, ..., Pn) is effectively going to do an n-way join, which can get super expensive unless you have well-optimized joins (one off-the-cuff solution idea:  store every fact in a B-tree so that every join is a sort-merge join with pre-sorted inputs. Not sure how this would handle something like (R1(x, y):= A(x, z), B(z, y), R1(x, y) := B(z, y), A(z, x), where each A you want to traverse A both in the first element and in the second element efficiently though...)

- The magic set rewrite optimizations are really weird when you think about it -- datalog is this declarative language, but we're effectively *changing* our datalog program in order to more efficiently explore the search space (although TBF the change is programmatic and automatic). IMO it makes thinking of the supplementary relationships and sidewise information strategies a little hard to think about, because those have to do with the search algorithm and not the semantics of the query itself, even though it's expressed in the datalog language.
</p>
  <div>Comments:</div>
  <p class="block">- I really like the Datalog language itself -- it seems like a very expressive formalism (and the syntax seems like it could be easily machine generated and parsed as just s-expressions or something, unlike the mess that is string munging SQL statements)

- I wish there was a better sense of how Datalog would work in practice. If I wanted to write a "normal" CRUD app (like an e-commerce shopping cart or something), I don't think I quite have a clear picture of how you would practically set up your facts and relationships. For example, would you have a `Item(id)` fact separate from a giant `ItemInformation(id, price, stock, ...)` fact? Or would you break up your "row" in a normal RDBMS into a lots of little facts instead, like `Costs(id, price)`, `InStock(price, stock), ...` (this latter approach intuitively matches Datalog better, but I think an explicit comparison would be nice).

- I'm not sure I quite understand the supplementary relationships and the magic sets. As far as I can tell, they  "hold" the values of bound variables (in some sense) -- but it's not clear to me how magic sets necessarily make things more efficient. You do prune out unnecessary derivations, but you also add a lot more (at least double?) the number of rules AFAICT.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>State-of-the-art</h5></div>
  <div class="block">These two chapter introduced language and semantics and recursive query processing, mainly on the presentation of core Datalog, discussed straightforward forms of negation with clear semantics and one representative method from the bottom-up method and the top-down method.</div>
  <div>Response:</div>
  <p class="block">The previous state-of-the-art is Seshadri et.al. proposes a runtime approach in which the ordering of atoms is optimized for the purpose of computing binding predicates for magic sets rewrites. While this system can compute the estimated size of any given predicate: it is the weight of the minimum spanning tree of the predicates dependency graph, where edge weights are composed with multiplication rather than addition.</p>
  <div>Comments:</div>
  <p class="block">The bottom-up approach is an alternative way of looking at the constructive fixpoint technique, this is done repeatedly until no new facts can be inferred, and eventually yields all facts that can be proven and is essentially the same as the fixpoint approach.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">The paper is about the Datalog, a logic based language. It have similar power to sql / relational calculus, but is defined in terms of base facts and rules to expand on these facts. The paper also covered evaluation strategies both top down and bottom up, naive, semi-naive, and magic sets</div>
  <div>Response:</div>
  <p class="block">Looking into the topic of Datalog, it seems that it have made a resurgence in recent years in domain specific applications. Specifically, variations have been made for in-memory dialog, distributed datalog over Hadoop/spark, and graph processing/ social network analysis (SocialLite). 

Every trend in databases in general is a potential future direction datalog can take. For the trend to distributed computing: 
- how frequently should datalog intermediate results be represented in a distributed setting for replication? 
- How should top down evaluations be performed in parallel? 
- How well does datalog scale in terms of the number of rules and basic atoms?

For trends in fast in-memory databases:
- Datalog's shouldn't need too much storage - compute is relatively more expansive. What is the best way to minimize memory footprints?

There have also been works to extend the language/expressitivity of datalog. Future work could also be done in perhaps accepting UDFs, and rules to bound them. </p>
  <div>Comments:</div>
  <p class="block">When reading the paper, I was struck by sentence mentioning that the datalog language can express any query that can be evaluated in polynomial time on an ordered database. I wasn't quite sure about why the database must be ordered, nor its relative power compared to regular sql on relational databases, which is also based on 1st order predicate logic. Some searching on the web seem to imply that datalog is more powerful because it can perform recursion (compute transitive closures), while relational calculus can express all first order logic based queries and datalog cannot. Then there is the issue of computational complexity, which I'm also not certain off as that seem to be dependent on the algorithm used to evaluate each. The concept of magic sets came up early in the semester with reducing UDF costs in hybrid caching. In both, the goal is to rearrange evaluation order to minimize unnecessary work, which suggests that algorithms should be transferable across domains. Thinking about UDFs however made me wonder again about the power of the language, since UDFS in Postgres makes sql Turing complete. </p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">Due to an  emerging interest in the use of recursive queries for multiple application domains, this paper presents a comprehensive survey about Datalog language and recursive queries, including lamguage definition, query processing and optimization techniques,  and an overview of systems and applications that use Datalog and recursive queries.</div>
  <div>Response:</div>
  <p class="block">* Relational Algebra has limited expressibility, thus, Datalog language has been introduced to allow the expressiveness of complex problems by supporting recursion through a declarative interface. A Datalog program is a collection of Datalog rules that constitutes a head and body in the form <head>:- <body>, where the head is an atom, and the body is a conjunction of multiple atoms. Predicate symbols are divided into two categories, extensional database predicates (EDB) and intensional database predicates (IDB). There are three different semantics to Datalog: (1) model-theoretic semantics, where rules are viewed as logical constraints and of which it shows that there is a minimal model that extends the base instance. (2) Least fixpoint semantic, which explains how a Datalog program might be computed. (3) Proof-theoretic semantics, that computes the results from the set of atoms that can be proved using the source database instance and the rules of the program. Handling negation is tricky, which can be achieved through stratified negation where recursion through negation is disallowed. For query processing, there are two methods: bottom up and top down. Bottom-up focus on newly derived tuples (deltas) from the previous iteration, with the advantage, that it minimizes the redundant derivation of the same tuples across multiple iterations. A disadvantage to bottom up is that it doesn't minimize the derivation of tuples that are not necessary for answering a query, which through top-down method, can be eliminated as it derives the tuples relevant to the query. 
 
&lt;/p&gt;
  <div>Comments:</div>
  <p class="block">-</p>
  &lt;/p&gt;
  <hr />

  <p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">This two chapter talks about the datalog and recursively query processing. Chapter 2 is mainly about datalog syntax and semantic. Chapter 3 is mainly about bottom up and top down evaluation. </div>
  <div>Response:</div>
  <p class="block">Datalog meets the need of highlevel abstractions for reasoning about and rapidly developing complex systems that process large amount of data, sometimes are distributed and parallel. The interface of datalog is mainly about what, not the low level how. I think datalog represents the trend of programming language. Later, there would be more domain specific system with high level declarative language which makes the complex system easy to build. </p>
  <div>Comments:</div>
  <p class="block">It is a nice textbook. It is organized and explain the syntax and semantic of datalog.</p>
  </p>
  <hr />

  <p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">Datalog is a logical database query language designed to enable recursive queries. A datalog program is a collection of rules. A datalog rule must satisfy the range restriction property, which says that every variable occuring in the head of a rule must also occur in a perdicate atom in the body. Each rule expresses the idea that, from certain combinations of tuples in certain relations, we may infer that some other tuple must be in some other relation or in the query answer. </div>
  <div>Response:</div>
  <p class="block">Datalog was invented to apply the principles of logical programming to database theory, especially by supporting recursive queries. Because it's made up of facts(tuples in the database) and rules(queries), it enables rigorous reasoning about queries and their properties. The facts effectively constitute a database table. Rules allow us to deduce new knowledge. Datalog allows the ability to use recursion to specify relationships in data. This has led to its use in many areas, such as artificial intelligence, networking, and advances in database theory. One example is the use of datalog to reason about the complexity of computing a query on a probabilistic database (by Prof. Dan Suciu). Many (open source and commercial) systems have emerged that are either based on datalog or provide a datalog interpreter.</p>
  <div>Comments:</div>
  <p class="block">It was difficult to understand Chapter 2 and 3 without first understanding the significance of Datalog as a language. </p>
  </p>
  <hr />

  <p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">The survey discusses Datalog, a declarative logic programming language, including its semanticd, query evaluation methods, optimizations such as magic-sets, and extensions of the language.</div>
  <div>Response:</div>
  <p class="block">he survey discusses many applications of Datalog, in the domains of program analysis, declarative networking, and data integration and exchange. 

Datalog is useful for program analysis because it allows concise expression of recursion, which appears in many program structures. For example, points-to analysis of a Java program determines what heap object a variable can point to. It be implemented by several Datalog rules that use those Java objects and variables as constants. While the relationships between variables and heap objects can also be expressed in imperative languages, Datalog's recursive predicates, along with query optimization techniques, makes Datalog implementations like Doop faster. 

Another application is declarative networking. For example, Datalog is suitable for implementing a routing protocol, because it can recursively express paths/subpaths, and their associated costs/subcosts. In that application, the Datalog constants are network links. The survey describes the evaluation strategy for Network Datalog programs. The semi-naive execution model is suitable for asynchronous, distributed settings because the execution can simply pause between interations until the next tuple is received. Each tuple arriving could be part of "delta" in the semi-naive model.

Interestingly, the rules in points-to analysis are not linearly recursive rules, which means Datalog optimizations cannot be used. This and several other applications of Datalog exposes a potential shortcoming--there is a hard trade-off between optimizability and expressiveness of Datalog rules. That is, we have to make stricter assumptions (e.g. the rules are linearly recursive) in order to get nice optimizations. This is kind of an unavoidable trade-off, so it's not necessarily a weakness, but it does become an issue when applications require more expressiveness. The survey spent a lot of time discussing semipositive Datalog^ and how various requirements can yield well-behaving/non-pathological Datalog rules. Another potential issue is readability/codeability--even if it's possible to express some application with a very strict subclass of Datalog rules, and therefore use some nice optimizations, it may become difficult to write the rules.</p>
  <div>Comments:</div>
  <p class="block">Lots of formalism, and it felt like some of the early theorems were basically by definition. Theorems about precedence, ordering, optimizations were deeper</p>
  </p>
  <hr />

  <p class="review">
  <h3>---</h3>

  <div class="block"><h5>Results</h5></div>
  <div class="block">Datalog is a way to write programs through logical expressions that have rules (intensional database predicates) and facts (extensional database predicates). Queries can be expressed through writing rules where the head is a condition dependent on the body. Through running Datalog queries, logical inferences can be generated from a set of ground truths.</div>
  <div>Response:</div>
  <p class="block">Datalogs primary point of evaluation of its contribution is its expressiveness. The recursive queries that Datalog makes very simple are quite difficult to reason about in SQL with UNION ALLs. Datalog evaluates/proves this by giving examples of running recursive queries on hierarchically structured data.
Datalog also has the important conceptual contribution of deductions that differs from SQL. Expressing the data in logic form makes it possible to deduce new pieces of data.
</p>
  <div>Comments:</div>
  <p class="block">Chapter 2 mentions that in the bottom-up evaluation strategy, query optimization techniques are used to avoid deriving facts that are not useful to answering the query. I wasnt clear on what those techniques might be, and I think it would be great to discuss that in class!
On a similar note, I wasnt clear on why bottom-up was so definitively preferred over top-down, being that bottom up seems like it would be doing a lot of extra work as it reaches the fixpoint. 

Overall this is conceptually a really interesting way to query! And the development of the ideas through the three different sets of semantics was really interesting. I think the notion of the immediate consequence operator in fixpoint-theoretic semantics was left a little vague, but the description of the proof tree in proof-theoretic semantics was really helpful. I guess that's kind of Datalog's version of a query plan?</p>
  </p>
  <hr />

  <p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">This paper presents a survey of the recursive query language Datalog, which is based on logic programming. The authors discuss the two major modes of evaluation Datalog uses: top-down, which computes from the state of the database and over the course of many generations of computation determine which Datalog rules evaluate true, ignoring all those that evaluate false; and bottom-up, which begins from the query given by the user and searches down for proof trees, each step computing the series of atoms necessary to prove a given rule true. </div>
  <div>Response:</div>
  <p class="block">Datalog is based on logic programming. A Datalog program defines a set of rules, or heads, which evaluate to true if all constituents of its body evaluate true. We may view Datalog as a set of logical constraints.

Datalog programs are evaluated according to a constructive procedure that generally relies upon bottom-up evaluation strategies. We begin from a fixed-state database, then, step-by-step, compute what we can infer to be true based on the defined rules. 

However, core Datalog does not handle negation - this is an added feature.

Datalog programs can be evaluated bottom-up or top-down.

Bottom-up evaluation is performed by, again, performing step-by-step computations of rules from the fixed-state database. However, this can lead to recomputation of already-computed rules. Thus, bottom-up evaluation relies on deltas from one generation of computations to the next to avoid this.

Bottom-up evaluation, however, does not optimize to prune computations of tuples that are unnecessary to compute a query. Top-down evaluation is a better case for accomplishing this. Top-down evaluation essentially searches out proof trees for the given query.</p>
  <div>Comments:</div>
  <p class="block">Perhaps it would have been helpful for the authors to more directly compare the benefits and sacrifices of bottom-up and top-down evaluation. However, this isnt a particularly major issue.</p>
  </p>
  <hr />

  <p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">These two chapters from the article surveys the syntax, semantics, recursive query processing evaluation methods, extensions and optimization techniques of the Datalog Language from the perspective of modern, practical applications and commercial systems. It uses examples to introduce three semantics of core Datalog and extensions such as negation and aggregation. Top-down and bottom-up evaluation methods are also explained with thorough analysis and optimization using magic sets is presented with examples and its effective applications. </div>
  <div>Response:</div>
  <p class="block">Compared to other query languages, Datalogs primary addition to the relational database semantics is recursive queries. The fact that Datalog is ideal for recursive language processing makes it a natural fit for many applications where recursive queries are expected. In Chapter 6 of this paper, the authors survey a series of applications of Datalog from a modern perspective. In particular, they discuss the domains of program analysis, declarative network, data integration and exchange, and the enterprise software systems. For example, program analysis, which covers a broad range of analysis such as pointer analysis and static code structure, is highly recursive in nature so that Datalog applies well. .QL and Doop are the two program analysis frameworks that the paper mentions where Datalog can be effectively used. Another example that the paper gives is declarative networking. Since network protocols are based on recursive relations among nodes in the network, Datalog provides benefits such as compactness of specification and optimizability that the network protocol specification could be directly compiled into a dataflow framework for execution. For example, the Network Datalog language could be used to implement the Path-vector protocol which is used as the based routing protocol for exchanging routes among Internet Services. </p>
  <div>Comments:</div>
  <p class="block">One interesting note is that practical applications always require extensions to the core language. As the paper comments on expressiveness of Datalog, it points out that stratified negation strictly extends core Datalog in expressiveness while preserving guarantees of termination in polynomial time in the size of the database instance. But other extensions such as arithmetic functions may destroy Datalogs poly-time guarantees. So it might be interesting to ask what are the bounds for Datalogs expressiveness and resulting applications.</p>
  </p>
  <hr />

  <p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">Datalog is a declarative language as well as a database system where queries and database updates are expressed in the logic language. The paper firstly gives the definition of the language and semantics, and then give top-down and bottom-up recursive query processing methods.</div>
  <div>Response:</div>
  <p class="block">To make datalog language recursive, it defines two types of predicates: EDB (extensional database) and IDB (intensional database) predicates. The base case of the recursion is called EDB while the recursive case is IDB. Besides, the paper also give three semantics to be clear of what, how the datalog program should be computed and the result of the computation (model theoretic, fixpoint theoretic and proof theoretic respectively). Besides, an important property is negation. Since datalog programs are monotone, it needs negation to express more queries.


For the query processing, there are mainly two types of methods: bottom-up and top-down. Bottom-up method is a semi-naive method that still compute iteratively. To avoid repeat compute, it compute the delta of each iteration. The example described in Figure 3.3 is really clear. However, this method still exists shortcomings like the derivation of tuples that are not in answering a query. To address such problems, a top down method is suggested. Top-down method is also called QSQ (query/subquery) method, which contains unification, that is binding informations with rules and targets.
</p>
  <div>Comments:</div>
  <p class="block">I think datalog is different from other database systems like the difference between haskell and python. And of course datalog's usecases are more special, like in program analysis. </p>
  </p>
  <hr />

