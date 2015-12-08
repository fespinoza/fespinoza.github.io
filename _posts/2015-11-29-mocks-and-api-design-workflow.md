---
layout: post
title: Mocks and API design workflow
description: how to design endpoints and rapid endpoint prototyping
tags: api mocks json markdown
---

This is a very simple workflow that we did at [Hyper](http://www.hyper.no) in order to work in a better way between Backend and iOS/Android developers when building a product.

When developing mobile applications, in some cases, for both Android and iOS the building process involves interaction between *UI desing*, *Client Developers*, *Backend Developers*, in that sense, this workflow is to aim to ease the communication between *Client* and *Backend* developers.

Conceptually, and API endpoint defines a **contract for the communcation between clients and backend**, so then the idea is to define **API Proposals** in markdown to stablish the parts that involve a web request to an endpoint.

Because the client/server communication is in both directions, the best way to define that contract is doing that collaboratively, this can be done without complications in a markdown file, given it's easy to create and modify. This "proposals" also help you to understand the real needs of the endpoints, as many times you know the information that you need to fill in a screen given an UI sketch.

Another interesting point for the proposals, is that allow you to think in terms of the possible edge cases, since the proposal is a more "concrete" representation of what the web request is about, and finally, creating a markdown proposal is easy to do.

## The workflow

This workflow has 5 simple steps:

1. Create endpoint proposal
2. Discussions/corrections
3. Agree
4. Mock it
5. Profit

### 1. Create endpoint proposal

A web request is composed by a:

- request: http verb, path, headers, parameters
- response: http status code, headers, body

So a proposal then needs to inform all this part of the request, as an example of use of it.

<script src="https://gist.github.com/fespinoza/440b250d19a0e9979909.js"></script>

This proposal can be done by anyone, including client developers.

### 2. Discussions/corrections

The best way to achieve this is by creating a Pull Request on GitHub.

So then all the developers involve can discuss about the fields, parameters, values, structure and possible edge cases.

This point is particularly nice since the parts will exchange ideas, improving the design of the endpoints and raising questions about the features in a very cheap way.

Since the proposal is in a Pull request, it's easy to keep track of the changes to it.

### 3. Agree

This means mostly to merge the pull request, treating it as an initial contract.

### 4. Mock it

Since the whole communication request/response was defined as example, in rails it's very easy to respond to a particular path + http verb, returning a `status code` and a `body`, [I wrote a gem to help you with that](https://github.com/hyperoslo/mock_api_rails).

This requires a JSON payload to be placed in a directory, that JSON is the body of the request defined in the *endpoint proposal*.

```ruby
Rails.application.routes.draw do
  api vendor_string: 'myapp' do
    version 1 do
      mock_api :post, '/api/cat_videos', status: 201
    end
  end
end
```

### 5. Profit

The previous step can be done in under 10 minutes, from code to deploy, meaning from this point on, the clients can use the endpoint without mocking any part of the web interactions, so they can complete the UI implementations, meanwhile the Backend developers do the actual functionality of the endpoint.

After that, given that the Backend developers have a contract to _stick to_, then when they release the actual implementation of the endpoint can be completely painless for the clients.*

Some advantages of this approach are:

- Quickly detect possible conceptual problems for the UI design.
- Ensure the requirements are met for both backend/client developers.
- Cheap and simple tool of communication.
- Existence of a registry of decisions and discussions.
- The endpoint proposal can be used by backend clients as spec.

This post is a write-out of my lightning talk in Full Stack Fest 2015, [near minute 15](https://www.youtube.com/watch?v=0EfomIPH48g&feature=youtu.be&t=15m3s)

<iframe src="https://www.youtube.com/embed/0EfomIPH48g" frameborder="0" allowfullscreen></iframe>
