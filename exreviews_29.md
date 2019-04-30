---
layout: page
---


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

<h3>--</h3>
<p class="block">A framework that leverages the provenance of visualization construction to enable queries on similarity between visualizations/construct similar ones</p>

<h3>--</h3>
<p class="block">streamlines and automates the common use case of having to do similar data transformations on differing starting datasets.</p>

<h3>--</h3>
<p class="block">1. creating visualization by taking advantages of lineages and changing pipelines. 2. Using query-by-example method to extend the scalability</p>

<h3>--</h3>
<p class="block">Introduces and gives examples of the notion of "query by example," a less technical method of defining queries.</p>

<h3>--</h3>
<p class="block">use provenance information to simplify and automate the construction of new visualizations</p>

<h3>--</h3>
<p class="block">This paper proposes a new framework that leverages visualization provenance to simplify and automate the construction of new visualizations.</p>

<h3>--</h3>
<p class="block">Build a framework that uses analogy to automate construction of data visualization</p>

<h3>--</h3>
<p class="block">leverage provenance information to simplify and automate the construction of new visualization by analogy</p>

<h2 id="reviews">Reviews</h2>

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">The main issue is that managing data flow pipeline can be difficult/ repetitive when the end goal of the end user is to create visualizations to analyze different datasets/processing steps. This is common in experimental sciences where numerous variables are tested, and visualizations used to discover significance. There exists system that enables visualization creation from dataflow which also keeps track of provenance of data transformations (Data explorer for pure vis, VisTrials have provenance and vis). This paper builds off VisTrials which captures changes in dataflow. 

The key idea is that given we have the provenance that leads to visualization, we can create a framework to query for pipelines with similar substructure. This can be useful to find datasets that matches certain criterias scientists look by examining their visualizations. It is also possible to find/approximate queries that are similar to a specified query, then change the result pipelines to that they result in similar visualizations for semi- automated visualization generation (visualization by analogy). The key idea behind matching is to use random walks to approximate neighborhood similarity between nodes.

The paper is mostly a proof of concept - for evaluation, the authors created 3 examples of their frame work - automatically creating new visualizations with different data, comparing different algorithm pipelines and creating a new compounded visualization based on 2 different original base visualizations. Unfortunately, the examples are fairly light on detail regarding in pipeline operators. This is actually a flaw of the paper overall as it does not give a concrete idea of what modules and nodes actually are and what comparisons of pipelines mean in practice. This makes it difficult to evaluate how useful it is to make these comparisons in the first place (eg - need to define the expressiveness). A possible improvement is a more comprehensive survey of operators possible, along with a survey from users of the usefulness of pipeline comparison and generation of similar visualizations. Another concern is that visualization usually need to be perfect for publication etc - a lot of details seem to require the system to make approximate choices (eg with heuristics), which could impact user control over visualizations. 

Overally, this is a fairly interesting system, which could be useful domains with high repetition dataflow visualizations. However, operations need to be better defined. Possible improvements include user customizable similarity algorithms, and more options for automatic manipulation.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">Vistrails is the paper the 1st paper builds off, a system for manipulating data and visualization transformations. It seems to enable things like reading data points, creating polygon mashes from it, and further transformations a the mash. Each transformation and parameters are stored in an XML file, which is the provenance of a visualization.</p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">This paper streamlines the common use case of having to do similar data transformations on differing starting datasets. The particular case given is that of creating visualizations, but it is applicable to any such workflow. This greatly helps in creating reproducibility for processes involving data transformation. 

The paper uses the notion of a pipeline, a composition of functions. Similarly to dataflow techniques, we track data at each step of its progress along some set of transformations and are aware of which data goes into and comes out of each operator. Also, similarly to Datalog, a pipeline can be defined by rules to apply given conditions, and so can be written in Datalog. 

The pipeline is a very user-friendly form of lineage tracking. Unlike other lineage systems we have discussed where data can be annotated with input records, or with prior transformations applied, in this form of lineage we track what input and output records each operator has. 

The main contribution of the paper is that it makes it possible to apply an analogy onto a pipeline which does not perfectly match the domain context of the pipeline being applied. The key insight is that some mapping function can be done which makes this possible. This is an improvement over a subgraph isomorphism, which does not have a good approximation algorithm, because it reduces the search space by only mapping differences instead of entire pipelines. Further, the contribution is that it identifies not only perfect subgraphs but also can score approximate subgraphs, so that approximate mappings are possible. 

