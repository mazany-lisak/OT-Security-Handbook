---
title: Czech Cybersecurity Act and Implementing Decrees
subtitle: "Zákon č. 264/2025 Sb. | Vyhláška 408 | Vyhláška 409 | Vyhláška 410"
category: Legislation
version: 1.2.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-06-29
review_cycle: Annual
---

# Účel dokumentu / Purpose

Tento dokument poskytuje inženýrsky orientovaný přehled českého národního rámce kybernetické bezpečnosti z perspektivy architekta průmyslové kybernetické bezpečnosti (OT Security Architect).

Namísto reprodukce právního textu vysvětluje, jak regulatorní rámec ovlivňuje návrh, řízení a provoz průmyslových systémů — zejména prostředí OT/ICS zahrnujících PLC, SCADA, DCS, RTU a průmyslové sítě.

Dokument pokrývá:

* **Zákon č. 264/2025 Sb.** — Zákon o kybernetické bezpečnosti (transpozice NIS2),
* **Vyhláška č. 408/2025 Sb.** — Regulované služby (určení rozsahu),
* **Vyhláška č. 409/2025 Sb.** — Vyšší povinnosti (bezpečnostní opatření),
* **Vyhláška č. 410/2025 Sb.** — Nižší povinnosti (bezpečnostní opatření).

> **Jazyk dokumentu:** Dokument je záměrně psán v angličtině jako primárním jazyce knowledge base, s českými termíny a checklisty tam, kde je to pro praktické použití přínosné.

---

# Navazující dokumenty / Related Documents

