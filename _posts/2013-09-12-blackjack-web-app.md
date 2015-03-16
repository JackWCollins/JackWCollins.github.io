---
layout: post
title: "Blackjack Web App"
description: ""
category: 
tags: []
---

The first month of structured Ruby education was a whirlwind, but after some great instruction by [Tealeaf Academy](www.gotealeaf.com) I have something to show for it! [Jack's Blackjack](http://peaceful-ocean-7378.herokuapp.com/game) is the result of a ton of learning centered around Ruby, object oriented programming, Sinatra, and Heroku. There were definitely some challenges along the way, but I'm very happy with how it turned out. 

##Ruby

Putting together a Blackjack game for a first project was hard, but Ruby made the process a whole lot easier. Ruby's syntax is very easy to read (at least compared to some other programming languages) and it has a lot of nice features. A method's parentheses are optional if the method doesn't have any arguments, and you don't have to end each line with a semicolon. Those two features alone make Ruby very user-friendly. In fact, a complete amateur programmer could look over a few of my helper methods for Jack's Blackjack and probably have a pretty good idea of what they did.

{% highlight ruby %}

  def winner!(msg)
    @play_again = true
    @show_hit_or_stay_buttons = false
    session[:player_pot] = session[:player_pot] + session[:player_bet]
    @winner = "<strong>#{session[:player_name]} wins!</strong> #{msg}"
  end 

  def loser!(msg)
    @play_again = true
    @show_hit_or_stay_buttons = false
    session[:player_pot] = session[:player_pot] - session[:player_bet]
    @loser = "<strong>#{session[:player_name]} loses.</strong> #{msg}"
  end

  def tie!(msg)
    @play_again = true
    @show_hit_or_stay_buttons = false
    @winner = "<strong>It's a tie!</strong> #{msg}"
  end

{% endhighlight %}

These are my helper methods used to determine which buttons to show for the user and what type of message to display. The helper methods make use of instance variables which are very effective in determining which actions the user should be presented with.

##Instance Variables

Instance variables are initialized for an instance of an object. In Jack's Blackjack instance variables were used to hold true/false to determine if certain actions or messages should be displayed. Instance variables must be initialized as part of a method or class. The variable used to display the "Hit" or "Stay" buttons was initialized using the "before" method. This method essentially tells the web page to excecute this code before moving on to the other methods. By initializing this variable first, we ensure that the hit or stay buttons will be ready to use as soon as the game begins. The @winner and @loser instance variables were initialized in the methods above.

{% highlight ruby %}

before do
  @show_hit_or_stay_buttons = true
end

{% endhighlight %}

Now that we have instance variables to use, we can put them to work. You can see that the "Hit" and "Stay" buttons are set to "true" by default. This means that we can use the instance variable as a condition in the game.erb file to show the "Hit" and "Stay" buttons during the Player's turn.


{% highlight ruby %}

#Player Turn

<% if @show_hit_or_stay_buttons %>
<p>
  <%= session[:player_name] %> has <%= calculate_total(session[:player_cards]) %>
</p>

<p>
  What would <%= session[:player_name] %> like to do?
</p>
<p>
  <form id="hit_form" action="/game/player/hit" method='post'>
    <input type="submit" class="btn btn-success" value="Hit"/>
  </form>
  <form id="stay_form" action="/game/player/stay" method='post'>
    <input type="submit" class="btn btn-warning" value="Stay"/>
  </form>
</p>
<% end %>

{% endhighlight %}

Since the @show_hit_or_stay_buttons variable is true, we show the player's total, a prompt, and the actions to hit or stay. Now, when the player chooses to stay then we don't want to give the player the option to hit or stay any more. This is easily accomplished by setting the @show_hit_or_stay_buttons variable to false after the player hits "Stay".

{% highlight ruby %}

post '/game/player/stay' do
  @success = "#{session[:player_name]} has chosen to stay."
  @show_hit_or_stay_buttons = false
  redirect '/game/dealer'
end

{% endhighlight %}

It's easy to see how instance variables can be utilized to control what the user sees and the available actions.

##Heroku

Running a web app on your local machine is simple enough, but deploying the app to the web for others to use is a little more difficult. There have been many solutions in the past to facilitate application deployment to the web, but none have been as quick or as easy as Heroku. Heroku allows free hosting of web applications and integrates extremely well with GitHub. Heroku is by far the easiest and most inexpensive option for hosting simple web apps like Jack's Blackjack. 

Your app should already be managed with GitHub, but if not then run these few commands:

{% highlight ruby %}

$ git init
$ git add .
$ git commit -m "init"

{% endhighlight %}

Now, we create the app on heroku using... a "create" command.

{% highlight ruby %}

$ heroku create

{% endhighlight %}

Finally, we deploy the code to Heroku:

{% highlight ruby %}

$ git push heroku master

{% endhighlight %}

Just like that, your web app is live and running! Heroku assigns a random name to your application. After about 10 minutes anyone in the world can view your app. Jack's Blackjack is live at [http://peaceful-ocean-7378.herokuapp.com/home](http://peaceful-ocean-7378.herokuapp.com/home).

Good luck!






