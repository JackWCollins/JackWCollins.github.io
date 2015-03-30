---
layout: post
title: "Lessons Learned from a Legacy Database Migration"
description: ""
category: 
tags: []
---

I was recently part of a fairly large scale database migration for our Ruby on Rails/AngularJS application. We performed this migration to integrate multiple different product features into one software application. The migration involved many simple name changes to follow Rails conventions, but also involved heavy refactoring of the table and columns themselves. Though this might not be a very common task, the lessons learned are still relevant for smaller database refactors. I hope our lessons learned can help some of you in the future.

## One Column at a Time

Given the sweeping database changes that we had to make, it was very tempting to try to make large changes at once. I personally considered creating each **new** database model, and then slowly re-writing the code from the legacy model into each new model. I might never have finished if I had taken that strategy. If you've programmed for any amount of time you have surely realized (probably after hours of debugging) that even the smallest code changes can have intricate and severe consequences. Large and sweeping changes would have had huge implications throughout the software that may take days or weeks to fully track down.

Instead, **take the migrations one column at a time**. We opted to take a very stepwise and methodical approach to the refactor. We created a specific migration for each table in the legacy database. Once we ran that migration we propagated the change throughout the Rails / AngularJS code and then moved on to the next. This focused approach allowed us to resist the deer-in-headlights feeling as we looked at the huge amount of code that needed to change. It also gave us more confidence that we were preserving the existing functionality. How?

## Keep the Tests Green

A solid test suite is undoubtedly one of the best ways that developers can get quick feedback about their changes. As I mentioned earlier, it was tempting to write new Rails Models to reflect the database changes, and then re-write the tests based on the new logic. However, this would involve a pretty significant period of uncertainty in the application. Any tests involving that model would fail until the new code was copleted in the new model. That doesn't provide quick enough feedback, and the sweeping changes would have hindered the steady progress of the team.

The stepwise migrations from the previous section made it easy to keep our tests green. With each migration we propagated the changes, fixed all of the failing tests, and then moved on. The green test suite gave us (and our managers) the confidence we needed to continue with the migration.

## Delegate, delegate, delegate

The mantra of many managers became our lifeline for managing some of the hairier table migrations. We used Rails modules' `delegate` method to preserve functionality (and green tests) while we propagated the changes. This made the most sense when we were splitting one legacy table into two or more tables in the new database. 

For example, let's say you have a `users` table that contains the users' name, address, and vehicle information, among other things. In the new database you want to create three tables: `users`, `addresses`, and `vehicles`. However, in the real world, there is a lot of application logic in various parts of your application that is very tough to decouple. By using Rails' `delegate` you can move the underlying code without breaking the various workflows that might call that method. 

The `delegate` method is trivial to use. Simply give it one or more methods to delegate followed by the class to delegate to.

{% highlight ruby %}

class User < ActiveRecord::Base

	# some code here...

	delegate :vehicle_summary, to: :vehicle

	# more code here...

end

class Vehicle < ActiveRecord::Base

	def vehicle_summary
		# return some vehicle information
	end

end

{% endhighlight %}

**NOTE:** This type of method delegation should only be used temporarily in the majority of cases. Leaving these delegate chains can make your code maddening to debug. 

## Overall - Take it Slow

The three tips above aren't anything groundbreaking, but they did allow a team of 5 to completely refactor a legacy database in two weeks. We were quite happy with that! The most important thing is to take it slow and to be methodical. When dealing with changes of this scale, the worst thing you can do is rush the process.

Happy Refactoring!