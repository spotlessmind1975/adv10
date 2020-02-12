# CODICE SORGENTE (CON SPIEGAZIONE)

Di seguito si trova il codice sorgente del videogioco. Il codice sorgente è stato scritto per esteso (senza abbreviazioni), per facilitarne la comprensione. Ogni riga è stata commentata per illustrare come funziona il codice.

## INIZIALIZZAZIONE (LINEE 1-3)

*L'ordine delle linee in questa sezione va dalla riga più corta a quella più lunga. In questo modo, ottimizzeremo lo spazio disponibile nelle prime tre righe di codice.*

Impostiamo la stanza (**x**) su zero. Successivamente la modificheremo automaticamente o in base alle azioni eseguite dal giocatore. Si veda anche [la stanza attuale (X)](/docs/game-state.md#la-stanza-attuale-x)

<pre>1 x=0</pre>

Questa variabile memorizzerà il carattere '0' come codice PETSCII. *Questa è un'ottimizzazione quando se ne utilizza il valore sullo schermo, e non è direttamente correlato al gioco.*

<pre>t=48</pre>

Cancella lo schermo (lo facciamo solo una volta!).

<pre>print "{clear}";</pre>

Puntatore all'inizio della memoria video dei caratteri (**k**) e della memoria video dei colori (**n**). Qui è dove inizieremo a scampare il punteggio, ovvero sulla 37a colonna della prima riga. Sul Commodore 64, la memoria video dei caratteri inizia dalla posizione 1024 e la memoria colori inizia da 55296, quindi: 1024 + 37 = 1061; 55296 + 37 = 55333. *Questa è un'ottimizzazione quando si effettua il "poke" dei valori sullo schermo, e non è direttamente correlata al gioco.*

<pre>k=1061 : n=55333</pre>

Puntatore all'inizio dei registri audio. *Questa è un'ottimizzazione quando si effettua il "poke" di valori sul controller audio, e non è direttamente correlato al gioco.*

<pre>w=54272</pre> 

Da questo momento in poi, dichiariamo i testi relativi ai vari componenti del gioco (per un totale di 17 testi): descrizioni delle stanze, oggetti, messaggi di errore, titoli e così via. Tutti i testi sono memorizzati in una matrice monodimensionale, il cui indice viene calcolato. Lo schema degli indici garantisce una facile accessibilità mediante un semplice calcolo numerico. Ad esempio, le descrizioni delle stanze coincidono quasi tutte con il numero della stanza. Il contenuto dei testi sono compressi utilizzando la [tecnica di "compressione nibble"](/docs/compression.md).

<pre>dim d$(17)</pre>

Modifichiamo il numero del dispositivo I/O corrente su 32. Seguendo [questa guida](https://www.c64-wiki.com/wiki/Zeropage), si nota come siano utilizzati solo i primi 5 bit. Quindi, impostando 32, ripristineremo la tastiera ma la useremo come "file". Questo disabiliterà la stampa del punto interrogativo (<code>?</code>) sull'input da tastiera - lo sostituiremo con un simbolo "maggiore di" esplicito, e più classico (<code>></code>).

<pre>poke 19,32</pre>

Ora impostiamo lo sfondo e il colore del bordo dello schermo ([qui per una mappa completa](https://www.c64-wiki.com/wiki/Page_208-211)). La cosa migliore è usare il colore nero (0) per entrambi. Questo è il colore "classico" delle avventure testuali.

<pre>poke 53281,0 : poke 53280,0</pre>

Messaggio: <code>ok</code>.
<pre>d$(11)="{226}{075}"</pre>

Messaggio: <code>eh?</code>
<pre>d$(10)="{236}{072}{254}{063}"</pre>

Messaggio: <code>sei uscito!</code>
<pre>d$(6)="{195}{244}{053}{071}{043}{254}{033}"</pre>

Messaggio: <code>vedi una corda</code>
<pre>d$(8)="{206}{086}{072}{095}{217}{127}{018}{216}"</pre>

Messaggio: <code>nient'altro.</code>
<pre>d$(14)="{073}{156}{235}{039}{173}{027}{098}"</pre>

Messaggio: <code>sul piedistallo.</code>
<pre>d$(4)="{083}{250}{078}{080}{140}{052}{219}{170}{098}"</pre>

Messaggio: <code>in una sala torture.</code>
<pre>d$(5)="{148}{095}{217}{063}{173}{253}{043}{177}{021}{108}"</pre>

Qui troviamo il "dizionario" utilizzato per l'algoritmo di [compressione](/docs/compression.md). La sequenza di caratteri è stata scelta con cura, in modo che le sequenze di escape siano ridotte al minimo. Si noti che solo i primi tredici (13) caratteri vengono utilizzati dalla codifica; il resto sono caratteri arbitrari ("*"). *L'estensione a 16 caratteri della stringa è necessaria per evitare situazioni di "overflow" nella routine di decompressione, quando vengono rilevati nibble rappresentanti sequenze di escape (posizione 14) o lo spazio (posizione 15).*

<pre>v$="rosiu.cdnltea***"</pre>

Messaggio: <code>la porta e' chiusa.</code>
<pre>d$(15)="{218}{239}{080}{018}{219}{207}{254}{039}{231}{072}{084}{211}{246}"</pre>

Messaggio: <code>** 10lines adv **</code>
<pre>2 d$(12)="{238}{042}{042}{239}{049}{174}{048}{148}{060}{223}{232}{086}{239}{042}{254}{042}"</pre>

Messaggio: <code>non puoi andare in quella direzione</code>
<pre>d$(7)="{041}{249}{094}{080}{066}{223}{137}{029}{252}{148}{239}{081}{197}{170}{253}{072}{193}{078}{090}{146}{252}"</pre>

Questa è la rappresentazione linearizzata della [mappa del gioco](/docs/game-state.md#mappa-del-gioco-e-connessioni). La mappa è composta da sei stanze, collegate tra loro su quattro direzioni. Ogni collegamento da una stanza all'altra è rappresentato da una cifra (0 ... 6), che corrisponde alla stanza di destinazione, se si procede lungo quella determinata direzione (N, S, W, E). La stanza 0 significa "passaggio non consentito". Quattro cifre compongono tutte le connessioni a partire da una stanza. Ci sono un totale di 24 cifre.

<pre>m$="000235160204003020000020"</pre>

Messaggio: <code>sul piedistallo,  vedi una chiave</code>
<pre>d$(9)="{083}{250}{078}{080}{140}{052}{219}{170}{226}{044}{255}{206}{086}{072}{095}{217}{127}{078}{072}{237}{086}{252}"</pre>

Messaggio: <code>una chiave, una corda, null'altro</code>
<pre>d$(17)="{149}{253}{231}{072}{212}{206}{086}{254}{044}{149}{253}{039}{129}{237}{044}{159}{165}{234}{039}{173}{027}{242}"</pre>

Messaggio: <code>cc by-nc-sa 3.0 by m.spedaletti</code>
<pre>d$(13)="{119}{239}{066}{238}{089}{045}{121}{062}{045}{253}{110}{051}{254}{048}{238}{066}{089}{239}{077}{054}{206}{080}{216}{202}{187}{244}"</pre>

Messaggio: <code>benvenuti nel dungeon.  c'e' una porta a est.</code>
<pre>d$(1)="{206}{066}{233}{086}{156}{181}{244}{201}{250}{088}{233}{071}{044}{105}{255}{231}{039}{236}{039}{095}{217}{239}{080}{018}{219}{223}{207}{179}{246}"</pre>

Messaggio: <code>in una stanza con un piedistallo a est, e un fossato tutto attorno.</code>
<pre>3 d$(3)="{148}{095}{217}{063}{219}{233}{090}{253}{039}{249}{149}{239}{080}{196}{072}{179}{173}{042}{223}{207}{179}{254}{044}{252}{149}{239}{070}{050}{211}{043}{191}{181}{043}{223}{187}{018}{041}{246}"</pre>

Messaggio: <code>in una stanza con un piedistallo. sul fossato, una corda sta penzolando a est.</code>
<pre>d$(16)="{148}{095}{217}{063}{219}{233}{090}{253}{039}{249}{149}{239}{080}{196}{072}{179}{173}{042}{246}{083}{250}{046}{070}{051}{189}{226}{044}{095}{217}{127}{018}{216}{063}{219}{239}{080}{156}{046}{090}{218}{137}{242}{253}{060}{107}"</pre>

Messaggio: <code>nel mezzo del corridoio. vedi due stanze, a nord e sud. c'e' una porta a est.</code>
<pre>d$(2)="{201}{250}{206}{077}{238}{090}{090}{242}{200}{250}{039}{017}{132}{066}{098}{239}{086}{140}{244}{088}{252}{179}{157}{206}{090}{254}{044}{253}{041}{129}{207}{063}{133}{246}{231}{039}{236}{039}{095}{217}{239}{080}{018}{219}{223}{207}{179}{246}"</pre>

Ok, questa è la prima volta che chiamiamo [la routine di decompressione che stampa una descrizione](/docs/compression.md#un-decompressore-leggibile) (linea 9). *Lo chiameremo più volte.* In **e$** mettiamo il testo da stampare, in **f** ne mettiamo la lunghezza, mentre in **z** mettiamo la posizione da cui iniziare decomprimere. Prima di stampare, impostiamo il colore verde (per i titoli).

Prima linea dei titoli:
<pre>print "{light green}";:z=1:e$=d$(12):f=len(e$):gosub9:print</pre>

Seconda linea dei titoli (e una riga vuota):
<pre>z=1:e$=d$(13):f=len(e$):gosub9:print:print</pre>

## LOOP DI GIOCO (LINEE 4-8)

Da questo punto in poi, le linee del programma sono ordinate secondo una logica di gioco.

### DESCRIZIONE DELLA STANZA E PUNTEGGIO (LINEE 4-5)

È qui che inizia il ciclo di gioco. Il giocatore tornerà su questa linea (linea 4) ogni volta che è necessaria la descrizione della stanza. La descrizione della stanza viene stampata usando il colore grigio chiaro.

<pre>4 print "{light gray}";</pre>

La posizione di partenza dipende [dalla stanza in cui si trova il giocatore](/docs/game-state.md#la-stanza-attuale-x) (la stanza numero zero è un caso speciale). Tutte le stanze hanno una descrizione che inizia dalla posizione 1, tranne la stanza 1, in cui inizia dalla posizione 15. *Con questo espediente, siamo in grado di differenziare la prima descrizione, in modo da caratterizzare l'inizio del gioco.*

<table>
    <tr>
        <td><pre>z = -
(x=0)-
(x=1)*15-
(x>0)</pre></td>
        <td><pre>equivale a:
[ IF X=0 THEN Z=1]
[ IF X=1 THEN Z=15]
[ IF X>0 THEN Z=1]</pre></td>
    </tr>
</table>

Se siamo nella stanza zero, spostiamoci nella stanza uno (altrimenti, rimaniamo là).

<table>
    <tr>
        <td><pre>x = -
(x=0)+
x</pre></td>
        <td><pre>equivale a:
[ IF X=0 THEN X=1]
[ IF X<>0 THEN X=X]</pre></td>
    </tr>
</table>

Questa è la descrizione da stampare sullo schermo, che dipende sia dal numero della stanza (**x**) sia, nel caso in cui il giocatore si trovi nella stanza nr. 3, [se il giocatore ha usato la corda o no](/docs/game-state.md#introduzione) (**g**).

<table>
    <tr>
        <td><pre>e$ = d$( -
(x=0)+
x+
(x=3)*(g=1)*13)</pre></td>
        <td><pre>equivale a:
[ IF X=0 THEN E$=D$(1) ]
[ IF X=3 THEN E$=D$(16) ]
[ IF X<>0 THEN E$=D$(X) ]</pre></td>
    </tr>
</table>

Infine, stampa la descrizione della stanza.

<pre>f=len(e$):gosub9</pre>

Ferma il suono. Il suono viene prodotto dalla routine che stampa la descrizione e viene utilizzato per fornire un feedback al giocatore.

<pre>poke w+4,32</pre>

Ora stampiamo una frase specifica per [le condizioni al contorno](/docs/game-state.md#introduzione). La posizione di partenza dipende dalla stanza in cui si trova il giocatore e dalla condizione che [la chiave non sia stata presa](/docs/game-state.md#introduzione) se il giocatore si trova nella stanza n. 4.

<table>
    <tr>
        <td><pre>z = 
(x=4)*(b=0)*11+1</pre></td>
        <td><pre>equivale a:
[ IF X=4 AND B=0 THEN Z=12 ELSE Z=1 ]</pre></td>
    </tr>
</table>

La seguente espressione complessa viene utilizzata per calcolare l'indice della frase da utilizzare, in base alle [condizioni al contorno](/docs/game-state.md#introduzione) e in base alla [stanza corrente](/docs/game-state.md#la-stanza-attuale-x) in cui si trova il giocatore.

<table>
    <tr>
        <td><pre>q=
(x=5)*(a=0)*8+
(x=4)*(b=0)*9+
(x=3)*(b=0)*9 :
q = q +
(x=2)*(h=0)*15</pre></td>
        <td><pre>equivale a:
[ IF X=5 AND A=0 THEN Q=8 ]
[ IF X=4 AND B=0 THEN Q=9 ]       
[ IF X=3 AND B=0 THEN Q=9 ]       
equivale a:
[ IF X=2 AND H=0 THEN Q=9 ]</pre></td>
    </tr>
</table>

Nota bene: viene stampato comunque un messaggio standard se nessuna delle condizioni è soddisfatta.

<table>
    <tr>
        <td><pre>e$ = d$( - 
(q>0)*q-
(q=0)*14)</pre></td>
        <td><pre>equivale a:
[ IF Q>0 THEN E$=D$(Q) ]
[ IF Q=0 THEN E$=D$(14) ]</pre></td>
    </tr>
</table>

Infine, stampa la descrizione delle condizioni al contorno.

<pre>f=len(e$):gosub9:print</pre>

Stampiamo il punteggio. Lo facciamo andando direttamente sulla memoria video, e così facendo impediamo che il punteggio scompaia a causa dello scorrimento quando arriviamo alla fine dello schermo.

<pre>5 pokek,t+a+b+g+h:poken,1:pokek+1,t-1:poken+1,1:pokek+2,t+4:poken+2,1</pre>

### PARSER (LINES 5-6)

Aspettiamo che il giocatore digiti qualcosa (in grigio).

<pre>input"{gray}>";p$</pre>

Passiamo la stringa al [parser](/docs/parser.md). Per cercare di capire cosa ha scritto il giocatore, esaminiamo le 2 lettere in due diverse posizioni (1 e 9). Nota che potrebbe digitare MENO di nove caratteri: per questo motivo, aggiungeremo in coda a quanto digitato la stringa che rappresenta la mappa di gioco, per avere abbastanza lettere. *Questa è un'ottimizzazione e non ha nulla a che fare con la logica del gioco.*

<pre>p1$=mid$(p$+m$,1,1)
p9$=mid$(p$+m$,9,1)</pre>

Decodifichiamo il verbo.
<table>
    <tr>
        <td><pre>v = -
(p1$="n")-
(p1$="s")*2-
(p1$="o")*3
v = v - 
(p1$="e")*4-
(p1$="t")*5-
(p1$="u")*6-
(p1$="i")*7</pre></td>
        <td><pre>equivale a:
[ IF P1$="N" THEN V=1 ]
[ IF P1$="S" THEN V=2 ]
[ IF P1$="O" THEN V=3 ]
equivale a:
[ IF P1$="E" THEN V=4 ]
[ IF P1$="T" THEN V=5 ]
[ IF P1$="U" THEN V=6 ]
[ IF P1$="I" THEN V=7 ]</pre></td>
    </tr>
</table>

Successivamente, decodifichiamo l'oggetto (se presente).
<table>
    <tr>
        <td><pre>6 o = 
(v=5)*((p9$="o")+
(p9$="h")*2)
o = o + 
(v=6)*
 ((p9$="a")+(p9$="v")*2)</pre></td>
        <td><pre>equivale a:
[ IF V=5 AND P9$="O" THEN O=1 ]
[ IF V=5 AND P9$="E" THEN O=2 ]
equivale a:
[ IF V=6 AND P9$="A" THEN O=1 ]
[ IF V=6 AND P9$="V" THEN O=2 ]</pre></td>
    </tr>
</table>

### LOGICA DEL GIOCO (LINEE 6-7)

Se l'azione è "prendi la corda" (v=5 e o=1), aggiorneremo lo stato, se la corda non è già stata presa (a=1) ed è nella stanza giusta (x=5).

<table>
    <tr>
        <td><pre>a = -
(a=1)+
((x=5)*(a=0)*(v=5)*(o=1))</pre></td>
        <td><pre>equivale a:
[ IF A=1 THEN A=1 ]
[ IF X=5 AND A=0 AND V=5 AND O=1 THEN A=1 ]</pre></td>
    </tr>
</table>

Se l'azione è "prendi chiave" (v=5 e o=2), aggiorneremo lo stato, se la chiave non è già stata presa (b=1) ed è nella stanza giusta (x=4).

<table>
    <tr>
        <td><pre>b = -
(b=1)+
((x=4)*(b=0)*(v=5)*(o=2))</pre></td>
        <td><pre>equivale a:
[ IF B=1 THEN B=1 ]
[ IF X=4 AND B=0 AND V=5 AND O=2 THEN B=1 ]</pre></td>
    </tr>
</table>

Se l'azione è "usa corda" (v=6 e o=1), aggiorneremo lo stato, se la corda non è già stata usata (g=1) ed è nella stanza giusta (x=3).

<table>
    <tr>
        <td><pre>g = - 
(g=1)-
((a=1)*(x=3)*(g=0)*(v=6)*(o=1))</pre></td>
        <td><pre>equivale a:
[ IF G=1 THEN G=1 ]
[ IF A=1 AND X=3 AND G=0 AND V=6 AND O=1 THEN G=1 ]</pre></td>
    </tr>
</table>

Se l'azione è "usa chiave" (v=6 e o=2), aggiorneremo lo stato, se la chiave non è già stata utilizzata (h=1) ed è nella stanza giusta (x=2).

<table>
    <tr>
        <td><pre>h = -
(h=1)-
((b=1)*(x=2)*(h=0)*(v=6)*(o=2))</pre></td>
        <td><pre>equivale a:
[ IF H=1 THEN H=1 ]
[ IF B=1 AND X=2 AND H=0 AND V=6 AND O=2 THEN G=1 ]</pre></td>
    </tr>
</table>

Passiamo ora a calcolare quale sarebbe la stanza successiva SE le condizioni fossero state soddisfatte. Innanzitutto, se il verbo è uno di quelli in movimento, la stanza dovrebbe essere quella indicata dalla mappa.

<table>
    <tr>
        <td><pre>r=
(v>0)*(v<5)*val(
 mid$(m$,(x-1)*4-(v>0)*v-(v=0),1)
 )</pre></td>
        <td><pre>equivale a:
[ IF V>0 AND V<5 THEN 
 R=VAL(MAPPA) ]</pre></td></tr>
</table>

Tuttavia, se il verbo non è di movimento, la stanza successiva sarà sempre quella corrente. Lo stesso accade se il programma non capisce cosa digita il giocatore.

<table>
    <tr>
        <td><pre>r=r - 
x*(v>4)-
x*(v=0)</pre></td>
        <td><pre>equivale a:
[ IF V>4 THEN R=X ]
[ IF V=0 THEN R=X ]</pre></td>
    </tr>
</table>

Indipendentemente dalla stanza in cui andrà il giocatore, devono essere soddisfatte le condizioni necessarie per lo spostamento.

<table>
    <tr>
        <td><pre>r = 
((r=4)*(g=1))*4+
((r=6)*(h=1))*6+
((r<>4)*(r<>6))*r</pre></td>
        <td><pre>equivale a:
[ IF R=4 AND G=1 THEN R=4 ]
[ IF R=6 AND H=1 THEN R=6 ]
[ IF R<>6 AND R<>4 THEN R=R ]</pre></td>
    </tr>
</table>

In definitiva, se la stanza successiva è zero (0), significa che il giocatore deve rimanere dove si trova; altrimenti, si sposterà di conseguenza.

<table>
    <tr>
        <td><pre>x = -
(r>0)*r-
x*(r=0)</pre></td>
        <td><pre>equivale a:
[ IF R>0 THEN X=R ]
[ IF R=0 THEN X=X ]</pre></td>
    </tr>
</table>

### RISPOSTA (LINE 8)

Ora dobbiamo calcolare la risposta. Implica il test di una serie di possibilità:
- che il programma non ha capito cosa intendesse il giocatore;
- che il giocatore voleva muoversi, ma non poteva farlo;
- che il giocatore voleva fare qualcosa, ma non poteva farlo.

Per ognuna di queste condizioni (**j=1**), c'è un messaggio specifico. C'è un messaggio specifico anche per la condizione che tutto sia ok (**j=0**) e che il giocatore possa spostarsi in un'altra stanza (o rimanere in quella in cui si trova).

<table>
    <tr>
        <td><pre>j = -
(r=0)-
(v=0)-
((v>4)*(v<>7)*(o=0))
j = j -
((v=5)*(o=1)*(a=0))-
((v=5)*(o=2)*(b=0))
8 j = j -
((v=6)*(o=1)*(g=0))-
((v=6)*(o=2)*(h=0))</pre></td>
        <td><pre>equivale a:
[ IF R=0 THEN J=1 ]
[ IF V=0 THEN J=1 ]
[ IF V>4 AND V<>7 AND O=0 THEN J=1 ]
equivale a:
[ IF V=5 AND O=1 AND A=0 THEN J=1 ]
[ IF V=5 AND O=2 AND B=0 THEN J=1 ]
equivale a:
[ IF V=6 AND O=1 AND G=0 THEN J=1 ]
[ IF V=6 AND O=2 AND G=1 THEN J=1 ]
</pre></td>
    </tr>
</table>

Ancora una volta, usiamo messaggi differenziati, sia che si verifichi un errore (**j=1**) o meno (**j= 0**).

<table>
    <tr>
        <td><pre>e$ = d$( -
(j=1)*(-(r=0)*7-
(r>0)*10)-
(j=0)*(-(v=7)*17-
(v<>7)*11))</pre></td>
        <td><pre>equivale a:
[ IF J=1 AND R=0 THEN E$=D$(7) ]
[ IF J=1 AND R>0 THEN E$=D$(10) ]
[ IF J=0 AND V=7 THEN E$=D$(17) ]
[ IF J=0 AND V<>7 THEN E$=D$(11) ]
</pre></td>
    </tr>
</table>

Il colore indica se si è verificato un errore (colore rosso) o meno (colore verde). Il verde è PETSCII 30 mentre il rosso è PETSCII 28.

<table>
    <tr>
        <td><pre>print
chr$(-(j=1)*28-
(j=0)*30)</pre></td>
        <td><pre>equivale a:
[ IF J=1 THEN PRINT CHR$(28) ]
[ IF J=0 THEN PRINT CHR$(30) ]
</pre></td>
    </tr>
</table>

Il punto di partenza da cui iniziare a decodificare la descrizione dipende dal messaggio e dalle [condizioni al contorno](/docs/game-state.md#introduzione). Questa è la logica che gestisce la risposta da restituire quando viene richiesto l'inventario (**v=7**). L'algoritmo fa sì che la scrittura venga inviata sullo schermo in base al "possesso" di oggetti. L'intero messaggio viene scritto quando il giocatore possiede tutti gli oggetti, mentre un messaggio sempre più breve viene stampato se il giocatore ne possiede solo alcuni, o nessuno.

<table>
    <tr>
        <td><pre>z = -
(j=1)+
(j=0)*(v<7)+
(j=0)*(v=7)*((b=1)*(a=1)+
(b=0)*(a=1)*7+
(b=0)*(a=0)*13)</pre></td>
        <td><pre>equivale a:
[ IF J=1 THEN Z=1 ]
[ IF J=0 AND V<7 THEN Z=1 ]
[ IF J=0 AND V=7 AND A=1 AND B=1 THEN Z=1 ]
[ IF J=0 AND V=7 AND A=1 THEN Z=7 ]
[ IF J=0 AND V=7 AND A=0 THEN Z=13 ]
</pre></td>
    </tr>
</table>

Infine, stampa la risposta (e una riga vuota).

<pre>f=len(e$):gosub9:print:print</pre>

Se la risposta è stata un errore (**j=1**), ci spostiamo al punto in cui chiediamo l'input dell'utente (linea 5); altrimenti (**j=0**) ripetiamo il ciclo di gioco, che proporrà una nuova stanza al giocatore (linea 4).
 
<pre>on j+1 goto 4,5</pre>

Questa è la routine di decompressione del testo ([vedi qui](/docs/compression.md#un-decompressore-leggibile) per un programma più leggibile).

<pre>9 w$=mid$(e$,z,1):y=asc(w$):
n0=yand15:n1=(y/16)and15
v0=(n0=14):v1=(n1=14):
l0=(n0<14):l1=(n1<14)
print 
 chr$(-v0*asc(mid$(e$,(-v0*z)+1,1))-
(n0=15)*32-l0*asc(mid$(v$,n0,1)));
 chr$(-v1*asc(mid$(e$,(-v1*z)+2+l0+(n0=15),1))-
 (n1=15)*32-l1*asc(mid$(v$,n1,1)));
 z=z-v0-v1+1:ifz<=fthen9</pre>

Sebbene quest'area sia ancora parte della routine di decompressione, abbiamo inserito un po' di codice e logica, perché non c'era spazio altrove. In particolare, emettiamo un suono quando abbiamo finito di stampare una stringa.

<pre>10 pokew+5,9 : pokew+6,0
pokew+24,15 : pokew+1,25
pokew,177 : pokew+4,33
</pre>

Se ci troviamo a stampare l'ultima frase del gioco, allora il gioco continuerà a funzionare (terminando). Altrimenti, torniamo al punto di chiamata, per continuare con l'esecuzione.

<pre>if x<>6 or f<10 then return</pre>