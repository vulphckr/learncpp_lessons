---
sticker: lucide//settings
---
## Basi di Makefile
### Esempo base di `Makefile`
Per realizzare un esempio di Makefile, inserire all'interno di un file chiamato `Makefile` il seguente codice:
```make
hello:
	echo "Hello, world!"
```

>[!warning] Le indentazioni vanno fatte per mezzo di TAB, oppure `make` fallirà.

Ora, se ci troviamo nella directory contenente il file `Makefile` e usiamo `make` otterremo il seguente output:

```
❯ make  
echo "Hello, world!"  
Hello, world!
```

### Sintassi di Makefile
Un `Makefile` consiste di un ***insieme di regole***. Ogni regola presente nel file sarà così fatta:
```
targets: prerequisites
	command
	command
	command
```
- I `targets` sono ***nomi di files***, separati da spazi. Solitamente è presente un nome di file per regola - questi sono gli obiettivi, ossia ***la cosa da ricavare***;
- Ogni `command` è una serie di passaggi che va realizzata per **ricavare il target**. Questi devono essere preceduti da un carattere TAB.
- I `prerequisites` sono **anch'essi nomi di files**, separati da spazi. Questi file ***devono esistere prima che i comandi contenuti nella regola possano essere avviati***. Questi sono anche chiamati ***dependencies***.

### L'essenza di Makefile
Usiamo l'esempio iniziale:
```
hello:
	echo "Hello, world!"
	echo "Questa linea verrà stampata se il file hello non esiste."
```
In questo esempio abbiamo:
- Un target chiamato `hello`;
- Due `command` che stampano frasi diverse;
- Nessun prerequisiti.
Quando scriviamo `make hello`:
- I comandi verranno eseguiti **se e solo se `hello` non esiste**.
- Se `hello` esiste, i comandi **non verranno eseguiti**.
Solitamente i target hanno nomi di file, questo perché nei comandi usati per la creazione del target c'è la creazione di un file che ha proprio come nome il target - questo non è sempre vero; per esempio, in questo caso, un file chiamato `hello` **non verrà creato**.

Vediamo ora un esempio in cui questo succede, ossia la compilazione di un file in C++. Supponiamo che, nella nostra *working directory* abbiamo il seguente file C++:
```cpp
// esempio.cpp
int main() {
	return 0;
}
```
e che il nostro `Makefile` sia così formato:
```
esempio:
	g++ esempio.cpp -o esempio
```
E scriviamo il comando `make`. 
- Siccome, inizialmente, il file `esempio` non esiste, i comandi presenti all'interno del `Makefile` verranno eseguiti e tale file verrà compilato da `esempio.cpp`. Nella nostra directory c'è ora un file chiamato `esempio`.
- Se scriviamo nuovamente il comando `make` i `command` presenti all'interno del `Makefile` ***non verranno eseguiti***: questo perché il file `esempio` è già presente. 
Supponiamo ora di fare delle modifiche al codice sorgente `esempio.cpp` e, dopo aver finito, di eseguire di nuovo il comando `make` - ancora una volta i `command` presenti non verranno eseguiti, proprio perché il file `esempio` è presente nella directory. Modifichiamo quindi il `Makefile` aggiungendo dei prerequisiti:
```
esempio: esempio.cpp
	g++ esempio.cpp -o esempio
```
All'esecuzione del comando `make` succedono ora le seguenti cose;
- **Siccome il primo target all'interno di un `Makefile` è per default il primo target da eseguire**, verrà scelto come target proprio `esempio`;
- Tale target ha un prerequisito, il quale è `esempio.cpp`.
- I comandi presenti all'interno del target verranno eseguiti **se il file corrispondente al target *non esiste* o *se i prerequisiti sono più nuovi del target***.
Per determinare se un file è "più nuovo" di un altro si fanno ricorso ai *timestamp* dati dal sistema operativo. Cambiare i *timestamp* dei file significa modificarne una possibile interpretazione di `make`, quindi prestare attenzione.
Quando un target viene eseguito, esso viene considerato ***fatto*** e quindi non più considerato all'interno del processo di `make`.
> [!warning] Se abbiamo un target con dei prerequisiti (o meno) i comandi presenti all'interno del target verranno elaborati nel seguente modo:
> - Vengono elaborati i prerequisiti: se questi esistono, allora si ritengono ***soddisfatti***, altrimenti ***viene messa "in pausa" la regola*** e viene cercato il target relativo a tale prerequisito nel `Makefile` e si procede analogamente.
> - Se i prerequisiti della regola sono **soddisfatti**, allora essa viene eseguita ***se e solo se il target non esiste o è più vecchio dei suoi prerequisiti***.

