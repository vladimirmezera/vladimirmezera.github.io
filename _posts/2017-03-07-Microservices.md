---
layout: post
title:  "Microservice Spring Boot vs. Wildfly Swarm"
date:   2017-03-07
category: architecture
tags: [architecture, microservices, spring, wildfly]
---

Spring Boot vs. Wildfly Swarm
=====

Dnes bude post trošku krátký, ale přesto si myslím, že bude zajímavý. Na jednom známém blogu jsem objevil srovnání dvou projektů, které lze využít na mikroslužby v Java světě. Jedná se o [Wildfly Swarm](http://wildfly-swarm.io/) a [Spring Boot](https://projects.spring.io/spring-boot/). Aktuálně se rozhoduji, který z těchto projektů využít. 

Oba projekty přistupují k možnosti vytváření mikroslužeb obdobně (generátor pom.xm). Na internetu lze najít spoustu návodů jak vytvářet služby v obou projektech. 

V mém projektu bude mikroslužba přistupovat k více službám různými protokoly (poslání souboru, WS, smtp). Takže v dalším článku možná porovnám oba projekty na možnosti integrace z okolnímy systémy a službami.  

Článek je k dispozici [zde](https://blog.akquinet.de/2017/01/12/the-lightweightness-of-microservices-comparing-spring-boot-wildly-swarm-and-haskell-snap/).

* [Wildfly Swarm generátor](http://wildfly-swarm.io/generator/)
* [Spring Boot generáror](http://start.spring.io/)
* [Spring Boot](https://projects.spring.io/spring-boot/)
* [Wildfly Swarm](http://wildfly-swarm.io/)
