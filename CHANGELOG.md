# Změny — MeshCore Czech

[EN — English version below](#english)

Přehled komunitních úprav vůči původnímu
[MeshCore](https://github.com/meshcore-dev/MeshCore).
Soubory vydání: [Releases](https://github.com/mesh-cz/MeshCore-Czech/releases).
Použití: [README](README.md) a [CLI manuál](CLI-manual.md).

## RC.10 — `v1.18.0.02rc.10` — příprava vydání

Komunitní vydání pro repeatery. Plánovaná stabilní verze je `v1.18.0.03`.

### Ruční odesílání a diagnostika

- `cz.health send` nejvýše **jednou za 4 hodiny**, s rezervací uloženou
  před odesláním. Restart během blokace znamená nové čtyři hodiny od startu.
  Změnou hodin se čekání nezkrátí. Automatické zprávy mají vlastní pravidla.
- Odmítnuté požadavky běžně nezapisují do flash; chyby uložení blokují send.
- Nové `cz.health check [config|sensors|time]`: důvody čekání, ruční limit,
  dostupnost čidel, skutečný zdroj napětí, stáří vzorku a původ času.

### Měření a čas

- Kontrola čidel ve společné vrstvě pro běžnou telemetrii i CzHealth: chyby
  čtení, NaN a hodnoty mimo měřicí rozsah se neposílají jako platné údaje.
- Teplotní změna nad **20 °C** vyvolá až dvě další čerstvé konverze;
  alespoň dvě platné hodnoty musí souhlasit do **5 °C**. Skutečná potvrzená
  změna se přijme; bez shody zůstává údaj nedostupný.
- Opakované chyby vedou k omezené obnově postiženého čidla. Ověřování je
  plánované tak, aby dlouhé čekání na měření neblokovalo rádiovou smyčku.
- INA/BMS čtení kontroluje dokončení I2C přenosů. Neztrácí se znaménko proudu;
  chybějící proud zvolené BMS se neoznačuje jako nula.
- Přechod na jiný zdroj napětí/teploty sám nevyvolává falešný alarm porovnáním
  nesrovnatelných zdrojů.
- Rozlišuje se administrátorská/GPS synchronizace a neověřený čas. Bez
  důvěryhodné synchronizace se nepřenáší důvěryhodně vypadající absolutní
  datum; příjemce použije čas příjmu a stáří vzorků. Ruční limit využívá uptime.

### Konfigurace a desky

- Konfigurační hlášení může kromě regionů obsahovat také verzi
  firmwaru a typ desky.
- README a manuál obsahují nastavení Android nRF52 DFU: **PRN ON/8,
  high MTU OFF, Disable resume ON, Force scanning ON, delay 0 ms**.
  Toto nastavení vyřešilo hlášený neúspěšný start na ProMicro/XIAO bez změny FW.

### Stav ověření

- **221/221 nativních testů** prošlo.
- Správce potvrdil funkční testy RC.10 podle místního postupu na XIAO nRF52,
  SenseCAP Solar P1, ProMicro a Heltec V4.

### Denní přehled o repeateru

- Bateriové uzly posílají denní DAY každých **23–25 hodin** i při stabilním
  napětí LiFePO4/LTO. Týdenní SUMMARY vyžaduje externí napájení, stabilitu
  a nepřítomnost vzorků proudu.
- Úspornější hlášení o stavu repeateru omezují zbytečný provoz v síti.
- Regionový blok přenáší každý název jednou se všemi rolemi. Změny nastavení
  spouštějí porovnání; před odesláním se čte aktuální RAM stav.
  Změnový limit 24 hodin a potvrzení po 7 dnech zůstávají.
- Napětí nad **65,535 V** je výslovně označeno jako nepodporované/nedostupné.

### Komunitní funkce a podpora desek

- PowerSaving 18, místní CzGuard a jeho automatické reporty, CzWatchdog.
- Jednotné příkazy `cz.guard`, `cz.health` a `cz.watchdog…` bez starých aliasů.
- CzHealth: nastavitelný typ baterie, články v sérii, skutečné zdroje
  napětí a podpora Vlastíkovy BMS přes emulaci INA260.
- Rozšiřitelný CONFIG s domácím, výchozím a flood regiony; samostatné
  ovládání `cz.health config on|off`.
- ProMicro podporuje BME280 a čidla INA včetně INA260.
- YomaMesh obsahuje úpravy **RadekT (`cncnet-info`)** pro E22P:
  AGC fallback 16 s při uložené nule, výchozí výkon čipu 5 dBm
  a vypnutá startovní napěťová blokace.
- Volitelné skrytí baterie/teploty hostům přes `cz.telemetry.guest.hide`,
  výchozí OFF. Aktualizace zachovává uložené předvolby.

### Základ firmwaru

- Základ MeshCore 1.18.0; PowerSaving 18 podle práce
  [IoTThinks / EasySkyMesh](https://github.com/IoTThinks/EasySkyMesh).
- CzHealth: historie po 30 minutách, denní/týdenní reporty, uptime a příčina restartu.
- Ochrana sítě omezuje četnost přeposílání senzorových hlášení od jednoho zdroje.
- Výchozí pravidla mesh-cz pro hash cesty a doplnění regionu `cz`.
- Obnova rádia a volitelný Radio Silence Watchdog.

---

## English

Community changes relative to upstream
[MeshCore](https://github.com/meshcore-dev/MeshCore).
Downloads: [Releases](https://github.com/mesh-cz/MeshCore-Czech/releases).
Usage: [README](README.md#english), [CLI manual](CLI-manual.md#english).

### RC.10 — `v1.18.0.02rc.10` — release preparation

Community repeater release. The planned stable version is `v1.18.0.03`.

#### Manual reports and diagnostics

- `cz.health send` at most **once per four hours**, with a reservation saved
  before queueing. Rebooting during a reservation starts another four-hour
  uptime wait. Clock changes cannot shorten it. Automatic reports have their own rules.
- Rejected requests normally do not write flash; storage failures block sending.
- New `cz.health check [config|sensors|time]`: delays, manual eligibility,
  sensor availability, voltage source, sample age and clock provenance.

#### Measurements and time

- Shared validation for ordinary telemetry and CzHealth rejects read errors,
  NaN and readings outside each sensor's supported range.
- Temperature steps over **20 °C** trigger up to two fresh conversions;
  at least two valid values must agree within **5 °C**. Confirmed real
  changes are accepted; otherwise the reading stays unavailable.
- Repeated errors trigger bounded recovery of the affected device.
  Scheduled retries avoid long measurement waits blocking radio work.
- INA/BMS reads check completed I2C transactions. Current remains signed;
  missing selected-BMS current is not reported as zero.
- Voltage/temperature source switches alone do not trigger false alarms
  by comparing unlike sources.
- Admin/GPS synchronization is distinguished from unverified clocks.
  Without trusted synchronization, absolute report dates are omitted;
  receivers use reception time/sample age. Manual limits use uptime.

#### Configuration and boards

- Configuration reports may include firmware version and board type
  alongside regions.
- README/manual document Android nRF52 DFU settings: **PRN ON/8,
  high MTU OFF, Disable resume ON, Force scanning ON, delay 0 ms**.
  These settings resolved the reported ProMicro/XIAO boot failure without changing firmware.

#### Validation status

- **221/221 native tests** passed.
- The maintainer reported successful RC.10 checklist tests on XIAO nRF52,
  SenseCAP Solar P1, ProMicro and Heltec V4.

#### Daily repeater health overview

- Battery nodes send DAY every **23–25 hours**, including stable LiFePO4/LTO
  voltages. Weekly SUMMARY requires external power, stability and no current samples.
- More compact device-health reports reduce unnecessary network traffic.
- Compact regions send each name once with all roles. Edits trigger comparison;
  current RAM state is read before sending. Daily change limits and weekly
  unchanged confirmation remain.
- Readings above **65.535 V** are explicitly marked unsupported/unavailable.

#### Community features and board support

- PowerSaving 18, local CzGuard and automatic reports, CzWatchdog.
- Canonical `cz.guard`, `cz.health`, `cz.watchdog…` commands without old aliases.
- CzHealth: configurable battery chemistry, series cells, actual voltage
  provenance and Vlastik BMS support through INA260 emulation.
- Extensible CONFIG with home/default/flood regions and independent
  `cz.health config on|off` control.
- ProMicro supports BME280 and INA sensors including INA260.
- YomaMesh integrates **RadekT (`cncnet-info`)** E22P changes:
  16-second AGC fallback for saved zero, 5 dBm default chip power
  and disabled boot-voltage lock.
- Optional guest battery/temperature hiding with `cz.telemetry.guest.hide`,
  default OFF. Updates retain saved preferences.

#### Firmware foundation

- MeshCore 1.18.0 base; PowerSaving 18 from
  [IoTThinks / EasySkyMesh](https://github.com/IoTThinks/EasySkyMesh).
- CzHealth half-hour sampling, daily/weekly reports, uptime and restart reason.
- Rate-limited relaying of sensor reports from a single source.
- mesh-cz defaults for path hashes and the `cz` region.
- Radio recovery and optional Radio Silence Watchdog.
