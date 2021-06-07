# Basis
Schrijf de query die je het jaar geeft waarop het eerste lid toegetreden is.
```
SELECT min(jaartoe)
FROM spelers
```
---
Geef de lijst van spelers die in een straat met naam laan of weg wonen.
```
SELECT naam, jaartoe
FROM spelers
WHERE straat LIKE'%laan%' OR straat LIKE '%weg%'
```
---
Schrijf de query die het aantal teams in de tweede divisie toont.
```
select count(teamnr)
from teams
where divisie = 'tweede'
```
---
Schrijf de query die het aantal gespeelde wedstrijden telt.
```
SELECT count(wedstrijdnr)
FROM wedstrijden
```
---
Schrijf de query die het aantal rijen in boetes telt.
```
SELECT count(betalingsnr)
FROM boetes
```
---
Geef een lijst van spelersnummers die nu geen bestuurslid meer zijn, maar die het wel ooit geweest zijn. Toon per spelersnummer zijn functie en de eind datum.
Hint: EIND_DATUM NOT NULL
```
SELECT spelersnr, functie, eind_datum
FROM bestuursleden
WHERE eind_datum IS NOT NULL
```
---
Geef een lijst van wedstrijden die gespeeld zijn door team 1 en waarbij ofwel het aantal gewonnen of het aantal verloren sets gelijk is aan nul.
```
SELECT wedstrijdnr, teamnr
FROM wedstrijden
WHERE teamnr = 1 AND gewonnen = 0 OR verloren = 0
```
---
Maak een lijst met alle mannelijke spelers.
```
Select spelersnr, naam, jaartoe
from spelers
where geslacht = 'M'
```
---
Maak een lijst met alle teams waarvan speler 27 de aanvoerder is.
```
SELECT teamnr, spelersnr, divisie
FROM teams
WHERE spelersnr = 27
```
---
Maak een lijst met alle spelers die in Zoetermeer wonen
```
SELECT spelersnr, naam, jaartoe
FROM spelers
WHERE plaats = 'Zoetermeer'
```

# Basis 2 
Geef een overzicht van alle divisies waar wedstrijden voor gespeeld zijn met spelers die actief bestuurslid zijn en minstens één boete hebben.
Zorg dat elke divisie maar één keer voorkomt en sorteer de lijst alfabetisch.
```
SELECT DISTINCT divisie
FROM Wedstrijden, teams, boetes, bestuursleden
WHERE teams.teamnr = wedstrijden.teamnr AND wedstrijden.spelersnr = bestuursleden.spelersnr AND wedstrijden.spelersnr = boetes.spelersnr 
AND eind_datum IS NULL
```
---
Geef alle boetes groter dan 25 euro voor aanvoerders die meegespeeld hebben in een winnende wedstrijd met het team waarvan ze aanvoerder zijn.
Sorteer op datum van de boete (de tekstuele waarde, niet de echte datum).
``` 
SELECT DISTINCT boetes.spelersnr, betalingsnr, TO_CHAR(datum,'dd/mm/yyyy') as boetedatum
FROM boetes, teams, wedstrijden
WHERE boetes.spelersnr = teams.spelersnr AND teams.spelersnr = wedstrijden.spelersnr AND teams.teamnr = wedstrijden.teamnr AND bedrag > 25 AND gewonnen>verloren 
ORDER BY boetedatum
``` 
---
Maak een lijst van alle spelers die al een wedstrijd gespeeld hebben.
Alleen spelers met een 'e' (hoofdletter of kleine letter) in de naam moeten getoond worden. Zorg dat deze laatste voorwaarde met één WHERE-conditie gerealiseerd wordt.
Let ook op de juiste kolomhoofdingen.
Sorteer op spelersnr en zorg dat elke speler maar één keer voorkomt.
Gebruik geen concat, maar wel ||.
```
SELECT DISTINCT spelers.spelersnr, voorletters || ' ' || naam as spelersnaam
FROM spelers, wedstrijden
WHERE spelers.spelersnr = wedstrijden.spelersnr AND UPPER(spelers.naam) LIKE '%E%'
ORDER BY spelers.spelersnr
```
---
Geef een lijst met het spelersnummer, de naam van de speler, de datum van de boete van al de spelers die in 1980 een boete gekregen hebben en in Rijswijk wonen.

Zorg voor de juiste opmaak van de datum in het resultaat (niet YYYY-MM-DD maar DD/MM/YYYY) waarbij je de to_char functie gebruikt.

