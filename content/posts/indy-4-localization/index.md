+++
title = "Localizing 'Indiana Jones and the Fate of Atlantis' 30 years after release"
date = 2023-11-18
draft = false
tags = ['digital archeology', 'retro','localization','game']
author = "retro"
authorLink = "https://github.com/simi"
+++

Nesnáším podzim! Veškerý volný čas, jenž se dá v léte smysluplně trávit nesmyslným tlacháním u piva a kořalky, v hospodě, venku na zahrádce, s rodinou a přáteli, najednou není čím naplnit. A tak si hledávám zábavu jinou - digitální. Už ani nevím proč, ale měl jsem na podzim 2022 nápad přeložit hru [Peglin](https://gedig.itch.io/peglin) do češtiny. Začal jsem se zajímat jak takové komunitní překlady vznikají, objevil jsem [prekladyher.eu](https://prekladyher.eu) no a co se stalo dál asi tušíte podle nadpisu. Správně, Peglin jsem nikdy nepřeložil. Narazil jsem ovšem na [žádost o pomoc s lokalizací cca 30 let staré hry](https://prekladyher.eu/vlakno/indiana-jones-and-the-fate-of-atlantis.4333/#post-54513).

*Poznámka autora: Ano, datum té žádosti (ani mé odpovědi) moc nezapadá do podzimu. To však nevyvrací fakt, že nesnáším podzim.*

# Seznámení se hrou

Hru "[Indiana Jones and the Fate of Atlantis](https://en.wikipedia.org/wiki/Indiana_Jones_and_the_Fate_of_Atlantis)" (dále jen Indy4) jsem jako malý chvilku hrál, ale protože byla anglicky, tak jsem moc netušil, co se tam děje. Ovšem dostala se ke mě česká verze předchozí hry ze série - "[Indiana Jones and the Last Crusade](https://en.wikipedia.org/wiki/Indiana_Jones_and_the_Last_Crusade)" (dalé Indy3) a u té jsem vytuhnul na opravdu dlouho. Tušil jsem tedy cca do čeho jdu. Klasická [Lucasfilm Games](https://www.lucasfilm.com/what-we-do/games/) adventura postavená na [SCUMM enginu](https://en.wikipedia.org/wiki/SCUMM).

Sehnal jsem si tedy kopii (*tentokrát již legální na [GOGu](https://www.gog.com/game/indiana_jones_and_the_fate_of_atlantis), a pro jistotu jsem koupil rovnou in [Indy3](https://www.gog.com/en/game/indiana_jones_and_the_last_crusade) a tím splatil jistý starý dluh*). Už tady na mě číhala první nástraha. Indy4 bylo vydáno v několika verzích. GOG prodává tzv. "Talkie" verzi, ta obsahuje i kompletní anglický dabing. Pro jistotu jsem ještě našel původní DOS verzi na archive.org a porovnal je. Soubory jsou stejné a přesně tuhle verzi jsem hledal. Hru jsem úspešně nainstaloval a spustil (na Linuxu přes [ScummVM](https://www.scummvm.org/)).

Dle [ScummVM wiki Indy4](https://wiki.scummvm.org/index.php/Indiana_Jones_and_the_Fate_of_Atlantis) obsahuje 3 hlavní datové soubory. Ty jsem našel v adresáří se hrou (v mém případě `/opt/Games/Indiana Jones and the Fate of Atlantis`) ve složce `data`.

```
[retro@retro  Indiana Jones and the Fate of Atlantis]❤ ls -lh data
total 153M
-rw-r--r--. 1 retro retro  12K Nov 12 16:38 ATLANTIS.000
-rw-r--r--. 1 retro retro 9.4M Nov 12 16:38 ATLANTIS.001
-rw-r--r--. 1 retro retro 143M Oct 15 14:00 MONSTER.SOU
drwx------. 2 retro retro 4.0K Oct 15 14:00 other
```

Prostou dedukcí (podle velikosti souborů) jsem usoudil, že v souboru `MONSTER.SOU` bude dabing. To jsem si i potvrdil porovnáním se soubory z verze bez dabingu, kde tento soubor úplně chybí. Stejně tak jsem se podíval na [původní překlad (bez české lokalizace)](https://www.idnes.cz/hry/cestiny/hry/indiana-jones-4-and-the-fate-of-atlantis.A061005_59388_bw-cestiny-hry_bw). Ten obsahuje právě soubory `ATLANTIS.000` a `ATLANSTIS.001`. Překlad jsem stáhl a aplikoval. Hru mám tedy česky, ale české znaky (`áčďéěíňóřšťúůýž` a `ÁČĎÉĚÍŇÓŘŠŤÚŮÝŽ`) chybí.

**A to je právě výzva pro mě. Naučit tuhle 30 let starou hru vykreslit správně například `č`.** Překlad z roku 2006 nahrazuje všechny znaky obsahující diakritické znaménko znaky bez něho. Čili místo `Stlač MEZERNÍK` hra vykreslí `Stlac MEZERNIK`.

![(1) překlad obsahuje c místo č](01-old-c.jpg "(1) překlad obsahuje c místo č")

## Práce s herními soubory

Ačkoliv se může zdát, že práce na 30 let staré hře má jen samé nevýhody, je tu i jistá výhoda. Lidstvo mělo 30 let času (čili 30 podzimů) tuto hru (bez zdrojových kodů) pomocí [reverzního inženýrství](https://en.wikipedia.org/wiki/Reverse_engineering) rozebrat a zdokumentovat. A díky tomu existuje sada nástrojů pro SCUMM hry jménem [scummtr](https://github.com/dwatteau/scummtr). Pár chvilek hledání po [fórech](https://forums.scummvm.org/), pár položených otázek na [ScummVM Discordu](https://discord.gg/4cDsMNtcpG) a nakonec i přečtené [README](https://github.com/dwatteau/scummtr/blob/main/README.md)... a hned mám jistou představou co tyto nástroje umí.

Pro začátek budu potřebovat vyexportovat ze hry texty. K tomu slouží program `scummtr` s parametrem `-o`. A naopak importovat text do hry lze stejným programem s parameterm `-i`.

```
$ /cesta/k/scummtr -g atlantis -p /cesta/k/indy4 -r -o -f texts.txt
$ /cesta/k/scummtr -g atlantis -p /cesta/k/indy4 -r -i -f texts.txt
```

Jelikož už teď vím, že mě nebude bavit pořád dokola psát tenhle příkaz (ani ty další), a také tuším, že ho budu používat často, začnu si to trochu organizovat. Na Linuxu je dobrá (moje) volba [Makefile](https://en.wikipedia.org/wiki/Make_(software)). *Na Windows by asi stačil [bat](https://en.wikipedia.org/wiki/Batch_file) soubor.* Dalé tuším že budu potřebovat hru spouštět pro testování a jako bonus si můžu přednastavit cesty k souborům a zkrátit tak příkazy na minimum. *Do souboru budeme průběžně přidávat další úlohy.*


```
INDY4_DATA_PATH="/opt/Games/Indiana Jones and the Fate of Atlantis/data"
SCUMMTR_BIN_PATH="/opt/scummtr/build/bin"

test:
	scummvm -p $(INDY4_DATA_PATH) --auto-detect

export-text:
	$(SCUMMTR_BIN_PATH)/scummtr -g atlantis -p $(INDY4_DATA_PATH) -r -o -f texts.txt

import-text:
	$(SCUMMTR_BIN_PATH)/scummtr -g atlantis -p $(INDY4_DATA_PATH) -r -i -f texts.txt
```

Stačí pustit `make export-text` a ve stejné složce se vytvoří soubor `texts.txt` s texty ze hry. Texty jsou již česky bez diakritiky, protože jsem na hru aplikoval původní češtinu. Jako jednoduchý test stačí v souboru najít testovací řetězec `Stlac MEZERNIK`, přepsat ho na `Stlač MEZERNIK`, uložit a naimportovat zpět do hry spuštěním `make import-text`.

*Za povšimnutí stojí že používám editor Notepad++ (přes Wine, nemá Linuxovou nativní verzi) a kódování OEM 852. Jak jsem k tomu dospěl vysvětlím později, byl to jeden z největších oříšků. V této fázi by fungovalo jakékoliv dobové (DOS) kódování.*

![(1) upravený text s diakritikou, (2) kódování OEM 852](02-editor.jpg "(1) upravený text s diakritikou, (2) kódování OEM 852")

Nakonec stačí pustit `make test`, po chvilce zmáčknout mezerník (což zapauzuje hru) a podívat se co hra vykreslí. Jelikož úkol je přidat vykreslování diakritiky, předpokládám, že hra nevykreslí `č`, a mám pravdu. Místo znaku `č` nevykreslí hra pro jistotu nic.

![(1) hra vynechá č](03-missing-c.jpg "(1) hra vynechá č")

# Úprava herního fontu

Sada nástrojů `scummtr` obsahuje i nástroj `scummfont`, kterým je možné (alespoň podle `README`) vyexportovat font z herních souborů jako [bitmapu](https://en.wikipedia.org/wiki/Bitmap) (formát bmp, *ten otevře i Malování*). Chvilku mi trvalo, než jsem pochopil, jak tohle funguje. Tento nástroj se totiž nepoužívá na herní soubory (`ATLANTIS.00X`) přímo, ale na soubory s fontem. Nakonec jsem zjistil (hledání, pročítání fór, Discord, ...), žě k těm je možno se dopracovat za pomocí nástroje `scummrp` s parametrem `-o`, který umí soubory `ATLANTIS.00X` "rozbalit". A stejně tak je umí s parametrem `-i` i "zabalit". Opět upravím `Makefile`, přidám dvě úlohy a jako cílovou složku volím `export`.

```
export-data:
	$(SCUMMTR_BIN_PATH)/scummrp -g atlantis -o -p $(INDY4_DATA_PATH) -d export

import-data:
	$(SCUMMTR_BIN_PATH)/scummrp -i -g atlantis -p $(INDY4_DATA_PATH) -d export
```

Ve složce `export` už pak konečně v adresáři `DISK_0001/LECF/LFLF_0068` nacházím soubory `CHAR_000X`. No a s těmi už umí pracovat program `scummfont`. S parametrem `o` umí herní fonty převést na bitmapu a s parametrem `i` zase převést bitmapu zpět na herní font. `Makefile` se opět rozrůstá. *Malý spoiler: je to už poslední rozšíření Makefile.*

```
export-fonts:
	$(SCUMMTR_BIN_PATH)/scummfont o export/DISK_0001/LECF/LFLF_0068/CHAR_0001 font-1.bmp
	$(SCUMMTR_BIN_PATH)/scummfont o export/DISK_0001/LECF/LFLF_0068/CHAR_0002 font-2.bmp
	$(SCUMMTR_BIN_PATH)/scummfont o export/DISK_0001/LECF/LFLF_0068/CHAR_0003 font-3.bmp
	$(SCUMMTR_BIN_PATH)/scummfont o export/DISK_0001/LECF/LFLF_0068/CHAR_0004 font-4.bmp
	$(SCUMMTR_BIN_PATH)/scummfont o export/DISK_0001/LECF/LFLF_0068/CHAR_0005 font-5.bmp

import-fonts:
	$(SCUMMTR_BIN_PATH)/scummfont i export/DISK_0001/LECF/LFLF_0068/CHAR_0001 font-1.bmp
	$(SCUMMTR_BIN_PATH)/scummfont i export/DISK_0001/LECF/LFLF_0068/CHAR_0002 font-2.bmp
	$(SCUMMTR_BIN_PATH)/scummfont i export/DISK_0001/LECF/LFLF_0068/CHAR_0003 font-3.bmp
	$(SCUMMTR_BIN_PATH)/scummfont i export/DISK_0001/LECF/LFLF_0068/CHAR_0004 font-4.bmp
	$(SCUMMTR_BIN_PATH)/scummfont i export/DISK_0001/LECF/LFLF_0068/CHAR_0005 font-5.bmp
```

Bitmapy už lze lehce vizuálně prozkoumat. Já používám [GIMP](https://www.gimp.org/), ale tady by opravdu postačilo i obyčejné [Malování](https://cs.wikipedia.org/wiki/Malov%C3%A1n%C3%AD_(Windows)) dostupné ve Windows. Font který hledám protože bych rád naučil `č` vypadá jako font v souboru `font-1.bmp`. Bitmapa obsahuje jen "dlouou nudli" znaků pod sebou. V tomto fontu má každý znak "chlívek" o velikosti 13x16px a některé "chlívky" jsou prázdné (tam asi hra hledá právě chybějící znaky a nevyrenderuje nic).

![ukázka fontu otevřeného v GIMPu jako bitmapa](04-font-showcase.jpg "ukázka fontu otevřeného v GIMPu jako bitmapa")

Abych si ověřil, že upravená bitmapa je správně převedená zpět na font, a úspěšně naimportována do hry, upravím například znak `S`.

![(1) původní znak S, (2) upravený znak S pro test](05-font-change.jpg "(1) původní znak S, (2) upravený znak S pro test")

No a správně použitý `Makefile` (`make import-fonts import-data import-text`) nám teď udělá všechno co potřebujeme. Důležité je pouštět `import-fonts` a až potom `import-data`, protože `import-fonts` uloží font zpět do rozbalených dat v herním formátu a `import-data` tyto data zabalí a zkopíruje zpět do složky se hrou.

![(1) upravený znak vykreslený hrou](06-font-imported.jpg "(1) upravený znak vykreslený hrou")

## Lokalizace fontu

V GIMPu (nebo klidně v Malování) stačí domalovat do patříčných "chlívků" české znaky a tím pokoříme výzvu. Kam ale přesně domalovat třeba to `č`?

*Poznámka autora: Tady jsem se hodně zapotil a párkrát to málem vzdal.*

První nápad byl zkusit to "[hrubou silou](https://en.wikipedia.org/wiki/Brute-force_attack)". Do každého prázdného "chlívku" v bitmapě namalovat něco unikátního (třeba symbol, nebo číslo), upravovat průběžně testovací text a zapisovat si který znak na jaké pozici v bitmapě se vykreslí. I jsem začal bitmapu takhle upravovat. Ale nechtělo se mi tento dost časově náročný proces opakovat třicetkrát. Naštěstí jsem si všiml, že v bitmapě jsou i německé znaky (například `ß`) a že tam jsou čísla v řadě za sebou, ale písmena jsou "náhodně". 

To mě přivedlo na stopu, že znaky nejsou v bitmapě náhodně, ale podle nějákého systému. Rychle jsou koukl do [ASCII tabulky](https://cs.wikipedia.org/wiki/ASCII#Tabulka_ASCII_k%C3%B3d%C5%AF) a i tam jsou čísla za sebou a písmena pak "náhodně". Porovnal jsem tedy pozici náhodného znaku v bitmapě a v ASCII tabulce. Například znak pro vykřičník (`!`) má v ASCII tabulce decimální hodnotu `33` a v bitmapě začíná znak vyříčníku na pozici `528` což je zároveň 33 * 16 (*16px je výška jednoho "chlívku" v bitmapě*). Zároveň jsem zjistil že bitmapa má na výšku 4096px, což odpovídá místu pro 256 "chlívků" po 16px a to je maximální počet znaků pro osmibitové znakové sady, které byly používané v DOSu. Našel jsem si teda znakovou sadu pro středoevropské jazyky používanou v systému DOS (CP852, také nazývanou OEM 852) a v ní našel pozici pro znak `č` (`9F` neboli `159`).

![znak č v znakové sadě CP852](07-c-cp852.jpg "znak č v znakové sadě CP852")

Lehce (159 * 16px = 2544px) jsem vypočítal pozici pro "chlívek" pro znak `č`, a protože byl prázdný, tak jsem tam `č` domaloval (*jen jsem zkopíroval `c` a přimaloval háček*).

![nový znak ve fontu "č"](08-new-c.jpg "nový znak ve fontu - č")

Soubor s texty jsem uložil v kódování OEM 852 (*proto ten Notepad++, nepodařilo se mi najít textový editor na Linuxu, který by uložil soubor správně*). Texty i font jsem naimportoval do hry (*<3 `Makefile`*) a hra správně zobrazila `č`! 

![nový znak ve fontu "č" ve hře](09-c-ingame.jpg "nový znak ve fontu - č - ve hře")

## Dokončení

Nyní už jen stačilo projet celou CP852 tabulku, vypočítat si pozici jednotlivých "chlívků" a domalovat do nich potřebné znaky. A to všechno dvakrát, protože je potřeba "počeštit" dva fonty. Jeden je použit pro uživatelské rozhraní (například několikrát zmíněný dialog pauzy) a druhý je použít pro dialogy osob ve hře. Font pro dialogy osob má jiný rozměr "chlívku" (39x10px), čili například č je na pozici 159 * 10px = 1590px.

Domalování jednotlivých znaků zabralo pár hodin, ale byla to v podstatě příjemná práce, protože už jsem věděl, že výzva je pokořena. Někdy to chtělo trochu důvtipu a vymslet jak přidat háček nebo čárku do omezeného prostoru. Konečný výsledek [můžete posoudit sami](https://prekladyher.eu/preklady/indiana-jones-and-the-fate-of-atlantis-1992.1341/updates#resource-update-3906).

![plně lokalizovaná hra](10-final.jpg "plně lokalizovaná hra s UI")
![plně lokalizovaná hra](11-final.jpg "plně lokalizovaná hra v cutscéně")

*Když tak vzpomínám,... tenhle podzim nebyl tak špatnej. Vždyť já mám ten podzim vlastně nakonec rád.*
