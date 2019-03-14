---
layout: page
---

# Streaming and Incremental Processing

Student questions

* Not clearly about the could result in relation in the paper 
* Not very clearly about the distributed progress tracking. How the nodes know whether there is more input.
* is it worthwhile to have the whole new system of Naiad?  AKA is it inherently a poor UI/UX or just the prototype?

### Background

A streaming perspective on query execution

* Run Q(D) well defined via relational algebra/semantics
* Implemented as query plan P with access methods over D
* If execution model is push based, then access methods push records up the plan
  * What if access method only lets you read from it once?
  * Push from access method is like new record from a stream?
* Query result initially empty (correct if inputs are empty)
  * new records update query result.  
  * only expose result once all inputs are read
* Can we expose intermediate results earlier?
  * Eddies
* How much state do we need to keep?
* Are we restricted to trees plans?
* What core properties do operators need to expose?

Pipelines

* We discussed how aggregation is a blocking operator
  * can't emit values before it sees all inputs
* what if we aggregate on time, and it has some ordering?

Consider recursive queries

		WITH RECURSIVE paths AS (
		  -- initialization
		  SELECT dst, w as totalw
		  FROM edges
		  WHERE src = 'A'

		  UNION 

		  SELECT edges.dst, 
				 paths.totalw + edges.w as totalw
		  FROM paths, edges
		  WHERE paths.dst = edges.src
		)
		SELECT *
		FROM paths

* run initialization query as 'input', call it v0
* run recursive query component using edges@v0, collect new records as v1
* repeat with @v1, @v2,... @vi until no more messages
* This is bulk synchronous processing


Consider Spark queries

* Run page rank using iterative computation
* What does its lineage graph look like after 2 iterations?  10? 100?
  * grows with each iteration -- need to update master lineage graph
* What if input changes (e.g., new record)?
  * need to rerun again
* What about spark streaming?
  * can get it to work if intermediate state is externalized as intermediate RDDs
  * can implement above shortest paths

## OK Naiad

What is Naiad good for, that prior systems could not/had trouble doing?  

* Wants:
  * low latency
  * iteration
  * "consistent intermediate outputs"
* The paper claims
  * batch processing can iterate, but block
  * streamng processing don't do iteration
  * triggers can iterate with no consistency guarantees (what does this mean?)
  * low level infra, not for end-users
* Constraints
  * In memory

Contrast with Spark Streaming (mini-batches)

* input stream.  partition by time into mini-batches
* run spark jobs over batches in parallel (can futher partition if really want)
* output stream of batches.
* compute doesn't cross partition boundaries

Main ideas

* logical hierarchical timestamps
  * `[epoch, (c1,..,ck)]`
  * input attaches epoch e
  * step() function tells system it won't recieve more messages with <= e epoch
* loops 
  * ingress: track this loop.  push new counter
  * egress: done with loop.  pop counter
  * feedback: update counter.
* t1 < t2 based on lexographic order
* for any path o1 ~> o2, we know timestamp at o2 can only be greater or equal to timestamp at o1 for same msg
* given the dataflow graph structure, can deterministically know how any path of operators will change the timestamps 

Low-level API

* operators communicate via async messages and logical queues

              e
        u  -------> v

* this.sendBy(e: Edge, m: Message, t: Timestamp)
  * send m along edge e.  triggers v.onRecv 
  * send immediately
* this.notifyAt(t: Timestamp)
  * wait until I won't see any more messages with t, then call onNotify
  * no more calls of v.ONRECV(e,m,t ′), for t ′ ≤ t
  * allows (logical) temporal buffering
* v.onRecv(e : Edge, m : Message, t : Timestamp)
  * handle a message
  * can only call sendBy and notifyAt with t' >= t
* v.onNotify(t : Timestamp)
  * can only call sendBy and notifyAt with t' >= t


Timestamps

* pointstamp: timestamp + operator/edge
* (t1, o1) could result in (t2, o2) iff 
  * exists path `o1 ~> o2` that updates t1 to be <= t2
  * note this is statically determined
* pointstamp (t, o)
  * occurrence count: number of outstanding events with pointstamp
    * maintain on calls of send/notify
    * active pointstamp if occurrence > 0
  * precursor count: number of active pointstamps that could-result-in (t, o)
    * when (t, o) becomes active, set it to number of preceeding active pointstamps
    * when (t, o) is inactive, decrement all could-results-in pointstamps
  * frontier: precursor count is 0
    * can deliver notification for t

### Example

        in --> A --> Ingress ---> B -----> C ---> Egress --> D --> Out
                              ^              |
                              |___feedback___|

* Messages are (key, value)
* A is a distinct-by-key operator
  * for inputs of (key, value) only emits the first (key, value) record with each distinct key.
* B is projection
  * value = value * 2 
* C is filter
  * if value > 10, send to Egress, else to Feedback
  * sends its output to Egress if the input value is greater than 10, and sends it to F otherwise.
* D is an aggregation operator 
  * sums all values irrespective of key.

What if we inject some records?

        (a, 2), [1]
        (b, 6), [1]
        step()
        (a, 5), [2]

* (a, 2): [1]
  * output of each operator
  * A: (a, 2), [1]
  * B: (a, 4), [1, (0)]
  * C: (a, 4), [1, (0)]
  * B: (a, 8), [1, (1)]
  * C: (a, 8), [1, (1)]
  * B: (a, 16), [1, (2)]
  * C: (a, 16), [1, (2)]
  * D: (16), [1]
* (b, 6), [1]
  * goes through loop once 6 -> 12
* (a, 5) suppressed by A

When does E.onNotify(1) get called?

* on the `step()` call, know epoch 1 is done
* wait until all active pointstamps <= 1 before E are done

Can we implement SQL operators?

* group-by aggregation
* join
* symmetric hash join

Other qusetions

* Is it different than Spark's central
