---
layout: post
title: "FactoryGirl Advice for Associated Object Creation"
description: ""
category: 
tags: []
---

One of the many challenges I encountered during a recent [large-scale database migration](http://jackwcollins.github.io/database-migration-lessons-learned/) was managing associated objects for use in testing. After struggling through various ways to manage these associated objects with Factory Girl, I've landed on two good rules-of-thumb that I'm going to use moving forward.

## Setting the Stage

First, the basics. For our examples, let's say that you are writing software to manage safari excursion tours. You start with multiple `tour_guides`. Each tour guide `has_many` `tours` and `has_one` `tour_vehicle`. Each `tour` `has_many` `passengers`.

{% highlight ruby %}

class TourGuide < ActiveRecord::Base
  has_many :tours
  has_one :tour_vehicle
end

class TourVehicle < ActiveRecord::Base
  belongs_to :tour_guide
end

class Tour < ActiveRecord::Base
  belongs_to :tour_guide
  has_many :passengers
end

class Passenger < ActiveRecord::Base
  belongs_to :tour
end

{% endhighlight %}

With FactoryGirl, you can easily create an associated object by default by simply listing the name of the object in the factory. For example, let's say you wanted to create a `tour_guide` record with an associated `tour_vehicle`.

{% highlight ruby %}

FactoryGirl.define do
  factory :tour_guide do
    name "Tammy Tour"
    email "tammy.tour@safari.com"

    # shorthand for association :tour_vehicle, factory: :tour_vehicle
    tour_vehicle
  end

  factory :tour_vehicle do
    type "Bus"
    capacity 35
    year_of_manufacture "2010"
  end
end

{% endhighlight %}

## Be Explicit - Ony Create Associated Records with Traits

To build on our example, let's say that you have three standard types of `tour_vehicles` that live in another model: SUVs (capacity = 6), vans (capacity = 12), and buses (capacity = 35). These different types of vehicles drive some business logic. Smaller tours in the SUVs are more expensive and have different routes than the buses. While building the factories, it might tempting to have the `tour_vehicle` factory build a bus by default, and then optionally build an SUV or van with a trait override. 

However, that factory strategy becomes problematic over time. A few weeks after writing this factory, you might reach for a `tour_vehicle` and forget that it builds a bus by default. That bus might have unintended consequences for your tests. While discovering unintended behaviors is usually a good thing, this type of factory design can cause more headache than it's worth. Instead, move all attributes for an object that drive busienss logic into an explicit trait. 

{% highlight ruby %}

class TourVehicle < ActiveRecord::Base
  has_one :vehicle_type
end

class VehicleType < ActiveRecord::Base
  belongs_to :tour_vehicle
end

# NOT RECOMMENDED

FactoryGirl.define do
  factory :tour_vehicle do
    association :vehicle_type, :bus
    year_of_manufacture "2010"

    trait :suv do
      association :vehicle_type, :suv
    end

    trait :van do
      association :vehicle_type, :van
    end
  end
end

# RECOMMENDED

FactoryGirl.define do
  factory :tour_vehicle do
    year_of_manufacture "2010"

    trait :bus do
      association :vehicle_type, :bus
    end

    trait :suv do
      association :vehicle_type, :suv
    end

    trait :van do
      association :vehicle_type, :van
    end
  end
end

{% endhighlight %}

This means that if you try to create a `tour_vehicle` without specifying a trait then you'll have a vehicle with no type. That type of issue will be far, far easier to debug than tracking down where your errant `vehicle_type` record came from.

## The One Exception - Isolated Objects

I have found one exception to this rule-of-thumb. If you are creating an associated object that is isolated **and** does not drive any business logic then it may be acceptable to use associations. For example, it would be safe to use associations if the `Passenger` model `has_one` `address` and that `address` doesn't drive any business logic in the application. These types of isolated objects likely have only one association themselves, and their factory would contain only static data.

{% highlight ruby %}

class Passenger < ActiveRecord::Base
  belongs_to :tour
  has_one: address
end

class Address < ActiveRecord::Base
  # has only one association
  belongs_to :passenger
end

FactoryGirl.define do

  factory :address do
    # only static data needed for the address factory
    street "1234 Essex Ave"
    city "Berkeley"
    state "CA"
  end

end

{% endhighlight %}

These are just a couple rules that I'm going to follow going forward. Let me know if you have thoughts on this strategy or have similar strategies of your own!