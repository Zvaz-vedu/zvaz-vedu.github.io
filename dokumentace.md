# Dokumentace k backendu a struktuře webu Zvaž vědu!

Tento dokument vysvětluje základní mechaniky a logiku, jak editovat obsah na tomto webu – zejména města, domovskou stránku a historii.

## 1. Stránky měst (např. `/brno/`, `/praha/`)
Web přešel z nepřehledného systému mnoha boolean vlajek (jako `liveAction: true`) na flexibilnější systém. Všechny komponenty stránky se nyní zapínají jednoduše tím, že jim v Markdown hlavičce (`_index.md`) přidělíte hodnotu. 

Pokud chcete skrýt např. program, stačí smazat klíč `program: true`, a tak podobně u všeho.

### Přehled konfiguračních klíčů v hlavičce (`_index.md`):
- `headerTitle`, `headerText`, `headerPhoto`, `location`: Nastavení hlavní hlavičky.
- `infoVid: "url_videa"`: Zobrazí YouTube video pod horními statistikami. (např. `https://www.youtube.com/embed/XXXX`)
- `program: true`: Zapne zobrazení sekce Program (obsah samotného programu vkládejte přes shortcode, např. `{{< program/brno26 >}}`).
- `abstracts: true` a `abstractUrl: "/brno/info/abstrakty"`: Tlačítko odkazující na abstrakty.
- `registration: "https://forms.gle/..."`: Pokud vyplníte URL, zapne se úplně dole registrační CTA sekce (tlačítko).
- `importantInfo: true`: Zapne informační zónu se zajímavým textem a galerií. Vyžaduje `importantInfoHeader` a `importantInfoText`.
- `additionalInfo: true`: Zapne "O projektu a akci", "Kamarády s sebou!" a blok nabídek "Na co se můžeš těšit". Lze použít jako prosté `true` (zobrazí všech 6 výchozích boxů), nebo rozšířit do podrobné konfigurace pro přidání, odebrání a úpravu boxů (viz níže).
- `showHistory: true`: (Dříve `pastActions`). Zobrazí na spodku stránky historii všech minulých ročníků v daném městě (Ohlédnutí). 
- `team`: Pole ID členů týmu z `data/team.yaml`.
- `partners` a `supporters`: Nastavení partnerů a podporovatelů pro dané město (podrobněji níže).

### Systém informačních boxů nabídek (`additionalInfo`)
Výchozí karty pro sekci „Na co se na Zvaž vědu! můžeš těšit?“ jsou vestavěny přímo v šabloně. K dispozici jsou výchozí boxy:
- `prednasky`: „Zajímavé přednášky“
- `networking`: „Networking“
- `vyzkum`: „Inspirace pro vlastní výzkum“
- `obcerstveni`: „Drobné občerstvení“
- `stanky`: „Prezentace a stánky“
- `exkurze`: „Odborné exkurze“

Pokud zadáte pouze `additionalInfo: true`, zobrazí se všech 6 výchozích boxů s automatickým číslováním 01 až 06.

Pokud chcete v daném městě vybrané boxy odebrat, upravit nebo přidat nové, zadáte `additionalInfo` jako objekt:
```yaml
additionalInfo:
  enabled: true                 # Volitelné (výchozí true)
  title: "Vlastní nadpis"       # Volitelné, přepíše výchozí "Na co se na Zvaž vědu! můžeš těšit?"
  images:                       # Volitelné obrázky pro horní dvě sekce
    - /media/imgs/gallery/...webp
    - /media/imgs/gallery/...webp
  remove:                       # Seznam ID boxů, které chcete skrýt
    - exkurze
    - obcerstveni
  override:                     # Přepsání textů či fotky existujícího boxu
    prednasky:
      description: "Vlastní krátký popis pro toto město."
      expanded_text: "Vlastní rozbalený text."
      photo: "/media/imgs/..."
  add:                          # Přidání nových vlastních boxů
    - id: workshopy             # Volitelné ID (pro případné řazení v order)
      title: "Workshopy"
      icon: "fa-solid fa-laptop-code"
      description: "Praktické workshopy s experty."
      expanded_text: "Podrobnosti o náplni workshopů..."   # Volitelné (pokud chybí text i foto, tlačítko "Zobrazit více" se nezobrazí)
      photo: "/media/imgs/gallery/..."                    # Volitelné
  order:                        # Volitelné pořadí boxů. Pokud není zadáno, přidané boxy se zařadí na konec za výchozí.
    - prednasky
    - workshopy
    - networking
```
Číslování boxů (01, 02, 03...) se vždy dopočítává automaticky podle konečného pořadí v gridu.

