# Integračná príručka - Národní katalog otevřených dat

## 1. ROZSAH PLATNOSTI A ÚČEL

Tato integrační příručka slouží k popisu Národního katalogu otevřených dat (NKOD), části projektu OD2.0.

## 2. DEFINÍCIA POJMOV A SKRATIEK

<dl>
  <dt>NKOD</dt>
  <dd>Národní katalog otevřených dat</dd>
  <dt>LKOD</dt>
  <dd>Lokální katalog otevřených dat</dd>
  <dt>RDF</dt>
  <dd>Resource Description Framework</dd>
</dl>

## 3. REFERENCIE

Čtenář této příručky by měl být obeznámen s:
* [Kubernetes](https://kubernetes.io/docs/home/)
* [Oracle Cloud Infrastructure (OCI)](https://docs.oracle.com/en-us/iaas/Content/home.htm)
* <a href="inštalačná príručka a pokyny na inštaláciu.md"> Inštalačná príručka a pokyny na inštaláciu (úvodnú/opakovanú)</a>
* <a href="aplikačná príručka.md">Aplikační příručka</a>
* <a href="používateľská príručka.md">Uživatelská příručka</a>

## 3. ARCHITEKTÚRA

![Aplikační architektura NKOD](obrázky/aplikačná%20príručka/aplika%C4%8Dn%C3%AD%20architektura.svg)

Aplikační architektura řešení je přehledově popsána v <a href="aplikačná príručka.md">Aplikační příručce</a> sekce 3.2 ZOZNAM A ZÁKLADNÝ POPIS SUBSYSTÉMOV A FUNKCIÍ.

Tento popis architektury také ukazuje komunikačními rozhraní, které je většinou také integračními body.
Ve zbytku této kapitoly nejprve zevrubně popíšeme jednotlivá komunikačními rozhraní a následně integrační body.

### 3.1. Komunikačné rozhrania

Komunikační rozhraní jsou součástí rozhraní popsaných v <a href="aplikačná príručka.md">Aplikační příručce</a> sekce 3.5 POPIS EXISTUJÚCICH ROZHRANÍ V IS.
Pro plné porozumění rozhraním je doporučeno přečíst jak tuto sekci tak sekci odkázanou.

V této sekci jsou popsána komunikační rozhraní směrem k:
* Poskytovali dat
* Externímu IS
* Návštěvník

#### 3.1.1 Webová stránka

Webová stránka umožňuje vybrat před připravený [SPARQL] dotaz nebo napsat vlastní, a nechat ho vykonat na rozhraní SPARQL endpoint nad RDF úložištěm NKOD a zobrazit výsledky.
Přes komponentu webové stránky je zpřístupněn i obsah NKOD v podobě souborů ke stažení.
Adresy souborů ke stažení jsou popsané v <a href="používateľská príručka.md">Uživatelské příručce</a>.

Webová stránka je k dispozici vícejazyčně, nyní v 5 jazycích dostupných na relativní URL:
* ```/``` - Slovenština
* ```/de/``` - Němčina
* ```/en/``` - Angličtina
* ```/hu/``` - Maďarština
* ```/uk/``` - Ukrajinština

Pro samotnou práci se SPARQL dotazem využívá knihovnu [Yasgui].
Tato knihovna je součástí repositáře [NKOD-SW] v cestě ```components/website/www/libs```.

#### 3.1.2 SPARQL endpoint

Vyhodnocuje dotazy v jazyce [SPARQL] nad RDF úložištěm NKOD.
Toto rozhraní je také využíváno formulářem pro SPARQL dotaz na Webové stránce.

Endpoint je ve výchozím nastavení dostupný na URL ```/api/sparql``` a využívá [CORS].
Toto nastavení je možné změnit v ```configuration/nginx.conf```.

#### 3.1.3 LKODy

Vstupním rozhraním pro Metadatový procesor - LinkedPipes ETL jsou registrované instance LKODů.
Jejich obsah musí odpovídat specifikaci [DCAT-AP-SK 2.0]. 

V oblasti vstupní komunikace dochází k získávání dat potřebných pro vytvoření dat NKODu.
Hlavním datovým vstupem jsou lokální katalogy LKOD, ze kterých jsou data harvestována dle specifikace DCAP-AP-SK 2.0.

<!-- ## 5. SYSTÉMOVÉ POŽIADAVKY -->

## 4. INTEGRAČNÉ API

Za integrační API lze považovat i komunikační rozhraní popsaná v sekci 3.1. Komunikačné rozhrania.
Tato komunikační rozhraní slouží zejména pro integraci z venku systému, tedy třetích stran. 
Rozhraní popsaná v této sekci slouží pro integraci uvnitř NKOD.

### 4.1 Registrační záznamy

Struktura synchronizačních záznamů je popsána v <a href="používateľská príručka.md">Uživatelské příručce</a>, sekce 3.2 Registrační záznamy.
Ve výchozím nastavení jsou registrační záznamy načítány z GitHub repositáře [NKOD-REG].
Z tohoto umístění jsou před každým spuštěním harvestování synchronizovány do ```nodc-registration-pvc``` definovaném v ```./k8s/linkedpipes/registration-pvc.yaml```.
Synchronizaci zajišťuje skript z komponenty Manažer.
Konkrétně se jedná a skript v repositáři [NKOD-SW] v souboru ```./components/manager/entrypoint.sh```.

Ve sdíleném úložišti musí být souboru uložené v adresáři ```repository```.
Důvodem je nemožnost vytvoření git repository přímo v rootu PVC, neb ten obsahuje skrytý soubor pro potřeby OCI.

URL repositáře s registračními záznamy je nastavené v konfiguračním souboru ```./configuration/nodc-configuration.properties```.

Toto je integrační bod využitelný pro budoucí Portál otevřených dat, kde se registrační záznamy budou spravovat a ukládat jiným způsobem, který nahradí stávající synchronizaci z GitHub repozitáře.

### 4.2 LinkedPipes ETL pipeliny

Metadatový procesor - LinkedPipes ETL provádí harvestování na základě definovaných pipeline.
Před každým spuštěním se provede stažení  pipeline a template z [NKOD-PPL].
Stažené pipeliny a templaty jsou následně zkopírovány do úložiště LinkedPipes ETL a importovány.

URL repositáře je nastavené v konfiguračním souboru ```./configuration/nodc-configuration.properties```.

[LinkedPipes ETL]: https://etl.linkedpipes.com "LinkedPipes ETL"
[SPARQL]: https://www.w3.org/TR/sparql11-query/ "SPARQL"
[Resource Description Framework (RDF)]: https://www.w3.org/TR/rdf11-concepts/ "RDF"
[DCAT-AP-SK 2.0]: https://datova-kancelaria.github.io/dcat-ap-sk-2.0/ "DCAT-AP-SK 2.0"
[NKOD-SW]: https://github.com/datova-kancelaria/nkod-software "NKOD Software"
[Yasgui]: https://yasgui.triply.cc/ "Yasgui"
[CORS]: https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS "Cross-Origin Resource Sharing"
[NKOD-REG]: https://github.com/datova-kancelaria/nkod-registrace "NKOD Registrace"
[NKOD-PPL]: https://github.com/datova-kancelaria/nkod-registrace