#### Semplici esempi pratici

```
blah: blah.o
	cc blah.o -o blah # Runs third

blah.o: blah.c
	cc -c blah.c -o blah.o # Runs second

# Typically blah.c would already exist, but I want to limit any additional required files
blah.c:
	echo "int main() { return 0; }" > blah.c # Runs first
```
In questo esempio, dopo aver scritto `make`:
- Si prende `blah` come target, ossia il primo target che compare nel file;
- Questo ha `blah.o` come prerequisito. Viene messa in pausa questa regola e viene cercata nel file la regola che ha come target `blah.o`;
- Il target `blah.o` ha come prerequisito `blah.c`. Viene messa in pausa questa regola e viene cercata nel file la regola che ha come target `blah.c`;
- Il target `blah.c` non ha prerequisiti e quindi i comandi presenti all'interno di tale regola vengono eseguiti;
- A questo punto, siccome il prerequisitodi `blah.o` è soddisfatto, viene eseguita la regola che ha come target `blah.o`;
- Infine, siccome il prerequisito di `blah` è soddisfatto, viene eseguita la regola che ha come target `blah`
Risultato: un programma C chiamato `blah`, un file oggetto `blah.o` e un file sorgente `blah.c`. All'esecuzione di `make`:
- Eliminando `blah.c` tutte e tre le regole verranno eseguite nuovamente;
- Modificando `blah.c` solo le prime due regole verranno eseguite nuovamente;
- Ricreando personalmente `blah.o` verrà eseguita solo la prima regola;
- Non cambiando niente, non verrà eseguita nessuna regola.
Vediamo anche quest'ultimo esempio:
```
some_file: other_file
	echo "This will always run, and runs second"
	touch some_file

other_file:
	echo "This will always run, and runs first"
```
All'esecuzione di `make`:
- Viene scelto `some_file` come primo target, siccome default. Esso ha un prerequisito, `other_file`; la regola viene messa in pausa e viene cercato il target `other_file`;
- Il target `other_file` non ha prerequisiti e viene eseguita la regola ad esso associata;
- A questo punto il target `some_file` ha soddisfatto il suo prerequisito, pertanto viene eseguita la regola ad esso associata. In questo caso la regola viene eseguita perché `some_file` non esiste e `other_file` sarà eseguito sempre prima di `some_file`.

### Make Clean
Molto spesso si usa `clean` come target per specificare una regola che ***"ripulisce il progetto"*** rimuovendo l'output di ogni regola. Per esempio, nel seguente `Makefile`:
```
some_file: 
	touch some_file

clean:
	rm -f some_file
```
Il target `some_file` crea un file chiamato `some_file` - il target `clean` rimuove invece tale file.
Il target `clean` introduce altre due nuovi canoni:
- Siccome non è il primo target e non ha `prerequisite\s` esso **non verrà mai eseguito** se non esemplicitamente tramite `make clean`;
- `clean` non è inteso come un nome di un file; se avessimo un file chiamato `clean` questo target non funzionerebbe.
> [!tip] Usa il target `clean` per "ripulire il progetto", ossia per permettere la rigenerazione dei target finali.
### Variabili
Le variabili di `make` sono ***solamente stringhe***. Esse vengono inizializzate per mezzo dell'operatore `:=` o `=` (meglio il primo). Un esempio di uso di variabili è:
```
files := file1 file2
some_file: $(files)
	echo "Look at this variable: " $(files)
	touch some_file

file1:
	touch file1
file2:
	touch file2

clean:
	rm -f file1 file2 some_file
```
I doppi o singoli apici, rispettivamente `""` e `''`, non hanno significato in `make`; sono tuttavia utili se si vogliono utilizzare dei `command` del linguaggio `bash`, i quali hanno spesso necessità di tali caratteri per specificare come le stringhe vanno stampate (es. `printf`).

