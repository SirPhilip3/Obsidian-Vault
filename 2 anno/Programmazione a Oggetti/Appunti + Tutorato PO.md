# Appunti
## 22/09/2023

Integer.parseint(args()) parsing args da string ad int

.class

Object oriented

java bytecode in output , universal execution , jvm si occupa dell'execution , worst performance
""""senza pointers

bytecode intermedio tra assembly e ( high level ) java , grabage collector , garbage = area di memoria che non ha un pointer assegnato

garbage collector avviato anche quando out of memory , rallentamento 

- out of memory
- garbage collection timeout ( max time for gc )

javabytecode 
operand stack  , pila delle operazioni 

i davanti a bytcode = int
/git
## 27/09/2023

[bytecode]([Java bytecode - Wikipedia](https://en.wikipedia.org/wiki/Java_bytecode))

```
if_icmpge 22
```

variabile speciale *this* in 0 di variabili locali : oggetto corrente

```
invokevirtual 
```
invocazione fun

var locali e operand stack per ogni fun

campo di una classe in heap
this.field = lettura valore di this
campo at compile time

```java
public class T{
	// campo
	int field; // in heap vale per tutti i metodi 
	

	int T{
		this.field++;
	}


}
```

bytecode  :
- laod / store local variables
- read / write heap locations
- invoke methods
- arithmetic op
- check conditions on values

metodi nativi mapping di funzioni in c in java 

puntato sul riutilizzo del codice 
-
classi campi e metodi

oggetto instanza di una classe con metodi etcc
rappresentazione di un'entità reale o astratta

funzionalità per evolvere l'oggetto
metodi modifiano i campi mantenendo consistenza logica

## 29/09/2023

encapsulamento dei metodi , non modifico i dati al di fuori della classe , espongo solo funzioni e nascondo i dati

struttura dati fissa durante l'execution 
ogni oggetto ha il prorpio life_cycle

ogni var locale che contiene un oggetto è un pointer a quell'oggetto se faccio 
	`fighter f3 = f1`

f3 prende stessi valori di f1 quando modificati *aliasing*

@ indica address accorciato 

metodi public / private
```java
class Runner{
// prima i campi e poi i metodi
	double speed;

	void refuel(){
	
	}

}
```

tutto il codice all'interno di una classe

ogni classe = nuovo tipo 

ciascuna classe in un file con nome del file = nome della calsse in realtà ci sono inner classes // not really

[naming conventions](https://www.oracle.com/java/technologies/javase/codeconventions-namingconventions.html)

Constructors

metodi sempre invocati su un oggetto

receiver = oggetto che viene modificato dal metodo chiamato

Contructor : 
stesso nome della classe
non ha un ritorno 

1 allocca memoria per un oggetto fighter 
2 invoca contructor

se non c'è un costruttore creazione del costruttore di default senza alcun parametro

se definisco un contructor non posso più cotruire un oggetto senza quel costruttore

posso definire più costruttori ( overloading )

## 04/10/2023

Instanza oggetto with contructor

new in bytecode = malloc di c ( malloc sizeof figther ) niiziailizza tutto a 0

nello stack delle op i parametri e li assegna a campi

tutti i metodi all'interno di una classe , sempre invocati in un metodo

tutti metodi dentro una calsse ha un prametro implicito this

chiamare constructor da constructor

```java
Fighter(Weapon w1, Armor a1){

	this(0,100,w1,a1)//chiama 2° constructor

}

Fighter(int exp , int life, Weapon w1, Armor a1) {  
    this.exp = exp;
    this.life=life;
    this.weapon = w1;  
    this.armor = a1;  
}
```

modifiers :
caratterizzare la classe , campo o metodo che ci permettono di modificarli

3 classi

+ Accesso
	+ public
	+ private
	+ protected
+ Concurrency
	+ synchronized
	+ volatile
+ Others
	+ satic 
	+ final
	+ obstract


static
	non è per forza legato ad un oggetto
	definire un metodo / campo di una classe 

metodi static 
	appartiene alla classe nella sua interezza 
	in un metodo statico il puntatore a this non esiste
	non esistendo in pos 0 dello stack c'è primo 

```java

static{

	Fighter.id=0;

}

```

costruttore statico per inizializzare i campi statici della classe , eseguito la prima volta che viene usata quella classe / start

clinit per static constructors

final : classi , campi e metodi
ha effetto su ereditarietà

campo final = non può essere modificato , si inizializza solo all'interno di un constructor
es id non deve essere modificato

costante globale static e final

## 06/10/2023

modi per accedere a campo statico 
```java

this.( nome campo statico )

```

```jvmbytecode
aload0
aload 0
pop
getstatic
```

inutile load visto che svolge pop dopo

campi persistono in heap fino a che l'oggetto non viene deallocato 
minimizzazione dei campi presenti ( mem occupation )

naming conventions : [Conventions](https://www.oracle.com/java/technologies/javase/codeconventions-namingconventions.html) 

static types :

primitive ( *values* )
objects ( *refrence types* )

```java
x=5
Weapon w = new Weapon()
```

|campi|value|
|---|---|
|x|5|
|w|pointer ad oggetto che abbiamo creato|

pointer nascosti
non posso allocare un oggetto direttamente nello stack ma viene allocato solamente il pointer

pointer condivisi tra oggetti
```
Weapon w1 = new Weapon(324)
FIghter f1 = new Fighter(w1)
f1.weapon.damage = 1200
```

sia f1 che w1 diventano 1200 modifico values nel pointer 

code data structure 

### Aliasing 

stessa area di memoria 2 nomi differenti es :
w1.damage
f1.weapon.damage

### Packages

Raggruppamenti di classi che possono essere importate in altre .class
naming conventions :
url reverse
deve rispecchiare il precorso delle directory

* * importa tutte le classi del package , memory !

### Encapsulation

Information hiding ( througth access modifiers ) :

private : solo visibile dalla classe ( visibilità di package , visibile solo dal package corrente , non c'è una gerarchia di package , package più profondi non ereditano , rimangono distinti )
public : tutti possono vedere ( final = non modificabile ) 
protected

deprecated metodo not supported

public "per usi interni" : modules file di testo in cui dico che classi esporto

## 9/10/2023

getter info su stato di un oggetto , può non essere direttametne un campo

Docs

.jar artifact contiene il bytecode
manifest : conteiene la classe main

javadoc ( docs in html )
commenti per documentation /** */ 

## 13/10/2023

artefatto = risultato compilazione

deployment = jar dentro un jar non ci sono commenti

nel manifest ho la versione del mio pacchetto non serve duplicarla con il javadoc `@version`

tag autore non in javadoc di default

visibility level = documentazione su private , public etcc

design by *contract*

specifica che input e corrisopndente output deve fornire un metodo

contratto sintattico


## 27/10/2023

Design by contract

@requires requisiti per classe javadoc ( precondition )
@ensures che val garantisce al completamento
indicazione con pre() per i dati presenti prima dell'esecuzione del metodo

contratto non deve contenere dei dati che non sono esposti esternamente

@ensures 
	return == 1 => f1.isAlive() && ! f2.isAlive() && f1.getLevel() >= pre(f1.getLevel())      && 
	return == 2 => f2.isAlive() && ! f1.isAlive() && f2.getLevel() >= pre(f2.getLevel())
non espone campi private e caraterizza ciò che succede se return 1 o 2 

per un campo possiamo definire un *object invariant* 

@invariant damage >= 0 && damage < pre(damage)
all'inzio damage >=0 e se viene utilizzato sarà sempre minore di ciò che era prima 

l'invariante deve essere soddisfatto in tutti i metodi che utilzzano il campo

java modelling lenguage

@loop_invariant ( invariante di ciclo ) ciò che rimane vero durante tutto il ciclo

new class 

## 03/11/2023

Inheritance

Creazione di classi generali poi espansione della loro definizione per definire classi diferrenti

Polimorifismo 

possesione di oggetti = ho una refernce ad un oggetto 

un oggetto può essere qualcosa es io sono una persona ma anche uno studente etcc

io ho tutti dati / funzionalità della persona + quello specifico per lo studente

*extends* 
![[erediarietà.excalidraw]]

erreditarietà singola una calsse estende solo un'altra 

Wizard non può avere come padre acnhe HUman

Gerarchia delle classi è un albero dx sx disgiunte 

perchè si possono creare dei casi di ambiguità

constructors se creo una sottoclasse prima inizializzo supercalsse poi me  stesso 
invocazione constructor superclasse :
`super(...)`

super pointer superclasse
pointer che mi permette di eseguire quello che sta nella superclasse

su sottoclassi invocazione costruttore sottoclasse 

se abbiamo costruttore senza parametri viene automaticametne invocato il supercostruttore

super può essere usato per eseguire campi e metodi ereditati ( i public )

this per tutti + sottoclasse

*protected* :
	visibile dal package e sottoclassi

Esternamente nessuno può inizializzare un personaggio di tipo generico solo wizard o figther

generalmente non fa parte dell'interfaccia esterna 
quando dobbiamo avere estensione alla calssse fa aprte dell'interfaccia esterna

volgio sapere quanti danni mi infligge un personaggio genreico o sapere qunato si difende , con personaggio generico non lo so

all'interno di figure hapublic getdamgae e setdamage ma non so l'implemetnaizoen per il personaggio genreico

*abstract* rendere i metodi astratti 
questo sarà obbligo delle mie sottoclassi di impleentarle

L'intera classe deve essere astratta , non può essere istanziata
metodo astratto può risiedere solo in una classe astratta 

se eredito da una calsse astratta , eredito l'obbligo di implementarlo se non viene iplementato deve essere definita astratta anche la sottoclasse

posso definire i metodi astratti nelle sottoclassi

## 08/11/2023

@override

ridefinizione dei metodi di super classi in sottoclassi 

definizione vs signature

ovverride stessa signature di un metodo 
overload signature differenti

overide visibiltà deve essere la stessa o più larga del metodo che sto overridando

## 10/11/2023

quando eredito un metodo deo avere la stessa vsiibilità o con visibilità più larga

final :
	In classi e metodi differente àfinal per i campi , si rifà alll'ereditarietà
	ci sono dei metodi che non voglio vengano riscritti adesso tutte le sottoclassi possono overridare un emtodo

`{java} final public boolean isAlive()`

in C# se non definisco modifier final è comunque final se voglio che lo sia uso keyword

campo non può essere astratto , campo non ha un'implementazione so già cosa va a fare

abastract+final -> metodo impossibile da implementare 

abstract+static -> qualsiasi cosa static non è ereditabile quindi non potrei definirlo 

static + final -> warning ridondante , statico non viene erdetiato quindi non potrebbe essere overridato ossia necessitare il final 

#### Classi 

classe final simile al metodo. la classe non può essere estesa

se ho una classe final posso non definire i metodi final -> ==ridondante==

classe non può essere privata poichè non darebbe utilizzabile , sarebbe visibile solo a se stessa 

classe  protected -> accedibile ovunque , non sarebbe protected ma publilc 

non può essere statica ( potrebbe avere senso se tutti i campi e metodi sono statici )

inner class può essere statica -> classe definita all'interno di un'altra classe  


### sottotipi

tipo statico c'è sempre , ogni tipo statico 

`{java} var` il compiler capisce che tipo possiede quell'oggetto 


quando ho una classe estesa -> calsse estesa è un sottotipo della supercalsse , permette nella pratica di applicare i l principio di sostituzione 

ci permette di usare metodi della superclasse nella sottoclasse poichè hanno lo stesso tipo 
>[!todo]
>ceck

`<:` -> sottotipo
Wizard `<:` Figure
Wizard tipo più preciso rispetto al supertipo Figure

### Polimorfismo

accelerate su macchina consuma benzian su bici accelera solo

tipo *dinamico* tipo :
`{java} Vehicle v = new Car()`

cambia da execution ad execution

il tipo dinamico è un sottotipo lo stesso tipo del tipo statico 
a livello statico posso sapere solo la "superclasse"

v è una macchina ma ptrebbe essere un'altra classe che estende la superclasse 

per vedere tipo dinamico  `{java} istanceof`

un wizard è anche un figure

posso castare il tipo `(tipo)`

## 15/11/2023

un qualcosa può essere più cose contemporanemaente
un camion e un carello sono entrambi caricabili 

multiple classes extensions creates diamond problem

![[Pasted image 20231115092234.png]]


**interfaces** 

ha un nome , all'interno una serie di firme di metodi  , non sto definendo l'implemetnazione dei metodi 
l'interfaccia è anche un *tipo* , non ha campi   

una classe può implementare un'interfaccia 
una classe eredita l'obbligo di implementare i metodi dell'interfaccia , se ne implementa solo alcuni o nessuno allora la classe deve essere astratta  

es magic sia una weapon sia una protezione w

```java

interface protection {

	public int getInfoDefence(){}

}
```

`{java} default`

per implementare metodi all'interno di interfaces 

```java
	// non serve default in campi di interface
	// private , non può essere utilizzato poichè potrebbe non essere inizializzato
	default public int getInfoDefence(){
		return defence;
	}
```


`final default` -> non può essere fatto 

campi default sono campi statici -> proprietà dedl mio tipo 
non si possono definire dei campi 
sono final anche

posso invocare degli altri metodi astratti che ogni classe deve definirlo 

firme dei metodi sono sempre pubbliche nelle interface 

interfacce che estendono altre interfacce  
quando un'interfaccia è sottotipo di un'altra interfaccia -> extends

quando 2 implemetazioni di default differenti da 2 interfaccie non posso ereditarle

interfaces
- senza campi

abstract class
+ solo eresitarietà multiple

## 17/11/2023

**Dispatching method calls**

reciever -> (this.speed ,, f.speed) che oggetto

quale metodo invocare , posso scieglere tra statico e dinamico
se mi baso sul tipo statico il metodo non cambierà a tempo di esecuzione 
chiamate a funzione cambiano a seconda del tipo dinamico

1 dal tipo dinamico guardo se ha implementaizione di quel metodo se è ereditata guardo nella superclasse 

risoluzione invocazione di metodo at runtime

risoluzione statica dei parametri ( con overloading scieglie quella con tipi statici soddisfatti ) 
perf

casting `figure->wizard` wizard è il tipo statico 

con più impleemtazioni che può scigliere sciglie  quella con i tipi più vicini' ( non devo cambiare tipo )

se ci sono 2 implemetazioni che hanno lo stesso 'costo' , priorità di scelta la stessa per il compiler

metodi statici  fa static binding quindi anche se viene chiamato da un tipo `dinamico` figthre viene eseguito il metodo del tipo statico `figure`

invocare metodo statico sempre su una classe no su un istanza , di base non viene ereditato quindi non può essere overridato

invokespecial ( binding statico sia del reciever sia dei  ) per costructors

`{java}return new Example(10)`

a livello statico so già che metodo devo chiamare -> example è satico + con parametri so il costruttore

![[Drawing 2023-11-17 11.48.03.excalidraw]]

classi generiche -> può ricevere dei tipi come parametri

## 22/11/2023

"Templates" in java :

quando classe generale viene utilizzata viene fatto il cast dinamico su tipo generico
tipo generico o sulla classe o sui metodi

```java
public <T> identity(T param){
	return param;
} 

public void foo(){
	AlmostEmpty obj = new AlmostEmpty;
	AlmostEmpty obj2 = obj.<AlmostEmpty>identity(new AlmostEmpty) ;
}
```

```java
obj.<AlmostEmpty>identity
```
indica su che tipo lavora il metodo

tipi generici a livello di bytecode non esistono 
invoca identity ritorna qualsisasi cosa ( non so il tipo di ritorno )

checkcast si passa dal tipo "qualisasi" al tipo che gli indichiamo ( casting se il tipo coincide con quello indicato  ) 

viene persa l'informazione di `{java}obj.<AlmostEmpty>` e viene fatto un cast dinamico `{java}(AlmostEmpty) obj.identity`

genericità su una classe 
```java
public class List<T1,T2>{// per il compiler una lista di oggetti (Object)

	//array list
	//private T[] elements = ...;
	// siccome non so il tipo a compile time non posso dichiarare un array con tipo T

	private T[] elements = null;

	public T get(int index){
		return elements[index];
	}

	public int length(){
		return elements.length;
	}

	public void add(T el){
		if(elements == null){
			ArrayList<T> list = new ArrayList<>();
			list.add(el);
			elements = list.toArrat(elements);
			// elements = Array.newIstance(); // array che è istanza di questa classe , di lunghezza n
		}
		//creare array con 1 el in più 
		elements = Arrays.copyOf(elements, elements.length+1);
		elements[elements.length-1]=el;
	}
	
}
```

```java

List<Wizard> f= new List<Wizard>(); 
Magic m = null;
f.add(new Wizard(m));
List<Figure> f2 = f; // non è possibile anche se la figure è suoerclasse di wizard 
// questo poichè in java le liste sono costanti
// se noi in f2 dopo l'aseganemnto aggiungiamo un wizard questi non sono compatibili

```

```java
Wizard[] f= new Wizard[2]; 
Magic m = null;
f[0]=new Wizard(m);
Sword s = null;
Armor a = null;
f[1]=new Fighter(s,a);
Wizard w = f[1];
```

I tipi degli array sono covarianti
ad un array di figure posso assegnare un array di wizard o figther
errore a runtime

tipi generici dei metodi sono legati ai metodi non alla classe 

il tipo statico essendo legato alla classe in generale T non è accessibile nel contesto satico devo dire che il metodo è parametrizzato su qualche tipo generico 
```java
public static <T1> T1 getFirst(List<T1> k)
```
`
`{java} new List<>()` il compiler assegan un tipo a seconda del contesto


tipi generici ristetti a solo sottotipi di qualche tipo 
`{java}public class ListFigure<T extends Figure> extends List<T>`
accetto solamente figure , figther , wizard

>[!note]
|
v

*covarianza* :
se ho lista di figure e una di figther se fosse covarainza figthrt sottotipo di figure va bene sui tipi di ritorno non sui parametri 

*controvarainza* figure sottipo figther poichè figure è un supertipo di fighter 

**Wildcards**
```java
List<?> listBoh = f;// f lista di figure
Object kk = listBoh.get(0);
// lista che è un supertipo di qualsiasi altra lista
// posso mettere dei bound per limitare i tipi
List<? extends Figure> listBoh = f;
Fighter kk = listBoh.get(0);
listBoh.add(new Wizard(null));// funziona so covarainza sul tipo di ritorno , nella controvarianza non funziona
```

## 24/11/2023

Object superclasse di tutto , implicitamente una classe in genrale estende la classe object

finalize sarebbe il free esplicito , viene invocato quando si libera la memoria , determinato dal gc non sappiamo se avviene e quando avviene

per sincronizzare i threads utilizzo degli oggeti qualsiasi

getClass oggetto che rappresenta il tipo dinamico dell'oggetto ( reflection )

equals per determinare se 2 oggetti sono gli stessi , implementato con oveload per ogni classe ( non stiamo coperendo il comportamento do Object )

>[!warning]

`{java}==` confronta addresses dei 2 object

equals proprietà matematiche :
```java
x.equals(y)
y.equals(x)

x.equals(y) > y.equals(z) -> x.equals(z)
```


## 29/11/2023

clone metodo protected >>> copia l'oggetto 
```java
@Override
protected Object clone(){
	return new Wizard(...) // calls constructor with all the parameter
} 
```

gli oggetti potrebbero non supportare il clone 

```java
protected Object clone() throws CloneNotSupportedException;
```

clone può essere fatto public (estensione della visibilità) , e cambiare return type 

interfaccia clonable , se viene inmplemetnata allora posso clonare quell'oggetto altrimenti no

per vedere se è cloanbile : 
```java
array[i] instanceof Clonable
```

Se lo clono non è detto che questo sia accessibile da ovunque ( clone potrebbe non essere pubblico -> potrebbe essere protected )

oggetti clonati sono equals -> ( di base può fare solo shallow copy ) , ed hanno stessa classe 

**hashCode**

all'inerno di un indice metto tutti gli oggetti che *potenzialmente* sono uguali 
se 2 oggetti sono == necessariamente devono avere lo stesso hashCode ( anche tra più objects )
se sono diversi non è required che abbiano index differenti in hashCode

`{java}Object.hash(el)` -> libreria che ci ritorna  un hash code adeguato 

```java
public int hasCode(){
	return 0; // always ok 
	// complessità pratica -> n object all'interno dell'indice 0
	return id%37; // grups of objects
	return id ; // tabella in cui ogni index 1 elemento memory duplication
} 
```

hashCode deve essere sempre definito se ridefinisco equals 

`{java}String` -> classe *final* sotto object 
istruzioni specifiche per String -> non fa nessuna new se gli passiamo una string 
`{bytecode}ldc "fff"` -> usando quei byte della variabile locale crea un oggetto di tipo stringa  
se 2 stringhe == costante nello stesso punto del pool 

```java
String a = "cf";
String b = "cf";

a.equals(b) -> true ; 

a==b -> true ; // stessa area di mem , aliasing 
se a.replace('c','t'); // a="tf" 

```

classe string è immutabile , a.replace ritorna una nuova stringa modificata non modifica l'originale 
`{java}a=a.replace()`

-> in questo modo non devo invocare ogni volta il costruttoredella stringa

operatori su stringhe -> 
+ concatenazione : `{java}a=a+"ceec"` in realtà non essendoci l'overload degli operatori è come fare `{java}a=a.concat("ceec")` -> in realtà invokedinamic ( non invoca la concat poichè è costoso ) nel bytecode 

`{java}toString()` -> ci aiuta a visualizzare lo stato di un oggetto ( è presente nella classe Object  )

```java
@Override
public String toString(){// public 

	return "....."; // descr oggetto

}
```


se non definisco un toString -> ritorna address ( posizione nel package + address ) di quell'oggetto 
se concateno una stringa con un oggetto chiamato il toString su di esso -> dove ho dei dati da rappresentare  


## 01/12/2023

**Sets**

Set tipo generico  ( insieme di elementi differenti tra loro ) 

hashset ->set che utilizza un hash table

ieteratori possiamo chiamarli esplicitamente per iterare in insieme di set 
altrimenti utilizziamo cicli for each
```java
for(T el : this)
```

il for each nel bytecode comunque chiama l'iterator 

con hash set non abbiamo nessuna garanzia dell'ordine in cui eseguiamo l'iterator per hashset
i treeset ci danno invece la garanzia di avere un ordine di iterazione

hashset estende direttametne abstract set 
tree set invece implementa navigable set -> ci da la caratteristica dell'ordine di iterazione

per fare treeset devo avere un modo per confrontare gli oggetti poichè li compara e poi li mette in ordine in modo da dare un ordine alla iterazione
quindi l'oggetto deve estendere il comparable

compareto -> ritorna un valore che ci permetta di capire se 
1 se ritorno 0 allora sono equals 

```java
// un oggetto e < di un'altro se il suo damage è minore il contrario se >
public int compareTo(OffensiveObject o){
	if(this.equels(o))
		return 0;
	int result = this.damage-o.getInfoDamage(); // se così se 2 oggetti differenti hanno damage == risulterano = anche se non lo sono
	if ( result != 0 )
		return result;
	return 1; // todo 
}
```

natural and total ordering 
se abbiamo un valore positivo l'oggetto è maggiore se diamo un valore negativo l'oggetto è minore

tutti gli oggetti che estendono offensiveobject devono implementare il compareto 

equals funziona con l'object mentre il comparable è parametrizzato 

il magi che compare to implementa quello di defensive object o quello di offensive object visto che entrambi hanno magic come sottoclasse
quando eredito 2 contratti con 2 tipi generici differenti , il compare to è definito su object e poi il compiler decide

aggiungo un interfaccia vuota comune ad entrambi ( estesa da entrambu ) che estenderà il comparable che lo farà su un oggetto generico 

implementazioni di default -> se abbaimo 2 che hanno stessa firma se sono ereditati -> non posso farlo per il problema di prima 

**classe collection**

collezione di oggetti

factory -> usa random per avere oggetti random

## 6/12/2023

### Sets

iterable -> non è detto che itero nella stessa sequenza 

classi ed interfacce con lettera maiuscola all'inizio -> tipi con lettera minuscola non sono sottoipo di object :
int , long , double , char

lista -> solo di sottotipi di object ( non tipi primitivi )

bytecode -> 
se variabile da 64 -> 2 address occupati il compiler lavora a 32 bit

regole di conversione impleicita 
int assegno un long -> int diventa un  long
float assegno duble -> float diventa double
al contario error

casting a long to int -> -1
errori di conservazione dell'info 

boolean non può essere castato esplicitamente -> booleano a se 

se assegno int  a char -> numero della tabella ascii 
da valore numeri a carattere non va

*boxing*
*unboxing*

sotto object abbiamo classe che rappresenta 
+ Bool
+ Char
+ Number
	+ Long
	+ Float
	+ Double
	+ -....-

valueof -> prende intero e crea un'istanza di integer ( wrapper per int )
ci permette di utilizzarli nei generics e farle interagire con altre classi


literals : costante -> di default un valore con . -> duble , non può essere castato a float -> devo indicare che è float -> 3.0F double->3.0D

valueof essendo che lo uso spesso creo una cache che contiene tutti i gli oggetti compresi tra min e max -> se creo 2 oggetti con stesso valueof indica a stesso address di memoria -> essendo che la classe è immutabile non ho problemi di aliasing 

posso fare :

```java
Integer i1 = 10 ; // 10 trasformata in istanza di integer Integer.valueof(10)
```
questo vale solo per queste classi 
posso fare 
`{java}Object o = 10; // integer è sottotipo di object quindi posso assegnarlo`

```java
String s = "Il valore è";
int i = 1;
String s2 = "halo";
String s1 = s+i; // works 
String s3 = s2+s1; // quando sommi = concatenzione

(i + i2)// interi -> somma inter + s1 // concat stringa
(s1 + i)// concatena 2 stringhe + i2 // non somma 2 int ma conact a stringa
```

### Exceptions

nullpointerexception -> classe
le eccezioni sono una classe

metodo che causa errore esce al primo momento in cui trovo eccezione , esce da tutte le chiamate che non gestiscono quell'errore

**Throwable** exceptions 

classe throwable passa messaggio e causa se causiamo una eccezione passiamo cmessaggio e causa es ho passatto personaggio null

messaggi esplicativi 

c'è stack delle invocazioni di metodo

esteso da error ed exception

error -> problema per cui il programma non puù riprendere l'esecuzione
exception -> posso tornare all'esecuzione normale 

runtime exceptions -> problemi durante esecuzione della jvm , problemi aritmetici , indexoutof bunds 
## 13/12/2023

Exceptions -> 

classi che estendono sotto exception se riguarada il nostro codice 

**Example**

```java
public class MissinFigureException extends Exception{

	// exception ha un supercostruttore senza parametri 

	public missinFigureException(String m){
		super(m);
	}

}
```

```java
throw new MissinFigureException("...")  // esce dal metodo corrente e lancia un'eccezione
```

la throw deve essere aggiunta anche nella firma del metodo -> indica che eccezioni può lanciare 

```java
public RandomMethod() throws missinFigureException
```

metodi che invocano metodi che lanciano eccezioni devono anchessi includere `{java}throws` 

se controllo a priori che non può accadere l'eccezione prima di chimaare il metodo 

```java
try {

...

} catch ( MissinFigureException e) {
	// non posso fare recovery dal'errore
}

// una o più espressioni catch per diverse exception 

```

se catcho l'eccezione non serve che la dichiaro

Errors 

```java
public class UnexpectedError extends Error{

	UnexpectedError(String m ){
		super(m)
	}

	UnexpectedError(String m , Throwable couse){
		super(m,cause)
	}

}
```

2 classi di Throwable per le quali non necessitiamo dichoarare che la classe lancia questa eccezzionie 
`{java}RuntimeException , Errors`

questi tipi di errori possono accaderer ovunque

eccezioni unchekd

in javadoc @throws -> fa parte dell'interfaccia esterna

metodi overridati con exception in cui non lancia eccezione
posso fare override lanciando un numero inferiore di eccezioni

tutte le eccezioni di un metodo overridato devono esssere un sottoinsieme di eccezioni di metodo originale 

per principio di sostituzione non posso farlo 

catturare sempre un'eccezione specifica non generale

```java
finally // svolge sempre e comunque quello che specifico
// quando il catch non è di un erroe compatibile ( sottoclasse )
```

finally usato quando ho bisogno di chiudere in qualsiasi caso qualcosa -> sockets , files
```java
try ( tipo closable s = nuovo oggetto sottipo ){

... closes everytinh	

}
```

in bytecode tabella delle eccezzioni  

Assertion -> assicurrsi che una espressione si a vera 
se vogliamo che l'esecuzione si fermi all'assaertion falsa devo autorizzare assertion a execution time ( ne complier )

-ea 

testing interno

`{java}assert .... : "messaggio"`

in bytecode c'è un campo nascosto Z che ha bool che indica se ho le assertion enabled o no

throw in bytecode athrow -> ferma esecuzione normale del codice 

@ sono annotazioni che applico al metodo

## 15/12/2023

Annotazioni -> aggiunta di informazioni in modo che aiuti il compiler 
posso dire che un metodo è deprecated -> non è possibileeliminarlo 
warning -> utilizzi deprecated 

per @deprecated posso aggiungerci uno stato
es @deprecated(since = 0.2)
posso anche dire for_removal -> lo voglio elminare prima o poi

se faccio @override -> non compila se non ho fatto un override 

posso creare annotazioni custom 
interfaccia annotation 

definire un'annotazione in un java separato : 
```java
@interface Speed{
	String type() default "km/h"; 
	boolean forward() ; 
}

// come una struttura con solo campi -> attributi del'annotazione

```

record con metodi standard già compilati

**ES**

```java
public @interface Restore{
	int percentage() default 100;
	int amount();
}
```
poicgè estende interfaccia in realtà sono metodi per fare in modo che possano essre modificati da 


se voglio che venga utilizzato solo : 
```java
@Target{ // annotando l'annotazione che vogliamo retringere
	ElementType.FIELD // annotazione solo su field,method,parameter
	ElementType.METHOD
	ElementType.PARAMETER
	// messi in value -> non mi serve definire il field se ne ho solo 1

}
```

@retention -> SOURCE eliminato come un commento
CLASS nel bytecode eliminato ad esecuzione
RUNTIME mantenuto anche a runtime

@documented se l'annotazione fa parte dell'interfaccia pubblica della libreria 
es @override non è documented 

@SuppressWarnings -> non far calcoalre warining al compiler -> per un metodo -> devo indicargli che tipo di warning sopprimere
```java
@SuppressWarnings({"unused","...."})
```

junit

directory di test contiente metodi di test 
```java
@Test
public void test1(){
	Sword s1 = new Sword();
	Armor a1 = new Armor();
	Figure Figure1 = new Figure(w1,a1);

	Assertions.assertTrue(Figure1.isAlive()); // deve sempre essere vera utilizzo assert di @Test Junit
	

}
```

junit esegue tutti i metodi con @Test 

utilizza reflection per eseguire il codice 

## 18/12/2023

JAXB

java architecture for xml binding
oggetto voglio salvarlo in un file attraverso XML 

fino a java 8 parte delle librerie di default 

@XmlRootElement , inizio classe che può essere salvata
@XmlType -> dico che deve essere salvato come xml

per ogni campo della classe che volgio salvare devo dirgli xml element o attribute
+ element più verbosa
+ attribute più concisa

marshal , unmarshal -> metti in xml , prendi da xml

JAXB context classe generica per istanzaiare marshall e unmarshall , è la classe che fa la new non l'utente

tutto questo crea eccezioni , dobbiamo avere una eccezione  per prendere eventuali eccezioni es leggo sword al posto di magic , + ioexception

marshal deve essere statico , non ha un this 

metodo main deve anche avere eccezzioni che possono essere lanciate

per usare jaxp devo avere un costruttore public senza parametri

attributo dentro tag , element compreso tra tag
```xml
<magic damage="5"> // attribute
	<defense>3<\defense> // element
<\magic>
```

encoding per sapere come leggere 
l'unmarshall creare un magic , legge l'xml e lo mette all'interno del nuovo oggetto 
```java 
Magic.class
```

xmlattributee xmlelement può essere utilizzato sui getter o setter 

**Reflection**

pezzo di codice che può accedere dovunque 

informazioni sul programma , come è fatto se stesso che sono già state caricate in memoria 
anche per eseguire parti del programma , non mi serve usare constriìoctors stetters etccc, poichè tramite codice devo sapere che esiste etcc mentre con reflection non ho bisogno di sapere 

potrei rompere l'encapsulation 

java.lang.reflect

classi che rappresentano parti del programma + exceptions

In classe Object
```java
getClass
```
ritorna un oggetto di tipo class 

in java lang 
`{java}Class Class<T>` 

Classe rappresenta la struttura delle classi 
dammi l'oggetto che rappresenta la tua struttura 
posso chiederti le interfaccie che implementi , la sua superclasse etcc
posso vedere tutte le annotations 

```java
Magic m = new Magic();
Class cls = m.getClass();
for(Constructors c : cls.getConstructors)
	println;
for(Mathods met : cls.getMethods)
	println;
for(Field fl : cls.getFields())
	println;
```

Stampa tutti i costruttori , metodi ( anche quelli ereditati ) , non tira fuori campi private solo public 
se uso `{java}getDecleradeFiled()` ritorna tutti i campi senza visibilità

se è presente assert vedo $sssertionDisabled del bytecode

vediamo stato del programma dopo compilazione

se non ho un oggetto dichiarato posso fare `{java}Class cls = Magic.class`
so come è strutturata la classe 

posso assegnare un valore ad un campo 
```java
set(Object obj, Object value)
```
setta il valore del field obj con il valore di value
illegal access -> potrebbe essere che il campo sia privato se uso declaredfields

posso rendere un campo sempre accessibile anche se private con `{java}setAccessible(true)`
e  scriverci dentro 

moduli ci permettono di fare reflection solo in un certo modulo

metodi , posso eseguire il metodo attraverso la reflection 
invoke -> istanza dove voglio chiamare il metodo , posso pasasgli altri valori dei parametri dei valori del metodo , ritorna un object 

```java
Magic m1 = cls.getDeclaredMethod("getInfoDefense")
Object result = m1.invoke(m); // su un oggetto magia
```

se il metodo ritorna void in result abbiamo null 

costruttore lo invochiamo solo quando creiamo l'area di memoria 

getConstructro , devo passargli l'istanza di calss che rappresenta quel tipo  int.class 
```java
Constructors c = cls.getConstructor(int.class, int.class, int.class)
```

c non ha invoke , ha il newInstance ceh crea un nuovo oggetto con il costruttore selezionato , ritorna sempre un oggetto dove mette la nuova istanza creata

## 20/12/2023

unchechecked exceptions :
```java
extends RuntimeExceptions
```

no need to declere in method

dobbiamo passare una cause
```java
catch(type1|type2|type3 e)
```
può catturare più eccezioni specifiche assieme per 

dall'annotazione posso vedere amount -> a lovello dinamico posso accedervi all'interno del metodo

getAnnotation ritorna istanza dell'annotazione , posso fare .amount

istanziare tutti i possibili perosnaggi che sono dichiarati 

classpath -> tutto l'ambiente di esecuzione

se trovo tutte le classi solamente quelle che sono state istanziate fino ad ora 

**Gradle**

classpath -> indica tutto il bytecode java utilizzato 
se eseguo classi che hanno bibsogno di altri calsspath devo metterle nel calsspath

nuova versione di una libreria deve essere retrocompatibile , 

building automation

DSL domain specific language -> per impostazioni

gradlew execute gradle

plugins

SNAPHOT -> in sviluppo

repositories da dove prendo dep

dependencies

testImplementatio -> only when testing
testRuntimeOnly -> quando eseguo il codice

per evitare clutterning del codice qunado in production

## 22/12/2023

cache delle librerie pef fare in modo di non dovrer riscaricare ogni volta se non funziona delete form cache





# Tutorati 
## 14/10/2023

final deve essere assegnato alla creazione dell'oggetto della classe solo 1 volta dal costruttore o nella dichiarazione della variabile

Encapsulation 

## 04/11/2023

## 18/11/2023

## 11/12/2023

Generics 

```java
List<? extends Superhero> se = new ArrayList<>();
// con extends non posso inserire elementi all'interno della lista
// posso fare il get poichè so che mi ritornerà un superhero
// non posso add poichè non so il tipo della lista , la lista ha solo 1 tipo 

List<? super MarvelSuperhero> se = new ArrayList<>();

// non posso fare il get 
// posso inserire sottotipi di marvelsuperhero
```

## 16/12/2023

Annotations 

costruttore può essere privato , può essere solo chiamato da altri costruttori all'interno della stessa classe 

metodi final -> non possono essere sovrascritti 

campi di una classe final possono essere modificati ma non può essere estesa la classe  

overloaded metodo + imple stessa classe 

chiamando un metodo su un oggetto imlementazione a tipo dinamico 

invarianza dei generics

producer extends consumer s pecs

per modificare la stringa se ne crea una nuova e poi viene modificata la reference.

