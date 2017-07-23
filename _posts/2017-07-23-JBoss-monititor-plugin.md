---
layout: post
title:  "Monitorovací sonda do aplikačních serverů JBoss AS, WildFly, JBoss EAP"
date:   2017-07-23
category: architecture
tags: [architecture, monitoring, icinga]
---

# Monitorování aplikačních serverů

Nedávno jsem řešil problém jak monitorovat aplikační servery (JBoss AS, WildFly, JBoss EAP) pomocí monitorovacích nástrojů z tzv. „Nagios rodiny“ (Nagios, Icinga2, Centreon). Aplikační servery sice podporují jmx, ale bohužel standartní jmx\_plugin, který je součástí pluginů, které lze do monitorovacího nástroje instalovat nefunguje.  


## Návrh sondy
Rozhodl jsem se tedy vytvořit plugin, kterým lze rodina aplikačních serverů od JBoss monitorovat. Aplikační servery mohou běžet ve více režimech (standalone, managed domain), případně v režimu „Managed domain“ může v rámci domény běžet více serverů. Snažil jsem se o to, aby si mohl případný uživatel plugin sám jednoduše upravit, takže jsem zvolil pro sondu bash script. 

Tento skript využívá navíc jq příkaz pro podporu JSON formátu. Tento příkaz je potřeba většinou externě doinstalovat. Sonda využívá REST API, které mají aplikační servery JBoss k dispozici. Skript používá formát nagios pluginy, takže vrací definovaný stav (OK, ERROR, WARNING, UNKNOWN), případně performace data. 

V této chvíli vrací aktuální hodnoty threadů a JVM Heapu, které pro základní monitorování JVM stačí. Případně lze tento plugin jednoduše upravit na vytahování dalších informací z aplikačních serverů. Pomocí REST API (defaultní adresa je http://localhost:9990/management) lze snadno zjistit velmi mnoho informací o běhu, případně konfiguraci. 


## Použití 
Sonda lze volat lokálně, případně i nainstalovat přímo na server a volat například pomocí ssh nebo NRPE. 

Sonda lze volat v konfiguraci aplikačního serveru standalone:
```shell
./check_wildfly.sh -u user -p userpassword -a http://server_url:9990 
```

Případně  v konfiguraci aplikačního serveru managed domain:
```shell
./check_wildfly.sh -u user -p userpassword -a http://server_url:19990 -s server_name -c controller_name 
```
V tomto případě může aplikační server pomocí domain controlleru (adresa serveru, kam se sonda připojuje) řidit více serverů na různých host kontrolerech. V tomto případě je potřeba nakonfigurovat název monitorovaného serveru a název host kontroleru, který ho zpravuje.

## Další možná vylepšení
V rámci tohoto pluginu mě jich napadá velká část a postupně se k některým doufám dostanu:

* Kontrola hodnot Domain a Host kontroleru
* Deployment statistiky do performance dat
* Lepší kontrola a možnost nastavení thresholdů u kontroly
* Kontroly platnosti certifikátů (toto lze například http sondou)
* Monitorování počtu requestů 

## Odkazy 

* [Icinga2](https://www.icinga.com)
* [Nagios](https://www.nagios.org)
* [Odkaz na vytvořenou sondu](https://github.com/vladimirmezera/icinga2-plugins/tree/master/wildfly-plugin)
