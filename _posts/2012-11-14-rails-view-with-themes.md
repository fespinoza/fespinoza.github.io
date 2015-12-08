---
layout: post
title: "Rails views with Themes"
date: 2012-11-14 23:10
comments: true
categories: development rails
tags: en
---

Almost all my development post so far have been about other people knowledge, but today I'm gonna talk about my solution to a problem I was facing within a work project.

The thing is that in this project I'm the only developer, but there'll be designers working on it too, specifically making new versions of the views of the site.

Rails don't support view themes by default so I wanted to extent rails to have this feature, but after a little investigation, the solution was pretty simple, I only have to modify the paths where rails looked for the views.

To implement this solution first I had to create a new directory called _default_ that represents the default theme

```
mkdir app/views/default
```

Then move all my other folders to ```app/views/default```, and then I created a ```config/theme.yml``` file with the content

```ruby
current_theme: "default"
```

Finally to make the trick I wrote an initializer to modify the lookup paths of rails views

```ruby
# project/config/initializers/themed_views.rb

# Set default theme for views and assets to inheritate from
ActionController::Base.view_paths = 'app/views/default'
['stylesheets', 'images', 'javascripts', 'fonts'].each do |subdirectory|
  Rails.application.config.assets.paths.insert 0,
    Rails.root.join("app", "assets", "themes", "default", subdirectory).to_s
end

theme_config = YAML.load(File.open("config/theme.yml")) rescue nil
theme_config['current_theme'] ||= 'default'

if theme_config['current_theme'] != 'default'
  ActionController::Base.prepend_view_path "app/views/#{theme_config['current_theme']}"
  ['stylesheets', 'images', 'javascripts'].each do |subdirectory|
    Rails.application.config.assets.paths.insert 0, Rails.root.join("app", "assets", "themes", theme_config['current_theme'], subdirectory).to_s
  end
end
```

The thing with this solution it's that if I create a new theme (a new directory under app/views) I can overwrite just the specific views that I want to change, and then the rest will fallback to the default theme.

The same thing happens with the assets, so now I can have specific assets for my app and only load those assets I use.

The thing is rails it's a beautiful framework, this a few lines of code you can extend it, just like that, that makes, in my opinion, rails just beautiful.

I solve this like this, because I didn't find a gem solving this, but maybe there is a better solution around.

## References

1. [layouts and rendering in rails](http://guides.rubyonrails.org/layouts_and_rendering.html)
2. [asset pipeline](http://guides.rubyonrails.org/asset_pipeline.html)
