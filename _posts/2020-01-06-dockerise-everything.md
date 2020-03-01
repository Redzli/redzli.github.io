---
layout: post
title: I start to dockerise everything
date: 2020-01-15
summary: So should you?
categories: docker
---

## Diving into a containerised world
Just too often we ran into a scenario where we happily cloned someone's repo/copied someone's code, but ended up finding them not compiling on our machine. Then we start to install environment variables, set up path etc. Too much fuss.

Using containerised application can solve the above problem gracefully. Let's take `Docker` as my example. A simple command `docker-compose up --build` (if using `docker-compose`) or `docker run <name>` (if only `Dockerfile`) can spin up the application with no extra trouble.

Also, I find docker image help install backend tools such as spinning up a `mongo` and `postgre` server. You simply find and pull a docker image from `dockerhub` and then you are good to go.

