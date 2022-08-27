---
title: "Put your personal side project on the market - Perimeter"
date: 2022-08-24T16:00:00+02:00
draft: false
showTableOfContents: true
tags: ['industralization']
description: "You've got a genius app idea and want to deploy it ? I've got something for you 😊"
showTaxonomies: true
---
Personal side projects are popular in 2022, they allow to build portfolios and add value to our job applications, or they simply became a hobby for people, as it did for me, who like to build things for fun in their free-time. But, sometimes, we feel like we've had a good idea, to the extent that we want to deploy it and sell it to potential customers. This series', illustrated with examples and my own personal experience, will tell this process from A to Z !

{{< alert >}}
This series of articles is dedicated to developers who like to play with many technologies. Every technical concept will not be entirely covered (like the actual application programming, for example).
{{< /alert >}}

## Landing Zone

In the beginning of this year, for my birthday, I had this crazy idea, but easy to make : I wanted to allow my friends to add songs to my Spotify queue, as I was tired with this choir during our parties. After building a prototype, I rolled it out for this specific party. I was very enthusiast and happy, I felt like I have made something useful, and I got many positive feedbacks to this early version, and also many ideas, which was the most important.

After discussing with potential customers and other enthusiasts, I decided to deploy this project in order to sell it online. This series of articles will relate my feedback on this process, as I do so !

It mainly addresses the technical challenge behind a production deployment, specifically when you have a "draft" prototype of your future product. But I wanted to share a global point of view around this process, so this first article will be about the conception of a **perimeter** around the application in order to put it on the market.

{{< badge >}}
Example
{{< /badge >}}

In order to have specific examples, while not actually messing with my main project, I built a basic app which will be useful to illustrate the different concepts and arguments. It is a website that allows its users to create to-do lists, and to share it with others in order to collaborate over it. Very simple, in fact. Here is a preview of the first version.

{{< figure 
    src="/posts/industrialization/perimeter/todo.png"
    alt="to-do list website screenshot"
    caption="Screenshot of the shared to-do lists website"
    >}}

