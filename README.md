# Invaders2090

stay tuned...

## Befehlssatz

Der Befehlssatz des Microtronic 2090 ist sehr durchdacht. Trotzdem habe ich das eine oder andere Mal bestimmte Operationen im Befehlssatz vermisst, mit dem sich eine gegebene Programmieraufgabe hätte schneller/eleganter/sparsamer lösen lassen. Zum Beispiel wären weitere bedingte Sprünge (_BRNC_, _BRNZ_, ...) sehr angenehm. Aaaber...

Sprungbefehle benötigen im Befehlscode die beiden hinteren Stellen für die Zieladresse aa, da der Programmspeicher von Adresse 00 bis FF reicht. Das bedeutet, dass jeder Sprungbefehl einen vollständigen Opcode-Korridor belegt (**B**aa - _CALL_, **C**aa - _GOTO_, **D**aa - _BRC_, **E**aa - _BRZ_). Da der Opcode-Korridor **F** für die Display-Darstellung (F1x-F6x) sowie für Befehle mit einem (F7x-FFx) oder mit keinem Operanden (F00-F0F) benötigt wird, bliebe für weitere wünschenswerte Sprungbefehle nur ein Opcode von **0**xx-**A**xx. In diesem Bereich des Befehlssatzes sind allerdings alle Befehle mit zwei Operanden untergebracht. Und von diesen Befehlen ließe sich nur unter größten Schmerzen irgendetwas einsparen. Man könnte z.B. die _SUB_/_SUBI_-Befehle streichen und im Lehrbuch mühsam erklären, dass Subtraktion eine Addition mit dem um 1 erhöhten Zweierkomplement ist, aber will man das wirklich? 

Ganz im Gegenteil, es war angesichts der Ökonomie des nur 3-stelligen Befehlscodes sogar nötig, einen Befehl nicht zu implementieren: Logisches _AND_ und _ANDI_ gibt es, logisches _OR_ gibt es auch, aber logisches _ORI_ existiert im Befehlssatz nicht. Besonders schlimm ist das nicht, da die Konstante (der _immediate_-Wert) natürlich vorher mit _MOVI_ in ein Register geschrieben und anschließend ein _OR_ ausgeführt werden kann - sofern für solche Späße noch Register frei sind. Das Beispiel zeigt aber, dass beim Design des Befehlssatzes bereits Kompromisse gemacht werden mussten. _ORI_ schien den Verantwortlichen wohl der am wenigsten notwendige Befehl zu sein. 

In diesem Zusammenhang wäre es übrigens historisch interessant, warum der - im Lehrbuch deutlich später eingeführte - _CALL_-Befehl den Opcode B **vor** dem Opcode C des unbedingten Sprungbefehls _GOTO_ erhalten hat. Im Vorwort zu seiner Diplomarbeit schreibt Jörg Vallen: "Es zeigte sich ferner, daß einige wichtige Befehle für die leichte und kapazitätssparende Programmierung fehlten, wie z.B. der Sprung in ein Unterprogramm..." Meine (wahrscheinlich völlig abwegige) Vermutung dazu: Ursprünglich könnte der Opcode-Korridor B mit dem _ORI_-Befehl belegt gewesen sein (zumal _OR_ den genau davor liegenden Korridor A belegt), dann jedoch wurde der Platz für den _CALL_-Befehl benötigt und _ORI_ musste weichen.

Auf _HALT_ (F00) hingegen hätte ich verzichten können - ein "ewiger" Sprung auf die eigene Adresse erfüllt den gleichen Zweck und lässt bei Programmende zudem die Displayanzeige intakt. Ebenso ist der Befehl _NOP_ (F01) nicht wirklich nötig, da etwa _MOV 0,0_ (000) neben vielen anderen redundanten Operationen den gleichen Zweck erfüllt - und auch optisch eher wie ein "leerer" Befehl aussieht.

Wie mir erst sehr kürzlich klar wurde, ist auch der _SHL_-Befehl eigentlich redundant. Eine bitweise binäre Verschiebung nach links ist ja eine Multiplikation mit 2, also eine Verdopplung. Das kann man natürlich auch durch Addition "mit sich selbst" erreichen. Es wäre interessant, in der Firmware nachzusehen, ob _SHL_ separat implementiert wurde - übersteigt aber meine Fähigkeiten. Im folgenden werde ich stattdessen einen Geschwindigkeitsvergleich zwischen _SHL d_ und _ADD d,d_ anstellen - dessen Ergebnis dann ein starkes Indiz für die eine oder andere Variante liefert.

## Ausführungsgeschwindigkeit 

Wie üblich aus reiner Neugier, aber ein bisschen auch von der Notwendigkeit getrieben begann ich darüber nachzudenken, wie schnell (oder langsam) der Microtronic einzelne Befehle ausführt. Michael hat dazu ja bereits Untersuchungen durchgeführt und eine Größenordnung von 40-120 Hips festgestellt. 

Da mir mehrere 2090 zu Verfügung stehen, habe ich mich zu einer Testreihe entschlossen:

- Gibt es (deutlich) messbare Unterschiede zwischen einzelnen 2090?
- Gibt es einen Unterschied zwischen ADD und SUB?
- Gibt es einen Unterschied zwischen SHR und SHL?
- Gibt es einen Unterschied zwischen SHL d und ADD d,d?
- zwischen NOP und MOV d,d
- Wie schnell werden Registerbänke getauscht (EXRL, EXRM, EXRA)? Gibt es Unterschiede?
- Wie schnell werden DIN und DOT durchgeführt?
- Wie schnell werden unbedingte und bedingte Sprünge ausgeführt?
- Welche Befehle sind "teuer", welche "billiger"?
- Erkennt der Microtronic redundante Befehle? MOV 0,0 vs. MOV 0,1? ADDI 0,d vs. ADDI 1,d?
- Was löscht ein Register schneller: MOVI 0,d oder SUB d,d?

Als Testanordnung dient im wesentlichen ein Programm, dass den jeweiligen Befehl ausreichend häufig (250 mal) hintereinander ausführt. Zu Beginn und am Ende des Tests wird ein akustisches Signal ausgelöst. Die Zeit zwischen den Signalen wird gemessen (mit Android phyphox) und durch die Anzahl der Ausführungen geteilt - dadurch erhalten wir einen relativ genauen Wert für die Geschwindigkeit der Ausführung eines einzelnen Befehls. 

Da inzwischen die komplette Firmware des Microtronic dem TMS1600 mühevoll entrissen und dankenswerterweise veröffentlicht wurde, kann man natürlich auch dort nachsehen, wie einzelne Befehle implementiert sind. Das wäre dann allerdings etwas, das ich erst im nächsten Leben angehen würde. 

```
- SHL d        20,248 ms
- ADD d,d      20,372 ms
- MOVI 1,d     20,336 ms
- SHR d        20,296 ms
- ADDI 1,d     20,328 ms
- ADDI F,d
- SUBI 1,d     20,296 ms
- SUBI F,d
- DOT s        20,288 ms
- DIN d        19,888 ms
- EXRA         21,960 ms
- EXRL         20,692 ms
- EXRM         20,780 ms
- NOP          20,444 ms
- MOV d,d      20,364 ms
- GOTO aa      20,040 ms
- BRC aa       20,200 ms
- BRZ aa       19,980 ms
- CALL aa      20,068 ms
- CLEAR        20,784 ms
- STC          20,416 ms
- RSC          20,232 ms
- RND          20,568 ms
- TIME         20,776 ms
- CMP s,d      20,108 ms
- CMPI n,d     20,006 ms      
```

- Der _NOP_-Befehl, der ja angeblich nix macht (außer den Programmzähler zu inkrementieren)... dieser Befehl ist einer der "teuersten". Das überrascht dann doch :-)
- Ein bedingter Sprung _BRZ_ wird schneller ausgeführt als ein unbedingter Sprung _GOTO_. Auch unerwartet.

# Let's do the time warp 

Ein Microcontroller kann nicht nichts tun. Sobald und solange Strom anliegt, arbeitet der TMS1600 sein im ROM hinterlegtes Programm ab. Jede Instruktion des TMS (nicht des Microtronic!) benötigt 6 Takte zur Ausführung. 

Anders als beworben und angegeben, arbeitet der Microtronic nicht mit 500 kHz, sondern mit einem Systemtakt von 676 kHz. TI in Freising war in die Entwicklung des 2090 eingebunden und ging offenbar sehr taktvoll mit dieser Übertaktung um, also dürfen wir annehmen, dass uns der TMS1600 nicht im Betrieb wegschmilzt. Da die ältesten Microtronics schon seit über 40 Jahren noch immer ihren Dienst tun, ist diese Vermutung auch hinreichend gestützt. 

Jetzt aber ein bisschen Mathematik...

```
1 Takt:                   1 / 676.000 Hz = 0,0000014793 s = 1,4793 ns
1 TMS-Instruktion:        6 x Takt = 8,8758 ns
```

Ein **Microtronic-Befehl** dauert wie gemessen ca. 20 ms, also werden innerhalb der Dauer eines Befehls 20 ms / 8,8758 ns = etwa **2.253 TMS-Instruktionen** ausgeführt. Wenn man bedenkt, dass das ganze Microtronic-ROM nur 4 kB groß ist... wird entweder tatsächlich immer das halbe ROM abgearbeitet (unwahrscheinlich) oder hauptsächlich irgendwo in Schleifen gewartet.




