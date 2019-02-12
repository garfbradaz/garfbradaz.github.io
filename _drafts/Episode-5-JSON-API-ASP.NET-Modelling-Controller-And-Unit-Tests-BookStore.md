---
layout: post
author: gareth
category: blog
tags: [how-to,dotnetcore,beginners,api,mongodb,aspnetcore,unittests,moq]
excerpt_separator: <!--more-->
series: ASP.NET Core Web API Episodes
comments: true
title: Episode 5 - JSON API using ASP.NET Core, Docker & MongoDB -  Modelling, Controller and Unit Tests - BookStore
---
## Previously on Dcoding

In [Episode 4]({{ site.baseurl }}{% post_url 2018-12-31-Episode-4-JSON-API-ASP.NET-Core-Docker-Compose %}) I set up our `docker-compose` files to allow us to knit together our application and the services it will be use. Today's episode is focusing on **Modelling, Controller and Unit Tests for the BookStore Object**. <!--more-->

Here is a reminder on the sample *User Stories / Epics* for phase 1.

> As a *book store*
> I can *add* our *store* to the database
> *So* we can be accessible
>
> As a *book store*
> I can *add* our *inventory* to our database
> *So* we can expose our inventory
>
> As a *book store*
> We can *update* a books stock level
> *For* an accurate stock level 
>
> As a *API consumer*
> I can look up a *stores address*
> *So* we know where to buy a *book*
>
> As a *API consumer*
> I can look up a *book*
> *So* we can get a *list* of *stores* who sell a
> *book*
>
> As *book store IT Security*
> We can add *API Keys* to the API
> *For* API Consumers to use when querying the API

## Dependency on MongoDB

Our application will ultimately be using *MongoDB* as its back end data store. We need to take a dependency on the MongoDB driver. This will allow us to communicate with and use MongoDB.

Using your shell of choice, change directory to `../src/api` directory. To recap, our directory structure is:

```
    .
    ├── src
    |   ├── api
    |       |
    |       ├── BookStoreApp.WebApi.csproj
    |       ├── Dockerfile
    ├── tests
    |   ├── integration
    |   ├── unit
    |       ├── BookStore.Tests.csproj
    ├── docker
```

**Note:** Something has changed, can you guess what? Yep that's right, I have renamed the `.csproj` to `BookStoreApp.WebApi.csproj` instead of `BookStore.WebApi.csproj`.

Now run the following command, which will add the latest Nuget package to your api project.

```bash
dotnet add package  MongoDB.Driver
```

## BookStore
### Modelling

The first of our models, will represent our **BookStore**. A simple [Plain old C# (POCO)](https://en.wikipedia.org/wiki/Plain_old_CLR_object) to represent this:

```c#
    /// <summary>
    /// BookStore POCO.
    /// </summary>
    public class BookStore
    {
        [BsonId]
        public ObjectId  Id {get; set;}
        public string Name {get;set;}
        public string AddressLine1 {get;set;}
        public string AddressLine2 {get;set;}
        public string AddressLine3 {get;set;}
        public string City {get;set;}
        public string PostCode {get;set;}
        public string TelephoneNumber {get;set;}

        /// <summary>
        /// Default constructor
        /// </summary>
        public BookStore()
        {
            this.Id = ObjectId.GenerateNewId();
        }
    }
```

I added a `Model` folder to my `../api` folder where this was added. MongoDB uses a serialization format standard called [BSON](https://docs.mongodb.com/manual/reference/glossary/#term-bson). BSON has the idea of *types* and one of those types is [`ObjectId`](https://docs.mongodb.com/manual/reference/bson-types/#objectid) which represents a unique id for the record. You decorate the `Id` property with `[BsonId]` attribute, to inform the MongoDB driver what field is your `BsonId` field.

I have also created a default constructor that sets that Id up. Now we have created the `Models` directory and structure is now the following:

```
    .
    ├── src
    |   ├── api
    |       ├── Models
    |           ├── BookStore.cs
    |       ├── BookStoreApp.WebApi.csproj
    |       ├── Dockerfile
    ├── tests
    |   ├── integration
    |   ├── unit
    |       ├── BookStore.Tests.csproj
```

### Controller

Now we need to create a **BookStore** controller. A controller is the *C* in MV**C** pattern. The controller will handle the HTTP requests that come in using *Actions*. The HTTP requests are mapped to Actions via the routing pipeline of MVC. For WebAPIs, they map to specific HTTP methods like `GET`, `POST`, `PUT` etc. 

ASP.NET expects you to follow conventions when creating your own Controllers:

- The Controller class name always ends with `Controller`.
- The Controller class should reside in a folder called `Controllers`.
- The Controller class should inherit from `Microsoft.AspNetCore.Mvc.ControllerBase` (WebAPI) / `Microsoft.AspNetCore.Mvc.Controller` (ASP.NET MVC Apps with Views).

When you do a `dotnet new webapi` the templates include a standard `ValuesController.cs` that lives in a `Controllers` folder. Create a new `BookStoreController.cs` file in there:

```
    .
    ├── src
    |   ├── api
    |       ├── Controllers
    |           ├── BookStoreController.cs
    |       ├── Models
    |           ├── BookStore.cs
    |       ├── BookStoreApp.WebApi.csproj
    |       ├── Dockerfile
    ├── tests
    |   ├── integration
    |   ├── unit
    |       ├── BookStore.Tests.csproj
```

To this file I have added the following:

```c#
    [Route("api/[controller]")]
    [ApiController]
    public class BookStoreController : ControllerBase
    {
        // GET api/values
        [HttpGet]
        public async Task<ActionResult> Get()
        {
            var bookStore = new List<BookStore>{
                new BookStore {
                    Name = "Waterstones",
                    AddressLine1 = "The Dolphin & Anchor",
                    AddressLine2 = "West Street",
                    City = "Chichester",
                    PostCode = "PO19 1QD",
                    TelephoneNumber = "01234 773030"
                }
            };
            return await Task.Run(() => new JsonResult(bookStore));
        }
    }
```