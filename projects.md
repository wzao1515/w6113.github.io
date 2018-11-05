---
layout: page
title: W6113 Research Project
---

<center>(This page is in flux)</center>

#### Important Dates 

Percentages are of your total class grade.

* Proposal Presentations    TBA (5%)
* Prospectus       TBA    (5%)
* Poster Session   TBA    (10%)
* Report          TBA     (50%)

#### Overview


The major portion of your grade is based on the research project. Students will organize into teams of 1-3 students and work on a semester long project.  Some possible ideas are [described below](#suggestions).

Good class projects can vary dramatically in complexity, scope, and topic. The only requirement is that they be related to something we have studied in this class and that they contain some element of research -- e.g., that you do more than simply engineer a piece of software that someone else has described or architected. To help you determine if your idea is of reasonable scope, we will arrange to meet with each group several times throughout the semester.

#### Proposal Presentations 

At the beginning of the 2nd to 4th lectures, each group will give a **5 minute presentation** about their proposed project to the class.  The presentation should contain:

* Project idea and hypotheses
* What work must be done and how it will be diveded amongst the team
* How the hypotheses will be evaluated
* Resources needed to complete project

Teams can meet with the instructor after their presentations for further discussion and feedback.

[**Click here to sign up**](https://calendar.google.com/calendar/selfsched?sstoken=UUlmUlc5VDIwWDJwfGRlZmF1bHR8MTUwY2E3NDBiMDNhMTU4ZDIyODhlMjFlZTAzZGMyZTU).  Click "next" until you get to the appropriate week.


<!-- Teams should consist of 1-3 people. In addition, if you have a project in mind, please indicate briefly (1--2 sentences) what you are thinking. We have included a list of possible projects at the end of this document although you are not required to choose from these. -->

<!-- [Click here to submit before class on 2/1](https://goo.gl/forms/j8aXKKtjxH0rzVgx2) -->


#### Prospectus 

Your reserach prospectus will contain an overview of the research problem, _your hypothesis_, first pass at related work, a description of how you plan to complete the project, and metrics to decide _if it worked_.   

<!--A good prospectus is basically the skeleton of the full report.  It is _highly recommended_ that you come to office hours to discuss project ideas before writing the prospectus.-->

Your prospectus should follow the example:

* [Click here for an example prospectus](./files/prospectus/prospectus.pdf)
* [Click here for the tex files](https://github.com/columbiaviz/columbiaviz.github.io/tree/master/files/prospectus)

Submission:

1. Rename the filename of your prospectus to the following format, last names should be in **alphabetical order**. `prospectus_<lastname1>_.._<lastnameN>.pdf`
2. [**Click here to upload the file by 2/11 11:59PM EST**](https://www.dropbox.com/request/Kmymco2V49BS3rSlWfls)


<a name="midpoint"></a>
#### Midpoint Review

The midpoint review will be an in-class exercise where teams will present and receive feedback/suggestions in a "speed-dating" style session.  Details will be released closer to the session.

Meet with your counterpart for 20 minutes - 10 minutes per team.  One team will describe the goal of their project and the current status and challenges, and the other team will listen and provide comments.

For the listening team, ask and provide answers to the following questions:

1. What opportunities/changes that make this work useful and timely? 
2. Why existing approaches fail to make use of these opportunities? 
3. What is the hypothesis?
4. What are the concrete steps to validate the hypothesis?
5. What suggestions did you, as the listener, provide?

#### Poster Session
Your team will prepare and present a project poster at the end-of-course poster session.   This gives you an opportunity to present a short demo of your work and show what you have accomplished in the class!

**Submission**

* Simply attend and present at the poster session.

#### Report

You will prepare a conference-style report on your project with **maximum length** of 15 pages (10 pt font or larger, one or two columns, 1 inch margins, single or double spaced -- more is not better.) Your report should expand upon your prospectus and introduce and motivate the problem your project addresses, describe related work in the area, discuss the elements of your solution, and present results that measure the behavior, performance, or functionality of your system (with comparisons to other related systems as appropriate.)

Because this report is the primary deliverable upon which you will be graded, **do not treat it as an afterthought**. Plan to leave at least a week to do the writing, and make sure your proofread and edit carefully!

**Submission**

1. Rename the filename of your report to the following format, last names should be in alphabetical order. `report_<lastname1>_<lastname2>.._<lastnameN>.pdf`
2. [**Click here to upload the file by 4/30 11:59PM EST**](https://www.dropbox.com/request/9zdikb92vHFFPYtaFF0e)

<!--

<a name="suggestions"></a>
## Project Suggestions


The following are examples of possible projects -- they are by no means a complete list and **you are free to select your own projects**.  In general, projects can be of three varieties:

1. Research project: model an unsolved problem, propose algorithmic solution, evaluate and report findings.
2. Win: pick an existing useful application and a well-recognized metric (latency, prediction, etc) and win against the state of the art.
3. Break and fix: implement a state of the art algorithm on real data, show that it doesn't actually work (results are poor, it's slow, etc), make it work.

#### Data Cleaning

Understand how scientific articles use and talk about data.  Two possible directions:

* Analyze how data is talked about
  * [Viziometrics](http://viziometrics.org/api/) has a corpus of figures from pubMed articles, analyze the way papers describe and talk about the contents of figures.  Is there a universal set of ways that figures are described (e.g., in terms of comparisons? in relative terms? ).  This can serve as the evidence for a new data analysis language.
  * We can help get access to a corpus of ArXiV papers and their LateX files
* Analyze how data is cleaned in practice
  * Analyze the text of scientific journals (science/nature/pubmed/arxiv/biorxiv) to categorize and summarize types of data cleaning applied.
  * or scrape and analyze code bases for cleaning operations


Arachnid is a new explanation engine that automatically generates cleaning programs based on user specifications of data quality.  It is an extension to ideas from [Scorpion](https://www.dropbox.com/s/1v6dcb16r840sdo/scorpion-vldb13.pdf?dl=0).  Contact Eugene for a copy of Arachnid.  Some possible projects:

* Integrate Arachnid into an interactive data exploration interface in a way that the user can clean any part of a visualization without programming
* Implement a fast version of Arachnid in the browser


#### Automatic Interface Generation

[Precision interfaces](https://arxiv.org/abs/1712.00078) automatically generates interaction interfaces from program logs.  It supports any parsable language that can be represented as an abstract syntax tree.  Extend the system in interesting ways 

* Extend Precision Interfaces to support program logs from different languages.  These can be real programming languages (e.g., Python, etc), or information that can be parsed into a syntax tree (e.g., structured requests embedded in network requests generated by a browser client)
* Extend Precision Interfaces to different modalities such as gesture or natural language.  The system has a flexible model of interactions that is agnostic to the mode of input.  
* Embed design heuristics into the interface generation process.  The system currently has a very simple model of "interface complexity" --- make it more real by taking existing HCI research into UI complexity and design into account.


#### Query Engine for Interactive Apps

[Smoke](https://www.dropbox.com/s/6xvg5qkdret60jk/smoke-vldb18-revision.pdf?dl=0) is the fastest lineage-enabled database engine.  It captures the relationships between output and input records as efficient lineage indexes.  It turns out, this can be used to express and speed up interactive applications such as visualizations.  Extend or use it in interesting ways 

* There are a number of compression techniques that are possible to reduce the storage costs.  Explore ways to generate compressed representations that do not increase, or even reduce the overhead of lineage capture.
* Explore the combination of offline data structures such as data cubes and online lineage index structures to further improve query performance.
-->




