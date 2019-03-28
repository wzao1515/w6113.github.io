---
layout: page
---


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

  <div class="block"><h5>Results</h5></div>
  <div class="block">This paper study how incomplete databases, probabilistic databases, bag semantics and why provenance are related through similar calculations, which the paper identifies commutative semirings as the right algebraic structure. It then argues that a symbolic representation (polynomials) of semiring calculations is what is needed to record, document and track RA querying from input to output for applications which require rich provenance information.</div>
  <div>Response:</div>
  <p class="block">Since this is a theory paper, the results are demonstrated through proofs over the proposed theorems. First, based on the proposed polynomials with integer coefficients for provenance semirings, the authors show that positive algebra semantics for any commutative semirings factors through the provenance semantics. The authors also extend their approach to recursive datalog queries by combining semirings with fixed point theory.</p>
  <div>Comments:</div>
  <p class="block">Enforcing Data Protection Regulations can be formulated as a provenance problem. Provenance can be used to enforce privacy policies over data, such as preventing sensitive data or any data derived from it from leaving some defined bound over a system.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">This paper prove that the relational algebra positive is a semiring of polynomial. And the paper extend this to datalog and semiring of formal power series. It demonstrate that it is the same as for standard set semantics.</div>
  <div>Response:</div>
  <p class="block">This paper gives a new view of positive relational algebra. It models it as a semiring. The union, projection, and selection are +. And join is *. It proves the positive relational algebra has the property of the semirings. To match the property of provenance that each tuple would contribute to the result, it proves that the semiring is polynomial by building homomorphism between any Evaluation mapping X-&gt; K to a semiring with polynomial computation.

Then, the paper builds the relationship between the datalog and K relations. Since the datalog is recursively processing, here the paper introduces the w-complete semiring and w-continuous semiring.  Here polynomial is not sufficient in define the datalog infinite sums. So the paper introduces one new feature again - formal power series. Then, with this model, it is easy to come up with the algorithm to compute the provenance in datalog.</p>
  <div>Comments:</div>
  <p class="block">This paper is more thereotical. It is much clear to read than the system description paper. It gives a nice definition about the provenance 
</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">This paper suggests a comprehensive provenance representation that uses semirings of polynomials, extend these considerations to datalog and semirings of formal power series, and give algorithms for datalog provenance calculation as well as datalog evaluation for incomplete and probabilistic databases.</div>
  <div>Response:</div>
  <p class="block">They solved the problem of giving semantics on K-relations to datalog queries with the expression of polynomials with coefficients in an arbitrary commutative semiring K</p>
  <div>Comments:</div>
  <p class="block">.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">The paper shows how different "tagged" relational algebra (where each row is tagged with some value, with rules that govern how these tags interact with RA operators) are equivalent to commutative semirings. All commutative semirings are homomorphisms of polynomials and can thus be represented as polynomials (which essentially record *how* the rows affecting the query were derived). They also extend to this to relational Datalog queries </div>
  <div>Response:</div>
  <p class="block">The problem here seems to be mostly a theory problem: we have four different applications where we need to somehow "tag" rows with more information (bag semantics where we tag rows with counts, probabilistic queries where rows have probabilities associated with them, maybe tables where rows have questionable identity, and lineage where we want to keep track of where rows come from). These all have similar rules governing these "tags", so we want to generalize them to hopefully gain a better intuition about what exactly is going on (and also apply it to new scenarios).</p>
  <div>Comments:</div>
  <p class="block">- This is a super cool idea, and I liked the connection to abstract algebra. The exposition could use a little more "this is important because ____", while now it just feels like we're exploring because it's theoretically elegant. The exposition's a little weirdly written (it mostly feels like just a pile of jargon, which for me even though I've studied abstract algebra before), although I'm not sure how to do better (b/c the entire point of the paper is to connect things with this formal mathematics).

- It's pretty little unclear to me how to extend this analysis to support other operations (mostly aggregations and set differences), which breaks the elegance a little bit.

- I'm a little unclear on what's going on in section 9 -- it feels pretty out of place with the rest of the paper.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">Provenance Semirings defines a general theoretical abstration for provence: It does not delve into how to compute provenence nor discuss the types of annotations one can make to keep , but instead defines provenance as the semi-ring of annotations where joins are multiplication and project/union  is addition. Examples are provided on how the abstraction applies to provenance on datalog and incomplete databases. </div>
  <div>Response:</div>
  <p class="block">In the paper, each tuple has some kind of annotation - the algebra defines the type of provenance that can be performed, while changing the actual annotations changes the domain. One frequently mentioned application of provenance is in terms of confidentially classification. The scenario is that each record in a table is labeled with different levels of classification, such as top secret, secret, declassified and inaccessible. Each label and queries is a part of a semiring. It is then possible to derive the classification of the results of queries, so that only it is possible to limit results a viewer get to tuples that can be derived from information they can access. 

Another common application, briefly mentioned in the paper is probabilistic tables. In this scenario, each tuple is assigned a probability of it being true. With provenance, the probability of a tuple in the result set is simply the evaluation of its provenance polynomial. 

Another concrete example is perhaps sensitivity analysis. With provenance, it would be possible to quickly see how results would change if certain tuples are removed from the original dataset. People performing analysis on dataset reliability could thus add and remove attributes/tables to view resulting changes. 

