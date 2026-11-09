# Space Invaders – das Spiel

- 254 Programmschritte, 2 frei
- Datei: [invaders.mic](invaders.mic) (Version 36)
- Benötigt die 8x8-LED-Matrix und drei Taster.

## Spiel

- **Schiff:** 3 LEDs breit in Zeile 1.
- **Aliens:** 11 Aliens in Zeile 6-8. Der Block pendelt zwischen zwei Lagen.
- **Rakete:** Es ist immer nur eine Rakete unterwegs. Sie trifft das erste Hindernis auf ihrem Weg, ein Alien oder eine Bombe.
- **Bombe:** Es fällt immer nur eine Bombe, sie entsteht in einer zufälligen Spalte von Zeile 5.
- **Neue Welle:** Sind alle Aliens abgeschossen, kommt sofort eine neue Welle.
- **Spielende:** Trifft eine Bombe das Schiff, ist das Spiel vorbei. Die Anzeige zeigt die Punkte dezimal.

| Treffer | Punkte |
|---|---|
| Alien in Zeile 6 / 7 / 8 | 1 / 2 / 3 |
| Bombe in Zeile 5 / 4 / 3 / 2 | 1 / 2 / 3 / 4 |

Alle 16 Punkte steigt die Spielstufe: Bomben kommen häufiger und fallen schneller. Ab 128 Punkten startet die Bombe schon in Zeile 4.

## Bedienung

| Taster | Pfeiltaste im Emulator | Wirkung |
|---|---|---|
| Eingang 2 | hoch | links |
| Eingang 3 | runter | Feuer |
| Eingang 4 | rechts | rechts |

Die Taster werden einmal je Spieltakt gelesen, also etwa alle 0,5-1 s. Den Taster deshalb halten, bis das Schiff reagiert.

## Start

RESET (alle Register 0, das Programm enthält kein CLEAR), Matrix einschalten, `HALT` `NEXT` `00` `RUN`. Neues Spiel: RESET.

## Matrixprotokoll

Das Programm arbeitet mit dem [ESP2090 Studio](https://github.com/rab-berlin/ESP2090) im Blockmodus:

1. Es wartet auf die Anfrage des ESP (REQ auf Eingang 1).
2. Es antwortet mit ACK 1.
3. Nach genau einem Befehl (EXRL) folgen die 16 Bildregister.
4. Zum Schluss setzt es die Ausgänge auf 0.

Eingang 1 wird beim Lesen der Taster ausgeblendet. Auf der Weboberfläche des ESP2090 Studios „LED on“ drücken, im [Emulator2090](https://github.com/rab-berlin/Emulator2090) die Matrix mit `m` einschalten (Modus ESP).

Version 35 lief noch mit dem früheren Blockprotokoll (Startnibble 1, Endnibble 0). Version 36 ist durch die neue Bildausgabe 2 Schritte kürzer.

## Stellschrauben

Beide kosten keinen zusätzlichen Schritt:

- **Adresse 69, `SUBI #4,ZUFALL`:** wie schnell die Bomben fallen. Eine kleinere Zahl ergibt ein gemütlicheres Spiel.
- **Adresse A1, `CMPI #1,ZUFALL`:** wie oft die Aliens pendeln. Eine größere Zahl macht sie zappeliger.
