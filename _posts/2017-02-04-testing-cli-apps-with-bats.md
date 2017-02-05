---
layout: post
title: "Testing CLI Apps with Bats"
date: 2017-02-04 00:01:19
categories: "software engineering"
tags: ["testing", "asaph", "scientific computing"]
---
I've been looking for a good way to test [Asaph](https://github.com/rnowling/asaph), the small machine-learning application I wrote for my Ph.D. thesis.

Most testing solutions I found didn't quite fit what I wanted.  The built-in Python [`unittest`](https://docs.python.org/2/library/unittest.html) framework is my usual go-to.  It's flexible, powerful, and easy-to-use.  Asaph is heavily data-dependent with relatively complex internal data structures and its workflow involves lots of file I/O.  Consequently, I found it cumbersome to write unit tests.

Most command-line testing solutions seem to be focused on testing the interfaces. Most examples I found focused on using `unittest` to test argument parsing with libraries such as [`argparse`](https://docs.python.org/2.7/library/argparse.html). Other options include testing interactive CLI apps such as those that prompt the user or use something like [`curses`](https://en.wikipedia.org/wiki/Curses_%28programming_library%29).  Asaph isn't really interactive, though.

Asaph's commands form a workflow.  The user first calls Asaph to convert data to its internal format.  The user then uses Asaph to train a Logistic Regression model or Random Forests models with different numbers of trees.  The user can then call Asaph to check convergence of the SNP rankings, deciding whether to train models with more trees or not.  Lastly, the user can output the SNP rankings from one of the models.  In each step, new files (initial data, models, plots, rankings) are added to the work directory or the contents of the work directory are queried.

What I really wanted was to test Asaph and its workflow holistically.  I want to call Asaph and check that it executes successfully and produces the expected output on disk. Sometimes it may be enough to merely check that the output exists, while in other cases, I want to query the output to make sure what it contains is reasonable.  By running Asaph's workflow on test data, we can check the most common codepaths and ensure no syntax or type errors have been introduced.

In my search, I came across [Bash Automated Testing System](https://github.com/sstephenson/bats) or bats. Bats allows you to write tests in Bash. Bash scripts map well onto my use case: Bash commands are external programs in the traditional Unix philosophy. You then define assertions through standard Bash comparisons.  Additionally, bash supports simple setup and teardown functions and loading helper functions.

Bats is best demonstrated through the tests I created for the Asaph import script:

<script src="https://gist.github.com/rnowling/74224fed33ac99137d373297d6694c34.js"></script>

In the example, I use the following features:

1. Defining an embedded helper function (`count_snps`)
2. Setup and teardown functions
3. Defining tests with the annotation `@test`
4. Running commands and checking the return codes
5. Check for the existence of output files and directories
6. Checking the contents of output files

My experience is not unique to Asaph.  Many of the scientific applications I've come across in my research are built around complex datasets and workflows of deeply-connected steps.  It can be easier to use holistic tests with for these applications.  I haven't quite come across anything like Bats before, but I think it can be an useful tool to computational scientists.