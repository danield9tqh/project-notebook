# Design notebook for week ending April 3, 2016


## Last week's critique

Just to answer some of the questions it seemed like you had in your critique Emma:

_Are you planning on translating each entry in a database into a corresponding Scala object and then querying those objects like a collection?_

I am planning on having a SQL table be represented by a schema and using that schema to represent the Scala Collection. I think this way would be reasonably efficient and the Scala program would not have to have any knowledge of the actual data in the database until it needed to use it. Then it would execute the query to retrieve the data. I think this could probably be done by using some of Scala's laziness features. 


_Can you think of any cases in the scope of your project where you'd run into concurrency issues?_

I can definitely think of cases where I would run into concurrency issues if I were modifying the database. However, so I don't have to worry about those cases, I am going to be focusing my project on only SQL reads statement and not be executing any writes on the database. I know this is not realistic for a really usable DSL but I thought it would be a good compromise to make given my time constraints. 

As for your suggestion about changing the syntax of the Scala collection functions to be more SQLy: I can see where that could be better. If I look at my project goals from the highest level, I am aiming to improve the SQL experience in Scala and not necessarily mimic exactly a Scala collection. My initial thought was that mimicking a Scala collection would be the best way to accomplish this goal because it follows a standardization that all Scala users are already familiar with. If however, I find a solution that is better I would consider deviating from the Collections interface. 


## Description

Most of my work this week has been research into SQL representations and the Scala Collections code. I feel that this research has payed off in that I now have a better idea of a set of three tasks that encompass my project. I describe them in my document [Language Design and Implementation](master/documents/design_and_implementation.md). I have also made progress on my first task of creating an intermediate representation of SQL based mostly on the abstract syntax of Relational Algebra.

## Questions

I think the most pressing issue for my project right now is understanding Relational Algebra. I want to create an accurate representation of SQL so that when I go to do my translations, it will be rather straightforward. One design decision I have to make is how accurate to make this representation. I can skimp on a few parts that I may think are not necessary such as GROUPBY clauses but I don't want to pay the cost of not having an accurate representation down the road. 

A question I have for my critique partners is:

How imperative is it that I can translate to many different SQL languages. Because I am writing my own internal representation of SQL that means that I must also write my own translation to SQL syntax. Right now I am thinking of just implementing a single SQL language such as MySQL or SQLite but that would cut down on usability of the language. Of course it could always be extended later. 

I spent about 6 hours this week working on this project.



