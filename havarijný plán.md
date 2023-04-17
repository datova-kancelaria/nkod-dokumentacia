# POKYNY PRE OBNOVU v PRÍPADE VYPADKU alebo HAVÁRIE (HAVARIJNÝ PLÁN):

## 1. Rozsah platnosti 

Toto je havarijný plán Národního katalogu otevřených dat (NKOD), části projektu OD2.0.

Tento plán vypracuje budoucí provozovatel NKOD a Portálu otevřených dat.

## 2. Definície pojmov a skratiek 

<dl>
  <dt>NKOD</dt>
  <dd>Národní katalog otevřených dat</dd>
  <dt>LKOD</dt>
  <dd>Lokální katalog otevřených dat</dd>
  <dt>RDF</dt>
  <dd>Resource Description Framework</dd>
</dl>

## 3. Kontaktné údaje:

### 3.1. Organizácia zodpovedajúca za nasadenie

Národná agentúra pre sieťové a elektronické služby
Kollárova 8
917 02 Trnava

### 3.2. Dodávateľ

Univerzita Karlova
Matematicko-fyzikální fakulta
Ke Karlovu 3
121 16 Praha 2
Česko

### 3.3. Klient

Ministerstvo investícií, regionálneho rozvoja a informatizácie Slovenskej republiky
Pribinova 25
811 09 Bratislava

#### 3.3.1. Zvolávací strom členov tímu


#### 3.3.2. Úlohy a zodpovednosti jednotlivých zamestnancov (členov tímov, tvorcov plánov a zamestnancov zodpovedných za ich udržiavanie)


#### 3.3.3. Postupy (ako reagovať a zvládať incidenty)


#### 3.3.4. Zdroje (určenie kľúčových zamestnancov, HW, Cloudu, SW, lokalít a vybavenia)


#### 3.3.5. Opis plánovaného priebehu procesu obnovy


#### 3.3.6. Podmienky aktivácie plánov


## 4. Kritické (hlavné) procesy

### 4.1. Stanovenie hodnôt RPO (Recovery Point Objective)
Na NKOD se neaplikuje.
Databáze NKOD je tvořena každým během harvestování znovu. Její obsah se tak ani nezálohuje.
V případě, že k výpadku dojde mimo dobu harvestování, o žádná data nepřijdeme.
Pokud k výpadku dojde v době průběhu harvestování, je třeba harvestování po obnovení spustit znovu.

### 4.2. Stanovenie hodnôt RTO (Recovery Time Objective)
Stačí 24h, jelikož NKOD není kritickým systémem.

### 4.3. Stratégia obnovy (hot site, Warm site, cold site)
NKOD je provozován v komerčním cloudu OCI.
Žádné nadstavbové záložní řešení není vyžadováno.