---
layout: post
title:  "AI-Assisted Software Development with LLMs: Part VI"
date:   2025-07-29 10:13:19
categories: "software-engineering"
tags: ["education", "ai", "llm", "cs-education"]
---

(This post is a continuation of a series I started previously.  Use these links to see
[part I](/software-engineering/2025/07/23/ai-programming-assistants-part-i.html), 
[part II](/software-engineering/2025/07/24/ai-programming-assistants-part-ii.html),
[part III](/software-engineering/2025/07/25/ai-programming-assistants-part-iii.html),
[part IV](/software-engineering/2025/07/26/ai-programming-assistants-part-iv.html), and
[part V](/software-engineering/2025/07/27/ai-programming-assistants-part-v.html).)

I wanted to starting exploring the use of AI-assisted programming for a larger and more
complex project.  I tend to write a large number of single-file Python scripts and 
Jupyter notebooks these days rather than working on traditional software projects.
But, I do have one long-running software project that was developed as a set of command-line
tools supported by a common library: [Asaph](), a bioinformatics tool
I developed for identifying and characterizing large chromosomal inversions using population
genetics variant data.  It's a relatively small code base at ~3,500 lines of code, but it has
structure to it, some user tutorials, a test suite, etc. I haven't actively worked the project for the last couple of
years, so it's time to make a cleaning pass to ensure that it still works with updated
versions of libraries and find any potential bugs that I missed.

I tend to do most of my development work from the command line.  I'm a fan of lightweight
editors like [micro](https://micro-editor.github.io/) rather than more complex IDEs and
tend to do a lot of my coding on the terminal.  I was excited when I saw that
Anthropic released a CLI tool called [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview)
that attempts to be a natural fit
for us CLI programmers.  Here, I report on my experience with setting up Claude Code
for the Asaph code base.

## Installing
Claude Code was pretty straightforward to install.  I didn't have npm on my system, so
I used MacPorts to install it.  After that, I was able to install the Claude Code package
with NPM.

## Initial Run
Once installed, I ran the `claude` CLI tool from within the `asaph` source directory.
I was prompted to authenticate my account since it was the first time running claude.

## Set Claude Up for Asaph
After authenticating, claude offered some helpful getting started tips:

![](/images/claude-asaph/first-run-advice.png)

One of those tips was to generate a markdown filed named `CLAUDE.md` that contains
project information.  Claude evaluated my code and documentation and populated the file with:

* A project overview
* Installation instructions
* Instructions for running the test suite
* An architecture overview covering the main modules of the common library
* Short descriptions of the command-line tools
* A summery of the workflow pipeline
* Dependencies
* Data directory structure

I was genuinely surprised by what the file contained.  Claude really discovered a lot. As part of that,
claude seemed to do good job of parsing the tutorials and pulling out contextual information that would
be useful to a human.

## Installing Asaph
I wanted claude to test its installation instructions. I asked claude to install asaph with the following prompt:

> Please create a virtual environment called 'claude-venv' and install asaph in that virtual environment.

One of the nice things about claude is that it prompts you for permission for each command it is going to execute
and every proposed change:

![](/images/claude-asaph/install-asaph-prompt.png)

The installation failed because `setuptools` was missing. Rather than failing, claude proposed to install setuptools.

![](/images/claude-asaph/install-asaph-setuptools-error.png)

Once the command was fixed, claude was successfully completed the installation:

![](/images/claude-asaph/install-asaph-quote-successful.png)

After overcoming that bump, claude re-tried the installation and offered to update its installation instructions:

![](/images/claude-asaph/installation-instructions-diff.png)

## Updating the Installation Instructions
I then asked claude to test the updated instructions:

> Please test the installation instructions in the CLAUDE.md file by deleting the current virtual environment "claude-venv"
> and reinstalling Asaph following the instructions in the CLAUDE.md file.

Claude ran into a second issue: the command it had generated for installing the dependencies didn't properly quote the package
with versions. Yet, claude identified the issue when the command failed and proposed a fix:

![](/images/claude-asaph/install-asaph-quote-error.png)

After the successful installation, claude offered to update the installation instructions:

![](/images/claude-asaph/install-asaph-quote-diff.png)

## Running the Test Suite
When claude was exploring the project, I saw that it identified that there were a number of tests implemented with
the [bats](https://github.com/bats-core/bats-core) framework.  So, I figured I would see how smart it is and ask it to run the test suite:

> Please run the bats test suite and report any errors.

The tests failed due to a change in the `random.sample()` function.  Python no longer supports sets.

![](/images/claude-asaph/python-deprecation-error.png)

Claude offered a fix:

![](/images/claude-asaph/python-deprecation-fix.png)

## Preparing a Pull Request
I figured I might as well commit the changes to the public repository.  I had claude prepare a commit in a new branch and push it:

> Please create a git branch, commit the changed file from Asaph, push the branch upstream, and give me the URL to create a
> GitHub pull request.

Claude executed this request perfectly. (Forgot to take a screenshot -- sorry!)

Lastly, I had claude switch back to the main branch and pull down any changes:

> Please switch to the main branch and pull any upstream changes so that the local copy matches the upstream copy.

I then used claude to generate a few other pull requests including updating the tutorial installation instructions
and adding CLAUDE.md to the `.gitignore` file.

## Reflection
So far, claude has been fantastic for software maintenance tasks.  I've been pleasantly surprised by its ability
to discover details about my project (from source code as well as documentation) and identify the appropriate
commands for a given request.  That said, I think the initial installation instructions were based on
conventions for when a setuptools `setup.py` file is detected.  I'm glad I asked claude to test the instructions
because then it was able to adapt them to what my project needed.

I also was surprised by how robust claude is. Claude was able to recover from errors.  For every error that was encountered, claude
was able to generate a fix and then go about it's merry way.  Because of claude's ability to keep track of context, I was then able
to have it apply the fixes to documentation as needed.

Lastly, claude handled ambiguity really well.  There were cases when I wasn't sure what file it would try to commit.  In every case,
though, it selected the correct files.

I had tried the Cursor IDE at one point.  I found it to be way too busy, way too complex, and overwhelming.  In particular, Cursor
was constantly making suggestions.  I much prefer the approach with claude where I initiate the prompting and it sticks to the task
at hand.  I'm already thinking about ways I can use it next.