### Systém partnerů a podporovatelů (`partners` a `supporters`)
Na stránkách měst (`_index.md`) se loga partnerů zobrazují ve dvou sekcích:
- **Partneři** (`#partners`, třída `.partner-logo-img`, výška 120 px)
- **Podporovatelé** (`#supporters`, třída `.supporter-logo-img`, výška 80 px)

Systém podporuje tři způsoby použití:

1. **Výchozí (zpětně kompatibilní) režim:**
   V `_index.md` uvedete pouze `partners: [id1, id2, ...]`, případně parametr vynecháte (pak se hledá podle města). V tomto režimu se subjekty automaticky rozdělují podle pole `type` v `data/partners.yaml`:
   - `generalni-partner`, `hlavni-partner`, `partner` &rarr; sekce **Partneři**
   - `podporovatel` &rarr; sekce **Podporovatelé**

2. **Zrušení rozdělování (všichni do Partneři, sekce Podporovatelé skrytá):**
   Pokud nechcete subjekty rozdělovat na dvě sekce, nastavte `supporters: false` (nebo `supporters: []`):
   ```yaml
   partners: [plzensky-kraj-cz, fav-zcu-cz, eserocz-cz, radovanek-cz]
   supporters: false
   ```
   Všechny uvedené subjekty (i ty, které mají v `data/partners.yaml` nastaveno `type: podporovatel`) se zobrazí v sekci **Partneři** a sekce **Podporovatelé** se nezobrazí vůbec.

3. **Vlastní rozdělení do obou sekcí:**
   Do `partners` uveďte subjekty pro hlavní sekci a do `supporters` subjekty pro druhou sekci:
   ```yaml
   partners: [plzensky-kraj-cz, fav-zcu-cz, kooperativa]
   supporters: [radovanek-cz, sit-port]
   ```
   Každý subjekt bude zařazen přesně do té sekce, ve které je uveden, v přesném pořadí zápisu a bez ohledu na výchozí `type` v `data/partners.yaml`.

*Poznámka: Jako alias pro `supporters` lze v hlavičce použít i klíč `sponsors`. Samostatná stránka `/partneri` není tímto nijak dotčena (řídí se nadále globálními daty).*

### Systém Bento Boxů (horní dlaždice)
Využijte parametr `bentoBoxes: [id_boxu, id_boxu, id_boxu]` v souboru `_index.md`.

Předdefinované boxy se nacházejí v souboru `/data/bento_boxes.yaml`. Aktuálně jsou k dispozici např.:
- `events_count`: Box čerpající z proměnné `events: "2"` ("Počet akcí")
- `speakers_count`: Box čerpající z `speakers: "10"` ("Přednášejících")
- `live_countdown`: Box s živým odpočtem, vyžaduje definovanou proměnnou `eventTime` v daném městě.
- `past_youtube`: Box odkazující na záznamy na YouTube.
- `past_feedback`: Box pro feedback po skončení akce.
- `new_presentations`: Zobrazí upozornění na nové přednášky, vyžaduje proměnnou `newPresentations: "10"`.

### ⚠️ DŮLEŽITÉ: Účastníci vs Kapacita (Rozdíl v proměnných)
V systému existují dvě zdánlivě podobné proměnné pro účastníky, které ale dělají odlišné věci. **Nikdy nepoužívejte zastaralou proměnnou `newParticipants`!**

