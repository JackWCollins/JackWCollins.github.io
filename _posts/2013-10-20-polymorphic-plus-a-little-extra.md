---
layout: post
title: "User Authentication and Polymorphic Associations"
description: ""
category: 
tags: []
---

Three weeks through the course and the Postit! app is really coming together. Week 3 focused a lot on user authentication and polymorphic associations related to the voting functionality. User authentication was an intimidating challenge to tackle for the first time but it went pretty smoothly. Polymorphic association was vital to the voting functionality for posts and comments, but presented a few additional challenges.

The third week's quiz focuses on view templates, user authentication, and polymorphic associations.

**What's the difference between rendering and redirecting? What's the impact with regards to instance variables and view templates?**

Rendering presents the same view template without changing the HTTP request. Therefore, you can access the same instance variables when rendering. Redirecting means you are issuing a new HTTP request and you no longer have access to the same instance variables. 

**If I need to display a message on the view template, and I'm redirecting, what's the easiest way to accomplish this?**

The "notice" method is the easiest way to display messages when redirecting.

{% highlight ruby %}

def create
  @post = Post.new(params.require(:post).permit(:url, :title, :description))

  if @post.save
    redirect_to posts_path, notice: "Your post was successful"
  else
    render :new
  end
end

{% endhighlight %}

**If I need to display a message on the view template, and I'm rendering, what's the easiest way to accomplish this?**

The best way to display a message when rendering is to use "flash.now" to display the message.

**Explain how we should save passwords to the database.**

Passwords should never be saved as plain text in a database. Passwords can be encrypted in a variety of ways, and one of the most straightforward ways is one-way encryption using a gem such as bcrypt-ruby. When a user registers, their password is encrypted and saved into the database as a "password digest". The same string will produce the same password digest, but you cannot decipher the password using only the password digest. During login, the user's password will be encrypted and compared with the password digest to determine if the login is successful or not. 

**What should we do if we have a method that is used in both controllers and views?**

It is best to consolidate logic in the application_controller.rb file and delare it as a helper method at the top of the file. This helps with long-term maintenance and readability. 

**What is memoization? How is it a performance optimization?**

Memoization is an optimization technique used in methods that remembers results for a specific set of inputs. Subsequent calls to the same memoized method with the same inputs return the remembered result instead of recalculating it which can eliminate a call to the database. 

A good example of memoization is a "current_user" helper method from application_controller.rb:

{% highlight ruby %}

def current_user
  @current_user ||= User.find(session[:user_id]) if session[:user_id]
end

{% endhighlight %}

Ruby uses the "||=" operator for memoization. The above line of code will find the user with the user_id passed in from the session if the session has a user_id. However, subsequent calls will remember the user_id from the first call. This is very beneficial for a current_user method since we use that method frequently throughout our application. 

**If we want to prevent unauthenticated users from creating a new comment on a post, what should we do?**

We should restrict their access at both the controller and view levels. The easiest way to do this is to create a helper method in the application_controller.rb file that checks if the current_user is a registered user, and that they're logged in. These three methods accomplish this in a readable way:

{% highlight ruby %}

helper_method :current_user, :logged_in?

def require_user
  unless logged_in?
    flash[:error] = "Must be logged in to do that."
    redirect_to root_path
  end
end

def current_user
  @current_user ||= User.find(session[:user_id]) if session[:user_id]
end

def logged_in?
  !!current_user
end

{% endhighlight %}

We can then use the require_user method before the restricted actions in the posts controller and comments controller. Finally, since we declared the method as a helper method, we can use the method in the view so that the comment box won't show up unless the user is logged in. 

**How do we set up polymorphic associations at the model layer? Give an example for the polymorphic model (eg, Vote) as well as an example parent model (the model on the 1 side, eg, Post).**

In the model vote.rb we would code:

{% highlight ruby %}

belongs_to :voteable, polymorphic: true

{% endhighlight %}

In the post.rb file we would code:

{% highlight ruby %}

has_many :votes, as: :voteable

{% endhighlight %}

**What is an ERD diagram, and why do we need it?**

ERD stands for Entity-Relationship Diagram and we use it to show the relationship of all of the tables in the application. An ERD diagram is very useful for planning an application and also provides a way to visually understand how all of the data relates to each other. 