---
aliases:
  - O.1 ~ Bit flags and bit manipulation via std::bitset
---
Nei calcolatori moderni, la più piccola quantità di informazione **che è possibile indirizzare** è il ***byte***, formato da **8 bit**. Per la maggior parte dei tipi di dato, questo non rappresenta un problema; tuttavia, per quanto riguarda i [[4.9 ~ Boolean values|valori booleani]], siccome essi possono essere rappresentabili tramite **un singolo bit**, ***i restanti 7 rimangono inutilizzati***, sprecando spazio utile.

Anche se questo potrebbe sembrare un problema grave, il risparmio di *soli 7 bit* rappresenta un problema **solo in contesti in cui la memoria va maneggiata attentamente** in quanto molto limitata (*come per esempio in sistemi embedded o microprocessori*) - in questi contesti è possibile ***raggruppare 8 valori booleani in un solo byte***, così da non rendere parte del byte inutilizzabile.

Per poter controllare i valori di queste variabili una volta raggruppate è necessario ***manipolare i singoli bit che formano il byte*** - in questo modo ciascun bit rappresenta una variabile indipendente dalle altre e non viene sprecato spazio.

C++ *fornisce gli strumenti necessari* per effettuare queste modifiche ai *singoli bit* - rendendo possibile il processo di ***bit manipulation***.

#### *Bit flags*
Abbiamo sempre considerato le **variabili** come **contenitori per *singoli* valori** di un determinato tipo. Tuttavia, invece di considerare **il singolo valore** che tale variabile memorizza, è possibile considerare ***i singoli bit* che compongono tale valore**, ciascuno corrispondente ad un *valore booleano*.

Quando viene effettuata questa considerazione, i *singoli bit* prendono il nome di ***bit flags***.
Un bit flag può avere il valore `0` (*false, off, not set*) o `1` (*true, on, set*). Il processo di cambiare il valore di una *flag* da `0` a `1` o viceversa viene detto ***flipping*** o ***inversion***.

> [!note] *flag*
> In generale, in informatica una ***flag*** è un valore booleano che ***segnala che una certa condizione all'interno di un certo sistema è o meno verificata***.

Per definire un insieme di *bit flags* vengono tipicamente usate due alternative:
- Un [[4.5 ~ Unsigned integers, and why to avoid them|unsigned integer]] di dimensione appropriata (8, 16, 32 o 64 bit, i quali possono essere forzati tramite [[4.6 ~ Fixed-width integers and size_t|un tipo di dato a grandezza fissa]]);
- Una variabile di tipo `std::bitset` ([[5.3 ~ Numeral Systems (decimal, binary, hexadecimal, and octal)|già vista qui]]).
In questa lezione vedremo la seconda opzione, in quanto la più semplice da usare.

#### Come numerare i bit
Ogni **sequenza** di bit viene numerata ***da destra verso sinistra*** - il bit più a destra ha ***posizione*** 0. Nel caso di un *byte*, quindi:
```
Posizione: 76543210
```
Prendendo per esempio la sequenza `0000 0101`, i bit alle posizioni 0 e 2 hanno valore `1`, mentre quelli nelle posizioni rimanenti hanno valore `0`. 