1. **`participants`** (např. `participants: "650+"`)
   - Udává **historický celkový počet** účastníků.
   - Slouží čistě jako text (takže se k němu smí psát znaménko plus).
   - Zobrazuje se v bento boxu `participants_count`.

2. **`maxParticipants`** (např. `maxParticipants: "300"`)
   - Udává tvrdou **kapacitu blížícího se ročníku**.
   - Slouží jako matematická hodnota (číslo) pro výpočet procent.
   - Zobrazuje se v bento boxu `live_registration` (progress bar s naplněností). Společně s ním je nutné mít vyplněné `registrationSheet` (URL na CSV export tabulky).

Příklad použití v hlavičce města:
```yaml
bentoBoxes:
  - "events_count"
  - "participants_count"
  - "speakers_count"
  - "live_registration"

events: "3"
participants: "650+"
maxParticipants: "300"
```
Pokud `bentoBoxes` nedefinujete, místo použije starý hardcodovaný systém. Doporučujeme u nových i starých míst přejít na `bentoBoxes`. (Staré boxy tam nechávám jako záchrannou síť, než si ty aktuální migrujete).

## 2. Aktuální události (Karty)
Aktuality na domovské stránce (karusel) a sekce Historie tahají data ze souboru `/data/cards.yaml`.
Tento systém podporuje dva typy karet: `type: "update"` a `type: "event"`.

### Parametr `hidden: true`
Nyní můžete kteroukoliv událost na domovské stránce skrýt pomocí `hidden: true`. 
```yaml
- date: 2026-06-08
  type: event
  hidden: true
  title: Zvaž vědu! Tajná akce
  link:
    url: "/tajne/"
    text: O akci
```
Tato akce **zmizí** z karuselu "Aktuálně" na hlavní stránce. Ve výpisu `/historie/` se však stále ukáže (je-li datum starší), ale **nebude mít žlutou barvu jako event**, bude vizuálně spadat pod standardní "Update". Zůstane tak zachována kontinuita historie bez zaneřádění hlavní stránky barevnými eventy, které např. už nechcete propagovat.

## 3. Responzivita a Animace
Byly opraveny následující vizuální vlastnosti pro vývojáře:
- **Žádný horizontální scroll**: `html, body` je tvrdě omezeno na max. `100vw`. Web nikdy nepřeteče doprava.
- **Pořadí sekcí**: U měst platilo globální pravidlo obracení sloupců na mobilech (`column-reverse`), což bylo zrušeno. Fotografie týmu by měly být vždy správně uspořádány.
- **Plynulost hover efektů**: Všechna CSS tlačítka a efekty využívají `--transition: all .2s ease-in-out;` v `root.css`. Loga partnerů, fotky týmů a tlačítka se nyní plynule prolínají nebo animují namísto bleskového blikání. (Dřívější chyba spočívala ve špatně pojmenované CSS jednotce u transition proměnné a v použití okamžitých vlastností display: none namísto opacity).
- **Fotka s textem na mobilu**: na stránkách se šablonou `single` (např. „O nás“) se sekce s fotkou a textem (`text-img`, `title-text`) na úzkých displejích (do 900 px) skládají pod sebe, fotka nahoře. Řeší to pravidlo `#content section .layout-content:has(> .img-body)` v `single.css`. Dříve zůstávaly vedle sebe a text byl stlačený do úzkého sloupce.
- **Loga na mobilu**: Na mobilních zařízeních a tabletech se již logo v horním menu nevyměňuje za malý symbol. Plné textové logo zůstává vždy ukotveno.

## 3a. Anglická verze (`/en/`)
Anglická verze („Consider Science!“) obsahuje jen základní informace o projektu (co děláme, jak velcí jsme, kde pořádáme akce). **Texty jsou statické** a píšou se ručně v angličtině, ale **čísla, fotky, tým a partneři se berou z české části webu**, takže se po úpravě české stránky aktualizují i tady. Žádný odkaz z anglické verze nevede na český web (přepínač jazyka záměrně není).

