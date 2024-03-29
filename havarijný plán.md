# POKYNY PRE OBNOVU v PRÍPADE VYPADKU alebo HAVÁRIE (HAVARIJNÝ PLÁN):

## 1. Rozsah platnosti 

Toto je havarijný plán Národního katalogu otevřených dat (NKOD), časti projektu OD2.0.

Projekt NKOD je prvou časťou webového portálu pre Centrálny portál otvorených dát.
Detailná špecifikácia tohto havarijného plánu bude vypracovaná až v spojitosti s projektom - Webový portál pre NKOD, ktorý je v súčasnosti predmetom verejného obstarávania.
Dovtedy beži projekt NKOD len v testovacej prevádzke.

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

Národná agentúra pre sieťové a elektronické služby<br>
Kollárova 8<br>
917 02 Trnava

### 3.2. Dodávateľ

Univerzita Karlova<br>
Matematicko-fyzikální fakulta<br>
Ke Karlovu 3<br>
121 16 Praha 2<br>
Česko<br>

### 3.3. Klient

Ministerstvo investícií, regionálneho rozvoja a informatizácie Slovenskej republiky<br>
Pribinova 25<br>
811 09 Bratislava<br>

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