#### *Bit manipulation* tramite `std::bitset`
[[5.3 ~ Numeral Systems (decimal, binary, hexadecimal, and octal)#Stampare numeri in base 2|Abbiamo introdotto]], nella lezione per visualizzare i numeri in base 2 anzichè in base 10, il tipo di dato `std::bitset`. Esso si trova nell'header `<bitset>`. 
Una variabile di questo tipo va inizializzata specificandone la lunghezza tramite una **costante *compile-time*** posta tra parentesi angolari `<>`.
```cpp
#include <bitset>

int main() {
	std::bitset<8> myBitSet {}; // Inizializzo un std::bitset di 8 bit.
	return 0;
}
```
Oltre a permettere la stampa di valori numerici in base 2, `std::bitset` possiede **4 *member functions*** che rendono facile la *bit manipulation*:
- `test(<pos>)` - **restituisce il valore del bit** alla posizione `pos`;
- `set(<pos>)` - **imposta a `1`** il bit alla posizione `pos` - non fa nulla se tale bit ha già il valore `1`;
- `reset(<pos>)` - **imposta a `0`** il bit alla posizione `pos` - non fa nulla se tale bit ha già il valore `0`;
- `flip(<pos>)` - **inverte il valore** del bit alla posizione `pos`, da `0` a `1` o viceversa.

```cpp
#include <bitset>
#include <iostream>

int main()
{
    std::bitset<8> bits{ 0b0000'0101 }; // 8 bit, inizializzo a 0000 0101
    bits.set(3); // Imposto a 1 il bit alla posizione 3 (0000 1101)
    bits.flip(4); // Inverto il bit alla posizione 4 (0001 1101)
    bits.reset(4); // Imposto a 0 il bit alla posizione 4 (0000 1101)

    std::cout << "Sequenza: " << bits << '\n';
    std::cout << "Il bit 3 ha valore: " << bits.test(3) << '\n';
    std::cout << "Il bit 4 ha valore: " << bits.test(4) << '\n';

    return 0;
}
```
Output:
```
Sequenza: 00001101
Il bit 3 ha valore: 1
Il bit 4 ha valore: 0
```

È possibile semplificare ancora più il valore semplicemente *dando un nome a ogni bit che viene usato tramite una apposita **constant variable***.
```cpp
#include <bitset>
#include <iostream>

int main()
{
    [[maybe_unused]] constexpr int  isHungry   { 0 };
    [[maybe_unused]] constexpr int  isSad      { 1 };
    [[maybe_unused]] constexpr int  isMad      { 2 };
    [[maybe_unused]] constexpr int  isHappy    { 3 };
    [[maybe_unused]] constexpr int  isLaughing { 4 };
    [[maybe_unused]] constexpr int  isAsleep   { 5 };
    [[maybe_unused]] constexpr int  isDead     { 6 };
    [[maybe_unused]] constexpr int  isCrying   { 7 };

    std::bitset<8> me{ 0b0000'0101 };
    me.set(isHappy);
    me.flip(isLaughing);
    me.reset(isLaughing);

    std::cout << "All the bits: " << me << '\n';
    std::cout << "I am happy: " << me.test(isHappy) << '\n';
    std::cout << "I am laughing: " << me.test(isLaughing) << '\n';

    return 0;
}
```
Abbiamo visto `[[maybe_unused]]` in una delle [[1.4 ~ Variable assignment and initialization#Variabili non usate|prime lezioni]].

#### Svantaggi di `std::bitset`
`std::bitset` ha due principali svantaggi: il primo è che non rende possibile **cambiare più di un bit alla volta**; per fare ciò non possiamo affidarci a questo tipo di dato, ma dobbiamo fare uso di metodi più tradizionali (manipolazione dei bit direttamente sugli `unsigned int`).

L'altro svantaggio è legato alla ***dimensione in memoria di un `std::bitset`***. Questo tipo di dato è infatti ottimizzato **per l'efficienza** nelle operazioni, ma non per la sua memorizzazione. La dimensione di un `std::bitset` è tipicamente ***il numero di byte necessari a memorizzare tutti i bit, arrotondato al valore più vicino di `sizeof(size_t)`***, che vale **4 byte** nelle architetture a 32-bit e **8 byte** nelle architetture a 64-bit.
Quindi se vogliamo memorizzare 8 bit con `std::bitset`, finiremo comunque per occupare **4 o 8 byte** di memoria. L'uso di `std::bitset` **non è dunque pensato per il risparmio della memoria**, quanto più per **la convenienza delle operazioni**.

#### Altre operazioni con `std::bitset`
`std::bitset` mette a disposizione anche altre *member functions* utili per controllare lo stato di ciò che è memorizzato:
- `size()` restituisce **la grandezza del bitset**, ossia quanti bit sta memorizzando;
- `count()` restituisce **il numero di bit che hanno valore `1`**;
- `all()` restituisce `true` **se tutti i bit del bitset hanno valore `1`**, `false` altrimenti;
- `any()` restituisce `true` se **anche solo un bit del bitset ha valore `1`**, `false` altrimenti;
- `none()` restituisce `true` **se non sono presenti bit di valore `1` nel bitset**, `false` altrimenti.

