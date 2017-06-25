---
layout: post
title:  "Camel subsystém ve Wildfly"
date:   2017-06-25
category: architecture
tags: [wildfly,camel, spring]
---

Camel subsystém ve Wildfly
====

Dnes napíšu o zajímavé možnosti spojení frameworku Camel s aplikačním serverem Wildfly.

Důvody využití
----

Máte aplikaci, které vyžaduje některé z těchto vlastností:

* Komunikace se systémy, možnost efektivně reagovat na výpadky
* Monitorování komunikace
* Modulární koncepce aplikace, možnost měnit tj. měnit nebo upravit část aplikace bez výpadku ostatních částí
* Transformace, validace dat, throttling, load balancing v rámci komunikace
* Využívaní integračních vzorů

Pak Camel je integrační a mediační framework, který vám je schopen tyto vlastnosti splnit. 

Camel lze použít i tak, že Camel knihovny přímo přidáte do aplikace (do war, ear apod), případně si vytvoříte vlastní modul do aplikačního serveru. Výhoda využití Camel subsystému je možnost použití již připravené konzole Hawtio, která je integrována v subsystému. Dále subsystém zjednodušuje nasazení jednotlivých konfigurací a inicializaci Camel contextu. Konfiguraci samotných toků si ukážeme někdy v dalším článku.

Camel
----

V jednom z předchozích článku jsem psal o integraci [Camel frameworku s Spring Boot](https://vladimirmezera.github.io/architecture/2017/06/04/SpringBoot/), které je ideální například na vytváření mikroslužeb. Camel je integrační framework, který velmi zjednodušuje integraci mezi aplikacemi. Obsahuje spoustu podpůrných komponent, kterými se lze napojit na různé systémy (messaging, SOAP/REST služby, databáze apod.). Tento framework lze efektivně napojit například do aplikačních serverů, ale lze jednoduše integrovat do jakékoliv jiné aplikace (malý počet závislostí, modulární architektura).  Výhoda tohoto projektu i velká členská základna, velký počet komponent, případně možnost rozšíření o vlastní komponentu. 

Wildfly
----
 
Wildfly je opensource aplikační server, který podporuje zejména standardy JEE 7 (RedHat má komerční variantu nazvanou JBoss EAP 7).  Wildfly je využíván pro běh a monitorování aplikací (war, ear, jar). Architektura Widfly aplikačního serveru je postavena tak, že pomocí rozšíření (subsystémy) lze rozšiřovat jeho funkcionalitu. Widlfly má dnes velmi dobře zpracované služby pro podporu běhu aplikací jako clustering, zabezpečení aplikací, monitorování a konfigurace bez výpadku. Mezi jeho další výhody pak patří možnost instalovat do Cloud prostředí (OpenShift, Azure, AWS), podpora vlastního load balanceru, efektivní konfigurace v cluster zapojení, instalace pomocí Docker. O Wildfly lze napsat samostatný článek, proto se mu někdy později budu věnovat.  Aktuální verze je nyní 10.1.0.Final.

Instalace Camel subsystému do Wildfly
----

Camel není standardní součástí Wildfly a tak musí být odinstalován. Instalace je jednoduchá. Je potřeba stáhnout instalační balík zde. V případě, že běžíte na starší verzi aplikačního serveru Wildfly, je potřeba zkontrolovat kompatibilitu serveru. Stáhněte si aktuální verzi [aplikačního serveru](http://wildfly.org) a [Camel subsystému](https://github.com/wildfly-extras/wildfly-camel/releases) do jednoho adresáře. 

Proveďte instalaci

```bash
[bash] unzip wildfly-dist-10.1.0.Final.zip
[bash] tar xvzf wildfly-camel-patch-4.7.0.tar.gz -C wildfly-10.1.0.Final/
```

V rámci čláku využívám proměnou WILDFLY\_HOME, která odkazuje na adresář `wildfly-10.1.0.Final`. Konfigurace je ukázána v shell terminálu, ale analogicky lze konfiguraci provést na jiných platformách.

Subsystém se automaticky nainstaluje a vytvoří konfigurační soubory a moduly (knihovny v Camel).

Instalace Camel subsystému do Wildfly
----

Camel není standardní součástí Wildfly a tak musí být odinstalován. Instalace je jednoduchá. Je potřeba stáhnout instalační balík zde. V případě, že běžíte na starší verzi aplikačního serveru Wildfly, je potřeba zkontrolovat kompatibilitu serveru. 
Subsystém se automaticky nainstaluje a vytvoří konfigurační 
Instalace automaticky neupdatuje původní konfigurační soubory (standalone.xml, domain.xml), ale instaluje nové soubory (standalone-camel.xml, domain-camel.xml).  Případně do adresáře WILDFLY\_HOME/bin je soubor fuseconfig.sh, kterým lze subsystém automaticky vytvořit i v původních konfiguračních souborech.

```bash
$WILDFLY_HOME/bin/fuseconfig.sh --configs=camel --enable
```

Spuštění aplikačního serveru můžete provést například pomocí příkazu.

```bash
$WILDFLY_HOME/bin/standalone.sh -c standalone-camel.xml
```

Tím se spustí aplikační server ve standalone módu. V konzoli po spuštění aplikačního serveru, případně logu (server.log v adresáří WILDFLY_HOME/standalone/log) by se měla objevit následující informace.

```log
[org.wildfly.extension.gravia] (MSC service thread 1-8) Activating Gravia Subsystem
[org.wildfly.extension.camel] (MSC service thread 1-7) Activating Camel Subsystem

```

A také naběhnutí monitorovací konzole hawtio.

```log
 [org.wildfly.extension.camel] (ServerService Thread Pool -- 75) Add Camel endpoint: http://127.0.0.1:8080/hawtio
```
Nyní můžete přes příkaz CRTL+C aplikační server vypnout.

Konfigurace subsystému Camel
----

Aby bylo možné přistoupit do monitorovací konzole hawtio, která je součástí CAMEL subsystému, je potřeba přidat uživatele. K tomu lze využít skript add-user.sh, který je součástí aplikačního serveru v adresáří `bin`.  

```bash
sh WILDFLY_HOME/bin/add-user.sh -m -u test -p test1234# -s
```
Tím se vytvoří `management` uživatel, kterým se lze přihlásit do hawtio konzole.

Spuťte znovu aplikační server 

```bash
$WILDFLY_HOME/bin/standalone.sh -c standalone-camel.xml
```

A v prohlížeči zadejte url adresu `http://127.0.0.1:8080/hawtio/`. Měla by se objevit login stránka.

![Hawtio login form](/public/camel/hawtio-login.png "Hawtio login form").

Přihlašte se pomocí uživatele `test` a hesla `test1234#`. Login by měl úspěšně proběhnout a měla by se objevit základní monitorovací stránka.

![Hawtio login sucess](/public/camel/hawtio-login-sucess.png "Hawtio login sucess.").



Nyní máme nainstalovaný subsystém Camel. Konfiguraci a vytváření toků (route) popíší v jednom z dalších článků.

* [Camel projekt](http://camel.apache.org/)
* [Aplikační server Wildfly](http://wildfly.org)
* [Camel subsystém do Wildlfy](https://github.com/wildfly-extras/wildfly-camel/releases) 
* [EIP vzory](http://www.enterpriseintegrationpatterns.com/)
