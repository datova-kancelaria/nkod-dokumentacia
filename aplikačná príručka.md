# Aplikační příručka - Národní katalog otevřených dat

# 1. ROZSAH PLATNOSTI A ÚČEL

Tato aplikační příručka slouží k popisu Národního katalogu otevřených dat (NKOD), části projektu OD2.0.

# 2. DEFINÍCIA POJMOV A SKRATIEK

<dl>
    <dt>CORS</dt>
    <dd>Cross-Origin Resource Sharing</dd>
    <dt>DCAT-AP-SK 2.0</dt>
    <dd>Specifikace pro metadatový záznam datasetu otevřených dat</dd>
    <dt>k8s</dt>
    <dd>Kubernetes</dd>
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

# 3. POPIS FUNKCIONALITY IS

## 3.1 STRUČNÝ POPIS IS

Národní katalog otevřených dat (NKOD) obsahuje zejména databázi metadatových záznamů datasetů otevřených dat poskytovaných různými institucemi veřejné správy, která poskytuje [SPARQL] endpoint pro dotazování.
V databázi se zrcadlí metadataové záznamy datasetů registrovaných jednotlivě přímo v NKOD a záznamy pocházející z Lokálních katalogů otevřených dat (LKOD) provozovaných přímo poskytovateli dat.
Metadatové záznamy odpovídají specifikaci [DCAT-AP-SK 2.0]. 
Databáze NKOD je tvořena pravidelně denně.
Po každém vytvoření databáze NKOD je zhodnocena i kvalita metadatových záznamů vzhledem k [DCAT-AP-SK 2.0] a dostupnost registrovaných zdrojů.
Na základě naměřených hodnot jsou vygenerovány reporty obsahující zjištěné skutečnosti.

## 3.2 ZOZNAM A ZÁKLADNÝ POPIS SUBSYSTÉMOV A FUNKCIÍ
![Aplikační architektura NKOD](obrázky/aplikačná%20príručka/aplika%C4%8Dn%C3%AD%20architektura.svg)

