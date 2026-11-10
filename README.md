# MeshCore Czech

[EN — English version below](#english)

> [!WARNING]
> **Testovací vydání — při OTA aktualizaci může dojít k problémům se spuštěním repeateru.**
> I po hlášení **100 % / Completed** nemusí repeater naběhnout a může být nutné nahrát firmware znovu přes USB.
>
> - Nahrávejte jej především na repeatery, ke kterým máte **fyzický přístup a možnost obnovy kabelem**. Vzdáleně nedostupná zařízení aktualizujte až s připravenou možností zásahu.
> - Před aktualizací si **exportujte a bezpečně uložte zálohu nastavení a identity repeateru**, pokud to používaná aplikace umožňuje. Stáhněte si také dosavadní funkční firmware pro svou desku pro případ obnovy.
> - Pro minimalizaci problémů při Bluetooth OTA na nRF52 **použijte doporučené nastavení nRF Device Firmware Update uvedené níže**: [nastavení DFU](#bluetooth-dfu-na-androidu-nrf52). Samotné dokončení přenosu nenahrazuje kontrolu funkčnosti přes LoRa a příkazem `ver`.

Komunitní firmware pro repeatery sítě mesh-cz. Jde o fork původního projektu
[MeshCore](https://github.com/meshcore-dev/MeshCore), rozšířený o funkce pro
českou komunitu v České republice.

**RC.10 — připravované vydání `v1.18.0.02rc.10`.** Toto je dokumentace k přípravě
vydání; dostupné soubory a jejich skutečné označení vždy ověřte v
[Releases](https://github.com/mesh-cz/MeshCore-Czech/releases).
Plánovaná stabilní verze je `v1.18.0.03`.

## Rychle po nahrání firmwaru

1. Nahrajte firmware určený přesně pro svou desku. U nRF52 nejprve přečtěte
   [nastavení Bluetooth DFU](#bluetooth-dfu-na-androidu-nrf52).
2. Přihlaste se jako **administrátor** a příkazem `ver` ověřte verzi.
3. Zvolte skutečný typ baterie a počet článků v sérii. Příklad pro 1S Li-ion:

   ```text
   cz.health battery liion
   cz.health battery.cells 1
   cz.health on
   cz.health sensors on
   cz.health config on
   ```

   Další typy: `auto`, `lifepo4`, `lto`, `naion`, `lead`, `other`,
   `lto-vlastik`, `naion-vlastik`. Výchozí je `auto` a jeden článek;
   v režimu auto odhad typu přenechává firmware přijímající aplikaci.
4. Vyčkejte alespoň přibližně minutu na první vzorek a zkontrolujte:

   ```text
   cz.health check
   cz.health check sensors
   cz.health battery
   cz.health send
   ```

   `send` odešle uložené vzorky, pokud jsou splněné podmínky. Další ruční
   odeslání je možné nejdříve za **4 hodiny**. Restart během blokace spustí
   nové čtyřhodinové čekání od startu. Další důvod čekání vysvětlí
   `cz.health check`.
5. Údaje můžete po přijetí hlášení sledovat v komunitní mapě nebo
   v CoreScope. Nemusejí se objevit okamžitě; jejich dostupnost závisí
   na pokrytí sítě. Stav zjistíte příkazem `cz.health check`.

Další nastavení a příklady: **[CLI manuál](CLI-manual.md)**.

## K čemu slouží provozní údaje

CzHealth pomáhá provozovateli včas poznat slábnoucí baterii, neobvyklou
teplotu nebo restart repeateru. Sdílí technické údaje o zařízení: naměřené
hodnoty, typ baterie a počet článků, dobu běhu a důvod restartu. Samostatně
lze sdílet nastavené regiony, verzi firmwaru a typ desky.

Tyto údaje mohou být veřejně zobrazené v komunitní mapě a CoreScope.
Sdílení je ve výchozím stavu zapnuté a provozovatel jej může ovládat:
`cz.health off` vypne sběr a senzorová hlášení, `cz.health config off`
vypne sdílení konfigurace. Pro vypnutí obou částí použijte oba příkazy.

## Co firmware nabízí

- **PowerSaving a RX power saving:** úsporné režimy podle možností desky a rádia.
- **CzGuard:** místní automatická ochrana před nadměrným provozem; stav a činnost
  zjistíte přes `cz.guard`, `cz.guard stats` a `cz.guard list`.
- **CzHealth:** historie napětí, teploty a dostupného proudu/vlhkosti, typ baterie,
  články v sérii, zdroje měření, uptime a důvod restartu.
- **Konfigurace repeateru:** domácí, výchozí a flood regiony v rozšiřitelné zprávě;
  RC.10 podle volného místa doplňuje také verzi firmwaru a název desky.
- **CzWatchdog:** volitelné sledování rádiového ticha; výchozí stav je vypnuto.

### Novinky RC.10

- Ruční send nejvýše jednou za **4 hodiny**, s uložením blokace přes restart.
- Diagnostika `cz.health check [config|sensors|time]`.
- Ověřování čidel v běžné telemetrii i CzHealth. Teplotní skok nad **20 °C**
  vyvolá kontrolní měření; alespoň dvě hodnoty musí souhlasit do **5 °C**.
  Neplatné údaje se nepovažují za platná měření.
- Kontrola čtení INA/BMS a označení skutečného zdroje napětí. Chybějící proud
  není vykazován jako nula; změna zdroje sama nezpůsobí falešný alarm skoku napětí.
- Evidence původu času; bez důvěryhodné synchronizace příjemce použije čas příjmu
  a stáří vzorků místo nedůvěryhodného data repeateru.

Podrobný přehled vydání: **[CHANGELOG](CHANGELOG.md)**.

## Nahrání firmwaru

Použijte soubor pro konkrétní desku z
[Releases](https://github.com/mesh-cz/MeshCore-Czech/releases).

| Platforma | Formát |
|---|---|
| nRF52 | `.uf2` přes USB bootloader nebo `.zip` pro Bluetooth DFU |
| ESP32 — aktualizace | `…-upgrade.bin` |
| ESP32 — čistá instalace | `…-freshInstall-merged.bin`; smaže celou flash včetně nastavení |
| RP2040 | `.uf2` |

Pro příslušné podporované postupy lze použít
[MeshCore Flasher](https://flasher.meshcore.io/). Rozhodující je vždy seznam
skutečně přiložených souborů vydání.

### Bluetooth DFU na Androidu (nRF52)

**V aplikaci nRF Device Firmware Update zkontrolujte před aktualizací:**

| Volba | Nastavení |
|---|---|
| Packets receipt notification | **ON** |
| Number of packets | **8** |
| Request high MTU | **OFF** |
| Disable resume | **ON** |
| Force scanning | **ON** |
| Prepare object delay | **0 ms** |

Na repeateru spusťte `start ota` a vyberte ZIP přesně pro svou desku.
Nevhodné nastavení může vést k hlášení **Completed**, ale repeater následně
nenaběhne. Samotných 100 % nepotvrzuje funkční start: ověřte LoRa a `ver`.
U hlášeného problému na ProMicro a XIAO nRF52 pomohlo toto nastavení bez
změny firmware. Postup odpovídá
[návodu MeshCore](https://blog.meshcore.io/2026/04/02/nrf-ota-update).

## Kde najdete data v CoreScope

Otevřete [CoreScope](https://analyzer.meshcore.website/).

| Místo | Co hledat |
|---|---|
| **Nodes — seznam** | Česká vlaječka před hvězdičkami označuje uzel s přijatými senzorovými zprávami CzHealth. Popis: „Komunitní FW MeshCore-Czech“. |
| **Nodes — detail repeateru** | Panel **MeshCore-Czech** nad **Recent Adverts**: přijaté hodnoty, baterie, zdroje, uptime, restart a grafy za 1/7/30/90 dní. |
| **Stejný panel po přijetí CONFIG** | Domácí, výchozí a flood regiony, čas konfigurační zprávy a případné označení neúplného seznamu. |
| **Map → Scope Config → FW MC Czech** | Filtr uzlů s přijatými senzorovými zprávami CzHealth; **All** vrátí všechny uzly. |

Vlaječka a filtr vycházejí z přijatých dat; neprokazují kompletní seznam
všech nahraných zařízení.

## Ověření a hlášení potíží

Správce potvrdil funkční testy RC.10 na XIAO nRF52, SenseCAP Solar P1,
ProMicro a Heltec V4.
Výsledek konkrétní desky nezaručuje všechny kombinace čidel a zapojení.

Při hlášení přiložte desku, výstup `ver`, způsob aktualizace, přesný příkaz
a odpověď. Pro CzHealth pomohou `cz.health check`, `cz.health check sensors`
a `cz.health check time`. Hesla neposílejte.

## Původ a licence

Základ projektu: [meshcore-dev/MeshCore](https://github.com/meshcore-dev/MeshCore).
Původní MIT text a autorství jsou v [license.txt](license.txt).

---

## English

> [!WARNING]
> **Test release — OTA updates may leave the repeater unable to boot.**
> Even after **100% / Completed**, the repeater may fail to start and require flashing again over USB.
>
> - Prefer repeaters you can **physically access and recover using a cable**. Update inaccessible remote devices only when an on-site recovery option is available.
> - Before updating, **export and securely save a backup of the repeater configuration and identity**, if your application supports it. Also download the previously working firmware for your exact board for recovery.
> - To minimize nRF52 Bluetooth OTA problems, **use the recommended nRF Device Firmware Update settings below**: [DFU settings](#android-bluetooth-dfu-nrf52). A completed transfer does not replace checking LoRa operation and `ver`.

Community firmware for mesh-cz repeaters. MeshCore Czech is a fork of
[MeshCore](https://github.com/meshcore-dev/MeshCore), extended with features
for the Czech community in the Czech Republic.

**RC.10 — release preparation for `v1.18.0.02rc.10`.** This documentation accompanies
release preparation; check [Releases](https://github.com/mesh-cz/MeshCore-Czech/releases)
for available files and their actual versions. The planned stable version is `v1.18.0.03`.

### Quick start after flashing

1. Flash the file for your exact board. For nRF52, first read the
   [Android Bluetooth DFU settings](#android-bluetooth-dfu-nrf52).
2. Log in as an **administrator** and check `ver`.
3. Choose the actual battery chemistry and series cell count. Example for 1S Li-ion:

   ```text
   cz.health battery liion
   cz.health battery.cells 1
   cz.health on
   cz.health sensors on
   cz.health config on
   ```

   Other choices: `auto`, `lifepo4`, `lto`, `naion`, `lead`, `other`,
   `lto-vlastik`, `naion-vlastik`. Defaults: `auto`, one cell. In auto mode,
   chemistry estimation is left to the receiving application.
4. Allow at least about one minute for the first sample, then check:

   ```text
   cz.health check
   cz.health check sensors
   cz.health battery
   cz.health send
   ```

   `send` transmits stored samples when eligible. Manual sends are limited
   to once per **4 hours**. A reboot during the reservation starts a new
   four-hour wait from boot. `cz.health check` explains other delays.
5. Once a report is received, you can view the readings on a community
   map or in CoreScope. They may not appear immediately and depend on
   network coverage. Use `cz.health check` to inspect the status.

More options: **[CLI manual](CLI-manual.md#english)**.

### What the operational data is for

CzHealth helps the operator spot a weakening battery, unusual temperature
or repeater restart. It shares technical device information: measurements,
battery chemistry and cell count, uptime and restart reason. Configured
regions, firmware version and board type can be shared separately.

These readings may be displayed publicly on community maps and in CoreScope.
Sharing is enabled by default and controlled by the operator: `cz.health off`
disables sampling and sensor reports; `cz.health config off` disables
configuration sharing. Use both commands to disable both parts.

### Features

- **PowerSaving and RX power saving:** energy-saving modes depend on the board/radio.
- **CzGuard:** local automatic protection against excessive traffic. Inspect
  `cz.guard`, `cz.guard stats` and `cz.guard list`.
- **CzHealth:** voltage, temperature and available current/humidity history,
  battery chemistry, series cells, measurement sources, uptime and restart reason.
- **Repeater configuration:** home/default/flood regions in an extensible report;
  RC.10 can also include firmware and board names when space permits.
- **CzWatchdog:** optional radio silence monitoring, disabled by default.

### New in RC.10

- Persistent **four-hour** manual-send reservation, surviving reboots.
- `cz.health check [config|sensors|time]` diagnostics.
- Sensor validation shared by telemetry and CzHealth. A temperature step over
  **20 °C** triggers confirmation; at least two values must agree within **5 °C**.
  Invalid values are not presented as valid measurements.
- Checked INA/BMS transactions and actual voltage-source reporting. Missing
  current stays unavailable; switching sources alone does not trigger a false voltage alarm.
- Clock provenance: without trusted synchronization, receivers use reception
  time and sample age instead of the repeater's untrusted absolute date.

Release details: **[CHANGELOG](CHANGELOG.md#english)**.

### Firmware installation

Select the exact board file from
[Releases](https://github.com/mesh-cz/MeshCore-Czech/releases).

| Platform | Format |
|---|---|
| nRF52 | USB bootloader `.uf2` or Bluetooth DFU `.zip` |
| ESP32 — update | `…-upgrade.bin` |
| ESP32 — clean installation | `…-freshInstall-merged.bin`; erases all flash, including settings |
| RP2040 | `.uf2` |

Use [MeshCore Flasher](https://flasher.meshcore.io/) where applicable. The
files actually attached to a release determine which builds are available.

### Android Bluetooth DFU (nRF52)

**Check nRF Device Firmware Update settings before uploading:**

| Option | Value |
|---|---|
| Packets receipt notification | **ON** |
| Number of packets | **8** |
| Request high MTU | **OFF** |
| Disable resume | **ON** |
| Force scanning | **ON** |
| Prepare object delay | **0 ms** |

Run `start ota` on the repeater and select its exact ZIP. Unsuitable settings
may produce **Completed** without a working boot. Check LoRa and `ver` after
the update. These settings resolved the reported ProMicro and XIAO nRF52
issue without changing firmware. See the
[MeshCore guide](https://blog.meshcore.io/2026/04/02/nrf-ota-update).

### Finding data in CoreScope

Open [CoreScope](https://analyzer.meshcore.website/).

| Location | What to look for |
|---|---|
| **Nodes — list** | Czech flag before the stars identifies nodes with received CzHealth sensor reports; tooltip: “Komunitní FW MeshCore-Czech”. |
| **Nodes — repeater detail** | **MeshCore-Czech** above **Recent Adverts**: measurements, battery, sources, uptime, restart and 1/7/30/90-day charts. |
| **Same panel after CONFIG reception** | Home/default/flood regions, configuration timestamp and an incomplete-list indicator if needed. |
| **Map → Scope Config → FW MC Czech** | Filter for nodes with received CzHealth sensor reports; **All** restores all nodes. |

Flags and filtering depend on received data; they do not enumerate all flashed
devices.

### Testing and reporting issues

The maintainer reported successful RC.10 checks on XIAO nRF52, SenseCAP Solar P1,
ProMicro and Heltec V4. This does not establish every board/sensor/wiring combination.

Include board name, `ver`, update method, exact command and response in an
issue report. For CzHealth, include `cz.health check`, `cz.health check sensors`
and `cz.health check time`. Do not share passwords.

### Origin and license

Based on [meshcore-dev/MeshCore](https://github.com/meshcore-dev/MeshCore).
The original MIT notice and attribution are in [license.txt](license.txt).
