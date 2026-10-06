Consideriamo un valore ***decimale*** intero, come ad esempio 5623 - il valore di questo numero è dato dalla **somma dei prodotti di ogni cifra con la potenza di 10 corrispondente alla posizione in cui si trova**, ossia:
$$5623 = 5 \cdot10^3 + 6 \cdot 10^2 + 2 \cdot 10^1 + 3 \cdot 10^0$$
Siccome ci sono **10 cifre**, ad ogni posizione moltiplichiamo per la ***potenza di 10 corrispondente***.

Se invece consideriamo un valore ***binario***, in cui il sistema ha solo **due cifre**, allora il valore di un numero è dato dalla **somma dei prodotti di ogni cifra con la potenza di 2 corrispondente alla posizione in cui si trova**.

Solitamente, per numeri grandi, in *decimale* suddividiamo le cifre del numero a gruppi di 3, come per esempio il numero 123456789 viene espresso come 123,456,789 - in *binario* possiamo fare una cosa simile raggruppando le cifre a gruppi di 4, come per esempio il numero 101110101 viene espresso come 0001 0111 0101 (gli zeri a sinistra vengono aggiunti per consistenza del gruppo da 4).

#### Conversione *unsigned* **da binario a decimale**
Supponiamo di parlare di ***numeri interi senza segno (unsigned)***.
Consideriamo il *numero binario a 8 bit (1 byte)* `0101 1110`. Per ottenere il valore decimale di questo numero, dobbiamo ***sommare i prodotti di ciascuna cifra e la potenza di 2 corrispondente alla sua posizione***. Quindi: $$(0101 \ 1110)_{2} = 0 \cdot 2^7 + 1 \cdot 2^6 + 0 \cdot 2^5 + 1 \cdot 2^4 + 1 \cdot 2^3 + 1 \cdot 2^2 + 1 \cdot 2^1 + 0 \cdot 2^0$$ $$= 64 + 16 + 8 + 4 + 2 = (94)_{10}$$
Quindi il numero *binario* `0101 1110` esprime in base 2 il numero *decimale* 94.

#### Conversione *unsigned* da **decimale a binario**
Convertire un numero da base 10 a base 2 è leggermente più complicato. Vediamo tre metodi diversi per convertire il numero *decimale* 148 in base 2.
##### Metodo dei resti delle divisioni per 2
In questo metodo il numero *decimale* **viene diviso ripetutamente per 2**, scrivendo **i resti di queste divisioni**. Il numero viene ricostruito, quando il risultato della divisione è 0, scrivendo **al contrario i resti delle divisioni**.
Convertiamo 148:
- 148 / 2 = 74, resto 0
- 74 / 2 = 37, resto 0
- 37 / 2 = 18, resto 1
- 18 / 2 = 9, resto 0
- 9 / 2 = 4, resto 1
- 4 / 2 = 2, resto 0
- 2 / 2 = 1, resto 0
- 1 / 2 = 0, resto 1

Scrivendo dal basso verso l'alto i resti otteniamo `1001 0100`, che è la rappresentazione binaria di 148.

