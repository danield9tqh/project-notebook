# Design notebook for week ending April 17, 2016

## Last week's critique

**TODO:** Fill in this part with a summary and reflection on the critique you
received for last week's work. Answer questions such as:  How, specifically, did
the feedback help improve the project? Did the feedback point out or offer
something you hadn't considered? Did it help you make a design decision? Was it 
helpful in addressing the most pressing issues in your project? How will you
incorporate the feedback into your work? Will you change something about the 
design, implementation, or evaluation as a result?


First, Emma thanks for reminding me that I should have my project's code in it's own repo. 

To answer your question: _Would you consider prompting the user of the language to edit the types contained within this (SQLType) object based on what their database supports?_

I had not considered allowing the user to do this directly for their individual database simply because I would prefer to have a closed system where I provide support for specific database and if a type of database is not support then the user is just out of luck. This may sound limiting but I think allowing users to describe themselves which database primitives they want could prove problematic. It may have unintended consequences with my code that I would not be able to check for. One thing I am considering however is conversion from a user's defined type to/from a SQL blob object. That way the user can store their own objects even if they are not a database primitive. 

I really like your suggestion to change the user's declaration of `new FieldName[Int]("id")` to simply `Field[Int]("id")`. I was able to do that with a simple companion object.

You suggested about the problem of representing a schema with Scala tuples: _You could try to store tuples in tuples, so if you have more than 22 fields, just store some of the fields in tuples._

I actually saw this implemented by someone when I was researching Scala Tuples. I'm not sure if Slick does this or not but it is a good idea. I agree though that my time is better spent elsewhere instead of worrying about DB schema with more that 22 fields.

_It's also worth testing whether the SQL queries that your program outputs will actually work in SQL._

I agree, that is one of my goals for this week.

_Currently, it seems like you've built the functionality of writing Scala to produce SQL queries, but it would be interesting if you were actually creating and querying the tables from Scala._

Yea, this is what Slick essentially allows. It may be a bit out of the scope of the project for this semester. However, it is the ultimate goal. If I continue the project after this class hopefully I can reach that.

## Description

**TODO:** Fill in this part with information about your work this week:
important design decisions, changes to previous decisions, open questions,
exciting milestones, preliminary results, etc. Feel free to include images
(e.g., a sketch of the design or a screenshot of a running program), links to
code, and any other resources that you think will help clearly convey your
design process.

There are a couple of things I want to implement this week. The first is that I want to be able to change my `filter` method to be as close as possible to a Scala Collections implementation. Their method is defined as `filter(p: T => Boolean)`. This would allow the user to write `filter(_.id > 10)` instead of `students.id > 10`. Secondly, I want to implement more functions for my SQLSet collection. Some I have in mind are `foreach`, `sortBy`, `fold`, `map` and some others if I have time. The most difficult one of these will probably be `foreach` because it involves actually getting information from a SQL DB. 

One change that I made was to change the user's definition of DB field from `new FieldName[Int]("id")` to `Field[Int]("id")` by adding a companion object to Field. This was Emma's suggestion. 

An issue I ran into while I was trying to change the input to the `filter` function was that Scala does not allow a static method to access an non-static field. Specifically, I would like to be able to define a new `SQLSet` as `val students = SQLSet[Student]("STUDENT_TABLE")` because theoretically specifying that the set contains `Student` objects should be enough to get all of the `Fields` defined in the `Student` class. However, for a generic type `SQLSet[A]`, I cannot get the fields for type `A` without an instance of type `A`. Therefore, I cannot get the attributes of type A.

One solutions I've found is to use a [Type Tag](http://docs.scala-lang.org/overviews/reflection/typetags-manifests.html) and pass the type as an implicit parameter to `filter`. Apparently, Scala is able to get the instance of a type parameter at runtime through this method. I'm really not sure if it's safe / if I will run into errors later with it but I am going with it for now. There is also the option that I could just pass an implicit parameter of type `A` however, I think the user would still have to create an instance of the `Student` class for this to work. I do not want them to have to do this.

Because I was able to do this for the `filter` method, I was also able to use it for creating a new `SQLSet` object. Before, the user had to create a `Student` instance `val student = new Student()` and then create the set `val students = SQLSet[Student]("students", student.attributes)`. Now, they do not need an instance of the class: `val students = SQLSet[Student]("STUDENT_TABLE")`.

## Questions

**What is the most pressing issue for your project? What design decision do
you need to make, what implementation issue are you trying to solve, or how
are you evaluating your design and implementation?**

I think the most pressing issue for my project at this point is that I have not finished implementing all of my SQL abstract syntax. Another issue I have is that for my `filter` function, I am getting the class types at runtime and I'm not sure if that is safe or not. I will have to look into that more. 

**What questions do you have for your critique partners? How can they best help
you?**

One specific design question I have pertains to the user creating database schema. Currently, if the user wants to create a `Student` schema, the class is created with no parameters `class Student() extends SQLTuple`. The user the defines the DB fields inside the class. I am wondering if this might be an issue if the user wants to create a `Student` object in their code outside of the database e.g `student = new Student(5, "Daniel", 21)`. This would not be possible because `Student` does not have a constructor. Thoughts?

**How much time did you spend on the project this week? If you're working in a
team, how did you share the labor?**

I spent about 8 hours on the project this week.
