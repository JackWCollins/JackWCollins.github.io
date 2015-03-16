---
layout: post
title: "Capybara Feature Specs and ngSrc"
description: ""
category: 
tags: []
---

I've been setting up some integration specs with Capybara for my most recent AngularJS project and I was running into some problems with image requests. The problem turned out to be a difference in how you request images in Angular versus other frameworks. ngSrc to the rescue!

For the Knockout Pool website I embedded many NFL team logos to help make the ticket interface a little more realistic. We used paperclip to store the images and generated the URLs when creating the NFL Teams. That was done in a simple model method within NflTeam:

{% highlight ruby %}

class NflTeam < ActiveRecord::Base
	validates_presence_of :name, :conference, :division

  has_attached_file :logo, :styles => { :medium => "300x300>", :thumb => "100x100>" }, s3_credentials: S3_CREDENTIALS, :default_url => "assets/missing.png"
  validates_attachment_content_type :logo, :content_type => /\Aimage\/.*\Z/

  def logo_url_small
    logo.url(:thumb)
  end
end

{% endhighlight %}

That is all fine and good, and whenever I needed an image I would return it from the Rails API as a URL and then use that URL in a template like so:

{% highlight coffeescript %}

.panel-body
	.text-center
		%span
			%p This week's pick:
			%img{"src" => "\{\{pool_entry.nfl_team.logo_url_small\}\}" }
			%h4 \{\{pool_entry.nfl_team.nfl_team_name\}\}

{% endhighlight %}

The "src" is the problematic part here. The browser first will try to fetch the image from the literal URL with the "\{\{\}\}" before Anuglar comes and replaces the expression inside the hash. This is solved by using "ng-src" since the browser won't try to fetch that image:

{% highlight coffeescript %}

.panel-body
	.text-center
		%span
			%p This week's pick:
			%img{"ng-src" => "\{\{pool_entry.nfl_team.logo_url_small\}\}" }
			%h4 \{\{pool_entry.nfl_team.nfl_team_name\}\}

{% endhighlight %}

Simple enough! The full documentation (which isn't much more than what I just wrote) is available here: 

https://docs.angularjs.org/api/ng/directive/ngSrc
