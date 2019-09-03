---
layout: post
title:  "Thoughts on Surviving My First Year of Teaching at a PUI"
date:   2019-09-02 12:13:19
categories: "teaching"
tags: ["teaching"]
---

With my summer wrapped up, I can now say that my first year as an assistant professor at a primarily-undergraduate institution (PUI) is done.  As I prepare to start my second year, I reflected on my first year and some of the lessons I learned.

## My First Year in a Nutshell
MSOE is on a quarter system.  In my department (EECS), most faculty teach three sections of about 20 students each per quarter. Most classes are in the format of three hours of lecture and a two-hour lab.  Frequently, two of the sections are for the same class, while the third section is for a second class.  This means faculty teach 9 sections per year corresponding to 6 classes.

First-year faculty also teach 6 classes but only one section of each.  For 2018-2019, I taught the following classes:

* Introduction to Software Development I and II
* Data Structures
* Algorithms
* Data Science
* Machine Learning

Overall, my first year was incredibly busy and, at times, overwhelming, but it went well.  In the end, my students learned a ton, and I earned good teaching evaluations from them.  Going into my second year, I am much more confident in my grasp on my courses, their materials, and the overall patterns of the academic year.

## Teaching Tips for First-Year Faculty
Multiple new faculty members are joining our department this year, and for most of them, this will be their first year as a professor at a PUI.  I found myself repeating some observations, so I wanted to write them down and share them more broadly in case they are useful to others.


### 1. Optimize for Short-Term Realities
One colleague gave the advice that my only goal for my first year was to survive.  In retrospect, this may have been some of the most useful advice I received. I had very high expectations for myself going in and ended up hitting a wall.  I quickly learned that I needed to moderate my expectations to be able to manage my workload and challenges of constantly doing something new every day for 9 months.

In practical terms, I had 5-6 lectures and 2 labs to prepare for every week.  Each lecture and lab took me about 2-3 hours of preparation, which included creating materials as well as reviewing and re-teaching the material to myself.  On top of that, I also had grading, office hours, and service and research commitments. (Research is not a focus at MSOE, but several of my colleagues and I are passionate about research and mentoring students.)

Several faculty gave me well-meaning and good advice on best practices.  I was not, however, in a good position to use all of it.  In particular, I was once told to try to prepare my class materials with the long-term in mind: prepare them well and then you can easily re-use them, saving time in the future.

Simply put, I didn’t have time to do things the "right" way.  In retrospect, I realized that many of my materials were placeholders or drafts.  I created minimal and unpolished slides, examples, labs, and assessments.  In the short term, it is easier to create something that you plan to use once and, potentially, throw away.  Optimizing for the short term was absolutely necessary to hit all of my weekly lecture and lab deadlines.

The same goes with grading.  Be careful about spending too much time on grading; course materials go with you from one year to the next but marked assignments do not.  I used spreadsheets to record rubrics, point assignments, and comments for labs (homework).  I copied the overall assignment grades and comments into Blackboard and sent a class-wise email with the rubric. Only in my last quarter did I improve on this by creating scripts that generated reports with my comments embedded alongside the students’ code.

During the summer after your first year, you can spend time polishing, extending, and re-developing your materials.  By then, you’ll have a sense of the “big picture” and overall themes for your courses.  That’s the best time to prepare reusable materials for the long term.

### 2. Plan for the Unexpected and Unknown
With the time needed to prepare my materials, I spent the entire academic year preparing everything right before it was needed.  I had no free time (or spare energy) to work ahead.

This approach assumes that you will always have the needed time for preparation.  But life doesn’t work that way.  You may have an unexpected situation arise or simply want a date night with your partner.  In those cases, you need “backup” materials to fill lecture time when you don’t have time to prepare.

In my case, I showed recorded talks that would be interesting to students.   I found recorded talks from industry conferences and other sources for general audiences, on current topics, or containing interesting observations related to the course material.  The talks were great for broadening students’ horizons and exposing them to cutting edge work.

Additionally, I would occasionally run out of lecture material because I was still figuring out the timing for my lectures.  I used in-class exercises to fill time.   The exercises gave all of the students opportunities to practice the material and often engaged or entertained more advanced students more than they would be otherwise.  I never required the students to submit their solutions or graded them — the exercises were purely meant to be fun puzzles and provide practice.

### 3. Quizzes: The Multi-Faceted Tool
Early on, a colleague suggested I give weekly quizzes.  My views and ways of using quizzes were further shaped by discussions with many of my colleagues.  Quizzes became one of my favorite tools in my toolbox.

I’ve discussed quizzes in a [previous blog post](/teaching/2019/03/09/quizzes-multifaceted-teaching-tool.html).  But, in summary, weekly quizzes provide opportunities for you and your students to see how your students are doing with the material.  They can indicate whether a topic needs to be revisited and do so early enough to enable course corrections (both for students and myself!) ahead of exams.

