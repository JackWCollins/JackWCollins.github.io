---
layout: post
title: "Some Rails Basics"
description: ""
category: 
tags: []
---

I've been working through the second class of the Tealeaf program (Rapid Prototyping with Ruby on Rails) for a week now. It has been, in a word, a whirlwind! I've been messing around with Rails for about 9 months prior to enrolling in the Tealeaf Program, but I can't believe how quickly I'm learning all sorts of different things about Rails and web development in general. 

As part of our first week we've been given a 15 question quiz on some Rails and web development basics. I thought this would be a perfect thing to include here in case others ever have similar questions. 

**Why do they call it a relational database?**

A relational database involves a series of tables with primary keys and foreign keys that map relationships between the tables. For example, lets say you have two tables: Users and Posts. The "users" table would consist of columns such as "username" and "created_at" as well as an "id" column and would be filled in with rows of different users. Almost every table will have an "id" column referred to as that table's primary key. The "posts" table would have columns like "title" and "description" in addition to the "id" column. Each row in the "post" table would be filled with a post's information. How would we know which user wrote a post? The "posts" table would also include a column titled "user_id". This "user_id" would be the foreign key of the "posts" table and would correspond to the primary key ("id") of the "users" table. That's the "relational" part in "relational database". The relationship is set up with two simple pieces of code in the User and Post controllers.

{% highlight ruby %}

class User < ActiveRecord::Base
  has_many :posts
end

{% endhighlight %}

{% highlight ruby %}

class Post < ActiveRecord::Base
  belongs_to :user
end

{% endhighlight %}

**What is SQL?**

SQL stands for Structured Query Language and is a language designed to communicate with and change databases

**There are two predominant views into a relational database. What are they, and how are they different?**

The two types of views are read-only and updateable. The primary difference is whether or not the database system can perform reverse mapping between the view and database schema. If it can, then it is updateable. Otherwise, the view is read-only.

**In a table, what do we call the column that serves as the main identifier for a row of data? We're looking for the general database term, not the column name.**

The column that serves as the main identifier for a row of data is the "primary key". See the example in question 1 for clarification.

**What is a foreign key, and how is it used?**

A foreign key is a column within one table that can identify one or more pieces of information in another table. In the Users and Posts example above, the foreign key of the "posts" table was the "user_id" column. The value in the "user_id" column corresponds to the primary key (the "id" column) of a single user in the "users" table.

**At a high level, describe the ActiveRecord pattern. This has nothing to do with Rails, but the actual pattern that ActiveRecord uses to perform its ORM duties.**

In general, the Active Record refers to the way that data is accessed from within a database. Each table or "view" corresponds to a class (in rails, it's a model). Therefore, a new instance of an object from that class is tied to a row in the table and a new row is created every time a new object is saved. Objects can also be updated or deleted. The properties or accessor methods for each column are defined in the wrapper class (model).

**If there's an ActiveRecord model called "CrazyMonkey", what should the table name be?**

    crazy_monkeys

**If I'm building a 1:M association between Project and Issue, what will the model associations and foreign key be?**

Project model:
    has_many :issues
Issue model:
    belongs_to :project
The foreign key will be in the issues table and titled "project_id"

**Given this code**
{% highlight ruby %}
class Zoo
  has_many :animals
end
{% endhighlight %}

  **What do you expect the other model to be and what does database schema look like?**
    The other model would likely be titled "Animal". The database would include columns "species", "age", "name" and certainly "zoo_id"

  **What are the methods that are now available to a zoo to call related to animals?**
    You could call methods specific to an animal through the relationship like zoo.animals.find(:age >="5"). You could also iterate through each animal and list its species with 
    {% highlight ruby %}
      <ul>
      	<% zoo.animals.each do |animal| %>
      	<li>
      		<%= animal.name %>
      	</li>
      	<% end %>
      </ul>
    {% endhighlight %}

  **How do I create an animal called "jumpster" in a zoo called "San Diego Zoo"?**
        * First you would need the zoo_id of San Diego Zoo. Let's say it is 4.
        * jumpster = Animal.create(name: "Jumpster", zoo_id: "4")

**What is mass assignment? What's the non-mass assignment way of setting values?**

Mass assignment is the process of assigning values to multiple variables in one command. A perfect example is in the previous question where I assigned the name and zoo_id to Jumpster in one command. The non-mass assignment way of setting values is to assign values one-per-command.
    * jumpster = Animal.new
    * jumpster.name = "Jumpster"
    * jumpster.zoo_id = "4"
    * jumpster.save

**What does this code do? Animal.first**

Animal.first will retrieve the first row of data from the "animals" table.

**If I have a table called "animals" with columns called "name", and a model called Animal, how do I instantiate an animal object with name set to "Joe". Which methods makes sure it saves to the database?**

You could do this a few different ways. The most straightforward way would involve mass assignment.

Joe = Animal.create(name: "Joe")

Animal.create will hit the database right away whereas Animal.new would only save the new object in memory and you would have to call Joe.save to hit the database.

**How does a M:M association work at the database level?**

A many-to-many table works through a join table to associate objects that might have multiple related instances of each other. For example, in a blog you might have a many posts and each post could belong to many different categories. So, you would have a "posts" table and a "categories" table. To associate the two with a M:M relationship you would need a "join table" titled post_categories with at least two columns: "post_id" and "category_id". Rails expects this syntax of "_id" added to the end of the columns. After you set up the model, the database would join the posts and categories through the post_categories table. Each post has an "id" which would be identified in the join table in the "post_id" column. The foreign_key for the category would be stored in the "category_id" column.

**What are the two ways to support a M:M association at the ActiveRecord model level? Pros and cons of each approach?**

The two ways are has_many :through and has_and_belongs_to_many. The has_many :through is usually the better choice for a number of reasons.

has_many :through

    Pros: This option is much more flexible if you need to modify the relationship or add columns to the join table.

    Cons: Slightly more complicated and you need to build the join table manually.

has_and_belongs_to_many

    Pros: Very easy to set up because you only need to include "has_and_belongs_to_many" in both models.

    Cons: You can't easily modify the relationship or add columns to the join table - because there isn't one!

**Suppose we have a User model and a Group model, and we have a M:M association all set up. How do we associate the two?**

I would build a join table called user_groups that had columns "user_id" and "group_id". In the User model I would include 

    has_many :user_groups
    has_many :groups, through: :user_groups

In the Group model I would include:

    has_many :user_groups
    has_many :users, through: :user_groups