- **Kde jsou soubory**: celá anglická verze je ve složce **`english/`**, oddělená od české části. `english/content/` jsou stránky, `english/layouts/` šablony (`baseof.html`, `section.html`, `city.html`, `contact.html`) a `english/partials/` pomocné části (mapa a mobilní menu). Hugo tyto složky „připojí“ (mounts v `hugo.toml`) na místa, kde je očekává: `english/content` jako `content/en` (adresy `/en/...`), `english/layouts` jako `layouts/en` a `english/partials` do `layouts/partials`. Jinde v repozitáři jsou jen věci, které už mají své místo: anglické loga ve `static/media/imgs/base/`, pole `position_en` a `team_en` v `data/team.yaml` a tato dokumentace. Pokud do `hugo.toml` přidáte další složku (např. `assets`), musíte ji do `[module]` přidat také, protože mounts nahrazují výchozí složky Hugo.
- **Obsah**: `english/content/` obsahuje `_index.md` (domů), `about.md` (o nás), `contact.md` (kontakt) a stránku pro každé město (`pilsen.md`, `brno.md`, `prague.md`, `liberec.md`, `ceske-budejovice.md`, adresy např. `/en/pilsen/`). Města mají v angličtině názvy Pilsen, Brno, Prague, Liberec a České Budějovice. `about.md` používá stejné shortcody a bloky jako česká stránka „O nás“ (`text-img`, `title-text`, karty z `offers.css`).
- **Stránka města** (`layout: city`, šablona `english/layouts/city.html`): v anglickém souboru je `headerTitle` (např. „in Pilsen“), `city` (anglický název města), `venue` (místo konání anglicky), `czechPage` (cesta české stránky města, např. `/plzen`) a v těle stránky **stručný anglický souhrn dosavadních akcí ve městě** (zkrácené shrnutí českých ohlédnutí, např. `content/plzen/plzen23.md` až `plzen25.md`). Stránka má vzhled jako české ohlédnutí: text vlevo a fotogalerie vpravo (v galerii je úvodní fotka `main.webp` a dalších zhruba 11 rovnoměrně vybraných fotek, kliknutím se otevře lightbox). Po každém novém ročníku souhrn ručně doplňte. Z české stránky města (`czechPage`) se automaticky berou: hlavní fotka v hlavičce (`headerPhoto`), čísla `events`, `participants`, `speakers` (nulové nebo chybějící se skryjí), tým (`team`), partneři (stejná logika jako na české stránce, jen s anglickými nadpisy) a **fotogalerie nejnovějšího ročníku**, kterým je nejnovější stránka v české složce města (podle `date`, fotky ve `static/media/imgs/gallery/<název souboru>/`). Termíny se na anglických stránkách měst záměrně neuvádějí. Nové město přidáte souborem `english/content/<město>.md` podle ostatních a odkazem v mapě, hero tlačítkách a patičce.
- **Kontakt** (`layout: contact`, šablona `english/layouts/contact.html`): stejný vzhled a data jako česká stránka „Náš tým“. Jména, fotky a kontakty jsou v `data/team.yaml`. **Anglická pozice se píše přímo v `data/team.yaml`** do pole `position_en`, ve stejném pořadí jako česká `position` (první česká pozice = první anglická atd.). Příklad: `position: ["vedoucí"]` a `position_en: ["head of department"]`. Pokud u člena `position_en` chybí nebo je kratší než `position`, anglická stránka zobrazí českou pozici a **`hugo` při sestavení vypíše varování** `data/team.yaml: <jméno> has no English position`. Stejně se píšou **anglické názvy oddělení** do pole `team_en` (opět ve stejném pořadí jako česká `team`); chybějící název vypíše při sestavení také varování. Pořadí oddělení na stránce je v šabloně `english/layouts/contact.html`, stejně jako v české `layouts/_default/contact.html`; při přidání nového oddělení ho přidejte do obou.
- **Hlavní stránka**: texty jsou přímo v `english/layouts/section.html`, čísla (akce, řečníci, účastníci, školy) se berou z front matter české hlavní stránky `content/_index.md`.
- **Styly**: žádné vlastní CSS. Anglická verze používá stejné soubory jako česká (`home.css`, `single.css`, `offers.css`, `location.css`, `contact.css`, `base.css`) a stejná ID a třídy, takže vypadá stejně a funguje světlý i tmavý režim. Pozor: změna těchto stylů se projeví i na anglické verzi.
- **Šablony**: `english/layouts/baseof.html` (horní menu, patička), `english/layouts/section.html` (hlavní stránka) a partials `en-mobile-top-bar.html` a `en-mobile-off-canvas-menu.html`. O nás používá běžné `layouts/_default/single.html`, kontakt `english/layouts/contact.html` a města `english/layouts/city.html`.
- **Logo**: anglické logo je ve stejné složce jako ostatní loga, `static/media/imgs/base/logo_en-black.svg` (pro světlý režim) a `logo_en-white.svg` (pro tmavý režim). Používá se v horním menu, v mobilním menu a v patičce anglické verze (`english/layouts/baseof.html` a partials `en-mobile-top-bar.html`). Při změně loga stačí oba soubory přepsat. Pokud bude v jiném formátu (např. WebP), změňte cesty v `baseof.html` a v `en-mobile-top-bar.html`.
- **Mapa**: `english/partials/en-map.html` je kopie mapy z české hlavní stránky, ale oblasti měst vedou na **anglické stránky měst** (`/en/<město>/`), ne na české. Odkazy na města jsou také v horním a mobilním menu, v hero tlačítkách na hlavní stránce a v patičce (`english/layouts/section.html`, `english/layouts/baseof.html`). Pokud změníte mapu v `layouts/_default/home.html`, změňte ji i zde.
- **Typografie**: skript `scripts/format_typography.py` prochází jen `content/` a `data/`, takže složku `english/` nijak neupravuje (nevkládá do angličtiny české nedělitelné mezery).
- **Test**: `test_layout.py` testuje i všechny anglické stránky (`/en/`, `/en/about/`, `/en/contact/` a stránky měst).