The paper could be clearer about what restrictions apply to the functions in a pipeline. According to the paper they need to have perfect inverses. It also seems that they need to do some form of type checking to determine whether the domain context is applicable. The paper as it stands does not compare itself to any benchmarks or other workflows. A comparison to a standard tree diff, including runtimes, would be extremely helpful and prove the authors point about why the VisTrails analogies heuristics are more powerful and computationally better. Also, since the end goal of the paper is time savings for complex workflows, it seems like they should address caching or some other optimization over the pipeline operators themselves. The actual time savings for the user in the current implementation is not having to redraw the pipeline on a new dataset. While this is convenient, it would be great to be able to also run some sort of optimization on applying the differenced pipeline.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">VisTrails works when all the functions to be applied have perfect inverses. It also only applies when the set of possible functions to be applied to a dataset is known.</p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">The problem that this paper tries to solve is that the collection of visualization is huge, thus sometimes during data exploration, user might have trouble finding the correct visualization method. To solve this, the paper proposes that the lineage information that captures the changes of pipelines and could be used to simplify the process. The query-by-example is a semi-supervised learning method to apply the desired visualization method to data. The key idea behind this is that the system needs to learn a program that could modify the pipeline. This idea is widely used like in program-by-example, for example, Excel flashfill uses this idea that takes some output label as the standard and learns the left ouput label according to the history and changes of the pattrns of the input.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">As the previous paper said, vistrials is an open source system that could achieve multiple-view visualizations by lineage and changing pipelines. So I guess the key part for the system is to introduce how the pipeline is optimized.</p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">This paper introduces the notion of "query by example" and "visualization by analogy" in the visualization space. The paper outlines how to compute differences between two pipelines and how to use this difference to transform a third pipeline into another, "analogous" pipeline.

This process is computationally expensive; the authors acknowledge that finding the minimal transformation is NP-Complete and solve this using a heuristic. However, it would have been helpful to have seen some performance metrics for this algorithm to see if it's computationally feasible even after the heuristic.

Although the idea mostly presents its contribution as a way to decrease the technical barrier to data visualization, I'd argue that this idea seems useful even for technical users in highly repetitive query areas, such as data-cleaning. 

Although this paper claims that it allows for a user with a lower level of technical knowledge to create visualizations, it would have been helpful to see at least some sort of minimal user study to evaluate how actual non-technical users interact with the platform.

All in all, this paper seems a bit light on the evaluation.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block"></p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">Creating visualizations is an iterative process were users try out visualizing multiple datasets while applying different techniques with varying parameters until they reach a satisfying view. This work leverages provenance metadata to automate the construction of new visualizations by introducing two techniques: query by example which includes an interface the aids in querying data workflows, and visualization by analogy which is a mechanism for semi-automatically creating visualization by analogy.

Query by example works as follow: the user construct query pipeline, and then this pipeline get matched against previous provenance workflows captured by VizTrails to find a set of similar pipelines.

The second step is to construct visualization by analogy, where the user chooses two pipelines one derived from the other, and which to apply similar changes to one or multiple pipelines (such as to the set of similar pipelines found during the matching process), which has the effect of updating those visualization without the need to modify each one individually as well as constructing complex transformations through chaining multiple analogies to a pipeline.
</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block"></p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">This paper by Scheidegger et al. proposes a new framework that leverages visualization provenance to simplify and automate the construction of new visualizations. It defines a set of operations over pipelines, which are the programming rules for displaying the data, and describes how these operations are used in the query-by-example interface and in creating visualizations by analogy. The paper also presented an implementation of the proposed framework in VisTrails system and motivates how the new dataflow manipulations could provide a simple and scalable interface for constructing visualizations. It concludes with a discussion of the works impact and directions for future work.

The significance I consider with the proposed framework and the system VisTrails that implements the framework is that it supports data exploration through an intuitive interface for users to query and reuse provenance information. First, with the query-by-example interface, users do not have to learn a query language to find matching pipelines, which requires knowledge of provenance that graphically stores structural information. Instead, they only need to construct a pipeline that contains the desired features using the familiar interface they use to build pipelines. This makes the system handy and intuitive for the users. In addition, with the visualization by analogy concept, users could easily modify workflows and integrate new features into existing pipelines by defining the analogy template. This could be done by selecting two pipelines or more pairs (as in chaining analogies) and applying their difference to the pipeline that they want to modify. The more straightforward procedure together with a visual interface allow users to interact with and explore data visualizations with lower level of knowledge. 

