---
layout: post
title: Docker Deep Dive by Nigel Poulton
---

After completing [*DevOps with Docker*](https://rualthan.github.io/devops_with_docker/) from the University of Helsinki MOOC Centre, I thought *Docker Deep Dive* (DDD) by Nigel Poulton would be a repeat. But it turns out it isn't. They complement each other. While the Helsinki course is heavily oriented towards labs and getting you containerizing apps, DDD offers digestible theory with the right amount of follow-along examples.

I am not new to Nigel. I had taken his Docker course a long time ago in 2016 on Pluralsight. It was a good course, but Docker was still new and I didn't have much idea how it could be used in the real world. I clearly remember networking and storage being gray areas. I recall telling people: the hello world version is easy, but it gets fuzzy when you try to do real things.

I like DDD's coverage of Docker's history and development, and the various standards bodies that oversaw it — such as the OCI (Open Container Initiative).

I like his style. He uses simple language and throws in witty comments here and there, which makes it a fun read. At a little over 200 pages, it's a light and easy read. I recommend it. If I were to do it again, I would read DDD first and then take the Helsinki course.

Some things that were new to me, which I learned from DDD:
* `docker init` to scaffold a Dockerfile (and related files) for a project
* Multipass
* Wasm (WebAssembly)
* Docker Overlay networks

I had been using Vagrant and VirtualBox for local VMs. Multipass is a lightweight alternative for spinning up Ubuntu VMs quickly. As for WebAssembly — write code in a supported language of your choice, compile it to a `.wasm` binary, and run it anywhere that has a Wasm runtime, inside or outside the browser.

One thing I noticed was missing: bind mounts. Given that volumes and bind mounts are the two primary ways to persist data in Docker, it's a bit surprising that bind mounts didn't get a mention.
