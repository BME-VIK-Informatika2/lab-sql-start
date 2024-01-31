# SQL labor

## Felkészülés
A laborra felkészüléshez olvasd el a segédletet és próbálj meg válaszolni az ellenőrző kérdésekre.

## A labor teljesítése
Ebbe a könyvtárba készítsd el az **SQL labor** megoldásait. A labor teljesítéséhez a jelenléten felül az 1-3 feladatok elkészítésére van szükség. A gyakorlat két közösen megoldott feladattal indul, a további feladatot önállóan kell megoldani.

> [!IMPORTANT]
> A megoldásokat pull request formájában kell beadni a határidő előtt a Moodle alatt található **Git tudnivalók** leírásban található utasítások alapján.
> - Hozz létre egy **új branchet** `megoldas` néven, és ezen dolgozz.
> - Töltsd ki a `neptun.txt` fájlt a saját Neptun kódoddal.
> - Minden feladat után kommitolj, és használj értelmes kommit üzeneteket.
> - A feladat végeztével vagy a labor végén **pushold** a megoldásodat és hozz létre egy **pull requestet**.
> - Ellenőrizd a pull request tartalmát és rendeld hozzá a laborvezetődet **reviewernek**.

> [!CAUTION]
> A nem ilyen formában megadott megoldások nem lesznek értékelve!

## Előkészítés
A labor során szükségünk lesz egy adatbázis szerverre, ami tárolja az adatokat. Ehhez használhatjuk a már előző órán megismert XAMPP szoftvert.
- Nyissuk meg az XAMPP programot és indítsuk el a MySQL szervert.

> [!NOTE]
> Laborgépeken az XAMPP szoftvert a `C:/Tools/xampp/xampp-control.exe` állománnyal tudjuk elindítani.

A feladatokat a MySQL Workbench program segítségével fogjuk megoldani:

> [!CAUTION]
> Mindig kövessétek a laborvezető utasításait a szoftver használatával kapcsolatban, különben könnyen adatvesztés történhet!

- A főoldalon a MySQL Connections alatt vegyél fel egy új kapcsolatot a `+` segítségével (ha még nincs).
    - A `Connection Name` legyen `localhost`, minden más maradhat alapértelmezett, és menjünk az `OK` gombra.
    - A létrejövő kapcsolatot nyissuk meg dupla kattintással.
- Nyisd meg és futasd le a `konyvtar.sql` scriptet, ezáltal egy egyszerű könyvtári mintaadatbázis jött létre az adatbázisában.
- Válasszuk ki alapértelmezettnek a `konyvtar` adatbázist.
- Generáljuk le az adatbázis modeljét a `Database` > `Reverse Engineer` menüpont segítségével.
- Tekintsd át táblaszerkezetet, valamint a táblákban található adatokat.

## 1. Tranzakciók vizsgálata

Ezeket a feladatokat közösen végezzétek el, amennyiben járulékos magyarázat szükséges, azt a gyakorlatvezető ismerteti. MySQL Workbench segítségével nyiss két kapcsolatot. Ez a két kapcsolat fogja demonstrálni a két párhuzamos felhasználói adatmanipulációt.

**Elveszett módosítás vizsgálata** : Két párhuzamos tranzakció módosítja az 1-es felhasználó által kivihető darabszámot. Az egyik csökkenti hárommal, a másik növeli eggyel. Hajtsd végre az alábbi ütemezést! Mit tapasztalsz és miért?

| **Felhasználó 1** | **Felhasználó 2** |
| --- | --- |
| `start transaction;` | |
| --- | --- |
| | `start transaction;` |
| `select * from tag where id=1;` | |
| | `select * from tag where id=1;` |
| `update tag set MaxKolcsonzes=2 where id=1;` | |
| | `update tag set MaxKolcsonzes=6 where id=1;` |
| `commit;` | |
| | `commit;` |
| `select * from tag where id=1;` | |

Adjunk megoldást az előző problémára! Megoldás lehet, hogy a rekordokat már kiolvasáskor zárolni kell, hogy más ne férhessen hozzá! Erre mutat példát az alábbi ütemezés.

| **Felhasználó 1** | **Felhasználó 2** |
| --- | --- |
| `start transaction;` | |
| --- | --- |
| | `start transaction;` |
| `select * from tag where id=2 for update;` | |
| | `select * from tag where id=2 for update;` |
| `update tag set MaxKolcsonzes=2 where id=2;` | **Várakozik** |
| `commit;` | **Várakozik** |
| | `update tag set MaxKolcsonzes=3 where id=2;` |
| | `commit;` |
| `select * from tag where id=1;` | |

## 2. Lekérdezések készítése

Hozd létre az alábbi lekérdezéseket a `query-1.sql` fájlba.

1. Listázzuk ki a könyvtár tagjait!
2. Listázd ki, hogy az egyes tagok eddig milyen könyveket kölcsönöztek ki!
3. Listázd ki, hogy az egyes tagok milyen szerzők könyveit kölcsönözték ki!
4. Listázd ki, hogy az egyes tagok hányszor kölcsönöztek eddig!
5. Listázd ki azokat a tagokat, akik eddig legalább 3-szor kölcsönöztek!
6. Listázd ki azokat a tagokat, akik eddig legalább 3-szor kölcsönöztek és érvényes tagságival rendelkeznek!
7. Listázd ki, hogy az egyes tagok mikor kölcsönöztek utoljára!
8. Listázd ki hogy az egyes szerzőknek összesen hány kölcsönzésük van!
9. Listázd ki azon tagok adatait akiknek a tagsága lejárt!
10. Listázd ki azon tagokat akiknek már lejárt a kölcsönzése, de még nem hozták vissza a kikölcsönzött könyvet!
11. Listázd ki azokat a szerzőket, akiknek még egyetlen könyvét sem kölcsönözték ki, de van könyvük a könyvtárban!
12. Kik írták a legdrágább könyvet?
13. Listázd ki azokat a könyveket, melyek átlagban legalább 30 napig voltak kikölcsönözve!
    a. Az átlagszámítás során csak a visszahozott kölcsönzéseket vedd figyelembe!
    b. Az átlagszámítás során a vissza nem hozott könyveket az aktuális dátummal vedd figyelembe!
14. Listázd ki azokat a tagokat akiknek a tagsága 100 napon belül lejár és van náluk kikölcsönzött könyv!
15. Listázd ki a kikölcsönözhető könyveket (azaz épp bent vannak a könyvtárban)!

### :bookmark_tabs: Beadandó

- [ ] Mentsd el a `query-1.sql` fájlt a repository gyökerébe.
- [ ] Kommitold a változtatásokat.

## 3. További lekérdezések készítése

Hozd létre az alábbi lekérdezéseket a `query-2.sql` fájlba.

1. Listázd ki azokat könyveket, amelyeket még nem kölcsönöztek ki soha!
2. Melyik a legrégebbi könyv?
3. Összesen hány napig volt kikölcsönözve a legrégebbi könyv?
4. Melyik tag a leglustább, azaz ki az, aki a legtöbbet késett egy könyv visszahozatalával?
5. Listázd ki azokat a könyveket, amelyek összesen legalább 40 napig ki voltak kölcsönözve!
6. Listázd ki, hogy az egyes könyveknek hány szerzője van!
7. Listázd ki a könyvek szerzőinek a számát a nemzetiségük szerint!

### :bookmark_tabs: Beadandó

- [ ] Mentsd el a `query-2.sql` fájlt a repository gyökerébe.
- [ ] Kommitold a változtatásokat.