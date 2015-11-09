---
title: "Using VCR with RSpec (with Conditional cassettes)"
layout: post
categories: rspec testing development
tags: en
---

Today I want to share a couple tips when using `VCR` with `RSpec`

When I have to interact with third party APIs, for convenience I like to use VCR to record the requests I do to that third party in order to run the test faster, when focusing more on my code given the response from the third party rather than the web interaction with the third party.

For this reason I usually use VCR with my projects, and here I want to share
my preferred tips when working with `VCR` with `RSpec`:

1. Use `RSpec` tags to generate the cassette names
2. Optionally running VCR

## Using RSpec tags to generate cassette names

Let's say we have an spec with VCR like this:

```ruby
describe ThirdPartyWrapper
  describe '#fetch_users' do
    it "returns the list of users from the 3rd party service" do
      VCR.use_cassette("custom_cassette_name") do
        # ... code testing the service ...
      end
    end
  end
end
```

Instead of calling manually `VCR.use_cassette` we can add a `:vcr` RSpec tag to the name of the test, so we can avoid that unnecessary code, so then it will be like:

```ruby
describe ThirdPartyWrapper
  describe '#fetch_users' do
    it "returns the list of users from the 3rd party service", :vcr do
      # ... code testing the service ...
    end
  end
end
```

For this to work you need to alternative enable symbols as tags in RSpec and add this to your VCR configuration

```ruby
VCR.configure do |c|
  c.cassette_library_dir = 'spec/cassettes'
  c.hook_into :webmock
  c.configure_rspec_metadata!
end
```

## Optionally Running VCR

Basically, the idea is that sometimes I want to hit the third party service to check the status of it (when is not that reliable), and I don't want to need to delete my old recordings in order to be able to do this, reason why I add the following code on my projects.

```ruby
require 'vcr_helper'

def use_vcr?
  ENV['USE_VCR'] == 'true'
end

RSpec.configure do |config|
  config.before(:suite) do
    if use_vcr?
      puts 'Using VCR'
    else
      puts 'Hitting external services'
      # disable vcr for the suite
      VCR.turn_off!(ignore_cassettes: true)
      VCR.configure do |c|
        c.allow_http_connections_when_no_cassette = true
        c.default_cassette_options = { record: :new_episodes }
        puts 'Configure VCR when USE_VCR == false'
      end
      WebMock.allow_net_connect!
    end

    puts "\n\n"
  end
end
```

So I can enable or disable VCR using the environment variable `USE_VCR`
