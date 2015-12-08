---
layout: post
title: "Talk Review: Fast Rails Tests"
date: 2013-03-29 19:10
comments: true
categories: talks development
tags: en
---

Today I watched this amazing talk by [Corey Haines](https://twitter.com/coreyhaines) called **"Fast Rails Test"** and here I'm going to talk about some of the main points of this talk.

<iframe src="http://player.vimeo.com/video/30893836" class="vimeo" frameborder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe>

It's interesting to see how people is using rails, first they began to understand rails and its test directory, that made testing part of the default development cycle.

After a while, people began to talk about **Fat Models, Skinny Controllers** for extracting the business logic into models, and a lot of other concepts like **REST**, **Factories**, **Mocks and Stubs**, etc. as part of the way you develop rails applications and how to test them.

The problem was you ended up with having a huge test suite that takes forever to run. Then Corey speaks this is a problem and the differences between *Test First and Test Driven* in development and how you react to pain in each one:

## Test First

If test are slow I'll modify my test in order to avoid that pain, that's why *specjour*, *guard* and *spork* appeared, but in reality this are band-aids for the problem.

## Test Driven

If test are slow, it is because the application is too complex. If you have a small test that takes forever to run is because is coupled a lot with a lot of code, in the case of rails, the conclusion is **rails itself is a 3rd party dependency** and is not really needed to test your core business logic.

That said, the proposed approach is to use plain ruby classes or modules to implement your business logic, and test them in isolation of rails.

As [Gary Bernhardt](https://twitter.com/garybernhardt) said in his **Sucks/Rocks series**: *"Rails is not your application"*, in that sense, it makes perfect sense to test your core App separately.

## An Example of Business Core Code Outside Rails

This is a classic code that could be found in a rails App

```ruby
class ShoppingCart < ActiveRecord::Base
  has_many :products

  def total_price
    products.map(&:price).inject(:+)
  end
end
```

The problem here, is that you have to load all rails to test the total price method. Now, Corey proposes two different approaches to test this outside rails which I will show with their test code (Taking Corey's code)

### Class Approach

I like to put external rails classes under the *lib* directory, that's a personal preference. So this would be the test code:

```ruby
# spec/lib/calculate_total_price_spec.rb

describe CalculateTotalPrice do
  it "returns 0 when there are no products" do
    expect(CalculateTotalPrice.of([])).to eq(0)
  end

  it "returns the sum of prices of the products" do
    products = [stub(price: 5), stub(price: 10)]
    expect(CalculateTotalPrice.of(products)).to eq(10)
  end
end
```

You run fast the spec with

```
rspec spec/lib/calculate_total_price_spec.rb
```

And the implementation of the class

```ruby
# lib/calculate_total_price.rb

class CalculateTotalPrice
  def self.of(elements)
    elements.map(&:price).inject(:+)
  end
end
```

Note that the implementation is almost the same, and because ruby is dynamically typed, this class will work with any class that has a price method and return an object that respond to "+". Finally the active record class uses it like:

```ruby
class ShoppingCart < ActiveRecord::Base
  has_many :products

  def total_price
    CalculateTotalPrice.of(products)
  end
end
```

The thing is that for testing the logic of total price, now we don't need rails at all making our tests amazingly fast. The example is very simple but it can be used for more Apps too.

### Module Approach

That example you'll find in the video of the talk ;), but the idea is almost the same as the class approach.

In conclusion, taking care of the quickness of our test suite in terms of design, recognizing third-party libraries and improving our design by doing so, make us better coders, because tests tell us what to do to complete our logic, you will run them more often, so listen to your test!.

I'll leave a link to another talk mentioned by Corey, that's awesome too and I'll be summarizing (more concisely) called [The Deep Synergy Between Testability and Good Design](http://vimeo.com/15007792)

