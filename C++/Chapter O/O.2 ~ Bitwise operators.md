C++ offre ***sei*** diversi operatori in grado di ***manipolare i bit***, questi chiamati ***operatori bitwise***.
Questi sono:
- `operator<<` - ***left shift***;
- `operator>>` - ***right shift***;
- `operator~` - ***bitwise NOT***;
- `operator&` - ***bitwise AND***;
- `operator|` - ***bitwise OR***;
- `operator^` - ***bitwise XOR***.
Tutti questi operatori sono ***[[6.2 ~ Arithmetic operators#Operatori *modificanti* e *non-modificanti*|non modificanti]]***.

Gli operatori *bitwise* sono definiti (e quindi funzionano correttamente) per [[4.1 ~ Introduction to fundamental data types#Tipi di dato fondamentali|tipi di dato integrali]] e [[O.1 ~ Bit flags and bit manipulation via stdbitset#*Bit manipulation* tramite `std bitset`|std::bitset]]. Negli esempi che faremo, verrà usato, per semplicità e per permettere una maggiore comprensione, `std::bitset`a `4` bit. 

È fortemente sconsigliato applicare gli operandi a tipi di dato *integral* ***con segno*** (come ad esempio [[4.4 ~ Signed integers|i signed integers]]), in quanto eventuali operazioni possono risultare in valori ***implementation defined***. Preferire, nella maggior parte dei casi, [[4.5 ~ Unsigned integers, and why to avoid them|unsigned integrals]] o `std::bitset`.

#### Gli operatori di *shift* binaro: `operator>>` e `operator<<`
##### *Left-Shifting* `operator<<`
L'operatore di *left shift binario* `operator<<` **sposta una sequenza di bit verso sinistra**. 
Il formato d'uso dell'operatore è `x << n`, dove:
- `x` è un'espressione che indica **la sequenza di bit su cui applicare lo *shift***;
- `n` è **un numero intero** che indica **di quanti bit effettuare lo *shift***.
Per esempio, dire `x << 2` significa "*produci una nuova sequenza di bit spostando verso sinistra i bit della sequenza `x` di `2` posizioni*".

L'operatore è *non modificante*, pertanto la sequenza che fa da operando sinistro **non viene modificata**. Nella sequenza prodotta, i nuovi bit aggiunti per permettere lo spostamento avranno valore `0`.

Esempi:
- `0011 << 1` produce `0110`;
- `0011 << 2` produce `1100`;
- `0011 << 3` produce `1000` - si noti che, in questo caso, abbiamo spostato un bit con valore `1` fuori dal numero - esso viene *perso per sempre*.

##### *Right-Shifting* `operator>>`
L'operatore di *left shift binario* `operator<<` **sposta una sequenza di bit verso destra**.
Il funzionamento e la sintassi sono uguali a quelle di `operator<<`, con la differenza dell'operatore e del risultato prodotto.

Esempi:
- `1100 >> 1` produce `0110`;
- `1100 >> 2` produce `0011`;
- `1100 >> 3` produce `0001` - anche in questo caso abbiamo spostato un bit con valore `1` fuori dal numero - esso viene *perso per sempre*.

#### `operator<<` e `operator>>` vengono usati per I/O
Prima di introdurli come operatori di *shift*, abbiamo visto `operator>>` ed `operator<<` usati per **effettuare input e output** tramite `std::cin` e `std::cout` (come visto [[1.5 ~ Introduction to iostream cout, cin, endl|qui]]).

Consideriamo il seguente codice:
```cpp
#include <iostream>
#include <bitset>

int main() {
	std::bitset<4> myBitSet { 0b0110 };
	myBitSet = myBitSet << 1; // operator<< per left-shift, diventa 1100
	std::cout << std::bitset<4> { myBitSet } << '\n'; // operator<< per output
	
	return 0;
}
```
Questo programma stampa, in modo corretto `1100`. Ma come è stato possibile capire il contesto in cui `operator<<` deve fare da *output* o da *shift-left*?
La risposta sta nel **tipo di dato dell'operando sinistro**:
- Se l'operando sinistro è un ***tipo integrale*** o un `std::bitset`, allora `operator<<` viene interpretato come ***shift-left***;
- Se l'operando sinistro è ***una stream di output*** come `std::cout`, allora `operator<<` viene interpretato come ***operatore di inserimento***.
Lo stesso discorso è applicabile a `operator>>`.

La capacità di un operatore di comportarsi in maniera diversa sulla base degli operandi che gli vengono forniti si chiama ***operator overloading***. Introdurremo questo concetto in lezioni future.

Se si vuole esplicitare, durante una stampa, il risultato di uno *shift-left* o *shift-right*, esso va **circondato da parentesi**.

```cpp
#include <bitset>
#include <iostream>

int main()
{
	std::bitset<4> x{ 0b0110 };

	std::cout << x << 1 << '\n'; 
	std::cout << (x << 1) << '\n';

	return 0;
```
Output:
```
01101
1100
```
Per ordine di precedenza, nel primo caso, `<<` viene interpretato ogni volta come *operatore di inserimento*, stampando `x` e poi il valore `1`; nel secondo caso, in quanto circondato da parentesi, viene valutato dapprima `x << 1` e poi stampato in output, stampando `1100`.

#### *Bitwise NOT* `operator~`
L'operatore di ***NOT bitwise*** `operator~` **inverte il valore dei bit da `0` a `1` e viceversa**.
Il formato d'uso è `~x`, dove `x` è un'espressione che indica **la sequenza di bit su cui applicare il NOT**.

Esempi:
- `~0011` produce `1100`
- `~0011 0101` produce `1100 1010`

#### *Bitwise OR* `operator|`
L'operatore di ***OR Bitwise*** `operator|` **effettua l'OR binario tra due sequenze di bit**.
Il formato d'uso è `x | y`, in cui sia `x` che `y` sono **espressioni che indicano sequenze di bit**.

Abbiamo visto [[6.8 ~ Logical operators|l'operatore di OR logico]], il quale restituisce `true` se almeno una di due condizioni risulta `true` (`1`), altrimenti restituisce `false` (`0`).
Se l'operatore di OR *logico* si applica su ***due condizioni booleane*** nella loro interezza, l'operatore di OR *bitwise* **viene applicato ad ogni coppia corrispondente di bit degli operandi**.

Vediamo un esempio: `0b0101 | 0b0110`:
```
0 1 0 1 OR
0 1 1 0
-------
0 1 1 1
```
Per ogni colonna si controlla se *ci sia almeno un valore `1`*, in caso affermativo il risultato per quella colonna è `1`, altrimenti è `0`. Gli operatori di *OR bitwise* possono essere **concatenati** con la stessa identica logica: ad esempio `0b0111 | 0b0011 | 0b0001`:
```
0 1 1 1 OR
0 0 1 1 OR
0 0 0 1
--------
0 1 1 1
```

#### *Bitwise AND* `operator&`
L'operatore di ***AND Bitwise*** `operator&` **effettua l'AND binario tra due sequenze di bit**. 
Il formato d'uso è `x & y`, in cui sia `x` che `y` sono **espressioni che indicano sequenze di bit**.

Questo operatore funziona in modo uguale al precedente, con la differenza che per ogni coppia di bit viene applicata la logica AND anzichè la logica OR: il risultato, per ogni coppia è `1` se entrambi i bit considerati hanno valore `1`, produce `0` altrimenti.

Vediamo un esempio: `0b0101 & 0b0110`:
```
0 1 0 1 AND
0 1 1 0
-------
0 1 0 0
```
In modo opposto a OR, per ogni colonna si controlla *se ci sia almeno un valore `0`*, in caso affermativo il risultato per quella colonna è `0`, altrimenti è `1`. Anche in questo caso gli operatori di *AND bitwise* possono essere **concatenati** seguendo la stessa identica logica:
```
0 1 1 1 AND
0 0 1 1 AND
0 0 0 1
--------
0 0 0 1
```

#### *Bitwise XOR* `operator^`
L'operatore di ***XOR Bitwise*** `operator&` **effettua lo XOR binario tra due sequenze di bit**. 
Il formato d'uso è `x ^ y`, in cui sia `x` che `y` sono **espressioni che indicano sequenze di bit**.

Per ogni coppia di bit corrispondenti viene applicata la logica XOR: se essi possiedono entrambi lo stesso valore, il risultato per quella coppia è `0`, altrimenti `1`.

Vediamo un esempio: `0b0101 ^ 0b0110`:
```
0 1 0 1 XOR
0 1 1 0
--------
0 0 1 1
```
Per ogni colonna si controlla se *c'è un numero pari di bit `1`*, in caso affermativo, il risultato per quella colonna è `0`, altrimenti è `1` . Anche in questo caso gli operatori di *XOR bitwise* possono essere **concatenati** seguendo la stessa identica logica:
```
0 0 0 1 XOR
0 0 1 1 XOR
0 1 1 1 
--------
0 1 0 1
```

#### Operatori di assegnazione *bitwise*
Oltre agli operatori *bitwise*, C++ fornisce ***cinque*** operatori di **assegnazione *bitwise***.
- `operator<<` - ***assegnazione left shift***;
- `operator>>` - ***assegnazione right shift***;
- `operator&` - ***assegnazione bitwise AND***;
- `operator|` - ***assegnazione bitwise OR***;
- `operator^` - ***assegnazione bitwise XOR***.
Questi operatori ***sono modificanti*** - in particolare viene cambiato il valore dell'operando sinistro.

Questi operatori permettono di scrivere in maniera più concisa **l'assegnazione di un risultato di un'operazione *bitwise*** allo stesso operando che vi viene coinvolto. Per esempio `x = x << 1` può essere scritto in maniera più concisa come `x <<= 1`.

Non esiste un operatore di assegnazione per il *NOT bitwise*, in quanto è un operatore unario. È però possibile eseguire l'assegnazione di un valore con i suoi bit invertiti in modo standard: `x = ~x`.

#### Gli operatori *bitwise* effettuano *integral promotion* su operandi più piccoli di `int`
Se uno degli operandi di un'operazione *bitwise* ha un tipo di dato ***più piccolo di `int`*** (per esempio `std::uint8_t`) allora dopo aver effettuato l'operazione **tale operando verrà convertito automaticamente in un `int` o in un `unsigned int`**.

Se per esempio abbiamo operandi ti tipo `unsigned short`, essi verranno convertiti a `unsigned int` e il risultato dell'operazione sarà un `unsigned int`.

Ci sono due casi, in particolare, a cui fare attenzione:
- `operator~` e `operator<<` sono *width-sensitive* - questo significa che, sulla base dell'operando, possono produrre risultati diversi
```cpp
std::uint8_t c { 0b00001100 };
std::bitset<32> test1 { ~c };
// Dovrebbe essere 00000000000000000000000000011110011
// Ma invece è 111111111111111111111111111110011
std::bitset<32> test2 { c << 6 };
// Dovrebbe essere 00000000000000000000000000000000000
// Ma invece è 00000000000000000000000001100000000
```
- Inizializzare una variabile di un tipo più piccolo di `int` con il risultato di un'operazione *bitwise* è una **narrowing conversion**. Questo non è permesso nelle [[1.4 ~ Variable assignment and initialization#List-initialization|list-initialization]], per cui potrebbe risultare in un errore di compilazione.
```cpp
std::uint8_t c { 0b00001100 };
std::uint8_t d { c << 2 }; // Errore: narrowing conversion non permesse.
```

Si possono ovviare a questi problemi utilizzando [[4.12 ~ Introduction to type conversion and static_cast|static_cast]], convertendo *esplicitamente* il risultato di un'operazione *bitwise* al tipo di dato *più piccolo* originale.
```cpp
std::uint8_t c { 0b00001100 };
std::bitset<32> test1 { static_cast<std::uint8_t>(~c) };
// test1 è corretto, vale 00000000000000000000000000011110011

std::bitset<32> test2 { static_cast<std::uint8_t>(c << 6) };
// test2 è corretto, vale 00000000000000000000000000000000000

std::uint8_t d { static_cast<std::uint8_t>(c << 2) }; // Nessun errore
```
Generalmente è *sconsigliato* l'uso di tipi di dato più piccoli di `int` per effettuare operazioni bitwise.