NKOD se skládá z následujících komponent:
1. RDF úložiště implementované pomocí [Ontotext GraphDB Free] poskytující SPARQL endpoint pro dotazování
2. Metadatový procesor a harvester implementovaný pomocí [LinkedPipes ETL]. Samotné datové procesy, harvestování lokálních katalogů, měření kvality a generování reportů jsou implementovány jako datové pipeliny, které jsou uloženy a zdokumentovány v [GitHub repozitáři](https://github.com/datova-kancelaria/nkod-sk), včetně pipeline zajišťujících migraci ze stávajícího data.gov.sk.
3. Webová stránka zpřístupňující stahování obsahu NKOD a SPARQL dotazování včetně předpřipravených SPARQL dotazů
4. Manažer zajišťuje, že datové pipeliny implementující harvestaci a měření kvality se před jejich spuštěním aktualizují z [GitHub repozitáře](https://github.com/datova-kancelaria/nkod-pipeline).
Dále na pokyn plánovače spouští samotnou harvestaci.
Nakonec tato komponenta zajišťuje, že registrační záznamy datových sad, lokálních katalogů otevřených dat a jednotlivých poskytovatelů, které dočasně administrátor NKOD ukládá v [GitHub repozitáři](https://github.com/datova-kancelaria/nkod-registrace), se před harvestací dostanou do prostředí NKOD.
Tato poslední funkcionalita bude později upravena/nahrazena správou registračních záznamů v jiné části projektu OD2.0 - v Portálu otevřených dat (POD).

## 3.3 DÁTOVY MODEL APLIKÁCIE
Data NKOD jsou uchovávána v datovém modelu [Resource Description Framework (RDF)] a skládají se z metadat datových sad registrovaných do NKOD či harvestovaných z LKOD.
Jejich struktura se řídí specifikací [DCAT-AP-SK 2.0].

![Datový model DCAT-AP-SK 2.0](obrázky/aplikačná%20príručka/dcat-ap-sk-2.0.svg)

Část týkající se měření kvality záznamů pak vychází ze slovníku [Data Quality Vocabulary].

![Datový model pro měření kvality](obrázky/aplikačná%20príručka/DQV.svg)

## 3.4 ZÁKLADNÝ FLOWCHART IS
![Flow na úrovni NKOD](obrázky/aplikačná%20príručka/IS%20flow.svg)

V diagramu vidíme uživatelské role

<dl>
  <dt>Administrátor NKOD</dt>
  <dd>Edituje seznam předpřipravených SPARQL dotazů a dočasně spravuje registrační záznamy, než správu převezmou poskytovatelé dat pomocí budoucího Portálu otevřených dat.</dd>
  <dt>Poskytovatel dat</dt>
  <dd>Provozuje registrované LKODy. V budoucnosti bude skrz Portál otevřených dat spravovat své registrační záznamy. Může vystupovat i v roli Návštěvníka.</dd>
  <dt>Externí IS</dt>
  <dd>Ptá se SPARQLem na SPARQL endpoint RDF úložiště, který zpřístupňuje Webová stránka.</dd>
  <dt>Návštěvník</dt>
  <dd>Ptá se SPARQLem na SPARQL endpoint RDF úložiště. SPARQL dotaz píše ve formuláři na Webové stránce, nebo si vybere jeden z Administrátorem NKOD připravených SPARQL dotazů.</dd>
</dl>

## 3.5 POPIS EXISTUJÚCICH ROZHRANÍ V IS

### 3.5.1 Webová stránka
![Webová stránka NKOD](obrázky/aplikačná%20príručka/sparql.webp)

Webová stránka NKOD je hlavní rozhraní pro návštěvníky NKOD - umožňuje vybrat předpřipravený SPARQL dotaz nebo napsat vlastní, a nechat ho vykonat na rozhraní SPARQL endpoint nad RDF úložištěm NKOD a zobrazit výsledky.
Pro samotnou práci se SPARQL dotazem využívá knihovnu [Yasgui].
Webová stránka je k dispozici vícejazyčně, nyní v 5 jazycích.
Přes komponentu webové stránky je zpřístupněn i obsah NKOD v podobě souborů ke stažení.

### 3.5.2 SPARQL endpoint
Samotný SPARQL endpoint je webová služba primárně určená pro komunikaci s aplikacemi.
Vyhodnocuje dotazy v jazyce [SPARQL] nad RDF úložištěm NKOD.
Toto rozhraní je také využíváno formulářem pro SPARQL dotaz na Webové stránce.

### 3.5.3 LinkedPipes ETL
![LinkedPipes ETL](obrázky/aplikačná%20príručka/etl.webp)

Rozhraní [LinkedPipes ETL] není veřejně přístupné a je určeno pouze pro Administrátora NKOD pro monitoring běhu harvestace NKOD.
Ten může vidět poslední tři běhy harvestace a případně vidět a diagnostikovat chyby, ke kterým může dojít.

## 3.6 ZÁKLADNÝ POPIS HLAVNÝCH PROCESOV V SUBSYSTEMOCH
![Proces harvestace](obrázky/aplikačná%20príručka/hlavn%C3%AD%20proces.webp)

Vyobrazen je proces tvorby obsahu NKOD realizovaný komponentou LinkedPipes ETL.
Zahrnuje zpracování registračních záznamů v NKOD a následnou harvestaci registrovaných LKODů, které mohou být realizovány pomocí 3 typů rozhraní dle [DCAT-AP-SK 2.0].
Výsledkem jsou sesbíraná metadata datových sad otevřených dat tvořící obsah NKOD.
Dále jsou spuštěny kontroly kvality metadatových záznamů, dostupnosti registrovaných zdrojů a dostupnosti techniky CORS na registrovaných zdrojích.
Výsledky jsou uloženy do RDF úložiště NKOD a z nich vytvořené reporty jsou zpřístupněny v podobě souborů [CSV] ke stažení.

## 3.7 POŽIADAVKY NA BEZPEČNOSŤ A OCHRANU ÚDAJOV V IS

### 3.7.1 Organizačné zabezpečenie
Přístup ke cloudové infrastruktuře, přes kterou lze aplikaci konfigurovat a nasadit, má pouze Administrátor NKOD.
Veřejně dostupná rozhraní jsou dostupná bez přihlášení, ale umožňují pouze čtení, nikoliv modifikaci.

### 3.7.2 Technické zabezpečenie
Pro zápis dat při procesu harvestace je nastaven systémový uživatel, jehož přístupové údaje jsou součástí konfigurace systému při nasazení, což se děje pomocí přístupu ke cloudové infrastruktuře, kde je aplikace nasazena.
Pro přístup k webovým rozhraním aplikace je použit standardní zabezpečený protokol HTTPS.

### 3.7.3 Spôsob a prideľovanie prístupových práv do IS
NKOD neobsahuje žádné části, ke kterým by bylo potřeba přidělovat přístup.
Řízení přístup ke cloudové infrastruktuře je pak mimo rozsah aplikační příručky NKOD.

### 3.7.4 Ochrana údajov a úprava IS
Úložiště NKOD obsahuje data, která jsou získána ze zaslaných registračních záznamů datových sad a v procesu harvestace sesbírána z veřejně přístupných zaregistrovaných LKODů a dále výsledky měření kvality nad vzniklým obsahem NKOD.
Neobsahuje tedy žádná data, která by bylo potřeba chránit z hlediska přístupu ke čtení.
Je třeba tedy pouze zabezpečit, že data nikdo neautorizovaně nezmění.
To je zajištěno nastavením úložiště RDF tak, že pro nepřihlášený uživatel má přístup pouze ke čtení.
Systémový uživatel s právy zápisu je tvořen při nasazení aplikace dle poskytnuté konfigurace.

# 4. POPIS MODULOV

## 4.1 RDF úložiště
![RDF úložiště](obrázky/aplikačná%20príručka/modul%20rdf%20%C3%BAlo%C5%BEi%C5%A1t%C4%9B.svg)

### 4.1.1 Popis modulu a komunikácií
RDF úložiště implementované pomocí [Ontotext GraphDB Free] slouží k ukládání aktuálního stavu NKOD. Pro dotazování poskytuje jako rozhraní svůj SPARQL endpoint. Stejný endpoint je použit i interně pro zápis pod systémovým uživatelem.

### 4.1.2 Tok údajov medzi modulmi vstup – modul – výstup
Po harvestaci a měření kvality je do něj uložen aktuální stav NKOD přes rozhraní SPARQL endpoint pod systémovým uživatelem.
Při příchodu SPARQL dotazu přes Webovou stránku je dotaz vyhodnocen a jsou vráceny výsledky.

## 4.2 Metadatový procesor - LinkedPipes ETL
![Metadatový procesor - LinkedPipes ETL](obrázky/aplikačná%20príručka/modul%20etl.svg)

### 4.2.1 Popis modulu a komunikácií
Metadatový procesor implementovaný pomocí [LinkedPipes ETL] (LP-ETL) je zodpovědný za tvorbu obsahu NKOD, měření kvality metadat a dostupnosti registrovaných datových zdrojů a techniky [CORS] na nich.
ETL pipeliny použité pro NKOD jsou publikovány a dokumentovány v [GitHub repozitáři](https://github.com/datova-kancelaria/nkod-sk).
Z tohoto repozitáře se také před každým během stahují - nemá tedy smysl je modifikovat přímo v testovacím či produkčním prostředí.

### 4.2.2 Tok údajov medzi modulmi vstup – modul – výstup
Na vstupu pro proces tvorby obsahu NKOD LP-ETL čte registrační záznamy jednotlivých datových sad, LKODů a informace o registrovaných poskytovatelích z úložišť registrací.
Na základě registrací LKODů pak čte jejich API a dostává registrační záznamy datových sad v nich obsažených.
Výstupem tohoto kroku je obsah NKOD, který je uložen do RDF úložiště skrz SPARQL endpoint, a do souborového systému jako soubory ke stažení, které zpřístupňuje Webová stránka.
Následuje proces měření kvality metadat, který si vystačí s právě vygenerovaným obsahem NKOD, a proces měření dostupnosti zdrojů a dostupnosti techniky [CORS] na nich, který navíc přistupuje ke všem registrovaným zdrojům pomocí jejich URL.

## 4.3 Webová stránka
![Webová stránka](obrázky/aplikačná%20príručka/modul%20stránka.svg)

### 4.3.1 Popis modulu a komunikácií
Webová stránka je jediné rozhraní NKOD směrem k uživateli.
Obsahuje seznam předpřipravených SPARQL dotazů konfigurovaných Administrátorem NKOD a editor vlastního SPARQL dotazu [Yasgui].
Uživatel může zvolit předpřipravený dotaz nebo vytvořit vlastní.
SPARQL dotaz pak vykoná nad RDF úložišťem a vidí výsledky.
Modul webové stránky pak slouží také pro zpřístupnění souborů ke stažení a technické zpřístupnění SPARQL endpointu pro externí IS formou reverse-proxy.

### 4.3.2 Tok údajov medzi modulmi vstup – modul – výstup
Webová stránka je vystavená na Internet.
V oblasti SPARQL dotazování je vstupem SPARQL dotaz, který se předá RDF úložišti k vykonání.
Vrácené výsledky se zobrazí na webové stránce.
Vstupem pro webovou stránku je také konfigurace předpřipravených SPARQL dotazů.
V oblasti souborů ke stažení je pak vstupem soubor ve filesystému, který je zpřístupněn ke stažení přes daná URL.

## 4.4 Manažer
![Manažer](obrázky/aplikačná%20príručka/modul%20manažer.svg)

### 4.4.1 Popis modulu a komunikácií
Manažer je modul zodpovědný za
1. Zrcadlení [GitHub repozitáře](https://github.com/datova-kancelaria/nkod-registrace) s registračními záznamy datových sad, LKODů a poskytovatelů, který spravuje Administrátor NKOD, do filesystému NKOD. Tato funkcionalita bude v budoucnu nahrazena Portálem otevřených dat (POD), který bude registrační záznamy spravovat.
Bude třeba zajistit pouze předávání takto spravovaných záznamů z POD do filesystému NKOD.
2. Aktualizaci samotných pipeline z [GitHub repozitáře](https://github.com/datova-kancelaria/nkod-pipeline) před jejich spuštěním.
3. Spuštění první pipeline v řetězu zajišťujícím celý proces harverstace a měření kvality.

### 4.4.2 Tok údajov medzi modulmi vstup – modul – výstup
Vstupem je [GitHub repozitář s registračními záznamy datových sad](https://github.com/datova-kancelaria/nkod-registrace) a [GitHub repozitář s pipelinami NKOD připravenými k importu](https://github.com/datova-kancelaria/nkod-pipeline).
Výstupem je klon repozitáře ve filesystému NKOD a aktualizované pipeliny v LinkedPipes ETL.

# 6. NÁROKY NA POUŽÍVATEĽA
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
