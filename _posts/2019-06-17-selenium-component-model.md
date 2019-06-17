---
layout: post
title: "How We Use a Component Model in Selenium\n to Increase Maintainability"
date: 2019-06-17
---

  Over the past 9 years of [Sprout Social's](https://sproutsocial.com) existence,  our [Selenium](https://www.seleniumhq.org/) framework has grown into 100's of scenarios with as many page classes and factory containers. We've noticed it has become cumbersome to manage and as such, our QA Engineers thought the models in use needed a refresh. So in recent times, we adopted what we like to call the **Component Model**.

  *Note: All code examples can be found in the following [Github Gist](https://gist.github.com/ddaypunk06/4f3e62d4b049cf2aa460e3155e2aa099?ts=4).*

  The idea for this component model was a result of a few things: 
* Modern websites aren't really built strictly of static "pages" any more
* A Grow@Sprout talk on the [SOLID principles](https://medium.com/@dhkelmendi/solid-principles-made-easy-67b1246bcdf) by Uncle Bob
* Our web app’s use of [React.js](https://reactjs.org/), which uses a similar idea of components for encapsulation

  Modern websites (i.e. web applications) are highly dynamic pieces of software with global bars, side bars, content views, and pop-outs. Many of these pieces are yet composed of other smaller pieces. Reflecting on this and the SOLID principles, we realized that the monolithic classes the page-object model created were not working to our advantage anymore. 

  We noticed we had multiple functional zones and really only one spot for actual page content. We had a global top nav bar that also included settings access. Additionally, there is a secondary left nav bar for navigating within functional areas. A lot of pages also contained a rail on the right side commonly housing date pickers and filters for the content view. Please refer to the mockup below to help visualize this:

![](https://thepracticaldev.s3.amazonaws.com/i/s37jtenw51dgmeq8849x.png)

  We noticed plenty of areas in the framework where we could [DRY (Don’t Repeat Yourself)](https://www.codeproject.com/Articles/36712/SOLID-and-DRY) up the code. This is included in the SOLID software development principles. Buttons, inputs, and data visualizations all share common code in the UI implementation. Why, then, should our tests implement multiple ways to get and interact with filters across multiple page classes? 

  Instead, we agreed that the components our selenium tests use should be a 1:1 representation of their equivalent web UI component. Take this example [CheckboxV1](https://gist.github.com/ddaypunk06/4f3e62d4b049cf2aa460e3155e2aa099?ts=4#file-checkboxv1-java) (click for the gist code snippet) class. The most beneficial aspects of this model are: the component houses its unique selenium selector, and specific constructors and methods that your tests can consume. This significantly enhances the readability of code and the maintainability of the framework.

  After using these components for a while, we started to notice that code was being repeated between classes. In order to *DRY* the repetition up, we created a class called [BaseComponent](https://gist.github.com/ddaypunk06/4f3e62d4b049cf2aa460e3155e2aa099?ts=4#file-basecomponent-java) to control much of the basic behavior of any component. One can then extend all other components from this BaseComponent to ensure every component implementation is as simple as possible.

  Combining implementation from our original **CheckboxV1** with **inheritance from BaseComponent** further simplifies the component and increases the maintainability of the framework. [CheckboxV2](https://gist.github.com/ddaypunk06/4f3e62d4b049cf2aa460e3155e2aa099?ts=4#file-checkboxv2-java) allows BaseComponent to do most of the heavy lifting not specific to the particular component. We also have one less spot to check when fixing issues as most of the behavior is within BaseComponent instead of multiple versions of similar functionality scattered across multiple components.

  A quick note on the above **CheckboxV2** implementation - we tend to use the String argument constructor the most with components that have attributes in the UI code. The *By* and *WebElement* argument constructors are backups for elements in our UI that might not contain special data attributes, what we refer to as QA attributes, but the follow same/similar component structure in the DOM. However, we encourage our team to add their own QA selectors if able to or further collaborate with the developers to get them added. Having them usually allows the component code and subsequent Selenium automation to be much simpler overall.

  Here is a quick example of how we then use the components within a [Page](https://gist.github.com/ddaypunk06/4f3e62d4b049cf2aa460e3155e2aa099?ts=4#file-page-java) **and** [Step Definition](https://gist.github.com/ddaypunk06/4f3e62d4b049cf2aa460e3155e2aa099?ts=4#file-checkboxsteps-java) classes.

  It has been a fun and interesting journey getting to this point through many refactors of our framework. However, it has proved time and time again to make our framework a bit easier to grok as well as maintain. We are already beginning to think of ways to improve upon this further such as with a wrapper class called *Selector* to help with formatting and making BaseComponent more friendly to work with. We look forward to providing more information on those in the future

  Have you or your organization used or are currently using something similar? Please share your experiences in the comments!

  - powered by [Jekyll](http://jekyllrb.com)
