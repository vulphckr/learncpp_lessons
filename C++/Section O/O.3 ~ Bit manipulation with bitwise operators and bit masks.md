Nella [[O.2 ~ Bitwise operators|scorsa lezione]] abbiamo introdotto i ***bitwise operators***, operatori con cui è possibile applicare operazioni logiche ai singoli bit di una determinata sequenza. Ora che ne conosciamo il funzionamento, vediamo le loro ***applicazioni principali***.

#### Le *bit masks*
Abbiamo visto che con `std::bitset` è possibile **manipolare i singoli bit**, tramite le sue apposite *[[O.1 ~ Bit flags and bit manipulation via stdbitset#Altre operazioni con `std bitset`|member functions]]*. Se però abbiamo un tipo di dato *integral*, abbiamo la necessità di **individuare i singoli bit che si vogliono modificare o testare** - purtroppo gli operatori *bitwise* **non possiedono il concetto di *posizione*** e hanno la necessità di usare delle ***bit masks***.

Una ***bit mask*** è una **sequenza di bit** che permette di ***selezionare esattamente quali bit verranno coinvolti in una determinata operazione bitwise***. 

Consideriamo un caso reale, in cui vogliamo verniciare una finestra - se non facciamo attenzione rischiamo di verniciare anche parte del vetro. Se però usiamo del nastro per coprire le zone della finestra che non vogliamo verniciare, quelle zone coperte non verranno verniciate. Alla fine avremmo verniciato solo le parti che non sono state coperte.
Una ***bit mask*** **svolge la stessa funzione del nastro** ma per i bit: permette di coinvolgere solo *determinati* bit in una certa operazione *bitwise*.
#### Definire le bit mask
Le più semplici bit mask definibili sono quelle che ***permettono la modifica di un singolo bit*** per ogni posizione possibile - queste possiedono `0` nelle posizioni che non vogliamo coinvolgere e `1` nelle posizioni che vogliamo coinvolgere.

Seppure possono essere *literal*, è sempre bene dare un nome alle bit mask - questo può essere fatto tramite **variabili costanti**, anche `constexpr`.

In C++14 possiamo esprimere le bitmask tramite ***binary literals***; tuttavia, nelle versioni precedenti come C++11, in cui i *binary literal* non erano presenti, è possibile utilizzare o *hexadecimal literals*, oppure tramite *left shift* di un singolo bit alla posizione desiderata:
```cpp
// Tramite binary literals (C++14)
constexpr std::uint8_t mask0b{ 0b0000'0001 }; // rappresenta bit 0
constexpr std::uint8_t mask1b{ 0b0000'0010 }; // rappresenta bit 1
constexpr std::uint8_t mask2b{ 0b0000'0100 }; // rappresenta bit 2
constexpr std::uint8_t mask3b{ 0b0000'1000 }; // rappresenta bit 3
constexpr std::uint8_t mask4b{ 0b0001'0000 }; // rappresenta bit 4
constexpr std::uint8_t mask5b{ 0b0010'0000 }; // rappresenta bit 5
constexpr std::uint8_t mask6b{ 0b0100'0000 }; // rappresenta bit 6
constexpr std::uint8_t mask7b{ 0b1000'0000 }; // rappresenta bit 7

// Tramite hex literals (C++11)
constexpr std::uint8_t mask0h{ 0x01 }; // hex for 0000 0001
constexpr std::uint8_t mask1h{ 0x02 }; // hex for 0000 0010
constexpr std::uint8_t mask2h{ 0x04 }; // hex for 0000 0100
constexpr std::uint8_t mask3h{ 0x08 }; // hex for 0000 1000
constexpr std::uint8_t mask4h{ 0x10 }; // hex for 0001 0000
constexpr std::uint8_t mask5h{ 0x20 }; // hex for 0010 0000
constexpr std::uint8_t mask6h{ 0x40 }; // hex for 0100 0000
constexpr std::uint8_t mask7h{ 0x80 }; // hex for 1000 0000

// Left shifting di un singolo valore alla posizione desiderata (C/C++)
constexpr std::uint8_t mask0ls{ 1 << 0 }; // 0000 0001
constexpr std::uint8_t mask1ls{ 1 << 1 }; // 0000 0010
constexpr std::uint8_t mask2ls{ 1 << 2 }; // 0000 0100
constexpr std::uint8_t mask3ls{ 1 << 3 }; // 0000 1000
constexpr std::uint8_t mask4ls{ 1 << 4 }; // 0001 0000
constexpr std::uint8_t mask5ls{ 1 << 5 }; // 0010 0000
constexpr std::uint8_t mask6ls{ 1 << 6 }; // 0100 0000
constexpr std::uint8_t mask7ls{ 1 << 7 }; // 1000 0000
```

Ora abbiamo un insieme di costanti, ciascuna rappresentante una *bit mask* per una determinata posizione.

#### Testing di un bit (controllo del valore, se `1` o `0`)
Ora che abbiamo le bit mask possiamo usarle assieme alle nostre sequenze di bit (*bit flags*) per effettuare manipolazioni in modo preciso.

Per controllare se un bit a una certa posizione ha valore `1` oppure `0`, possiamo usare l'operazione di ***AND Bitwise*** tramite `operator&` tra la nostra sequenza e la bit mask corrispondente alla posizione da controllare.

```cpp
#include <cstdint>
#include <iostream>

int main()
{
	[[maybe_unused]] constexpr std::uint8_t mask0{ 0b0000'0001 }; // bit 0
	[[maybe_unused]] constexpr std::uint8_t mask1{ 0b0000'0010 }; // bit 1
	[[maybe_unused]] constexpr std::uint8_t mask2{ 0b0000'0100 }; // bit 2
	[[maybe_unused]] constexpr std::uint8_t mask3{ 0b0000'1000 }; // bit 3
	[[maybe_unused]] constexpr std::uint8_t mask4{ 0b0001'0000 }; // bit 4
	[[maybe_unused]] constexpr std::uint8_t mask5{ 0b0010'0000 }; // bit 5
	[[maybe_unused]] constexpr std::uint8_t mask6{ 0b0100'0000 }; // bit 6
	[[maybe_unused]] constexpr std::uint8_t mask7{ 0b1000'0000 }; // bit 7

	std::uint8_t flags{ 0b0000'0101 }; // 8 bit di grandezza = 8 flag diverse

	std::cout << "Il bit alla posizione 0 vale " 
		<< (static_cast<bool>(flags & mask0) ? "1\n" : "0\n");
	std::cout << "Il bit alla posizione 1 vale " 
		<< (static_cast<bool>(flags & mask1) ? "1\n" : "0\n");

	return 0;
}
```
Output:
```
Il bit alla posizione 0 vale 1
Il bit alla posizione 1 vale 0
```

Vediamo il funzionamento: `flags & mask0` significa fare la seguente operazione:
```
0000'0101 AND
0000'0001
---------
0000'0001
```
Usiamo poi [[4.12 ~ Introduction to type conversion and static_cast|static_cast]] per convertire il valore `0000'0001` a un `bool`. Ogni valore diverso da `0` viene convertito in `true`, pertanto l'espressione viene valutata a `true`.

Nel caso di `flags & mask1` si ha:
```
0000'0101 AND
0000'0010
---------
0000'0000
```
Usiamo anche in questo caso `static_cast` per la conversione a `bool`. In questo caso il valore da convertire è `0`, quindi l'espressione viene valutata `false`. 

#### Impostare il valore di un bit a `1`
Per impostare il valore di un bit, posto in una specifica posizione, al valore `1` possiamo usare una operazione di ***assegnazione OR Bitwise*** tramite `operator|=` tra la nostra sequenza di bit e la bit mask corrispondente alla posizione da modificare.
```cpp
#include <cstdint>
#include <iostream>

int main()
{
	[[maybe_unused]] constexpr std::uint8_t mask0{ 0b0000'0001 }; // bit 0
	[[maybe_unused]] constexpr std::uint8_t mask1{ 0b0000'0010 }; // bit 1
	[[maybe_unused]] constexpr std::uint8_t mask2{ 0b0000'0100 }; // bit 2
	[[maybe_unused]] constexpr std::uint8_t mask3{ 0b0000'1000 }; // bit 3
	[[maybe_unused]] constexpr std::uint8_t mask4{ 0b0001'0000 }; // bit 4
	[[maybe_unused]] constexpr std::uint8_t mask5{ 0b0010'0000 }; // bit 5
	[[maybe_unused]] constexpr std::uint8_t mask6{ 0b0100'0000 }; // bit 6
	[[maybe_unused]] constexpr std::uint8_t mask7{ 0b1000'0000 }; // bit 7

	std::uint8_t flags{ 0b0000'0101 }; // 8 bit di grandezza = 8 flag diverse

	std::cout << "Il bit alla posizione 1 vale " 
		<< (static_cast<bool>(flags & mask1) ? "1\n" : "0\n");
		
	flags |= mask1; // Imposto a 1 il bit alla posizione 1.
		
	std::cout << "Il bit alla posizione 1 vale " 
		<< (static_cast<bool>(flags & mask1) ? "1\n" : "0\n");

	return 0;
}
```
Output:
```
Il bit alla posizione 1 vale 0
Il bit alla posizione 1 vale 1
```

È possibile anche impostare a `1` più bit contemporaneamente, usando una bit mask formata da più bit mask in *OR bitwise* tra loro:
```cpp
flags |= (mask4 | mask5); // Imposto a 1 i bit alle posizioni 4 e 5.
```

#### Impostare il valore di un bit a `0`
Per impostare il valore di un bit, posto in una specifica posizione, al valore `0` possiamo usare una operazione di ***assegnazione AND Bitwise*** tramite `operator&=` tra la nostra sequenza di bit e **l'inverso della bit mask corrispondente alla posizione da modificare** (ossia *NOT bit mask*).

```cpp
#include <cstdint>
#include <iostream>

int main()
{
	[[maybe_unused]] constexpr std::uint8_t mask0{ 0b0000'0001 }; // bit 0
	[[maybe_unused]] constexpr std::uint8_t mask1{ 0b0000'0010 }; // bit 1
	[[maybe_unused]] constexpr std::uint8_t mask2{ 0b0000'0100 }; // bit 2
	[[maybe_unused]] constexpr std::uint8_t mask3{ 0b0000'1000 }; // bit 3
	[[maybe_unused]] constexpr std::uint8_t mask4{ 0b0001'0000 }; // bit 4
	[[maybe_unused]] constexpr std::uint8_t mask5{ 0b0010'0000 }; // bit 5
	[[maybe_unused]] constexpr std::uint8_t mask6{ 0b0100'0000 }; // bit 6
	[[maybe_unused]] constexpr std::uint8_t mask7{ 0b1000'0000 }; // bit 7

	std::uint8_t flags{ 0b0000'0010 }; // 8 bit di grandezza = 8 flag diverse

	std::cout << "Il bit alla posizione 1 vale " 
		<< (static_cast<bool>(flags & mask1) ? "1\n" : "0\n");
		
	flags &= ~mask1; // Imposto a 0 il bit alla posizione 1.
		
	std::cout << "Il bit alla posizione 1 vale " 
		<< (static_cast<bool>(flags & mask1) ? "1\n" : "0\n");

	return 0;
}
```
Output:
```
Il bit alla posizione 1 vale 0
Il bit alla posizione 1 vale 1
```
È possibile anche impostare a `0` più bit contemporaneamente, usando una bit mask formata da più bit mask in *OR bitwise* tra loro:
```cpp
flags &= ~(mask4 | mask5); // Imposto a 0 i bit alle posizioni 4 e 5.
```

> [!warning]
> A causa della *integer promotion* vista [[O.2 ~ Bitwise operators#Gli operatori *bitwise* effettuano *integral promotion* su operandi più piccoli di `int`|nella scorsa lezione]] è opportuno castare la bitmask al tipo di dato dell'operando a sinistra, nel caso esso abbia tipo di dato più piccolo di `int`.
> ```cpp
> std::uint8_t flags{ 0b0000'0010 }; // tipo di dato più piccolo di int
flags &= static_cast<std::uint8_t>(~mask2); // OK: nessun comportamento imprevisto
> ```

#### Invertire un bit
Per invertire il valore di un bit posto in una specifica posizione dal valore `0` al valore `1` e viceversa possiamo usare una operazione di ***assegnazione XOR Bitwise*** tramite `operator^=` tra la nostra sequenza di bit e la bitmask relativa alla posizione da modificare.
```cpp
#include <cstdint>
#include <iostream>

int main()
{
	[[maybe_unused]] constexpr std::uint8_t mask0{ 0b0000'0001 }; // bit 0
	[[maybe_unused]] constexpr std::uint8_t mask1{ 0b0000'0010 }; // bit 1
	[[maybe_unused]] constexpr std::uint8_t mask2{ 0b0000'0100 }; // bit 2
	[[maybe_unused]] constexpr std::uint8_t mask3{ 0b0000'1000 }; // bit 3
	[[maybe_unused]] constexpr std::uint8_t mask4{ 0b0001'0000 }; // bit 4
	[[maybe_unused]] constexpr std::uint8_t mask5{ 0b0010'0000 }; // bit 5
	[[maybe_unused]] constexpr std::uint8_t mask6{ 0b0100'0000 }; // bit 6
	[[maybe_unused]] constexpr std::uint8_t mask7{ 0b1000'0000 }; // bit 7

	std::uint8_t flags{ 0b0000'0010 }; // 8 bit di grandezza = 8 flag diverse

	std::cout << "Il bit alla posizione 1 vale " 
		<< (static_cast<bool>(flags & mask1) ? "1\n" : "0\n");
		
	flags ^= mask1; // Inverto il bit alla posizione 1.
		
	std::cout << "Il bit alla posizione 1 vale " 
		<< (static_cast<bool>(flags & mask1) ? "1\n" : "0\n");
	
	flags ^= mask1; // Inverto di nuovo il bit alla posizione 1.
		
	std::cout << "Il bit alla posizione 1 vale " 
		<< (static_cast<bool>(flags & mask1) ? "1\n" : "0\n");

	return 0;
}
```
Output:
```
Il bit alla posizione 1 vale 1
Il bit alla posizione 1 vale 0
Il bit alla posizione 1 vale 1
```
Anche in questo caso è possibile invertire più bit contemporaneamente, usando una bit mask formata da più bit mask in *OR bitwise* tra loro:
```cpp
flags ^= (mask4 | mask5); // Inverto il valore dei bit alle posizioni 4 e 5.
```

#### Uso di bit mask con `std::bitset` 
Gli `std::bitset` **possono usare senza problemi tutte le operazioni bitwise** (posto che gli operandi siano entrambi `std::bitset`), pertanto possono anche fare uso delle bit mask anche se possiedono le loro *member functions*.
Perchè fare ciò? Perchè le bit mask, a differenza delle *member functions*, possono modificare **più bit alla volta**.

#### Dare contesto alle bit mask
È opportuno chiamare le *variabili costanti* che contengono le bitmask con dei **nomi simbolici** aventi significato nel contesto del programma. In questo modo sono più facili da contestualizzare e anche da usare nel programma. 

```cpp
#include <bitset>
#include <iostream>

int main()
{
        // Define a bunch of physical/emotional states
	[[maybe_unused]] constexpr std::bitset<8> isHungry   { 0b0000'0001 };
	[[maybe_unused]] constexpr std::bitset<8> isSad      { 0b0000'0010 };
	[[maybe_unused]] constexpr std::bitset<8> isMad      { 0b0000'0100 };
	[[maybe_unused]] constexpr std::bitset<8> isHappy    { 0b0000'1000 };
	[[maybe_unused]] constexpr std::bitset<8> isLaughing { 0b0001'0000 };
	[[maybe_unused]] constexpr std::bitset<8> isAsleep   { 0b0010'0000 };
	[[maybe_unused]] constexpr std::bitset<8> isDead     { 0b0100'0000 };
	[[maybe_unused]] constexpr std::bitset<8> isCrying   { 0b1000'0000 };


	std::bitset<8> me{}; // all flags/options turned off to start
	me |= (isHappy | isLaughing); // I am happy and laughing
	me &= ~isLaughing; // I am no longer laughing

	// Query a few states (we use the any() function to see if any bits remain set)
	std::cout << std::boolalpha; // print true or false instead of 1 or 0
	std::cout << "I am happy? " << (me & isHappy).any() << '\n';
	std::cout << "I am laughing? " << (me & isLaughing).any() << '\n';

	return 0;
}
```
#### Contesti in cui le bit mask sono utili
In tutti gli esempi appena fatti, stiamo dichiarando **9 variabili diverse**: 8 costanti, che memorizzano le bit mask, mentre una che contiene la sequenza di bit. In un contesto dove vogliamo risparmiare memoria, **stiamo invece occupando 9 byte!**

L'uso di bit mask risulta molto efficiente nel caso dobbiamo operare con *bit flags **tutte uguali***. Supponiamo che, nell'esempio appena fatto, al posto di avere una sola persona ne avessimo 100, tutte uguali. Se dovessimo associare 8 valori booleani a ciascuna delle 100 persone, avremmo ***800 byte occupati*** - se invece dichiariamo 8 *bit mask* e 100 *bit flags* tutte uguali, abbiamo occupato solamente ***108 byte*** - un risparmio di quasi 8 volte la memoria.

Un altro caso in cui le *bit mask* si prestano molto bene è per **specificare delle opzioni di una funzione**. Supponiamo di avere una funzione che può assumere una combinazione di 32 opzioni diverse, tutte attivabili o meno:
```cpp
void someFunction(bool option1, bool option2, bool option3, bool option4, bool option5, bool option6, bool option7, bool option8, bool option9, bool option10, bool option11, bool option12, bool option13, bool option14, bool option15, bool option16, bool option17, bool option18, bool option19, bool option20, bool option21, bool option22, bool option23, bool option24, bool option25, bool option26, bool option27, bool option28, bool option29, bool option30, bool option31, bool option32);
```
*Sperando di aver dato contesto a ciascuna delle opzioni*, ipotizziamo di invocare la funzione con le opzioni 10 e 32 attivate - la chiamata risulterà della forma:
```cpp
someFunction(false, false, false, false, false, false, false, false, false, true, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, true);
```
Oltre ad essere terribilmente difficile da leggere, è necessario anche ricordarsi a cosa è associata ciascuna opzione, causando una grande perdita di tempo.

Possiamo invece definire la stessa funzione che accetta come parametro una `std::bitset`:
```cpp
void someFunction(std::bitset<32> options);
```
E possiamo usare delle *bit mask* concatenate con *OR bitwise* come parametri, in modo da attivare i parametri che ci interessano in modo contestuale:
```cpp
someFunction(option10 | option32);
```
Questa forma è molto più semplice da leggere e decodificare - essa viene usata anche in OpenGL per specificare delle opzioni per alcune delle funzioni.
```cpp
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); // clear the color and the depth buffer
```
Dove tali flag sono specificate in un file a parte:
```cpp
#define GL_DEPTH_BUFFER_BIT               0x00000100
#define GL_STENCIL_BUFFER_BIT             0x00000400
#define GL_COLOR_BUFFER_BIT               0x00004000
```

#### Bit mask a multipli valori
Seppure nella maggior parte dei casi le *bit mask* vengono usate per selezionare un solo bit, è possibile creare delle bit mask che **selezionano più bit contemporaneamente**. 

Possiamo usare come esempio **lo standard RGBA** per i colori.
I pixel di televisori e monitor mostrano i colori combinando luce *rossa, verde e blu* (RGB). Sulla base dell'intensità di ciascuna di queste tre luci è possibile ottenere una vasta gamma di colori.

Tipicamente l'intensità per ciascuno dei tre colori viene rappresentata da **un `unsigned int` a 8 bit**, ossia un numero che va da 0 a 255. Se un pixel è rosso, allora `R=255,G=0,B=0`; se è viola, allora `R=255,G=0,B=255`; se è grigio, allora `R=127,G=127,B=127`.

Oltre all'intensità delle luci, è presente un ulteriore valore, chiamato `A` (*alpha*), anch'esso un `unsigned int` a 8 bit, che rappresenta **la trasparenza del colore**. Se `A` vale 0, il colore è trasparente, mentre se vale 255 è opaco.

Solitamente un colore RGBA viene memorizzato nella sua interezza **in un intero a 32 bit**, dove:
- I bit nelle posizioni 31-24 rappresentano il rosso (R);
- I bit nelle posizioni 23-16 rappresentano il verde (G);
- I bit nelle posizioni 15-8 rappresentano il blu (B);
- I bit nelle posizioni 0-7 rappresentano la trasparenza (A).
```
RRRRRRRR GGGGGGGG BBBBBBBB AAAAAAAA
```
In questo caso è opportuno creare delle bit mask in cui **i bit nelle posizioni corrispondenti a ciascun colore sono impostati a `1`**.
```cpp
	constexpr std::uint32_t redBits{ 0xFF000000 }; // 24-31
	constexpr std::uint32_t greenBits{ 0x00FF0000 }; // 16-23
	constexpr std::uint32_t blueBits{ 0x0000FF00 }; // 8-15
	constexpr std::uint32_t alphaBits{ 0x000000FF }; // 0-7
```