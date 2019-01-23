---
layout: page
---

# Introduction


Eugene Wu

* PhD MIT, 2015
* Systems for Human Data Interaction
  * Database systems, data cleaning, explanation, visualization
* Other projects
  * WebTables
  * SASE
  * Neural Network debugging
* ewu@cs.columbia.edu

Prereqs

* Should understand 4111 cold
* Ideally have taken 4112, which discusses DB implementation details
  * we will go over some important concepts since 4112 is not taught this semester
* See Ramakrishnan for basics

Goal

* Read variety of classic and current DB research topics
* Teach you how to do DB research (read and ask questions)
  * Being able to read work: identify the good and bad
  * Being able to identify trade-offs: systems usually != proofs
  * Being able to ask questions and extrapolate

How?
* In class
  * Lecture + discussion
* Assignments (forcing functions)

Lectures

* Reading is hard.  We will go over ideas and critque the papers in class
* This is the time to clear any doubts when reading the paper
* It is good to be wrong or confused
* 1/2 pg or less paper reviews day before class
* Recommend writing full paper reviews

Participation

* Paper reviews BEFORE class
* Discussion on Piazza
* Scribe for each lecture
* Discussion in class: if uncomfortable asking in class, send comments/questions to me before lecture
* Program Committee
  * review research project papers beforehand
  * In class discussion of research project paper first drafts (authors leave the room)
  * write shared review for each project

Assignments

* Teams of 1-2
* Hack on a simple Python read-only database engine: DataBass
  * 2700 LOC
  * see the basic end-to-end structure 
  * parsing, disambiguation, plan optimization, interpretation, compilation
  * implement some ideas from papers

Research projects

* Teams of 2-3
* Pick a question answerable in 3-4 weeks
* Implement ideas, and evaluate it to understand if it answers the question
* Get feedback, and learn to give feedback
* Write a research paper ~10 pages, ACM format
* Steps
  * Proposal/prospectus
  * Meet with me
  * First draft
  * Presentation
  * Program Committee
  * Final Paper
* Evaluation 
  * Clear hypotheses?
  * Contextualized with existing work?
  * Does methodology make sense?
  * Quality of results and understandings
  * Presentation
* What is research?
  * Not engineering
    * I built a cup
  * About answering a question (the "science")
    * Is this cup better? (than what?)
    * Is this cup better than this bowl?
    * Why is this cup better?
    * When is this cup better than this bowl?
    * There are hundreds of cup designs, how do they compare?
    * Could this cup have been built with dirt?
    * Is this the best way to have built the cup?
    * This cup is used for drinking.  What is the ideal way to drink?  How can we make that a reality?
  * Engineering is the mechanism to answer the question
    * Building the prototype and experiment framework  is means to the end
    * The research is corretly answering the question
  * Better understand the numbers
    * goes to point 1.  "I made a graph" is not research

Rough Grades

* 70% research project
  * 40%: Final paper
  * 10%: Prospectus
  * 10%: Program committee
  * 10%: Presentation
* 15%: Participation
* 15% assignments

* Relationship with 4112:
  * DB implementations dives into the Cow Book's details of locking, recovery (simplified ARIES), B+ indexes, etc
  * Will not replicate too much content from 4112
  * Focus will be on reading the research papers and may go over some 4111 content as background

TODOs

* Specialized for this week
* HW0  
  * Databass: expression compilation
  * Paper review
  * Released Weds 10AM
  * Due Sunday midnight
* paper review
  * released: Tuesday 8PM
  * due: Thursday noon

# Short DB History

