# Håndover — Askø & Lilleø hjemmeside

> Status pr. 15. juli 2026. Skrevet til at blive læst af en ny Claude-session.
> Sig fx: *"Læs HANDOVER.md, så fortsætter vi derfra."*

---

## 1. Hvad er projektet

Hjemmesiden for **Askø/Lilleø Beboerforening** — to små øer i Smålandshavet, forbundet af en dæmning fra 1914.

- **Mappe:** `/Users/lucasbuch/Desktop/Askø-Lillø hjemmeside`
- **Repo:** https://github.com/Lucfbuch/Ask--Lille- (branch `main`)
- **Live:** https://lucfbuch.github.io/Ask--Lille-/ (GitHub Pages, deployer automatisk ved push til `main`)
- **Teknik:** Almindelig statisk HTML/CSS/JS. Ingen framework, ingen build. Man retter i filen, committer, pusher — så er det live efter ~1 min.

**Alt er committet og pushet. Arbejdstræet er rent.** Seneste commit: `53eac89 Gør seværdighedskort klikbare med læs mere-modal`.

---

## 2. Sider

| Fil | Rolle |
|---|---|
| `index.html` | Forside: hero med luftfoto, stats, 4 indgange, havnekort-teaser, årstider, kalender-teaser, CTA |
| `turist.html` | Dagsturisten. Seværdigheder (8 klikbare kort m. modal) → dagsprogram → kort → sejler-CTA → praktisk |
| `sejlere.html` | **Flagskibet.** Interaktivt havnekort, faciliteter, priser, kom i land |
| `oebo.html` | Øboer og fritidsbeboere: praktisk, nyhedsmail, "overvejer du at flytte hertil" |
| `oe-liv.html` | Ø-liv **og** foreninger (blev slået sammen — de overlappede) |
| `historie.html` | Øernes historie |
| `kalender.html` | Arrangementer |
| `foreninger.html` | **Kun en viderestilling** til `oe-liv.html#foreninger`. Rør den ikke — den holder gamle links i live |

Navigationen har 6 punkter: Turist · Sejlere · Øbo · Ø-liv · Historie · Kalender.

---

## 3. Vigtige arbejdsregler (lært på den hårde måde)

1. **Cache-busting er obligatorisk.** Alle sider linker til `styles.css?v=18`. **Ret du i `styles.css`, så tæl `v` op på ALLE html-filer** — ellers ser Lucas den gamle CSS, og vi har haft en grim bug ud af netop det (kæmpe sort kamera-ikon på live-siden).
   ```bash
   sed -i '' 's/styles.css?v=18/styles.css?v=19/' index.html turist.html sejlere.html oebo.html oe-liv.html historie.html kalender.html
   ```
2. **Deploy, så Lucas kan se det.** Min lokale `python3 -m http.server` kører kun på min maskine — han kan ikke se mit preview. Verificér lokalt, og **push så**, og bed ham hard-reloade (**Cmd + Shift + R**).
3. **Screenshots i preview er upålidelige** efter programmatisk scroll (blanke billeder). Verificér i stedet DOM'en med `javascript_exec`.
4. **Inline SVG skal have eksplicitte `width`/`height`/`fill`/`stroke`-attributter** — ikke kun CSS. Ellers eksploderer de, hvis CSS'en er cachet.
5. **zsh word-splitter ikke variabler** som bash. `sed ... $FILER` virker ikke — skriv filnavnene ud.
6. **Verificér fakta mod kilden, og gæt aldrig på tal.** Vi har spurgt Lucas direkte, når vi var i tvivl (fx havnetaksterne).
7. **Kopiér ikke Askø Sejlklubs fotos** — vi har bevidst ladet være uden tilladelse.
8. Alt indhold er på **dansk**, i en varm og ligefrem tone. Ingen udråbstegn-salg.

---

## 4. Design