In general, I find this paper well presented. It starts by defining theoretical pipeline operation concepts and progresses to their implementation in the VisTrail system. It uses neat mathematical notations and informative figures such that I find little difficulty understanding the procedures. The case studies provided in section 6 nicely demonstrates the capability of the framework. 

One difference this paper exhibits compared to some other papers weve read is that although they all propose a framework and implement it in a database system, this paper does not have an experimental evaluation section. This is reasonable because Id say the major goal of the proposed framework is to improve usability rather than performance. But I wonder if the paper could present some form of simple user study that investigates the users experience with the system. How does it simplify the construction of new visualizations and to what extent. 
</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">This paper describes the design and implementation of VisTrails, a new system that enables interactive multiple-view visualizations by simplifying the creation and maintenance of visualization pipelines, and by optimizing their execution. According to the abstract and introduction, the paper emphasizes the use of visualization trail (vistrail) and the characteristic of the system that clearly separates pipeline specification and execution. The other important feature is the optimization that Vistrail uses vistrail specification to identify and avoid redundant operations. These features provide a scalable and efficient mechanism for generating visualizations.</p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">The purpose of this paper is to make the process of data exploration in lab settings much easier and quicker. A problem exists in current data exploration process is that users need to continuously assemble, modify, and execute the pipeline to get update data visualizations. The state-of-art approach such as Data Explorer enables user to create visualization without further training and efforts, and SCIRun focuses on intentional placement of visualizations and human intervention. However, this paper builds a new framework to generate updated visualizations based on analogy or the provenance. By computing the difference between each pipeline, they define the \delta to be the group of operations between from pipeline a to b. Using the \delta, it can be applied to other pipelines if all the primitives exists in the new pipeline. The idea of using algebraic approach to formulate the visualization problem is interesting, and probably can be more accurate because the matching function now is just aggregate all the scoring functions. There could be some weight difference between each scoring since the visualization difference is not uniform. The paper does not include an experiment, so hard to tell how the framework works exactly. Based on the cases studies, it looks like analogy can be applied to allow multiple pipeline updates, replacing the rendering algorithm entirely, and chaining multiple analogies to get the new one. I think the results can be tested with a user survey or comparing the ease of usage to other state-of-art systems. The future of research can be refining the algorithm to find the best matching mapping with better big-O. If the analogy generates result that is not satisfactory to users, there could be some ways to manual tune the results by setting the scoring. Also, user inputs can be used to guide the matching process, and research could be done about what user inputs are valid and useful. The framework can be applied to data visualization fields. The paper gives examples of scientific research and I think the tool can also be used in business analyst settings such as applying a pipeline of generating annual report from this year to another. The overhead of the system is not mentioned in the paper, there could be high overhead when running the matching between visualizations.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block"></p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">The background of this paper is that visualization is widely used in application for data anlysis. But the way to visualize it is labrious. So the main contribution of this paper is making the construction of new visualization automatedly generated. The method is using the provenance model to get the information of parameters. For the query by example and visualization by analogy, they propose the definition of pipeline and the. They define the distance of pipeline and how to match the pipeline. The distance and match method provide the analogy example. Like in the example the paper gives, they do the function like map(difference()) to find the output of new visualization. For the implementation, they use the vistrail system.They use the traditional graph representation to do the matching and update the pipeline. The limitation of this method would be when there is little similarity or when there exists ambiguity, the output would become wrong. Anther question about this paper, it mentioned scalable many times, but in the content it does not talk enough about how to achieve scalibility by the primitive in the paper. Also, the whole picture of the problem which query by example, it gives a feeling of view maintenance problem. The matching function and difference function may be the most novel thing, It is like you use the matching algorithm to find which view to maintanence - a view selection problem. And the difference function just means you have a view, and you need to do more on the view to get the result you want. So the problem can become a view problem.?
</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">Vistrail has the feature that it separates the pipeline specification from its intance to make it scalable. This like the logical plan and physical plan In database which brings the benefit of independence. Vistrail also has a cache manager which caches the result for overlapping pipelines
</p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />
