# COMPRESSIONE DEI TESTI A "NIBBLE"

Uno degli elementi più "ingombranti" dei programmi scritti in BASIC V2 sono sicuramente i testi: messaggi di errore, descrizioni, etichette, istruzioni ... tutto deve essere fortemente ridotto per risparmiare spazio prezioso per il resto del programma. Questo vale sia per applicazioni più che meno serie, come i giochi, e diventa fondamentale in quei giochi in cui i testi sono essenziali.

Mi riferisco, ovviamente, alle cosiddette "avventure testuali" e sarebbe fantastico se trovassimo un modo per rappresentare i testi in modo più "compatto".

in generale. Il modo migliore per risolvere questo problema è prendere in considerazione la bassa variabilità delle lettere e, quindi, utilizzare una rappresentazione di nibble (4 bit) anziché un byte (8 bit).

![Un nibble è di 4 bit o metà di un byte. Una cifra esadecimale ha una dimensione di un nibble. Source: Wikipedia.](nibble.png)

Questo approccio ridurrebbe idealmente l'occupazione dello spazio necessario esattamente a metà. Ovviamente, possiamo rappresentare solo 16 simboli (0 ... 15), che sono insufficienti per tutte le lettere dell'alfabeto e un segno di punteggiatura (28 lettere: a-z, il punto "" e lo spazio).

Tuttavia, se assegnassimo le lettere più frequenti in quel testo a questi 16 simboli, **ridurremmo statisticamente l'occupazione di memoria** poiché useremo più frequentemente un nibble anziché un byte.

Per poter codificare qualsiasi testo generico, dovremo comunque sacrificare alcuni dei valori rappresentabili, per introdurre un meccanismo per tornare (temporaneamente) alla rappresentazione a 8 bit, quandose ne ravveda il bisogno.