This application consists of a front-end and an API. The front-end has been built with [Vue](https://vuejs.org), Vue Router and [Bulma](https://bulma.io). The API has been designed using [NodeJS](https://nodejs.org) with [Express](https://expressjs.com) and uses [MongoDB](https://mongodb.org) to persistently store user data.

The code source is entirely free and available at the following repositories :
- [https://github.com/nkirchhoffer/idt-example-frontend](https://github.com/nkirchhoffer/idt-example-frontend)
- [https://github.com/nkirchhoffer/idt-example-backend](https://github.com/nkirchhoffer/idt-example-backend)

We are now ready to begin the process of deploying our to-do lists prototype !

## Step 0 : Believe in your project

This step really isn't one, that's why it's called "step 0". However, it is essential to successfully deploy your project.

Believe in your ideas !

If deploying this project crossed your mind at some point, it is legitimate, you believe in its potential and in its capacity to solve your customers' issues. You must not give up !

Seek for people who you can trust and that will help you to complete this challenge. Having constructive, not necessarily positive, feedback is really helpful. It allows you to enlarge your point of view, and to not fall in the confirmation biais. Having good friends and people to trust is essential to have the energy to stay focus in your objectives.

## Step 1 : Write a "state of the art"

The State of the Art allows you to understand the current situation of your project and to where you want to go.

Even if, when you decide to deploy this project, you don't have completed much technically, it is always interesting to do this little exercise in order to have a global point of view of your future product, and of the steps to complete before you could really begin to work on it.

In my case, given that my personal project was already in a "prototype" state, I have a code base that I can use and a set of features that will be the application's backbone.

Here is the framework that I followed for my project :
- What are the features currently implemented on my prototype ?
- What are the technical limitations of my prototype ?
- What are the positive aspects of my prototype ?
- Do I have an identity for my product ?
  - A name ?
  - A brand ? (graphical)

{{< badge >}}
Example
{{< /badge >}}
Applied to our "to-do lists" example project, here is an example of State of the Art :
- Features already implemented on the prototype
    - As an user, I can create my own to-do list with a name
    - As an user, I can share a to-do list with its unique URL
    - As an user, I can create a task by referencing its textual objective
    - As an user, I can mark a task as read
- Technical limitations
    - There is no authentication management, nor users, nor permissions
    - The API is not versioned
    - There is no test nor codebase standards set
- Positive aspects
    - My code source allows for a simplified scaling framework
    - My code structure allows to make important structural changes (for example, a database change)
- This product doesn't have a name, nor a logo, but the emojis is a defined apsect of the design.

Answering these questions will allow you to establish precise specifications of your final product and will thus help you to position yourself on a potential market.

## Step 2 : Define specifications

Specifications of your product reflects your own vision of it. It must indicate every little detail that define your product, to whom it is targeted, who will use it, etc.

Specifications must be the result of a challenged thinking about every aspect of your final product. What is the use of this specific feature, what is the targeted audience for the application ?

Don't hesitate to think about every question you have in mind in order to define your specifications. The exercise is even more legit when it is done with other people, you will avoid confirmation bias.

Here is how I redacted my specifications :

- Product's name
- Quick description
- Targeted audience (social category, for example, students, a video games forum, or even certain types of professionals, for examples, coffee shops, bars/pubs and restaurants).
- Users types
    - Your team (who will manage the product - the back-office)
    - The customer (the one who pays for it)
    - The end user (could be your customer - they use the application)
- Define every feature as User Stories (put yourself in the shoes of a specific user)
    - As an end user, I want to add a Spotify song to the distributed waiting queue
    - As a customer, I want to be able to see my subscription status, the billing and to manage my offer
    - As a team member, I want to see the turnover of the product in a given time period

{{< badge >}}
Example
{{< /badge >}}
The specifications for the example project is given here : [Todoing.pdf](/posts/industralization/perimeter/Todoing.pdf)

There are no limits to true specifications, everything you want your product to be should be in those. They are the written copy of your final product, it will guide your teams through its conception and is what you will be selling to your customers.

It is even recommanded to include your graphic brand in your specifications, but your market study is the step that will really help you build your brand. However, it is always helpful to consider a potential brand name and to buy the domain name accordingly fast.

## Step 3 : Conduct a market study

Now that the perimeter of your final product is defined, you can begin the most determining step of your project. Without it, it would be like sailing a ship without compass, in troubled waters in which you can easily get lost.

It is important that your specifications are defined and precise enough to conduct a market study, otherwise your conclusion might be based over incomplete deductions. With non-complete specifications, you can easily confuse your product with another. For example, by defining very brief specifications of Twitter, that I simply describe with "Post content on the Internet", we can easily deduce the same specifications of Facebook or any other social media. 

Internet is already really vast, and your product probably already exists in another form if you specify brief specifications. Everything resides in nuances, that will allow you to distanciate yourself from your potential competitors.

Don't engage with projects that are too specific though, you must be able to evaluate the number of people that you will target by previously defining your target audience. If it is too broad, implementations of your product probably already exist, if it is too precise, you won't be able to make a consistent change on the market.

To conduct a good market study, don't hesitate to browse public databases for brands and patents (like Google Patents). Make Google searches of products that are close to yours, by searching with keywords and concepts that you can affiliate your product with.

If you don't want to spend much time yourself on such a thing, you can always seek for external help as many companies will conduct it for you. 

If you do conduct it by yourself, here are the points that I think are important to make a good market study :
- Analyse the actors of your potential market, write profiles about them, about the products they sell and the audiences they target
- Compare their products to yours, clearly establish the nuances by writing them
- Compare the target audiences and which type of population they don't target, so that you can distanciate yourself from them on specific markets

If, in every aspect, your product is similar to an existing one from a competitor, and that you do not have prior revolutionary technique to sell at a lower price (we will come back to this later), it might not be advised to launch your own product if you don't provide with further features or imlementations.

If you do find a legit nuance that can become a marketing argument, then your product may be legit ! You can continue the deployment process without making important changes to your specifications.

{{< badge >}}
Example
{{< /badge >}}
For the to-do lists, the competition is tough ! There are many implementations of them that cover the same specifications as we do.

- The actors : [Todoist](https://todoist.com), [Trello](https://trello.com), [Microsoft To-Do](https://todo.microsoft.com), [Google Tasks](https://en.wikipedia.org/wiki/Google_Tasks), and many others...
- Comparisons with our product
    - Todoist : users can create diverse lists, collaborate. It has more features than what our specifications cover. It is available as a web browser extension, on mobile devices, as a website... (also has a professional version)
    - Trello : allows people to organize ideas as many lists on a board, allows them to attribute tasks to other collaborators, allows them to set a deadline and to put them in categories, and much more..., for example its vast plugins marketplace
    - Microsoft To Do : allows the same things as Trello and Todoist (has a business version)
    - Google Tasks : Google implementation of to-do lists, having more or less the same features as its competitors, allows users to create tasks from the Google Workspace chat

Every solution has a business alternative, however, these products offer so much features that they can not be considered "minimalist". This is the selling point we will stay focus on for the following articles.

{{< alert >}}
In all seriousness, it would be quite a bad idea to still decide to launch a to-do lists product after analyzing its market... pretty much every implementation of them have been done
{{< /alert >}}

Once you have determined (or not) your position in a precise market, of which you know the actors and the stakes bound to each one of them, you can developed a true brand : a name, logo, visual identity - colors, photos... - catch phrases, etc...

The order to first define specifications then conduct the market study seemed logical to me. However, it can be interchanged if you are not sure about the pertinence of your own implementation. Both must use one another to be useful for your decision making.

## Conclusion

We have now completed some steps that will define a perimeter for our project before launching it. Even if they can be boring, I tried to make you understand the stake of doing them so that you can elaborate a true profile of your product.

At the end of this article, we have :
1. An elementary prototype (that we had at the beginning of the article...)
2. A State of the Art indicating elements already in our possession
3. Specifications that define what we want our product to be, it will influence - and be influenced by - our market study
4. A list of actors, of their products, of their relations inside a specific market, which will define the marketing strategy for our product

The next article will be about the application conception, of a technical and architectural point of view. We will be speaking about critical aspects such as the distributed architectures, scaling, user management and security throughout an elaborate permissions system.