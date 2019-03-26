---
layout:page
---



# Datalog Primer

Syntax

        A :- B1,... Bn

* Atom: a predicate symbol/function + its arguments
* Ground atom: all args are constants
* Extensional predicate: source tables of database
* Intensional predicate: derived tables aka views
* EDB: extensional db: source data
* IDB: intensional db: derived predicates
* Source DB: database of all atoms
* Active domain: set of all constants in a database.  DB must be finite in datalog
* *Range restriction property*: every variable occurring in the head of a rule must 
  also occur in a predicate atom in the body, or be equivalent (modulo the comparisons 
  in the body) to a variable occurring in a predicate atom in the body.

3 Semantics (first order logic FOL) for datalog programs


* Example program

        r1 reachable(X,Y) :- link(X,Y).
        r2 reachable(X,Y) :- link(X,Z), reachable(Z,Y). 
        query(X,Y) :- reachable(X,Y).

* Logical model theoretic semantics

        ∀X∀Y   (link(X,Y) --> reachable(X,Y))
        ∀X∀Y∀Z (link(X,Z) ^ reachable(Z,Y) --> reachable(X,Y))

  Given source db I and program P, a model of P is instance I' superset I 
  that satisfies all above logic rules.  Of all possible models, there is 
  a *minimal model* that is smallest.

  * For monotone programs there is a _Unique_ minimal model
  * Theorem: for any core program P and source I, there is a minimal model `P(I) = I'`
    that is polynomial in size of I
  * States the result, but not procedural definition

* Least fixpoint semantics
  * equiv to Logical semantics, procedural
  * *Immediate Consequence Operator T_P* maps I to I' based on rules in P:

        A is an immediate consequence of I, P if:
        1. A is a ground atom
        2. A :- B1, ..., Bn is
           - a ground instance rule
           - B1,...Bn are in I

  * T_P is monotonic
  * Fix point: T_P(I) = I
  * Least fix point: subset of all fix points, equiv to minimal model

* Proof theoretic semantics
  * Given atom A, its existance can be derived with a proof tree whose leaves are ground atoms

        A = reachable(a,c)
        Proof:
          link(b,c)           given
          reachable(b,c)      rule
          link(a,b)           given
          reachable(a,c)      rule

  * Set of atoms that have proofs

Negation (!datalog)

* Core Datalog is monotonic e.g., no updates (delete + insert).  Adding negation can be
  unsafe because 
* The following doesn't have a unique minimal model

        p :- !q
        q :- !p

  both {p} and {q} are minimal, yet proofs for either are not finite

* Add negation using two steps: *Semipositive datalog* and *stratefied !datalog*

Semipositive datalog

* negation only allowed in rule body on EDB predicates

        reachable(X,Y) :- link(X,Y).
        reachable(X,Y) :- link(X,Z), reachable(Z,Y).
        indirect(X,Y) :- reachable(X,Y), not link(X,Y).

  `indirect` depends on negated atom
* Safety condition: Every variable in body must be in 1+ positive atoms
* Polynomial time (wrt instance size) evaluatable

Stratified Negation

* Following is not semipositive because `not reachable` is over an intensional predicate

        R1 reachable(X,Y) :- link(X,Y).
        R2 reachable(X,Y) :- link(X,Z), reachable(Z,Y).
        R3 node(X) :- link(X,Y).
        R4 node(Y) :- link(X,Y).
        R5 unreachable(X,Y) :- node(X), node(Y), not reachable(X,Y).

* Desired evaluation: evaluate all non-negated rules (R1-4) until fix point,
  freeze database so all IDBs are EDBs, then evaluate R5 via semipositive logic.
* Stratification: `P` rewritten as sequence of _semipositive programs_ `P1,...Pn`
  where each all IDBs of each step are "frozen" before starting next step

        A :- ...B...    if A in Pi, B in Pj -> i ≥ j
        A :- ..!B...    if A in Pi, B in Pj -> i > j

* P that can be stratified is called *stratifiable*.  
  * e.g., `p :- not q.  q :- not p.` is not stratifiable
  * Can be syntactically determined
