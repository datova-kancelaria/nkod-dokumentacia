# Uživatelská příručka - Národní katalog otevřených dat

# 1.	ROZSAH PLATNOSTI A ÚČEL

Tato uživatelská příručka slouží k popisu uživatelské částí Národního katalogu otevřených dat (NKOD), části projektu OD2.0.

# 2.	DEFINÍCIA POJMOV A SKRATIEK

<dl>
    <dt>DCAT-AP-SK 2.0</dt>
    <dd>Specifikace pro metadatový záznam datasetu otevřených dat</dd>
    <dt>LKOD</dt>
    <dd>Lokální katalog otevřených dat</dd>
    <dt>NKOD</dt>
    <dd>Národní katalog otevřených dat</dd>
    <dt>POD</dt>
    <dd>Portál otevřených dat</dd>
    <dt>RDF</dt>
    <dd>Resource Description Framework - datový model využívaný NKOD</dd>
    <dt>SPARQL</dt>
    <dd>Dotazovací jazyk nad daty v RDF</dd>
</dl>

# 3.	POPIS IS

NKOD slouží jako backend budoucího Portálu otevřených dat (POD). Uživatelské prostředí NKOD je tedy nyní minimalistické a tvoří ho jediná webová stránka.

![Webová stránka NKOD](obrázky/pou%C5%BE%C3%ADvate%C4%BEsk%C3%A1%20pr%C3%ADru%C4%8Dka/sparql.webp)

Na webové stránce lze přepínat mezi 5 jazyky, vybírat předpřipravené SPARQL dotazy a SPARQL dotazy spouštět a vidět výsledky.

