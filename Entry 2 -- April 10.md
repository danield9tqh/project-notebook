# Design notebook for week ending April 10, 2016

## Last week's critique

In last week's critique Rebekah addressed many of the questions I had about where to spend my time in my project. Should I take time to implement support for multiple different SQL languages? Should I take time to implement all SQL features such as complex JOINS and GROUPBY. Her suggestion was basically to go the agile development route. Create something that is elegant and works for a small subset of problems such as a single SQL language or simpler SQL queries and then depending on how much time I have, expand from there. I agree with this approach and it was what I was more leaning towards in the first place. 

Rebekah also asked the question: Are there Scala features you could use to remove some of the Scala "flavor" and make it a little bit more of a unique language within Scala? In other words, do I need to strictly follow Scala Collections syntax for accessing database or could I make it more DSLy. She also mentioned that this may be easier for non Scala users. 

My response for where non Scala users are concerned is that this DSL is not really meant for non Scala users. Because it is an internal DSL in Scala, it is meant to increase efficiency of database access for users who already use Scala. I could imagine that if it became popular, users of other languages may want to write only their database accesses in Scala and may not be familiar with other Scala syntax. However, I think it is too early to consider that situation at this point in the project. However, I am still considering changing the syntax a little bit because of other reasons. Those reasons mostly include implementation issues I ran into while trying to closely follow the Scala Collections syntax. These issues include dealing with Scala tuples and anonymous function predicates which I will discuss below. If it turns out that it is impossible for me to exactly mimic a Scala Collection, it may be prudent to drop that goal to an extent and implement some of my own syntax. I think that it is more important that my syntax be consistent and coherent than mimic Scala Collections exactly. The only reason I wanted to mimic them in the first place was for consistency's sake. If that is not possible, I will likely go another route.

## Description

This week I was still working on implementing an abstract SQL syntax based on relational algebra. One problem that I ran into was my ignorance of the Scala type system. This was really apparent when I wanted to implement Conditions in my SQL queries. Conditions would have a special place in the _WHERE_ clause of a SQL query. An example condition is _age > 10_ and the query would look like _WHERE age > 10_. I wanted a way to implement this using already built in Scala type checking using the Scala [Ordered](http://like-a-boss.net/2012/07/30/ordering-and-ordered-in-scala.html) trait. I learned a couple things when trying to implement this. First of all, it is possible in Scala to define parameters that conform to a certain subtype and or supertype. For example, if I wanted the variable _age_ to be an attribute in my SQL relation and I also wanted the type contained in the attribute (in this case an _Integer_) to be _Ordered_, I could define the class _Attribute_ as:

_case class Attribute\[T <: Ordered[T]\](val name : String)_

Then the age attribute would look like

_Attribute\[Integer\]("age")_

This enforces that an _Attribute_ cannot contain a type that is not Ordered. In other words, it cannot contain a type that does not support the functions _>_, _<_ or _=_.


Another interesting issue I ran into this week was when I was trying to decide how a user should define a schema for a SQL table. It turns out that Slick defines a schema basically as a Scala Tuple. This makes sense because a relational database is also made up of tuples and each of those tuples can be type checked. For example if our table had two fields that were both _Int_ then the type of our table would be _(Int, Int)_. Naturally, I wanted to find a way in Scala to express a tuple with a variable number of types. As it turns out however, Tuples in Scala can only have a max of 22 types. This is because _(Int, Int)_ is actually syntactic sugar for _Tuple2[Int, Int]_. Only classes _Tuple0_ through _Tuple22_ are defined in Scala. Because of this I have to change my thinking of how I would represent a relational tuple in Scala if I want a table to contain more than 22 fields. 

I started to create a _filter_ method this week for my SQL Set class. One issue I ran into while doing this was that a normal _filter_ method takes in a predicate of the type _A => Boolean_. This is an issue because I am not applying this predicate to actual objects of type A. Rather, I am trying to translate this predicate into SQL syntax so that I can send it to the database and the database can do the comparison. For example, if the user passed in the predicate _id < 10_, I would want to translate that to a new SQL _Comparison_ object in my abstract syntax so that eventually it will be translated into the string "WHERE id < 10". This may prove difficult because I cannot find a way to extract the logic away from the anonymous function _id => id < 10_. Slick implements new operators for each of their comparators <, >, ==, <=, >= and !=. I may end up going this route.


Another issue I ran into with types was how to know what is a valid type for a SQL field. I stared off by saying that any _Ordered_ object in Scala is valid. This means anything that is comparable. That would include Int, String and many other types. This however, is not accurate. Most SQL databases only support a limited number of types. I wanted to reflect this in my type checking system so I created a new object _SQLType_ and added implicit conversions from all Scala types that I want to allow. For the moment I have only implemented Int and String but it should be easy to expand on that until I have included every type that a SQL database would allow. 

## Questions

**What is the most pressing issue for your project? What design decision do
you need to make, what implementation issue are you trying to solve, or how
are you evaluating your design and implementation?**

There are two pressing issues for my project at this point. The first is to continue implementing my SQL abstract syntax. I have implemented the most basic operations such as the relational algebra Selection and Projection, however I still need to implement more complicated features such as table joins and renames. 

The second pressing issue for me is how I am going to allow users to define their database schema. I want users to be able to work with their own classes. For example, if a user has a database table filled with students, I want them to be able to easily write a Students class in their code that corresponds to the database. However, they must also define the type of each field for the Student object. Figuring out how to make this simple is my top objective. I have been having trouble maximizing ease of use while still making sure that I can type check operations to the database.

**What questions do you have for your critique partners? How can they best help
you?**


One problem that I am currently struggling with is how a user should define a database schema. Ideally, 
the schema that the Scala type checking system sees should be the same as that of the external database. Slick has the user [define the schema explicitly](http://slick.typesafe.com/doc/3.1.1/gettingstarted.html#database-configuration). I think this is a reasonable solution but it also seems a little verbose. I was looking for suggestions on how this could be improved.

Another problem related to defining a schema is that the types of a table must be defined by a user in Scala. The way Slick does this is with Scala tuples. Scala tuples however can only have up to 22 element. Would this be an issue when defining database schema? Is it common to have more than 22 fields in a table?


**How much time did you spend on the project this week? If you're working in a
team, how did you share the labor?**

I spent about 8 hours this week on my project. 