Last but not least, as mentioned in smoke, provenance could be used in visualization - frequently it is necessary to know the source of a visualization, information that provenance could provide. </p>
  <div>Comments:</div>
  <p class="block">The formalism was a little overwelming, and it took some outside reading to understand how the algebra works on datalog. The part on formal power series and its computation was also hard to understand. Overall, it is very neat how by simply examining the polynomial, it is possible to infer exactly how a row is computed. </p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>State-of-the-art</h5></div>
  <div class="block">Provenance / lineage describes the origins and the history of data in its life cycle. This paper gives algorithms for computing provenance and extends results to datalog queries.</div>
  <div>Response:</div>
  <p class="block">I think the state-of-the-art turns to focus on continuous queries over data streams, which means to compute provenance under dynamicity and heterogeneity. Qian Liu et al. in Provenance Management over Linked Data Streams proposed a new approach to provide a dynamic provenance trace of queries. Specifically, they suggested continuous provenance polynomial, the difference between the continuous and the original paper is that they add a corresponding timestamp to each element within the provenance polynomial. Besides, another aspect is to rank tuples based on their importance of their connectivity within the provenance graph. Zachary G. Ives et al. extended the ProQL provenance query language to support a wide array of random walk algorithms in a high-level way, and identify opportunities for query optimization.</p>
  <div>Comments:</div>
  <p class="block">Provenance reminds me of blockchain. For example, I think the sell of one product (an output tuple) could be related to the join of many "responsible tuples" in supply chain. And one common use case would be querying the overall profit that one product could bring to all blocks on the chain. By doing that we could model the query as datalog with bag semantics.
</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">The paper generalizes the relational databases calculation for incomplete database, probabilistic database, bag semantics and why provenance to be expressible by a particular class of semirings polynomial. It also gives algorithms for calculating datalog provenance that can terminate even in the case of infinite multiplicity. </div>
  <div>Response:</div>
  <p class="block">As the author states in the Future work, an immediate research could be to explore the provenance of databases that are not relational. It makes sense because the polynomial and formal power series annotations are universal expressions as the factorization theorem indicates that any query in generalized relational algebra can be expressed by applying valuation on the abstractly-tagged k-relation. In paper, it mentions the research of AI on constraint satisfaction problems and claims that there is a deep connection with k-relations in this paper. In particular, the CSP constrains over semirings are identical k-relations, and relational join and projection are the same as constraints over semirings. The use of fixed point on computing semirings for joins and projections is also the same as the one used in Semiring-based constraint logic programming: syntax and semantics. Thus, a research could be using similarities in the AI on CSP and this paper to see how to incorporate each other.</p>
  <div>Comments:</div>
  <p class="block">A concept that is related to the provenance is annotation placement problem. The problem is that given a source database I, a query Q, a view V=Q(I), and an annotation of some place v in V, then decide whether there exists a location in I to place an annotation v such that v propagates the minimal number of output locations. In general, it studies backwards propagating annotations from output of a query to the database.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">This paper proposes that the calculations for incomplete databases, probabilistic databases, bag semantics, and why-provenance can be represented using semirings, which define a set of operators that can be used to perform computations over the course of a query on annotations that have been added to tuples. Essentially, this allows a query to represent not just the result of a calculation but how that calculation was derived, as well. The author then apply and illustrate these principles using examples in Datalog.</div>
  <div>Response:</div>
  <p class="block">Since provenance tracks the operations applied to a given field in the course of a query, perhaps the notion of semirings could be used to translate certain types of UDFs into queries and optimize them after-the-fact by working backwards from the semiring.

The paper also points to using provenance for debugging purposes, so perhaps some research into the best interface to represent provenance to the user might be warranted.</p>
  <div>Comments:</div>
  <p class="block">This paper was overall very difficult to understand - maybe its my lack of background knowledge in the topic, but I had to do a bit of research into the notion of a semiring, and even after looking up that and other numerous foreign topics its tough to figure out how the notion of a semiring actually applies. I dont think this is a problem with the paper; rather, I wonder if this is really a great introductory paper to the subject of provenance.

It seems like cost estimation could be viewed as a form of provenance - every operator performed in a query incurs a certain cost, and when these operators are chained, the costs must be scaled appropriately (for example, a join may cost more depending on where in the query plan it falls).</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Hater</h5></div>
  <div class="block">The paper demonstrates a way to use polynomials to capture provenance of tuples. Specifically, when tuples are tagged, we can track which output tuples come from which input tuples and also track the different ways that tuples can be produced.</div>
  <div>Response:</div>
  <p class="block">It is unclear how the initial provenance information is gathered. In Figure 5, we are given tables (a) and (b) which make finding (c) straightforward, but how do we expect to gather the information in (a) and (b) initially?

Also, it is unclear how this information should be stored and how it will be updated. While it is an interesting conceptual model, are these polynomials stored alongside the data? Are the polynomials updated with more terms if more contributor tuples are added, or are they strictly for static datasets? And if they are for static datasets, why do we need to track provenance? To what extent is it valid to use the polynomials algebraically, and when, if ever, does that break down? Since relational algebra queries with difference are not supported, is it possible to violate that with operations on the polynomials?
In cases where there are infinite sums then do we store that information differently than standard polynomials, and do the same operations apply?

Also, if many tuples have the same tag, the polynomials become less and less useful as the provenance becomes more and more generalized. </p>
  <div>Comments:</div>
  <p class="block">http://www.cs.ox.ac.uk/dan.olteanu/papers/oz-tapp11.pdf 
Also read this paper for more context on what these polynomials could do: the idea that factorizing these polynomials and doing other mathematical operations on them is meaningful is pretty wild- it is a really clever way of creating more efficient, faster subqueries and allows exponential savings on time complexity of computing the polynomial.</p>
  </p>
<hr />
