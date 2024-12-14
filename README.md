# Virtual Memory and Cache

### Disclaimer
Det här systemet är inte perfekt,
jag vet inte om all logik stämmer till 100%, men det
här är hur jag tänkte på uppgiften och det fungerade för mig.
Om man klarar den här uppgiften så behöver man bara skrapa ihop
15 poäng till för att klara tentan. 
Denna metod garanterar de flesta poängen på uppgiften iallafall

**Skicka feedback och frågor till mig om något är oklart eller fel.**

## Översikt

Så det här är alla tal som kan ändras i Uppgiften, 
utifrån dem går allt annat att räkna ut om man vet hur, 
jag tänker gå igenom alla tal och hur de hänger ihop.

![image](./images/Overview.png)

## Page size

Vi tittar först på de första tre talen. 
Alla dessa påverkas endast av virtuellt-, fysiskt minne och page size (blå, grön och magenta).

![image](./images/First3.png)

Eftersom våran mips är 32 bits så är virtuellt minne 32 bitar, 
alltid, denna kommer aldrig ändras.

Fysiskt minne däremot ändras beroende på hur mycket RAM vi har, 
i detta fall har vi 4GB RAM vilket är 2^32 bytes.*\
Detta innebär då att även våran fysiska adress är 32 bitar stor; 
alltså exponenten i 2^32 är 32.

Det sista talet är page size, detta är 4kB vilket är 2^12 bytes.*
Här betyder exponenten i 2^12 att page offset är 12 bitar stor.

Vi kan nu fylla i de första tre talen i bilden.
Notera att vi har lite tur att både virtuellt och fysiskt minne är 32 bitar
vilket ger oss samma page number för båda.
Detta är definitivt inte alltid fallet.
* Offset = 12 bitar
* Virtual page number = virtuell adress - page offset = 32 - 12 = 20 bitar
* Physical page number = fysisk adress - page offset = 32 - 12 = 20 bitar

![image](./images/First3.png)
![image](./images/First3Input.png)

\* Bra att memorera att 2^32 = 4GB och 2^12 = 4kB, 
då är det lätt att ta reda på dessa om de ändras i uppgiften,
ex 2^33 = 8GB, 2^31 = 2GB och 2^13 = 8kB, 2^11 = 2kB.

## Tags
De nästa tre talen är relativt straight forward,
de påverkas endast av lines (röd) och den virtuell 
page vi räknade ut tidigare.

![image](./images/TagsLines.png)

Först får vi reda på hur många lines vi har vilket innebär att vi har
så många lines som behöver jämföras. 
Därav är antalet lines = antalet Comparisons.
Så det hämtar vi bara från frågan vilket är 16 i detta fall.

Det andra numret kan vi som tur är också bara hämta.
Bredden av en line kommer direkt från bredden av virtual page number.
Den har vi redan räknat ut till 20 bitar, det viktiga är att vi kommer ihåg
att det är *virtual* page number och inte *physical* page number.

Det sista numret är nu lätt att räkna ut. Det är helt enkelt bara
_comparators_ * _lines_. I detta fall är det 16 x 20 = 320 bitar.

Vi kan nu fylla i de tre talen i frågan.
* Comparators = lines = 16
* Line width = virtual page number = 20 bitar
* Tag width = comparators * line width = 16 * 20 = 320 bitar

![image](./images/TagsLines.png)
![image](./images/TagsExtendedInput.png)

## Lines

Bland nästkommande 3 tal går 2 tal att skriva in direkt.
Ett av dem kräver dock lite mer beräkning, men påverkas endast av
data cache (gul) och data words (cyan).

![image](./images/Lines.png)

Vi börjar att skriva in de enkla talen.
Dessa är physical adress och adress bits.

Physical adress är samma som när vi bestämde 
fysiskt minne i första delen.
Det är alltså 32 bitar, eftersom 4GB = 2^32 bytes.

Adress bits är _alltid_ 4..0.
Det är bara att komma ihåg.

Det andra talet är lite mer komplicerat.
För att ta reda på det måste vi först göra några beräkningar.

