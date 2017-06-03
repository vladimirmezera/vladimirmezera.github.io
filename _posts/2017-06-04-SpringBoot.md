---
layout: post
title:  "Spring Boot integrace s projektem Camel"
date:   2017-06-04
category: architecture
tags: [architecture, microservices, spring]
---


Několik příspěvků zpětně jsem řešil jakou technologii zvolit (Wildfly Swarm vs Spring Boot). Nakonec zvítězila technologie Spring Boot a to zejména z těchto důvodů:

* Větší komunita uživatelů než u Wilfly Swarm
* Větší množství příkladů a testů
* Lepší a jednoduší integrace s Camel frameworkem

U projektů, které vyžadují komunikaci přes různé protokoly a v rámci logiky využívají integrační toky (přenos a transformace dat mezi různými body) využívam většinou framework Camel. 

## Camel
Camel je open source integrační framework, který podporuje EIP (Enterprise Integration Patterns). Tento framework umožnuje efektivně a rychle integraci nad různými datovými zdroji, umožnuje provádět transformace mezi daty a případně řešit error handling a další věci spojené s přenosem a transformacemi dat.  Výhody a konfigurace Camel je na samostatný článek (rád v budoucnu o něm napíši). 

Camel je součástí dnes i open source integračních řešení jako Apache Service Mix, případně SwitchYard, ale například i komerčních (JBoss Fuse od Red Hat). Camel je sada knihoven, takže lze snadno integrovat i do vlastních aplikací. Camel není jediný integrační framework. Spring má vlastní projekt Spring Integration.  Camel jsem zvolil hlavně z těchto důvodů:
* Velká komunita uživatelů Camel 
* Dlouhodobě udržovaný projekt 
* Modulárnost celého řešení
* Rozšiřitelnost o vlastní komponenty
* Testovací komponenty, které umožnují dělat i složitější testy (Mockování služeb)
* Možnost zápisu integračních toků pomoci Spring DSL (XML) a Java DSL
* Předchozí zkušenosti s tímto frameworkem

## Integrace se Spring Boot
Integrace se Spring Boot je jednoduchá. Stačí využít Spring Boot generátor, případně podle různých návodů nakonfigurovat například `pom.xml` používáte-li maven na sestavení.

![Spring Boot generátor](/public/springboot/init.png "Spring Boot generátor")


V mé ukázce jsem nejdříve nakonfigurovat dependencyManagement, kde jsem nastavil dvě BOM závislosti. Jedna je pro Spring Boot a druhá pro integraci Spring Boot s Camel. 
```xml
   <dependencyManagement>
        <dependencies>
            <!-- Spring Boot BOM -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring.boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!-- Camel BOM -->
            <dependency>
                <groupId>org.apache.camel</groupId>
                <artifactId>camel-spring-boot-dependencies</artifactId>
                <version>${camel.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

Poté již je k dispozici většina knihoven potřebná pro sestavení aplikace a případný běh nebo testy. Camel má základní služby v modulu camel-core. Další adaptéry a komponenty jsou k dispozici v dalších podpůrných modulech např. komunikace se smtp nebo imap je v modulu camel-smtp. 

```xml
	<dependency>
            <groupId>org.apache.camel</groupId>
            <artifactId>camel-mail</artifactId>
        </dependency>

        <dependency>
            <groupId>org.apache.camel</groupId>
            <artifactId>camel-servlet</artifactId>
        </dependency>

```

## Vytváření integračních toků
Vytváření integračních toků je již velice jednoduché. Konfigurací již Spring Boot aplikace při startu bude hledat Camel routy (bloky ve kterých se tyto integrační toky definují) a bude je na základě konfigurace spouštět. Aby Spring Boot aplikace veděla, kde tyto bloky hledat, stačí označit třídy (pomocí spring anotace Component) ve kterých jsou tyto routy definované. 

Ukázka konfigurace pomocí Java DSL, kde každá třída musí dědit abstraktní třídu RouteBuilder. V metodě `configure` je poté konfigurace samostatných route. Spring Boot umožnuje Camel routy také načítat pomocí Spring DSL (XML konfigurace).  

```java
@Component
public class DummyRoute extends RouteBuilder {
    @Override
    public void configure() throws Exception {

        from("direct:getInfo").process(exchange -> {
            exchange.getIn().setBody("Basic information");
        });
         

    }
}
```
Tento příklad pouze vytváření jednoduchou routu, která pouze vrací zprávu s obsahem `Basic information`. Route může být v aplikace více (preferuji rozdělení například podle komponent nebo integračních toků).

Vlastní logiku v Camel routách ukážu někdy v dalších příspěvkách.

Kompletní ukázka integrace Spring Boot s Camel je [zde](https://github.com/vladimirmezera/camel-spring-boot).  

* [Ukázka příkladu integrace Spring Boot s Camel](https://github.com/vladimirmezera/camel-spring-boot)
* [Camel projekt](http://camel.apache.org/)
* [Enterprise Integration Patterns](http://www.enterpriseintegrationpatterns.com/)
* [Spring Boot generáror](http://start.spring.io/)
* [Spring Boot](https://projects.spring.io/spring-boot/)
