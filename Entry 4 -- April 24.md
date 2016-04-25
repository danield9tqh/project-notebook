# Design notebook for week ending April 24, 2016

## Last week's critique

**TODO:** Fill in this part with a summary and reflection on the critique you
received for last week's work. Answer questions such as:  How, specifically, did
the feedback help improve the project? Did the feedback point out or offer
something you hadn't considered? Did it help you make a design decision? Was it 
helpful in addressing the most pressing issues in your project? How will you
incorporate the feedback into your work? Will you change something about the 
design, implementation, or evaluation as a result?

_One thing I would encourage you to take time for, is to step away from the computer for an hour or so and maybe map out on a white board what you have accomplished so far and what you are still hoping to accomplish._

I appreciate this suggestion from Rebekah. I understand that the project deadline is rapidly approaching and that I have high goals for the project that I may not be able to complete by that deadline. I intend to continue this project after the class is over so, in my mind, I may have been thinking about features too far in the future. After taking a step back, I think I've made distinctions between features I can implement by the end of the semester and features I will save for future work. I've included this these below in my project description for this week. 

_I would invest some time in thinking about error checking to help your user debug if they make a mistake in their program._

I agree this is an important feature however, I don't think I will be able to get to it this semester. I think connecting my code to and querying an actual database is of higher priority. I do plan to improve error checking in the future however. `type mismatch; found : s.name.type (with underlying type scalaSQL.FieldName[String]) required: ?{def ==(x$1: ? >: Int(10)): ?}` is definitely not as helpful as `student.name is of type String and not of type Int`. There is much room for improvement. 

_One related problem could be the determining if your methods are all safe because you want to make sure your users will not run into any unexpected problems._

I think I've taken great care throughout the project to make sure all my methods are type-safe. However I can never be sure and that is also one of my main priorities. As this would involve a better understanding of the Scala type system, I will leave this as future work. 

_You asked specifically about creating a Student constructor in your notebook entry. Why would someone want to create a Student object outside of the class? How often do you imagine that a user might want to do this?_

These are good questions. It's true I don't know how used this feature would be. I could see users wanting to instantiate database objects more-so if they are able to insert them into the database. However, currently I have not implemented that feature. For those reasons, I plan to save this as future work.

_Would it be better to simply forgo that method (`filter`) and instead work on one of the other methods you mentioned?_

I did end up doing what I wanted with the `filter` method. However, I could imagine that would change when I research into lazy collections so that work may have been for naught. 

## Description

**TODO:** Fill in this part with information about your work this week:
important design decisions, changes to previous decisions, open questions,
exciting milestones, preliminary results, etc. Feel free to include images
(e.g., a sketch of the design or a screenshot of a running program), links to
code, and any other resources that you think will help clearly convey your
design process.

Before I started work this week, I took some time to detail what I intend to finish or not by the end of this semester. 

##### Features to Complete by End of Semester
- implement `foreach` method. This entails connecting and querying an actual MySQL database so that the user can read from it. 

- Do initial research on Scala lazy collections. If I am able to leverage Scala collections' `lazyMap` and other lazy functions, I may be able to get many functions for my SQLSet for free.

- Improve my project's package hierarchy.

##### Features for After This Semester
- Implement SQL JOIN functions. Be able to join two tables (collections) based on a predicate. This entails finishing my SQL AST. 
- Implement lazy evaluation of SQL queries / collections if I do not get to that in my project
- Add missing collection functions
- Improve error messages for Scala users
- Improve syntax and user experience
- Implement SQL read functions

If I can finish these tasks by the end of the semester, I feel that I will have reached my goals for the project. At the beginning of the project, my goals were to implement SQL read functions and possible SQL write functions if I had time. Although I did not have time to get to the latter, I am satisfied with my progress because I feel I have learned a great deal about Scala, its Collections and type system, as well as SQL and Relational Algebra. I am confident that continuing this project will be easier from all the research I've done. I will also be satisfied because, although the features will be limited, I will still have a working product to demonstrate.

The first issue I ran into this week while implementing the `foreach` method again had to do with type erasure. To recall, type erasure in the JVM dictates that for any use of generics (for example in a collection) the actual type of that generic will be reduced at runtime to its bounds or removed all together if there are no type bounds. For this reason it's impossible in some cases to recover the run-time type of a given object. For example, if we receive a student tuple from a database query inside the `SQLSet` class, we cannot dynamically create a `Student` object from that because we don't know that the collection is of type `SQLSet[Student]`, we only know that it is of type `SQLSet[A <% SQLTuple]`. Even though `Student` extends `SQLTuple`, I could not figure out a way for `foreach` to specifically return a `Set[Student]` rather than just a generic `Set[SQLTuple]`. 

I realized that this may be the reason Slick uses tuples instead of lists to represent a DB object's attributes. A tuple has set type for each element (e.g. `(Int, String, String, Int)`) whereas a list is just `List[Int, String]`. I also realized that Slick defines its attributes as methods instead of values the way I do (e.g. `def name = ` instead of `val name = `). I am going to look into both of these areas to see if one of these changes contains a solution. I do want to try my best however to preserve my syntax because I think it is better than Slick's.

I looked into using reflection. Reflection is examining the state of your program at runtime. This means it is possible to get type signatures, methods etc. This implementation worked but it was a little bit contrived. It was not completely type safe. After talking with Prof Ben, I decided to rearrange my code structure a little bit an make the user write a little bit more code on their end for defining a DB schema. The user would have to separate their `Student` class from a `StudentRecord` class. I am making this decision because it is less error prone and I believe that I will be able to cut out the `StudentRecord` class in the future possibly with annotations and macros. 
 
## Questions

**What is the most pressing issue for your project? What design decision do
you need to make, what implementation issue are you trying to solve, or how
are you evaluating your design and implementation?**

I think the most pressing issue for my project right now is learning about annotations and macros in Scala so that I can cut down on the amount of code the user has to write.

**What questions do you have for your critique partners? How can they best help
you?**

What do you think about splitting up the `Student` class from a `StudentRecord` class. It would allow for users to use `Student` objects elsewhere in their code but it also required more class definitions by the user. 

**How much time did you spend on the project this week? If you're working in a
team, how did you share the labor?**

I spent about 10 hours on my project this week. 