* Theorem: All stratifications of a program yield the same result.  (WHOA)
* Computing stratas (Linear time wrt #rules)
  * create dependency/precedence graph.  
  * compute strongly connected components.  Each is a strata
  * any cycles contain negation edge -> not stratifiable
  * topologically sort connected components
* Expressiveness

        Stratified !Datalog = polytime queries on ordered databases. 
        I.e., any query computable in polytime on an ordered database 
        is expressable by a stratified !Datalog program.

        i.e., exactly all tractable queries (theoretical perspective)

  * Caveats
    * _query_ = strict logical expressions over database tables.  Doesn't
      include functions nor arithemtic! e.g., SUM, AVG, COUNT
    * Depends on the database being *ordered*.  
    * A polytime query language for unordered databases is an open question.

Aggregation

        reachable(X,Y) :- link(X,Y).
        reachable(X,Y) :- reachable(X,Z), link(Z,Y).
        summary(X, count<Y>) :- reachable(X,Y).

* aggregation function: f: bags of domain vals -> domain vals
* Rule head can include aggregation functions
* *Range-restriction property*
  * _every_ variable in rule head must appear in the body
  * No variable in agg term of head can occur as an ordinary term:
    e.g., summary(X, count<X>) not allowed
* Challenges

        p(X) :- q(X).
        p(sum<X>) :- p(X).
  `p` is an infinite table with no fix point
* Stratification is same as for negation with an additional rule:

        A :- ...B...    if A has agg() and in Pi, B in Pj -> i ≥ j

  Extend cycle detection with aggregation to test for non-stratifiability

Aggregate Selection

* Push down filters to inputs of aggregation for extrema operators e.g., min/max
* Don't completely understand the selection rule:

        Selections s1 = path_s1(X,Y,P,C) : groupby(path_s1(X,Y,P,C),[X,Y],min(C))

Functors

* uninterpreted functions e.g., f(x,y) is the value f(x,y), not an integer (if f = sum, x,y = 1,2)
* usage

        path(S,D,P) :- link(S,D), P = [S,D].
        path(S,D,Z,P) :- link(S,Z), path(Z,D,Z2,P2), P = [S,P2].

  `[_,_]` is a list concatonation functor
* If we want to split `emp(name, salary)` into `names(id, name)` and `salary(id, salary)`, use a 
  _skolem_ functor

        names(f(name,salary), name) :- emp(name, salary)
        salary(f(name, salary), salary) :- emp(name, salary)

  Logically, it changes the first order statement

        ∀x ∀y (emp(x, y) → ∃z names(z, x) ∧ salary(z, y))

  And gets rid of the existential `∃` by fixing a value to `z = f(x,y)`

        ∀x ∀y (emp(x, y) → names(f(x, y), x) ∧ salary(f(x, y), y))
* Functors make datalog turing complete (Datalog was originially prolog w/out functions)
  * Bud now can't guarantee termination
  * _weakly acyclic_ test: similar to dep graph for aggregation to check for functor-based cycles.
    1. for each IDB predicate `p` with `k` verticies, (p, i)...(p,k) are nodes
    1. (p,i) -> (q,j) if `p :- ...q...` where i'th term in p is same as j'th term in q
    1. label edge * if i'th term in p is a functor and j'th term refs one of the functor's arguments


## Query Execution


Topdown vs bottom up

* Topdown: start with query + constraints (on allowed values of variables), and propogate constraints through the program until every free variable is bound via a set of constraints 
* Bottom up: start with base facts, and expand set of facts using rules
* Three representative approaches
  * Seminaive bottom up 
  * Query/Subquery Top down 
  * Magic Sets Rewrites ("sideways message passing")
* Main challenge: how to execute to minimize recomputing redundant facts, and facts that are not in the final result?


### Naive/SemiNaive

Naive approach straightup recomputes everything all the time

* Start with database instance D_0, program P

      D_0
      until D_i = D_{i-1}
        D_i = P(D_{i-1})

SemiNaive leverages fact that P is monotone to only propagate changes

* proof tree doesn't change for most ground atoms, so focus on changes to facts rather than facts themselves
* Notice similariy with Naiad
          
      Δ_0 = D_0
      until Δ_i is empty
        Δ_i = P(D_{i-1}, Δ_{i-1})
        
* Consider the following program.  pi is a predicate, qi is a fact (tuple)

      P = p1, .. pn, q1,... qm

      Let's just say
      Q = q1... qm

* We know the following, where δi are tuples added to pi after ith iteration

      p_{i+1} = p1_i, ..., pn_i, Q
      and
      p_{i+1} = pi U δi

* Thus

      p_{i+1} = (p1_{i-1} U δ1_{i-1}), ..., (pn_{i_1} U δn_{i-1}), Q

* Given δs for a given table, we can compute the new table as:

        p_{i+1} = p_i U Δ_i
        Δ_i = δ1_{i-1}, p2, ..., pn_{i_1}, Q
        ...
        Δ_i = p1_{i-1}, ..., δn_{i-1}, Q

        where

        δ_i = Δ_i - p_i

        Since we know that

        p_i U Δ_i == δ_i U p_i


* Pattern: Rule body basically joins.  Thus, a generalization to N-way joins.
* This is a generalization of the ideas behind symmetric join!
  * S JOIN T
  * Build hash tables on S, T seen so far
  * New records for T probe S hash table
  * New records for S probe T hash table

        Q(a, b, c) = S(a, b), T(b, c)
                   = S_i(a, b)  T_i(b, c) U 
                     δS_i(x, y) T_i(y, z) U
                      S_i(m, n) δT_i(n, o)

* When we discuss recursive WITH queries, it's usually wrt one relation.  
  * There's a special case here, where p is recursive on p

        p   = p1, ..., p, ... pn

        p_{i+1} = p_i U Δ_i
        Δ_i     = p1_{i-1}, ..., δp_{i-1}, ... pn_{i-1}
        δp_i    = δp_{i-1}, Q

### Top Down Evaluation

Main idea

* Syntactically rewrite program to be "more efficient"
  * Start with head of query rule.
  * Augment by binding variables to constants based on selection predicates over the query head
  * Propogate constraints through to body of rules until proof tree is built
* Properties
  * Only derive tuples relevant to query result
  * "pass" information (constraints)
    * top-down information passing: from head to body rules
      * aka selection pushdown
    * sidways info passing: from left predicate in body to right predicate
      * take selection info from outer join relation and filter inner relation
      * semijoin trick
      * why join order matters

Example

    r1: R(X, Y) = L(X, Y)
    r2: R(X, Y) = L(X, Z), R(Z, Y)
        Q(X, Y) = R(X, Y)
        Q(1, Y)

Terminology

* Distinguished query atom: atom with bound variables: Q(1, Y)
* Candidate rules: head atom unified with distinguished query atom
* Unify: basically "apply selection"
* Adorned predicate: Q[bf](X, Y)   
  * b: first argument is bound based on a binding constraint
  * f: second argument is a free variable
  * Syntactically determined based on program
* Binding relation: mapping from bound variables to their values (the constraints)
  * { (X->1, Y->2), (X->1, Y->3) }
  * `in_R[bf](V)`
    * R is the predicate name
    * V is the binding relation, with variables for ONLY the bound variables
    * `in_R[bf](X)` denotes head of a rule that binds `X`
* Supplementary relation: for passing from jth atom to j+1th atom
  * `sup^i_j(V)` is the input binding for jth atom of ith rule
  * if j is the last atom, then it corresponds to result of the rule (passed to head atom)
  * `sup^i_0(V)` is the input binding for left-most atom


Example 

    R(X, Y) = L(X, Z), R(Z, Y)
    R(1, Y)

    top down info pass:

    R(1, Y) = L(1, Z), R(Z, Y)

    eval L since it's a base relation

    L(1, 2), L(1, 3) matches

    R(1, Y) = L(1, 2), R(2, Y)
            = L(1, 3), R(3, Y)

    R(2, Y) and R(3, Y) are candidate rules now 
    since they are heads and distinguished


    

### Magic Sets

Main idea

* Bottom up eval, but with sideways info passing to restrict join cardinalities

Adorned Exampl

Start with adorned program

    ar1  R[bf](X, Y) = L[bf](X, Y)
    ar2  R[bf](X, Y) = L[bf](X, Z), R[bf](Z, Y)

Create inital binding predicates

         in_R[bf](1)  
          sup^1_0(X) = in_R[bf](X)
          sup^1_1(Y) = sup^1_0(X), L[bf](X, Y)

          sup^2_0(X) = in_R[bf](X)
          sup^2_1(Z) = sup^2_0(X), L[bf](X, Z)
          sup^2_2(Y) = sup^2_1(Z), R[bf](Z, Y)

Recursion implies that supplementary relation for R also feeds input bindings

         in_R[bf](X) = sup^2_1(X)

Finally, get results

          R[bf](X, Y) = sup^1_2(Y)
                      = sup^2_2(Y)

Semi-naive evaluation naturally filters out irrelevant facts!

* why semi-naive eval?  more efficient from systems perspective?  Easier to implement, with same guarantees
* Input and supplementary predicates are called "Magic predicates"

Concerns: managing and applying constraints is not free!

* High level
  * Managing: representing binding tables
  * Applying: equi-joins
* 3 concrete issues
  * Eval Order: How to order the atoms in a rule body to most constraint the computation?  (AKA join ordering)
  * Which subqueries: Which atoms to even create binding predicates for?  Their selectivity should be "worth it" compared to cost of computing it
  * Which attributes: Which variables to bind that minimizes size of bindind predicates 


Filter-Join for R join S

* Create binding predicate for S
* Project R based on join attrs, filter S using it

    R(X, Y), S(Y, Z)

    into

    R(X, Y), sup(Y), S(Y, Z)


## Extensions

* Statelog: Explicit notation of state. State is timestamped logically using natural numbers. 


        State constant 0, unary function +1, state variable S
        The state terms set _S_ is the least set s.t.,

          - [0]∈S
          - [S]∈S
          - [T]∈S --> [T+1]∈S

        Atom: [T]p(x1,..,xn)
        xi:   constant or variable
        Literal: Atom or its negation
        Rule: [S+k0]H :- [S+k1]B1, ... [S+kn]Bn

        Head is Atom, body are literals
        Time can be _anything_
  
* Dedalus differs in two ways
  * It restricts temporal rules:
    * `k0 = ... = kn`
    * `k0 = kj+1, k1 = .. = kn`
    * `k0 = kj+n, k1 = .. = kn` where `n` is _some_ value greater than `0`
  * Persistence is explicitly defined as `p(..)@next :- p(..)`

