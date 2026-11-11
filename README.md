# MeshCore Czech

[EN — English version below](#english)

**[📦 Stáhnout firmware](https://github.com/mesh-cz/MeshCore-Czech/releases)** ·
**[🚀 Rychlý start](#rychly-start)** ·
**[📖 CLI manuál](CLI-manual.md)** ·
**[📊 CoreScope](#corescope-cz)** ·
**[📝 Changelog](CHANGELOG.md)**

Komunitní firmware pro repeatery sítě mesh-cz. Jde o fork původního projektu
[MeshCore](https://github.com/meshcore-dev/MeshCore), rozšířený o funkce pro
českou komunitu v České republice.

## 💡 Proč tento fork?

**Cílem forku je usnadnit spolehlivý a úsporný provoz především venkovních
repeaterů v české komunitní síti.** Správcům repeaterů nabízí přehled o stavu
zařízení, aby mohli včas odhalit například problémy s baterií, nabíjením nebo
provozem v zimních podmínkách. Součástí jsou také nástroje pro ochranu před
nadměrným provozem a možnosti nastavení podle potřeb správce.

Projekt se rozvíjí společně s komunitou a navazuje na práci autorů MeshCore
a dalších přispěvatelů.

> **⚠️ Upozornění**
>
> **Testovací vydání — při OTA aktualizaci může dojít k problémům se spuštěním repeateru.**
> I po hlášení **100 % / Completed** nemusí repeater naběhnout a může být nutné nahrát firmware znovu přes USB.
>
> - Nahrávejte jej především na repeatery, ke kterým máte **fyzický přístup a možnost obnovy kabelem**. Vzdáleně nedostupná zařízení aktualizujte až s připravenou možností zásahu.
> - Před aktualizací si **exportujte a bezpečně uložte zálohu nastavení a identity repeateru**, pokud to používaná aplikace umožňuje. Stáhněte si také dosavadní funkční firmware pro svou desku pro případ obnovy.
> - Pro minimalizaci problémů při Bluetooth OTA na nRF52 **použijte doporučené nastavení nRF Device Firmware Update uvedené níže**: [nastavení DFU](#bluetooth-dfu-na-androidu-nrf52). Samotné dokončení přenosu nenahrazuje kontrolu funkčnosti přes LoRa a příkazem `ver`.

**Aktuální testovací vydání:** [v1.18.0.02rc.10](https://github.com/mesh-cz/MeshCore-Czech/releases/tag/v1.18.0.02rc.10).
Plánovaná stabilní verze je `v1.18.0.03`.

<a id="rychly-start"></a>

## 🚀 Rychle po nahrání firmwaru

1. Z [vydání RC.10](https://github.com/mesh-cz/MeshCore-Czech/releases/tag/v1.18.0.02rc.10)
   vyberte a nahrajte soubor pro svou desku. Pro nRF52 si před OTA přečtěte
   [nastavení Bluetooth DFU](#bluetooth-dfu-na-androidu-nrf52).
2. Přihlaste se jako **administrátor** a příkazem `ver` ověřte verzi.
3. Nastavte skutečný typ baterie a počet článků v sérii. Příklad pro 1S Li-ion:

   ```text
   cz.health battery liion
   cz.health battery.cells 1
   cz.health on
   cz.health sensors on
   cz.health config on
   ```

   Výchozí typ je `auto` a počet článků 1. Další typy a nastavení najdete v
   [CLI manuálu](CLI-manual.md).
4. **Počkejte přibližně minutu na první vzorek**, potom můžete odeslání zkusit
   příkazem `cz.health send`. Při zamítnutí zkontrolujte `cz.health check`.
   Ruční odeslání má čtyřhodinový limit; restart během blokace spustí čekání
   znovu. Data se v CoreScope zobrazí po přijetí hlášení.

## 💚 CzHealth a provozní údaje

**CzHealth** poskytuje přehled o stavu repeateru. Pomáhá správci včas poznat
slábnoucí baterii, neobvyklou teplotu nebo restart repeateru. Sdílí technické údaje o zařízení: naměřené
hodnoty, typ baterie a počet článků, dobu běhu a důvod restartu. Samostatně
lze sdílet nastavené regiony, verzi firmwaru a typ desky.

Tyto údaje mohou být veřejně zobrazené v komunitní mapě a CoreScope.
Sdílení je ve výchozím stavu zapnuté a provozovatel jej může ovládat:
`cz.health off` vypne sběr a senzorová hlášení, `cz.health config off`
vypne sdílení konfigurace. Pro vypnutí obou částí použijte oba příkazy.

## 🛡️ CzGuard – ochrana před nadměrným provozem

**CzGuard** pomáhá chránit repeater před nadměrným provozem. Jeho stav a
činnost lze zkontrolovat příkazy `cz.guard`, `cz.guard stats` a `cz.guard list`.
Podrobnosti o nastavení jsou v [CLI manuálu](CLI-manual.md).

## ⚡ Co firmware nabízí

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

## 📥 Nahrání firmwaru

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

<a id="corescope-cz"></a>

## 📊 Kde najdete data v CoreScope

Otevřete [CoreScope](https://analyzer.meshcore.website/).

| Místo | Co hledat |
|---|---|
| **Nodes — seznam** | Česká vlaječka před hvězdičkami označuje uzel s přijatými senzorovými zprávami CzHealth. Popis: „Komunitní FW MeshCore-Czech“. |
| **Nodes — detail repeateru** | Panel **MeshCore-Czech** nad **Recent Adverts**: přijaté hodnoty, baterie, zdroje, uptime, restart a grafy za 1/7/30/90 dní. |
| **Stejný panel po přijetí CONFIG** | Domácí, výchozí a flood regiony, čas konfigurační zprávy a případné označení neúplného seznamu. |
| **Map → Scope Config → FW MC Czech** | Filtr uzlů s přijatými senzorovými zprávami CzHealth; **All** vrátí všechny uzly. |

Vlaječka a filtr vycházejí z přijatých dat; neprokazují kompletní seznam
všech nahraných zařízení.

## 🧪 Ověření a hlášení potíží

Správce potvrdil funkční testy RC.10 na XIAO nRF52, SenseCAP Solar P1,
ProMicro a Heltec V4.
Výsledek konkrétní desky nezaručuje všechny kombinace čidel a zapojení.

Našli jste problém? Založte [Issue na GitHubu](https://github.com/mesh-cz/MeshCore-Czech/issues).
Při hlášení přiložte desku, výstup `ver`, způsob aktualizace, přesný příkaz
a odpověď. Pro CzHealth pomohou `cz.health check`, `cz.health check sensors`
a `cz.health check time`. Hesla neposílejte.

## 🤝 Poděkování a použité úpravy

- **PowerSaving 18** od [IoTThinks / EasySkyMesh](https://github.com/IoTThinks/EasySkyMesh/wiki/PowerSaving) přináší úsporné funkce pro podporovaná zařízení.
- **YomaMesh:** úpravy podpory desky od **RadekCX** ([cncnet-info](https://github.com/cncnet-info)), včetně kalibrace rádia a nastavení desky.

Děkujeme autorům za jejich práci a přínos komunitě.

## 📜 Původ a licence

Základ projektu: [meshcore-dev/MeshCore](https://github.com/meshcore-dev/MeshCore).
Původní MIT text a autorství jsou v [license.txt](license.txt).

---

## English

**[📦 Download firmware](https://github.com/mesh-cz/MeshCore-Czech/releases)** ·
**[🚀 Quick start](#quick-start-en)** ·
**[📖 CLI manual](CLI-manual.md#english)** ·
**[📊 CoreScope](#corescope-en)** ·
**[📝 Changelog](CHANGELOG.md#english)**

Community firmware for mesh-cz repeaters. MeshCore Czech is a fork of
[MeshCore](https://github.com/meshcore-dev/MeshCore), extended with features
for the Czech community in the Czech Republic.

### 💡 Why this fork?

**The goal of this fork is to make repeaters, particularly outdoor ones,
easier to run reliably and efficiently in the Czech community network.** It
gives repeater operators an overview of device health so they can identify
potential battery, charging, or winter operation issues early. It also offers
protection against excessive traffic and settings to suit operators' needs.

The project grows with the community and builds on the work of the MeshCore
authors and other contributors.

> [!WARNING]
> **Test release — OTA updates may leave the repeater unable to boot.**
> Even after **100% / Completed**, the repeater may fail to start and require flashing again over USB.
>
> - Prefer repeaters you can **physically access and recover using a cable**. Update inaccessible remote devices only when an on-site recovery option is available.
> - Before updating, **export and securely save a backup of the repeater configuration and identity**, if your application supports it. Also download the previously working firmware for your exact board for recovery.
> - To minimize nRF52 Bluetooth OTA problems, **use the recommended nRF Device Firmware Update settings below**: [DFU settings](#android-bluetooth-dfu-nrf52). A completed transfer does not replace checking LoRa operation and `ver`.

**Current test release:** [v1.18.0.02rc.10](https://github.com/mesh-cz/MeshCore-Czech/releases/tag/v1.18.0.02rc.10).
The planned stable version is `v1.18.0.03`.

<a id="quick-start-en"></a>

### 🚀 Quick start after flashing

1. Download and flash the file for your board from the
   [RC.10 release](https://github.com/mesh-cz/MeshCore-Czech/releases/tag/v1.18.0.02rc.10).
   Before nRF52 OTA, read the [Bluetooth DFU settings](#android-bluetooth-dfu-nrf52).
2. Log in as an **administrator** and check the version with `ver`.
3. Set the actual battery chemistry and number of series cells. Example for 1S Li-ion:

   ```text
   cz.health battery liion
   cz.health battery.cells 1
   cz.health on
   cz.health sensors on
   cz.health config on
   ```

   The defaults are `auto` and one cell. See the [CLI manual](CLI-manual.md#english)
   for other battery types and settings.
4. **Wait about one minute for the first sample**, then you can try
   `cz.health send`. If rejected, check `cz.health check`. Manual sends are
   limited to once per four hours; rebooting during the wait starts it again.
   Data appears in CoreScope after a report is received.

### 💚 CzHealth and operational data

**CzHealth** provides an overview of repeater health. It helps operators spot
a weakening battery, unusual temperature or a repeater restart. It shares technical device information: measurements,
battery chemistry and cell count, uptime and restart reason. Configured
regions, firmware version and board type can be shared separately.

These readings may be displayed publicly on community maps and in CoreScope.
Sharing is enabled by default and controlled by the operator: `cz.health off`
disables sampling and sensor reports; `cz.health config off` disables
configuration sharing. Use both commands to disable both parts.

### 🛡️ CzGuard – protection against excessive traffic

**CzGuard** helps protect the repeater from excessive traffic. Inspect its
status and activity with `cz.guard`, `cz.guard stats` and `cz.guard list`.
See the [CLI manual](CLI-manual.md#english) for configuration details.

### ⚡ Features

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

### 📥 Firmware installation

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

<a id="corescope-en"></a>

### 📊 Finding data in CoreScope

Open [CoreScope](https://analyzer.meshcore.website/).

| Location | What to look for |
|---|---|
| **Nodes — list** | Czech flag before the stars identifies nodes with received CzHealth sensor reports; tooltip: “Komunitní FW MeshCore-Czech”. |
| **Nodes — repeater detail** | **MeshCore-Czech** above **Recent Adverts**: measurements, battery, sources, uptime, restart and 1/7/30/90-day charts. |
| **Same panel after CONFIG reception** | Home/default/flood regions, configuration timestamp and an incomplete-list indicator if needed. |
| **Map → Scope Config → FW MC Czech** | Filter for nodes with received CzHealth sensor reports; **All** restores all nodes. |

Flags and filtering depend on received data; they do not enumerate all flashed
devices.

### 🧪 Testing and reporting issues

The maintainer reported successful RC.10 checks on XIAO nRF52, SenseCAP Solar P1,
ProMicro and Heltec V4. This does not establish every board/sensor/wiring combination.

Found a problem? Open an [issue on GitHub](https://github.com/mesh-cz/MeshCore-Czech/issues).
Include board name, `ver`, update method, exact command and response in the
issue report. For CzHealth, include `cz.health check`, `cz.health check sensors`
and `cz.health check time`. Do not share passwords.

### 🤝 Acknowledgements and contributions

- **PowerSaving 18** by [IoTThinks / EasySkyMesh](https://github.com/IoTThinks/EasySkyMesh/wiki/PowerSaving) provides power-saving features for supported devices.
- **YomaMesh:** board support improvements by **RadekCX** ([cncnet-info](https://github.com/cncnet-info)), including radio calibration and board settings.

Thank you to the authors for their work and contributions to the community.

### 📜 Origin and license

Based on [meshcore-dev/MeshCore](https://github.com/meshcore-dev/MeshCore).
The original MIT notice and attribution are in [license.txt](license.txt).