Sorteer op spelersnr.
```
SELECT spelers.spelersnr, voorletters, naam, TO_CHAR(datum, 'dd/mm/yyyy') AS boetedatum
FROM Boetes, spelers
WHERE EXTRACT (YEAR FROM datum) = 1980 AND spelers.spelersnr = boetes.spelersnr AND spelers.plaats = 'Rijswijk'
ORDER BY spelersnr
```
---
Maak een lijst van alle vrouwelijke aanvoerders van een team. Hierbij toon je voor deze spelers het spelersnummer en de volledige naam.
```
SELECT spelers.spelersnr, naam, voorletters
FROM spelers, teams
WHERE geslacht = 'V' AND spelers.spelersnr = teams.**spelersnr**
```
---
Maak een lijst van alle boetes met een boetebedrag boven 75 euro die betaald werden in 1980, 1981, 1982 of 1983.

Let op:
1) de titels van de kolommen moeten gebruikersvriendelijk zijn en geen afkortingen bevatten (spelersnr wordt speler, jaar van de boete verschijnt als jaar en het bedrag wordt weergegeven als bedrag).
2) Geef geen date als jaar terug, maar een int.
```
SELECT spelersnr AS speler, EXTRACT (YEAR FROM datum) AS jaar, bedrag
FROM boetes
WHERE bedrag>75 AND EXTRACT (YEAR FROM datum) BETWEEN 1980 AND 1983
```
---
Maak een lijst met de wedstrijden van speler nummer 6 waarbij je aangeeft of de wedstrijd gewonnen (toon: 'gewonnen') of verloren (toon: verloren) werd door de betreffende speler.
```
SELECT spelersnr, wedstrijdnr, gewonnen, verloren,
CASE WHEN gewonnen>verloren THEN 'gewonnen'
     WHEN verloren>gewonnen THEN 'verloren'
END AS resultaat
FROM wedstrijden
where spelersnr = 6
```
---
Maak een lijst van alle actieve bestuursleden.
Een actief bestuurslid is een bestuurslid met een lege eind_datum.
Sorteer op begin_datum oplopend.
```
SELECT spelersnr, functie
FROM bestuursleden
WHERE eind_datum is null
ORDER BY begin_datum
```
---
Maak een overzicht van alle wedstrijden van speler met nummer 6 waarbij je berekent met welk aantal sets verschil hij de wedstrijd gewonnen of verloren heeft.
```
SELECT spelersnr, gewonnen, verloren, gewonnen-verloren as verschil
FROM wedstrijden
WHERE spelersnr = 6
```
---
Maak een lijst van alle wedstrijden, die gespeeld werden voor het tweede team (teamnr: 2) en die gewonnen zijn door een speler van onze club (let op de benamingen van de kolomkoppen).
```
SELECT wedstrijdnr, spelersnr, gewonnen as gewonnen_sets, verloren as verloren_sets
FROM wedstrijden
WHERE teamnr = 2 AND gewonnen > verloren
			
```
# Basis extra
Geef voor alle bestuursleden een overzicht met hun naam, functie, de leeftijd die ze hadden op het moment van de start van hun functie en de leeftijd die ze hadden op het moment van het einde van hun functie.

Tip: gebruik de postgresqlfuncties AGE en EXTRACT om het aantal jaar te krijgen. En CAST(...) as CHAR(2).

(Dit is een moeilijke oefening)
```
SELECT s.spelersnr, naam, functie, CAST(AGE(begin_datum, geb_datum) AS CHAR(2)) || ' jaar' as leeftijd_begin,
CASE 
WHEN eind_datum IS NULL THEN 'nog bezig'
ELSE CAST(AGE(eind_datum,geb_datum) AS CHAR(2)) || ' jaar'
END AS leeftijd_einde
FROM Spelers s INNER JOIN Bestuursleden b
	ON s.spelersnr = b.spelersnr
```
---
Geef een overzicht van alle spelers met een bondsnummer.
Sorteer als volgt:
- Eerst moeten alle spelers uit zoetermeer getoond worden, deze sorteer je op naam. (spelers met dezelfde naam uit zoetermeer worden gesorteerd op bondsnr)
- Daarna de andere spelers, gesorteerd op bondsnr.

Tip: je kan CASE ook gebruiken in de ORDER BY component (zet dan '0')
```
select spelersnr, naam, plaats, bondsnr
from spelers s
where bondsnr is not null
order by
case when plaats = 'Zoetermeer' then 0 end,
case when plaats = 'Zoetermeer' then naam end,
bondsnr;
```
---
Geef alle bestuursleden die geboren zijn voor 1970-08-05 en hun staat van dienst.
Bekijk de output om de juiste formatering te zien.
gebruik een combinatie van CASE, de || functie en datum opmaak.
Sorteer op naam en begin datum
```
SELECT voorletters, naam, functie, CASE
	WHEN eind_datum IS NULL THEN 'actief: sinds ' || TO_CHAR(begin_datum,'DD/MM/YYYY')
	ELSE 'actief: van ' || TO_CHAR(begin_datum,'DD/MM') || ' tot ' || TO_CHAR(eind_datum,'DD/MM/YYYY')
	END AS periode
FROM bestuursleden b INNER JOIN spelers s
	ON b.spelersnr = s.spelersnr
WHERE geb_datum<'1970-08-05'
ORDER BY naam, begin_datum
```
---
Geef alle spelers die in januari begonnen zijn met een bestuursfunctie.
Sorteer op spelersnr en begindatum (de oorspronkelijke waarde).
Belangrijk: de grotere gegevenset heeft wél bestuursleden die niet in januari begonnen zijn.

Gebruik || ipv concat.
```
SELECT voorletters, naam, functie ||': ' || TO_CHAR(begin_datum,'DD/MM/YYYY') as bestuursfunctie
FROM Bestuursleden b INNER JOIN spelers s
ON b.spelersnr = s.spelersnr
WHERE EXTRACT(MONTH FROM begin_datum) = 01
ORDER BY s.spelersnr, begin_datum
```
---
Geef alle spelers die een wedstrijd met twee sets of meer verschil gewonnen of verloren hebben. Zorg voor de juiste output van de gegevens met onder andere vermelding van spelersnaam en eerste cijfer van het spelersnummer tussen haakjes. (gebruik hiervoor een CAST functie om het nummer om te zetten naar een CHAR(1))
Sorteer op spelersnr en daarna op wedstrijdnr.
Concateneer met ||.
```
SELECT naam || ' (' || CAST(s.spelersnr AS CHAR(1)) || ')'  as speler, wedstrijdnr
FROM wedstrijden inner join spelers s
	on (wedstrijden.spelersnr = s.spelersnr)
WHERE gewonnen-verloren >= 2 OR verloren-gewonnen >= 2
ORDER BY s.spelersnr, wedstrijdnr
```
---
Maak een lijst met alle spelers die in de jaren 60 geboren zijn.
Doe dit met maar één WHERE-conditie (hint: BETWEEN).
```
SELECT spelersnr, naam, geb_datum
FROM spelers
WHERE EXTRACT(YEAR FROM geb_datum) BETWEEN 1960 AND 1969
```
# Joins
Geef een lijst van alle spelers die nog geen wedstrijd gespeeld hebben.
Sorteer op spelersnr
```
SELECT spelersnr, naam
FROM spelers s FULL JOIN wedstrijden w USING (spelersnr)
WHERE wedstrijdnr IS NULL
ORDER BY spelersnr
```
---
Geef van elke speler wonend in Rijswijk het spelersnr, de naam, de lijst met boetebedragen (indien er zijn) en de lijst met teams waarvoor hij/zij een wedstrijd gespeeld heeft (indien hij er gespeeld heeft). Geef het resultaat volgens oplopend spelersnr, teamnr en bedrag
```
SELECT spelersnr, naam, bedrag, teamnr
FROM spelers s LEFT JOIN Boetes b USING (spelersnr) LEFT JOIN Wedstrijden w USING (spelersnr)
WHERE plaats = 'Rijswijk'
ORDER BY spelersnr asc , teamnr asc , bedrag asc
```
---
Geef een aflopend gesorteerde lijst van de nummers van alle spelers waarvoor nog geen boete werd betaald en die nog nooit in het bestuur van de tennisvereniging hebben gezeten.
```
SELECT s.spelersnr
FROM spelers s FULL JOIN boetes bo USING (spelersnr) FULL JOIN Bestuursleden be USING (spelersnr)
WHERE betalingsnr IS NULL AND functie is null
ORDER BY s.Spelersnr desc
```
---
>Geef voor alle huidige bestuurleden hun functie en de lijst van boetes die voor hen werd betaald.
Omdat je dit wil vergelijken met de boetebedragen die betaald werden voor spelers die niet in het bestuur zitten, wil je deze boetebedragen ook opnemen in de tweede kolom van je resultaat. Sorteer je antwoord eerst op functie en daarna op het boetebedrag.
```
Niet gevonden
```
---
Geef een alfabetisch gesorteerde lijst van de namen van alle leden van de tennisvereniging die ofwel een recreatiespeler zijn (speelt geen wedstrijden) ofwel een wedstrijdspeler die nog geen wedstrijden gespeeld heeft.
```
SELECT naam
FROM Spelers s FULL JOIN Wedstrijden w ON s.spelersnr = w.spelersnr
WHERE wedstrijdnr IS NULL
ORDER BY naam
```
---
Geef voor elke mannelijke speler wiens naam minstens 2 keer de letter 'e' bevat een lijst van de functies die hij op dit moment uitoefent.
Ook mannelijke spelers zonder huidige functie moeten getoond worden.
Sorteer op spelersnr.
```
SELECT s.naam, s.geslacht, bs.functie
FROM spelers AS s
LEFT OUTER JOIN bestuursleden AS bs ON bs.spelersnr = s.spelersnr AND bs.eind_datum IS NULL
WHERE s.naam ilike '%e%e%' AND s.geslacht = 'M'
ORDER BY s.spelersnr
```
---
Geef voor elke wedstrijd het wedstrijdnummer en de volledige naam van de aanvoerder van het team waarvoor de wedstrijd werd gespeeld. Sorteer je resultaat volgens het wedstrijdnummer in oplopende volgorde.
```
SELECT wedstrijdnr, naam, voorletters
FROM wedstrijden w INNER JOIN teams t ON t.teamnr = w.teamnr INNER JOIN spelers s ON s.spelersnr = t.spelersnr
ORDER BY wedstrijdnr
```
---
Geef voor alle vrouwelijke spelers die in Den Haag, Zoetermeer of Leiden wonen
het spelersnummer, hun woonplaats en een lijst van de teams waarvoor ze ooit gespeeld hebben.
Sorteer op spelersnr
```
SELECT s.spelersnr, plaats, teamnr  
FROM spelers s FULL OUTER join wedstrijden w
	ON s.spelersnr = w.spelersnr
WHERE geslacht = 'V' AND (plaats = 'Den Haag' OR plaats = 'Zoetermeer' OR plaats = 'Leiden')
ORDER BY s.spelersnr
```
---
Maak een lijst van alle mannelijke aanvoerders van een team. Geef voor die aanvoerders de wedstrijden waarin ze gespeeld hebben.
Geef ook aanvoerders die geen wedstrijd gespeeld hebben.
Hierbij toon je voor deze spelers het spelersnummer en de volledige naam, voor het team de divisie en voor de wedstrijd het wedstrijdnummer.
Sorteer aflopend op het wedstrijdnummer.
```
SELECT s.spelersnr, naam, voorletters, divisie, wedstrijdnr
FROM wedstrijden w INNER JOIN teams t 
	ON w.spelersnr = t.spelersnr
	inner join spelers s
	ON s.spelersnr = t.spelersnr
WHERE geslacht = 'M'
ORDER BY wedstrijdnr desc
```
---
Geef een lijst met het spelersnummer, de naam van de speler, de datum van de boete en het bedrag van de boete van al de spelers die een boete gekregen hebben met een bedrag groter dan 45,50 euro en in Rijswijk wonen.
Sorteer op spelersnr en het volgnummer van de boete.
Gebruik expliciete joins.
```
SELECT s.spelersnr, naam, datum, bedrag
FROM Boetes b INNER JOIN spelers s
	ON b.spelersnr = s.spelersnr
WHERE bedrag > 45.50 AND plaats = 'Rijswijk'
```
# Joins 2
Geef alle spelers die geen boete gekregen hebben en niet in Den Haag wonen.
Sorteer op jaar van toetreden.
```
SELECT s.spelersnr, naam, plaats 
FROM boetes b RIGHT OUTER JOIN spelers s 
	ON s.spelersnr = b.spelersnr
WHERE plaats!='Den Haag' AND bedrag IS NULL
ORDER BY jaartoe
```
---
>Geef per team de verloren wedstrijden. Zorg dat teams zonder verloren wedstrijden ook in de output verschijnen.
Duid per wedstrijd aan of het om een actief bestuurslid gaat.
Sorteer op divisie en wedstrijdnummer.
```
Niet gevonden
```
---
Geef een lijst met spelers (naam, voorletters en geboortedatum) die nog geen boete gekregen hebben met een bedrag hoger dan 75 euro.
Sorteer op spelersnr.
```
SELECT naam, voorletters, TO_CHAR(geb_datum,'DD/MM/YYYY') as geboortedatum
FROM spelers LEFT JOIN boetes USING(spelersnr)
WHERE bedrag < 75 OR bedrag IS NULL
GROUP BY spelersnr, naam, voorletters, geb_datum
ORDER BY spelersnr
```
---
Geef een lijst van alle huidige bestuursleden die nog geen boete gekregen hebben, maar wel al minstens één wedstrijd verloren hebben.
Sorteer op spelersnr en wedstrijdnr.
```
SELECT b.spelersnr, s.naam, functie, begin_datum, wedstrijdnr
FROM bestuursleden b LEFT OUTER JOIN boetes bo 
	ON b.spelersnr = bo.spelersnr 
	LEFT OUTER JOIN wedstrijden w
	ON w.spelersnr = b.spelersnr
	LEFT OUTER JOIN spelers s
	ON s.spelersnr = w.spelersnr
WHERE bedrag IS NULL
AND verloren > gewonnen
AND eind_datum IS NULL
ORDER BY spelersnr, wedstrijdnr
```
---
Geef een lijst van alle mogelijke dubbelcombinaties tussen spelers onderling.
Sorteer op "eerste speler" en vervolgens op "tweede speler".
```
SELECT s1.voorletters || ' ' || s1.naam as "eerste speler", s2.voorletters || ' ' || s2.naam as "tweede speler"
FROM spelers s1 INNER JOIN spelers s2
	ON s1.spelersnr != s2.spelersnr
ORDER BY "eerste speler", "tweede speler"
```
# Basis AgMT
Geef de namen van alle spelers waarin de letter e tweemaal voorkomt, maar geen 3 keer. Geef van elke speler ook zijn leeftijd in jaren. Sorteer op geboortedatum zodat de oudste speler eerst wordt getoond. TIP: gebruik AGE().
```
SELECT naam, EXTRACT(YEAR FROM AGE(geb_datum)) as leeftijd
FROM SPELERS
WHERE UPPER(naam) LIKE('%E%E%') AND UPPER(naam) NOT LIKE('%E%E%E%')
ORDER BY geb_datum
```
---
Selecteer de namen van voorzitters en de periode (= begindatum streepje einddatum) waarin ze voorzitter waren (of nog steeds zijn) en op welke datum ze beboet zijn geweest.
Gebruik ||.
```
SELECT naam, CASE
WHEN eind_datum IS NOT NULL THEN to_char(begin_datum,'DD/MM/YYYY') || ' - ' || to_char(eind_datum,'DD/MM/YYYY')
ELSE to_char(begin_datum,'DD/MM/YYYY') || ' -'
END as periode, datum
FROM bestuursleden b INNER JOIN boetes bo USING (spelersnr) 
	INNER JOIN spelers USING(spelersnr)
WHERE functie = 'Voorzitter'
```
---
Geef per speler per wedstrijd dat hij gespeeld heeft, of hij gewonnen heeft met zijn team. Sorteer enkel op de naam van de speler (en niet op wedstrijdnummer).
Gebruik geen join in de from, maar gebruik een where conditie.
```
SELECT naam, wedstrijdnr, CASE
WHEN verloren>gewonnen THEN 'verloren'
ELSE 'gewonnen'
END as uitslag
FROM wedstrijden w , spelers s
WHERE w.spelersnr = s.spelersnr 
ORDER BY naam
```
---
Tel het aantal mensen die momenteel geen specifieke bestuursfunctie hebben (dus zij die momenteel enkel lid zijn).
```
select count(spelersnr) as count
from bestuursleden
where functie = 'Lid'
and eind_datum is null
```
---
Selecteer de naam van de spelers die Voorzitter geweest zijn, en de periode in 1 kolom. Sorteer van meest recent naar minst recent.
```
SELECT naam as voorzitter, begin_datum || ' ' || eind_datum as periode
FROM bestuursleden inner join spelers using(spelersnr)
WHERE functie = 'Voorzitter' 
AND eind_datum IS NOT NULL
ORDER BY begin_datum
```
---
Zoek de gemiddelde leeftijd in jaren in 2014 onafhankelijk van in welke maand de speler geboren is(geen kommagetal toegelaten, rond af naar boven)
```
SELECT ceil(avg(2014 - (EXTRACT (year FROM geb_datum)))) as "gemiddelde leeftijd"
FROM spelers
```
---
Zoek het gemiddelde toetredingsjaar (geen kommagetal toegelaten, rond af naar beneden). Merk op dat het type van je resultaat numeric moet zijn (en niet decimal bv.).
```
select floor(avg(jaartoe)) as "gemiddelde toetredingsjaar"
from spelers
```
---
>Selecteer de namen en geboortejaren van de leden. Geef de volledige naam weer in 1 kolom zodat je eerst de voorletters hebt, vervolgens de voorvoegsels en tot slot de naam.
```
Niet gevonden
```
---
Tel hoeveel verschillende plaatsen er zijn.
```
select count( distinct plaats)
from spelers
```
---
Geef alle persoonnamen met een naam die meer dan 8 tekens telt
```
select naam AS persoonnamen
from spelers
where length(naam)>8
```
---
Selecteer de straatnamen die eindigen op weg.
```
SELECT straat
FROM spelers
WHERE straat LIKE ('%weg')
```
---
Geef de totale som van het aantal betaalde boetes en daarnaast ook het totale aantal boetes.
```
select sum(bedrag) as "som der boetes", count(bedrag) as "aantal boetes"
from boetes
```
---
We willen een vereenvoudigd, geindexeerd overzicht van alles boetes.
Boetes kleiner dan 25 worden weergegeven als "verwaarloosbaar" (tip: gebruik CASE), alle andere boetes moeten met 10% verhoogd worden. Geef voor elke boete ook de spelersnummer weer.
Merk op dat je in 1 kolom bedragen als Strings moet voorstellen, gebruik hiervoor to_char, met een formatering van 3 getallen.

