---
layout: post
author: garfbradaz
category: blog
tags: [raspberrypi,kubernetes,openfaas]
excerpt_separator: <!--more-->
comments: true
title: Creating Raspberry Pi Cluster to Learn Kubernetes
---
## Creating Raspberry Pi Cluster to Learn Kubernetes

In August 2018 I bought some Pi's tp create a cluster, mainly after reading and watching [Alex Ellis](https://blog.alexellis.io/build-your-own-bare-metal-arm-cluster/) and [Scott Hansleman's](https://www.hanselman.com/blog/HowToBuildAKubernetesClusterWithARMRaspberryPiThenRunNETCoreOnOpenFaas.aspx) blogs and videos on the subject, which inspired me to make the purchase. I was running Code Clubs at the time, so I thought this would make a good show and tell as well.<!--more-->

Both Alex and Scott posts were both inspiring and I really wanted to give it ago. Anyway I got the equipment, but work and real-life got in the way and progress was slow. Then in December 2018 I landed a new job, starting in April this year, and they use Kubernetes extensively so I also had another good reason to get it up and running - to learn Kubernetes!

So this month I finally got it working, including a proper master and node setup:

![](/assets/img/posts/Pi-3.jpg)

There were some pain points, like make sure you set up your static IP's for your Pi's (and set them up correctly on your router), and more importantly, set up a static IP *for* the router itself. The yellow box is the picture is the [rGL-MT300N-V2 router I went for](https://www.amazon.co.uk/gp/product/B073TSK26W/ref=oh_aui_search_asin_title?ie=UTF8&psc=1)  in the end, and the only bit of Hanselmans shopping list from his post, I diverted from.

## My Sons love it

My two sons love it, as we are building Raspberry Pi robots as well in parallel to this. They are interested in what this is doing and *why*. Sparking curiosity in little minds is important, and me just building websites doesn't get their juices flowing!

## Should I do this?
Yes. :)

## Whats next?

Well, learning Kubernetes itself and running some .NET Core container sites. I would like to see how quick the .NET SDK is for building on the Pi's now, as I would like to experiment with an local build server.

Also, Alex is the creator of [*OpenFaaS*](https://www.openfaas.com/), a cloud agnostic Serverless solution. Having worked with Azure Functions recently at my current job, I can definitely see why this is a good idea. A Serverless infrastructure *without* being tied down to a specific cloud. I want to explore converting a small Web API app to OpenFaaS and experimenting there (I'm actually super excited).

Lastly, when I restart my Code Clubs up, I would like to do a show and tell with something interesting that includes the cluster. I was thinking about running some OpenFaaS functions that "talk" to my Pi robot I'm building and possibly allow you to control it for example.

Lots to do before April arrives!


