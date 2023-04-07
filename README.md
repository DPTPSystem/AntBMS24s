# AntBMS24s
Ant BMS 24s assembled
DPTP System 2023-04-07.
Projekt start: 2019-06-20.
Ant BMS and STM32F072

# Miért
Az ok amiért ez a szál megszülethetett egészen egyszerű. Egy elektromos kerékpáron használatos kijelző egyik nap nem adott életjelet, 
ennek a hiba vizsgálata kapcsán -és már persze előtte is érdekes volt- javításokba kezdtem és kiderült, hogy a kijelző meghajtó PCB-n használt
STM32-es kontroller megadta magát. Volt lábkompatibilis MCU a kéznél és megjavítottam a hardvert, de innentől kezdődőtt az egész, hiszen a szoftver
részét ismét meg kellett írnom. Ráadásúl teljesen vakon, vagy is csak abból, ami a kapcsolás visszafejtéséből kiderült. Innen indult el a BMS és a kijlező
hardvere közti adatok megfigyelése és visszafejtése, majd ennek a gyakorlati megvalósítása illetve felhasználása.

# Célok
A célok egyértelműek voltak. Készíteni egy olyan hardvert, amely az én igényeimnek megfelelő módon közli a BMS adatokat + egyéb álltalam hasznosnak vélt
adatokat. Pl.: hőmérsékleti adatok, sebesség, használatból eredendő adatok, mint legnagyobb teljesítmény, legnagyobb sebesség, ...stb. 
Továbbá kisérletezés, hogy mely kijelző típussal lehetne e legoptimálisabb olvashatóságot elérni legkevesebb érzékenység figyelembevételével. 3 féle verzió 
létezett, amelyből alább majd képeket is közlök.

# Hardver
Sokat nem akarok róla írni, a lényeg, hogy a BMS kommunikáció visszafejtése, hogy sikerült és milyen eredményeket értem el, azokat véleményem szerint minden 
Ant typusú BMS-nél fel lehet használni. Én egy 24 cellás verziót használtam, amely képes volt bluetooth-on keresztül beállításokat fogadni és információkat
közölni egy megfelelő appon keresztül. Pár kép a gyári BMS és kijelzőjéről:

Ez a gyári kombó:
![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/antbms.jpg "Ant BMS 24s")

Kijelző: (ez az egyik változat is egyben a kijelzők tekintetébe)
![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/antbms_lcd2.jpg "Ant BMS 24s")

Alkalmazás:
![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/antbms_app.jpg "Ant BMS 24s")