Un riferimento a variabile è possibile tramite gli operatori `${}` o `$()`
```
x := Ciao!

test:
	echo ${x}
	echo $(x)
```

## Targets
### Il target `all`
Supponiamo di avere un singolo `Makefile` che deve creare non uno, ma più programmi. Abbiamo la necessità che tutti i target relativi ai programmi che vogliamo compilare vengano eseguiti all'interno del nostro singolo `Makefile`; pertanto si è adottata ***la convenzione di specificare un singolo target che esegua tutti gli altri target***. Questo specifico target è `all`, il quale va posto ***in cima al `Makefile`*** e dovrà dipendere da tutti i target che vogliamo eseguire.
```
# Verranno eseguiti i target programma1 e programma2
all: programma1 programma2

programma1:
	# Compila programma 1
	
programma2:
	# Compila programma 2
	
clean:
	# Rimuovi programmi 1 e 2
```

### Target multipli
Nel caso di target multipli, ***i comandi presenti all'interno di tale regola verranno eseguiti per ciascun target***. Esiste una variabile automatica, `$@`, che contiene **il nome del target che si sta eseguendo**.
```
all: f1.o f2.o

f1.o f2.o:
	echo $@
# Equivalent to:
# f1.o:
#	 echo f1.o
# f2.o:
#	 echo f2.o
```

## Automatic Variables and Wildcards
### Wildcards
Esistono due ***wildcards*** in `make`: `*` e `%`. Esse vanno ***sempre*** accompagnate dalla funzione `wildcard`, siccome potrebbero non sempre venire espanse.
#### Wildcard `*`
La *wildcard* `*` cercherà nel filesystem file aventi un nome che ***corrisponde a quanto indicato***. Per esempio:
```
print: $(wildcard *.c)
	ls -la  $?
```
cercherà nella directory file che hanno estensione `.c` e ne stamperà le informazioni.
Inserire `*` nella funzione `wildcard` permette un funzionamento uguale alla *wildcard* `*` di `bash`. Utilizzare `*` all'interno di una variabile è considerato un errore, sia perché viene interpretata come stringa, sia perché, se non vengono ritrovati file con nome corrispondente all'uso della *wildcard*, **se non viene utilizzata la funzione `wildcard`** non viene espansa ulteriormente e rimarrà una stringa.
#### Wildcard `%`
La *wildcard* `%` ha un duplice utilizzo:
- È usata per ***matching***, ossia per memorizzare qualsiasi occorrenza di una determinata ***radice*** in una stringa quando viene utilizzata;
- È usata per ***replacing***, ossia per rimpiazzare tale ***radice*** all'interno di istruzioni successive.
In particolare è usata nelle ***Pattern Rules***: quando viene usata nei **target** cattura la parte di testo corrispondente al `%` dei target e lo rimpiazza nei ***prerequisiti***. Questo `Makefile`
```
main.o: main.c
	gcc -c main.c -o main.o

utils.o: utils.c
	gcc -c utils.c -o utils.o

server.o: server.c
	gcc -c server.c -o server.o
```
È equivalente a questo:
```
%.o: %.c
	gcc -c $< -o $@
```
Infatti in questo caso viene usata inizialmente per matching, ossia si memorizza tutti i nomi di tutti i file `.o` disponibili; dopodiché viene usata in replacing, sostituendosi ai prerequisiti `.c`.
> [!tip] La wildcard `%` è nettamente più usata di `*` siccome permette l'utilizzo dei pattern di sostituzione in quasi ogni occasione.
### Variabili automatiche
Esistono alcune variabili automatiche utilizzabili nelle regole di `make`:
- `$@` restituisce ***il nome del target***;
- `$?` restituisce ***tutti i prerequisiti più nuovi del target***;
- `$^` restituisce ***tutti i prerequisiti***;
- `$<` restituisce ***il primo prerequisito***.

