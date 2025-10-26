# Invaders2090

stay tuned...

# Befehlssatz

Der Befehlssatz des Microtronic 2090 ist sehr durchdacht. Trotzdem habe ich das eine oder andere Mal bestimmte Operationen im Befehlssatz vermisst, mit dem sich eine gegebene Programmieraufgabe hätte schneller/eleganter/sparsamer lösen lassen. Zum Beispiel wären weitere bedingte Sprünge (_BRNC_, _BRNZ_, ...) sehr angenehm. Aaaber...

Sprungbefehle benötigen beide Stellen für die Zieladresse, da der Programmspeicher von Adresse 00 bis FF reicht. Das bedeutet, dass jeder Sprungbefehl einen vollständigen Opcode-Korridor belegt (**B**xx - _CALL_, **C**xx - _GOTO_, **D**xx - _BRC_, **E**xx - _BRZ_). Da der Opcode-Korridor **F** für die Display-Darstellung (F1x-F6x) sowie für Befehle mit einem (F7x-FFx) oder mit keinem Operanden (F00-F0F) benötigt wird, bliebe für weitere wünschenswerte Sprungbefehle nur ein Opcode von **0**xx-**A**xx. In diesem Bereich des Befehlssatzes sind allerdings alle Befehle mit zwei Operanden untergebracht. Und von diesen Befehlen ließe sich nur unter größten Schmerzen irgendetwas einsparen. Man könnte z.B. die _SUB_/_SUBI_-Befehle streichen und im Lehrbuch mühsam erklären, dass Subtraktion eine Addition mit dem um 1 erhöhten Zweierkomplement ist, aber will man das wirklich? 

Ganz im Gegenteil, es war angesichts der Ökonomie des nur 3-stelligen Befehlscodes sogar nötig, einen Befehl nicht zu implementieren: Logisches _AND_ und _ANDI_ gibt es, logisches _OR_ gibt es auch, aber logisches _ORI_ existiert im Befehlssatz nicht. Besonders schlimm ist das nicht, da die Konstante (der _immediate_-Wert) natürlich vorher mit _MOVI_ in ein Register geschrieben und anschließend ein _OR_ ausgeführt werden kann - sofern für solche Späße noch Register frei sind. Das Beispiel zeigt aber, dass beim Design des Befehlssatzes bereits Kompromisse gemacht werden mussten. _ORI_ schien den Verantwortlichen wohl der am wenigsten notwendige Befehl zu sein. 

In diesem Zusammenhang wäre es übrigens historisch interessant, warum der - didaktisch später eingeführte - _CALL_-Befehl den Opcode B **vor** dem Opcode C des unbedingten Sprungbefehls _GOTO_ erhalten hat. 


Auf _HALT_ (F00) hingegen hätte ich verzichten können - ein "ewiger" Sprung auf die eigene Adresse erfüllt den gleichen Zweck und lässt bei Programmende zudem die Displayanzeige intakt. Ebenso ist der Befehl _NOP_ (F01) nicht wirklich nötig, da etwa _MOV 0,0_ (000) neben vielen anderen redundanten Operationen den gleichen Zweck erfüllt - und auch optisch eher wie ein "leerer" Befehl aussieht.

