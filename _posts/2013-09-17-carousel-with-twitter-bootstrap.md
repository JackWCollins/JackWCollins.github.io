---
layout: post
title: "Carousels and Skydiving"
description: ""
category: 
tags: []
---

I have a genuine passion for skydiving. It is a rare passion, but skydiving is truly something I couldn't live without. I am a member of a local non-profit, volunteer run dropzone called Seven Hills Skydivers that I frequent most weekends. As a non-profit organization, we don't have a lot of money to spend on a nice website design. Conveniently, I also have a passion for web development and web design, so a natural project for me is to redesign our club's website. I'm really excited about my pet project! 

The current [website](www.sevenhillsskydivers.org) is pretty outdated. We have one member, Hairy Bob, who keeps the website updated very well and has been in charge of it for quite some time. He does a great job, but it is time for a facelift. One of the first things I wanted to implement was a carousel of pictures. Many of the top dropzones in the area have fantastic, high-quality pictures of skydiving that display through a carousel and I'm sure that drives a lot of business. So, I set out to implement my own.

I'm using Twitter Bootstrap for the new website, so my natural first step was to examine the [Javascript Bootstrap Docs](http://getbootstrap.com/javascript/). One of the last sections of their Javascript Docs is about the carousel feature. The setup is pretty well documented and very easy to customize. My code for the 5 slides and captions is below:

{% highlight ruby %}

<div id="home-carousel" class="carousel slide">

      <!-- Wrapper for slides -->
      <div class="carousel-inner">
        <div class="item active homeImg">
          <img src="/images/jay_tandem.jpg" alt="Tandem Skydive">
          <div class="carousel-caption">
            <h3>Experience the thrill of a lifetime!</h3>
          </div>
        </div>
        <div class="item homeImg">
          <img src="/images/john_exit.jpg" alt="Tandem Exit">
          <div class="carousel-caption">
            <h3>Tandem Skydive from 10,000 feet!</h3>
          </div>
        </div>
        <div class="item homeImg">
          <img src="/images/murray_iad.jpg" alt="IAD Jump">
          <div class="carousel-caption">
            <h3>Instructor-Aided Deployment</h3>
          </div>
        </div>
        <div class="item homeImg">
          <img src="/images/russ_linda_tandem.jpg" alt="Tandem Skydive">
          <div class="carousel-caption">
            <h3>More fun than you can imagine!</h3>
          </div>
        </div>
        <div class="item homeImg">
          <img src="/images/jack_student_smile.jpg" alt="Student Skydive">
          <div class="carousel-caption">
            <h3>Learn to Skydive on your own!</h3>
          </div>
        </div>
      </div>

      <!-- Controls -->
      <a class="left carousel-control" href="#home-carousel" data-slide="prev">
        <span class="glyphicon glyphicon-chevron-left"></span>
      </a>
      <a class="right carousel-control" href="#home-carousel" data-slide="next">
        <span class="glyphicon glyphicon-chevron-right"></span>
      </a>
    </div>

{% endhighlight %}

The code is farily self-explanatory. The "carousel-inner" div holds all of the images and captions. Later, some control links are added with glyphicons to give a basic user interface to the carousel. One important note - you must initialize the carousel in the application.js file of your application. The command itself is very simple - when the document is ready, initialize the carousel object.

{% highlight javascript %}

$(document).ready(function() {
	$(.'carousel').carousel();
});

{% endhighlight %}

Furthermore, if you look carefully at the carousel code, you'll also notice that I added a "homeImg" class to each image div. I implemented this in an effort to make each image the same size. Originally, I planned on making each image the same size manually, but with the wide variety of cameras used for skydiving photography I quickly realized that would not be a good use of my time. The solution was to specify the height and max-height in my application.css file while allowing the width to be automatically determined. This ensured that the aspect ratio would remain constant and the image would look natural. 

{% highlight html %}

.homeImg img {
	margin: 0 auto;
	width: auto;
	height: 400px;
	max-height:400px;
}

{% endhighlight %}

All in all, the carousel was fairly simple to implement especially with the help of Twitter Bootstrap. I am very happy with the result - I think that single feature adds a ton of value to the infantile website. Let me know what you think!

[Seven Hills Skydivers - Heroku App](http://afternoon-chamber-1003.herokuapp.com/iad)