## Fancy Rules
Esistono alcune regole che rendono l'utilizzo di `make` più efficiente.
### Implicit Rules
Sono regole implicite per la compilazione di file C e C++. Si chiamano *implicite* poiché non è necessario scrivere codice aggiuntivo per effettuare la compilazione o il linking di codice.
Esistono alcune variabili importanti per le *implicit rules*:
- `CC` - contiene il compilatore per C - di default è `cc`;
- `CXX` - contiene il compilatore per C++ - di default è `g++`;
- `CFLAGS` - contiene *flag* extra per il compilatore C;
- `CXXFLAGS` - contiene *flag* extra per il compilatore C++;
- `CPPFLAGS` - contiene *flag* extra per il preprocessore C;
- `LDFLAGS` - contiene *flag* extra da dare al compilatore quando questo invoca il linker.
In particolare, le *implicit rules* permettono di:
- Compilare un file `.c` in un oggetto `.o`.
- Linkare un file `.o` in un file eseguibile.
- Compilare un file `.cpp` in un oggetto `.o`.
Queste vengono usate di rado, ma sono molto utili per risparmiare tempo. È però sempre meglio specificare come il file va compilato - nonostante questo le variabili di cui sopra sono molto utili anche a questo fine.

### Static Pattern Rules
Esistono dei pattern che permettono di scrivere meno codice in `make`, una di queste è la seguente:
```
targets...: target-pattern: prereq-patterns ...
	commands
```
Qui si vede un utilizzo buono dell'operatore `%` visto prima:
- Vengono forniti i *target*; tali target vengono *matchati* tramite un apposito operatore `%` che si memorizzerà la ***radice***.
- La ***radice*** viene poi sostituita sempre tramite l'operatore `%`, questa volta in *replacement*, in modo da creare per ciascuno il prerequisito corretto.
Un esempio tipico di questa cosa è la seguente:
```
files = ciao1.o ciao2.o ciao3.o

all: $(files) # Questo ha come prerequisiti i file scritti sopra
	$(CC) $^ -o all # Linka gli oggetti ad un unico file 'all'
	
ciao1.o: ciao1.c
	$(CC) -c ciao1.c -o ciao1.o

ciao2.o: ciao2.c
	$(CC) -c ciao2.c -o ciao2.o
	
ciao3.o: ciao3.c
	$(CC) -c ciao3.c -o ciao3.o
	
%.c
	touch $@

clean:
	rm -f *.c *.o all
```
Che diventa:
```
files = ciao1.o ciao2.o ciao3.o

all: $(files) # Questo ha come prerequisiti i file scritti sopra
	$(CC) $^ -o all # Linka gli oggetti ad un unico file 'all'
	
$(files): %.o: %.c
	$(CC) -c $^ -o $@
	
%.c
	touch $@
	
clean:
	rm -f *.c *.o all
```
Molto più pulito!
## Commands and execution
### Commands Echoing/Silencing
Di base ogni comando che viene eseguito in `make` viene anche stampato sulla *shell*. Per evitare che ciò accada, possiamo far partire `make` con la flag `-s` oppure aggiungere un `@` prima di ogni comando.
```
all:
	@echo "Hello world!"
	echo "Questa viene visualizzata"
```
In questo caso il primo comando non verrà visualizzato, mentre il secondo sì.

### Command Execution
***Ogni comando viene eseguito in una shell figlia separata***. È necessario utilizzare gli operatori di separazione di comando di `bash` al fine di eseguire più comandi assieme.
```
all: 
	cd .. # Eseguito a parte
	echo `pwd` # Eseguito a parte - il comando precedente non ha influenzato.

	# Questi comandi vengono eseguiti l'uno dopo l'altro grazie a ;
	cd .. ; echo `pwd`

	# Uguale
	cd ..; \
	echo `pwd`
```

### Shell di default
La *shell* di default è `/bin/sh`. È possibile cambiare la *shell* che viene usata cambiando la variabile `SHELL`:
```
SHELL := /bin/bash

all:
	echo "Ciao da bash!"
```

### Doppio simbolo dollaro `$$`
Usare un doppio simbolo di dollaro `$$` significa stampare il simbolo di dollaro. Questo può essere usato per stampare il valore di variabili in `bash` durante l'esecuzione di comandi.
```
var_make := Sono una variabile make

all:
	sh_var='Variabile bash' ; echo $${sh_var} # Variabile bash creata e stampata
	echo $(var_make) # Variabile di bash stampata
```

