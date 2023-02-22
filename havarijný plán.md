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

TODO: provozovatel

### 3.1. členov tímov „deploymentu“

TODO: provozovatel

### 3.2. dodávateľov a servisných firiem

TODO: provozovatel

### 3.3. klientov

TODO: provozovatel

#### 3.3.1. Zvolávací strom členov tímu

TODO: provozovatel

#### 3.3.2. Úlohy a zodpovednosti jednotlivých zamestnancov (členov tímov, tvorcov plánov a zamestnancov zodpovedných za ich udržiavanie)

TODO: provozovatel

#### 3.3.3. Postupy (ako reagovať a zvládať incidenty)

TODO: provozovatel

#### 3.3.4. Zdroje (určenie kľúčových zamestnancov, HW, Cloudu, SW, lokalít a vybavenia)

TODO: provozovatel

#### 3.3.5. Opis plánovaného priebehu procesu obnovy

TODO: provozovatel

#### 3.3.6. Podmienky aktivácie plánov

TODO: provozovatel

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

## 5. Proces tvorby a realizácie (Plan-Do-Check-Act-Improve-Recovery)
TODO: provozovatel

### 5.1. Plan (identifikácia rizík a dopadov, identifikácia hrozieb, analýza informácií)

### 5.2. Do (spracovanie stratégie, tvorba a implementácia plánov obnovy)

### 5.3. Check (testovanie, aktualizácia a audit)

### 5.4. Act (údržba a zlepšovanie)

### 5.5. Improve

### 5.6. Recovery 