##### Metodo dell'inglobamento delle potenze di 2
In questo metodo si considera la più grande potenza di 2 che può essere contenuta nel numero di partenza, e si considerano progressivamente le potenze di due più piccole. Ogni volta che la potenza attuale può essere contenuta nel numero, si segna un valore 1 e si sottrae al numero - altrimenti si segna uno 0. In questo caso il numero si legge progressivamente mentre viene creato (dall'alto verso il basso).

Convertiamo 148, la cui potenza di 2 più grande che può essere inizialmente contenuta è 128:
- $148 \ge 128$, quindi segnamo 1 e sottraiamo: $148 - 128 = 20$
- $20 < 64$, quindi segnamo 0
- $20 < 32$, quindi segnamo 0
- $20 \ge 16$, quindi segnamo 1 e sottraiamo: $20 - 16 = 4$
- $4 < 8$, quindi segnamo 0
- $4 \ge 4$, quindi segnamo 1 e sottraiamo: $4 - 4 = 0$
- $0 < 2$ e $0 < 1$, quindi per entrambi segnamo 0.

Abbiamo ottenuto `1001 0100`, che è la rappresentazione binaria di 148.

##### Metodo delle divisioni di potenze di 2 successive
In questo metodo si considerano le **divisioni del numero iniziale per le potenze di 2**, a partire dalla più grande potenza che può essere contenuta nel numero iniziale. Se il risultato è un numero *dispari* si segna un 1, altrimenti 0. In questo caso, il resto non viene considerato e si leggono i numeri segnati dall'alto verso il basso.

Convertiamo 148, la cui potenza di 2 più grande che può essere inizialmente contenuta è 128:
- 148 / 128 = 1; dispari, quindi segnamo 1
- 148 / 64 = 2; pari, quindi segnamo 0
- 148 / 32 = 4; pari, quindi segnamo 0
- 148 / 16 = 9; dispari, quindi segnamo 1
- 148 / 8 = 18; pari, quindi segnamo 0
- 148 / 4 = 37; dispari, quindi segnamo 1
- 148 / 2 = 74; pari, quindi segnamo 0
- 148 / 1 = 148; pari, quindi segnamo 0

Abbiamo ottenuto `1001 0100`, che è la rappresentazione binaria di 148.

#### Somma di numeri binari
In alcuni casi è necessario **sommare numeri binari**. Vediamo come fare con un esempio: sommiamo `0110` e `0111` (che hanno rispettivamente i valori decimali 6 e 7).
- Disporre i due numeri uno sopra l'altro:
```
0110 +
0111
----
```
- La somma funziona esattamente nello stesso modo di quella decimale, con l'eccezione che ci sono solo 4 possibilità di somma: 
	- `0 + 0 = 0`
	- `0 + 1 = 1 + 0 = 1` 
	- `1 + 1 = 0` con riporto di 1 nella colonna successiva.
```
0110 +
0111
----
1101
```
Il risultato è `1101` che è 13 in decimale (6 + 7 fa effettivamente 13).

#### Numeri con segno e **complemento a 2**
Negli esempi fatti fino ad ora abbiamo considerato ***numeri interi senza segno*** (*unsigned*). Vediamo ora come avviene la memorizzazione di ***numeri interi con segno*** (*signed*).

I numeri con segno vengono memorizzati con una tecnica chiamata ***complemento a 2*** - il bit ***più significativo*** viene usato per ***mantenere il segno*** del numero: se questo ha valore 0 significa che stiamo rappresentando un numero positivo (o 0) mentre se vale 1 significa che stiamo rappresentando un numero negativo.

I numeri *con segno* positivi si comportano in modo esatto a quelli *senza segno*, con il bit di segno a 0; i numeri *con segno* **negativi** vengono rappresentati ***come l'opposto bitwise dello stesso numero positivo più 1***.

#### Conversione *signed* da **decimale a binario**
Per convertire un numero *signed* da decimale a binario è necessario applicarvi il complemento a 2. Per prima cosa, è necessario trovare la rappresentazione *positiva* del numero da rappresentare, dopodichè vi si applica il *NOT bitwise* ed infine vi si aggiunge 1.

Prendiamo come esempio il numero -76:
- Consideriamo il numero positivo 76: `0100 1100`
- Applichiamo il *NOT bitwise*: `1011 0011`
- Aggiungiamo 1: `1011 0100`.
L'aggiunta dell'1 garantisce **che non vi siano due rappresentazioni del numero 0** (una positiva e una negativa).

#### Conversione *signed* da **binario a decimale**
Per convertire un numero *signed* da binario a decimale (quindi inizialmente rappresentato tramite complemento a 2) dobbiamo valutare **il bit di segno**:
- Se il bit di segno è a 0, convertire [[#Conversione *unsigned* **da binario a decimale**|come visto prima]].
- Se il bit di segno è 1, bisgona applicare *NOT bitwise* al numero, aggiungere 1, e poi convertire come visto prima. Il risultato ottenuto ***dovrà avere segno negativo***.

Convertiamo `1001 1110` da binario a decimale:
- Il bit di segno vale `1`, quindi il numero è negativo.
- Applichiamo *NOT bitwise* (`0110 0001`) e aggiungiamo 1 (`0110 0010`)
- Il numero ottenuto vale 98, quindi il risultato è -98 (in quanto il numero iniziale è negativo).

#### Perché il tipo di dato è importante
In memoria, la memorizzazione di un valore, per i [[4.1 ~ Introduction to fundamental data types#Insiemi di tipi di dato|tipi di dato integrali]], avviene in forma binaria. Supponiamo di voler memorizzare il numero `1011 0100` - come fa il linguaggio a capire se interpretarlo come un numero ***con o senza segno?***

A questo vengono in soccorso i ***tipi di dato***, in particolare ***[[4.4 ~ Signed integers|i tipi di dato signed]]*** e ***[[4.5 ~ Unsigned integers, and why to avoid them|i tipi di dato unsigned]]*** - grazie a questa differenza, richiediamo ***esplicitamente al compilatore*** di interpretare i valori contenuti nelle variabili **con o senza il complemento a 2**; in questo modo non si hanno ambiguità quando un numero deve essere convertito.

Se per esempio il numero `1011 0100` viene memorizzato in una variabile `unsigned int`, il complemento a 2 non verrà usato e quindi verrà interpretato come il valore decimale 180. Se però memorizziamo quello stesso valore in un `signed int` allora quel valore verrà interpretato come -76 a causa del complemento a 2.

