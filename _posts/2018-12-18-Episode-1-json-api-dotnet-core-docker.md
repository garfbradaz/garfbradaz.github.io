---
layout: post
author: gareth
category: blog
tags: [how-to,dotnet-core,beginners,api]
excerpt_separator: <!--more-->
series: ASP.NET Core Web API Episodes
comments: true
title: Episode 3 - JSON API using ASP.NET Core, Docker &  MongoDB
---
## Inspiration

There is a series on [dev.to](https://dev.o) that I have followed by [Nathan Bland](https://dev.to/nathanabland) who has recorded as series of videos around creating a JSON API, using *Node.js*, *docker* and *Express*. <!--more-->

I liked the idea around this, so I'm going to do the same, but in a blog style, but replacing *Node.js* and *Express* with *ASP.NET Core WebAPI*. I also want to use a MongoDB as a the backend.

## Set the Scene

The API itself will be for a *Book Shop Chain* who want to expose their stores and titles via a public RESTful service for other companies to use. This can be used for stock lookups and to see which stores have stock available to sell.

Here are some example Epics to show the shape of the API needed:

> As a *book store*
> I can *add* our *store* to the database
> *So* we can be accessible

> As a *book store*
> I can *add* our *inventory* to our database
> *So* we can expose our inventory

> As a *book store*
> We can *update* a books stock level
> *For* an accurate stock level 

> As a *API consumer*
> I can look up a *stores address*
> *So* we know where to buy a *book*

> As a *API consumer*
> I can look up a *book*
> *So* we can get a *list* of *stores* who sell a
> *book*

> As *book store IT Security*
> We can add *API Keys* to the API
> *For* API Consumers to use when querying the API

## What to expect

My plan is to release weekly blog posts covering in a similar fashion like Nathan's:

- Setting up your .NET Core Project Structure.
- Setting up docker environment using docker-compose.
- Modelling API JSON back end using Mongo.
- Modelling API using Swagger.
- Unit Tests with Xunit.
- Integration Tests with Postman.
- Skeleton Book Store API (Routes etc).
- Skeleton Inventory API (Routes etc).
- Securing your API.
- Data validation.
- Pagination for large data queries.
- Filter and Search.
- Hooking up to Azure Dev Ops for Build and Release Pipeline.
- Publisher Swagger Documentation on Github.

I will post any code on Github as well.

## Start at Zero

So before starting cracking on with this, make sure your environment is set up. Because this is *.NET Core*, this is cross platform, so if you own a Mac or Linux box, feel free to join in. I'm going to be building this on a *Surface Book*, but I will try to be a good Xpat-Citizen and test across environments.

Please make sure the following is installed:

- [Dotnet Core ](https://dotnet.microsoft.com/download) - I'm currently using 2.2.
- [Docker](https://www.docker.com/get-started) - You will need to also create a login for [Docker Hub](https://hub.docker.com/signup) if you do not have one.
- [Powershell Core](https://docs.microsoft.com/en-gb/powershell/scripting/overview?view=powershell-6) - PowerShell Core is cross-platform and runs on Windows, Linux, and MacOS while the old one runs on Windows only(CLI). There are options to install on the OS of choice.

We wont need to install Mongo as we will be running it using *Docker*.

## IDE

I will be using *Visual Studio Code* for cross platform .NET Core support. I use a bunch of extension which I have put here:

[https://gist.github.com/garfbradaz/be010080df076d19892bf4ec71127b53](https://gist.github.com/garfbradaz/be010080df076d19892bf4ec71127b53)

You can import these using the following *Settings Sync* extension on the marketplace:

[https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)

If you already use *VSCode*, you may just want to find the *C#*, *Docker* *GitLens* and *.NET Core Test Explorer* [extensions](https://code.visualstudio.com/docs/editor/extension-gallery).

**Note:**My list contains extensions like *Jekyll* support which you wont need for this.

## Next time

We will create the *ASP.NET Core WebAPI* template, set up the project structure. Join me next time!.