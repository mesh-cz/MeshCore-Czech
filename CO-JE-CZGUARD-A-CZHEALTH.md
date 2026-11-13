# 🛡️ Co je CzHealth a CzGuard?

**EN: [English version below](#english-version)**

> 📣 **Chcete pomoct celé síti MeshCore v ČR?** Provozujete repeater?
> Chcete mít nízkou spotřebu a vidět stav svého repeateru?
> **Tak tohle je určeno přímo vám…**

> ℹ️ Popis platí pro firmware **MeshCore Czech RC.10**. V dalších verzích se
> chování může změnit.

## ⚡ V kostce

- 🩺 **CzHealth** posílá správci repeateru technické údaje: napětí baterie,
  teplotu, dobu provozu a podobně. Díky tomu si problému všimne dřív, než
  repeater na kopci přestane fungovat.
- 🛡️ **CzGuard** chrání síť před zahlcením. Zasáhne jen proti **nepřetržitému
  flood provozu**, který běží 24 hodin denně a zatěžuje celou síť.
- 🙋 **CzGuard neblokuje uživatele a ani to neumí.** Omezí jen přeposílání
  jednoho konkrétního opakujícího se proudu flood provozu. Tvoje zprávy,
  přímé zprávy ani běžné používání se ho netýkají.
- 🔋 **Šetří baterii.** Firmware má úsporný režim, a když CzGuard zastaví
  škodlivý flood, nemusí ho vysílat ani tvůj repeater, ani ostatní repeatery
  v síti. V zimě se to hodí.
- 🚨 **Centrální blokace** je poslední pojistka pro případ, že by útočník
  zahlcoval celou síť. Zatím žádná vydaná nebyla a o každé se bude hlasovat.
- 👀 Všechny blokace, automatické i centrální, budou i s důvodem uvedené na
  veřejné stránce.

## 🤝 Děláme to pro všechny

> 💚 **Pomozte komunitě udržet síť v provozu.** Každý repeater s firmwarem
> MeshCore Czech, každé nahlášení chyby a každá zpětná vazba pomáhá. Pomůžete tím
> sobě i všem ostatním: vaše zprávy projdou dál, repeatery vydrží déle na
> baterii a síť zůstane spolehlivá.
>
> **To je cíl celého projektu: síť, která funguje pro všechny.**

## 🙋 Co to znamená pro mě jako běžného uživatele?

> ✅ **Prakticky nic.** Když normálně píšeš do kanálů, posíláš přímé zprávy
> nebo občas pošleš advert, CzGuard tě vůbec nezaznamená.

Zasáhne až proti provozu, který se opakuje **nepřetržitě, den i noc**. Takový
provoz obvykle nevytváří člověk, ale útočník, rozbitá automatizace nebo
špatně nastavené zařízení.

I v tom případě se nezablokuje zařízení ani člověk. Repeater jen přestane
přeposílat dál do sítě ten konkrétní opakující se proud. Všechno ostatní ze
stejného zařízení prochází normálně.

## 💡 Proč to vzniklo?

Repeatery často běží bez dozoru na střechách, kopcích a stožárech, mnohé
na baterii se solárním panelem. Když se něco pokazí, správce tam nemůže hned
dojet.

Repeatery navíc sdílí jeden rádiový prostor. Jedno zařízení, které
nepřetržitě floodí, dokáže zaměstnat repeatery v celé síti: zabírá vysílací
čas ostatním a vybíjí baterie. Nemusí jít o úmysl, často je příčinou chyba
v nastavení nebo v automatizaci.

Proto vznikly dvě funkce: **CzHealth** hlídá stav zařízení,
**CzGuard** hlídá zátěž sítě.

## 📖 Pár pojmů

| Pojem | Co znamená |
| --- | --- |
| **Repeater** | Zařízení, které přijme zprávu a pošle ji dál. Propojuje místa, která na sebe přímo nedosáhnou. |
| **Flood** | Způsob šíření, kdy zprávu přeposílají všechny repeatery v okolí, a ty zase dál. Hodí se, když není známá cesta, ale zaměstná hodně zařízení najednou. |
| **Proud (stream)** | Stále stejný druh flood provozu ze stejného zdroje, například pořád dokola stejná zpráva do téhož kanálu. CzGuard posuzuje proudy, ne lidi. |
| **CoreScope** | Webový nástroj na sledování sítě a údajů z repeaterů. |

---

## 🩺 CzHealth: stav repeateru na dálku

CzHealth průběžně měří technické údaje a posílá je v pravidelných hlášeních.
Správce pak vidí stav zařízení, aniž by musel jezdit na místo.

> 📉 **Příklad:** Repeater na kopci běží ze solárního panelu. Správce
> v CoreScope vidí, že napětí baterie několik dní klesá a přes den se nevrací
> na obvyklou úroveň. Stihne zkontrolovat baterii, panel nebo regulátor dřív,
> než repeater zhasne.

Údaje jsou pomůcka, ne diagnóza. Pokles napětí sám neřekne proč. Záleží na
typu baterie, teplotě, zapojení a na tom, kde se napětí měří.

> 🎯 **K čemu CzHealth je a k čemu ne:** vznikl jako **nástroj pro správce
> repeateru**, aby měl přehled o stavu svého repeateru: jestli má dost
> energie, jestli se nepřehřívá, jestli se nerestartuje. **Není to měřicí
> stanice** na teplotu ve skleníku, meteostanice ani čidlo pro chytrou
> domácnost. Teplota a vlhkost slouží k posouzení podmínek, ve kterých
> repeater běží.

### 📊 Jaké údaje posílá?

Podle desky a připojených čidel:

| | Údaj | K čemu je dobrý |
| --- | --- | --- |
| 🔋 | **Napětí a jeho zdroj** | Sledování napájení. Hodnota může pocházet z desky, z INA čidla nebo z podporované BMS. |
| 🧪 | **Typ baterie a počet článků** | Aby aplikace správně vyhodnotila napětí. Typ nastavuje správce, firmware ho sám spolehlivě nepozná. |
| 🌡️ | **Teplota** | Z externího čidla nebo z mikrokontroléru. |
| ⚡ | **Proud a vlhkost** | Jen s podporovaným čidlem. Chybějící údaj neznamená nulu. |
| ⏱️ | **Doba provozu a důvod restartu** | Poznáš, že se repeater restartoval, a pokud to deska umí, i proč. |
| 🗺️ | **Regiony, verze firmwaru, deska** | Posílá se v samostatném konfiguračním hlášení. |

Hlášení obsahují jen tyto technické údaje, žádný obsah zpráv.

### ⏰ Jak často?

Repeater hodnoty průběžně měří a posílá je souhrnně, aby zbytečně nezatěžoval
síť. Data tedy nechodí v reálném čase:

- první hlášení po startu přijde zhruba za **30 až 90 minut**,
- bateriové uzly posílají přehled **aspoň jednou denně**, i když se nic
  nemění,
- správce si může hlášení vyžádat ručně, nejvýš **jednou za 4 hodiny**,
- konfigurační hlášení s regiony má vlastní plán.

> ⚠️ Odeslané hlášení nemusí dorazit, třeba při slabém pokrytí nebo když ho
> nezachytí žádný přijímač připojený ke službě. **Chybějící údaj tedy
> neznamená, že je repeater vypnutý nebo rozbitý.**

### 👁️ Kdo to vidí?

**Údaje jsou veřejné.** Zobrazují se v komunitních službách, například v
[CoreScope](https://analyzer.meshcore.website/) v detailu repeateru, panel
**MeshCore-Czech**. Senzorová i konfigurační hlášení jde vypnout každé zvlášť,
postup je v [CLI manuálu](https://github.com/mesh-cz/MeshCore-Czech/blob/main/CLI-manual.md).

Když má repeater vypnuté přeposílání (`Repeat OFF`), CzHealth ve výchozím
stavu nic neposílá. Správce může hlášení povolit i v tomto režimu, přeposílání
cizích zpráv se tím nezapne.

---

## 🛡️ CzGuard: ochrana před zahlcením sítě

CzGuard běží přímo v repeateru a sleduje flood provoz, který má repeater
přeposlat dál. Když nějaký proud floodí **nepřetržitě**, repeater ho přestane
šířit do sítě. Ostatní provoz jde dál beze změny.

### ⚖️ Co CzGuard dělá a co ne

**✅ Dělá:**

- sleduje vybrané druhy flood provozu: některé kanálové zprávy, opakované
  adverty a určité dotazy a pokusy o přihlášení,
- u proudu, který neustále zatěžuje síť, nejdřív omezí jeho dosah, a když
  to pokračuje, dočasně ho přestane přeposílat,
- po čase situaci znovu vyhodnotí; když flood přestane, omezení skončí.

**❌ Nedělá:**

- **neblokuje uživatele ani zařízení**, omezuje jen konkrétní proud flood
  provozu,
- nesahá na přímo směrované zprávy ani na prioritní kontakty,
- nečte obsah zpráv a neposuzuje, o čem se píše, sleduje jen četnost,
- nedokáže zabránit tomu, aby rádiové vysílání fyzicky doletělo k okolním
  stanicím, jen ho repeater nepošle dál.

> 🤖 **Příklad:** Něčí automatizace se kvůli chybě zacyklí a dnem i nocí
> posílá stejnou zprávu do kanálu. Bez CzGuardu by ji přeposílaly všechny
> repeatery v okolí a zabíraly vysílací čas ostatním. S CzGuardem ji
> repeatery po čase přestanou šířit dál. Majitel automatizace dál normálně
> komunikuje, jen jeho zacyklená zpráva už nezahlcuje síť.

Omezení neznamená, že někdo útočí. Většinou jde o chybu, o které majitel
zařízení ani neví.

### 🚨 Místní a centrální blokace

**Místní (automatická) blokace:** každý repeater rozhoduje sám podle toho,
co zachytí. Dva repeatery proto můžou vyhodnotit stejnou situaci v různou
chvíli. Správce repeateru může místní automatiku vypnout nebo přepnout do
zkušebního režimu.

**Centrální blokace** je **poslední pojistka** pro případ, že by útočník
cíleně zahlcoval celou síť a místní ochrana nestačila. Je to nástroj pro
krizovou situaci, ne pro běžnou správu:

- **zatím žádná centrální blokace vydaná nebyla**,
- o vydání se **hlasuje v komunitě na Telegramu**, nikdo o ní nerozhoduje
  sám; v novějších verzích může přibýt i jiný způsob hlasování,
- každá blokace má omezenou platnost, nebo ji jde centrálně zrušit,
- v RC.10 ji správce repeateru nemůže lokálně přebít, jinak by v
  krizové situaci neplnila svůj účel. **Od verze RC.11 bude mít správce
  repeateru možnost centrální blokace na svém repeateru vypnout.**
- **Od verze RC.11:** vypnutím CzGuard, nebo jen jeho funkce Central Ban,
  repeater nebude odesílat ani CzHealth.

### 🔎 Kde uvidím, co je blokované?

Připravujeme **veřejnou stránku**, kde budou uvedené automatické blokace
na repeaterech i centrální blokace, vždy s důvodem. Odkaz doplníme po
spuštění.

Správce repeateru vidí stav ochrany a statistiky v administraci
repeateru, příkazy jsou v
[CLI manuálu](https://github.com/mesh-cz/MeshCore-Czech/blob/main/CLI-manual.md).

> 📝 **Myslíš si, že blokace je chybná?** Zapiš si čas, popis situace a verzi
> firmwaru a ozvi se správci repeateru nebo komunitě na Telegramu.

---

## 🔋 Šetří to baterii?

> ❄️ **Ano**, a v zimě, kdy je málo slunce a solární repeatery jedou na hraně,
> se to hodí dvojnásob.

**Úsporný režim firmwaru.** MeshCore Czech umí šetřit baterii pomocí
úsporného režimu (PowerSaving). Zapíná se v nastavení repeateru podle
možností desky a rádia, postup je v
[CLI manuálu](https://github.com/mesh-cz/MeshCore-Czech/blob/main/CLI-manual.md).

**CzGuard šetří baterii taky.** Vysílání je to nejdražší, co repeater
dělá. Když CzGuard zastaví škodlivý nepřetržitý flood, repeater ho nemusí
pořád dokola vysílat, a šetří tak vlastní baterii. Hlavně ale flood nepošle
dál, takže ho nemusí vysílat ani **ostatní repeatery v síti**. Jeden repeater
s CzGuardem tak chrání baterie i vysílací čas repeaterů za sebou. Kolik se
ušetří, záleží na tom, kolik takového provozu v okolí je.

**CzHealth** sám nešetří, ale pomáhá problém s napájením **včas odhalit**,
třeba že baterie v mrazu nestíhá dobíjet. Vysílací výkon nesnižuje a do
úsporného režimu repeater nepřepíná.

Hlášení obou funkcí samozřejmě taky spotřebují trochu vysílání, ale je to
zlomek toho, co dokáže spotřebovat nepřetržitý flood.

---

## ❓ Časté otázky

**Můžou mi kvůli CzGuardu přestat chodit zprávy?**  
Při normálním používání ne. CzGuard reaguje jen na nepřetržitý flood provoz
24/7 a i pak omezí jen ten jeden proud, ne tebe.

**Kdo rozhoduje o centrální blokaci?**  
Komunita hlasováním na Telegramu. Zatím žádná vydaná nebyla a je určená
jen pro případ útoku na celou síť.

**Znamená blokace, že někdo útočí?**  
Ne. Častěji jde o chybu nebo špatně nastavenou automatizaci.

**Zaručí CzGuard, že v síti nebude spam?**  
Ne. Řeší jen nepřetržitý flood. Běžný spam ani fyzické rušení neodstraní.

**Jsou údaje z CzHealth veřejné?**  
Ano. Správce může hlášení vypnout, viz výše.

**Proč v přehledu nic není?**  
Hlášení ještě nebylo odesláno, nedorazilo, deska údaj nemá, nebo ho služba
neumí zobrazit. Stav ověříš v administraci repeateru.

**Nahrazuje to údržbu?**  
Ne. Pomáhá poznat, kdy je potřeba zajet k repeateru. Baterie, anténa
a zapojení pořád potřebují kontrolu.

## 🔗 Kde najdu víc?

- [MeshCore Czech: README a rychlý start](https://github.com/mesh-cz/MeshCore-Czech)
- [Vydání firmwaru](https://github.com/mesh-cz/MeshCore-Czech/releases)
- [CLI manuál pro správce](https://github.com/mesh-cz/MeshCore-Czech/blob/main/CLI-manual.md)
- [CoreScope](https://analyzer.meshcore.website/)

Obě funkce vznikají s pomocí komunity. Zpětná vazba, hlášení chyb a výsledky
testování jsou vítané.

---
---

# English version

**CZ: [⬆ Česká verze nahoře](#)**

## 🛡️ What are CzHealth and CzGuard?

> 📣 **Want to help the whole MeshCore network in the Czech Republic?** Do you
> run a repeater? Want low power consumption and a view of your repeater's
> status? **Then this is meant for you…**

> ℹ️ This description applies to **MeshCore Czech RC.10** firmware. Behaviour
> may change in later versions.

### ⚡ In a nutshell

- 🩺 **CzHealth** sends the repeater's operator technical data: battery
  voltage, temperature, uptime and so on. That way they notice a problem
  before the repeater on a hilltop stops working.
- 🛡️ **CzGuard** protects the network from being flooded. It only steps in
  against **continuous flood traffic** that runs 24 hours a day and loads the
  whole network.
- 🙋 **CzGuard does not block users, and it has no way to.** It only limits
  forwarding of one specific repeating stream of flood traffic. Your
  messages, direct messages and normal use are not affected.
- 🔋 **It saves battery.** The firmware has a power-saving mode, and when
  CzGuard stops harmful flooding, neither your repeater nor the other
  repeaters in the network have to transmit it. Handy in winter.
- 🚨 **Central blocking** is the last line of defence in case an attacker
  floods the whole network. None has been issued so far, and every one will
  be put to a vote.
- 👀 All blocks, automatic and central, will be listed together with the
  reason on a public page.

### 🤝 We do this for everyone

> 💚 **Help the community keep the network running.** Every repeater running
> MeshCore Czech firmware, every bug report and every piece of feedback helps.
> You help yourself and everyone else: your messages get through, repeaters
> last longer on battery and the network stays reliable.
>
> **That's the goal of the whole project: a network that works for
> everyone.**

### 🙋 What does this mean for me as a regular user?

> ✅ **Practically nothing.** If you write in channels, send direct messages
> or send an advert now and then, CzGuard won't even notice you.

It only acts against traffic that repeats **continuously, day and night**.
Such traffic is usually not created by a person but by an attacker, a broken
automation or a misconfigured device.

Even then, no device or person gets blocked. The repeater simply stops
forwarding that one repeating stream into the network. Everything else from
the same device passes through normally.

### 💡 Why does this exist?

Repeaters often run unattended on roofs, hills and masts, many on a
battery with a solar panel. When something goes wrong, the operator can't
just drive there right away.

Repeaters also share one radio space. A single device that floods
continuously can keep repeaters busy across the whole network: it takes up
airtime from others and drains batteries. It doesn't have to be intentional;
often the cause is a configuration or automation mistake.

That's why there are two features: **CzHealth** watches the device's
condition, **CzGuard** watches the network load.

### 📖 A few terms

| Term | Meaning |
| --- | --- |
| **Repeater** | A device that receives a message and passes it on. It connects places that can't reach each other directly. |
| **Flood** | A way of spreading where every repeater nearby forwards the message, and those forward it further. Useful when no route is known, but it keeps many devices busy at once. |
| **Stream** | The same kind of flood traffic from the same source over and over, for example the same message sent to the same channel again and again. CzGuard judges streams, not people. |
| **CoreScope** | A web tool for monitoring the network and repeater data. |

---

### 🩺 CzHealth: repeater status from a distance

CzHealth continuously measures technical data and sends it in regular
reports. The operator can see the device's condition without travelling to
the site.

> 📉 **Example:** A repeater on a hill runs on a solar panel. In CoreScope
> the operator sees that battery voltage has been dropping for several days
> and doesn't recover to its usual level during the day. They have time to
> check the battery, panel or charge controller before the repeater goes
> dark.

The data is an aid, not a diagnosis. A voltage drop alone doesn't tell you
why. It depends on the battery type, temperature, wiring and where the
voltage is measured.

> 🎯 **What CzHealth is for, and what it isn't:** it was created as **a tool
> for repeater operators**, so they have an overview of their repeater's
> condition: whether it has enough power, whether it's overheating, whether
> it keeps restarting. **It is not a measuring station** for greenhouse
> temperature, a weather station or a smart-home sensor. Temperature and
> humidity are there to judge the conditions the repeater runs in.

#### 📊 What data does it send?

Depending on the board and connected sensors:

| | Data | What it's good for |
| --- | --- | --- |
| 🔋 | **Voltage and its source** | Monitoring power. The value can come from the board, an INA sensor or a supported BMS. |
| 🧪 | **Battery type and cell count** | So the app can interpret the voltage correctly. The operator sets the type; the firmware can't reliably detect it on its own. |
| 🌡️ | **Temperature** | From an external sensor or from the microcontroller. |
| ⚡ | **Current and humidity** | Only with a supported sensor. A missing value does not mean zero. |
| ⏱️ | **Uptime and reset reason** | Shows that the repeater restarted and, if the board supports it, why. |
| 🗺️ | **Regions, firmware version, board** | Sent in a separate configuration report. |

Reports contain only this technical data, no message content.

#### ⏰ How often?

The repeater measures values continuously and sends them in batches so it
doesn't load the network unnecessarily. The data is not real-time:

- the first report after start-up arrives in roughly **30 to 90 minutes**,
- battery-powered nodes send an overview **at least once a day**, even if
  nothing changes,
- the operator can request a report manually, at most **once every 4
  hours**,
- the configuration report with regions has its own schedule.

> ⚠️ A sent report may not arrive, for example with weak coverage or if no
> receiver connected to the service picks it up. **A missing value
> therefore doesn't mean the repeater is off or broken.**

#### 👁️ Who can see it?

**The data is public.** It is shown in community services, for example in
[CoreScope](https://analyzer.meshcore.website/) in the repeater detail,
**MeshCore-Czech** panel. Sensor and configuration reports can each be
turned off separately; see the
[CLI manual](https://github.com/mesh-cz/MeshCore-Czech/blob/main/CLI-manual.md).

When the repeater has forwarding turned off (`Repeat OFF`), CzHealth sends
nothing by default. The operator can allow reports in this mode too; that
does not turn on forwarding of other people's messages.

---

### 🛡️ CzGuard: protection against network flooding

CzGuard runs directly in the repeater and watches flood traffic the repeater
is about to forward. When a stream floods **continuously**, the repeater
stops spreading it into the network. All other traffic passes unchanged.

#### ⚖️ What CzGuard does and doesn't do

**✅ It does:**

- watch selected kinds of flood traffic: some channel messages, repeated
  adverts and certain queries and login attempts,
- for a stream that keeps loading the network, first limit its reach and,
  if it continues, temporarily stop forwarding it,
- re-evaluate the situation after a while; when the flooding stops, the
  limit ends.

**❌ It doesn't:**

- **block users or devices**; it only limits a specific stream of flood
  traffic,
- touch directly routed messages or priority contacts,
- read message content or judge what is being written; it only looks at
  frequency,
- prevent a radio transmission from physically reaching nearby stations;
  the repeater just doesn't pass it on.

> 🤖 **Example:** Someone's automation gets stuck in a loop due to a bug and
> sends the same message to a channel day and night. Without CzGuard, every
> repeater around would forward it and take airtime from others. With
> CzGuard, the repeaters stop spreading it after a while. The automation's
> owner can still communicate normally; only their looping message no
> longer floods the network.

A limit doesn't mean someone is attacking. Most of the time it's a mistake
the device owner isn't even aware of.

#### 🚨 Local and central blocking

**Local (automatic) blocking:** each repeater decides on its own based on
what it hears. Two repeaters may therefore evaluate the same situation at
different times. The repeater's operator can turn local automation off or
switch it to test mode.

**Central blocking** is **the last line of defence** in case an attacker
deliberately floods the whole network and local protection isn't enough. It
is a tool for a crisis, not for everyday administration:

- **no central block has been issued so far**,
- issuing one is **put to a community vote on Telegram**; nobody decides
  on their own. Newer versions may add another way of voting,
- every block has a limited validity, or it can be lifted centrally,
- in RC.10 the repeater's operator cannot override it locally, otherwise
  it wouldn't serve its purpose in a crisis. **From RC.11 on, repeater
  operators will be able to turn central blocking off on their own
  repeater.**
- **From RC.11 on:** if CzGuard, or just its Central Ban feature, is turned
  off, the repeater will not send CzHealth either.

#### 🔎 Where can I see what is blocked?

We are preparing a **public page** listing automatic blocks on repeaters
as well as central blocks, always with the reason. We'll add the link once
it's live.

The repeater's operator can see protection status and statistics in the
repeater's administration; commands are in the
[CLI manual](https://github.com/mesh-cz/MeshCore-Czech/blob/main/CLI-manual.md).

> 📝 **Think a block is wrong?** Write down the time, what happened and the
> firmware version, and contact the repeater's operator or the community on
> Telegram.

---

### 🔋 Does it save battery?

> ❄️ **Yes**, and in winter, when there's little sun and solar repeaters are
> running on the edge, it's doubly useful.

**Firmware power-saving mode.** MeshCore Czech can save battery with a
power-saving mode (PowerSaving). It is turned on in the repeater settings
depending on what the board and radio support; see the
[CLI manual](https://github.com/mesh-cz/MeshCore-Czech/blob/main/CLI-manual.md).

**CzGuard saves battery too.** Transmitting is the most expensive thing a
repeater does. When CzGuard stops harmful continuous flooding, the repeater
doesn't have to transmit it over and over, saving its own battery. More
importantly, it doesn't pass the flood on, so **other repeaters in the
network** don't have to transmit it either. One repeater with CzGuard
protects the batteries and airtime of the repeaters behind it. How much is
saved depends on how much such traffic there is nearby.

**CzHealth** doesn't save power by itself, but it helps **spot** a power
problem **early**, for example a battery that can't keep up with charging
in frost. It doesn't reduce transmit power or switch the repeater into a
power-saving mode.

Of course, the reports of both features also use a little airtime, but
that's a fraction of what continuous flooding can consume.

---

### ❓ FAQ

**Can CzGuard make my messages stop getting through?**  
Not with normal use. CzGuard only reacts to continuous 24/7 flood traffic,
and even then it limits just that one stream, not you.

**Who decides on central blocking?**  
The community, by a vote on Telegram. None has been issued so far, and it's
meant only for an attack on the whole network.

**Does a block mean someone is attacking?**  
No. More often it's a mistake or a badly configured automation.

**Does CzGuard guarantee there will be no spam on the network?**  
No. It only deals with continuous flooding. It won't remove ordinary spam
or physical radio interference.

**Is CzHealth data public?**  
Yes. The operator can turn reports off; see above.

**Why is the overview empty?**  
The report hasn't been sent yet, didn't arrive, the board doesn't have that
value, or the service can't display it. You can check the status in the
repeater's administration.

**Does it replace maintenance?**  
No. It helps you tell when it's time to visit the repeater. The battery,
antenna and wiring still need checking.

### 🔗 Where can I find more?

- [MeshCore Czech: README and quick start](https://github.com/mesh-cz/MeshCore-Czech)
- [Firmware releases](https://github.com/mesh-cz/MeshCore-Czech/releases)
- [CLI manual for operators](https://github.com/mesh-cz/MeshCore-Czech/blob/main/CLI-manual.md)
- [CoreScope](https://analyzer.meshcore.website/)

Both features are developed with the community's help. Feedback, bug
reports and test results are welcome.
