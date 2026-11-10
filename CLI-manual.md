# CLI manuál — MeshCore Czech RC.10

[EN — English version below](#english)

Manuál připravovaného `v1.18.0.02rc.10` pro **repeatery**. Příkazy zadávejte
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
Probíhající ověřování čidla může vzorek odložit. Historie je v RAM; restart
ji nezařadí zpět z předchozího běhu. Vypnutí sběru neznamená vymazání již
uložených vzorků. Chybějící hodnoty nejsou naměřené nuly.

### Automatické zprávy

- Bateriový uzel bez příznaku externího napájení posílá DAY každých
  **23–25 hodin**, i při stabilním napětí LiFePO4 nebo LTO.
- Týdenní SUMMARY vyžaduje příznak externího napájení, stabilní napětí/teplotu
  a nepřítomnost dostupných vzorků proudu. Samotné nabíjení nestačí.
- První automatická senzorová zpráva se plánuje **30–90 minut po startu**;
  u stabilního externě napájeného uzlu může jít o SUMMARY.
- Podmínkou jsou zapnuté CzHealth a senzory, dostupné vzorky
  a splněné podmínky odeslání. Alarmy mají vlastní plán.

### Ruční send — nové pravidlo RC.10

Ruční odeslání je nejvýše **jednou za 4 hodiny**. Před odesláním se uloží
rezervace do flash. První požadavek bez aktivní rezervace může projít;
restart během rezervace znamená **nové 4 hodiny čekání od startu**.
Změna hodin limit nezkrátí. Uplynutí blokace se trvale zaznamená.

Odmítnuté požadavky běžně nevytvářejí další zápisy. Při chybě úložiště
se odeslání odmítne. Rezervace zůstává i při následném selhání zařazení zprávy;
automatická hlášení tento ruční limit neblokuje. Po CONFIG může být nutný
odstup více než hodinu. `OK - sent` neznamená potvrzené doručení na server.

### Diagnostika — nové v RC.10

| Příkaz | Význam |
|---|---|
| `cz.health check` | Stav odesílání, čekání, ruční limit a počet vzorků. |
| `cz.health check config` | Stav CONFIG a nejbližší způsobilý termín. |
| `cz.health check sensors` | Zdroj napětí/teploty, BMS/INA, dostupnost proudu/vlhkosti a stáří vzorku. |
| `cz.health check time` | Zdroj času, jeho důvěryhodnost, čas a uptime. |

Diagnostika sama neodesílá nový CzHealth report a nepřidává nový vzorek.

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

RC.10 kontroluje chyby čtení, NaN a rozsah konkrétního čidla. Skok teploty
nad **20 °C** vyvolá až dvě další čerstvá měření přibližně po sekundě.
Alespoň dvě platné hodnoty musí souhlasit do **5 °C**. Potvrzená skutečná
změna je přijata; bez shody je údaj nedostupný a opakované chyby vedou
k omezenému pokusu o obnovu čidla. V auto režimu lze použít MCU.
MCU a venkovní čidlo se neporovnávají jako stejné místo měření.

## 5. CONFIG a regiony

`cz.health config on|off` řídí samostatnou zprávu. Obsahuje domácí, výchozí
a flood regiony včetně `*`, pokud má příslušnou roli. Podle dostupného místa obsahuje také verzi FW a typ desky.

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

U `*` určuje flood oprávnění zacházení s provozem bez regionového omezení.

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

## 7. Úspory, Repeat, watchdog a hosté

| Příkaz | Význam |
|---|---|
| `powersaving` / `powersaving on` / `powersaving off` | Stav / zapnutí / vypnutí běžného PowerSaving. |
| `get radio.rxps` | Stav RX power saving. |
| `set radio.rxps balanced` / `set radio.rxps off` | Vyvážené RXPS / vypnutí, pokud je podporované. |
| `get repeat` / `set repeat on` / `set repeat off` | Stav a povolení přeposílání cizích paketů. |
| `get tx` / `set tx <dBm>` | Výkon rádiového čipu; rozsah závisí na desce. |
| `get reboot.interval` / `set reboot.interval 0` | Interval plánovaného restartu / jeho vypnutí. |
| `gps` / `gps on` / `gps off` | Používání GPS v sestavení s podporou. |
| `cz.watchdog` | Stav watchdogu rádiového ticha. |
| `cz.watchdog.silence [on\|off]` | Zjistit/nastavit sledování ticha, výchozí OFF. |
| `cz.watchdog.silence.timeout [1-240]` | Limit minut, výchozí 60. |
| `cz.watchdog.reboots` | Počet restartů vyvolaných tímto watchdogem. |
| `cz.telemetry.guest.hide [on\|off]` | Skrytí baterie/teploty v běžné telemetrii hostů, výchozí OFF. |

PowerSaving a RXPS jsou samostatné volby; podporu určuje deska a rádio.
Výchozí výkon rádiového čipu závisí na desce; uložený vlastní výkon
se zachová. Výkon čipu neodpovídá výkonu za externím PA.
Nový automatický úsporný režim při nízké baterii není součástí RC.10.

Watchdog obnovují skutečný RX a dokončený TX. Jeho počítadlo nepočítá všechny
restarty a po odpojení napájení nebo OTA se může vynulovat.
Skrytí údajů hostům zachovává přístup správce a neukrývá samostatné CzHealth zprávy.

## 8. Čas a aktualizace

`clock` zobrazí čas; administrátorské `clock sync` jej synchronizuje s klientem,
pokud to používané rozhraní podporuje. `gps sync` využije dostupné GPS.
`cz.health check time` ukáže původ a důvěryhodnost. Bez důvěryhodného času
se report opírá o čas příjmu a stáří vzorků; ruční limit používá uptime.

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

CLI manual for the planned **`v1.18.0.02rc.10` repeater** release. Use an
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

First sample after about **60 seconds**, then every **30 minutes**; sensor
confirmation may defer it. History is in RAM and is not restored across reboot.
Disabling collection does not necessarily clear previous RAM samples.
Missing measurements are not measured zeroes.

#### Automatic reports

- Without the external-power flag: DAY every **23–25 hours**, including
  flat-voltage LiFePO4/LTO batteries.
- Weekly SUMMARY requires external power, stable voltage/temperature and no
  available current samples. Charging alone is insufficient.
- First automatic sensor report is scheduled **30–90 minutes after boot**;
  stable externally powered nodes may send SUMMARY.
- Health and sensors must be enabled, samples available and sending
  conditions met. Alarms have their own schedule.

#### Manual send — RC.10 rules

At most **once per four hours**, with a flash reservation committed before
queueing. The first request without an active reservation may proceed.
A reboot during a reservation starts a **new four-hour uptime wait**.
Changing the clock cannot shorten it. Expiry is saved persistently.

Rejected requests normally do not write again. Storage errors block sending;
a reservation survives a subsequent queueing failure. The manual limit does
not block automatic reports. CONFIG may require a gap longer than one hour.
`OK - sent` does not confirm server delivery.

#### Diagnostics — new in RC.10

| Command | Meaning |
|---|---|
| `cz.health check` | Sending state, delays, manual reservation and sample count. |
| `cz.health check config` | CONFIG state and earliest eligible time. |
| `cz.health check sensors` | Voltage/temperature sources, BMS/INA, current/humidity availability and sample age. |
| `cz.health check time` | Clock source, trust, time and uptime. |

These checks do not generate a new health report or sample.

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

RC.10 checks bus failures, NaN and device-specific ranges. A temperature step
over **20 °C** triggers up to two fresh checks about one second apart.
At least two valid readings must agree within **5 °C**. Confirmed real changes
are accepted; otherwise the reading is unavailable. Repeated failures trigger
bounded recovery attempts. Auto mode can fall back to MCU. MCU and external
sensors are not treated as measurements of the same physical location.

### 5. CONFIG and regions

`cz.health config on|off` controls the independent report. Home/default/flood
regions include `*` when applicable. Firmware version and board type
are also included when space permits.

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

For `*`, the flood permission controls handling of unscoped traffic.

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

### 7. Power saving, forwarding, watchdog and guests

| Command | Meaning |
|---|---|
| `powersaving` / `powersaving on` / `powersaving off` | Read/enable/disable normal PowerSaving. |
| `get radio.rxps` | Read RX power saving. |
| `set radio.rxps balanced` / `set radio.rxps off` | Balanced RXPS / disable, where supported. |
| `get repeat` / `set repeat on` / `set repeat off` | Read/control forwarding other packets. |
| `get tx` / `set tx <dBm>` | Radio-chip power, board-dependent range. |
| `get reboot.interval` / `set reboot.interval 0` | Read scheduled reboot interval / disable. |
| `gps` / `gps on` / `gps off` | GPS use when compiled in. |
| `cz.watchdog` | Radio silence watchdog status. |
| `cz.watchdog.silence [on\|off]` | Read/control silence monitoring; default OFF. |
| `cz.watchdog.silence.timeout [1-240]` | Minutes, default 60. |
| `cz.watchdog.reboots` | Restarts triggered by this watchdog. |
| `cz.telemetry.guest.hide [on\|off]` | Hide battery/temperature from ordinary guest telemetry; default OFF. |

PowerSaving and RXPS are separate, board/radio-dependent settings.
Default chip power depends on the board; saved explicit values win.
Chip power differs from antenna output behind an external PA. The new
automatic low-battery power-reduction mode is not part of RC.10.

Real RX and completed TX refresh the watchdog timer. Its counter excludes
other restart causes and may reset on power loss/OTA. Guest hiding preserves
administrator access and does not conceal separate CzHealth reports.

### 8. Clock and updates

`clock` displays time; administrator `clock sync` synchronizes with the client
where supported. `gps sync` uses available GPS. `cz.health check time` shows
provenance/trust. Without trusted time, reports rely on receive time/sample
age; manual reservations always use uptime.

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
