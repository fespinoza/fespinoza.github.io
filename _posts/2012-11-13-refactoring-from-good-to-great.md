---
layout: post
title: "Talk Review: Refactoring from good to great"
date: 2012-11-13 18:46
comments: true
categories: development talks
tags: en
---

This talk is by Ben Orenstein and is about refactoring in ruby and it's really cool

<iframe src="https://www.youtube.com/embed/DC-pQPq0acs" frameborder="0" allowfullscreen></iframe>

First a personal advice

<div style='font-size: 1.4em;'><p><strong>If you plan to refactor anything, make sure you have tests of the class you want to refactor!</strong></p></div>


In any case, I want to summarize the main points exposed in this talk, they are:

* **Public APIs with one line methods**: this an advice focused on readability of the code, considering that code like this is usually conceptually better and more usable, in my opinion a good suggestion but definitely not a rule.
* **Tell don't ask**: this is a OO principle that it's about that inside a class A, don't check for the status of an object of class B, just call the action you want the object to perform and delegate the responsibility of the state to the object. A simple code example, it means replace a code like this:

```ruby
class Order
  ORDER_PRICE = 10.to_money

  def initialize(customer)
    @customer = customer
  end

  def charge_customer
    if @customer.present?
      customer.charge!(ORDER_PRICE)
    end
  end
end
```

Replace it for:

```ruby
class Order
  ORDER_PRICE = 10.to_money

  def initialize(customer)
    @customer = customer
  end

  def charge_customer
    customer.charge!(ORDER_PRICE)
  end
end
```
But what about when the customer it's not provided?, for that the next advice

* **Null object pattern**: This programming pattern could be use to delete a great amount of conditionals and use the _tell don't ask_ principle, the idea is have an object with the same interface to the one we want to replace when there is none, for the previous example, a null object pattern application would be

```ruby
class Order
  ORDER_PRICE = 10.to_money

  def initialize(customer)
    @customer = customer || NullCustomer.new
  end

  def charge_customer
    customer.charge!(ORDER_PRICE)
  end
end

class NullCustomer
  def charge!(amount)
  end
end
```

In this way using polymorphism we avoid the conditionals and can be specially useful with a user model, and the typical line of code `if current_user? do...`. The downside, you have to keep in sync two interfaces, but the programming is so easy that this drawback is really affordable.

* **Don't Be Afraid to extract terms into their own classes**: it's better explained at the video, in it the speaker replaces two date variables `@start_date` and `@end_date` for `DateRange.new(start, end)` in this way you enforce the condition of the two dates presence and you can handle the corresponding logic in its own class.

* **Refactor when going to change**: this sounds a great advice to me, to be more pragmatic about when to refactor and what, the idea it's when you have to make a change to a class, make the most effort (the difficult stuff) in refactor that class, so making the change takes much less effort (the easy stuff) that originally would take.

* **Refactor God Objects**: The speaker talks about god objects, that are the most important objects of your system and focus on them in term of refactoring and extract business logic from them, this objects are:

  * highest line code models
  * high change rate files
  * class with frequent bugs

And that's it for this talk, I personally recommend the [Destroy All Software Screencast](destroyallsoftware.com) by [@garybernhardt](https://twitter.com/garybernhardt) that talks a lot of software craftsmanship and he is a really experienced programmer.