* 60s:
  * CODASYL (Charles Bachman Turing #8)
  * IMS: hierarchical (think file system)

    suppliers -- parts

    * can't have parts without a supplier, and vice versa
    * redundancy if many to many relationship
  * navigational pointer-chasing data manipulation language
    * aka for loops.  loops are fine (as qcompilation will show), but not for specification!
    * doesn't separate the data from the physical representation/execution
* 70
  * Codd's paper (Turing #18)
    * Set-based DMS
    * Relationships are just sets
    * Data independence
    * Why do you want data independence?
      * When app changes slower than SW/HW environment
      * When else? Think cloud.  Menus abstract the food from the creation.  Instruction sets
* Mid 70s: the two teams
  * Ingres: Berkeley 74-77
    * Stonbraker (turing #32) and Wang 
    * influenced/begat ingres, britton-lee, sybase, ms sql server, PACE, tandem
  * SystemR: IBM almaden
    * big research team: 15 phds
    * begat: DB2, oracle, HP allbase, tandem
    * Jim Gray (turing #22)
  * Proved that the theory was practical.  Spawned RDBMS market and all software on top of it
  * We will read these papers
* 80s: The Market for "get DBs to work"
  * Oracle takes reads SystemR papers and goes to market
  * IBM releases several RDBMses, didn't want to cannibalize IMS initially. eventually releases DB2
  * Jim Gray and others join Tandem
  * Stonebraker makes Ingres
  * Britton/Lee from Ingres create Britton-Lee
  * Epstein leaves BL to create sybase (bought by SAP for 5B)
  * Wang creates PACE
  * Informix starts
  * Teradata started by caltech alums based on networking tech.  Moves into parallel DB space
  * SQL becomes the standard.  DB2 wins over IMS at IBM
* 90s onward
  * IMS still used as legacy system.  Cash cow
  * Relational is a commodity.  
    * MS, Oracle, IBM arguably leaders
    * Cloud is big: Amazon, Google, MS arguably leaders
    * MySQL/PostgreSQL/SQLite popular open source
    * Others surviving/niche
* Today: scale and specialization.  Market for "get DBs to work at scale" and "for X"
  * New wave of distributed/scalable/forX database companies
* The big money arguably in apps built atop databases
  * Resource planning/Inventory tracking
  * CRM: Salesforce, IBM, 
  * Supply chains
  * HR/Direct Marketing/Call centers/automation/etc
  * Web companies: Facebook/etc
  * App generates SQL to access/query/modify the universe of data
  * DB makes easy to write these apps correctly

# Life of a query from Architecture of a database system

User wants to run a query. Now what?

* Connection: Client establishes connection to DB server
  * DB allocates memory to track connection info, recent queries/state, result cache
* Admission Control: start running query, or wait, or reject?
  * Give query resources -- a DBMS thread/threads depending on model
* Query Processor: compile text string --> abstract tree --> logical plan
  * disambiguate table and attribute/col references

          SELECT a, b
          FROM S, T
          WHERE S.x = y

  * Rewrite rules
  * Optimize into physical plan
  * Run the plan
  * Plan == dataflow graph (tree)
  * Bottom (leaves) of plan are access methods 
* Transactional Storage Manager
  * complicated, said to be "monolithic"
    * Buffer manager
    * Log manager
    * Lock manager
    * Access Methods
  * ways to read/write to files (tables, indexes)
  * Set transaction level for xact
  * Buffer manager decides which pages to move between storage hierarchy levels
  * Acquire appropriate locks before accessing/updating data
  * Make sure log is correctly written to for recovery
* Results:
  * Once TSM has acquired locks, setup xact, loaded into buf manager
    can return control to physical operators/executor
  * Operators try to minimize memory copying, 
    may allocate heap memory to built tmp data structs
  * results placed in buffer to send back to client
* Finally:
  * connection closed
  * TSM cleans up, free tmp memory/resources

We will look at many questions

* Macro design choices 
  * system r/postgres papers
  * beyond RDBMS architectures
  * languages beyond relalg/SQL
* Micro designs
  * Joins
  * Different access methods
  * How do operators communicate?
  * Range of optimizations
* Physical DB Design
* Tentative
  * AQP
  * X in DBs

Will contrast with general systems concepts when appropriate



The DBMS is one of the two major classes of software systems.  Other is OS.

* DB topdown design: start w/ semantics and declarative API.  Focus on correctness
* OS bottom up: elegant programming mechanisms (that compose, simple/minimal, etc)

# Resources

Reading papers

* [S Keshav](http://blizzard.cs.uwaterloo.ca/keshav/home/Papers/data/07/paper-reading.pdf)
* [M Mitzenmacher](http://www.eecs.harvard.edu/~michaelm/postscripts/ReadPaper.pdf)
* [A Fedorova](http://www.cs.sfu.ca/~fedorova/Teaching/CMPT886/Spring2007/howtoreadpapers.html)

Research

* [You and Your Research](http://www.cs.virginia.edu/~robins/YouAndYourResearch.html): there are big problems and small problems
* [Research as Optimization](https://researchsetup.github.io/researchmodel)