Je hoeft niet te sorteren.
```
SELECT spelersnr, CASE
WHEN bedrag < 25 THEN 'verwaarloosbaar'
ELSE TO_CHAR(round(bedrag*1.10),'999')
END AS bedrag
FROM boetes
```
---
We willen van alle boetes de volgende feiten weten: gemiddelde, kleinste boete en grootste boete. Elk afgerond op 2 cijfers na de komma.
```
SELECT ROUND(AVG(bedrag),2) AS "gemiddelde boete", MIN(bedrag) AS "kleinste boete", MAX(bedrag) AS "grootste boete"
FROM boetes
```
---
Maak een lijst met alle spelers die niet in Zoetermeer wonen en die na 1980 zijn toegetreden tot de club.
Zorg dat de jongste speler bovenaan staat
```
SELECT spelersnr, naam, voorletters
FROM Spelers
WHERE plaats != 'Zoetermeer' AND jaartoe>1980
ORDER BY geb_datum DESC
```
# Aggr 1
Geef de langste bestuurstermijn in dagen voor bestuursleden die een wedstrijd gewonnen hebben.
```
SELECT max(eind_datum-begin_datum) as bestuurstermijn
FROM bestuursleden b INNER JOIN wedstrijden w USING(spelersnr)
WHERE verloren<gewonnen
```
---
Geef een lijst met voor elke speler met een boete, het totaal bedrag aan boetes. Als het aantal boetes echter groter is dan 2, zet je 'veel boetes'. Bij de andere spelers zet je een -.
Sorteer op aantal boetes aflopend en daarna op totaal boetebedrag aflopend.
```
SELECT spelersnr, sum(bedrag) as totaalboetebedrag, CASE
WHEN count(bedrag)>2 THEN 'veel boetes'
ELSE '-'
END as status
FROM boetes b
GROUP BY spelersnr
order by count(bedrag) desc , totaalboetebedrag desc
```
---
Geef het grootste boetebedrag voor een speler die momenteel bestuurslid is.
Gebruik geen limit.
```
SELECT max(bedrag)
FROM boetes bo INNER JOIN bestuursleden b using(spelersnr)
WHERE eind_datum IS NULL
```
---
Geef het gemiddeld aantal verloren sets door vrouwelijke spelers.
```
SELECT avg(verloren) as "avg"
FROM spelers s INNER JOIN wedstrijden w USING(spelersnr)
WHERE geslacht='V'
```
# Aggr 2
Geef spelers die in het jaar dat ze lid geworden zijn van de club reeds een boete van meer dan 50 euro gekregen hebben en de som van al deze boetes groter of gelijk is aan 100 euro.
Geef buiten de voorletters en de naam van de speler ook het aantal boetes die aan bovenstaande voorwaarden voldoen.
Sorteer op spelersnr.
```
SELECT voorletters, naam, count(bedrag) as aantalboetes
FROM boetes b INNER JOIN spelers s USING (spelersnr)
WHERE EXTRACT(year from datum) = jaartoe AND bedrag > 50
GROUP BY spelersnr, voorletters, naam 
HAVING sum(bedrag)>=100
ORDER BY spelersnr
```
---
Maak een lijst met per spelersnummer het aantal wedstrijden dat een speler verloren heeft op voorwaarde dat deze speler meer dan één wedstrijd verloren heeft. Bijkomend moet de lijst gesorteerd worden op basis van het spelersnummer.
```
SELECT spelersnr, count(verloren) as aantal
FROM wedstrijden
WHERE verloren>gewonnen
GROUP BY spelersnr
HAVING COUNT(verloren)>1
ORDER BY spelersnr
```
---
Geef de teams (het nummer van het team en de divisie) waarvoor meer dan vier verschillende spelers gespeeld hebben.
```
SELECT teamnr, divisie
FROM wedstrijden w INNER JOIN teams te using(teamnr)
GROUP BY teamnr, divisie
HAVING count(DISTINCT w.spelersnr)>4
```
---
Geef het aantal spelers met een bondsnummer die een boete gehad hebben.