Designet er lavet efter inspiration fra **Visit Ærø** og **Lithuania Travel** (Lucas' egne ord: *"Jeg elsker visit ærø og lithuania"*). Navy-domineret.

`:root` i `styles.css` — skift `--hav`, og det slår igennem overalt:
```css
--hav:#1D3B64;        /* navy */
--hav-dyb:#14294C;    /* dyb navy */
--soeglas:#EFF3F0; --skum:#FFFFFF;
--aeble:#C13B2A;      /* rød accent (knapper, pile) */
--blomst:#E9C3BC; --tang:#4E6E5D;
--blaek:#1B2A2E; --graa:#4A5A5E;
```
Æblegrøn `#AED98C` bruges til `<em>` i foto-heroes (fx *"På én dag."*).

**Nøglekomponenter:** `.foto-kort` (billedkort m. hover-zoom) · `.ph` (placeholder-boks) · `.info-modal` (læs mere) · `.lightbox` (havnekort) · `.reveal` → `.vist` (scroll-fade via IntersectionObserver i `script.js`) · `.moerk` (mørke sektioner) · `.pris-grid` · `.foto-annoteret`/`.foto-tag` (havnekortets labels).

---

## 5. Placeholder-systemet — vigtigt at forstå

Der hvor vi mangler fotos, står en **markeret stiplet boks** (`.ph`) med præcis besked om, hvilket billede der skal tages. Det var Lucas' eget ønske: *"så smutter jeg ud og tager nogle billeder"* — så **fjern dem ikke**, de er hans arbejdsseddel.

Når et foto kommer ind, konverteres det sådan:
```bash
sips -s format jpeg -Z 1600 -s formatOptions 80 "/Users/lucasbuch/Desktop/Foto.HEIC" --out "images/navn.jpg"
```
…og `.ph`-boksen erstattes med `<img src="images/navn.jpg" alt="..." loading="lazy">`.

### Fotolisten der stadig mangler
**Heroes:**
- `historie.html` — gammelt foto fra Askø Museum (fx færgen eller mejeriet)
- `kalender.html` — sommerudflugt, julebanko eller fællesspisning
- `oebo.html` — landsbyen, et sommerhus eller færgen der lægger til

**Kort på `index.html`:** landsbyen · æbleblomstring (maj) · æblepluk (sept–okt) · vinterstille havn/trækfugle

**Kort på `turist.html`:** Askø Kirke · Askø Museum · Kirkestien · æbleplantagerne · fugle over strandengen

*(Bemærk: modalens tekster i `turist.html` har deres egen `note:`-tekst i JS'en, som også skal skiftes til `img:` når fotoet kommer.)*

---

## 6. Det vi lige har lavet (seneste tre commits)

1. **Café Askø Krukken + Askøs gårdbutik** tilføjet under seværdigheder med rigtige fotos (`images/krukken.jpg`, `images/gaardbutik.jpg`).
2. **Seværdigheder flyttet op over dagsprogrammet** — først *hvad* man skal opleve, så *hvordan* dagen skrues sammen.
3. **"Læs mere"-modal** (nyeste). Alle 8 seværdighedskort er nu `<button data-emne="...">` og åbner et vindue med stort billede, 2 afsnit uddybende tekst og en handlingsknap. Inspireret af Ærøs kort. Lukkes med kryds, **Esc** eller klik udenfor. Data ligger i objektet `emner` i et inline `<script>` nederst i `turist.html`. Kort uden foto viser placeholderen inde i modalen.

> ⚠️ **Teksterne i modalerne bør Lucas læse korrektur på.** Især Krukken og gårdbutikken er skrevet ud fra hans beskrivelser og skiltning, ikke fra en officiel kilde. Kirkens historie (altertavlen, kirkeskibet *"Askø's Enighed"* fra 1867, pest-tjørnen fra 1710-11) stammer fra research undervejs.

---

## 6b. Vejviser-kortet (juli 2026 — sidens nye signatur-feature)

Interaktivt kort på **både sejlere.html (#vejviser) og turist.html (#kort)**: tryk på et mål, og en rød rute tegner sig fra havnen; når den er fremme, glider en info-boks frem (foto + "Læs mere" der åbner seværdigheds-modalen). Knappen "Vandrestier" tænder/slukker Kirkestien + kystruten som rød stiplet lag.

- **Grundkort:** `images/kort-askoe.png` — OSM-fliser (zoom 15) syet sammen. Kildeangivelse "© OpenStreetMap contributors" er obligatorisk og står under kortet.
- **Geometri:** ruterne er OSRM-gåruter (følger vejene), afstandene er ægte. Mål-koordinater er slået op via Nominatim/Overpass; Lucas har leveret adresserne (Gårdbutik: Lilleøvej 27 · Købmand: Konemadevej 45 · Shelterplads: Askø Skolevej 8).
- **Vandrestierne** er OSM-stier, som Lucas har kurateret hårdt (mange segmenter fjernet som "ikke stier"), plus to håndlagte stykker efter hans anvisning: Kirkesti-forbindelsen langs grøften og kystruten om Østerhoved (følger kystlinje-polygonen + engens nordkant).
- **Genbyg/udvid:** scripts ligger i scratchpad (byg_kort.py, byg_ruter2.py m.fl.) — men geometrien er indbagt i HTML'ens SVG, så nye mål kræver at køre pipeline igen (Nominatim → OSRM → pixels via web-mercator, Z=15, NV-hjørne 54.9170/11.4690).
- **Dybe links:** `turist.html#se-<emne>` åbner seværdigheds-modalen direkte (hash-handler + hashchange i turist.html).
- Pas på: `.reveal` + rutetegning kræver synlig fane — i throttled/occluded browser-pane fryser animationer (0 rAF); det er miljøet, ikke koden.

## 7. Verificerede fakta (brug disse — de er tjekket)

- **Havnetakster 2026** (Lolland Havnes takstblad, bekræftet af Lucas): 0–7,99 m = 205 kr · 8–10,99 m = 225 kr · 11–13,99 m = 270 kr · 14–16,99 m = 320 kr · 17–19,99 m = 385 kr · 20 m+ = 22 kr/m · strøm 26 kr/døgn.
- **Ingen overnatning** på øerne ud over telt. Derfor brander vi bevidst på endagsturen: **"Oplev to øer. På én dag."** Lucas var meget klar her — lov aldrig senge, vi ikke har.
- **Færgen:** Kragenæs ⇄ Askø, ca. 45 min. **Færgetider må IKKE skrives på siden** — Lucas fik dem fjernet, fordi de ikke passede. Link altid til booking i stedet.
- **Købmandens åbningstider:** link til http://www.askoe-koebmand.dk/åbningstider — aldrig skriv dem af.
- Sejlerhero'ens slogan er **"Læg til. Mærk roen."** — *ikke* "Kast fortøjningen" (det betyder, at man sejler væk; Lucas fangede den).

---

## 8. Ideer på hylden (Lucas' egne, ikke afvist — bare udskudt)

- **Live-webcam i havnen** — *"det var bare en god ide. Den gemmer vi til senere."*
- Engelsk/tysk version
- Cykeløerne (68 km øhop)-kort
- Til sejlersiden: dybgang ~2,5 m · kun indsejling i dagslys (Lindholm Dyb er ubelyst) · sæler på "Den Toppede Sten"

---

## 9. Ting der er blevet afvist — foreslå dem ikke igen

- **Håndtegnet SVG-skitse af havnen** — *"Er det den bedste skitse du kan lave af en havn? Hvis ikke du kan gøre det bedre så bare fjern det."* Vi bruger nu labels oven på det rigtige luftfoto. Det virker.
- **Æble-blad på "o"-et i "roen"** — forsøgt to gange, kunne ikke se intentionelt ud i den størrelse. Droppet i enighed.
- **Højrestillet hero-tekst** på forsiden — *"Nej tilbage igen."*
- **Cremefarvet/mørk basis-palet** — *"lidt for mørkt/basic"*, de mørke baggrunde var *"mah"*. Derfor navy.
- **Bornholm-agtig topbjælke** (skråt logofelt + farvet bjælke med store ikon-knapper, juli 2026) — *"Ingen af delene, syntes det ser rodet ud."* Prøvet i grøn og navy variant. Bemærk dog: ønsket bag den (store, ældre-venlige knapper) står stadig — måske via større forsidekort i stedet.
- **Logomærke af øernes silhuetter + navy navigationsbjælke** (juli 2026) — *"Det er virkeligt dårligt det du har lavet der, bare drop det."* Forsøgt som tegnede ø-former (både frit abstraheret og tegnet efter luftfotoet), i segl-cirkel og set fra siden, kombineret med en navy bjælke og rød "Book færgen". Ingen af delene holdt. **Bemærk:** Lucas synes stadig, bjælken og ordmærket "Askø & Lilleø" er kedelige — problemet er ikke løst, men *det her* er ikke svaret. Prøv en anden vej end et tegnet mærke.

---

## 10. Standard-workflow

```bash
cd "/Users/lucasbuch/Desktop/Askø-Lillø hjemmeside"
# 1. ret filerne
# 2. hvis styles.css er rørt: tæl v= op på alle html-filer
python3 -m http.server 8801 &          # 3. preview
# 4. verificér DOM'en med javascript_exec (ikke screenshots)
pkill -f "http.server 8801"            # 5. luk
git add -A && git commit -m "..." && git push origin main
# 6. sig til Lucas: hard-refresh med Cmd+Shift+R
```

Commit-beskeder er på dansk, i bydeform: *"Gør seværdighedskort klikbare med læs mere-modal"*.

Tilladelser ligger i `.claude/settings.local.json` (gitignored) — `acceptEdits` + brede allows, så der ikke skal trykkes "allow" hele tiden. Det var et udtrykkeligt ønske.