Vi börjar med att räkna ut hur mycket data vi har i varje line.
Detta räknas ut genom att multiplicera data words 
med bredden av en data word, rätt så logiskt.

data words * data word width = cache data line.\
I detta fall är det 8 * 4 = 32 bitar.

Nu delar vi bara hur mycket minne vi har i cache
med hur mycket data vi har i varje line. Minnet vi har anges
i KB så kom ihåg att tolka det som tusental.

cache size / cache data line = lines.\
I detta fall är det 64000 / 32 = 2000 lines.
I uppgiften frågas om antal lines i tusental, så svaret blir 2.

Vi kan nu fylla i de tre talen i frågan.
* Physical address = fysiskt minne = 32 bitar
* Address bits = 4..0
* Lines = cache size / cache data line = 64000 / 32 = 2000 lines = 2k lines
  * (Cache data line = data words * data word width = 8 * 4 = 32 bitar)

![image](./images/Lines.png)
![image](./images/LinesInput.png)

## Direct Mapped & Set Associative Cache

Den här delen av uppgiften är honestly rätt flummig. 
Det finns system för att räkna ut allt men jag föredrar och rekommenderar
att bara memorera vad det kan vara och utgå från det istället. 
Jag kan också alldeles för lite om hur man systematiskt räknar ut det.
Så det får bli memorering vilket fungerade för mig, 
men denna del av uppgiften är definitivt den svåraste.

### Direct Mapped Cache

![image](./images/DirectMapped.png)

Jag tycker vi börjar med de lättaste talen.

Antalet comparators verkar alltid vara 1 och bredden av en comparator verkar 
alltid vara 16 bitar.
![image](./images/Comparators.png)

Från detta kan vi nu räkna ut resten.
Kom ihåg hur stor vår fysiska adress är som vi räknade ut förut, 
2^x, i detta fall 2^32 = 32 bitar.
Eftersom bitar är nollindexerade så är det 31..0 vi följer.
Då är det första talet 31..16 eftersom vi har 16 bitar i en comparator.
Sedan, eftersom vi redan konstaterat att line offset är 5 bitar, 4..0.
Så vet vi att det sista talet är 15..5.
![image](./images/Adress_bits.png)

Nu är det endast storleken på vårat memory kvar.
För att räkna ut detta måste vi kika tillbaka på hur många lines vi har.
Från förra delen av uppgiften vet vi att vi har 2k lines:
![image](./images/Lines2.png)

Vi vet nu också att bredden av en comparator alltid är 16 bitar.
Så vi multiplicerar bara dessa två tal för att få storleken på vårat minne.
2k * 16 = 32k bitar minne.
![image](./images/CacheMemory.png)

Nu kan vi fylla i alla tal.
* Adress bits
  * 31..16 (16 bitar lång, alltid)
  * 15..5 (resten av adressen)
  * 4..0 (5 bitar lång, alltid)
* Cache memory = lines * comparator width = 2k * 16 = 32k bitar
* Comparators = 1
* Comparator width = 16 bitar
![image](./images/DirectMappedInput.png)
![image](./images/DirectMappedInput2.png)

### Set Associative Cache

Här är det lite mindre tal att hålla reda på, och det är bara
ett av talen vi behöver ens räkna ut, resten är bara att memorera.
![image](./images/Associative.png)

Vår fysiska adress är fortfarande 32 bitar, 2^32 = 4GB.
Därmed, eftersom bitar är nollindexerade, är det fortfarande 31..0 vi följer.

Första talet är därför 31.

Resterande tal är bara att komma ihåg. 13, 12 och 5.
Varför? Jo på set associative cache är det alltid x..13 för första intervallen.
Därmed, eftersom line offset alltid är 5 bitar, 4..0, så måste det sista talet alltid vara 12.
Så dessa kommer som jag förstått det, alltid vara samma. 
Det enda som ändras är den högsta biten som beroende på hur stor fysisk adressen är.

Vi kan nu fylla i alla tal.
* Adress bits
  * 31
  * 13
  * 12
  * 5

![image](./images/AssociativeInput.png)