```
SELECT count( DISTINCT spelersnr) as aantal
FROM spelers s INNER JOIN boetes b USING(spelersnr)
WHERE bondsnr IS NOT NULL
```
# Basis ruimte
Geef de gemiddelde afstanden tot hun planeet van alle satellieten van Saturnus, afgerond op 2 getallen na de komma
```
SELECT  round(AVG(afstand),2) AS "gemiddelde afstand"
FROM hemelobjecten
WHERE satellietvan = 'Saturnus'
```
---
Geef de totale som van alle afstanden tot hun planeet van alle satellieten van Saturnus

```
SELECT SUM(afstand) AS "totale afstand"
FROM hemelobjecten
WHERE satellietvan = 'Saturnus'
```
---
Geef het aantal unieke satellieten van planeten binnen ons sterrenstelsel
```
SELECT  COUNT(DISTINCT objectnaam) AS aantal_satellieten
FROM hemelobjecten
WHERE satellietvan != 'Zon'
```
---
Geef het aantal satellieten van Jupiter
```
SELECT COUNT(objectnaam) AS count
FROM hemelobjecten
WHERE satellietvan = 'Jupiter'
```
---
Geef van alle reizen die langer dan 400 dagen duurden, een lijst van deelnemers. Output is reisnr, klantnr. Sorteer eerst op reisduur en dan op klantnr
```
SELECT reisnr, klantnr
FROM reizen INNER JOIN deelnames USING(reisnr)
INNER JOIN klanten USING(klantnr)
WHERE reisduur > 400
ORDER BY reisduur, klantnr
```
---
Geef het bedrag van de duurste reis als "duurste"
```
SELECT max(prijs) AS duurste
FROM reizen
```
---
Geef voor klant met klantnr 121 het aantal reizen dat hij heeft gemaakt.
```
SELECT count(reisnr) AS aantal_reizen
FROM deelnames INNER JOIN reizen USING(reisnr)
WHERE klantnr = 121
```
---
Geef het geboortejaar van de jongste klant
```
SELECT  MAX(EXTRACT(YEAR FROM geboortedatum)) AS date_part
FROM klanten
```
---
Geef alle hemelobjecten weer met een diameter groter dan 50 en kleiner dan 10000.
Sorteer eerst aflopend op diameter en dan alfabetisch op naam
```
SELECT objectnaam, diameter
FROM hemelobjecten
WHERE diameter BETWEEN 50 AND 10000
ORDER BY diameter DESC, objectnaam
```
---
Geef voor alle effectieve ruimtebezoeken het bezochte hemelobject weer en de verblijfsduur. Sorteer aflopend op verblijfsduur
```
SELECT objectnaam, verblijfsduur
FROM bezoeken 
WHERE verblijfsduur != 0
ORDER BY verblijfsduur desc
```
# Ruimte 1
Welke reizigers hebben al meer dan 1 reis ondernomen waarvoor ze meer dan 2,5 miljoen euro moesten betalen?
Sorteer op naam
```
SELECT naam, count(reisnr) as aantal_reizen
FROM reizen INNER JOIN deelnames USING(reisnr)
INNER JOIN klanten USING(klantnr)
WHERE prijs > 2.50
GROUP BY naam
HAVING count(reisnr) > 1
ORDER BY naam
```
---
Bereken voor de klant wiens naam begint met G en eindigt met s hoeveel hij in totaal al besteed heeft aan reizen.
```
SELECT naam, SUM(prijs)
FROM klanten INNER JOIN deelnames USING(klantnr) INNER JOIN reizen USING(reisnr)
WHERE naam LIKE('G%s') 
GROUP BY naam
```
---
Maak een lijst met een overzicht van de reizen en het aantal deelnemers van elke reis. Orden de lijst dalend op basis van het aantal deelnemers, als er eenzelfde aantal deelnemers is, moeten deze stijgend geordend worden volgens reisnummer.
```
select reisnr, count(reisnr) as deelnemers
from reizen
inner join deelnames using(reisnr)
group by reisnr
order by deelnemers desc, reisnr
```
---
>Welke reizen hebben exact drie verschillende hemelobjecten als reisdoel?
Sorteer op reisnr.
```
Niet gevonden
```
---
Maak een lijst met een overzicht van de reizen en het aantal deelnemers van elke reis. Orden de lijst dalend op basis van het aantal deelnemers, als er eenzelfde aantal deelnemers is, moeten deze stijgend geordend worden volgens reisnummer.
Zorg dat reizen zonder deelnames ook in het resultaat staan.
```
select reisnr, count(klantnr) as deelnemers
from reizen left join deelnames using(reisnr)
group by reisnr
order by deelnemers desc, reisnr
```
---
Op welke planeten verblijft men gemiddeld langer dan 2 dagen?
Sorteer op objectnaam.
```
SELECT h.objectnaam, AVG(b.verblijfsduur)
FROM bezoeken AS b INNER JOIN hemelobjecten AS h USING (objectnaam)
WHERE h.satellietvan = 'Zon'
GROUP BY h.objectnaam
HAVING AVG(b.verblijfsduur) > 2
ORDER BY h.objectnaam
```
---
Welke planeten met meer dan 7 manen worden bezocht (met of zonder verblijf)?
Sorteer aflopend op basis van het aantal manen.
Let erop dat je planeten die meerdere keren bezocht worden, niet dubbel telt.
```
SELECT p.objectnaam
FROM hemelobjecten AS p
INNER JOIN hemelobjecten AS m ON m.satellietvan = p.objectnaam
INNER JOIN bezoeken AS b ON b.objectnaam = p.objectnaam
INNER JOIN hemelobjecten AS zon ON zon.objectnaam = p.satellietvan AND zon.satellietvan IS NULL
GROUP BY p.objectnaam
HAVING COUNT(distinct m.objectnaam) > 7
ORDER BY COUNT(distinct m.objectnaam) DESC
```
---
Bereken voor alle hemelobjecten die satellieten hebben, het aantal satellieten per hemelobject.
De lijst moet dalend gesorteerd worden op basis van het aantal satellieten van de hemelobjecten en daarna alfabetisch op basis van objectnaam.
```
select satellietvan as objectnaam , count(satellietvan)
from hemelobjecten
group by satellietvan
having satellietvan is not null
order by count desc, objectnaam
```
# Ruimte 2
Geef de planeten en het aantal verschillende personen die hen bezocht hebben (met of zonder verblijf).
Sorteer op objectnaam.
```
SELECT objectnaam, count(klantnr) as aantalbezoekers
FROM hemelobjecten h LEFT JOIN bezoeken b USING(objectnaam)
LEFT JOIN reizen USING(reisnr)
LEFT JOIN deelnames USING(reisnr)
WHERE satellietvan = 'Zon'
GROUP BY objectnaam
ORDER BY objectnaam
```
---
Sorteer de klanten aflopend op gemiddelde kostprijs per bezoek (totaalprijs van alle reizen per klant/totaal aantal dagen verblijf op een hemelobject), op twee cijfers na de komma afgerond, daarna op klantnr.
```
select klantnr,
	round(sum(prijs)/sum(verblijfsduur),2) as gemiddeldeprijs
from deelnames inner join reizen using(reisnr) inner join bezoeken using(reisnr) 
group by klantnr
order by gemiddeldeprijs desc,
	klantnr
```
---
Geef per klant het totaal aantal dagen dat deze klant in totaal op een planeet zal verblijven.
Sorteer op klantnr.
```
SELECT klantnr, SUM(verblijfsduur) AS totaleverblijfsduur
FROM deelnames INNER JOIN reizen USING(reisnr) INNER JOIN bezoeken USING(reisnr) INNER JOIN hemelobjecten USING(objectnaam)
WHERE satellietvan = 'Zon'
GROUP BY klantnr
ORDER BY klantnr
```
---
Geef alle planeten (gesorteerd op afstand van de zon) en het aantal verschillende reizen die deze planeet bezocht hebben.
```
SELECT objectnaam, count(reisnr) as aantalbezoeken
FROM hemelobjecten h LEFT JOIN bezoeken USING(objectnaam)
WHERE satellietvan = 'Zon'
GROUP BY objectnaam, afstand
ORDER BY afstand
```
---
Geef de reizen met meer dan drie klanten.
Sorteer op reisnr.
```
select reisnr
from reizen inner join deelnames using(reisnr)
group by reisnr
having count(klantnr) > 3;
```
# Ruimte 3
>Vind per planeet die zijn maan die het verst ervan verwijderd is. Geef planeet, maan en afstand en sorteer stijgend op afstand.
```
Niet gevonden
```
---
Reken per planeet uit wat de gemiddelde afstand van zijn manen is.
```
select satellietvan as planeet, avg(afstand) as afstand
from hemelobjecten
where satellietvan != 'Zon'
group by satellietvan
order by afstand
```
---
Zoek de hemelobjecten op die meer dan eens in een zelfde ruimtereis bezocht worden. Geef reisnr, objectnaam en aantal bezoeken. Sorteer op reisnr.
```
SELECT reisnr, objectnaam, count(objectnaam)
FROM bezoeken b
GROUP BY objectnaam, reisnr
HAVING count(objectnaam) >1
ORDER BY reisnr
```
---
Tel per reis het aantal bezochte hemelobjecten. Sorteer op reisnr.
```
SELECT reisnr, COUNT(objectnaam)
FROM bezoeken
GROUP BY reisnr
ORDER BY reisnr
```
---
