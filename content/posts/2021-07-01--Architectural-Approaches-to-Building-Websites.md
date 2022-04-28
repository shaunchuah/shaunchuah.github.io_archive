---
title: 4 Major Architectural Approaches to Building Websites in 2021
date: "2021-07-01"
template: "post"
draft: false
slug: "architectural-approaches-to-building-websites"
category: "Web Development"
tags:
  - "Web Development"
  - "Javascript"
  - "Python"
  - "CSS"
description: "This article is for people who are new to web development, lost in the myriad of web frameworks and are asking themselves: What are the real differences in the frameworks?"
socialImage: "/media/webarchitecture.jpg"
---

![Web Architecture in 2021](/media/webarchitecture.jpg)

This article is for people who are new to web development, lost in the myriad of web frameworks and are asking themselves:

**What are the real differences in the frameworks?**

Having spent the last year getting up to speed with the state of the web, I'm going to summarise the 4 major architectural approaches to launching a new website in 2021. Although I write about them as discrete categories, in reality they exist on a continuum and some frameworks will blur the lines between these categories (eg. NextJS). For the beginner though, it is helpful to have a rough overview of the major choices available to you.

## #1 Traditional HTML/CSS/Javascript

The original way websites were build. Just pure html, css and maybe a bit of javascript. Throw in a css framework or sprinkle a bit of javascript. Ideal for one-page brochure websites which do not need constant updates of content, at a stretch maybe up to 2-3 pages. If you're planning anything bigger, try one of the other options below.

### Deployment Methods

1. Grab index.html and all associated files and SFTP it to apache or nginx or CDN of your choice
2. A better way - Setup a git repo on github and use git clone into your apache/nginx server. Makes updating website a matter of running git pull.

**Advantages:** Fast to develop, extremely easy to customise, easy to deploy, easy to scale.

**Disadvantages:** Does not suit websites which have more than one page or content that needs updated regularly.

## #2 Full Stack CMS

_Examples: Wordpress, Django, Ruby on Rails, Laravel_

These are the traditional server side frameworks which still run most of the web today. The magic here is that the website is connected to a backend database which can hold different content and render the content into html based on templates. These frameworks also allow for user authentication and storing things like contact form data.

### Deployment Methods

1. Usually custom to the framework. Generally requires installing a few pieces of software on your server. Think of the traditional LAMP stack (Linux Apache MySql PHP).
2. Use docker to automate the framework deployment steps.
3. Some webhost may offer one-click deployment eg of Wordpress
4. Platform as a service eg. Heroku

**Advantages:** Well used, well documented, tried and tested way of having a website hosting content which can be updated easily.

**Disadvantages:** Need to deploy, monitor and look after database backend.

## #3 Decoupled Javascript Frontend/API Backend

_Examples: React/Vue + Django Rest Framework/Laravel/Ruby on Rails, React/Vue/Mobile Frontend + AWS Amplify/Google Firebase (Backend as a service)_

The trendiest way to build websites in 2021. Pick a frontend javascript framework and communicate with databases via an API gateway. Makes for very slick websites and web applications on par with desktop apps.

### Deployment Methods

1. More involved than #2. Deploy both the frontend and the backend.
2. Docker is highly recommended here.

**Advantages:** Amazingly slick and interactive websites. Easy to split work between multiple developers.

**Disadvantages:** Need to deploy, monitor and look after two projects. Double the work of the traditional frameworks.

## #4 Full JAMStack Static Site Generation

_Examples: Hugo, Jekyll, GatsbyJS, Docusaurus, VuePress, Gridsome_

Number 3 and 4 are similar in concepts at first glance. The slight difference is that these full on static site generators are more suited for webpages where the content can be preloaded (eg blogs, articles) and less suited for web applications which have users interacting with the database (such as chat apps/social media).

The key concept is that before the site is deployed, the engine pulls all the content and all the code and spits out html/javascript/css files. These files are then deployed like #1 to a server or to a CDN for global scaling.

**Advantages** Can create single page websites (like this blog) which do not reload the browser when navigating between pages. Makes the website feel really slick.

**Disadvantages:** This is for websites rather than web applications (use #3 instead). Initial learning curve on the generator of choice.

## Conclusion

The right architectural choice will depend on the scope of what you're building and the expected scale/audience. Brochure websites are different to blogs which are different to web applications.

Something important to note is that web projects are often unpredictable and tend to grow in size and complexity with time. You may reach a point where you need to consider a rework of the architecture (usually this is a good problem to have if your site traffic is growing!).

Choosing the right approach and avoiding premature optimisation will avoid wasted effort and speed your time to deployment.