| Dokument | Vztah |
|----------|-------|
| [NIS2.md](#) | Směrnice EU, kterou zákon transponuje — číst jako první |
| [IEC62443-Overview.md](#) | Inženýrský implementační rámec pro OT |
| [ISA95.md](#) | Funkční referenční model průmyslové automatizace |

---

# Regulatorní hierarchie

```
[Směrnice NIS2 (EU 2022/2555)]          ← Strategické cíle EU
        │
[Zákon č. 264/2025 Sb.]                 ← Národní právní povinnosti (CZ)
        │
        ├── [Vyhláška 408]              ← Kdo je regulován (rozsah)
        │
        ├── [Vyhláška 409]              ← Jak — Vyšší povinnosti
        │
        └── [Vyhláška 410]              ← Jak — Nižší povinnosti
                │
        [IEC 62443 / ISA-95]            ← Inženýrská implementace
```

Zákon definuje **co** je právně povinné. Vyhlášky upřesňují **v jakém rozsahu**. IEC 62443 říká **jak** to implementovat. Všechny čtyři vrstvy musí být konzistentně provázány.

---

---

# Část 1 — Zákon č. 264/2025 Sb. (Zákon o kybernetické bezpečnosti)

---

## Přehled

Zákon o kybernetické bezpečnosti transponuje směrnici NIS2 do českého práva.

Zákon stanovuje:

* které organizace podléhají regulaci,
* jaké kybernetické povinnosti platí,
* roli NÚKIB (Národní úřad pro kybernetickou a informační bezpečnost),
* rozlišení mezi režimem vyšších a nižších povinností,
* právní základ pro vydávání prováděcích vyhlášek.

Zákon nahrazuje předchozí zákon č. 181/2014 Sb.

> **Dozorový orgán:** NÚKIB je primárním českým orgánem dohledu v oblasti kybernetické bezpečnosti. Vydává metodická doporučení, provádí inspekce, přijímá hlášení incidentů a spolupracuje s ENISA a institucemi EU. NÚKIB rovněž vydává závazná opatření a může ukládat sankce.

---

## Perspektiva architekta

Zákon není technická norma. Stanovuje:

* očekávání v oblasti governance (odpovědnost managementu),
* povinné organizační závazky,
* požadavky na řízení rizik,
* struktury odpovědnosti (accountability),
* dohledové a sankční procesy.

Technická implementace je zajišťována prostřednictvím architektury, provozních procesů a bezpečnostních opatření — informovaných vyhláškou 409 nebo 410 a normami IEC 62443.

**Klíčový princip pro architekty:** Zákon nestanovuje, jak má vypadat síť, jaké produkty použít ani jaká topologie je povinná. Určuje výsledky (outcomes), nikoli prostředky. Architekt má svobodu volby implementace — nese však odpovědnost za prokázání, že zvolená implementace požadované výsledky dosahuje.

---

## Governance — odpovědnost managementu

Zákon činí kybernetickou bezpečnost explicitní odpovědností managementu, nikoli pouze IT nebo OT oddělení.

### Povinnosti managementu

* Schvalování politik kybernetické bezpečnosti.
* Zajišťování přiměřených zdrojů (finančních, lidských, technologických).
* Dohled nad procesy řízení rizik.
* Podpora neustálého zlepšování.
* Zajišťování regulatorní shody.
* Odpovědnost za sankce při porušení zákona.

### Architektonické implikace pro OT

V průmyslových projektech to v praxi znamená:

* **Ředitel závodu / výroby (Plant Manager)** musí být formálně zapojen do schvalování architektonických rozhodnutí a bezpečnostních kompromisů — nemůže delegovat celou odpovědnost na CISO nebo bezpečnostního architekta.
* **Provozní management** musí rozumět implikacím bezpečnostních rozhodnutí pro výrobu (dostupnost, bezpečnost procesu, výkon).
* **CISO** musí mít přehled o OT rizicích, nejen o IT prostředí.
* Bezpečnostní architekt musí být schopen vysvětlit technická rozhodnutí managementu v business jazyce.

### Praktický problém v OT projektech

Z praxe: Nejčastějším selháním governance v OT prostředí je situace, kdy bezpečnostní tým navrhne opatření, ale nedostane zdroje ani mandát k implementaci, protože výrobní management nebyl zapojen od začátku. Zákon toto explicitně adresuje tím, že management odpovídá osobně.

---

## Řízení rizik

Zákon vyžaduje průběžné řízení kybernetických rizik — nikoli jednorázové hodnocení.

### Co musí každé architektonické rozhodnutí obsahovat

* Dokumentované předpoklady (co se předpokládá o hrozbách, prostředí, schopnostech útočníka).
* Posouzení rizik (risk assessment) vztahující se k danému rozhodnutí.
* Odůvodnění rozhodnutí (decision rationale) — proč bylo zvoleno toto řešení a ne jiné.
* Sledovatelnost (traceability) od zákonného požadavku přes architektonický prvek k provoznímu procesu.

### Kontinuita rizikového managementu

Risk assessment není projekt — je to průběžný proces. Musí být aktualizován při:

* změnách v OT prostředí (nová zařízení, nové integrace, nové verze SW),
* změnách v hrozebném prostředí (nové CVE, nové vektory útoku),
* bezpečnostních incidentech (i u jiných organizací ve stejném sektoru),
* organizačních změnách (noví dodavatelé, nové smlouvy na vzdálený přístup),
* periodicky — minimálně jednou ročně pro vyšší povinnosti.

---

## Sankce a vynucování

Zákon zavádí sankční mechanismy, které jsou pro OT organizace relevantní.

### Finanční sankce

| Režim | Maximální pokuta |
|-------|-----------------|
| Vyšší povinnosti | 250 000 000 Kč nebo 2 % celkového ročního obratu (vyšší z hodnot) |
| Nižší povinnosti | 175 000 000 Kč nebo 1,4 % celkového ročního obratu (vyšší z hodnot) |

### Nefinanční sankce

* Dočasný zákaz výkonu funkce pro vedoucí pracovníky odpovědné za závažná porušení.
* Závazná opatření NÚKIB ukládající konkrétní bezpečnostní kroky s lhůtou.
* Povinné informování zákazníků nebo veřejnosti o bezpečnostním incidentu.
* Zveřejnění informace o porušení (naming and shaming).

> **Praktická implikace:** Výše sankcí je dostatečná pro to, aby kybernetická bezpečnost OT byla finančně odůvodnitelnou investicí i z čistě ekonomické perspektivy. Náklady na incident a sankci obvykle mnohonásobně převyšují náklady na preventivní bezpečnostní architekturu.

---

## Hlášení incidentů NÚKIB

Zákon stanovuje závazné lhůty pro hlášení kybernetických incidentů.

### Lhůty hlášení

| Fáze | Lhůta | Obsah |
|------|-------|-------|
| Včasné varování (early warning) | Do 24 hodin od zjištění | Základní informace: co se stalo, rozsah dopadu |
| Plné hlášení incidentu | Do 72 hodin | Podrobný popis, postižené systémy, přijatá opatření |
| Průběžná zpráva | Na vyžádání NÚKIB | Aktuální stav řešení |
| Závěrečná zpráva | Do 1 měsíce od plného hlášení | Analýza příčin, přijatá opatření, poučení |

### Co je "významný incident"

Incident je hlásitelný, pokud způsobil nebo mohl způsobit:

* závažné narušení provozní kontinuity regulované služby,
* finanční ztráty přesahující stanovené prahy,
* dopad na jiné fyzické nebo právnické osoby.

V OT kontextu: výpadek výroby, narušení řídicího systému, neautorizované změny v PLC programu, přerušení dodávky energie nebo vody — to vše jsou kandidáti na hlásitelný incident.

### Architektonické předpoklady pro splnění lhůt

Lhůta 24 hodin na včasné varování je v OT prostředí velmi těsná, pokud není detekční schopnost navržena předem.

Povinné architektonické prvky:

* **Centralizované logování** s chráněnou integritou (WORM storage nebo ekvivalent).
* **NTP synchronizace** na všech OT zařízeních — bez ní jsou logy forenzně nepoužitelné.
* **OT monitoring / IDS** s alertingem v reálném čase — operátor nesmí být prvním, kdo zjistí incident vizuálně na HMI.
* **Definované komunikační kanály do NÚKIB** mimo OT síť — v případě incidentu nesmí být komunikační cesta sama postižena.
* **RACI matice pro incident response** — kdo co dělá v prvních 24 hodinách musí být definováno a nacvičeno předem.
* **Kontaktní místo pro NÚKIB** — jmenovaný bezpečnostní kontakt s dostupností 24/7.

> Schopnost hlášení incidentů musí být navržena jako součást systémové architektury — nelze ji improvizovat v průběhu reálného incidentu.

---

## Interakce s NÚKIB — praktický průvodce pro architekty

### Registrace

Organizace, která zjistí, že je poskytovatelem regulované služby, má povinnost se registrovat u NÚKIB. Registrace předchází implementaci opatření — a v praxi je také příležitostí ke konzultaci s NÚKIB ohledně výkladu rozsahu a požadavků.

### Inspekce NÚKIB

NÚKIB může provádět:

* **Plánované inspekce** — oznámené, s předem definovaným rozsahem.
* **Neplánované inspekce** — typicky po incidentu nebo na základě podnětů.
* **Vzdálené přezkoumání dokumentace** — bez fyzické přítomnosti.

Co inspektoři NÚKIB typicky prověřují:

* Existence a aktuálnost dokumentace (politiky, inventář, risk assessment).
* Soulad skutečné architektury s dokumentací.
* Funkčnost procesů (incident response, change management, patch management).
* Záznamy o provedených opatřeních (logy změn, záznamy školení, záznamy testování).
* Smluvní zabezpečení dodavatelů.

### Doporučení pro přípravu na inspekci

* Dokumentace musí odrážet skutečný stav — nikoli ideální stav.
* Architektonické diagramy musí být aktuální (tzn. verze z doby inspekce, ne z doby návrhu).
* Záznamy o incidentech, změnách a testech musí být dohledatelné.
* Pracovníci musí být schopni vysvětlit bezpečnostní procesy vlastními slovy — nestačí mít dokumenty v šuplíku.

---

## Časté omyly při interpretaci zákona

Zákon **nepředepisuje**:

* Konkrétní produkty firewallu nebo dodavatele.
* Povinné SIEM platformy.
* Pevné síťové topologie.
* Konkrétní autentizační technologie.
* Povinné použití specifických norem (ač IEC 62443 je de facto standardem).

Zákon stanovuje právní povinnosti — inženýrská rozhodnutí zůstávají odpovědností organizace. Architekt rozhoduje o implementaci, zákon definuje výsledky, kterých musí být dosaženo.

---

---

# Část 2 — Vyhláška č. 408/2025 Sb. (Regulované služby)

---

## Účel vyhlášky

Vyhláška 408 určuje, **zda organizace spadá do rozsahu** zákona o kybernetické bezpečnosti a který regulatorní režim se na ni vztahuje.

Je povinným výchozím bodem každého compliance assessment. Bez dokončení tohoto kroku je diskuse o bezpečnostních opatřeních předčasná — může vést k zbytečné práci nebo naopak k opominutí povinností.

---

## Postup posouzení rozsahu

### Krok za krokem

```
KROK 1: Identifikace organizace a jejích obchodních služeb
         │
KROK 2: Porovnání služeb se seznamem regulovaných služeb
         (příloha k vyhlášce 408)
         │
KROK 3: Posouzení kritérií významnosti
         (velikost, povaha, sektorové prahy)
         │
KROK 4: Identifikace VŠECH regulovaných služeb organizace
         (ne pouze primární služby)
         │
KROK 5: Určení aplikovatelného režimu
         (vyšší nebo nižší povinnosti)
         │
KROK 6: Dokumentace posouzení a uchování jako součást governance
         │
KROK 7: Přechod na vyhlášku 409 nebo 410
         │
KROK 8: Zahájení návrhu bezpečnostní architektury
```

Toto posouzení musí být dokumentováno a uchováno jako součást projektové dokumentace.

---

## Regulované sektory s OT/ICS relevancí

### Sektor energetiky

Energetika je sektorem s nejvyšší pravděpodobností zařazení do vyšších povinností.

| Podsegment | Typické OT systémy | Poznámka |
|------------|-------------------|----------|
| Výroba elektřiny | DCS, SCADA, PLC turbín a kotlů, EMS | Takřka jistě regulováno |
| Přenos elektřiny (ČEPS) | SCADA, EMS, RTU, IEC 61850 | Kritická infrastruktura |
| Distribuce elektřiny | DMS, SCADA, AMI, RTU | Závisí na velikosti distribuční soustavy |
| Obchodní operace s plynem | SCADA, TEGAS, RTU, měřicí stanice | Závisí na rozsahu |
| Distribuční soustava plynu | SCADA, regulační stanice, PLC | Závisí na velikosti |
| Jaderná energetika | I&C systémy, Safety Systems, SCADA | Specifická regulace + zákon o KB |
| Výroba tepla (velké CZT) | SCADA, DCS, PLC výměníkových stanic | Závisí na velikosti |

### Sektor dopravy

| Podsegment | Typické OT systémy | Poznámka |
|------------|-------------------|----------|
| Železniční infrastruktura (SŽDC) | Zabezpečovací zařízení, ERTMS, PLC, ETCS | Kritická infrastruktura |
| Letecká doprava (ŘLP) | ATC systémy, RADAR, ATIS | Kritická infrastruktura |
| Provozovatelé letišť | SCADA BMS, FIDS, palubní systémy | Závisí na velikosti letiště |
| Říční a námořní doprava | VTS systémy, SCADA plavebních komor | Specifická kritéria |
| Silniční infrastruktura | SCADA tunelů, ITS, dopravní signalizace | Závisí na rozsahu |

### Sektor voda

| Podsegment | Typické OT systémy | Poznámka |
|------------|-------------------|----------|
| Zásobování pitnou vodou | SCADA, PLC čerpacích stanic, úpraven vody | Závisí na počtu zásobovaných osob |
| Odpadní vody (ČOV) | SCADA, PLC čerpacích stanic, aerace | Závisí na kapacitě ČOV |

### Výrobní sektory (příloha II NIS2)

Pozor: Ne každý výrobce je automaticky regulován. Regulace závisí na kategorii výrobku, jak je definována v příloze II NIS2.

| Kategorie výrobku | Typické OT systémy | Pravděpodobnost |
|-------------------|-------------------|-----------------|
| Zdravotnické prostředky | ICS, PLC výrobních linek, SCADA | Možné |
| Počítače a elektronika | ICS výrobních linek | Možné |
| Strojní zařízení | PLC, robotické systémy | Závisí na kategorii |
| Motorová vozidla | Robotika, PLC svařovacích/lakoven linek | Závisí na kategorii |
| Chemické produkty | DCS, SIS, SCADA | Možné |
| Potraviny | SCADA, PLC potravinářských linek | Závisí na velikosti |

### Digitální infrastruktura a ICT

| Podsegment | Relevance pro OT | Poznámka |
|------------|-----------------|----------|
| Datová centra | Facility management (BMS, SCADA chlazení, UPS) | Závisí na velikosti a klasifikaci |
| Poskytovatelé DNS | — | Primárně IT |
| Cloud providéři | — | Primárně IT |

> **Poznámka architekta:** Nikdy nepředpokládejte, že každá průmyslová organizace je regulována. Posouzení musí být evidence-based, ideálně za účasti právního zástupce, specialisty na regulatorní compliance a konzultace s NÚKIB.

---

## Kritéria významnosti

Samotné poskytování regulované služby nemusí postačovat ke vzniku regulatorních povinností. Vyhláška definuje kritéria významnosti:

* Velikost organizace (počet zaměstnanců, roční obrat, roční bilanční suma).
* Povaha a kritičnost regulované činnosti.
* Sektorově specifické prahy (např. počet zásobovaných odběratelů, objem přenesené energie, kapacita zpracování odpadu).
* Přeshraniční dopad.
* Tržní podíl.

Posouzení musí být podloženo dokumentovanými důkazy — nikoli pouze odhadem.

---

## Klíčové pravidlo: eskalace režimu

Každá regulovaná služba má počáteční přiřazení k režimu. Platí však zásadní pravidlo:

> **Pokud alespoň jedna regulovaná služba organizaci zařadí do režimu vyšších povinností, platí tento režim pro celou organizaci.**

Musí být proto posouzeny **všechny** regulované služby organizace, nikoli pouze primární. Toto pravidlo má přímý dopad na návrh architektury — architektura pro celou organizaci musí splňovat požadavky vyššího režimu, i pokud jen jedna z poskytovaných služeb tento režim zakládá.

---

## Typické chyby při posouzení rozsahu

* Předpoklad, že každá průmyslová organizace je regulována.
* Předpoklad, že výroba automaticky zakládá vyšší povinnosti.
* Posouzení pouze primární obchodní služby — opomenutí vedlejších regulovaných služeb.
* Ignorování kritérií významnosti.
* Zahájení architektonického návrhu před dokončením posouzení rozsahu.
* Delegování posouzení rozsahu výhradně na IT oddělení bez zapojení právního a provozního týmu.
* Spoléhání na analogie s jinými organizacemi v sektoru bez vlastního posouzení.

---

## Checklist posouzení rozsahu (Vyhláška 408)

### Česky — pro použití v projektu

**Fáze 1: Identifikace organizace**
- [ ] Identifikoval jsem všechny obchodní aktivity organizace
- [ ] Identifikoval jsem všechny služby, které organizace poskytuje třetím stranám
- [ ] Zahrnul jsem i interní sdílené služby (např. IT/OT podpora pro skupinu)
- [ ] Ověřil jsem, zda organizace neposkytuje regulovanou službu nepřímo (jako subdodavatel)

**Fáze 2: Porovnání se seznamem regulovaných služeb**
- [ ] Porovnal jsem aktivity organizace s přílohou vyhlášky 408
- [ ] Konzultoval jsem výklad hraniční případů s právním zástupcem nebo NÚKIB
- [ ] Zdokumentoval jsem výsledek porovnání s odkazem na konkrétní položky přílohy

**Fáze 3: Posouzení kritérií významnosti**
- [ ] Shromáždil jsem data o velikosti organizace (zaměstnanci, obrat, bilanční suma)
- [ ] Posoudil jsem sektorově specifická kritéria pro každou identifikovanou regulovanou službu
- [ ] Zdokumentoval jsem posouzení kritérií s podpůrnými důkazy

**Fáze 4: Určení režimu**
- [ ] Posoudil jsem VŠECHNY regulované služby organizace (ne pouze primární)
- [ ] Aplikoval jsem pravidlo eskalace (jedna vyšší povinnost → celá organizace)
- [ ] Zdokumentoval jsem výsledné přiřazení k režimu (vyšší / nižší povinnosti)

**Fáze 5: Governance**
- [ ] Posouzení rozsahu bylo schváleno managementem
- [ ] Dokumentace posouzení je uložena jako součást projektové dokumentace
- [ ] Je nastaven proces pro opětovné posouzení při změnách v organizaci nebo regulaci
- [ ] Byl informován NÚKIB (registrace) pokud je organizace regulována

---

---

# Část 3 — Vyhláška č. 409/2025 Sb. (Vyšší povinnosti)

---

## Účel vyhlášky

Vyhláška 409 definuje bezpečnostní opatření, která musí organizace v režimu **vyšších povinností** implementovat a udržovat.

Specifikuje požadované **bezpečnostní schopnosti** — nikoli produkty ani konfigurace. Implementace musí být proporcionální provoznímu riziku, kritičnosti procesu, expozici hrozbám a obchodním požadavkům.

Většina OT projektů v regulované kritické infrastruktuře bude nakonec spravována touto vyhláškou.

---

## Inženýrská filozofie

Vyhláška 409 je soubor požadovaných bezpečnostních schopností — ne compliance checklist, který se odškrtá.

Každá schopnost musí být:

* implementována proporcionálně k riziku,
* udržována prostřednictvím provozních procesů,
* dokumentována ověřitelným způsobem,
* pravidelně přezkoumávána.

Shoda přirozeně vyplyne z dobře navržené a dobře provozované architektury.

---

## Capability Domain 1: Governance (Řízení)

### Záměr

Etablovat jasnou, dokumentovanou odpovědnost za kybernetickou bezpečnost napříč organizací.

### Požadované prvky

* Definované bezpečnostní role a odpovědnosti — RACI matice zahrnující OT i IT.
* Schválené politiky kybernetické bezpečnosti pokrývající OT prostředí.
* Dohled managementu a pravidelné přezkoumání.
* Proces kontinuálního zlepšování (Plan-Do-Check-Act).
* Kybernetická bezpečnost jako součást projektové governance od prvního dne.

### Praktická implementace v OT

**Jmenování odpovědné osoby pro OT bezpečnost:**
V průmyslovém prostředí se role CISO nebo bezpečnostního manažera musí explicitně vztahovat na OT doménu. Není přípustné, aby CISO pokrýval pouze IT a OT bylo "v odpovědnosti provozu bez formální bezpečnostní role."

**Propojení governance s provozním řízením:**
Bezpečnostní výbory nebo review boards musí zahrnovat zástupce provozu (plant operations), technologů (process engineers) a OT architektů — nejen IT bezpečnostní tým.

**Politiky specifické pro OT:**
Generické IT politiky (acceptable use, email security, cloud usage) nejsou dostatečné. Jsou potřeba politiky adresující OT-specifické scénáře:
* Politika přístupu dodavatelů do OT sítě.
* Politika změnového řízení pro PLC programy a DCS konfigurace.
* Politika správy přenosných médií v OT prostředí.
* Politika vzdáleného přístupu do řídicích systémů.
* Politika aktualizací firmwaru a softwaru v OT.

---

## Capability Domain 2: Správa aktiv (Asset Management)

### Záměr

Vědět, co je třeba chránit, dříve než navrhujeme ochranu.

### Požadované schopnosti

* Inventář aktiv pokrývající všechny OT komponenty.
* Přiřazení vlastníka aktiva (asset owner).
* Klasifikace kritičnosti.
* Sledování životního cyklu.
* Záznamy o verzích firmwaru a softwaru.
* Konfigurační záznamy (configuration baseline).

### OT Asset Inventory — co zahrnout

| Kategorie | Příklady | Kritická metadata |
|-----------|---------|-------------------|
| Řadiče (Controllers) | PLC (Siemens S7, Rockwell, Schneider, ABB, Beckhoff), RTU, PAC | Výrobce, model, HW revize, verze FW, verze OS, IP adresa, komunikační protokoly |
| Distribuované řídicí systémy | DCS (ABB 800xA, Honeywell Experion, Emerson DeltaV, Yokogawa CENTUM) | Verze DCS softwaru, konfigurace I/O, komunikační karty |
| SCADA servery a klienti | Wonderware/AVEVA, Ignition, iFIX, WinCC, ClearSCADA | Verze softwaru, OS, databáze, komunikační ovladače |
| HMI | Panelové HMI, PC-based HMI | Verze softwaru, OS, síťové připojení |
| Engineeringové workstations | Laptopy/PC s PLC/DCS engineering software | Privilegovaná aktiva — přísná kontrola |
| Historiani | OSIsoft PI/AVEVA PI, Aspentech, Wonderware Historian | Kritické pro reporting do L4; přístupové politiky |
| Průmyslové síťové prvky | Managed switche (Hirschmann, Moxa, Phoenix Contact), průmyslové routery, firewally | Verze FW, konfigurace, SNMP community strings |
| Safety systémy | SIS (Triconex, HIMA, Pilz), ESD, F&G systémy | Samostatná zóna; koordinace s funkční bezpečností |
| Bezdrátová zařízení | WirelessHART, ISA100.11a přístupové body, průmyslové WiFi | Šifrování, autentizace, RF segmentace |
| Analyzátory a smart instruments | HART, Foundation Fieldbus zařízení s diagnostikou | Firmware, komunikační konfigurace |

> **Neznámá aktiva nemohou být zabezpečena, patchována ani obnovena.** Inventář aktiv je základem všech ostatních bezpečnostních schopností.

### Automatizovaná discovery vs. ruční inventář

| Přístup | Výhody | Rizika v OT |
|---------|--------|-------------|
| Pasivní síťová discovery (Nozomi, Claroty, Dragos) | Automatická, průběžná, neinvazivní | Nemusí zachytit offline zařízení nebo HART/Fieldbus |
| Aktivní skenování (Nmap, Tenable.OT) | Rychlé, kompletní | Může destabilizovat legacy OT zařízení — testovat nejdříve v testovacím prostředí |
| Ruční inventarizace | Spolehlivá pro fyzická aktiva | Pracná, zastarávající |
| Kombinace pasivní discovery + ruční ověření | Nejlepší pokrytí | Vyžaduje koordinaci |

---

## Capability Domain 3: Řízení rizik (Risk Management)

### Záměr

Podpořit informovaná architektonická a provozní rozhodnutí prostřednictvím systematické analýzy rizik.

### Požadované schopnosti

* Systematická identifikace rizik (threat modeling pro OT prostředí).
* Hodnocení rizik (pravděpodobnost × dopad).
* Rozhodnutí o léčbě rizik s dokumentovaným odůvodněním.
* Přijetí rizika s podpisem managementu (risk acceptance).
* Pravidelný přezkum rizik.

### Doporučená metodika pro OT

| Krok | Standard / Metoda | Výstup |
|------|------------------|--------|
| Definice systému (SuC) | IEC 62443-3-2 | Scope document |
| Identifikace aktiv | IEC 62443-2-1 | Asset inventory |
| Threat modeling | STRIDE pro OT, ATT&CK for ICS | Threat register |
| Analýza zranitelností | CVE databáze, vendor advisories, IEC 62443-3-2 | Vulnerability register |
| Hodnocení rizik | SLRA (IEC 62443-3-2) | Risk register s SL-T per zóna |
| Léčba rizik | IEC 62443-3-3, compensating controls | Risk treatment plan |
| Akceptace zbytkového rizika | Management sign-off | Signed risk acceptance |
| Pravidelný přezkum | Minimálně ročně nebo při změnách | Updated risk register |

### MITRE ATT&CK for ICS — praktický nástroj

ATT&CK for ICS (https://attack.mitre.org/matrices/ics/) poskytuje strukturovaný katalog technik útoků specifických pro průmyslové řídicí systémy. Doporučené použití:

* Threat modeling na základě reálných technik (Initial Access, Execution, Persistence, Evasion, Discovery, Lateral Movement, Collection, C&C, Inhibit Response Function, Impair Process Control, Impact).
* Mapování existujících detekčních schopností na katalog — identifikace blind spots.
* Podklad pro risk assessment a definici Security Level Targets.

### Funkční bezpečnost a kybernetická bezpečnost — průsečík

Toto je oblast, která se v praxi nejčastěji opomíjí a přitom má nejvyšší možný dopad.

**Klíčové principy:**

* Kybernetický útok na Safety Instrumented System (SIS) může deaktivovat ochrany funkční bezpečnosti.
* Bezpečnostní opatření nesmí narušit schopnost SIS odpovědět na bezpečnostní požadavek (safety demand).
* IEC 62443 a IEC 61511 (funkční bezpečnost procesů) musí být koordinovány.
* Security assessment SIS musí brát v úvahu Safety Integrity Level (SIL) a schopnost systému odolat kybernetickému útoku při zachování safety funkce.
* HAZOP/LOPA pro kritické systémy by měl zahrnovat analýzu kybernetických hrozeb jako samostatnou kategorii initiating events.

**Referenční incident — Triton/TRISIS (2017):**
V roce 2017 útočníci (atribuováni ruské státní skupině TEMP.Veles / Sandworm) kompromitovali Triconex Safety Instrumented System v petrochemickém závodu na Blízkém východě. Cílem bylo deaktivovat SIS ochrany a způsobit fyzickou havárii s potenciálními ztrátami na životech. Incident byl odhalen náhodou — chybou v útočném kódu, která způsobila nouzové vypnutí. Tento incident jako první prokázal, že státní aktéři aktivně útočí na safety systémy s cílem způsobit fyzickou katastrofu.

**Praktická doporučení pro SIS:**
* SIS musí tvořit samostatnou bezpečnostní zónu oddělenou od základní řídicí sítě (BPCS).
* Žádné trvalé síťové propojení BPCS ↔ SIS — pouze jednosměrné datové brány pro monitoring.
* Engineering přístup k SIS musí být fyzicky a logicky oddělen od přístupu k BPCS.
* SIS firmware a konfigurace musí mít vlastní zálohovací a change management proces.
* SIS musí být zahrnuta do OT incident response planu s jasně definovanými eskalačními cestami k safety týmu.

---

## Capability Domain 4: Správa identit a přístupu (IAM)

### Záměr

Zajistit, aby pouze autorizované identity měly přístup k OT aktivům — v rozsahu, který je provozně nezbytný.

### Požadované schopnosti — detailní přehled

**Správa účtů:**
* Unikátní uživatelské účty — žádné sdílené účty (administrator, operator, scada) na SCADA, DCS ani HMI.
* Separované účty pro OT a IT prostředí — OT účty nesmí být totožné s doménovými IT účty.
* Lifecycle management: provisioning (při nástupu / přidělení role), pravidelný přezkum (minimálně ročně), deprovisioning (okamžitě při ukončení pracovního poměru nebo změně role).
* Service accounts (servisní účty pro komunikaci systémů) musí být inventarizovány, přiřazeny vlastníkovi a pravidelně přezkoumávány.

**Řízení přístupu:**
* Role-based access control (RBAC) na úrovni SCADA a DCS — operátor, supervizor, inženýr, administrátor musí mít jasně definovaná oprávnění.
* Princip nejmenšího oprávnění (least privilege) — přístup pouze ke zdrojům nezbytným pro výkon role.
* Kde je to technicky možné: omezení přístupu na úrovni PLC function blocks (čtení vs. zápis, jednotlivé programové bloky).
* Time-limited privileged access — elevovaná oprávnění pouze po dobu potřebnou pro konkrétní úkon.

**Autentizace:**
* Multi-factor authentication (MFA) pro vzdálený přístup a privilegované operace bez výjimky.
* Silné heslo nebo passphrase politiky pro lokální účty na OT zařízeních — kde technologie umožňuje.
* Biometrická nebo čipová autentizace pro fyzický přístup do řídicích místností.

**Privileged Access Management (PAM):**
* PAM řešení pro engineering workstations a PLC engineering software.
* Session recording pro všechny privilegované relace.
* Approval workflow pro přístup k nejkritičtějším systémům (SIS, napájecí SCADA).
* Just-in-time (JIT) přístup jako alternativa k trvalým privilegovaným účtům.

**Přístup dodavatelů — detailní požadavky:**

| Požadavek | Implementace |
|-----------|-------------|
| Časově omezený přístup | Vydání přihlašovacích údajů s automatickým vypršením (max. na dobu servisního úkonu + buffer) |
| MFA | Povinné pro veškerý vzdálený přístup bez výjimky |
| Jump server / Bastion host | Veškerý vzdálený přístup výhradně přes jump server v průmyslové DMZ |
| Session recording | Nahrávka celé relace (video + keystroke logging) uložená mimo OT síť |
| Protokol akcí | Potvrzení dodavatele o provedených akcích + automatický log systémových událostí |
| Přezkum přístupu | Po skončení servisního úkonu okamžité zrušení přístupu |
| Vendor identity verification | Ověření identity technika před udělením přístupu |

---

## Capability Domain 5: Síťová bezpečnost a segmentace

### Záměr

Minimalizovat útočnou plochu prostřednictvím kontrolovaných komunikačních cest a pevně definovaných bezpečnostních hranic.

Síťová segmentace je **jedním z nejkritičtějších architektonických prvků** v OT prostředí.

### Referenční architektura — zónový model

```
╔══════════════════════════════════════════════════════════════════╗
║  ZÓNA: Enterprise IT (Úroveň 4/5)                               ║
║  ERP, SCM, Finance, HR, korporátní IT                           ║
╠══════════════════════════════════════════════════════════════════╣
║  FIREWALL (stateful, deny-by-default)                            ║
╠══════════════════════════════════════════════════════════════════╣
║  ZÓNA: Průmyslová DMZ (Úroveň 3.5)                              ║
║  Historian replica (read-only), Jump server, File transfer,      ║
║  Patch management server, Antivirus update server                ║
╠══════════════════════════════════════════════════════════════════╣
║  FIREWALL (allowlist rules pouze, žádné default-allow)           ║
╠══════════════════════════════════════════════════════════════════╣
║  ZÓNA: Supervizorní / MOM (Úroveň 3)                            ║
║  MES, Historian (production), CMMS, Quality, výrobní reporting   ║
╠══════════════════════════════════════════════════════════════════╣
║  FIREWALL / Průmyslový přepínač s ACL                            ║
╠══════════════════════════════════════════════════════════════════╣
║  ZÓNA: Řídicí síť (Úroveň 2)                                    ║
║  SCADA servery, DCS workstations, HMI servery, Engineering WS,   ║
║  Alarm management, OPC servery                                   ║
╠══════════════════════════════════════════════════════════════════╣
║  Průmyslový firewall / L2 segmentace                             ║
╠══════════════════════════════════════════════════════════════════╣
║  ZÓNA: Field / Procesní síť (Úroveň 0/1)                        ║
║  PLC, RTU, DCS řadiče, Drives s Ethernet rozhraním              ║
╠══════════════════════════════════════════════════════════════════╣
║  [Fyzicky separovaná] ZÓNA: Safety (SIS, ESD, F&G)              ║
║  Pouze jednosměrné data diody pro monitoring                     ║
╚══════════════════════════════════════════════════════════════════╝
```

### Klíčové architektonické principy

**Industrial DMZ — povinná součást:**
* Žádné přímé propojení IT ↔ OT bez DMZ buffer zóny.
* DMZ obsahuje pouze systémy, které skutečně potřebují komunikovat oběma směry (historian replica, jump server, file transfer, patch server).
* DMZ je chráněna firewally z obou stran s odlišnými pravidly.

**Unidirektionální brány (Data Diodes):**
* Použití tam, kde OT → IT datový tok nevyžaduje zpětný kanál.
* Příklady: přenos dat z historian do enterprise reporting, přenos log souborů mimo OT síť, přenos telemetrických dat do cloud monitoringu.
* Hardwarové data diody (Waterfall Security, Owl Cyber Defense, Advenica) poskytují fyzicky garantovanou jednosměrnost.

**Firewallová politika — allowlist přístup:**
* Deny-by-default na všech hranicích zón.
* Explicitní povolení pouze pro nezbytné komunikační toky (zdrojová IP, cílová IP, port, protokol, směr).
* Každé firewall pravidlo musí mít dokumentovaný provozní důvod a vlastníka.
* Pravidelný přezkum firewall pravidel (minimálně ročně) — odstranění zastaralých pravidel.
* Logování všech blokovaných spojení — anomálie v deny logu jsou cenným detekčním signálem.

**VLAN segmentace:**
* VLAN segmentace uvnitř OT sítě podle bezpečnostních zón.
* Každá VLAN musí mít definovaná ACL pravidla — ne pouze L2 separace.
* Managementová VLAN pro síťové prvky musí být oddělena od procesní VLAN.

**Protokoly a porty:**
* Každý otevřený port a protokol musí být zdokumentován a odůvodněn.
* Průmyslové protokoly (Modbus TCP, EtherNet/IP, PROFINET, DNP3, IEC 61850) nesmí přecházet přes hranice zón bez explicitního conduit s content inspection.
* OPC DA (DCOM) nesmí být povolen přes firewall — nahradit OPC UA s security profilem.

**Fyzická separace:**
* Kritické řídicí sítě musí mít fyzicky separovanou kabeláž od korporátní infrastruktury.
* Žádné sdílené switch infrastruktury pro OT a IT provoz.
* Bezdrátový přístup do OT sítě vyžaduje zvláštní posouzení — pokud je povolen, musí být v separované bezdrátové zóně s WPA3 Enterprise a RADIUS autentizací.

### Nejčastější síťové architektonické chyby v OT

1. **Přímé RDP/VNC z IT do OT** — bez jump serveru, bez MFA, bez záznamu.
2. **Sdílený firewall pro IT a OT** — jeden kompromitovaný firewall admin = přístup do obou prostředí.
3. **OPC DA/DCOM přes firewall** — neauditovatelné, dynamické porty, bezpečnostní noční můra.
4. **Sdílená infrastruktura** — OT a IT na stejných switchích, pouze VLAN separace bez ACL.
5. **Zapomenuté modemové linky / 3G/4G routery** — historické vzdálené přístupy dodavatelů bez evidence.
6. **Překrývající se IP adresní prostory** — OT a IT ve stejném subnetu, fakticky plochá síť.
7. **Absence monitoringu na hranici zón** — firewall logy nejsou analyzovány.

---

## Capability Domain 6: Kryptografie

### Záměr

Chránit citlivé komunikace a autentizační mechanismy prostřednictvím moderní kryptografie.

### Požadované schopnosti

* TLS (minimálně 1.2, preferovaně 1.3) pro všechny síťové komunikace, kde OT zařízení tuto funkci podporuje.
* Správa certifikátů a jejich životního cyklu — PKI infrastruktura nebo managed CA; přehled o datech expirace.
* Bezpečné uložení kryptografických klíčů (HSM nebo ekvivalent tam, kde je to praktické).
* Moderní kryptografické algoritmy — zamezit použití zastaralých protokolů a algoritmů.

### Zakázané / zastaralé protokoly a algoritmy

| Protokol / Algoritmus | Status | Náhrada |
|-----------------------|--------|---------|
| SSLv2, SSLv3 | Zakázat | TLS 1.2 / 1.3 |
| TLS 1.0, TLS 1.1 | Zakázat | TLS 1.2 / 1.3 |
| MD5 pro podepisování | Zakázat | SHA-256 nebo vyšší |
| SHA-1 pro podepisování certifikátů | Zakázat | SHA-256 nebo vyšší |
| DES, 3DES | Zakázat | AES-128 nebo AES-256 |
| RSA < 2048 bitů | Zakázat | RSA 2048+ nebo ECDSA |

### OT specifická omezení kryptografie

Legacy OT zařízení (starší PLC, RTU, průmyslové switche) nemusí podporovat moderní kryptografii. Tato situace musí být řešena prostřednictvím kompenzačních kontrol:

* Síťová izolace zařízení bez kryptografické podpory (zahrnutí do nejrestriktivnější zóny).
* Šifrování na vyšší vrstvě sítě (VPN gateway terminující před legacy zařízením).
* Dokumentace, odůvodnění a formální akceptace zbytkového rizika managementem.
* Plán náhrady legacy zařízení v rámci modernizačního plánu.

---

## Capability Domain 7: Logování a monitoring

### Záměr

Poskytnout provozní viditelnost (operational visibility) a schopnost detekce a vyšetřování incidentů.

### Požadované schopnosti — detailní přehled

**Centralizované logování:**
* Sběr logů ze všech OT aktiv schopných generovat logy (SCADA, DCS, HMI, engineering WS, průmyslové firewally, switche).
* Centrální log management mimo OT produkční síť (v DMZ nebo dedikovaném log segmentu).
* Ochrana integrity logů (WORM storage, kryptografické podepisování log záznamů).
* Retence logů minimálně v rozsahu požadovaném vyhláškou (typicky 1–3 roky pro bezpečnostní záznamy).

**NTP synchronizace — kritický požadavek:**
* Všechna OT zařízení musí mít synchronizovaný čas.
* Bez NTP synchronizace jsou logy pro forenzní analýzu nepoužitelné (události nelze korelovat napříč systémy).
* Doporučená architektura: NTP server v průmyslové DMZ synchronizovaný z důvěryhodného externího zdroje; OT zařízení synchronizují z tohoto interního serveru (ne přímo z internetu).
* GPS-based NTP je alternativa pro izolovaná prostředí.
* Monitorovat NTP stratum a upozorňovat na desynchronizaci.

**OT-specifický monitoring:**
* Pasivní síťový monitoring průmyslových protokolů — nástroje jako Claroty, Nozomi Networks, Dragos Platform, Microsoft Defender for IoT.
* Tyto nástroje monitorují Modbus, DNP3, IEC 61850, PROFINET, EtherNet/IP, OPC UA provoz bez aktivního vkládání paketů.
* Anomaly detection: neobvyklé komunikační vzory, nové devices v síti, neobvyklé Modbus function codes, neautorizované PLC write operace.
* Alerting pro specifické OT události: neautorizované změny PLC konfigurace, program download/upload, Force I/O, přihlášení k engineering software mimo maintenance window.

**Integrace se SIEM (pokud implementovaný):**
* OT logy musí být parsovány s pochopením průmyslových protokolů — generické SIEM parsery nejsou dostatečné.
* Pravidla korelace musí zohledňovat OT provozní kontext (maintenance windows, batch cycles, plánované odstávky).
* Výstrahy z OT prostředí musí být zpracovány týmem s OT znalostí — IT SOC bez OT kontextu generuje příliš mnoho false positives.

**Co logovat v OT prostředí:**

| Zdroj | Co logovat | Proč |
|-------|-----------|------|
| Firewally na hranicích zón | Všechna povolená i blokovaná spojení | Detekce anomálií, forenzní analýza |
| SCADA server | Přihlášení/odhlášení, změny konfigurace, alarmy | Audit trail operátorských akcí |
| DCS / PLC engineering SW | Download/upload programů, Force I/O, konfigurace | Detekce neautorizovaných změn |
| Jump server | Veškerá aktivita vzdálených relací | Auditovatelnost přístupu dodavatelů |
| Active Directory / LDAP pro OT | Přihlášení, změny skupin, selhání autentizace | IAM monitoring |
| Průmyslové switche | Změny konfigurace, port security violations, spanning tree události | Síťové anomálie |
| OT IDS/NDR | Anomálie protokolů, nová zařízení, behaviorální odchylky | OT-specifická detekce |

---

## Capability Domain 8: Správa zranitelností (Vulnerability Management)

### Záměr

Identifikovat a řešit zranitelnosti způsobem přiměřeným OT omezením — aniž by byla ohrožena provozní stabilita.

### Specifika patch managementu v OT

OT patch management se zásadně liší od IT patch managementu:

| Dimenze | IT přístup | OT přístup |
|---------|-----------|-----------|
| Frekvence patchování | Měsíčně (Patch Tuesday) | Dle maintenance windows (měsíčně až ročně) |
| Testování před nasazením | Testovací prostředí | Nutné testovací/staging OT prostředí |
| Vliv na dostupnost | Krátký restart přijatelný | Výrobní zastávka musí být koordinována s plánem výroby |
| Schválení | IT change management | OT change management + souhlas provozu |
| Dodavatelská podpora | Standardní update kanály | Vendor approval pro patch (PLC FW, DCS update) |

### Zdroje informací o zranitelnostech OT

* **ICS-CERT Advisories** (CISA) — https://www.cisa.gov/ics-advisories
* **Vendor Security Advisories** — Siemens ProductCERT, Rockwell Security, Schneider Electric, ABB...
* **NVD (National Vulnerability Database)** — https://nvd.nist.gov
* **NÚKIB Varování** — varování vydávaná NÚKIB pro český trh
* **Dragos Year in Review / Claroty Research** — sektorově specifické hrozby

### Kompenzační opatření pro systémy bez patchů

Systémy, které nelze patchovat (end-of-life PLC, legacy SCADA na nepodporovaných OS), musí být ošetřeny kompenzačními opatřeními:

* Síťová izolace (přísné firewall pravidla, žádné zbytečné komunikační toky).
* Enhanced monitoring (specifická pravidla detekce pro zranitelné systémy).
* Omezení přístupu (přístup pouze pro nezbytné osoby, žádné vzdálené připojení).
* Application whitelisting (pokud OS podporuje — AppLocker, podobné nástroje).
* Fyzická bezpečnost (přístup pouze pro autorizované osoby do fyzické blízkosti).
* Formální dokumentace, odůvodnění a podpis akceptace rizika managementem.
* Plán náhrady nebo modernizace s definovaným termínem.

---

## Capability Domain 9: Zálohy a obnova (Backup and Recovery)

### Záměr

Zajistit schopnost obnovy OT systémů po kybernetickém incidentu v definovaném čase — nikoli pouze existenci zálohy.

### Co zálohovat v OT prostředí

| OT komponenta | Co zálohovat | Frekvence |
|---------------|-------------|-----------|
| PLC (Siemens, Rockwell, Schneider...) | Program (ladder, FBD, SCL), konfigurace HW, IP adresy | Po každé změně + pravidelně |
| DCS (ABB, Honeywell, Emerson...) | Konfigurace controllerů, grafické obrazovky, alarm listy, historian tagy | Po každé změně + pravidelně |
| SCADA (AVEVA, Ignition, WinCC...) | Projekt, databáze tagů, grafiky, skripty, runtime konfigurace | Denně (databáze) + po změnách (projekt) |
| Engineering workstations | Celý obraz disku včetně engineering software a licencí | Měsíčně + po změnách |
| Průmyslové firewally | Konfigurace (running config + startup config) | Po každé změně |
| Průmyslové switche | Konfigurace | Po každé změně |
| Historian | Databáze procesu | Denně |
| Jump server | Konfigurace, záznamy o přístupu | Denně |

### Klíčové požadavky na zálohy

**Offline / air-gapped zálohy:**
* Kritické konfigurace (PLC programy, DCS konfigurace) musí existovat v offline kopii fyzicky uložené mimo síť.
* Offline záloha chrání před ransomware, který šifruje i síťově dostupné zálohy.
* Rotace offline médií — uchovávat více historických verzí.

**Testování obnovy — povinné:**
* Záloha bez ověřené obnovy není záchrannou schopností.
* Plán testování obnovy musí být dokumentován a prováděn pravidelně.
* Testování v reálných podmínkách (nebo co nejpodobnějším testovacím prostředí) — nikoli pouze ověření čitelnosti zálohy.
* Záznamy o testování obnovy musí být uchovány jako součást dokumentace.

**RTO a RPO pro OT:**
* Recovery Time Objective (RTO) — maximální přípustná doba obnovy pro každý kritický řídicí systém.
* Recovery Point Objective (RPO) — maximální přípustná ztráta dat (stáří zálohy).
* RTO a RPO musí být definovány managementem s vědomím provozních dopadů výpadku.
* Architektura zálohování musí být navržena tak, aby RTO bylo dosažitelné (dostatečné zálohy, dokumentovaný postup obnovy, vyškolený tým).

**Degradovaný provoz:**
* Definovat a zdokumentovat postup pro "degraded mode operation" — co dělá operátor, když SCADA nebo DCS není dostupný.
* Papírové záznamy, záložní indikátory, manuální ovládání ventilů — vše musí být zdokumentováno a nacvičeno.
* Výroba musí vědět, jak pokračovat v bezpečném provozu bez automatizace, byť v omezené kapacitě.

---

## Capability Domain 10: Řízení incidentů (Incident Management)

### Záměr

Detekovat, reagovat a zotavit se z kybernetických incidentů strukturovaným způsobem — s minimálním dopadem na bezpečnost procesu a provozní kontinuitu.

### ICS Incident Response Plan — klíčové rozdíly od IT IR

| Dimenze | IT Incident Response | OT / ICS Incident Response |
|---------|---------------------|---------------------------|
| Primární priorita | Ochrana dat a systémů | Bezpečnost osob, pak bezpečnost procesu, pak systémy |
| Izolace systémů | Rychlá izolace standardní | Izolace musí být koordinována s bezpečností procesu — PLC nelze jednoduše odpojit |
| Forenzní sběr | Standardní IT forenzní nástroje | Specializované OT forenzní nástroje; nesmí narušit provoz |
| Komunikace | IT, management, legal | Provozní tým, safety team, NÚKIB, regulátor sektoru |
| Recovery | Obnova ze zálohy relativně rychlá | Obnova PLC/DCS konfigurace vyžaduje koordinaci s provozem a někdy vendor support |

### Fáze ICS Incident Response

**1. Příprava (Preparation)**
* ICS-specifický IR plán zdokumentován a schválen.
* Kontakty: interní (provozní tým, safety, management), NÚKIB (kontaktní místo pro hlášení), vendor emergency contacts (Siemens, Rockwell, Honeywell...).
* Forenzní nástroje připraveny a otestovány (network capture, log collection, memory dumps).
* Tabletop exercises a simulace — minimálně jednou ročně.

**2. Detekce a analýza (Detection & Analysis)**
* Zpracování alertů z OT IDS, SCADA alarmů, operátorských hlášení.
* Klasifikace incidentu: kybernetický incident nebo provozní porucha?
* Posouzení dopadu na: bezpečnost procesu (safety), výrobu, dostupnost OT systémů.
* Rozhodnutí o eskalaci — kdy přejít na krizový management?
* Počáteční lhůta: do 24 hodin musí být splněno včasné varování NÚKIB.

**3. Omezení dopadu (Containment)**
* Izolace postižených systémů — **koordinovat se safety týmem** před jakoukoliv izolací v OT.
* Zachování provozní bezpečnosti má absolutní prioritu před rychlou izolací.
* Aktivace degradovaného provozu (manual operation) pokud je nutná izolace řídicích systémů.
* Zabránění šíření: blokování komunikačních cest, izolace segmentů sítě.

**4. Eradikace a obnova (Eradication & Recovery)**
* Identifikace a odstranění příčiny incidentu (malware, zákeřná konfigurace, kompromitovaný účet).
* Obnova ze zálohy — ověření integrity zálohy před nasazením.
* Ověření integrity PLC programů a DCS konfigurace před restartem výroby.
* Postupný restart výroby za přítomnosti safety a provozního týmu.
* Zvýšený monitoring po obnově.

**5. Poučení (Lessons Learned)**
* Post-incident review do 2 týdnů od uzavření incidentu.
* Analýza příčin (root cause analysis).
* Identifikace mezer v architektuře, procesech, detection capability.
* Aktualizace risk registru, IR planu, bezpečnostní architektury.
* Zpětná vazba do risk management procesu.

---

## Capability Domain 11: Bezpečnost dodavatelského řetězce (Supply Chain Security)

### Záměr

Řídit kybernetická rizika vyplývající z závislosti průmyslových systémů na externích dodavatelích, systémových integrátorech a poskytovatelích vzdáleného servisu.

### Kategorie dodavatelů v OT prostředí

| Kategorie | Příklady | Typická rizika |
|-----------|---------|---------------|
| Výrobci OT zařízení | Siemens, Rockwell, ABB, Schneider, Honeywell | Firmware backdoors, end-of-life bez patchů, zranitelné protokoly |
| Systémoví integrátoři | SI firmy provádějící engineering a instalaci | Přístup k engineering WS, import malware přes USB/laptop |
| Poskytovatelé vzdáleného servisu | Vendor technici přistupující přes VPN | Kompromitovaný vendor endpoint, credential theft |
| Dodavatelé SCADA/DCS SW | AVEVA, Rockwell, Honeywell, Emerson | Zranitelnosti v software, update kanály, supply chain attacks |
| Dodavatelé průmyslových sítí | Hirschmann, Moxa, Cisco IE | Firmware zranitelnosti v síťových prvcích |

### Řízení přístupu dodavatelů — detailní požadavky

**Před přístupem:**
* Smluvní bezpečnostní požadavky uzavřeny (viz níže).
* Ověření identity technika (předem zaslaná ID, ověření při příchodu nebo online).
* Vydání časově omezených přihlašovacích údajů — maximálně na dobu servisního úkonu.
* Briefing dodavatele o bezpečnostní politice OT (co smí a nesmí dělat v síti).

**Během přístupu:**
* Veškerý vzdálený přístup přes jump server v průmyslové DMZ — bez výjimky.
* Full session recording (video, keystroke).
* Interní koordinátor přítomný nebo monitorující relaci v reálném čase pro kritické systémy.
* Žádné přenosy souborů mimo schválené kanály (žádné USB bez antivirové kontroly, žádné sdílení přes cloud bez schválení).
* Přístup pouze k systémům a segmentům nutným pro daný servisní úkon.

**Po přístupu:**
* Okamžité zrušení přístupu po dokončení úkonu.
* Přezkum session záznamu (nebo namátkový audit).
* Protokol provedených prací od dodavatele.
* Ověření integrity systémů po přístupu (PLC program hash, konfigurace).

### Smluvní bezpečnostní požadavky

Každá smlouva se systémovým integrátorem nebo poskytovatelem servisu musí obsahovat:

* Povinnost hlásit bezpečnostní incidenty (u dodavatele nebo jeho systémů).
* Právo na audit bezpečnostní praxe dodavatele.
* Požadavky na bezpečný vývoj softwaru (reference na IEC 62443-4-1 pro SW produkty).
* Požadavky na bezpečnostní způsobilost systémového integrátora (IEC 62443-2-4).
* Zákaz sdílení přihlašovacích údajů mezi techniky dodavatele.
* Povinnost použít schválené, aktualizované a bezpečné zařízení pro přístup (žádné osobní laptopy bez endpointové ochrany).
* Podmínky pro subcontracting (dodavatel nesmí dávat přístup sub-dodavateli bez souhlasu).

### Softwarová a firmwarová integrita

* Ověřování hash/digitálního podpisu při instalaci softwaru a firmwaru.
* Stahování pouze z oficiálních zdrojů výrobce (ne ze třetích stran).
* Žádná nekontrolovaná USB média v OT prostředí — přenosná média musí procházet antivirovou kontrolou na dedikované stanici v DMZ.
* Izolované testovací prostředí pro validaci aktualizací před nasazením do produkce.
* Software Bill of Materials (SBOM) pro kritické OT komponenty — umožňuje rychlé posouzení dopadu při objevení nové zranitelnosti (viz Log4Shell, SolarWinds).

---

## Capability Domain 12: Provozní kontinuita (Business Continuity)

### Záměr

Zajistit provozní odolnost a definovanou schopnost obnovy pro případ kybernetického incidentu nebo jiného narušení.

### Klíčové prvky

**Business continuity plán pro OT:**
* Definovat kritické procesy výroby a jejich prioritu obnovy.
* Definovat minimální přijatelnou úroveň výroby v degradovaném provozu.
* Scénáře: kybernetický incident (ransomware, sabotáž), výpadek SCADA, výpadek DCS, ztráta řídicí sítě.
* Komunikační plán pro případ incidentu (interní, zákazníci, regulátor, NÚKIB).

**Disaster recovery plán:**
* Recovery priority: safety systémy → kritické řídicí systémy → supervizorní systémy → reporting.
* Definované RTO a RPO pro každý systém (viz Backup & Recovery).
* Alternativní provozní procedury pro dobu obnovy.

**Manuální provozní procedury:**
* Dokumentace postupů pro manuální provoz kritických procesů.
* Tyto procedury musí být fyzicky dostupné v řídicí místnosti — ne pouze v digitální podobě na SCADA.
* Pravidelné nácviky manuálního provozu (zejména pro operátory, kteří byli celou kariéru v prostředí plné automatizace).

**Testování:**
* Plán kontinuity bez testování je pouze dokument.
* Tabletop exercises pro management a operátory.
* Technické testy obnovy ze zálohy.
* Výsledky testů dokumentovány, zjištění zapracovány do aktualizace plánů.

---

## Capability Domain 13: Dokumentace

### Záměr

Dokumentace je inženýrský výstup, nikoli papír pro audit.

Aktuální, přesná dokumentace je předpokladem pro efektivní provoz, rychlou obnovu po incidentu a prokázání shody při inspekci NÚKIB.

### Povinná dokumentace pro OT prostředí

| Dokument | Obsah | Frekvence aktualizace |
|----------|-------|-----------------------|
| Architektonické diagramy | Síťová topologie, zóny a kanály (conduits), datové toky — musí odpovídat skutečnosti | Po každé změně architektury |
| Inventář aktiv | Všechna OT aktiva s verzí FW/SW, vlastníkem, kritičností, umístěním, IP adresou | Průběžně, min. čtvrtletní přezkum |
| Risk assessment | Hrozby, zranitelnosti, hodnocení rizik, opatření | Ročně nebo při změnách |
| Bezpečnostní politiky | IAM, patch management, vzdálený přístup, změnové řízení, správa médií | Ročně nebo při změnách |
| Provozní postupy | Bezpečné pracovní postupy pro OT systémy | Při změnách systémů nebo procesů |
| Incident response plán | OT-specifický, otestovaný | Ročně nebo po incidentu |
| Zálohovací postupy | Co, kdy, jak, kde, kdo testuje, doklad testování | Při změnách zálohovací strategie |
| Change management záznamy | Auditovatelný záznam všech konfigurační změn | Každá změna |
| Dokumentace dodavatelů | Smlouvy se security přílohou, záznamy přístupu, výsledky auditů | Průběžně |
| SIS dokumentace | Specifikace Safety Instrumented Functions, bezpečnostní manuál, záznam testování (SIL verification) | Dle IEC 61511 + při změnách |
| Záznamy o školení | Kdo byl vyškolený, co, kdy | Po každém školení |
| Záznamy o testech a cvičeních | IR exercises, obnova ze zálohy, penetrační testy | Po každém testu |

> Dokumentace, která neodpovídá skutečnému stavu systému, je horší než žádná dokumentace — zavádí architekty i operátory a komplikuje response při incidentu.

---

---

# Část 4 — Vyhláška č. 410/2025 Sb. (Nižší povinnosti)

---

## Účel vyhlášky

Vyhláška 410 definuje bezpečnostní opatření pro organizace v režimu **nižších povinností**.

Cílem není snížená bezpečnost — jde o **proporcionální, udržitelnou, rizikem řízenou bezpečnost** odpovídající skutečnému rizikovému profilu a provozní kapacitě organizace.

---

## Inženýrská filozofie

Režim nižších povinností představuje:

* proporcionální governance,
* praktická bezpečnostní opatření,
* přiměřenou dokumentaci,
* průběžný risk management v odpovídajícím měřítku,
* provozní udržitelnost.

> Jednodušší architektura, která je konzistentně provozována a udržována, je obvykle bezpečnější než komplexní architektura, která nemůže být udržena.

---

## Bezpečnostní schopnosti — Nižší povinnosti

Stejné capability domény jako u vyšších povinností se aplikují i zde. Rozdíl spočívá v očekávané hloubce, formalitě a komplexitě implementace.

### Governance
* Jmenovaná odpovědná osoba pro kybernetickou bezpečnost.
* Dokumentovaná politika schválená managementem.
* Pravidelný přezkum.

### Správa aktiv
* Aktuální inventář aktiv podporujících regulované služby.
* Vlastník, funkce, umístění, kritičnost, stav životního cyklu.

### Řízení rizik
* Identifikace významných rizik.
* Posouzení dopadu.
* Definovaná léčba rizik.
* Periodický přezkum.

### IAM
* Unikátní uživatelské účty — žádné sdílené přihlašovací údaje.
* Přiměřená síla autentizace.
* Nejmenší oprávnění.
* Lifecycle management účtů.
* Pravidelný přezkum privilegovaných účtů.
* Kontrolovaný a dokumentovaný přístup dodavatelů.

### Síťová bezpečnost
* Základní síťová segmentace oddělující OT od IT.
* Kontrolované komunikační cesty.
* Bezpečný vzdálený přístup.
* Přiměřený firewall.

### Logování a monitoring
* Sběr relevantních bezpečnostních událostí.
* Synchronizace času (NTP).
* Dostatečná retence logů pro vyšetřování incidentů.
* Schopnost vyšetřovat incidenty.

### Správa zranitelností
* Identifikace známých zranitelností.
* Rizikem řízená prioritizace.
* Plánování patchů.
* Kompenzační opatření tam, kde okamžitý patch není možný.

### Zálohy a obnova
* Zálohy konfigurace a systémů.
* Dokumentované postupy obnovy.
* Pravidelné testování obnovy.

### Řízení incidentů
* Postup detekce a počátečního posouzení.
* Eskalační cesta.
* Postup obnovy.
* Dokumentace incidentů.
* Hlášení NÚKIB tam, kde je vyžadováno.
* Posouzení významnosti incidentu pro účely hlášení.

### Dokumentace
* Inventář aktiv.
* Síťové diagramy.
* Bezpečnostní postupy.
* Zálohovací postupy.
* Risk assessment.
* Záznamy o změnách.

---

---

# Srovnání režimů

| Oblast | Vyšší povinnosti (Vyhláška 409) | Nižší povinnosti (Vyhláška 410) |
|--------|--------------------------------|--------------------------------|
| Governance | Formální struktura, RACI, výbory | Jmenovaná osoba, schválená politika |
| Dokumentace | Rozsáhlá, auditovatelná, průběžně udržovaná | Zaměřená, praktická |
| Risk Management | Detailní, strukturované procesy, threat modeling | Zjednodušený, ale průběžný |
| Bezpečnostní opatření | Komplexní sada schopností | Základní schopnosti |
| IAM | PAM, MFA, full lifecycle, service accounts | Unikátní účty, least privilege, dodavatelský přístup |
| Síťová bezpečnost | Průmyslová DMZ, data diodes, přísné allowlisty, fyzická separace | Základní segmentace IT/OT, kontrolované cesty |
| Monitoring | OT IDS, pasivní analýza protokolů, integrace SIEM | Sběr relevantních událostí, NTP, vyšetřovací schopnost |
| Správa zranitelností | CVE tracking, kompenzační opatření, formální akceptace | Identifikace, prioritizace, plán patchů |
| Zálohy | Air-gapped, testované, RTO/RPO definované | Zálohy konfigurace a systémů, testovaná obnova |
| Audit | Pravidelný, nezávislý | Interní / ad hoc |
| Dohled NÚKIB | Proaktivní | Reaktivní |
| Cíl | Komplexní resilience | Udržitelná resilience |

Oba režimy jsou založeny na průběžném řízení rizik. Rozdíl spočívá v očekávané úrovni zralosti — nikoli v důležitosti kybernetické bezpečnosti.

---

---

# Checklisty pro architekta OT projektu

---

## Checklist 1: Zahájení OT projektu — posouzení regulatorního rozsahu

Použij na začátku každého OT projektu v potenciálně regulovaném prostředí.

### Česky

- [ ] Identifikoval jsem všechny obchodní služby organizace
- [ ] Porovnal jsem je se seznamem regulovaných služeb (příloha Vyhlášky 408)
- [ ] Posoudil jsem kritéria významnosti pro každou potenciálně regulovanou službu
- [ ] Posoudil jsem VŠECHNY regulované služby (ne pouze primární)
- [ ] Aplikoval jsem pravidlo eskalace (jedna vyšší povinnost → celá organizace)
- [ ] Zdokumentoval jsem výsledek posouzení rozsahu se zdůvodněním
- [ ] Posouzení bylo konzultováno s právním zástupcem nebo NÚKIB
- [ ] Posouzení bylo schváleno managementem a uchováno v projektové dokumentaci
- [ ] Je nastaven proces opětovného posouzení při změnách v organizaci nebo regulaci
- [ ] Byl informován NÚKIB (registrace), pokud je organizace regulována

---

## Checklist 2: Návrh bezpečnostní architektury OT

Použij při zahájení architektonického návrhu.

### Česky

**Inventář a rozsah**
- [ ] Byl proveden inventář všech OT aktiv (PLC, RTU, DCS, HMI, SCADA, engineering WS, historian, průmyslové sítě, SIS)
- [ ] Každé aktivum má přiřazeného vlastníka, verzi FW/SW a klasifikaci kritičnosti
- [ ] Byla identifikována legacy aktiva bez bezpečnostní podpory (end-of-life)

**Analýza rizik**
- [ ] Byl proveden Security Level Risk Assessment (SLRA) dle IEC 62443-3-2
- [ ] Byly identifikovány hrozby relevantní pro průmyslový sektor (MITRE ATT&CK for ICS)
- [ ] Byl proveden threat modeling pro klíčové OT systémy
- [ ] Byly určeny Target Security Levels (SL-T) pro každou bezpečnostní zónu
- [ ] Byl posouzen vztah kybernetické bezpečnosti a funkční bezpečnosti (SIS / IEC 61511)

**Síťová architektura**
- [ ] Jsou definovány bezpečnostní zóny a kanály (Zones and Conduits) dle IEC 62443
- [ ] Je navržena průmyslová DMZ jako povinná hranice IT/OT
- [ ] Neexistuje žádné přímé propojení IT ↔ OT mimo DMZ
- [ ] Jsou firewallová pravidla na allowlist bázi (deny-by-default)
- [ ] Jsou zváženy jednosměrné brány (data diodes) pro toky OT → IT bez zpětného kanálu
- [ ] Je SIS zóna fyzicky a logicky oddělena od základní řídicí sítě
- [ ] Je navržen bezpečný vzdálený přístup (MFA + jump server + session recording)

**IAM**
- [ ] Jsou definovány unikátní uživatelské účty pro všechny role v OT prostředí
- [ ] Je implementován RBAC pro SCADA, DCS a HMI systémy
- [ ] Je navržen PAM pro engineering workstations a privilegovaný přístup
- [ ] Je definován postup pro přístup dodavatelů (časově omezený, MFA, jump server, recording)
- [ ] Je definován postup pro zrušení přístupu při ukončení spolupráce

**Monitoring a logging**
- [ ] Je navrženo centralizované logování mimo produkční OT síť
- [ ] Je zajištěna NTP synchronizace pro všechna OT aktiva
- [ ] Je navržen OT-specifický pasivní monitoring / IDS
- [ ] Je definována retence logů dle požadavků vyhlášky
- [ ] Je navržen alerting pro kritické OT události (neautorizované PLC změny, anomálie protokolů)

**Zálohy a obnova**
- [ ] Jsou definovány zálohy pro PLC programy, DCS konfigurace, SCADA projekty
- [ ] Jsou navrženy offline/air-gapped zálohy pro kritické konfigurace
- [ ] Jsou definovány RTO a RPO pro každý kritický řídicí systém
- [ ] Existuje plán testování obnovy s dokumentovanými výsledky

**Dodavatelský řetězec**
- [ ] Jsou v smlouvách se SI a servisními organizacemi zahrnuty bezpečnostní požadavky
- [ ] Je definován postup pro správu přenosných médií v OT prostředí
- [ ] Je definován postup pro ověřování integrity SW a FW před instalací
- [ ] Je IEC 62443-2-4 zahrnut jako kritérium výběru systémového integrátora

**Dokumentace**
- [ ] Jsou vytvořeny architektonické diagramy odpovídající skutečnému stavu
- [ ] Je dokumentace udržována průběžně jako inženýrský výstup
- [ ] Je nastaven change management proces pro aktualizaci dokumentace při změnách systémů

---

## Checklist 3: Incident Response — připravenost OT

Použij pro ověření připravenosti na kybernetický incident.

### Česky

**Plánování**
- [ ] Existuje ICS-specifický Incident Response plán (ne adaptace IT IR plánu)
- [ ] Jsou definovány eskalační cesty pro incident ovlivňující výrobu nebo procesní bezpečnost
- [ ] Je definován kontakt na NÚKIB a postup pro hlášení incidentu (24h / 72h / 1 měsíc)
- [ ] Jsou zdokumentovány nouzové kontakty na vendory (Siemens ProductCERT, Rockwell, Honeywell...)
- [ ] Jsou dokumentovány manuální provozní postupy pro případ výpadku SCADA/DCS

**Detekce**
- [ ] Je implementováno OT monitoring s alertingem v reálném čase
- [ ] Jsou operátoři vyškoleni k rozpoznání příznaků kybernetického incidentu
- [ ] Je definováno, kdo přijímá bezpečnostní alerty mimo pracovní dobu (24/7 pokrytí)
- [ ] Je NTP synchronizace funkční a monitorovaná na všech OT zařízeních

**Cvičení**
- [ ] Byl proveden tabletop exercise pro management a provozní tým (min. 1× ročně)
- [ ] Byl proveden technický test obnovy ze zálohy (min. 1× ročně)
- [ ] Byly výsledky cvičení zdokumentovány a zjištění zapracována do aktualizace plánů

**Recovery**
- [ ] Jsou zálohy PLC programů a DCS konfigurací dostupné a otestované
- [ ] Je definován postup obnovy pro každý kritický systém
- [ ] Je definováno, kdo schvaluje restart výroby po kybernetickém incidentu

---

## Checklist 4: Příprava na inspekci NÚKIB

Použij před plánovanou inspekcí nebo jako průběžné ověření připravenosti.

### Česky

**Dokumentace**
- [ ] Inventář aktiv je aktuální a odpovídá skutečnému stavu OT prostředí
- [ ] Architektonické diagramy odpovídají skutečné síťové topologii (ne stavu z doby projektu)
- [ ] Risk assessment byl aktualizován v posledních 12 měsících
- [ ] Bezpečnostní politiky jsou schválené managementem a datované
- [ ] Záznamy o incidentech jsou kompletní a uložené
- [ ] Záznamy o testech (IR exercises, zálohy) jsou dostupné
- [ ] Záznamy o školení pracovníků jsou dostupné
- [ ] Smlouvy s dodavateli obsahují bezpečnostní přílohy

**Procesní připravenost**
- [ ] Klíčoví pracovníci jsou schopni vysvětlit bezpečnostní procesy vlastními slovy
- [ ] Procesy jsou skutečně provozovány (ne pouze zdokumentovány)
- [ ] Change management záznamy dokládají skutečně prováděný proces
- [ ] Přístupové záznamy dodavatelů jsou k dispozici

**Technická připravenost**
- [ ] Firewallová pravidla odpovídají dokumentovaným komunikačním tokům
- [ ] Skutečná síťová topologie odpovídá architektonickým diagramům
- [ ] Zálohy existují a jsou čitelné
- [ ] Monitorovací systémy jsou funkční a generují alerty

---

# AI Guidance

Při odpovídání na otázky týkající se českého zákona o kybernetické bezpečnosti:

* Vždy začni určením, zda je organizace regulována — posouzení rozsahu dle Vyhlášky 408 musí předcházet diskusi o opatřeních.
* Rozlišuj právní povinnosti (zákon, vyhlášky) od inženýrských doporučení (IEC 62443).
* Identifikuj aplikovatelný režim (vyšší nebo nižší povinnosti) před doporučením bezpečnostních opatření.
* Doporučuj rizikem řízené architektury proporcionální k aplikovatelnému režimu.
* Odkazuj na [IEC62443-Overview.md](#) pro implementační detail.
* Odkazuj na [NIS2.md](#) pro rámec EU governance.
* Nedoporučuj over-engineered řešení pro prostředí nižších povinností.
* Zohledňuj implikace funkční bezpečnosti při diskusi o OT bezpečnostních opatřeních.
* Upřesňuj předpoklady o regulatorní aplikovatelnosti, pokud kontext organizace není jasný.
* Pro SIS a safety systémy vždy zdůraznit koordinaci IEC 62443 a IEC 61511.
* Při diskusi o síťové architektuře odkazovat na [ISA95.md](#) pro funkční kontext.

---

# Historie revizí

| Verze | Datum | Popis |
|-------|-------|-------|
| 1.0.0 | 2026-06-28 | Počáteční vydání (separátní soubory) |
| 1.1.0 | 2026-06-29 | Konsolidace do jednoho dokumentu; přidána regulatorní hierarchie, OT implementační detaily, funkční bezpečnost, srovnávací tabulka režimů, checklist, cross-linky |
| 1.2.0 | 2026-06-29 | Rozsáhlé rozšíření: sankce a vynucování, interakce s NÚKIB, příprava na inspekci, detailní OT asset inventory, ATT&CK for ICS, SIS/Triton reference, síťová architektura ASCII diagram, detailní vendor access management, patch management srovnání IT vs OT, OT backup tabulka, ICS IR fáze, supply chain kategorie, dokumentační tabulka, 4× checklist v češtině, detailní protokolová srovnání |
