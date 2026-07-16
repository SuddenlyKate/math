# Vizualizace funkcí

Interaktivní webová aplikace pro vizualizaci matematických funkcí — od goniometrie a limit přes 3D plochy až po komplexní analýzu. Celá aplikace je **jediný soubor `index.html`** bez jakýchkoli knihoven a build kroků: stačí ho otevřít v prohlížeči.

Vznikla jako pomůcka pro pochopení látky z matematické analýzy — místo aby vzorec jen „platil na papíře", je vidět, co dělá.

---

## Co to umí

Aplikace má devět režimů (záložek). Kreslení běží na jednom `<canvas>`; každý režim má vlastní ovládací panel.

| Záložka | Co ukazuje |
|---|---|
| **Goniometrické funkce** | `a·sin(bx+c)+d` (i cos, tan) se slidery parametrů; odvozené vlastnosti — amplituda, perioda, fázový a svislý posun |
| **Jednotková kružnice** | Obíhající bod na kružnici synchronizovaný s rozvíjející se sinusovkou; sin/cos/tan jako úsečky, přesné hodnoty význačných úhlů |
| **Limity** | Výuková galerie limit funkcí s vysvětlením, animací přibližování a tabulkou konvergujících hodnot |
| **Inverzní funkce** | Funkce a její inverze zrcadlené podle osy y = x |
| **Vlastní funkce** | Vlastní předpisy (až 6 najednou); tečna s derivací, křivka derivace i integrální funkce, Riemannovy součty, Taylorův polynom, limita v bodě, významné body (nuly, extrémy, průsečíky) |
| **Posloupnosti** | Členy posloupnosti jako body, odhad limity, ε-pás a hledání indexu n₀ |
| **3D plochy** | Plochy `z = f(x,y)`, parametrické křivky i plochy (koule, torus, Möbiova páska), rotační tělesa; řezy, vrstevnice, tečná rovina s gradientem, dvojné Riemannovy součty, osvětlení, definiční obory |
| **Komplexní funkce** | Domain coloring funkcí `w = f(z)` — odstín = argument, světlost = velikost; včetně speciálních funkcí **gamma** a **zeta** |
| **Trojúhelník** | Konstrukce a výpočty (SSS/SUS/USU), kontrola sestrojitelnosti, dopočet úhlů, obsahu, kružnic opsané/vepsané |

Napříč aplikací: světlý i tmavý režim, zoom a posun grafu, odečítání souřadnic, export do PNG.

---

## Jak to spustit

Žádná instalace, žádné závislosti:

```
Stáhni index.html a otevři ho v prohlížeči.
```

Vše — parser, vykreslování, 3D engine, komplexní aritmetika — je v tom jednom souboru v čistém HTML, CSS a JavaScriptu.

---

## Jak je to postavené

Přes 11 000 řádků, ale s jasnou strukturou. Hlavní vrstvy:

**Parser výrazů.** Textový předpis (`2sin(x)+x^2`) se rozloží na tokeny a přeloží rekurzivním sestupem. Ten postupuje od operací s nejnižší prioritou k nejvyšší — sčítání a odčítání, pak násobení a dělení (i implicitní, „2x"), unární minus, mocnina a nakonec základ (číslo, závorka, funkce). Tím, že se nejvolněji vázané operace zpracují jako první a nejtěsněji vázané nejhlouběji, vznikne správná priorita sama. Parser nevytváří strom, ale rovnou skládá spustitelné funkce. Zvládá definiční obory za středníkem (`sqrt(x); x >= 0`), a v samostatné větvi i **komplexní aritmetiku** (operace nad dvojicemi [reálná, imaginární část]).

**Kreslicí jádro.** Převod matematických souřadnic na pixely a zpět; adaptivní vzorkování s detekcí asymptot (aby `tan(x)` nekreslil svislé čáry přes body nespojitosti) a jejich zpřesněním bisekcí.

**Numerické metody** (vlastní, bez knihoven): bisekce pro nulové body a průsečíky, centrální diference pro derivace, kumulativní lichoběžníkové pravidlo pro integrální funkci, metoda nejmenších čtverců (s Gaussovou eliminací) pro Taylorovy koeficienty, marching squares pro vrstevnice a průsečnice ploch, Lanczosova aproximace pro funkci gamma a funkční rovnice pro zetu.

**3D engine.** Vzorkování plochy na mřížce, rotace kamery, perspektivní projekce, malířův algoritmus pro viditelnost a Lambertovské osvětlení (normály přes vektorový součin). Vše ručně na 2D canvasu, bez WebGL.

**Domain coloring** komplexních funkcí se kreslí po pixelech přes `ImageData`, s progresivním renderem (`requestAnimationFrame`), aby drahé funkce jako zeta neblokovaly UI.

---

## Jak to vzniklo

Projekt začal jako jednoduchá vizualizace goniometrických funkcí a postupně se rozrostl — přes limity, derivace, integrály a Riemannovy součty (v podstatě celý první ročník matematické analýzy v obrázcích) až k 3D plochám a komplexní analýze.

Hlavní kód psal AI asistent (Claude Code) na základě mých zadání. Moje role byla návrhářská a řídicí: rozhodovala jsem, co se bude přidávat a jak se to má chovat, formulovala jsem zadání, testovala výsledky a hledala chyby. Řada vlastností vznikla právě z odhalených problémů — například falešné svislé čáry u tangens (podvzorkování, Nyquistův limit), chybějící polovina „srdíčka" (mocnina záporného základu), nebo Taylorovy koeficienty závislé na zoomu (fit přes špatně zvolené okolí).

Postupovala jsem po malých ohraničených krocích a numerický kód jsem ověřovala proti hodnotám, které jdou spočítat nezávisle — Γ(5) musí vyjít 24, ζ(2) = π²/6, objem polokoule o poloměru 2 = 16π/3, Taylorovy koeficienty sin(x). Když kontrolní hodnoty sedí, dá se věřit i složitějším výsledkům. Tenhle přístup („funguje" ještě neznamená „počítá správně") se táhne celým projektem.

---

## Technologie

Čistý HTML + CSS + JavaScript, Canvas 2D API. Žádné frameworky, žádné knihovny, žádný build. Jeden soubor.
