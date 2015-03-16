---
layout: post
title: "Inheritance and Composition"
description: "An intro to Inheritance and Composition"
category: 
tags: []
---

Inheritance and Composition are two basic concepts in object oriented programming. They are one of the easiest and most concrete ways to exercise DRY programming - Don't Repeat Yourself. Both techniques are a way to organize frequently used methods in one place so that many different classes can use them. 

##Inheritance

The most straightforward way to think about inheritance is to think about Parent -> Child -> Grandchild relationships. Inheritance is referred to as an "is-a" relationship. Methods defined in the Parent class can be used by the Child or Grandchild class. Similarly, methods defined in the Child class can be used by the Grandchild class but can <em>not</em> be used by the Grandchild class. A classic example of inhertance is readily available when looking at animals. 

{% highlight ruby %}

class Mammal
  def initialize
    @alive = true
  end

  def warm_blooded?
    true
  end
end

class Dog < Mammal
end

puppy = Dog.new
puppy.warm_blooded?

{% endhighlight %}

This code will return true because puppy, the Dog object, <strong>inherits</strong> the .warm_blooded? method from the Mammal parent class. This is a good way of organizing methods if you are <strong>100% sure</strong> that your Child objects should inherit every method from the parent object. Many programmers shy away from Inheritance for the more versatile Composition style of organization. 

##Composition

Composition is a far less rigid way of organizing methods and is preferred by programmers for most applications. The composition relationship is referred to as a "has-a" relationship because classes can <strong>have</strong> the classes defined by composition but might not actually <strong>be</strong> a member of a parent class. Methods used for composition are organized using <strong>modules</strong>. Let's use the example above to introduce a new "Swimmable" module to our Dog class, but not the Cat class.

{% highlight ruby %}

class Mammal
  def initialize
    @alive = true
  end

  def warm_blooded?
    true
  end
end

module Swimmable
  def can_swim?
    puts "I can swim!"
  end
end

class Dog < Mammal
  include Swimmable
end

class Cat < Mammal
end

puppy = Dog.new
kitty = Cat.new

puppy.warm_blooded?
kitty.warm_blodded?

puppy.can_swim?
kitty.can_swim?

{% endhighlight %}

Now, let's see what this would return:

{% highlight ruby %}

true
true
I can swim!
<No Method Error>

{% endhighlight %}

In the code above, we <strong>mix in</strong> the Swimmable module into the Dog class but not the Cat class. What does this mean? It means that any object created from the Dog class can use the .can_swim? method but objects from the Cat class cannot access the .can_swim? method. Objects of the Dog and Cat classes don't have to be a member of the Swimmable class, but instead the Swimmable class can be <strong>mixed in</strong> to any classes that require methods from the module. We can already see the versatility that Composition possesses. 

