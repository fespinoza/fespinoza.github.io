---
layout: post
title: "Opening an iOS App from a web page"
date: 2013-12-09 18:15
comments: true
categories: development tips
tags: en
---

Today at work I have to handle this case regarding my jobs web App and iOS App.
There is an invitation email to register in my application and I need to behave like this:

1. Opening the link on an iPhone will result in:
  - Open the iOS App in a certain view if the App is installed.
  - Open the iTunes page of the App if the App is not installed.
2. Open the web application if the device is any other than and iPhone.

To accomplish this, after a little research I found the solution as something like this.

```haml
!!! 5
%html{:lang => "es"}
  = content_for :javascript do
    :javascript
      $(document).ready(function(){
        if (navigator.userAgent.match(/iPhone/)) {
          setTimeout(function() {
            // launch the facebook itunes page if the next one can't be accesed
            window.location = "itms://itunes.apple.com/us/app/facebook/id284882215?mt=8&uo=6";
          }, 25);

          // launch the facebook app in the current user's profile
          window.location = "fb://profile";
        };
      });
  %body.body-landing
    .container
      = yield # rest of the registration page
```

So as you see the magic occurs in the Javascript according to the iOS URL schemas that can
be created for applications.

# References

* [http://skookum.com/blog/open-an-ios-app-from-an-email/](http://skookum.com/blog/open-an-ios-app-from-an-email/)
* [http://stackoverflow.com/questions/10907185/iphone-http-url-redirection](http://stackoverflow.com/questions/10907185/iphone-http-url-redirection)
* [http://stackoverflow.com/questions/1108693/is-it-possible-to-register-a-httpdomain-based-url-scheme-for-iphone-apps-like/1109200#1109200](http://stackoverflow.com/questions/1108693/is-it-possible-to-register-a-httpdomain-based-url-scheme-for-iphone-apps-like/1109200#1109200)