## 4. Rozcestník registrací (/registrace/)
Stránka pro registrace `/registrace/` již nefunguje jako přesměrování (dříve s `layout: "port"`), ale zobrazuje rozcestník umožňující registraci na události ve více městech.
Stránka má vlastní šablonu v `layouts/_default/registrace.html` a obsah tahá strukturovaně z front matter souboru `content/registrace/index.md`. 
Pokud potřebujete přidat další město (odkaz), stačí v tomto souboru do sekce `options` přidat:
```yaml
  - title: "Registrace – Nové Město"
    text: "Krátký popisek k akci."
    url: "odkaz_na_gforms"
```

### Automatické napojení na město (ukazatel kapacity)
Karty v rozcestníku umí automaticky zobrazit progres bar (ukazatel aktuálně registrovaných účastníků), pokud je propojíte s existujícím městem.
K tomu slouží parametr `cityRef`. Pokud jej uvedete, rozcestník si automaticky "sáhne" do daného města (např. `/plzen`), přečte si z něj URL Google tabulky (`registrationSheet`), maximální kapacitu a registrační odkaz (pokud nevyplníte vlastní `url`).

```yaml
  - title: "Okno do vesmíru #4"
    text: "Ve čtvrtek 17. září od 15:00 v Semlerově rezidenci v Plzni"
    cityRef: "/plzen"
```
*Poznámka: Aby ukazatel kapacity fungoval, dané město (zde `/plzen/_index.md`) musí mít v hlavičce definované položky `registrationSheet` a `participants` (nebo `maxParticipants`).*
