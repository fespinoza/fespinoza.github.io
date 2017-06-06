---
layout: post
title: Creating a QuickLook plugin for a document-based macOS application
description: required knowledge and tutorial to create QL plugins.
tags: swift document-based-apps cocoa macOS quicklook
---

I've been developing my first mac application, [Linked Ideas][linked-ideas], where I create documents where I write down ideas and connect them easily. For this application, due the presence of a custom document type I wanted to create a QuickLook plugin to generate previous of this documents.

My first direction to learn about this was apple's [quicklook programming guide][quicklook-programming-guide], where I learned the basics of the different types of plugins. Unfortunately the examples there were in Objective-C, but my application was written in Swift, even more, my application already contained code to render a document so i wanted to reuse that code for creating this plugin.

Googling around on how to write a quicklook plugin I realized the lack of material (or outdated one) to achieve this task. Also, some tutorials I found online started with _Create a new Xcode project for the QuickLook plugin_, but in my case I needed to share code and embed the final result of the quicklook plugin into my macOS app.

I tried also to get some examples on the internet about _How to develop quicklook plugins with Swift_, but according to what I found in [apple's dev forum][no-swift-quicklook], I realized this was not possible.

After a lot of struggle, here is a list of things I had to learn to achieve my goal:

1. What are _Xcode targets_.
2. How to share code between targets.
3. Use Swift code in Objective-C land.
4. How to use _Build Phases_ to embed the quicklook plugin into the macOS app.

First, to learn about what Xcode targets are I recommend the [Introduction to Xcode][intro-to-xcode] talk from WWDC 2016. In simple terms, Xcode targets allow you to have different executables as a product of each target, and a Xcode project can contain many targets.

Second, for sharing code within different targets and projects I recommend this very good talk from WWDC 2014 about [Sharing code between iOS and macOS][sharing-code-ios-osx], here they briefly mention that **for small projects, like mine, is better to include the files in both targets**, the target for the macOS app and the target for the Quick Look plugin.

Third, given that the code of my application was in Swift and I wanted to reuse it to generate the QuickLook plugin preview I needed to be able to access Swift from Objective-C, to learn about that I recommend the WWDC talk [Swift and Objective-C iteroperability][swift-and-objc]. In my case, since my code was part of the two targets of interest, I just had to declare my swift clases that I wanted exposed to Objective-C as `public` and finally include the `TARGETNAME-Swift.h` header in my Objective-C file. When trying to compile my quicklook plugin I found a couple problems whose solution are in [here][solution-one] and [here][solution-two]

Fourth, I needed a way to embed my QuickLook plugin in my macOS app for convenience of shipment, the [QuickLook Programming Guide][quicklook-programming-guide] mentions that plugins can be embedded in the macOS app under the `MyApp.app/Contents/Library/QuickLook` directory. To achieve this I needed to learn how to use _build phases_, in particular the _copy files_ build phase. That didn't work entirely, since I had an issue regarding code signing with the QuickLook plugin, luckily [Stack-Overflow came to rescue][solution-three] one more time.

## Now you can create your own plugin, right?

If you have been following along with my explanations so far you may get the same feeling that I got when googling around to figure out what to do. I found a lot of _just do X and it will work_, then I was _how do I do X in my case?_.

For that reason I created a full-length video tutorial where I go step by step creating the pluging and I show how to perform all the previous step I mentioned about.

<iframe src="https://www.youtube.com/embed/g7O0KRFaK2E?ecver=1" frameborder="0" allowfullscreen>
</iframe>

Also, since my application is Open Source, you can find the [Pull Request where I added this plugin to my App][linked-ideas-pr].

I hope this information can help you to create your quick look plugin. For suggestions and fixes to this post you can tweet me to [@fespinozacast][my-twitter]

[linked-ideas]: https://github.com/fespinoza/linked-ideas-osx
[quicklook-programming-guide]: https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/Quicklook_Programming_Guide/Articles/QLArchitecture.html#//apple_ref/doc/uid/TP40005020-CH4-SW4
[no-swift-quicklook]: https://forums.developer.apple.com/message/227783#227783
[intro-to-xcode]: https://developer.apple.com/videos/play/wwdc2016/413/
[sharing-code-ios-osx]: https://developer.apple.com/videos/play/wwdc2014/233/
[swift-and-objc]: https://developer.apple.com/videos/play/wwdc2015/401/
[solution-one]: http://stackoverflow.com/questions/34412625/user-defaults-migration-from-obj-c-to-swift
[solution-two]: http://stackoverflow.com/a/25842284/611678
[solution-three]: https://stackoverflow.com/questions/14474961/how-to-distribute-app-with-quicklook-plugin
[linked-ideas-pr]: https://github.com/fespinoza/linked-ideas-osx/pull/62
[my-twitter]: https://twitter.com/fespinozacast