Per fare un esempio, prenderò il seguente testo da [10LINES ADV (edizione italiana)] (https://github.com/spotlessmind1975/adv10):

> NEL MEZZO DEL CORRIDOIO. VEDI DUE STANZE, A NORD E SUD. C'E' UNA PORTA A EST.

Questa stringa è lunga esattamente 78 caratteri e ha 19 lettere diverse. Se lo rappresentiamo utilizzando caratteri a 8 bit, occuperebbe esattamente 78 byte.

<table>
    <tr>
        <td>"I"</td>
        <td>"N"</td>
        <td>(spazio)</td>
        <td>"T"</td>
        <td>"H"</td>
        <td>"E"</td>
        <td>...</td>
    </tr>
    <tr>
        <td>7</td>
        <td>6</td>
        <td>F</td>
        <td>2</td>
        <td>9</td>
        <td>5</td>
        <td>...</td>
    </tr>
</table>

Supponiamo ora di utilizzare la seguente tabella di corrispondenze: ho creato questa tabella ordinando le lettere, dalla più frequente alla meno frequente in quel testo:

19Array ( [0] => e [1] => o [2] => d [3] => a [4] => n [5] => r [6] => t [7] => s [8] => u [9] => i [10] => . [11] => z [12] => c [13] => l [14] => ' [15] => v [16] => m [17] => , [18] => p )

Now, we suppose to use the following correspondence table. I built this table by ordering the letters from the most frequent to the least one in that text:

<table>
    <tr>
        <th>LETTER</th>
        <th>DECIMAL (4 bit)</th>
        <th>HEXADECIMAL (4 bit)</th>
    </tr>
    <tr>
        <td>"E"</td>
        <td>1</td>
        <td>1</td>
    </tr>
    <tr>
        <td>"O"</td>
        <td>2</td>
        <td>2</td>
    </tr>
    <tr>
        <td>"D"</td>
        <td>3</td>
        <td>3</td>
    </tr>
    <tr>
        <td>"A"</td>
        <td>4</td>
        <td>4</td>
    </tr>
    <tr>
        <td>"N"</td>
        <td>5</td>
        <td>5</td>
    </tr>
    <tr>
        <td>"R"</td>
        <td>6</td>
        <td>6</td>
    </tr>
    <tr>
        <td>"T"</td>
        <td>7</td>
        <td>7</td>
    </tr>
    <tr>
        <td>"S"</td>
        <td>8</td>
        <td>8</td>
    </tr>
    <tr>
        <td>"U"</td>
        <td>9</td>
        <td>9</td>
    </tr>
    <tr>
        <td>"I"</td>
        <td>10</td>
        <td>A</td>
    </tr>
    <tr>
        <td>"."</td>
        <td>11</td>
        <td>B</td>
    </tr>
    <tr>
        <td>"Z"</td>
        <td>12</td>
        <td>C</td>
    </tr>
    <tr>
        <td>"C"</td>
        <td>13</td>
        <td>D</td>
    </tr>
    <tr>
        <td>(escape)</td>
        <td>14</td>
        <td>E</td>
    </tr>
    <tr>
        <td>(spazio)</td>
        <td>15</td>
        <td>F</td>
    </tr>
</table>

Ora prendiamo una parte del testo precedente e lo esprimiamo usando i numeri esadecimali della tabella delle frequenze precedente:

<table>
    <tr>
        <td>"D"</td>
        <td>"U"</td>
        <td>"E"</td>
        <td>(spazio)</td>
        <td>"S"</td>
        <td>"T"</td>
        <td>...</td>
    </tr>
    <tr>
        <td>3</td>
        <td>9</td>
        <td>1</td>
        <td>14</td>
        <td>8</td>
        <td>7</td>
        <td>...</td>
    </tr>
</table>

Poiché tutti questi numeri possono essere rappresentati con un solo nibble (4 bit), possono essere "raggruppati" in un singolo byte ogni due nibble (8 bit):

<table>
    <tr>
        <td>"DU"</td>
        <td>"E" (spazio)</td>
        <td>"ST"</td>
        <td>...</td>
    </tr>
    <tr>
        <td>76</td>
        <td>F2</td>
        <td>95</td>
        <td>...</td>
    </tr>
</table>

Il testo "due st", che in precedenza occupava 6 byte, dopo il "processo" occupa solo 3 byte. **Pertanto abbiamo risparmiato il 50% dello spazio!**

Se la lettera non è tra quelle in corrispondenza, abbiamo bisogno di un valore aggiuntivo, (escape) (14, hex. E). Questo valore rappresenta una "sequenza di escape" e indica che la lettera non verrà rappresentata con un nibble ma con il byte successivo.

Nell'esempio sopra, se dovessimo tradurre "NE" avremmo avuto la seguente sequenza:

<table>
    <tr>
        <td>"M"</td>
        <td>"E"</td>
        <td></td>
        <td></td>
        <td>...</td>
    </tr>
    <tr>
        <td>E</td>
        <td>1</td>
        <td>7</td>
        <td>7</td>
        <td>...</td>
    </tr>
</table>

Quindi: 2 byte per 2 byte, e ovviamente non vi sono risparmi.

Può succedere che vi siano due lettere che non possono essere tradotte in una sequenza. In questo caso, avremo **DUE** sequenze di escape e due caratteri nella coda: quindi 3 byte per 2 byte e avremo una perdita.

Ad esempio:

<table>
    <tr>
        <td>"M"</td>
        <td>"M"</td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td>...</td>
    </tr>
    <tr>
        <td>E</td>
        <td>E</td>
        <td>7</td>
        <td>7</td>
        <td>7</td>
        <td>7</td>
        <td>...</td>
    </tr>
</table>

Si tenga in debito conto il fatto che un nibble con valore 0 è escluso, al fine di evitare che una doppia sequenza di questo valore possa emettere un byte di uscita non valido (uguale a 00). In generale, alcune sequenze potrebbero non essere valide sul sistema di destinazione e pertanto devono essere sostituite con un doppio nibble "EE", seguito dai due caratteri "così come sono". Uno di questi casi è il carattere di virgolette (") su COMMODORE 64, utilizzato per racchiudere una stringa.

Per valutare la quantità di risparmio che potremmo ottenere applicando questo schema di compressione, dobbiamo verificare la relazione tra lo spazio occupato da tutti gli elementi necessari per ricostruire il testo rispetto alla lunghezza del testo originale. Ovviamente, poiché le performance variano in base al contenuto del testo, **non è possibile fornire una misura deterministica di tale rapporto**. Tuttavia, possiamo presentare un modello di stima (pessimistico) che tenga conto delle seguenti condizioni:

 - **che il 50% del testo** possa essere codificato da 2 nibble (2 bytes to 1 bytes);
 - **che il 40% del testo** possa essere codificato da 1 nibble e da una sequenza di escape (2 bytes to 2 bytes);
 - **che il 10% del testo** possa essere codificato da due sequenze di escape (2 bytes to 3 bytes).

![Tendenza del rapporto di compressione in funzione della lunghezza del testo](compression_ratio.png)

Questo grafico mostra l'andamento del rapporto di compressione in funzione della lunghezza del testo di input, con le regole sopra esposte sul contenuto.

L'andamento del grafico con le varie lunghezze presenta un punto di equilibrio intorno a 70-90 caratteri e migliora notevolmente con l'aumentare della lunghezza dei testi (l'asse x è in scala logaritmica).

Ad esempio, con un testo di 256 caratteri si ottiene un risparmio di 46 caratteri, raggiungendo un rapporto di compressione di circa l'86%. Con 1 KB di testo si ottiene un rapporto dell'81% e un risparmio netto di 190 caratteri. In realtà, **i risparmi sono maggiori e ciò rende l'utilizzo di questa tecnica molto vantaggioso**, specialmente se i testi possono essere modificati per ottimizzare il contenuto.

Per rendere riutilizzabile questo algoritmo, il decodificatore è stato implementato **come una singola riga di codice BASIC V2** di esattamente 251 caratteri. Questa routine si trova sulla riga 9 del programma **adv10** e può essere chiamata come subroutine (<code>GOSUB 9</code>), purché si usi <code>RETURN</code> nella riga seguente.

Si devono passare le seguenti variabili come parametri:
- in **v$** il dizionario delle lettere (una string adi 16 caratteri);
- in **e$** la sequenza di byte da decomprimere;
- in **f** la lunghezza di questa sequenza;
- in **z** il carattere di partenza, dal quale iniziare la decodifica.

Segue la routine commentata, in cui le tecniche "in una riga" sono state abbandonate a favore di una migliore leggibilità:

<pre><code>50 REM *************************************************************************
51 REM *** DECOMPRESSORE NIBBLE (BASIC V2) by m.spedaletti (asimov@mclink.it)
52 REM *************************************************************************
53 REM *** UTILIZZO:
54 REM ***   v$ = dizionario (16 simboli, usati solo i primi 13)
55 REM ***   e$ = sequenza di byte da decodificare
56 REM ***   f  = lunghezza della sequenza da decodificare
57 REM ***   z  = prima posizione da decodificare
58 REM ****************************************************************************
59 REM
60 REM ESEMPIO (tratto da "adv10.bas")
61 v$="aneotisuldrc.p"
62 e$=""{050}{249}{062}{077}{238}{090}{090}{244}{058}{249}{076}{187}{166}{100}{212}{239}{086}{163}{246}{138}{243}{087}{033}{062}{090}{254}{044}{241}{066}{171}{063}{127}{168}{253}{236}{039}{227}{039}{143}{018}{239}{080}{180}{021}{031}{063}{087}{237}{032}"
63 f=len(e$)
64 z=1
65 GOSUB 100
66 END
100 REM DECOMPRESSORE NIBBLE
110 REM Prendiamo lo z-esimo carattere da decodificare
120 w$=mid$(e$,z,1)+e$
130 REM Convertiamolo in un valore byte
140 y=asc(w$)
150 REM Estraiamo i due nibble
160 n0=yand15:n1=(y/16)and15
170 REM Sono uno spazio?
180 s0=(n0=15):s1=(n1=15)
190 REM Sono una sequenza di escape?
200 v0=(n0=14):v1=(n1=14)
210 REM Sono lettere?
220 l0=(n0<14):l1=(n1<14)
230 REM Stampiamo il primo e il secondo carattere
240 if v0 then print mid$(e$,z+1,1); REM primo = escape
250 if s0 then print " "; REM primo = spazio
260 if l0 then print mid$(v$,n0,1); REM primo = lettera
270 if v1 then print mid$(e$,z+(v0=0)+2,1); REM secondo = escape
280 if s1 then print " "; REM secondo = spazio
290 if l1 then print mid$(v$,n1,1); REM secondo = lettera
300 REM Andiamo avanti sulla sequenza, tenendo conto del fatto che
310 REM ogni sequenza di escape implica andare avanti di un byte in piu'.
320 REM Quindi si può andare avanti da 1 a 3 byte alla volta.
330 z=z-v0-v1+1
340 REM Decodifica terminata?
350 ifz<=fthen100
360 return
</code>