### Gestione errori & interruzione
Si possono usare dei *flag* per la gestione degli errori:
- Aggiungere la flag `-k` a `make` ignorerà tutti gli errori, facendo andare avanti fino alla fine l'esecuzione. Utile se si vogliono vedere tutti gli errori;
- Aggiungere `-` davanti ad un comando sopprimerà eventuali errori generati da esso. 
- È possibile sopprimere tutti gli errori per mezzo del flag `-i`
È possibile interrompere l'esecuzione di `make` tramite `Ctrl-C`. Questo cancellerà anche i nuovi target che ha creato. 

### Uso ricorsivo di Make e variabili d'ambiente
È possibile usare l'istruzione speciale `$(MAKE)` per istanziare `make` con le flag già specificate in precedenza. Questo permette di evitare di dover rispecificare tutte le flag per l'esecuzione di `make` in sottodirectory a quella attuale.

Quando viene eseguito `make`, tutte le variabili d'ambiente della *shell* che viene utilizzata **diventano variabili `make`**, disponibili per tutte le regole presenti all'interno del `Makefile`.
È possibile anche utilizzare la *keyword* `export` di `make` al fine di rendere d'ambiente tutte le variabili `make` nelle *shell* in cui ogni comando viene eseguito. Questo risulta molto utile in un uso ricorsivo di `make`.
Utilizzare l'espressione `.EXPORT_ALL_VARIABLES` esporterà automaticamente ogni variabile creata nel `Makefile`.

## Variabili Pt. 2
### Flavors and modification
Esistono due tipologie di variabili:
- ***Ricorsive*** `=` - la variabile viene interpretata solo quando viene usata, non quando è definita.
- ***Espanse in modo semplice*** `:=` - la variabile viene dichiarata in modo normale. Vengono espanse variabili già dichiarate.
```
one = one ${later_variable}
two := two ${later_variable}

later_variable = later

all: 
	echo $(one) # Stampa "one later"
	echo $(two) # Stampa "two"
```
È possibile fare appending di altre variabili nel caso di variabili espanse in modo semplice `:=`, o tramite l'operatore `+=`. Inoltre può essere usato l'operatore `?=` per impostare variabili che non sono ancora state impostate.
```
one = hello
one ?= hello di nuovo # Non funziona!
two ?= hello di nuovo nuovo # Funziona!
one := ${one} there # one diventa "hello there"

all:
	echo $(one) # Stampa "hello there"
	echo $(two) # Stampa "hello di nuovo nuovo"
```
Esiste una variabile, `$(nullstring)` che contiene una stringa contenente un singolo spazio.
**Variabili indefinite verranno trattate come stringhe vuote**.

### Command lines arguments and override
È possibile fare override di variabili che arrivano dalla command line tramite l'istruzione `override`. Per esempio, usiamo `make option_one=hi` con questo `Makefile`:
```
# Questo argument viene cambiato
override option_one = did_override
# Questo no
option_two = not_override
all: 
	echo $(option_one) # Stampa "did_override"
	echo $(option_two) # Stampa "not_override"
```

### Target e Pattern Specific Variables
È possibile impostare delle variabili specifiche per specifici pattern o specifici target.
```
all: one = Ciao

all:
	echo Qui va tutto bene, $(one)
	
altro:
	echo qua invece no, $(one) # al posto di one stringa vuota.
```

```
%.c: ciao = ciao

main.c:
	echo Qua tutto ok $(ciao)
	
altro:
	echo Qua no $(ciao)
```

## Condizionali
### If/Else condizionale
```
foo = ok
all:
ifeq ($(foo), ok)
	echo Sono uguali!
else
	echo Non sono uguali.
endif
```
Come vediamo `ifeq`, `ifneq`, `else`, `endif` vanno messi non TABbati, nella stessa colonna del target.

### Controllo variabile vuota
```
foo =
all:
ifeq ($(foo),)
	echo È vuota!
else
	echo È piena!
endif
```

### Controllo variabile definita
Serve l'istruzione `ifdef` per controllare se la variabile **è definita e non è vuota** o `ifndef` per controllare se **è vuota** o **non è definita**  - non espande le variabili.
```
bar =
foo = ${bar}

all:
ifdef foo
	echo foo definita
endif
ifndef bar
	echo bar non definita
endif
```

