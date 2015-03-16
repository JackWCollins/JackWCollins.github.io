---
layout: post
title: "Resources, forms, partials, and more"
description: ""
category: 
tags: []
---

Week 2 of ''Rapid Prototyping with Ruby on Rails'' has been even better than the first. This week encompassed named routes, RESTful resources, forms, helpers, partials, and more! It was a lot to take in, so I'll summarize each of those topics here.

**Name all the 7 (or 8) routes exposed by the resources keyword in the routes.rb file. Also name the 4 named routes, and how the request is routed to the controller/action.**

{% highlight ruby %}

# Assuming you are getting resources :posts

$ rake routes

get '/posts', to: posts#index                  => posts_path
get '/posts/:id', to: posts#show               => post_path(:id)
get '/posts/:id/edit', to: posts#edit          => edit_post_path(:id)
get '/posts/new', to: posts#new                => new_post_path
post '/posts', to: posts#create
patch '/posts/:id', to: posts#update
put '/posts/:id', to: posts#update
delete '/posts/:id', to: posts#destroy

{% endhighlight %}

**What is REST and how does it relate to the resources routes?**

REST stands for "REpresentational State Transfer" and is a set of conventions that expose URL endpoints which allow you to perform operations on resources. Rails implements this well by using the "resources" keyword which exposes the above URL endpoints. Those URL endpoints correspond to the CRUD actions on the controller side (create, retrieve, update, and delete).

**What's the major difference between model backed and non-model backed form helpers?**

The major difference is that model backed form helpers are tied to an object. There is also a slight syntactical difference. Model backed helpers look like this:

{% highlight ruby %}
<%= f.text_field :title %>
{% endhighlight %}

Non-model backed forms include tags which generate HTML. 

{% highlight ruby %}

<%= text_field_tag :title %>

{% endhighlight %}

Additionally, model backed forms have some functionality built in. For example, if you run into some validation errors when you submit a page, model backed forms can automatically repopulate certain fields for you. 

**How does form_for know how to build the "form" element?**

You must pass form_for and object, and based on the state of that object form_for will know whether or not to give it the "new" route, or give it the existing URL which is routed to the update action. 

For example, take this form:

{% highlight ruby %}

<% form_for @post do |f| %>
	<%= f.label :title %>
	<%= f.text_field :title %>
<% end %>

{% endhighlight %}

If the @post object exists in the database, then Rails knows to route the form to the update action. If @post does not exist in the database, then Rails will route to the new action.

**What's the general pattern we use in the actions that handle submission of model-backed forms (ie, the create and udpate actions)?**

{% highlight ruby %}

def create
  @post = Post.new(params.require(:post).permit(:url, :title, :description))

  if @post.save
    flash[:notice] = "Your post was saved."
    redirect_to posts_path
  else
    render :new
  end
end


{% endhighlight %}

{% highlight ruby %}

def update
  @post = Post.find(params[:id])

  if @post.update(params.require(:post).permit(:url, :title, :description))
    flash[:notice] = "Your post was updated."
    redirect_to post_path(@post)
  else
    render :edit
  end
end


{% endhighlight %}

**How exactly do Rails validations get triggered? Where are the errors saved? How do we show the validation messages on the user interface?**

Rails validations are triggered any time we have a method call that tries to hit the database. The errors are saved on the object itself in the errors array and can be retrieved by iterating through the errors array and parsing out the messages. For example:

{% highlight ruby %}
<% if object.errors.any? %>
  <div ...>
    <% obj.errors.full_messages.each do |msg| %>
      <li><% msg %></li>
    <% end %>
  </div>
<% end %>
{% endhighlight %}

**What are Rails helpers?**

Rails helpers are methods that help us format view template code. Additionally, they can extract logic out of the view template code. This allows us to clean up our view templates. By delaring helper methods within the "application_helper.rb" file, we are albe to use the methods in view without displaying all of the logic directly inside the view. Furthermore, if we have helpers that we know only pertain to a specific resource then we can put them in post-specific helpers such as "posts_helper.rb". 

**What are Rails partials?**

Rails partials allow us to consolidate redundant pieces of HTML throughout many different views. Partials are named with an underscore that the beginning:

{% highlight ruby %}

_errors.hrml.erb

{% endhighlight %}

**When do we use partials vs helpers?**

Partials are used when you want to consolidate HTML, while helpers are used to consolidate code logic from the views. 

**When do we use non-model backed forms?**

Non-model backed forms are used when you don't have a model to work with for the object, such as creating a new session during a user login. 
