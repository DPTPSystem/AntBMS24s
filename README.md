# AntBMS24s
* Ant BMS 24s assembled
* DPTP System 2023-04-07.
* Projekt start: 2019-06-20.
* Ant BMS 24s and STM32F072 and GLCD or E-ink

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
![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/antbms_lcd2.PNG "Ant BMS 24s")

Alkalmazás:
![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/antbms_app.PNG "Ant BMS 24s")

GLCD változat:
![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/bms_graph_1.jpg "Ant BMS 24s")

![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/bms_graph_2.jpg "Ant BMS 24s")

E-ink változat fejlesztés közben:
![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/epaper_1.jpg "Ant BMS 24s")

# Adatok kinyerése
Az első lépés az volt, hogy megfigyeljem a kommunikációt, ehhez egyszerűen a BMS UART vezetékeire rácsatlakoztam egy digitális analizátort
és megfigyeltem, elmentettem az ott zajló kommunikációt. Ezeket az adatokat a "digital_signal_log" mappában találjátok.

Hallgatozás közben:
![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/bms_log.jpg "Ant BMS 24s")

Adatok látszanak:
![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/bms_listening.jpg "Ant BMS 24s")

# Hardver javítása és programozása
STM32 MCU leszedve:
![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/bms_lcd.jpg "Ant BMS 24s")