I also found that quizzes gave opportunities for review.  I would often give a quiz in class and then immediately discuss the answers.  Students were more engaged than in the usual lectures, enjoyed the opportunity to try on their own, and appreciated the immediate feedback.  A quiz and the follow up review would frequently fill an entire lecture.

Students really appreciated the opportunity to practice testing.  My quizzes tended to reflect my exams.  Consequently, students felt more confident and at ease going into my exams.  This is especially true since many students aren’t yet skilled at creating realistic testing situations for practice.  Further, when students struggled with a concept, I would keep quizzing them until they had the concept down.  This also helped students prepare for the exam.

Lastly, weekly quizzes provided structure and motivation to help students stay on top of studying.  I gave quizzes at the same time each week.  This gave students motivations to study every week instead of waiting for the exams.

### 4. Accept Failure: Your Ideas Will Fail and Your Decisions Will Backfire
In my first year of teaching, every teaching idea I had was new and untested.  The first year was really just a bunch of experiments.  Some of those ideas went well, while others failed miserably.  I had to learn to accept failure without taking it personally.

I have two examples from my Data Structures class.  My first lecture on linked lists failed miserably.  In Data Structures, I rotated between conceptual and live-coding lectures.  In the case of linked lists, I didn’t spend enough time on the concepts before jumping into live-coding the implementation.  Secondly, I originally attempted to closely follow the textbook.  Unfortunately, the textbook’s code example was designed to avoid code duplication.  My students left the lecture feeling completely lost and intimidated.

I ended up redoing the lecture.  I spent more time lecturing on the concepts before diving into the code. I also decided not to follow the textbook and create my own linked list implementation.  I embraced repetition to emphasize the common patterns associated with traversing a linked list.  This second lecture went much better.  Through repetition, the students grasped the traversal patterns.  By the end of the second lecture, students were anticipating how to implement various operations such as searching the list.

Another idea I had did go well. I decided to introduce unit testing on quizzes, labs, and exams in the style of test-driven development (TDD).  In labs, multiple students reported that their tests caught bugs and exposed edge cases they hadn’t initially considered.  On quizzes and exams, I asked the students to write test code before starting on their implementations.  Data Structures problems tend to be more abstract and complex than the problems in the introductory courses. My goal for the students was to improve their understanding of the problems by creating example inputs and outputs.  Multiple students confirmed that the tests helped to clarify their understanding and were valuable exercises.

### 5. Use a Light-Weight Task-Tracking Solution
With six lectures and two labs every week, I had a large number of weekly deadlines to hit and tasks to complete.  I had never been so busy or felt so under the gun in my previous work (either my Ph.D. or industry positions).  I also had to keep an eye on deadlines that were farther out such as exams and paper deadlines.

I found it incredibly useful to create a customized task management solution using Trello.  I borrowed the concept of a Kanban board.   Kanban boards normally have lists like “To Do”, “In Progress”, “Blocked,” and “Done.”  In my case, I used lists titled “Due in a Month,” “Due in a Week,” “Due Tomorrow / Next Class Meeting,” and “Done.”  By organizing tasks into different time scales, I was able to see what was due soon and further out in a single place.  I reviewed and updated the Trello board on a daily and weekly basis.  I wrote about my approach in [another blog post](/productivity/2019/01/11/organizing-class-agile.html).

### 6. Keep up with Hobbies, Exercise, and Other Activities
My first year was incredibly stressful.  Teaching, even at the college level, requires substantial emotional labor.  I was, in effect, always “on call” for forty students who needed my time and attention.

Feeling overwhelmed, I canceled on the gym and other activities in my first month.  In the short term, that may have helped me meet a few deadlines.  But in the longer term, my mental and physical health took a hit.  With the urging of my wife and a friend, I set up a regular gym schedule again.  That time became a place in my schedule to work out my frustrations and anxiety, feel accomplished, and have time to myself.


### 7. Seek Advice from Your Colleagues
My success in teaching in my first year was made possible due in no small part to the wisdom and support of my colleagues.  I benefited greatly by bouncing ideas off of my colleagues and listening to their experiences.  I would often take the same piece of material to two or three faculty members to get different points of view.  Many of my colleagues have 10 or more years of accumulated experience.  Their advice and feedback often kept me from going down a wrong turn and helped me polish my rough, untested ideas before coming into contact with the harsh nature of reality.  I am greatly indebted to them for the time, attention, and support they gave me.

My colleagues were happy, if not eager, to share their course materials with me.  I found that creating my own slides and materials was a necessary part of my process of preparing for lectures.  Nonetheless, having examples to study saved me time when preparing my own materials and made it easier to synchronize across sections.  Don’t be afraid to ask for and borrow materials!

## Epilogue
My first year of full-time teaching as an assistant professor at a PUI was quite a challenge, but I’m comfortable calling it a success.  From the various challenges, I was able to grow by leaps and bounds both professionally and personally.  The net result is that as I prepare to enter my second year I feel more confident in my teaching; I’m facing fewer unknowns and better prepared all around.

(Thank you to [Josiah Yoder](https://faculty-web.msoe.edu/yoder/) for helpful edits and feedback!)


