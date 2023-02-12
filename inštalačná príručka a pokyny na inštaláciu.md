# Inštalačná príručka a pokyny na inštaláciu (úvodnú/opakovanú) - Národní katalog otevřených dat

Čtenář této příručky by měl být obeznámen s:
* [Kubernetes](https://kubernetes.io/docs/home/)
* [Oracle Cloud Infrastructure (OCI)](https://docs.oracle.com/en-us/iaas/Content/home.htm)

## 1. ROZSAH PLATNOSTI A ÚČEL

Tato inštalačná příručka slouží k popisu Národního katalogu otevřených dat (NKOD), části projektu OD2.0.

## 2. DEFINÍCIA POJMOV A SKRATIEK

<dl>
  <dt>NKOD</dt>
  <dd>Národní katalog otevřených dat</dd>
  <dt>OCI</dt>
  <dd>Oracle Cloud Infrastructure</dd>
  <dt>OCID</dt>
  <dd>Oracle Cloud Identifier</dd>
  <dt>LP-ETL</dt>
  <dd>LinkedPipes ETL</dd>
  <dt>SPARQL Endpoint</dt>
  <dd>Webová služba běžící nad RDF databází, přijímající dotazy v jazyce SPARQL, a vracející výsledky vyhodnocení dotazu nad daty v databázi.</dd>
  <dt>OCPU</dt>
  <dd>The Oracle CPU</dd>
</dl>

## 3. POŽIADAVKY NA ZÁKLADNÉ HW / Cloud a SW PROSTREDIE

Tato instalační příručka počítá s existencí Kubernetes clusteru verze 1.24.1 v prostředí OCI.

Z hlediska hardwarových požadavků jsou směrodatné dvě výpočetně intenzivní komponenty:
* RDF úložiště
* Metadatový procesor - LinkedPipes ETL.
Provoz každé z komponent požaduje 16GB operační paměti a jedno OCPU, které odpovídá zhruba dvěma CPU.
Výše uvedené komponenty nelze v rámci clusteru replikovat.
Z toho důvodu je postačují, aby měl cluster tři výpočetní instance (worker nodes).

## 4. POSTUP INŠTALÁCIE (ÚVODNEJ / OPAKOVANEJ)

### 4.1 Popis inštalácie SERVEROVEJ ČASTI

Serverová část řešení je nasazena do Kubernetes clusteru na OCI.
Uvedené příkazy je potřeba pouštět OCI Cloud Shell připojeném do sítě Kubernetes.
Připojení ke správné síti můžeme otestovat funkčností příkazu ```kubectl```.

#### 4.1.1 Zjištění informací o clusteru

Pro potřeby instalace je třeba zjistit základní informace o OCI. 
* _{Availability Domain}_
* _{Compartment}_
* _{Virtual Cloud Network}_
* _{Subnet}_

Tyto hodnoty by nám měl sdělit správce OCI do kterého provádíme instalaci.
Pro interakci s Kubernetes je téměř vždy třeba znát OCID daného zdroje, nikoliv jeho lidsky čitelné pojmenování.

**TODO:** Vypsat jak konkrétně to je pro test/produkci?

#### 4.1.2 OCI File Systems

Data NKOD jsou uložena na dvou typech úložišť [_Block Storage_](https://docs.oracle.com/en-us/iaas/Content/Block/Concepts/overview.htm) a _File System_.
První typ je vhodný pro datově intenzivní operace, například databáze. 
Druhý pak spíše pro sdílení souborů. 

Oba druhy úložišť vytvořit z prostředí Kubernetes.
Nicméně z důvodu snazší instalace a lepší použitelnosti jsou úložiště _File System_ vytvořena v prostředí OCI a následně použita v Kubernetes.
Samotný _File System_ poskytuje pouze datové úložiště, pro jehož použití musíme definovat _Export Path_, která je dostupná přes _Mount Target_.
Neb je _Mount Target_ schopen obsloužit více _File System_, využijeme pro jednoduchost pouze jedné jeho instance. 

Následující seznam obsahuje údaje nezbytné pro [vytvoření _File System_](https://docs.oracle.com/en-us/iaas/Content/File/Tasks/creatingfilesystems.htm#Creating_File_Systems):
* NODC-Website
  * Name: NODC-Website
  * Compartment: _{Compartment}_
  * Export path: /website
  * Mount name: MountTarget-NODC
  * VCN: _{Virtual Cloud Network}_
  * Subnet: _{Subnet}_
* NODC-Registration
  * Name: NODC-Registration
  * Compartment:  _{Compartment}_
  * Export path: /registration
  * Vyberme existující _Mount Target_ vytvořený pro první _File System_
* NODC-LinkedPipes-Storage
  * Name: NODC-LinkedPipes-Storage
  * Compartment:  _{Compartment}_
  * Export path: /linkedpipes-storage
  * Vyberme existující _Mount Target_ vytvořený pro první _File System_
* NODC-Certificate
  * Name: NODC-LinkedPipes-Certificate
  * Compartment:  _{Compartment}_
  * Export path: /certificate
  * Vyberme existující _Mount Target_ vytvořený pro první _File System_

Pro další kroky v instalaci bude třeba si zjistit následující informace o vytvořených objektech: 
* OCID pro NODC-LinkedPipes-Storage
* OCID pro NODC-Registration
* OCID pro NODC-Website
* OCID pro NODC-Certificate
* IP adresu vytvořeného _Mount Target_

Všechny tyto hodnoty je možné zjistit skrze webové rozhraní v detaily jednotlivých objektů.

#### 4.1.3 Projektový repozitář

Pro potřeby instalace vyžijeme definice z repozitáře [NKOD-SW].
Nejsnazším řešením je repozitář naklonovat do domovského adresáře v OCI Cloud Shell:
```shell
git clone https://github.com/datova-kancelaria/nkod-software.git
```
V dalších krocích předpokládáme, že se uživatel nachází v kořeni naklonovaného repozitáře, tedy adresáři ```./nkod-software/```.

#### 4.1.4 Kubernetes jmenné prostory

Všechny Kubernetes objekty NKOD jsou umístěny ve jmenném prostoru.
Ten je možné vytvořit následujícím příkazem:
```shell
kubectl apply -f ./k8s/namespace.yaml
```

*Poznámka*: Ačkoliv je možné jmenné prostory tvořit poměrně snadno z příkazové řádky, umístíme je do souboru.
Důvodem je menší počet příkazů a snazší přidání nových jmenných prostorů bez změny procesu instalace.

#### 4.1.5 Propojení Kubernetes a OCI

V tomto kroku budeme vytváře Kubernetes objekty, které se napojují na objekty v OCI.
Do této kategorie patří zejména datová úložiště vytvořená v 4.1.2 OCI File Systems ale i další OCI specifické objekty.

Vytvoření potřebných tříd pro _Block Storage_ je možné pomocí následujících příkazů:
```shell
kubectl apply -f k8s/oci/oci-block-storage-balanced.yaml
kubectl apply -f k8s/oci/oci-block-storage-high.yaml
```

*Poznámka*: V základní konfiguraci se využívá pouze balanced _Block Storage_ a tvorba druhého typu _Block Storage_ tak vlastně není nutná.
Tento typ úložiště se používá v ```./k8s/graphdb/graphdb-pvc.yaml``` a ```./k8s/linkedpipes/executor-pvc.yaml```.
Zde je možné přejít na výkonnější třídu úložiště v případě nedostatečného výkonu.


Dále je třeba napojit Persistent Volume Claims z Kubernetes na _File System_ z OCI.
Za tímto účelem bude třeba upravit soubory Persistent Volume Claims definující soubory:
* ```./k8s/oci/linkedpipes-storage-pv.yaml```
* ```./k8s/oci/registration-pv.yaml```
* ```./k8s/oci/website-pv.yaml```
* ```./k8s/oci/certificate-pv.yaml```

V každém souboru je třeba provést substituci hodnot získaných v předchozích krocích.
Jedná se vždy o hodnotu pro ```spec.csi.volumeHandle```.
Úprava souborů je až na použité hodnoty totožná, z tohoto důvodu popíšeme úpravu pouze pro  ```./k8s/oci/website-pv.yaml```.
Ostatní soubory je třeba upravit podobným způsobem.

V tomto souboru je nutné nahradit ```#  {NODC-Website-OCID}:{MountTargetIP}:/website```, včetně začátku komentáře ```#``` za konkrétní hodnoty.
Mějme například hodnoty:
* ```NODC-Website-OCID``` = ```ocid1.filesystem.oc1.prague.sd_223```
* ```MountTargetIP``` = ```10.0.1.123```

Řádek po úpravě bude vypadat následovně:
```yaml
    volumeHandle: ocid1.filesystem.oc1.prague.sd_223:10.0.1.123:/website
```
Při substituci je třeba nezapomenout na zachování odsazení řádku, které určuje strukturu YAML dokumentu.

Jakmile jsou soubory upraveny můžeme na jejich základě vytvořit definice:
```shell
kubectl apply -f ./k8s/oci/linkedpipes-storage-pv.yaml
kubectl apply -f ./k8s/oci/registration-pv.yaml
kubectl apply -f ./k8s/oci/website-pv.yaml
kubectl apply -f ./k8s/oci/certificate-pv.yaml
```

Dalším specifickým zdrojem je veřejný Load Balancer.
Hned na úvod je třeba upozornit na skutečnost, že smazáním Load Balanceru po jeho vytvoření ztratíme veřejnou IP adresu.
Z výše uvedeného důvodu doporučujeme po vytvoření Load Balanceru nemazat.

Podobně jako u souborových systémů je třeba i zde upravit definici zdroje.
V tomto případě je třeba nahradit hodnotu ```# {Subnet}``` v souboru ```./k8s/oci/website-load-balancer.yaml ```.
Hodnotu pro nahrazení jsme získali dle sekce 4.1.1 Zjištění informací o clusteru.
Jakmile budou soubor upraven, můžeme vytvořit definici:
```shell
kubectl apply -f website-load-balancer.yaml
```

#### 4.1.6 Konfigurace a tajemství

Konfigurační soubory jsou umístěné v adresáři ```./configuration```. 
Za účelem editace doporučujeme si soubory zkopírovat a  následně upravit.
Ve zbytku této sekce popíšeme význam jednotlivých konfiguračních souborů a jejich naplnění. 
Nakonec uvedeme příkazy pro vytvoření konfigurace v Kubernetes z uvedených konfiguračních souborů. 

*dockerconfig.json* tento soubor slouží pro nastavení přístupu k OCI [Container Registry](https://docs.oracle.com/en-us/iaas/Content/Registry/Concepts/registryoverview.htm).
Tuto konfiguraci potřebujeme, neb Kubernetes nemá v základu přístup do OCI Container Registry.
Popis vytvoření soubor je možné najít v návodu [Pull an Image from a Private Registry](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/).
Přihlášení do OCI Container registry je popsáno v [Logging in to Oracle Cloud Infrastructure Registry](https://docs.oracle.com/en-us/iaas/Content/Functions/Tasks/functionslogintoocir.htm), případně [Push an Image to Oracle Cloud Infrastructure Registry](https://www.oracle.com/webfolder/technetwork/tutorials/obe/oci/registry/index.html).

*nginx.conf* konfigurační soubor pro NginX, je možné nechat ve výchozí podobě.
V tomto souboru jsou uložené cesty k certifikátům.

*nodc-configuration.properties* konfigurace komponent v rámci NKOD. 
Význam jednotlivých nastavení je popsán v konfiguračním souboru. 
Soubor by nemělo být nutné měnit.

*nodc-secret.properties* tento soubor obsahuje konfiguraci hesel.
Význam jednotlivých nastavení je popsán v konfiguračním souboru. 

Jakmile máme všechny konfigurační soubory připravené můžeme vytvořit konfigurace a tajemství:
```shell
kubectl create configmap nodc-configuration --namespace=nodc --from-env-file=./configuration/nodc-configuration.properties 
kubectl create configmap nodc-nginx --namespace=nodc --from-file=website.nginx=./configuration/nginx.conf

kubectl create secret generic nodc-secret --namespace=nodc --from-env-file=./configuration/nodc-secret.properties
kubectl create secret generic oci-registry-secret --namespace=nodc --type=kubernetes.io/dockerconfigjson --from-file=.dockerconfigjson=./configuration/dockerconfig.json
```
Neb nevytváříme konfigurace ze souborů ale přímo z příkazové řádky, je třeba specifikovat cílový jmenný prostor pomocí ```--namespace=nodc```.


Ačkoliv je konfigurace propagována automaticky stejně jako tajemství, většina komponent čte konfiguraci pouze při vytvoření.
Z tohoto důvodu je třeba po změně konfigurace smazat Pody, nebo restartovat Deployment. 
Za tímto účelem je možné využít následující příkaz:
```shell
kubectl rollout restart deploy {deployment-name} --namespace=nodc
```
Kde ```{deployment-name}``` je třeba nahradit za jméno Deploymentu, jehož Pody se mají smazat a znovu vytvořit.

### 4.1.7 Sestavení a publikace Docker image pro RDF úložiště

Pro potřeby RDF úložiště je zvolena databáze [Ontotext GraphDB Free].
Tato verze bohužel nemá dostupný použitelný Docker image.
Navíc verze ani není veřejně dostupná pro stažení a proto není možné Docker image automaticky vytvořit a publikovat na GitHubu.
Z tohoto důvodu je třeba GraphDB image sestavit na jiném stroji a publikovat ho do OCI [Container Registry](https://docs.oracle.com/en-us/iaas/Content/Registry/Concepts/registryoverview.htm).
Za účelem publikace je třeba mít zřízen přístup.
Pro získání přístupu lze využít obdobný postup jako pro vytvoření *dockerconfig.json* souboru v sekci 4.1.6 Konfigurace a tajemství.

Chybějící soubor je možné stáhnout přímo ze stránek [Ontotext GraphDB Free] po registraci.
Stažený soubor je nutné umístit do adresáře ```./components/graphdb```. 
V současné verzi s verzí 10.0.0 a tedy názvem soubor ```graphdb-free-10.0.0-dist.zip```.
V případě změny je nutné upravit ```GRAPHDB_URL``` v souboru ```./components/graphdb/Dockerfile```. 

Po přidání chybějícího souboje je pak možné z  adresáři ```./components/graphdb``` provést sestavení a publikaci pomocí příkazů:
```shell
docker login -u '{object-storage-namespace}/{identity-domain}/{user-name}' fra.ocir.io
docker build -t fra.ocir.io/{object-storage-namespace}/graphdb:10.0.0 .
docker push fra.ocir.io/{object-storage-namespace}/graphdb:10.0.0
```

Hodnotu ```{object-storage-namespace}``` je možné získat pomocí příkazu ```oci os ns get```.
Hodnota ```{identity-domain}``` je součástí uživatelského jména.

#### 4.1.8 Nasazení RDF úložiště GraphDB

Nasazení se provede pomocí příkazu:
```shell
kubectl apply -f ./k8s/graphdb/
```

#### 4.1.9 Nasazení Metadatového procesoru - LinkedPipes ETL

Nasazení se provede pomocí příkazu:
```shell
kubectl apply -f ./k8s/linkedpipes/
```

#### 4.1.10 Získání HTTPS certifikátu

Aby mohla probíhat HTTPS terminace uvnitř prostředí NKOD, je zapotřebí získat certifikát.
Ten je třeba uložit do ```certificate-pvc```.

Tento proces je možné provést pomocí příkazu:
```shell
kubectl apply -f ./k8s/certificate-manager/update-certificate.yaml
```
Vytvořený job je rozpoznán jako cíl pro Load Balancer a je na něj po čas běhu vedena komunikace z portu 80 pro ověření.
Z tohoto důvodu nesmí nesmí v době běhu být spuštěna komponenta Webová stránka.
Příkaz vytvoří job pro Docker container z ```./components/certificate-manager```.
Ten využívá Let's Encrypt pro získání certifikátu.
Let's Encrypt využije HTTP ověření přístupem na URL domény, pro které je certifikát požadován.
Je tedy třeba nastavit příslušný DNS záznam před tímto krokem.
Certifikát má platnost 3 měsíce a je tedy třeba ho manuálně obnovovat.

#### 4.1.11 Nasazení Webové stránky

Jakmile máme k dispozici certifikát a běží všechny ostatní služby je možné nasadit Webovou stránku.
Důvodem pro toto omezení je vlastnost NginX, který při startu ověřuje dostupnost služeb pro které funguje jako proxy.
Nasazení se provede následujícím příkazem:
```shell
kubectl apply -f ./k8s/website/
```

Součástí konfigurace webové stránky je seznam SPARQL dotazů.
Tento seznam je uložen v souboru ```./components/website/www/src/sparql-queries.yaml```.
Po zapsání změn v tomto souboru do repozitáře dojde k automatickému sestavení a publikaci Docker image.
V případě změny konfigurace při běžící komponentě Webové stránky je nutné vynutit její přenasazení.
To je možné pomocí následujícího příkazu:
```shell
kubectl rollout restart deploy nodc-website-deployment --namespace=nodc
```

#### 4.1.12 První spuštění

Po první instalaci je třeba vytvořit kopii dat pro další zpracování v Metadatovém procesoru - LinkedPipes ETL.
Toho je možné dosáhnout ručním spuštění pipeline ```00 - Cache```.
Tuto pipeline je možné pustit opakovaně pro aktualizaci připravených dat.

<!-- TODO Using VPN -->

### 4.1.13 Manažer

O pravidelné zpracování dat se stará komponenta Manažer.
Tuto komponentu lze nasadit pomocí příkazu:
```shell
kubectl apply -f ./k8s/manager.yaml
```
Čas zpracování je nastaven v souboru ```./k8s/manager.yaml``` v časové zóně UTC+0.

Zpracování je také možné pustit ručně.
V příkazu je nutné nahradit placeholder ```{job-name}``` unikátním identifikátorem jobu.
Identifikátor může být například ```nodc-manager-cronjob-manual-20230110```, výsledný příkaz pak vypadá následovně:
```shell
kubectl create job --namespace=nodc --from=cronjob/nodc-manager-cronjob {job-name}
```

### 4.2 Popis Inštalácie KLIENTSKEJ ČASTI

Klientskou částí je webová stránka pro dotazování nad RDF databází. 
Tuto část není třeba instalovat.

## 5. CHYBOVÉ STAVY PRI KONFIGURÁCIÍ

Tato sekce popisuje možné chybové stavy a komplikace v procesu instalace.

### 5.1 Nelze se přihlásit do OCI Container Registry

V případě pokusu o nahrání GraphDB obrazu můžeme dostat následující chybu:
```Error response from daemon: Get "https://eu-frankfurt-1.ocir.io/v2/": unknown: Unauthorized```
Důvodem může být absence object-storage-namespace v uživatelském jménu. 
Uživatelské jméno má vzor ```{object-storage-namespace}/{identity-domain}/{user-name}``` kde 
Hodnotu ```{object-storage-namespace}``` je možné získat pomocí příkazu ```oci os ns get```.
Hodnota ```{identity-domain}``` je zadána v procesu přihlášení do OCI.

Podobnou chybu můžeme získat i při nasazení GraphDB do Kubernetes, kdy není možné image stáhnout z OCI Container Registry.
V takovém případě je třeba zkontrolovat přihlašovací údaje a tajemství ```oci-registry-secret```.

## 6. UMIESTNENIE ZAKLADNEJ DOKUMENTÁCIE
https://github.com/datova-kancelaria/nkod-dokumentacia


[LinkedPipes ETL]: https://etl.linkedpipes.com "LinkedPipes ETL"
[SPARQL]: https://www.w3.org/TR/sparql11-query/ "SPARQL"
[Ontotext GraphDB Free]: https://www.ontotext.com/products/graphdb/download/ "Ontotext GraphDB Free"
[NKOD-SW]: https://github.com/datova-kancelaria/nkod-software "NKOD Software"
