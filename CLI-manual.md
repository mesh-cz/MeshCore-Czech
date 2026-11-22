# CLI manuál — MeshCore Czech v1.18.0.03

[EN — English version below](#english)

Manuál vydání `v1.18.0.03` pro **repeatery**. Příkazy zadávejte
jako správce přes sériový terminál nebo vzdálené administrátorské rozhraní.
`on` zapíná, `off` vypíná. Zápis `a|b` znamená vybrat jednu možnost;
`<hodnota>` se nahrazuje konkrétním údajem a `[údaj]` je volitelný.

Vlastní funkce používají `cz.guard`, `cz.health` a `cz.watchdog…`.
Staré názvy těchto funkcí bez `cz.` nejsou podporované. Standardní příkazy
MeshCore a PowerSaving, například `ver`, `region` a `powersaving`, zůstávají.

## 1. Rychlá kontrola

```text
ver
board
power
get repeat
cz.health
cz.health check
cz.health check sensors
cz.health check time
```

`power` ukazuje mimo jiné `batt` z měření desky.
CzHealth může přednostně používat jiné napětí z INA/BMS.

Příklad prvního nastavení pro **1S Li-ion**:

```text
cz.health battery liion
cz.health battery.cells 1
cz.health on
cz.health sensors on
cz.health config on
```

**Počkejte přibližně minutu na první vzorek; potom lze zkusit `cz.health send`.
Při zamítnutí zkontrolujte `cz.health check`.**

## 2. CzHealth — zapnutí, stav a historie

CzHealth sdílí provozní stav repeateru pro přehled o baterii, čidlech
a dostupnosti zařízení. Údaje mohou být veřejně zobrazené v komunitních
službách. Sdílení je výchozí ON. Pro vypnutí senzorových hlášení použijte
`cz.health off`, pro vypnutí sdílení konfigurace `cz.health config off`.
Pro vypnutí obou částí použijte oba příkazy.

| Příkaz | Význam |
|---|---|
| `cz.health` | Stav, baterie, zdroj teploty, počet vzorků a další odeslání. |
| `cz.health on` / `cz.health off` | Zapnout/vypnout sběr a senzorové zprávy. CONFIG má samostatnou volbu. |
| `cz.health sensors [on\|off]` | Zjistit/nastavit sběr a odesílání senzorových údajů. |
| `cz.health config [on\|off]` | Zjistit/nastavit samostatné odesílání konfigurace. |
| `cz.health samples` | Souhrn uložené historie. |
| `cz.health samples battery [1-4]` | Napětí, 12 vzorků na stránku. |
| `cz.health samples temperature` | Nejvyšší teplota v jednotlivých dvouhodinových úsecích. |
| `cz.health samples current` | Průměr proudu v jednotlivých dvouhodinových úsecích. |
| `cz.health samples humidity` | Nejvyšší vlhkost v jednotlivých dvouhodinových úsecích. |
| `cz.health send` | Ruční DAY z uložených vzorků; nevyvolává nové okamžité měření. |

První vzorek je přibližně po **60 sekundách**, další každých **30 minut**.
Má-li repeater důvěryhodný čas (synchronizace adminem nebo GPS), vzorky se
zarovnají na celou a půl hodinu. Probíhající ověřování čidla může vzorek
odložit. Historie je v RAM; restart ji nezařadí zpět z předchozího běhu.
Vypnutí sběru neznamená vymazání již uložených vzorků. Chybějící hodnoty
nejsou naměřené nuly.

### Automatické zprávy

Hlášení mají verzi **v5**.

- První DAY se plánuje **30–90 minut po startu**, další každých
  **10–12 hodin**. Každý DAY nese posledních 24 hodin, takže každý vzorek
  jde ve dvou hlášeních a jedno ztracené nevadí. Platí to pro bateriové
  i externě napájené uzly; týdenní SUMMARY se už neposílá.
- **STATS:** jednou denně (nejvýš jednou za 23 hodin, asi 5 hodin po DAY)
  krátká statistika provozu: šum, vysílací a přijímací čas, počty přijatých,
  odeslaných a CzGuardem zahozených paketů, duplicit, chybných přihlášení
  a odmítnutých přehrání.
- **Alarm** (DAY dřív než podle plánu, nejvýš jednou za **6 hodin**):
  - u stabilního externě napájeného uzlu, když se napětí odchýlí o víc než
    **120 mV** od průměru při posledním hlášení nebo teplota o víc než **15 °C**;
  - u kteréhokoli uzlu při **ztrátě externího napájení**.
- Mezi plánovanými hlášeními CzHealth (DAY, CONFIG, STATS) je víc než hodina.
- Podmínkou jsou zapnuté CzHealth a senzory, dostupné vzorky
  a splněné podmínky odeslání.

### Ruční send

Ruční odeslání je nejvýše **jednou za 4 hodiny**. Před odesláním se uloží
rezervace do flash. První požadavek bez aktivní rezervace může projít;
restart během rezervace znamená **nové 4 hodiny čekání od startu**.
Změna hodin limit nezkrátí. Uplynutí blokace se trvale zaznamená.

Odmítnuté požadavky běžně nevytvářejí další zápisy. Při chybě úložiště
se odeslání odmítne. Rezervace zůstává i při následném selhání zařazení zprávy;
automatická hlášení tento ruční limit neblokuje. Po CONFIG nebo STATS může být
nutný odstup více než hodinu. `OK - sent` neznamená potvrzené doručení na server.

### Diagnostika

| Příkaz | Význam |
|---|---|
| `cz.health check` | Verze hlášení (`v5`), výsledek posledního odeslání (`last ok`/`fail`), stav odesílání, čekání, ruční limit a počet vzorků. |
| `cz.health check config` | Stav CONFIG a nejbližší způsobilý termín. |
| `cz.health check sensors` | Zdroj napětí/teploty, BMS/INA, dostupnost proudu/vlhkosti a stáří vzorku. |
| `cz.health check time` | Zdroj času, jeho důvěryhodnost, čas a uptime. |

Diagnostika sama neodesílá nový CzHealth report a nepřidává nový vzorek.
`last ok` znamená, že repeater hlášení odeslal do sítě, ne že ho server přijal.

## 3. Baterie a zdroj napětí

| Příkaz | Význam |
|---|---|
| `cz.health battery` | Typ, články a skutečný zdroj posledního uloženého napětí. |
| `cz.health battery auto\|liion\|lifepo4\|lto\|naion\|lead\|other\|lto-vlastik\|naion-vlastik` | Nastavit typ baterie, výchozí `auto`. |
| `cz.health battery.cells [1-16]` | Počet článků v sérii, výchozí 1; paralelní články se nepočítají. |
| `cz.health ina.channel [1-3]` | Fyzický kanál INA3221 pro napětí, výchozí 1. |

Typ a počet článků se ukládají. Jejich změna vymaže dosavadní vzorky v RAM,
aby nebyly chybně označené novými metadaty. `auto` neprovádí detekci chemie
ve firmwaru; odhad je na přijímači. Volba typu nemění nabíječku ani ochranu baterie.

**Napětí se vybírá při každém vzorku:**

1. Pro `lto-vlastik` / `naion-vlastik` má přednost Vlastíkova BMS emulující INA260.
2. Jinak, nebo při nedostupné BMS, se použije dostupné nenulové napětí INA.
3. Bez použitelného INA se použije ADC desky. Bez použitelného měření je údaj nedostupný.

Zdroj `adc` / `ina` / `bms` se přenáší spolu s daty. Při nedostupné zvolené
BMS zůstává její proud nedostupný; záložní ADC může měřit výstup měniče.
Přímý přepínač pro vynucení ADC/INA není. Kanály 2/3 patří k INA3221;
u jednokanálového čidla používejte kanál 1.

CzHealth podporuje napětí do **65,535 V**. Vyšší čtení se označí jako
nepodporované/nedostupné, nikoli jako platná nulová hodnota.

### Vlastíkova BMS: LTO a Na-ion

Pro BMS od Vlastimila „Vlastíka“ ([uart.cz](https://uart.cz/)), která přes I2C
emuluje INA260, zvolte podle skutečné
baterie `lto-vlastik` nebo `naion-vlastik`. Například pro **LTO s jedním
článkem v sérii**:

```text
cz.health battery lto-vlastik
cz.health battery.cells 1
cz.health current on
```

Počkejte přibližně minutu na nový vzorek a zkontrolujte `cz.health battery`
a `cz.health check sensors`.

Počet článků upravte podle skutečného zapojení. Změna typu či počtu článků
vymaže dosavadní vzorky v RAM a první nový vzorek je plánovaný přibližně za
minutu. Diagnostika ukazuje poslední měření; po změně kanálu nebo zapojení
počkejte na další vzorek. Čidla se zjišťují při startu, proto po připojení
zařízení, které při startu chybělo, repeater restartujte.

Příklad výstupu `cz.health check sensors`:

```text
sensors on, n 1, age 29s, V bms, BMS ok, INA not detected, temp mcu, current ok, humidity unavailable
```

- `V bms` znamená, že poslední vzorek napětí pochází z BMS; `V ina` označuje
  jiný INA zdroj a `V adc` analogový vstup desky.
- `BMS ok` znamená, že se při posledním vzorku podařilo přečíst napětí a proud
  přes rozhraní INA260. Pokud máte připojenou Vlastíkovu BMS, odpovídá
  tento výstup jejímu běžnému chování. Firmware ale výrobce ani typ zařízení
  spolehlivě nerozpozná; stejně může odpovědět i jiné kompatibilní zařízení.
- `current ok` říká, že je pro poslední vzorek dostupný proud; `temp mcu`
  označuje teplotu mikrokontroléru a `humidity unavailable` nedostupnou vlhkost.
- **V režimech `lto-vlastik` a `naion-vlastik` položka `INA` neověřuje
  samostatně další připojené INA čidlo.** `INA not detected` proto
  v těchto režimech samo o sobě nedokazuje, že další INA není připojená.
  `BMS unavailable` znamená, že se čtení zvolené BMS nepodařilo; firmware
  může pro napětí použít INA nebo ADC, proud BMS je pak nedostupný.

`cz.health ina.channel 1`, `2` nebo `3` vybírá fyzický kanál INA3221 pro
**napětí** a `cz.health ina.channel` zobrazí uloženou volbu. U samostatné
jednokanálové INA219 použijte kanál 1. Tato volba nepřepíná kanál BMS ani
nevybírá zdroj proudu. Je-li BMS dostupná a zvolená jako `lto-vlastik` nebo
`naion-vlastik`, její napětí má přednost; kanál INA3221 se může uplatnit pro
záložní měření napětí. Tento příkaz nevybírá konkrétní I2C zařízení; při
více INA čidlech záleží na jejich detekci a pořadí ve firmwaru.

## 4. Teplota, proud a kontrola čidel

| Příkaz | Význam |
|---|---|
| `cz.health temperature auto` | Externí čidlo, při nedostupnosti podporovaná teplota MCU. |
| `cz.health temperature mcu` | Pouze teplota MCU, pokud ji deska poskytuje. |
| `cz.health temperature sensor` | Pouze externí čidlo, jinak nedostupné. |
| `cz.health temperature off` | Teplotu v CzHealth vypnout. |
| `cz.health current on` / `cz.health current off` | Proud v CzHealth zapnout/vypnout. |
| `sensor list` | Dostupné položky nastavení senzorů, pokud je podporuje sestavení. |

Vlhkost se sbírá při dostupném čidle a zapnutém sběru, bez vlastního přepínače.
Čidla se detekují při startu; čidlo chybějící při startu vyžaduje po připojení restart.

Firmware kontroluje chyby čtení, NaN a rozsah konkrétního čidla. Skok teploty
nad **20 °C** vyvolá až dvě další čerstvá měření přibližně po sekundě.
Alespoň dvě platné hodnoty musí souhlasit do **5 °C**. Potvrzená skutečná
změna je přijata; bez shody je údaj nedostupný a opakované chyby vedou
k omezenému pokusu o obnovu čidla. V auto režimu lze použít MCU.
MCU a venkovní čidlo se neporovnávají jako stejné místo měření.

## 5. CONFIG a regiony

`cz.health config on|off` řídí samostatnou zprávu. Obsahuje domácí, výchozí
a flood regiony včetně `*`, pokud má příslušnou roli. Podle dostupného místa obsahuje také typ desky.
Verzi firmwaru server pozná přímo z hlášení, v CONFIG se už neposílá.

- Změna regionů vyvolá porovnání; před odesláním se načte aktuální stav z RAM.
- Změnová zpráva nejvýše jednou za **24 hodin**, nezměněný stav po **7 dnech**.
- První zpráva přibližně za **1–2 hodiny**; platí odstup více než hodinu od
  senzorové zprávy a ochrana přes restart.
- Pokud se seznam nevejde, zachová se domácí/výchozí region a další podle
  pořadí; report je označený jako neúplný.
- CONFIG se ovládá nezávisle na senzorech. `cz.health send` konfiguraci
  neposílá; vlastní ruční CONFIG příkaz není.

Užitečné standardní příkazy:

| Příkaz | Význam |
|---|---|
| `region` | Vypsat regiony. |
| `region home` / `region default` | Zjistit domácí/výchozí region. |
| `region put <název> [rodič]` | Vytvořit region. |
| `region allowf <název>` / `region denyf <název>` | Povolit/zakázat flood regionu. |
| `region save` | Uložit změny regionů, aby přežily restart. |
| `region load` | Načíst regiony po řádcích (viz níže). |

U `*` určuje flood oprávnění zacházení s provozem bez regionového omezení.

Při `region load` posílejte řádky s odsazením (hloubka odsazení určuje
rodiče); repeater každý potvrdí (`OK …` nebo `Err - line ignored`). Prázdný
řádek načtené regiony použije. Řádek bez odsazení nebo **60 sekund** bez
řádku načítání zruší a regiony zůstanou beze změny
(`Err - region load cancelled, regions unchanged`). Rušicí řádek se jako
příkaz neprovede, další příkazy už se zpracují normálně.

## 6. CzGuard — místní ochrana

| Příkaz | Význam |
|---|---|
| `cz.guard` / `cz.guard help` | Stav / nápověda. |
| `cz.guard on` / `cz.guard off` | Zapnout/vypnout místní automatiku. |
| `cz.guard dryrun on` / `cz.guard dryrun off` | Pozorovat místo uplatnění místních automatických omezení. |
| `cz.guard stats` | Počítadla ochrany. |
| `cz.guard list [stránka]` | Omezené zdroje, čtyři na stránku. |
| `cz.guard unban <hex>` | Zrušit místní omezení odpovídající prefixu. |
| `cz.guard allow` | Vypsat místní výjimky. |
| `cz.guard allow add <c\|a\|r\|n> <hex>` | Přidat místní výjimku. |
| `cz.guard allow del <c\|a\|r\|n> <hex>` | Odebrat místní výjimku. |
| `cz.guard reset` | Vymazat stav sledovaných proudů a místních omezení. |

`c` = zprávy v kanálech, `a` = adverty, `r` = flood dotazy,
`n` = anonymní požadavky. Prefix nemusí zdroj určit jednoznačně.
Dryrun se týká místní automatiky; neznamená vypnutí všech ochranných mechanismů.
`save failed` znamená problém s trvalým uložením; nepovažujte takovou změnu
za spolehlivě uloženou přes restart.

Ve v1.18.0.03 rozhoduje jen místní automatika. Centrální blokace a výjimky
ani příkazy pro ně (`cz.guard central`, `inject`, `key`, `send`) už nejsou;
soubory z dřívějších verzí repeater při startu smaže.

Repeater posílá o své ochraně krátký report: první 5–30 minut po startu,
potom jen při novém banu nebo jeho konci (nejvýš jednou za 6 hodin)
a jednou denně. Omezení, zkušební den ani vyšší stupeň banu report nevyvolají.
Nová blokace přihlášení (viz níže) vyvolá report během několika minut, nejvýš
jednou za 6 hodin.

### Ochrana proti hádání hesel

| Příkaz | Význam |
|---|---|
| `cz.guard login` / `cz.guard login help` | Stav (limity, zablokované klíče, špatná hesla, odmítnuté pokusy, blokace) / nápověda. |
| `cz.guard login on` / `cz.guard login off` | Zapnout/vypnout ochranu přihlášení. Výchozí: zapnuto. |
| `cz.guard login list` | Zablokované klíče: začátek klíče, zbývající čas, počet špatných hesel. |
| `cz.guard login unlock` | Zrušit všechny blokace přihlášení. |
| `cz.guard login key <3-100>` | Kolik špatných hesel z jednoho klíče klíč zablokuje. Výchozí 10. |
| `cz.guard login all <5-250>` | Kolik špatných hesel ze všech klíčů dohromady zablokuje všechny neznámé klíče. Výchozí 30. |
| `cz.guard login window <5-120>` | Okno v minutách, ve kterém se špatná hesla počítají. Výchozí 15. |
| `cz.guard login lock <5-240>` | Délka první blokace v minutách. Výchozí 15. |
| `cz.guard login flood` | Zobrazit limit přeposílání flood přihlášení. |
| `cz.guard login flood <počet> <min> <ban min>` | Nastavit ho: počet 5–250, okno 5–240 min, ban 10–2880 min. Výchozí 30 za 60 min, ban 720 min (12 h). |

- Platí pro přihlášení administrátora i hosta. Klíče, které už jsou v ACL jako
  administrátor, se nikdy neblokují; nového administrátora lze vždy přidat
  přes sériovou konzoli.
- Během blokace repeater heslo vůbec nekontroluje, odmítne i správné a neodpoví,
  stejně jako u špatného hesla.
- Další blokace do 24 hodin po předchozí trvá dvojnásob: u jednoho klíče
  nejvýš 12 hodin, u všech klíčů nejvýš 2 hodiny. Správné heslo mimo blokaci
  vynuluje počítadlo daného klíče.
- Blokace a počítadla jsou jen v paměti a restart je smaže; nastavení se ukládá.
- Má-li repeater výchozí heslo administrátora, `cz.guard` a `cz.guard login`
  zobrazí `! default admin password - change it`. Heslo změňte příkazem
  `password <nové heslo>`.
- **Flood přihlášení:** repeater přeposílá flood žádosti o přihlášení
  k jednomu cílovému repeateru (od kohokoli) jen do nastaveného limitu; nad něj
  je po dobu banu nepřeposílá. Tento limit patří pod `cz.guard on/off`
  a `cz.guard dryrun`, ne pod `cz.guard login on/off`.

## 7. Úspory, Repeat, watchdog a hosté

| Příkaz | Význam |
|---|---|
| `get repeat` / `set repeat on` / `set repeat off` | Stav a povolení přeposílání cizích paketů. |
| `get tx` / `set tx <dBm>` | Výkon rádiového čipu; rozsah závisí na desce. |
| `get reboot.interval` / `set reboot.interval 0` | Interval plánovaného restartu / jeho vypnutí. |
| `gps` / `gps on` / `gps off` | Používání GPS v sestavení s podporou. |
| `cz.telemetry.guest.hide [on\|off]` | Skrytí baterie/teploty v běžné telemetrii hostů, výchozí OFF. |

Výchozí výkon rádiového čipu závisí na desce; uložený vlastní výkon
se zachová. Výkon čipu neodpovídá výkonu za externím PA.
Skrytí údajů hostům zachovává přístup správce a neukrývá samostatné CzHealth zprávy.

### PowerSaving a RX power saving (RXPS)

| Příkaz | Význam |
|---|---|
| `powersaving` | Stav PowerSavingu (`on` / `off`). |
| `powersaving on` | Zapnout. nRF52 hned; ESP32 po 5 minutách a sériová konzole se vypne; desky s bridgem nepodporují. |
| `powersaving off` | Vypnout. |
| `get radio.rxps` | Stav RX power saving (význam výpisu níže). |
| `set radio.rxps off` | Vypnout RXPS, rádio přijímá nepřetržitě. |
| `set radio.rxps on` / `set radio.rxps conservative` | Opatrná úspora, úroveň 3. |
| `set radio.rxps balanced` | Vyvážená úspora, úroveň 6. Výchozí pro novou instalaci. |
| `set radio.rxps max` | Nejvyšší úroveň v mezích datasheetu, úroveň 8. |
| `set radio.rxps level <1-10> [preamble <16\|32>]` | Konkrétní úroveň, volitelně s délkou preambule. |
| `set radio.rxps max preamble <16\|32>` | Max s danou preambulí. |
| `set radio.rxps overdrive` / `set radio.rxps riskyWorkingMax` | Úroveň 9 / 10, mimo časování z datasheetu. **Nedoporučujeme**, úroveň 10 už může ztrácet pakety. |
| `set radio.rxps <rx_us> <sleep_us>` | Ruční délka příjmu a spánku v µs (1 000–30 000 000). Jen pro zkušené. |

- PowerSaving a RXPS jsou samostatné volby; podporu určuje deska a rádio.
  RXPS podporují rádia SX1262 a LR1110; LR1121, LR2021 a SX127x přijímají
  nepřetržitě (`supported=no`).
- **Úrovně:** vyšší úroveň znamená delší spánek mezi příjmy a menší spotřebu,
  ale menší rezervu na zachycení začátku paketu. Pro běžný repeater
  doporučujeme `balanced`.
- **Preambule:** `16` je bezpečná pro všechny odesílatele v síti; `32` dovolí
  delší spánek, ale počítá s odesílateli s delší preambulí. Bez zadání se volí
  podle SF.
- **Výchozí stav:** nová instalace má `balanced` (úroveň 6, preambule 16).
  Uložená volba, včetně `off`, se při aktualizaci zachová.
- **Výpis `get radio.rxps`:** `desired` = uložené on/off; `effective` =
  `armed` (úspora běží) nebo `continuous` (nepřetržitý příjem); `supported` =
  jestli to rádio umí; `err` / `fail` = poslední chyba a počet nezdarů;
  `level`, `preamble`, `rx` / `sleep` (µs) = nastavení; `catch` = kolik symbolů
  preambule repeater ještě zachytí.
- **Chyby:** nastavení, které nesedí na aktuální SF/BW, repeater odmítne. Po
  změně rádia zůstane v nepřetržitém příjmu s `err=-10001` a uloženou volbu si
  drží. Po třech nezdarech zůstane v nepřetržitém příjmu, dokud nastavení
  nezměníte. `set radio` platí až po restartu.
- **PowerSaving a GPS:** se zapnutým PowerSavingem běží GPS nejvýš 10 minut
  a pak spí až den. `gps` ukáže, kdy usne nebo se probudí a kdy naposledy
  synchronizoval čas.
- Nový automatický úsporný režim při nízké baterii není součástí této verze.

### CzWatchdog — hlídání rádiového ticha

CzWatchdog je vlastní funkce MeshCore Czech. Když repeater po nastavenou dobu
nic nepřijme ani neodvysílá, nejspíš se zaseklo rádio, a repeater se proto
sám restartuje. Hodí se pro repeatery na těžko dostupných místech.

| Příkaz | Význam |
|---|---|
| `cz.watchdog` | Souhrn: zapnuto/vypnuto, limit v minutách a počet restartů. |
| `cz.watchdog.silence` | Je hlídání zapnuté? |
| `cz.watchdog.silence on` / `cz.watchdog.silence off` | Zapnout / vypnout. Výchozí: vypnuto. |
| `cz.watchdog.silence.timeout` | Limit ticha v minutách. |
| `cz.watchdog.silence.timeout <1-240>` | Nastavit limit. Výchozí 60 minut. |
| `cz.watchdog.reboots` | Kolikrát watchdog repeater restartoval. |

- Odpočet ticha nulují skutečně přijaté pakety a dokončené vysílání.
- Limit volte podle provozu v okolí: v tiché oblasti příliš krátký limit
  zbytečně restartuje i funkční repeater.
- Počítadlo restartů počítá jen restarty od watchdogu. Na nRF52 přežije běžný
  restart (nejvýš 255); po odpojení napájení nebo OTA se může vynulovat,
  na ostatních platformách platí jen do restartu.
- Nastavení se ukládá a přežije restart i aktualizaci.

## 8. Čas a aktualizace

`clock` zobrazí čas; administrátorské `clock sync` jej synchronizuje s klientem,
pokud to používané rozhraní podporuje. `gps sync` využije dostupné GPS.
`cz.health check time` ukáže původ a důvěryhodnost. Bez důvěryhodného času
se report opírá o čas příjmu a stáří vzorků; ruční limit používá uptime.

### Ochrana proti přehrání příkazů

Repeater přijme vzdálený příkaz administrátora jen s novějším časem, než měl
poslední příkaz od stejného klienta. Ve v1.18.0.03 si tento čas pamatuje
i přes restart, takže zachycený starý příkaz (třeba `reboot`) nejde poslat
znovu. Pokud vás repeater po posunutí času v telefonu nebo companionu dozadu
nepustí, srovnejte čas, nebo se přihlaste heslem z jiného (případně
resetovaného) companionu.

### nRF52 Bluetooth DFU na Androidu

Před `start ota` nastavte v nRF Device Firmware Update:

| Volba | Nastavení |
|---|---|
| Packets receipt notification | **ON** |
| Number of packets | **8** |
| Request high MTU | **OFF** |
| Disable resume | **ON** |
| Force scanning | **ON** |
| Prepare object delay | **0 ms** |

Vyberte ZIP pro svou desku. I po Completed ověřte LoRa a `ver`; vhodné
nastavení vyřešilo hlášený problém ProMicro/XIAO bez změny firmwaru.
[Podrobnosti a odkaz na postup MeshCore](README.md#bluetooth-dfu-na-androidu-nrf52).

Při chybě hlaste desku, `ver`, způsob nahrání, příkaz a přesnou odpověď.
Hesla neposílejte. [Přehled změn](CHANGELOG.md).

---

## English

CLI manual for the **`v1.18.0.03` repeater** release. Use an
administrator serial terminal or authenticated remote admin interface.
`on` enables, `off` disables; `a|b` means choose one, `<value>` is a placeholder,
and `[value]` is optional.

Custom features use `cz.guard`, `cz.health` and `cz.watchdog…`, without old
aliases. Standard MeshCore/PowerSaving commands such as `ver`, `region`
and `powersaving` keep their names.

### 1. Quick checks

```text
ver
board
power
get repeat
cz.health
cz.health check
cz.health check sensors
cz.health check time
```

`power` includes the board's battery reading. CzHealth may
prefer INA/BMS voltage. Example setup for **1S Li-ion**:

```text
cz.health battery liion
cz.health battery.cells 1
cz.health on
cz.health sensors on
cz.health config on
```

**Allow about one minute for the first sample, then try `cz.health send`.
If refused, inspect `cz.health check`.**

### 2. CzHealth — control and history

CzHealth shares operational device status to help monitor the battery,
sensors and availability. Readings may be displayed publicly by community
services. Sharing defaults to ON. Use `cz.health off` to disable sensor
reports and `cz.health config off` to disable configuration sharing.
Use both commands to disable both parts.

| Command | Meaning |
|---|---|
| `cz.health` | Status, battery, temperature source, sample count and next send. |
| `cz.health on` / `cz.health off` | Enable/disable sampling and sensor reports; CONFIG is independent. |
| `cz.health sensors [on\|off]` | Read/set sensor collection and reporting. |
| `cz.health config [on\|off]` | Read/set configuration reporting. |
| `cz.health samples` | History summary. |
| `cz.health samples battery [1-4]` | Voltage history, 12 samples per page. |
| `cz.health samples temperature` | Maximum temperature per two-hour slot. |
| `cz.health samples current` | Average current per two-hour slot. |
| `cz.health samples humidity` | Maximum humidity per two-hour slot. |
| `cz.health send` | Manual DAY using stored samples; no new immediate measurement. |

First sample after about **60 seconds**, then every **30 minutes**. With a
trusted clock (admin or GPS sync) samples are aligned to the full and half
hour. Sensor confirmation may defer a sample. History is in RAM and is not
restored across reboot. Disabling collection does not necessarily clear
previous RAM samples. Missing measurements are not measured zeroes.

#### Automatic reports

Reports are version **v5**.

- First DAY **30–90 minutes after boot**, then every **10–12 hours**. Each DAY
  carries the last 24 hours, so every sample goes out in two reports and one
  lost report does not matter. This applies to battery and externally powered
  nodes alike; the weekly SUMMARY is no longer sent.
- **STATS:** once a day (at most every 23 hours, about 5 hours after a DAY)
  short traffic statistics: noise floor, TX and RX airtime, packets received,
  sent and dropped by CzGuard, duplicates, wrong-password logins and refused replays.
- **Alarm** (a DAY ahead of schedule, at most every **6 hours**):
  - on a stable externally powered node, when the voltage moves more than
    **120 mV** from the average at the last report or the temperature more than **15 °C**;
  - on any node, on **loss of external power**.
- Scheduled CzHealth reports (DAY, CONFIG, STATS) are more than one hour apart.
- Health and sensors must be enabled, samples available and sending
  conditions met.

#### Manual send

At most **once per four hours**, with a flash reservation committed before
queueing. The first request without an active reservation may proceed.
A reboot during a reservation starts a **new four-hour uptime wait**.
Changing the clock cannot shorten it. Expiry is saved persistently.

Rejected requests normally do not write again. Storage errors block sending;
a reservation survives a subsequent queueing failure. The manual limit does
not block automatic reports. CONFIG or STATS may require a gap longer than one
hour. `OK - sent` does not confirm server delivery.

#### Diagnostics

| Command | Meaning |
|---|---|
| `cz.health check` | Report version (`v5`), result of the last send (`last ok`/`fail`), sending state, delays, manual reservation and sample count. |
| `cz.health check config` | CONFIG state and earliest eligible time. |
| `cz.health check sensors` | Voltage/temperature sources, BMS/INA, current/humidity availability and sample age. |
| `cz.health check time` | Clock source, trust, time and uptime. |

These checks do not generate a new health report or sample. `last ok` means
the repeater sent the report into the mesh, not that the server received it.

### 3. Battery and voltage source

| Command | Meaning |
|---|---|
| `cz.health battery` | Chemistry, cells and source of the last stored voltage sample. |
| `cz.health battery auto\|liion\|lifepo4\|lto\|naion\|lead\|other\|lto-vlastik\|naion-vlastik` | Set chemistry; default `auto`. |
| `cz.health battery.cells [1-16]` | Series cells, default 1; parallel cells do not count. |
| `cz.health ina.channel [1-3]` | INA3221 physical voltage channel, default 1. |

Chemistry/cells persist. Changing them clears RAM history to avoid relabeling
older samples. `auto` leaves estimation to the receiver; firmware does not
identify chemistry. These settings do not configure charging or battery protection.

**Source selection at every sample:**

1. `lto-vlastik` / `naion-vlastik` prefer Vlastik's INA260-emulating BMS.
2. Otherwise, or if BMS is unavailable, a usable nonzero INA voltage is preferred.
3. Without usable INA, use board ADC; without a usable reading, report unavailable.

The report includes `adc` / `ina` / `bms` provenance. Failed selected BMS
current remains unavailable; fallback ADC may measure a converter output.
There is no direct force-ADC/INA switch. Channels 2/3 are for INA3221;
single-channel devices should use channel 1.

CzHealth supports up to **65.535 V**. Higher readings are marked unsupported
and unavailable, rather than becoming a valid zero reading.

#### Vlastik BMS: LTO and Na-ion

For the BMS by Vlastimil “Vlastik” ([uart.cz](https://uart.cz/)), which emulates
an INA260 over I2C, choose `lto-vlastik`
or `naion-vlastik` according to the actual battery. Example for **one LTO
cell in series**:

```text
cz.health battery lto-vlastik
cz.health battery.cells 1
cz.health current on
```

Wait about one minute for the new sample, then check `cz.health battery` and
`cz.health check sensors`.

Set the series-cell count to match the actual wiring. Changing chemistry or
cell count clears RAM samples and schedules a fresh first sample after about
one minute. Diagnostics show the last measurement; after changing a channel
or wiring, wait for another sample. Sensors are discovered at boot, so restart
the repeater after connecting a device that was absent at startup.

Example `cz.health check sensors` output:

```text
sensors on, n 1, age 29s, V bms, BMS ok, INA not detected, temp mcu, current ok, humidity unavailable
```

- `V bms` means the last voltage sample came from the BMS; `V ina` indicates
  another INA source and `V adc` the board's analog input.
- `BMS ok` means voltage and current were read successfully through an
  INA260 interface for the last sample. If you have connected Vlastik's BMS,
  this is consistent with its expected operation. Firmware cannot
  reliably identify the manufacturer or device type; another compatible device
  can produce the same result.
- `current ok` means current is available in the last sample; `temp mcu` is
  the MCU temperature and `humidity unavailable` means no humidity reading.
- **In `lto-vlastik` and `naion-vlastik` modes, the `INA` diagnostic field
  does not independently check another INA sensor.** Therefore `INA not
  detected` alone does not prove another INA is absent. `BMS unavailable`
  means the selected BMS read failed; voltage may fall back to INA or ADC,
  while BMS current remains unavailable.

`cz.health ina.channel 1`, `2` or `3` selects the physical INA3221 channel
for **voltage**; `cz.health ina.channel` shows the saved choice. Use channel 1
for a single-channel INA219. This does not change the BMS channel or select
the current source. When BMS is available and `lto-vlastik` or `naion-vlastik`
is selected, its voltage takes priority; the INA3221 channel can be used for
fallback voltage. This command does not select a particular I2C device; with
multiple INA sensors, detection and firmware ordering affect selection.

### 4. Temperature, current and sensor validation

| Command | Meaning |
|---|---|
| `cz.health temperature auto` | External sensor, with supported MCU fallback. |
| `cz.health temperature mcu` | MCU only, if supported. |
| `cz.health temperature sensor` | External sensor only, otherwise unavailable. |
| `cz.health temperature off` | Omit temperature from CzHealth. |
| `cz.health current on` / `cz.health current off` | Enable/disable health current data. |
| `sensor list` | Available sensor setting entries, where supported. |

Humidity is collected when supported and collection is enabled; no separate
switch. Sensors are detected at boot; attaching a previously absent device
requires restarting for discovery.

The firmware checks bus failures, NaN and device-specific ranges. A temperature step
over **20 °C** triggers up to two fresh checks about one second apart.
At least two valid readings must agree within **5 °C**. Confirmed real changes
are accepted; otherwise the reading is unavailable. Repeated failures trigger
bounded recovery attempts. Auto mode can fall back to MCU. MCU and external
sensors are not treated as measurements of the same physical location.

### 5. CONFIG and regions

`cz.health config on|off` controls the independent report. Home/default/flood
regions include `*` when applicable. The board type is also included when
space permits. The server learns the firmware version from the report itself;
CONFIG no longer carries it.

- Region edits trigger comparison; the current RAM state is read again before sending.
- Changed configuration at most once per **24 hours**; unchanged confirmation after **7 days**.
- Initial report after about **1–2 hours**, with more than one hour separation
  from sensor reports and reboot protection.
- If space is insufficient, home/default names and ordered additional regions
  are retained and the report is marked incomplete.
- CONFIG is controlled independently of sensors. Manual `cz.health send`
  does not send CONFIG; no separate manual CONFIG command.

| Standard command | Meaning |
|---|---|
| `region` | List regions. |
| `region home` / `region default` | Read home/default region. |
| `region put <name> [parent]` | Create a region. |
| `region allowf <name>` / `region denyf <name>` | Allow/deny region flooding. |
| `region save` | Persist region edits across reboot. |
| `region load` | Load regions line by line (see below). |

For `*`, the flood permission controls handling of unscoped traffic.

During `region load`, send indented lines (the indent depth sets the parent);
the repeater acknowledges each one (`OK …` or `Err - line ignored`). A blank
line applies the loaded regions. A line without indent, or **60 seconds**
without a line, cancels the load and leaves the regions unchanged
(`Err - region load cancelled, regions unchanged`). The cancelling line is
not run as a command; later commands are handled normally.

### 6. CzGuard — local protection

| Command | Meaning |
|---|---|
| `cz.guard` / `cz.guard help` | Status / help. |
| `cz.guard on` / `cz.guard off` | Enable/disable local automatic protection. |
| `cz.guard dryrun on` / `cz.guard dryrun off` | Observe instead of applying local automatic penalties. |
| `cz.guard stats` | Protection counters. |
| `cz.guard list [page]` | Penalized sources, four per page. |
| `cz.guard unban <hex>` | Remove matching local penalties. |
| `cz.guard allow` | List local exceptions. |
| `cz.guard allow add <c\|a\|r\|n> <hex>` | Add a local exception. |
| `cz.guard allow del <c\|a\|r\|n> <hex>` | Remove a local exception. |
| `cz.guard reset` | Clear tracked streams and local penalties. |

`c` = channel messages, `a` = adverts, `r` = flood requests,
`n` = anonymous requests. A prefix may match more than one source.
Dryrun concerns local automation and does not disable every protection
mechanism. Treat `save failed` as a persistence failure, not a saved change.

In v1.18.0.03 only the local automation decides. Central blocks and
exceptions and their commands (`cz.guard central`, `inject`, `key`, `send`)
are gone; files from earlier versions are deleted at boot.

The repeater sends a short report about its protection: the first one 5–30
minutes after boot, then only when a ban starts or ends (at most every
6 hours) and once a day. A limit, a test day or a higher ban stage does not
trigger a report. A new login lock (see below) triggers a report within a few
minutes, at most every 6 hours.

#### Protection against password guessing

| Command | Meaning |
|---|---|
| `cz.guard login` / `cz.guard login help` | Status (limits, locked keys, wrong passwords, refused attempts, locks) / help. |
| `cz.guard login on` / `cz.guard login off` | Enable/disable login protection. Default: on. |
| `cz.guard login list` | Locked keys: start of the key, time left, number of wrong passwords. |
| `cz.guard login unlock` | Clear all login locks. |
| `cz.guard login key <3-100>` | Wrong passwords from one key that lock it. Default 10. |
| `cz.guard login all <5-250>` | Wrong passwords from all keys together that lock every unknown key. Default 30. |
| `cz.guard login window <5-120>` | Window in minutes in which wrong passwords are counted. Default 15. |
| `cz.guard login lock <5-240>` | Length of the first lock in minutes. Default 15. |
| `cz.guard login flood` | Show the forwarding limit for flood logins. |
| `cz.guard login flood <count> <min> <ban min>` | Set it: count 5–250, window 5–240 min, ban 10–2880 min. Default 30 per 60 min, ban 720 min (12 h). |

- Applies to admin and guest logins. Keys already in the ACL as admin are
  never locked; a new admin can always be added over the serial console.
- During a lock the repeater does not check the password at all, refuses even
  a correct one and does not reply, just as with a wrong password.
- Another lock within 24 hours of the previous one lasts twice as long: at
  most 12 hours for one key, at most 2 hours for all keys. A correct password
  outside a lock resets that key's counter.
- Locks and counters are kept in memory only and a reboot clears them; the
  settings are saved.
- If the repeater still has the default admin password, `cz.guard` and
  `cz.guard login` show `! default admin password - change it`. Change it with
  `password <new password>`.
- **Flood logins:** the repeater forwards flood login requests to one target
  repeater (from anyone) only up to the set limit; above it, it stops forwarding
  them for the ban time. This limit belongs to `cz.guard on/off` and
  `cz.guard dryrun`, not to `cz.guard login on/off`.

### 7. Power saving, forwarding, watchdog and guests

| Command | Meaning |
|---|---|
| `get repeat` / `set repeat on` / `set repeat off` | Read/control forwarding other packets. |
| `get tx` / `set tx <dBm>` | Radio-chip power, board-dependent range. |
| `get reboot.interval` / `set reboot.interval 0` | Read scheduled reboot interval / disable. |
| `gps` / `gps on` / `gps off` | GPS use when compiled in. |
| `cz.telemetry.guest.hide [on\|off]` | Hide battery/temperature from ordinary guest telemetry; default OFF. |

Default chip power depends on the board; saved explicit values win.
Chip power differs from antenna output behind an external PA. Guest hiding
preserves administrator access and does not conceal separate CzHealth reports.

#### PowerSaving and RX power saving (RXPS)

| Command | Meaning |
|---|---|
| `powersaving` | PowerSaving state (`on` / `off`). |
| `powersaving on` | Enable. nRF52 at once; ESP32 after 5 minutes and the serial console turns off; bridge boards not supported. |
| `powersaving off` | Disable. |
| `get radio.rxps` | RX power saving status (output explained below). |
| `set radio.rxps off` | Disable RXPS; the radio receives continuously. |
| `set radio.rxps on` / `set radio.rxps conservative` | Careful saving, level 3. |
| `set radio.rxps balanced` | Balanced saving, level 6. Default for a fresh install. |
| `set radio.rxps max` | Highest level within the datasheet limits, level 8. |
| `set radio.rxps level <1-10> [preamble <16\|32>]` | A specific level, optionally with the preamble length. |
| `set radio.rxps max preamble <16\|32>` | Max with the given preamble. |
| `set radio.rxps overdrive` / `set radio.rxps riskyWorkingMax` | Level 9 / 10, outside the datasheet timing. **Not recommended**; level 10 may already lose packets. |
| `set radio.rxps <rx_us> <sleep_us>` | Manual receive and sleep periods in µs (1,000–30,000,000). Experts only. |

- PowerSaving and RXPS are separate settings; support depends on the board
  and radio. RXPS works on SX1262 and LR1110; LR1121, LR2021 and SX127x
  receive continuously (`supported=no`).
- **Levels:** a higher level means longer sleep between receive windows and
  lower consumption, but less margin to catch the start of a packet. For an
  ordinary repeater we recommend `balanced`.
- **Preamble:** `16` is safe for all senders in the network; `32` allows longer
  sleep but assumes senders with a longer preamble. Without it the value
  follows the SF.
- **Default:** a fresh install uses `balanced` (level 6, preamble 16). A saved
  choice, including `off`, is kept across updates.
- **`get radio.rxps` output:** `desired` = saved on/off; `effective` = `armed`
  (saving active) or `continuous` (continuous receive); `supported` = whether
  the radio can do it; `err` / `fail` = last error and failure count; `level`,
  `preamble`, `rx` / `sleep` (µs) = settings; `catch` = preamble symbols the
  repeater still catches.
- **Errors:** a setting that does not fit the active SF/BW is rejected. After
  a radio change the repeater stays in continuous receive with `err=-10001`
  and keeps the saved choice. After three failures it stays in continuous
  receive until the setting changes. `set radio` takes effect after a reboot.
- **PowerSaving and GPS:** with PowerSaving on, GPS runs at most 10 minutes
  and then sleeps up to a day. `gps` shows when it will sleep or wake and when
  it last synchronised the clock.
- The new automatic low-battery power-reduction mode is not part of this release.

#### CzWatchdog — radio silence watchdog

CzWatchdog is a MeshCore Czech feature. When the repeater neither receives nor
transmits anything for the set time, the radio has most likely stalled, so the
repeater restarts itself. Useful for repeaters in hard-to-reach places.

| Command | Meaning |
|---|---|
| `cz.watchdog` | Summary: on/off, limit in minutes and restart count. |
| `cz.watchdog.silence` | Is the watchdog on? |
| `cz.watchdog.silence on` / `cz.watchdog.silence off` | Enable / disable. Default: off. |
| `cz.watchdog.silence.timeout` | Silence limit in minutes. |
| `cz.watchdog.silence.timeout <1-240>` | Set the limit. Default 60 minutes. |
| `cz.watchdog.reboots` | How many times the watchdog restarted the repeater. |

- Real received packets and completed transmissions reset the silence timer.
- Choose the limit for the traffic around you: in a quiet area a limit that
  is too short restarts even a working repeater.
- The counter counts only watchdog restarts. On nRF52 it survives an ordinary
  restart (up to 255); power loss or OTA may clear it, on other platforms it
  lasts until the next restart.
- The settings are saved and survive restarts and updates.

### 8. Clock and updates

`clock` displays time; administrator `clock sync` synchronizes with the client
where supported. `gps sync` uses available GPS. `cz.health check time` shows
provenance/trust. Without trusted time, reports rely on receive time/sample
age; manual reservations always use uptime.

#### Replay protection of commands

The repeater accepts a remote admin command only with a newer timestamp than
the last command from the same client. In v1.18.0.03 it keeps that timestamp
across restarts, so a recorded old command (such as `reboot`) cannot be sent
again. If the repeater refuses you after the phone or companion clock was set
back, fix the clock or log in with the password from another (or reset)
companion.

#### Android nRF52 Bluetooth DFU

Before `start ota`, set nRF Device Firmware Update to:

| Option | Value |
|---|---|
| Packets receipt notification | **ON** |
| Number of packets | **8** |
| Request high MTU | **OFF** |
| Disable resume | **ON** |
| Force scanning | **ON** |
| Prepare object delay | **0 ms** |

Select the exact board ZIP. Check LoRa and `ver` even after Completed.
These settings resolved the reported ProMicro/XIAO problem without changing
firmware. [Details and MeshCore guide](README.md#android-bluetooth-dfu-nrf52).

For issue reports, include board, `ver`, update method, command and exact
response. Do not share passwords.
[Changelog](CHANGELOG.md#english).