Már a programozás látható:
![Ant BMS 24s](https://github.com/DPTPSystem/AntBMS24s/blob/master/images/bms_lcd_2.jpg "Ant BMS 24s")

# Érdemi munka
Az adatok megfigyelése és lementése a munka mondhatni szerencsésebb és gyorsabb része. Az érdemi munka ezen adatok értelemszerá feldolgozása, úgy
hogy az áttekinthető legyen és majd ez után jöhet a visszafejtés, hogy még is melyik adat mit is jelet. Ez innetnől érdekes is, mert egy-egy
adatról meg kell fejteni, hogy az milyen hosszú is valójában és hogy miképpen milyen tipusban kell értelmezni. Pl. egy dátumnál lehetséges,
hogy egy datastamp vagy is időbélyeget használ a rendszer, de az is lehet, hogy szimplán hexa vagy decimálisan adja meg. Vagy pl. a feszültség
felbontása mekkora lehet, 2 vagy 4 byte? Szóval ez mind sok idő és energia, mire minden a helyére kerül. Sok munka és még több energiával a 
következőkre jutottam egyelőre csak leíárs szerűen:

Az eslő visszafejtés még szemrevételezéssel történt:
```
	/* Alap állapot, motor nem működik
	* 3.253702750000000 - Parancs:0x5A, 0x5A, 0x00, 0x00, 0x01, 0x01, 
	* 3.257670875000000 - Adatok: 0xAA, 0x55, 0xAA, 0xFF, 0x02, 0xCF, 0x0E, 0x04, 0x0E, 0x05, 0x0D, 0xFB, 0x0E, 0x04, 0x0E, 0x05,
	*                             0x0D, 0x99, 0x0E, 0x06, 0x0E, 0x04, 0x0E, 0x06, 0x0E, 0x04, 0x0E, 0x06, 0x0E, 0x05, 0x0E, 0x06,
	*                             0x0E, 0x04, 0x0D, 0xD7, 0x0E, 0x04, 0x0E, 0x06, 0x0E, 0x06, 0x0E, 0x04, 0x0E, 0x01, 0x00, 0x00,
	*                             0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	*                             0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x22, 0x01, 0xAB, 0x3F, 0x00, 0x00,
	*                             0x8D, 0x7E, 0xC3, 0x00, 0x02, 0xBB, 0x2B, 0x00, 0x07, 0xFB, 0x1E, 0x00, 0x1B, 0x00, 0x1B, 0x00,
	*                             0x1A, 0x00, 0x1A, 0x00, 0x1B, 0x00, 0x1B, 0x01, 0x00, 0x00, 0x03, 0xE8, 0x00, 0x0A, 0x00, 0x00,
	*                             0x00, 0x00, 0x00, 0x07, 0x0E, 0x06, 0x06, 0x0D, 0x99, 0x0D, 0xFC, 0x14, 0x00, 0x00, 0x00, 0x05,
	*                             0x00, 0x6C, 0x02, 0xB3, 0x00, 0x00, 0x00, 0x00, 0x00, 0x82, 0x0E, 0xA6, 
	*/
	/*
	// Nagy teljesítmény, vagy is motor is működik
	* 0.134106937500000 - Parancs:0x5A, 0x5A, 0x00, 0x00, 0x01, 0x01,
	* 0.138156187500000 - Adatok: 0xAA, 0x55, 0xAA, 0xFF, 0x02, 0xCF, 0x0E, 0x05, 0x0E,
	*                             0x06, 0x0D, 0xFD, 0x0E, 0x06, 0x0E, 0x06, 0x0D, 0x9B, 0x0E, 0x08, 0x0E, 0x06,
	*                             0x0E, 0x07, 0x0E, 0x06, 0x0E, 0x07, 0x0E, 0x06, 0x0E, 0x07, 0x0E, 0x05, 0x0D,
	*                             0xD6, 0x0E, 0x06, 0x0E, 0x08, 0x0E, 0x08, 0x0E, 0x06, 0x0E, 0x03, 0x00, 0x00, 
	*                             0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	*                             0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
	*                             0x22, 0x01, 0xAB, 0x3F, 0x00, 0x00, 0x8D, 0x7B, 0xAC, 0x00, 0x02, 0xBB, 0x30,
	*                             0x00, 0x08, 0x02, 0x6E, 0x00, 0x19, 0x00, 0x19, 0x00, 0x19, 0x00, 0x19, 0x00,
	*                             0x19, 0x00, 0x19, 0x01, 0x01, 0x00, 0x03, 0xE8, 0x00, 0x0A, 0x01, 0x00, 0x00, 
	*                             0x00, 0x00, 0x07, 0x0E, 0x08, 0x06, 0x0D, 0x9B, 0x0D, 0xFE, 0x14, 0x00, 0x00,
	*                             0x00, 0x70, 0x00, 0x6C, 0x02, 0xB3, 0x00, 0x00, 0x00, 0x00, 0x38, 0xC2, 0x0E, 
	*                             0xE7, 
	*                             
	*                             6 nap 2 óra 4 perc - 02:04
	*                             0x00, 0x02, 0xBB, 0x40, - 0x00, 0x08, 0x03, 0xEF
	*                             179008                    525295 másodperc
	*                             0x00, 0x02, 0xBB, 0x30, - 0x00, 0x08, 0x01, 0xCD
	*                             178992
	*                             0x00, 0x02, 0xBB, 0x2B, - 0x00, 0x07, 0xFB, 0x25
	*                             178987
	*                             */
```
Konkrét visszafejtés, legalább is amelyet sikerült megfejtenem, már az adatok érkezésének poziciójától függően meghatározva:
```
	/*Adatok 140 byte
	Nyugtázás 		        4 byte	0xAA, 0x55, 0xAA, 0xFF	1-4
	Aku fesz		        2 byte				            5-6
	Cella 1-24		        2 byte * 24			            7-54
	Cella -25-32	        2 byte * 8			            55-70
	NA			            3 byte				            71-73
	Felvett amper           1 byte				            74
	Aku %			        1 byte				            75
	Aku kapacitás	        4 byte				            76-79
	Aku árama		        4 byte				            80-83
	Valamilyen idő		    4 byte				            84-87
	Üzemidő	(másodpercben)  4 byte				            88-91
	Hőszenzorok		        2 byte * 6			            92-103
	NA			            2 byte (0x0100 - 256)		    104-105
	NA			            2 byte (0x0003 - 3)		        106-107
	NA			            2 byte (0xE800 - 59392)		    108-109
	NA			            2 byte (0x0A00 - 2560)		    110-111
	NA			            2 byte				            112-113
	NA			            2 byte				            114-115
	Legmagassabb cella szám	1 byte (0x07 - 7)	        	116
	Legmagassabb cella fesz	2 byte (0x0E06 - 3590)	    	117-118
	Legalacsony. cella szám	1 byte (0x06 - 6)		        119
	Legalacsony. cella fesz 2 byte (0x0D99 - 3481)	      	120-121
	Átlag cella fesz	    2 byte (0x0DFC - 3580)		    122-123
	Cellák száma		    1 byte (0x14 - 20)		        124
	NA			            2 byte				            125-126
	NA			            2 byte (0x0005 - 5)		        127-128
	Legal és Legmag külöm.	2 byte (0x006C - 108)		    129-130
	NA			            2 byte (0x02B3 - 691)		    131-132
	NA			            2 byte				            133-134
	NA			            2 byte				            135-136
	NA			            2 byte (0x0082 - 130)		    137-138
	NA			            2 byte (0x0EA6 - 3750)		    139-140*/
```
A fenti visszafejtések segítségére C# programot használtam. Olyan programot írtam, amely képes a digitális analizátorral kimentett adatokat
feldolgozni és olyan kimenetet adni, amely értelmezése már egyszerű hallandók részére is könnyedén megy.
Ezt a kimenetet az assembled_out mappában találod.

Ezek után már meghatározató egy struktúra, amellyel már tovább lehet dolgozni:
```
// Vissza fejtett BMS és kijelző vezérlés közti UART-os kommunikáció alapján
typedef struct
{
	// Nyugtázás 		        4 byte	0xAA, 0x55, 0xAA, 0xFF	1-4
	unsigned int Nyugta;
	// Aku fesz		        	2 byte				            5-6
	unsigned short AkkuV;
	// Cella 1-24		        2 byte * 24			            7-54
	unsigned short CellaV[24];
	/*Cella -25-32	       		2 byte * 8			            55-70
	NA			            	3 byte				            71-73*/
	// Felvett amper			1 byte							74
	unsigned char FelvettAmper;
	// Aku %			        1 byte				            75
	unsigned char AkkuEnergia;
	// Aku kapacitás	        4 byte				            76-79
	unsigned int AkkuKapacitas;
	// Aku árama		        4 byte				            80-83
	unsigned int AkkuAram;
	// Valamilyen idő		    4 byte				            84-87
	unsigned int Valamilyenido;
	// Üzemidő	(másodpercben)  4 byte				            88-91
	unsigned int BMSUzemido;
	// Hőszenzorok		        2 byte * 6			            92-103
	unsigned short HoSzenzor[6];
	/*NA			            2 byte (0x0100 - 256)		    104-105
	NA			            	2 byte (0x0003 - 3)		        106-107
	NA			            	2 byte (0xE800 - 59392)		    108-109
	NA			            	2 byte (0x0A00 - 2560)		    110-111
	NA			            	2 byte				            112-113
	NA			            	2 byte				            114-115*/
	// Legmagassabb cella szám	1 byte (0x07 - 7)	        	116
	unsigned char LMCellaSzama;
	// Legmagassabb cella fesz	2 byte (0x0E06 - 3590)	    	117-118
	unsigned short LMCellaV;
	// Legalacsony. cella szám	1 byte (0x06 - 6)		        119
	unsigned char LACellaSzama;
	// Legalacsony. cella fesz 	2 byte (0x0D99 - 3481)	      	120-121
	unsigned short LACellaV;
	// Átlag cella fesz	    	2 byte (0x0DFC - 3580)		    122-123
	unsigned short AtlagCellaV;
	// Cellák száma		    	1 byte (0x14 - 20)		        124
	unsigned char CellakSzama;
	/*NA			            2 byte				            125-126
	NA			            	2 byte (0x0005 - 5)		        127-128*/
	// Legal és Legmag külöm.	2 byte (0x006C - 108)		    129-130
	unsigned short LMesLACellaKulombseg;
	/*NA			            2 byte (0x02B3 - 691)		    131-132
	NA			            	2 byte				            133-134
	NA			            	2 byte				            135-136
	NA			            	2 byte (0x0082 - 130)		    137-138
	NA			            	2 byte (0x0EA6 - 3750)		    139-140*/
	unsigned char Frissites;
} BMSData;
BMSData BMSAdatok;
```
# Kivitelezések elméletben
Ez a struktúra már felhasználható egy megfelelő program megírásához. Én pl.: STM32F072 és STM32F407-es MCU-kon használtam fel. A program
megirásánál fontos, hogy az UART megfigyelést megszakításba kezeljük. Ilyenkor vagy is megszakításonként 140 byte adat fog érkezni pont úgy,
ahogyan azt feljebb is látható. A megszakításban az adatokat fogadjuk majd egy fleg-et használva a főprogramnak tudjuk jelezni, hogy érkezett
új adat, majd a megfelelő feltételben elvégezni a szükséges dekódolásokat vagy is elvégezzük a feldolgozást.

# Konkrét kódok
Sokat agyaltam rajta, hogy a teljes projeket osszam e meg, amít már megírtam, de mivel azokat nem fejeztem be és egyes esetekben elég specifikus
beállíátsokat is tartalmazhat (pl: éritőkijelző, egyébb szenzorok, beállítási szál bonyolultsága, kerékpár adatainak kezelése, ram/rom kezelése
adatok mentésére), csak össze zavarná az érdeklődőt. Ezért a konkrét program részeket osztom csak meg itt a leírásban, ezzel le egyszerűsítve
és segítve az érdeklúdőket.

* Megszakítás kezelése (UART)
```
// stm32f4xx_it.c
/* USER CODE BEGIN EV */
volatile unsigned char RxBuffer[140] = {0};
volatile unsigned char RxFlag=0;
/* USER CODE END EV */

void USART1_IRQHandler(void)
{
  /* USER CODE BEGIN USART1_IRQn 0 */

  /* USER CODE END USART1_IRQn 0 */
  HAL_UART_IRQHandler(&huart1);
  /* USER CODE BEGIN USART1_IRQn 1 */
  if(HAL_UART_Receive_IT(&huart1, (uint8_t *)RxBuffer, 140) == HAL_OK)
  {
	  RxFlag=1;
  }

  /* USER CODE END USART1_IRQn 1 */
}
```

* Fontosabb változók a struktúrán kívűl, mert az feljebb teljes egészében látható és a feldolgozás menete:
```
// main.c
__HAL_UART_ENABLE_IT(&huart1, UART_IT_RXNE);	// Aktíváljuk RX fleget
unsigned char TxBuffer[] = {0x5A, 0x5A, 0x00, 0x00, 0x01, 0x01};
BMSAdatok.Frissites=1;
u8 StrTemp[100] = {0};
u8 Akkumulator_szint[] =  {9 + 48};

// main()
if(BMSAdatok.Frissites)
{
	BMSAdatok.Frissites=0;
	
	// Felvett amper
	sprintf(StrTemp, "%02i.%01iAh", BMSAdatok.FelvettAmper/10, BMSAdatok.FelvettAmper%10);
	StringWrite(90,110,200,144,24,StrTemp, WHITE, Kek);

	//wf.WriteLine("Felvett teljesítmény: {0} W", (double)(int.Parse(temp, System.Globalization.NumberStyles.HexNumber) * (double)int.Parse(Ebike[4] + Ebike[5], System.Globalization.NumberStyles.HexNumber) / 10.0)/10.0);
	// Felvett teljesítmény
	sprintf(StrTemp, "%03i.%02iWh", (BMSAdatok.FelvettAmper*BMSAdatok.AkkuV)/100, (BMSAdatok.FelvettAmper*BMSAdatok.AkkuV)%100);
	StringWrite(200,110,200,144,24,StrTemp, WHITE, Kek);

	// Sebbességé kiírása
	StringWrite(85,140,200,144,96,"29", WHITE, Kek);
	StringWrite(290,140,96,24,24,"Km/h", WHITE, Kek);

	// Napi számláló
	StringWrite(40,300,140,24,24,"042 Km", WHITE, Kek);
	StringWrite(224,300,200,24,24,"000713 Km", WHITE, Kek);

	// Akku feszültség
	sprintf(StrTemp, "%02i.%iv", BMSAdatok.AkkuV / 10, BMSAdatok.AkkuV % 10);
	StringWrite(505,185,100,24,24,StrTemp, WHITE, Szurke);

	// Akku töltöttsége %-ban
	sprintf(StrTemp, "%02i%c", BMSAdatok.AkkuEnergia, '%');
	StringWrite(480,210,200,50,32,StrTemp, WHITE, Szurke);

	// Akkumulátor maradék áramerősége
	sprintf(StrTemp, "%02i.%03iAh", BMSAdatok.AkkuAram / 1000000, (BMSAdatok.AkkuAram % 1000000)/1000);
	StringWrite(485,260,200,24,24,StrTemp, WHITE, Szurke);

	// Akkumlátor teljes kapacítása 28Ah
	sprintf(StrTemp, "Max:%02i.%01iAh", BMSAdatok.AkkuKapacitas / 1000000, BMSAdatok.AkkuKapacitas % 1000000);
	StringWrite(500,285,100,12,12,StrTemp, WHITE, Szurke);

	// Akku hőmérsékleti adatok
	sprintf(StrTemp, "%02i'", BMSAdatok.HoSzenzor[0]);
	StringWrite(582,307,20,12,12,StrTemp, WHITE, Szurke);

	sprintf(StrTemp, "%02i'", BMSAdatok.HoSzenzor[1]);
	StringWrite(582,353,20,12,12,StrTemp, WHITE, Szurke);

	sprintf(StrTemp, "%02i'", BMSAdatok.HoSzenzor[2]);
	StringWrite(582,400,20,12,12,StrTemp, WHITE, Szurke);

	sprintf(StrTemp, "%02i'", BMSAdatok.HoSzenzor[3]);
	StringWrite(582,448,20,12,12,StrTemp, WHITE, Szurke);

	// Akku és BMS hőmérsékleti adatok
	sprintf(StrTemp, "%02i'", BMSAdatok.HoSzenzor[4]);
	StringWrite(540,377,20,12,12,StrTemp, WHITE, Szurke);

	sprintf(StrTemp, "%02i'", BMSAdatok.HoSzenzor[5]);
	StringWrite(540,424,20,12,12,StrTemp, WHITE, Szurke);

	// Kicsi akuszint jelző
	if(BMSAdatok.AkkuEnergia!=0)
	Akkumulator_szint[0] = BMSAdatok.AkkuEnergia/12 + 48;	// Vékony aksi keret az alap (+48 anssi karakterek miatt)
	StringWrite(458,446,48,24,48,Akkumulator_szint, WHITE, Szurke);

	// Cella feszültségek, mind a 24 cella
	int y = 0;
	u16 Szin = 0;
	for (int x = 0; x < 24; x++)
	{
	  sprintf(StrTemp, "  %02i. %i.%iv  ",y+++1, BMSAdatok.CellaV[x] / 1000, BMSAdatok.CellaV[x] % 1000);
	  if(y==BMSAdatok.LMCellaSzama) Szin = RED;
	  else if(y==BMSAdatok.LACellaSzama) Szin = BLUE;
	  else if(BMSAdatok.CellaV[x]==0) Szin = Szurke;
	  else Szin = BLACKGREEN;
	  StringWrite(670,34+((y-1)*18)+1,100,12,12,StrTemp, WHITE, Szin);
	}

	// Legmagasabb cella feszültség
	//sprintf(StrTemp, "Legmagasabb cella feszultseg: %i. Cella %i.%iv", BMSAdatok.LMCellaSzama, BMSAdatok.LMCellaV / 1000, BMSAdatok.LMCellaV % 1000);
	//StringWrite(10,362,300,12,12,StrTemp, WHITE, Szurke);

	// Legalacsonyabb cella feszültség
	//sprintf(StrTemp, "Legalacsonyabb cella feszultseg: %i. Cella %i.%iv", BMSAdatok.LACellaSzama, BMSAdatok.LACellaV / 1000, BMSAdatok.LACellaV % 1000);
	//StringWrite(10,375,300,12,12,StrTemp, WHITE, Szurke);

	// Legmagasabb es legalacsonyabb töltés közti külömbség
	//sprintf(StrTemp, "Legmagasabb es legalacsonyabb toltes kozti kulombseg: %i.%iv", BMSAdatok.LMesLACellaKulombseg / 1000, BMSAdatok.LMesLACellaKulombseg % 1000);
	//StringWrite(10,388,400,12,12,StrTemp, WHITE, Szurke);

	// Atlag cella feszültség
	//sprintf(StrTemp, "Atlag cella feszultseg: %i.%iv", BMSAdatok.AtlagCellaV / 1000, BMSAdatok.AtlagCellaV % 1000);
	//StringWrite(10,401,300,12,12,StrTemp, WHITE, Szurke);

	// Cellak száma
	//sprintf(StrTemp, "Cellak szama: %idb", BMSAdatok.CellakSzama);
	//StringWrite(10,414,300,12,12,StrTemp, WHITE, Szurke);

	sprintf(StrTemp, "BMS uzemideje: %03i nap, %02i:%02i", (BMSAdatok.BMSUzemido / (3600 * 24)), ((BMSAdatok.BMSUzemido % (3600 * 24))/3600), (((BMSAdatok.BMSUzemido % (3600 * 24))%3600)/60));
	StringWrite(40,60,300,12,12,StrTemp, WHITE, Kek);

	sprintf(StrTemp, "Valamilyen ido: %03i nap, %02i:%02i", (BMSAdatok.Valamilyenido / (3600 * 24)), ((BMSAdatok.Valamilyenido % (3600 * 24))/3600), (((BMSAdatok.Valamilyenido % (3600 * 24))%3600)/60));
	StringWrite(10,446,300,12,12,StrTemp, WHITE, Szurke);

	StringWrite(10,460,300,16,16,"DPTP System - E-bike Computer 2019", WHITE, Szurke);
}// Frissítés vége


// Ha érkezet értékelhető adat, nyugtázás 0xAA55AAFF
if(RxFlag==1)
{
  RxFlag=0;	// Flag semlegesítése
  if((((((((unsigned int)RxBuffer[0]<<8)|RxBuffer[1])<<8)|RxBuffer[2])<<8)|RxBuffer[3])==0xAA55AAFF)
  {
	  // Ha minden oké akkor az adatokat fel kell dolgozni
	  // Nyugtázás 		        4 byte	0xAA, 0x55, 0xAA, 0xFF	1-4
	  BMSAdatok.Nyugta = ((((((unsigned int)RxBuffer[0]<<8)|RxBuffer[1])<<8)|RxBuffer[2])<<8)|RxBuffer[3];
	  // Aku fesz		        	2 byte				            5-6
	  BMSAdatok.AkkuV = (( unsigned int)RxBuffer[4] << 8 ) | RxBuffer[5];
	  // Cella 1-24		        2 byte * 24			            7-54
	  //BMSAdatok.CellaV[24];
	  i=0;
	  for (int x = 0; x < 48; x++)
	  {
		  BMSAdatok.CellaV[i++] = (( unsigned int)RxBuffer[6+x] << 8 ) | RxBuffer[7+x];
		  x++;
	  }
	  // Felvett amper			1 byte							74
	  BMSAdatok.FelvettAmper = RxBuffer[73];
	  // Aku %			        1 byte				            75
	  BMSAdatok.AkkuEnergia = RxBuffer[74];
	  // Aku kapacitás	        4 byte				            76-79
	  BMSAdatok.AkkuKapacitas = ((((((unsigned int)RxBuffer[75]<<8)|RxBuffer[76])<<8)|RxBuffer[77])<<8)|RxBuffer[78];
	  // Aku árama		        4 byte				            80-83
	  BMSAdatok.AkkuAram = ((((((unsigned int)RxBuffer[79]<<8)|RxBuffer[80])<<8)|RxBuffer[81])<<8)|RxBuffer[82];

	  // Valamilyen idő		    4 byte				            84-87
	  BMSAdatok.Valamilyenido = ((((((unsigned int)RxBuffer[83]<<8)|RxBuffer[84])<<8)|RxBuffer[85])<<8)|RxBuffer[86];
	  // Üzemidő	(másodpercben)  4 byte				            88-91
	  BMSAdatok.BMSUzemido = ((((((unsigned int)RxBuffer[87]<<8)|RxBuffer[88])<<8)|RxBuffer[89])<<8)|RxBuffer[90];

	  // Hőszenzorok		        2 byte * 6			            92-103
	  //BMSAdatok.HoSzenzor[6];
	  i=0;
	  for (int x = 0; x < 12; x++)
	  {
		  BMSAdatok.HoSzenzor[i++] = (( unsigned int)RxBuffer[91+x] << 8 ) | RxBuffer[92+x];
		  x++;
	  }
	  // Legmagassabb cella szám	1 byte (0x07 - 7)	        	116
	  BMSAdatok.LMCellaSzama = RxBuffer[115];
	  // Legmagassabb cella fesz	2 byte (0x0E06 - 3590)	    	117-118
	  BMSAdatok.LMCellaV = (( unsigned int)RxBuffer[116] << 8 ) | RxBuffer[117];
	  // Legalacsony. cella szám	1 byte (0x06 - 6)		        119
	  BMSAdatok.LACellaSzama = RxBuffer[118];
	  // Legalacsony. cella fesz 	2 byte (0x0D99 - 3481)	      	120-121
	  BMSAdatok.LACellaV = (( unsigned int)RxBuffer[119] << 8 ) | RxBuffer[120];
	  // Átlag cella fesz	    	2 byte (0x0DFC - 3580)		    122-123
	  BMSAdatok.AtlagCellaV = (( unsigned int)RxBuffer[121] << 8 ) | RxBuffer[122];
	  // Cellák száma		    	1 byte (0x14 - 20)		        124
	  BMSAdatok.CellakSzama = RxBuffer[123];
	  // Legal és Legmag külöm.	2 byte (0x006C - 108)		    129-130
	  BMSAdatok.LMesLACellaKulombseg = (( unsigned int)RxBuffer[128] << 8 ) | RxBuffer[129];

	  BMSAdatok.Frissites=1;		// LCD frissítése

	  //ADAT OK!

	  // Biztonság kedvéért töröljük a buffert és RxFerCount-ot is beállítjuk FULL-ra.
	  memset(RxBuffer, '\0', sizeof(RxBuffer));
	  huart1.RxXferCount = 140;
  }
  else
  {
	  // Biztonság kedvéért töröljük a buffert és RxFerCount-ot is beállítjuk FULL-ra.
	  memset(RxBuffer, '\0', sizeof(RxBuffer));
	  huart1.RxXferCount = 140;
	  //ADAT ERROR!
  }

}
```

# Konkluzió
A programot anno 2019-ben használtam, de a kerékpár eladása miatt a projekt teljesen nem készült el soha, a fenti adatok inkább csak tájékoztató
jellegűek, annak felhasználása csak saját felelőségre történhet. Remélem tudtam segíteni és ha esetleg más is jár úgy mint én, hogy tönkre megy
ez a tipusú kijelzője, akkor ezzel a leírással talán meg tudja majd menteni és nem kell további komolyabb költségbe vernie magát. Ez a projekt
tanulás szempontjából sem volt rossz, mert talán az első olyan kihívás volt számomra, amelyben konkért visszafejtésre volt szükségem.
Köszi, hogy megtekinteted a projektet.. ;)