## Funzioni
Le ***funzioni*** di `make` vengono usate principalmente per ***processare del testo***. Una chiamata ad una funzione viene realizzata per mezzo della sintassi `${function, arguments}` o `$(function, arguments)`.

### `subst` e `patsubst`
Le due funzioni `subst` e `patsubst` servono ad effettuare delle sostituzioni di determinati pattern presenti all'interno di stringhe con altri pattern.
In particolare, `subst` viene usato per mezzo di *stringhe o variabili*, mentre `patsubst` viene usato spesso in combinazione con la wildcard `%`.
Le sintassi sono rispettivamente:
- `$(subst toberep,repwith,text)` - `toberep` è la stringa con cui verrà rimpiazzata `repwith` nella stringa `text`;
- `$(patsubst pattern,replacement,text)` - rimpiazza `pattern` con `replacement` nella stringa `text`.
```
comma := ,
empty:=
space := $(empty) $(empty)
foo := a b c
bar := $(subst $(space),$(comma),$(foo))

all: 
	@echo $(bar)
```
In questo esempio viene stampato `a,b,c`. Attenzione a non lasciare spazi.
```
foo := a.o b.o l.a c.o
one := $(patsubst %.o,%.c,$(foo))

all:
	echo $(one)
	echo $(two)
	echo $(three)
```
In questo caso l'output è `a.c, b.c, l.a, c.c`.
### `foreach`
La funzione `foreach` converte ***una lista di parole in un altra lista di parole***, applicando a ciascuna parola della prima la stessa modifica per ottenere la seconda. La sintassi è `$(foreach var,list,text)` dove `var` è il *dummy name* per la variabile, `list` è la lista di parole iniziale e `text` è il modo in cui ciascuna parola dovrà essere manipolata.
```
foo := who are you
bar := $(foreach wrd,$(foo),$(wrd)!)

all:
	# Output: "who! are! you!"
	@echo $(bar)
```

### `if`
La funzione `if` è condizionale, simile all'operatore ternario in C. La sintassi è `$(if not-empty,then,else)` - se `not-empty` non è vuoto, la funzione viene sostituita da `then`, altrimenti viene sostituita da `else`.
```
foo := $(if this-is-not-empty,then!,else!)
empty :=
bar := $(if $(empty),then!,else!)

all:
	@echo $(foo)
	@echo $(bar)
# Output: then! else!
```

### `call`
La funzione `call` permette di richiamare semplici funzioni utente. Queste funzioni possono essere chiamate per mezzo di variabili, e i parametri di tale funzione vanno specificati numericamente. Vediamo per esempio una semplice funzione:
```
my_func := Ciao, sono $(0). I miei argomenti sono $(1) ed $(2).
```
Riguardo ai parametri:
- `$(0)` contiene il nome della funzione;
- `$(1), $(2)` sono i veri e propri parametri.
Applicando la funzione `call`:
```
my_func = Ciao, sono $(0). I miei argomenti sono $(1) ed $(2).

all:
	@echo $(call my_func, primoarg, secondoarg)
# Output: Ciao, sono my_func. I miei argomenti sono primoarg ed secondoarg.
```

### `shell`
La funzione `shell` viene usata per richiamare la shell di esecuzione di `make`. Rimpiazza le *newline* con spazi, quindi viene usata poco. La sintassi è `$(shell command)`.

### `filter`
La funzione `filter` serve a ***selezionare specifiche sottostringhe da una stringa se queste corrispondono a un certo pattern***. Esiste anche la versione contraria, `filter-out`, che rimuove dalla stringa gli elementi che corrispondono ad uno specifico pattern.
La sintassi di entrambe le funzioni è `$(filter patterns,text)`. È possibile specificare uno o più `patterns` ed è possibile annidare `filter` e `filter-out`.
```
obj_files = foo.result bar.o lose.o
filtered_files = $(filter %.o,$(obj_files))

all:
	@echo $(filtered_files)
# Output: bar.o lose.o
```

