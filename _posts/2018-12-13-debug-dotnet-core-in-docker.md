---
layout: post
author: gareth
category: blog
tags: [jekyll,release-notes,how-to]
excerpt_separator: <!--more-->
---
## Debugging ASP.NET Core in a Linux Docker with Visual Studio Code

So, you have started using Docker for your .NET Core applications - awesome news. You are using Linux Containers, and Visual Studio Code, and you then go to Debug your container.....oh. <!--more--> (I'm using Windows).

Its not the most straight forward process I have found, if you are looking at getting this up and running. Firstly I didn't figure this out on my own, the reason I'm blogging about it is because I meshed together the knowledge I had, plus these very informative posts:

- [Jonathan Channon](http://blog.jonathanchannon.com/2017/06/07/debugging-netcore-docker/)
- [Richard Banks](https://www.richard-banks.org/2018/07/debugging-core-in-docker.html)

It was the above two posts that glued it together for me.  Have a good read of those as well. Before we go any further, just to confirm these instructions are for *Visual Studio Code* and *Linux Containers* using *Docker Compose*, I'm not sure how this is set-up on Visual Studio. I'm presuming you have *VSCode*, *Docker* and *.NET Core SDK* installed. And you are using Linux Containers.

## DockerFile

Firstly lets look at my multi-staged (Multiple `FROM`) Dockerfile for building my project.

### 1st Stage Build

The first part of the Dockerfile `build-env` stage, takes an `ARG` of `buildconfig`, which controls how the `dotnet publish` is configured. And yes I know, I have a todo item to refactor the if/else to just pass the `buildconfig` as a `-c` value.

### 2nd Stage Run

So the 1st stage handled the build, the 2nd handles the running of the Web API project. As you can see, there is a `Debug` switch again, to install some dependencies when running in `Debug`. Ignore the **mongo** installs, it is the *vsdbg* installation We are interested in.

`curl -sSL https://aka.ms/getvsdbgsh | bash /dev/stdin -v latest -l`

>
> The debugger platform has been expanded between Visual Studio 2017 15.3 and
> previous versions of Visual Studio. Visual Studio can now communicate using
> the debug adapter protocol which is used by Visual Studio, Visual Studio Code
> and Visual Studio for Mac. This protocol is now used for debugging
> cross-platform .NET Core applications.
>
> [Source](https://github.com/Microsoft/MIEngine/wiki/Offroad-Debugging-of-.NET-Core-on-Linux---OSX-from-Visual-Studio)

So vsdbg is installed **into** the container to allow remote debugging from your host machine using Visual Studio Code.

```yaml
FROM microsoft/dotnet:2.2-sdk AS build-env
ARG buildconfig
WORKDIR /app
COPY ServiceApp.WebApi.csproj .
RUN dotnet restore
COPY . .
RUN if [ "${buildconfig}" = "Debug" ]; then \
        dotnet publish -o /publish -c Debug; \
    else \
        dotnet publish -o /publish -c Release; \
    fi

FROM microsoft/dotnet:2.2-aspnetcore-runtime
ARG buildconfig
ENV DEBIAN_FRONTEND noninteractive
WORKDIR /publish
COPY --from=build-env /publish .
RUN if [ "${buildconfig}" = "Debug" ]; then \
        apt-get update && \
        apt-get install -y --no-install-recommends apt-utils && \
        apt-get install curl unzip procps mongodb -y && \
        curl -sSL https://aka.ms/getvsdbgsh | bash /dev/stdin -v latest -l /publish/vsdbg; \
     else \
        echo "*Whistling*"; \
    fi
ENV DEBIAN_FRONTEND teletype
ENTRYPOINT [ "dotnet","ServiceApp.WebApi.dll" ]
```

## Launch Task Configuration for Visual Studio Code

So this was the bit I struggled with, to the point I [logged a Github Issue](https://github.com/OmniSharp/omnisharp-vscode/issues/2724) and subsequently had a light bulb moment afterwards!

Both Jonathan and Richards work and investigations for their posts, again helped tenfold.

Important bits are the `sourceFileMap` which map your projects code to the binaries in the Container. The */app* in reference in mine, relates to the */app* directory declared in the Dockerfile.

The other important snippet is the `debuggerPath` option. This is the directory where vsdbg has been installed within your Container. You can set different directories within your `curl` command in the Dockerfile if you so wish.

Lastly, note the Container name of `webapi_debug`, I set this up in the relevant compose file.

The below will live in a `launch.json` in your `.vscode` directory. If configured correctly you should get a nice launch icon:

![icon](/assets/img/posts/debug-vscode-docker.png)

```json
{
    "name": "Attach to webapi (Docker)",
    "type": "coreclr",
    "request": "attach",
    "sourceFileMap": {
        "/app": "${workspaceFolder}/src/api"
    },
    "processId" : "${command:pickRemoteProcess}",
    "pipeTransport": {
        "debuggerPath": "/publish/vsdbg/vsdbg",
        "pipeProgram": "docker",
        "pipeCwd": "${workspaceFolder}/src/api",
        "quoteArgs": false,
        "pipeArgs": [
            "exec","-i", "webapi_debug"
        ]
    }
}
```

## Composing

Because I'm using separate Docker containers for *MongoDB* and *Identity Server* I use `docker-compose` to knit together my containers.

**Note:** I'm planning on doing a Identity Server post next on my experiences in getting that set-up.

The below is from my `docker-compose.dev.yml` file.

So the container with the interesting configurations is `webapi` which is my ASP.NET Core Web API project.  As you can see I set the container name to `webapi_debug` is set. As I mentioned before, the launch task needs that to remote into that particular container.

You can also see I mount my Source Code directory from my **host** Windows machine using `volumes` using a relative path to my source code directory (*../api*) to *:/app*, allowing us, when a breakpoint is hit, for the code to be shown. If you configure this bit wrong, you will get the following error when the breakpoint is thrown:

![file-not-found](/assets/img/posts/not-found-debug.png)

The other interesting config points, are the **ASPNETCORE_*** environment variables for `Development` and the `buildconfig` argument of `Debug`, which as we learnt previously, is passed into the `Dockerfile` and used.

```yaml
version: "3"
services:
  identity:
    image: garfbradaz/serviceident:develop
    container_name: identity
    build:
      args:
        buildconfig: Debug
      context: ../quickidentity
      dockerfile: Dockerfile
    volumes:
      - ../quickidentity:/app2
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=http://identity:5002  
      - AuthorityUrl=http://identity:5002
    ports:
      - "5002:5002"
  webapi:
    image: garfbradaz/serviceapp:develop
    container_name: webapi_debug
    build:
      args:
        buildconfig: Debug
      context: ../api
      dockerfile: Dockerfile
    volumes:
      - ../api:/app
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=http://+:5001
      - AuthorityUrl=http://identity:5002
    ports:
      - "5001:5001"
    depends_on:
      - mongodb
      - identity
  mongodb:
    image: mongo:latest
    container_name: mongodb
    ports:
      - "27017:27017"
```

## Running

So put this all together you can run your containers using the Compose:

`docker-compose -f docker-compose.dev.yml up -d --build`

Set a breakpoint in your code (Example I set in my Controller), and run your debug task in *Visual Studio Code*. This will ask you to pick your process for your `dotnet` code. Then when the breakpoint is hit,  you can inspect it as normal.

I find that it can hang and take half a minute sometimes for the debug session to initiate, so bear with it.

Anyway I hope this helps someone, and a massive thank you to [Jonathan](https://twitter.com/jchannon) and [Richard](https://twitter.com/rbanks54).