Editor SPARQL dotazu je realizován nástrojem [Yasgui], jehož uživatelská dokumentace je [dostupná online](https://triply.cc/docs/yasgui).

Data v NKOD se řídí specifikací [DCAT-AP-SK 2.0].
Data o výsledcích měření jsou pak založena na specifikaci [Data Quality Vocabulary].

Aktualizace NKOD probíhá zpravidla denně, v noci.

# 3.1 URL s výstupními daty
Kromě webové stránky samotné jsou vystavena i následující URL.
Předpokládejme, že prefixem všech URL je `https://opendata.mirri.tech`.

## SPARQL endpoint NKOD
- [`/api/sparql`](https://opendata.mirri.tech/api/sparql) - SPARQL endpoint NKOD

## Soubory s obsahem NKOD v RDF
- [`/subor/nkod.trig`](https://opendata.mirri.tech/subor/nkod.trig) - Dump obsahu NKOD v [RDF TriG]
- [`/subor/lkody.trig`](https://opendata.mirri.tech/subor/lkody.trig) - Informace o registrovaných LKODech v [RDF TriG]
- [`/subor/nkod-metadata.ttl`](https://opendata.mirri.tech/subor/nkod-metadata.ttl) - základní metadata o NKOD, zejména datum poslední aktualizace v [RDF Turtle]

## Soubory s výsledky z měření kvality a dostupnosti

### Dostupnost registrovaných zdrojů
- [`/subor/kvalita/dostupnost/dostupnost.ttl`](https://opendata.mirri.tech/subor/kvalita/dostupnost/dostupnost.ttl) - Poslední detailní výsledky měření
- [`/subor/kvalita/dostupnost/dostupnost-archiv.ttl`](https://opendata.mirri.tech/subor/kvalita/dostupnost/dostupnost-archiv.ttl) - Poslední agregované výsledky měření
- [`/subor/kvalita/dostupnost-archiv/dostupnost-YYYY-MM-DD.ttl.gz`](https://opendata.mirri.tech/subor/kvalita/dostupnost-archiv/dostupnost-YYYY-MM-DD.ttl.gz) - Komprimovaný archiv agregovaných výsledků měření k danému datu

### Dostupnost [CORS] na registrovaných zdrojích
- [`/subor/kvalita/dostupnost-cors/cors.ttl`](https://opendata.mirri.tech/subor/kvalita/dostupnost-cors/cors.ttl) - Poslední detailní výsledky měření
- [`/subor/kvalita/dostupnost-cors/cors-archiv.ttl`](https://opendata.mirri.tech/subor/kvalita/dostupnost-cors/cors-archiv.ttl) - Poslední agregované výsledky měření
- [`/subor/kvalita/dostupnost-cors-archiv/dostupnost-cors-YYYY-MM-DD.ttl.gz`](https://opendata.mirri.tech/subor/kvalita/dostupnost-cors-archiv/dostupnost-cors-YYYY-MM-DD.ttl.gz) - Komprimovaný archiv agregovaných výsledků měření k danému datu

### Kvalita metadatových záznamů
- [`/subor/kvalita/kvalita/kvalita.ttl`](https://opendata.mirri.tech/subor/kvalita/kvalita/kvalita.ttl) - Poslední detailní výsledky měření
- [`/subor/kvalita/kvalita/kvalita-archiv.ttl`](https://opendata.mirri.tech/subor/kvalita/kvalita/kvalita-archiv.ttl) - Poslední agregované výsledky měření
- [`/subor/kvalita/kvalita-archiv/kvalita-YYYY-MM-DD.ttl.gz`](https://opendata.mirri.tech/subor/kvalita/kvalita-archiv/kvalita-YYYY-MM-DD.ttl.gz) - Komprimovaný archiv agregovaných výsledků měření k danému datu

### Kombinované indikátory
- [`/subor/kvalita/kombinace/kombinované-indikátory.ttl`](https://opendata.mirri.tech/subor/kvalita/kombinace/kombinované-indikátory.ttl) - Poslední agregované indikátory
- [`/subor/kvalita/kombinace-archiv/kombinované-indikátory-YYYY-MM-DD.ttl.gz`](https://opendata.mirri.tech/subor/kvalita/kombinace-archiv/kombinované-indikátory-YYYY-MM-DD.ttl.gz) - Komprimovaný archiv k danému datu

## Soubory s CSV reporty výsledků měření kvality a dostupnosti
Tyto soubory jsou v cestě `/subor/kvalita/indikátory/`.
- [`a1-1-nedostupnost.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a1-1-nedostupnost.csv) - Nedostupnost distribucí datových sad
- [`a1-2-nedostupnost-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a1-2-nedostupnost-seznam.csv) - Nedostupné distribuce datových sad
- [`a1-3-nedostupnost-cors.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a1-3-nedostupnost-cors.csv) - Nedostupnost techniky CORS u distribucí ve formě souboru ke stažení
- [`a1-4-nedostupnost-cors-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a1-4-nedostupnost-cors-seznam.csv) - Distribuce ve formě souboru ke stažení s nedostupnou technikou CORS u souboru ke stažení
- [`a2-1-nedostupnost-schemat.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a2-1-nedostupnost-schemat.csv) - Nedostupnost schémat distribucí datových sad
- [`a2-2-nedostupnost-schemat-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a2-2-nedostupnost-schemat-seznam.csv) - Nedostupná schémata distribucí datových sad
- [`a2-3-nedostupnost-cors-schemat.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a2-3-nedostupnost-cors-schemat.csv) - Nedostupnost techniky CORS u schémat distribucí ve formě souboru ke stažení
- [`a2-4-nedostupnost-schemat-cors-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a2-4-nedostupnost-schemat-cors-seznam.csv) - Distribuce ve formě souboru ke stažení s nedostupnou technikou CORS u schématu souboru ke stažení
- [`a3-1-nedostupnost-podminek-uziti.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a3-1-nedostupnost-podminek-uziti.csv) - Nedostupnost podmínek užití distribucí datových sad
- [`a3-2-nedostupnost-podminek-uziti-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a3-2-nedostupnost-podminek-uziti-seznam.csv) - Nedostupné podmínky užití distribucí datových sad
- [`a4-1-nedostupnost-dokumentace.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a4-1-nedostupnost-dokumentace.csv) - Nedostupnost uživatelské dokumentace datových sad
- [`a4-2-nedostupnost-dokumentace-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a4-2-nedostupnost-dokumentace-seznam.csv) - Nedostupné uživatelské dokumentace datových sad
- [`a5-1-neshoda-media-type.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a5-1-neshoda-media-type.csv) - Neshoda mezi formátem distribuce v NKOD a formátem indikovaným serverem - statistika
- [`a5-2-neshoda-media-type-list.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a5-2-neshoda-media-type-list.csv) - Neshoda mezi formátem distribuce v NKOD a formátem indikovaným serverem - seznam
- [`a6-1-nedostupnost-endpoint-url.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a6-1-nedostupnost-endpoint-url.csv) - Nedostupnost přístupových bodů distribucí ve formě datové služby
- [`a6-2-nedostupnost-endpoint-url-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a6-2-nedostupnost-endpoint-url-seznam.csv) - Nedostupné přístupové body distribucí ve formě datové služby
- [`a6-3-nedostupnost-endpoint-url-cors.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a6-3-nedostupnost-endpoint-url-cors.csv) - Nedostupnost techniky CORS u přístupových bodů distribucí ve formě datové služby
- [`a6-4-nedostupnost-endpoint-url-cors-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a6-4-nedostupnost-endpoint-url-cors-seznam.csv) - Přístupové body distribucí ve formě datové služby s nedostupnou technikou CORS
- [`a7-1-nedostupnost-endpoint-description.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a7-1-nedostupnost-endpoint-description.csv) - Nedostupnost popisů přístupových bodů distribucí ve formě datové služby
- [`a7-2-nedostupnost-endpoint-description-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a7-2-nedostupnost-endpoint-description-seznam.csv) - Nedostupné popisy přístupových bodů distribucí ve formě datové služby
- [`a7-3-nedostupnost-endpoint-description-cors.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a7-3-nedostupnost-endpoint-description-cors.csv) - Nedostupnost techniky CORS u popisů přístupových bodů distribucí ve formě datové služby
- [`a7-4-nedostupnost-endpoint-description-cors-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a7-4-nedostupnost-endpoint-description-cors-seznam.csv) - Popisy přístupových bodů distribucí ve formě datové služby s nedostupnou technikou CORS
- [`a8-1-nedostupnost-specifikaci-sluzeb.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a8-1-nedostupnost-specifikaci-sluzeb.csv) - Nedostupnost specifikací datových služeb
- [`a8-2-nedostupnost-specifikaci-sluzby-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a8-2-nedostupnost-specifikaci-sluzby-seznam.csv) - Nedostupné specifikace datových služeb
- [`a9-1-nedostupnost-specifikace.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a9-1-nedostupnost-specifikace.csv) - Nedostupnost specifikací datových sad
- [`a9-2-nedostupnost-specifikaci-seznam.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/a9-2-nedostupnost-specifikaci-seznam.csv) - Nedostupné specifikace datových sad
- [`nkod-pocty-sad.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/nkod-pocty-sad.csv) - Počty datových sad registrovaných z formuláře a přes LKOD
- [`nkod-typy.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/nkod-typy.csv) - Druhy registrace datových sad v NKOD
- [`nkod-zebricek.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/nkod-zebricek.csv) - Počty datových sad a distribucí dle poskytovatele
- [`q1.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q1.csv) - Počet distribucí bez specifikovaných podmínek užití dle poskytovatele
- [`q2.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q2.csv) - Počet datových sad, jejichž distribuce nemají specifikovány podmínky užití dle poskytovatele
- [`q3.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q3.csv) - Počet záznamů datových sad nesplňujících povinné atributy dle poskytovatele
- [`q3l.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q3l.csv) - Záznamy datových sad nesplňujících povinné atributy dle poskytovatele
- [`q4a.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q4a.csv) - Media typy souborů ke stažení dle poskytovatele
- [`q4b.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q4b.csv) - Specifikace datových služeb dle poskytovatele
- [`q4c.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q4c.csv) - Formáty dat distribucí celkem
- [`q5b.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q5b.csv) - Počty poskytovatelů dle podmínek užití distribucí
- [`q5c.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q5c.csv) - Podmínky užití distribucí celkem
- [`q5.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q5.csv) - Podmínky užití distribucí dle poskytovatele
- [`q6c.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q6c.csv) - Počty datových sad s danou periodicitou aktualizace celkem
- [`q6.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q6.csv) - Počty datových sad s danou periodicitou aktualizace dle poskytovatele
- [`q7a.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q7a.csv) - Počty datových sad s daným klíčovým slovem
- [`q7atop30.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q7atop30.csv) - Počty datových sad s daným klíčovým slovem - prvních 30
- [`q7b.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q7b.csv) - Počty poskytovatelů datových sad s daným klíčovým slovem
- [`q7btop30.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q7btop30.csv) - Počty poskytovatelů datových sad s daným klíčovým slovem - prvních 30
- [`q8.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q8.csv) - Počty distribucí datových sad s nevalidním MIME Typem
- [`q8l.csv`](https://opendata.mirri.tech/subor/kvalita/indikátory/q8l.csv) - Seznam distribucí datových sad s nevalidním MIME Typem

# 3.2 Registrační záznamy
V současnosti uživatelsky nelze spravovat registrační záznamy datových sad a lokálních katalogů.
Tuto fukncionalitu přidá až Portál otevřených dat.
Registrační záznamy tak spravuje Administrátor NKOD ručně v [GitHub repozitáři](https://github.com/datova-kancelaria/nkod-registrace).
Registrační záznam datové sady se řídí [DCAT-AP-SK 2.0].
Registrační záznam LKOD typu [DCAT-AP Dokumenty](https://datova-kancelaria.github.io/dcat-ap-sk-2.0/#rozhranie-dcat-ap-dokumenty) vypadá například takto:
```turtle
@prefix dct: <http://purl.org/dc/terms/> .
@prefix dcat: <http://www.w3.org/ns/dcat#> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix vcard: <http://www.w3.org/2006/vcard/ns#> .

@prefix lkod: <https://data.gov.sk/def/local-catalog-type/> .

<https://data.gov.sk/set/catalog/0144444444> a dcat:Catalog, lkod:1 ;
    dct:title "DCAT-AP-SK 2.0 DCAT-AP Dokumenty LKOD 1"@sk, "DCAT-AP-SK 2.0 DCAT-AP Documents LKOD 1"@en ;
    dcat:contactPoint [
        a vcard:Organization ;
        vcard:fn "Jakub Klímek"@sk ;
        vcard:hasEmail <mailto:jakub.klimek@matfyz.cuni.cz>
    ];
    dct:publisher <https://data.gov.sk/id/legal-subject/00681156> ;
    dcat:endpointURL <https://raw.githubusercontent.com/jakubklimek/sk-lkod-1/main/catalog.ttl> ;
    foaf:homepage <https://github.com/jakubklimek/sk-lkod-1> .
```

kde `lkod:1` indikuje typ LKOD dle číselníku typů LKOD.

Pro přidání registračního záznamu doporučujeme zkopírovat nějaký existující a upravit IRI a obsažená metadata.

# 4.	NÁROKY NA POUŽÍVATEĽA
Uživatelé NKOD jsou 3 druhů.
<dl>
  <dt>Návštěvník bez znalosti SPARQL</dt>
  <dd>Tento si vystačí s předpřipravenými SPARQL dotazy a tedy nemusí mít žádné zvláštní dovednosti.</dd>
  <dt>Návštěvník se znalostí SPARQL</dt>
  <dd>Pokud chce návštěvník psát vlastní SPARQL dotaz, musí ovládat SPARQL a RDF, znát specifikaci DCAT-AP-SK 2.0, a pro zpracovávání údajů z měření kvality pak ještě Data Quality Vocabulary.</dd>
  <dt>Externí IS</dt>
  <dd>Externí IS bude přistupovat k webové službě SPARQL endpointu. Potřebuje knihovnu pro práci se SPARQLem, znát RDF, znát specifikaci DCAT-AP-SK 2.0, a pro zpracovávání údajů z měření kvality pak ještě Data Quality Vocabulary.</dd>
</dl>

[DCAT-AP-SK 2.0]: https://datova-kancelaria.github.io/dcat-ap-sk-2.0/ "DCAT-AP-SK 2.0"
[LinkedPipes ETL]: https://etl.linkedpipes.com "LinkedPipes ETL"
[SPARQL]: https://www.w3.org/TR/sparql11-query/ "SPARQL"
[Ontotext GraphDB Free]: https://www.ontotext.com/products/graphdb/download/ "Ontotext GraphDB Free"
[Resource Description Framework (RDF)]: https://www.w3.org/TR/rdf11-concepts/ "RDF"
[Data Quality Vocabulary]: https://www.w3.org/TR/vocab-dqv/ "Data Quality Vocabulary"
[Yasgui]: https://yasgui.triply.cc/ "Yasgui"
[CSV]: https://www.rfc-editor.org/rfc/rfc4180 "CSV"
[CORS]: https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS "Cross-Origin Resource Sharing"
[RDF Turtle]: https://www.w3.org/TR/turtle/ "RDF Turtle"
[RDF TriG]: https://www.w3.org/TR/trig/ "RDF TriG"