## Funzioni aggiuntive
Esistono alcune funzioni aggiuntive per `make`.
### `include` Makefiles
È possibile leggere altri `Makefile`, esterni da quello principale, tramite la keyword `include`, posta all'inizio del file. Questa istruzione è utile quando si creano `Makefile` basati sul codice sorgente, che vanno pertanto eseguiti in un secondo momento e quindi vanno inclusi sin da subito nell'esecuzione del principale.

### Direttiva `vpath`
Si usa la direttiva `vpath` al fine di specificare dove **alcuni prerequisiti esistono già**. Questo permette a certe regole di essere eseguite (o meno) sulla base dell'esistenza di questi file all'interno delle directory specificate tramite `vpath`. La sintassi della direttiva è `vpath <pattern> <directories>` - in `pattern` è possibile usare `%` per sostituzione, mentre le `directories` possono essere separate da spazi o virgole.

### Multilinea
Se le stringhe che risultano nel codice sono troppo lunghe, è possibile spezzarle per mezzo dell'operatore `/` - questo permette di andare a capo senza separare la stringa precedente.
```
all:
	echo Ciao sono una stringa molto molto \
		molto molto lunga!
```

### `.PHONY`
Associare `.PHONY` ad uno specifico target permetterà a `make` di ***non confondere tale target con un nome di un file***. Questo è utile per segnalare target speciali quali `all` e `clean`, in modo che, anche se esistono file chiamati `all` e `clean`, ***tali target verranno comunque eseguiti***.

### `.DELETE_ON_ERROR`
Se una regola genererà un errore (ossia se uno dei comandi all'interno di una regola fornisce uno status code diverso da 0), `make` terminerà immediatamente la sua esecuzione. Specificare un target tramite `.DELETE_ON_ERROR` significa che se quel target genererà un errore allora ***tale target verrà eliminato***.
```
.DELETE_ON_ERROR:
all: one two

one:
	touch one
	false # Errore qui, verrà eliminato il file 'one'

two:
	touch two
	false
```

## Cookbook for C/C++
Mettendo assieme tutto ciò che abbiamo imparato:
```
# Thanks to Job Vranish (https://spin.atomicobject.com/2016/08/26/makefile-c-projects/)
TARGET_EXEC := final_program

BUILD_DIR := ./build
SRC_DIRS := ./src

# Find all the C and C++ files we want to compile
# Note the single quotes around the * expressions. The shell will incorrectly expand these otherwise, but we want to send the * directly to the find command.
SRCS := $(shell find $(SRC_DIRS) -name '*.cpp' -or -name '*.c' -or -name '*.s')

# Prepends BUILD_DIR and appends .o to every src file
# As an example, ./your_dir/hello.cpp turns into ./build/./your_dir/hello.cpp.o
OBJS := $(SRCS:%=$(BUILD_DIR)/%.o)

# String substitution (suffix version without %).
# As an example, ./build/hello.cpp.o turns into ./build/hello.cpp.d
DEPS := $(OBJS:.o=.d)

# Every folder in ./src will need to be passed to GCC so that it can find header files
INC_DIRS := $(shell find $(SRC_DIRS) -type d)
# Add a prefix to INC_DIRS. So moduleA would become -ImoduleA. GCC understands this -I flag
INC_FLAGS := $(addprefix -I,$(INC_DIRS))

# The -MMD and -MP flags together generate Makefiles for us!
# These files will have .d instead of .o as the output.
CPPFLAGS := $(INC_FLAGS) -MMD -MP

# The final build step.
$(BUILD_DIR)/$(TARGET_EXEC): $(OBJS)
	$(CXX) $(OBJS) -o $@ $(LDFLAGS)

# Build step for C source
$(BUILD_DIR)/%.c.o: %.c
	mkdir -p $(dir $@)
	$(CC) $(CPPFLAGS) $(CFLAGS) -c $< -o $@

# Build step for C++ source
$(BUILD_DIR)/%.cpp.o: %.cpp
	mkdir -p $(dir $@)
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -c $< -o $@


.PHONY: clean
clean:
	rm -r $(BUILD_DIR)

# Include the .d makefiles. The - at the front suppresses the errors of missing
# Makefiles. Initially, all the .d files will be missing, and we don't want those
# errors to show up.
-include $(DEPS)
```