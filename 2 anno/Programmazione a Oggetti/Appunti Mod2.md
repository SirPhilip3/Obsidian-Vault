# 13/02/2024

imperativi -> con assegnamento
funzionali -> senza assegnamento ( only ricorsione ) 

```java
public class main {

	public static int f(int n){ // metodo statico , funzione , non ha bisongo di un istanza di un oggetto
		return n+1;
	}
	// metodo non statico ha bisogno di un oggetto 
	// main static altrimenti avrebbe bisogno di un oggetto istanziato

	public int g(int n){
		return n+1;
	}
}
```

```java
Main o;
int k = o.g(4);
// runtime error , tipi corrispondono
// a runtime o non è istanziato in memoria
```

primitivi call by value altri by reference

linguaggio ad oggetti se offre il subtyping -> 

*Polimorfismo* 
sottotipi stanno in supertipi
dinamic dispatching -> 

subsunmption conosci cosa precisa ma la dai per generale
	nei binding ( costruisco dog metto dentro animal )
	in passaggio di argomento
	assegnamento

# 15/02/2024

```java
Animal pluto = new Dog(); // subsumption
```

library managment 

java standard library 

array statici in java ( nativi ) 
vector array dinamici 
arraylist 

*jdk* ( *java development kit* ) [jdk](https://docs.oracle.com/en/java/javase/17/docs/api)

**Iterable** 

```java

```

estendere o implementare sono la stessa cosa , entrambi creano un nuvo sottotipo , posso fare subsumption in entrambi i casi

```java
interface I{

// ....

}

class K interface I{

// ....

}

// K è sottotipo di I , posso fare subsumptio

```


```java

public interface I {



}

public static abstract class K {

// solo metodi astratti 

} 

// classe stessa cosa della interface
// differenze 
// classe può essere ereditata solo da 1 altra classe 
// 

```

```java
public interface I {

	void a();
	void b();
	default void c(){
		a();
		b();
	} 

}

public static abstract class K {

	public abstract void a();
	public abstract void b();

	public void c(){
		a();
		b();
	}

} 

// classe stessa cosa della interface
```

in iterable metodo iterator deve essere implementato ( non default )

**Iterator\<E>**

iterable garantisce che sono strutture dati itereabili , collection aggiunge add ,contains etcc + Iterator() ereditato da iterable 
Iterator 

*List* -> aggiunge a collection dei metodi ( interfaccia ) , ci sono altri metodi per costrutire liste ( arraylist , circolari , queue , stack ) , insieme di elementi legati tra loro 
una struttura dati linerare a cui puoi accedere dato un indice ( array , list )

collection rappresenta strutture dati che non posso accedere tramite indice ( set ) , posso solo scorrere + pushback in coda

```java
ArrayList<Integer> A = new ArrayList<Integer>(); // no subsumptio
// costruttore di default quello che non viene implementato , 
// costruttore vuoto quello senza parametri implementato ( crea arraylist vuoto )
A.add(23); // per arraylist in coda
A.add(2); 
```

add ereditato da collection

```java
Collection<Integer> A = new ArrayList<Integer>(); // subsumption
A.add(23); // add è aggiunta da Collection poi a reuntime runna il metodo di Arraylist 
A.add(2); 
```


```java
List<Integer> A = new ArrayList<Integer>(); // subsumption
A.add(23); // add è aggiunta da Collection poi a reuntime runna il metodo di Arraylist 
A.add(2);
A.set(2,45); // ritrona elemento che c'era prima di quello che setterò
```

List rende la collection indicizzabile , accessibile in lettura e scrittura

```java
for(int i=0; i<A.size(); i++){
	A.get(i);
}
```
Spreco di tempo faccio get troppe volte 

```java
Ierator<Integer> it = A.iterator() // poichè è stato ereditato da iterable
// iterator punta all'inizio della lista
while (it.hasNext()){
	int n = it.next(); // ritorna l'elemento in cui ti trovi + si sposta la porssimo
	// hasNext fallisce quando va fuori dalla list
}
```

utilizzo for each ( utilizza iterator ) 
```java

```

# 20/02/2024

package 

iterable deve fornire l'iteratore almeno
ogni file una sola entità ( se volessi solo nested nessuna relazione con classe al cui è all'interno )
se interfaccia dentro interfaccia dovrei riferirmi `{java}Iterable.Iterator` meglio creare una nuova interface

differenza type parameter e type argument : 

```java
void f(int n) // n parametro type parameter

f(7) // 7 è un argomento type argument
// n non esiste nello scope del chiamante
```

```java
public interface Iterable<T>{ // è un parametro , analogo a n 
	// all'interno dello scope posso utilizzare T , argument quando faccio new
}
```

strutture dati eterogenee (senza tipo) per fare get posso gettare solo object , non sai cos'è e non posso fare nulla e ti assumi il richio di castarlo potresti castarlo ad un tipo sbagliato 

compiler non ti obbliga a bindare un risultato

# 22/02/2024

il tipo di null subsume a tutto ( sottotipo di chiunque )

un metodo usato tantissime volte -> scomodo fare sempre try cach

jdk mette unchecked 

classi *anonime*

# 26/02/2024

interfacce sono solo cosa devo implementare non come ( elenchi di metodi che se sono una calsse che la implementa devono essere imlementati )

Dobbiamo utilizzare l'iteratore per iterator poichè 

creare un iteratore vuol dire fare una calsse che rispetta iterator , mi basta imlpementare i due metodi

*anonimus class* 

```java
private static class MyIterator<T> implements Iterator<T>{  
  
    private int pos = 0;  
  
    private ArrayList<T> enclosing;  
  
    public MyIterator(ArrayList<T> a){  
        this.enclosing = a;  
    }  
  
    @Override  
    public boolean hasNext() {  
        return pos< enclosing.size();  }  
  
    @Override  
    public T next() {  
        return enclosing.get(pos++);    }  
}  
  
@Override  
public Iterator<T> iterator() {  
      return new Iterator<T>{ // classe anonima  
	    // espressione che istanzia al volo di un oggetto di tipo iterator
	    @Override  
	    public boolean hasNext() {  
	        return pos< enclosing.size();}  
	    @Override  
	    public T next() {  
	        return enclosing.get(pos++);} 
		};  
}

```

*nested class*

```java
class 
	...
	class
```

vantaggio : 
 + così ha accesso a tutti i campi/metodi della enclosing
 + semplicità implementazione 
siccome devo scorrere l'enclosing mi fa comodo accedere alla mia enclosing

per i campi statici il campo non viene messo in virtual table  

i campi prima di avere chiamato costruttore tutti i reference type vengono inizializzati a null mentre quelli primitivi con i loro valori di default
es per int mette 0 poichè il defualt contructor di int inizializza a 0 (  ) 
quando faccio = sovrascrivo sempre , il codice generato da compiler alloca memoria quando costruisco una classe , alloca la memoria relativa ai tipi primitivi e reference ( prima della new ) 
dati oggetto = somma spazio dei suoi campi ( come una struct in c )

array sono reference type = 8 byte ( cpu a 64 bit con world a 64 bit ) , int = 4 byte

anche pointer a funzione ( pointer a metodi ) -> pointer a codice dei metodi 

dynamic dispatching con chiamate a funzione , svolto a runtime , se avessi un animale ma è un dog quando chiamo un metodo mi chiama quello del cane non degli animali 
insieme ai campi di oggetto creata tabella con pointer a metodi ( virtual table )
se io istanzio array list e la passo a qualcuno che la subsume a collection ( subsumption ) perdo infromazione sul tipo originale , faccio 2 dereference , prendo address nell'oggetto stesso e jumpo lì ( jump indiretta ) 

è anche polimorfismo

costruttori non sono mai polimorfi ( non sono in virtual table ) 

metodi override :
+ pointer della mia implementazione 

quando una cosa è static non ho in virtual table , non ho accesso agli altri pointer , nessuno può chiamarmi , non posso fare polimofismo 

metodo static , metodi che non dipendono dai campi di un oggetto

```java
// non utilizzo metodi o campi di ararylist , posso usare static
public static int fact(int n)
	return ....
```

nested non statiç è in virtual tabel con enclosing

namespace di cpp per dividere in sottocartelle 

meglio nested se lo uso solo solo all'interno altrimenti inquino il package

# 27/02/2024

*classi anonime*

```java
@Override  
public Iterator<T> iterator() {  
  return new Iterator<T>{ // classe anonima  
	// espressione che istanzia al volo di un oggetto di tipo iterator
		private int pos=0;
		
		@Override  
		public boolean hasNext() {  
			return pos<size();
		}  
		@Override  
		public T next() {  
			return get(pos++);
		} 
	};  
}
```

Oggetto anonimo 

Espressione ( ha un tipo e può essere valutata ) dalla new al punto e virgola , occupano una righa tra ; valutano qualcosa di un certo tipo 

la new ha tipo iterator computa un nuovo oggetto , puoi costruire "al volo" una istanza di un tipo senza definire la sua classe definendo al volo le implementazioni , la new in questo caso non sto invoncando un costruttore , se dopo new metto nome interfaccia sto costruendo un oggetto al volo  

```java
n > 3 // tipo bool -> computa true false 
```

Statements , alcuni sono fatti di espressioni
>[!example]
```java
int n=6;
return ...;
if ...;
for
while
do while
switch 
break 
continue
try catch 
throw
```

if then else in forma di espressione -> operatore ternario 

Posso aggiungere metodi ma questo viene subsutnto all'interfaccia quindi non può chiamarlo visto che vede solo i metodi di quella interfaccia

vantaggi : non inquino il package con classi che poi non mi servirebbereo ad altro

```java
@Override  
public Iterator<T> iterator() {  
	
	int pos = 0;
	
	return new Iterator<T>{ // classe anonima  
	// espressione che istanzia al volo di un oggetto di tipo iterator
		@Override  
		public boolean hasNext() {  
			return pos<size();
		}  
		@Override  
		public T next() {  
			return get(pos++);
		} 
	};  
}
```

posso accedere alle variabil del metodo in cui siamo all'interno ,
Le classi anonime sono delle *closure* , una anonimus class porta con se lo scope in cui è stata definita

fondamentale per funzioni lambda , programmazione funzionale etcc 
se avessimo il campo potremmo vederlo solo all'interno dell'anonimus class , metterla come variabile ci permette di utilizzate delle cose che sono nello scope 

campi hanno attributi di visibilità le variabili no 

== confronto by reference , se sono lo stesso pointer , se non sono pointer confronto strutturale , polimorfo 
omogeneo -> confronto lo stesso tipo
eterogeneo -> confronto tipi differenti

Equals dentro object
Equals predefinito prende un object come argomento 
è polimorfo -> posso confrontarmi con una cosa qualsiasi ( *eterogeneo* )
Normalmente uguaglianza deep

metodi di blitting copia di blocchi di vettori

Implementiamo Mappe -> associa ad ogni chiave un valore , un array è una mappa aventi come key int

Single linked list 

clear -> gc multishot perchè all'inizio tolgo reference al primo blocco e così via ( il gc passa ogni n secondi servirebbero l cicli della gc per completare  ) , nel jdk garbage collection oneshot mettendo tutti i next a null

# 05/03/2024

```java
// ricerca dell'indice 
// getter del node
// meglio protected , eventuali sottoclassi di linked list che devono scorrere portebbero utilizzare getNode
// ora la classe node sta meglio protected
// anche head protected 
// se voglio tenere protected faccio una serire di metodi per chi sta sotto per ottenere le informazioni (API)
// se lascio private la head faccio un metodo getHead , setHead protected
// ha senso fare API solo se libreria molto sofisticata
private Node getNode(int i){

	// 

}

public T get(int i){

	return getNode(i).data; // non faccio binding con una variabile pochè latrimenti devo

}

public T set(int i, T x){

	Node n = getNode(i);
	T old = n.data;
	n.data = x;
	return old;

}
```

Binding -> battezzare un dato dopo di che lo posso usare più volte se lo uso più volte allora lo dichiaro ( riuso dei dati )
Bind usato una sola volta dal compiler viene tolto e trasformato inline

size lasciamo private tanto c'è già sopra ( chiamata con dynamic dispatching )

se voglio fare add in coda in una sottoclasse anche *sz* deve essere protected poichè questa dovrà cambiare la size 

private non si usa quasi mai poichè blocca l'estensione di classi ( esseenzialmente blocca il principio base dell'ereditarietà ) , dovrebbe essere tutto protected di default ( linguaggi moderni la private diventa come la protected )

Set -> sequenza ordinata senza doppioni non random accessible , non è garantito a compile time 
Posso creare implementazioni differenti dei set , per non mettere duplicati potrei usare una contains , usando equals , equals però dipende dalla struttura dati utilizzata i set invece possono essere implementati con diverse strutture dati 

*stub* sono wrapper piccoli ossia che chiamano solo una funzione o ritornano un dato etcc

# 07/03/2024

*stub* -> wrapper molto semplice -> chimare altra funzione 
quando eredito -> stub automatiche 

se devo togliere qualcosa dovrei ristrutturare il tutto con classi astratte 
, una classe astratta può avere metodi implementati , può avere campi , interfacce non hanno campi nè implementazione ( apparte metodi default )

se non metto campi classi astratte == interfaccia

potrei fare classe astratta superclasse di list

abstract class tutto protected

incapsulamento -> invece di ereditare faccio le stub sul mio dato

*naming* di classi etcc -> quello che fa , abstractcollection è una arraylist senza qualcosa , arraylist contiene parola list anche se ci fermiano a implementare collection , 
Abstract -> va lasciata prima del nome 

array che possono cambiare dimensione ad un array -> resizable array 
possiamo togliere nome dell'interfaccia che implemento perchè viene fuori dalla documentazione

confronto hash -> confronto elementi tra hash

hash code -> hash dei campi e fare xor ( somma lo renderebbe commutativo )

abstract set che ha tutte le cose che hanno i set -> tipo add etcc 

super non può essere usato dentro una interfaccia 
abstract in pratica condensano servizi comuni di classi più in basso 

# 12/03/2024

*Sorted set* 

è un set no dup + iterabile + è ordinata
ci accorgiamo che è sorted solo se ci iteriamo sopra ( utilizzatore della libreria )
sorted set -> potresti perdere dettaglio subsumendo a set es -> *interface* sottointerfaccia di *set*
sorted -> caratteristica univoca per vari set implementati in modo differente (hash,strucutral) etcc 



```java

```

*extends* -> in java 3 cose 
1. quando extends interfaccia sto ereditando un' interfaccia -> faccio una sottointerfaccia
2. quando extends class una certa classe è figlia di 
3. `{java}<T extends ....>` T deve essere almeno del tipo a dx , non sto ereditando nulla 

ogni keyword occupa uno spazio per un nome di variabile , meno keyword possibili in un linguaggio

# 14/03/2024

sort con double , int -> tipi primitivi -> implementazione ideintica a meno di bynding specifici
il < non è polimorfo in java può essere overloadato ma non su tipi qualsiasi ( in c++ è polimorfo )

i generic solo reference type -> posso fare solo 1 versione della sort  polimorficamente , veci del < che fa da operatore di confronto `Comparable` , poichè non posso overloaddarlo nei tipi generici


non posso passare metodi 
< è una function binary -> ret bool , per astrarlo in oggetti , l'entità è un interfaccia che da come contratto solo il metodo che compara -> solo 1 metodo -> il  programmatore è invitato a implemetnare solo 1 , static possono essere tolti , default non serve che li implemento se non voglio , equals di object che ha implementazione di default

comparable extends solo se creo una classe / oggetto che io sono un'entità confrontabile 
comparatore si presta meglio per essere costrutito al volo per confrontare 2 oggetti 

compare non statico poichè mi serve perchè abbia dynamic dispatching , meglio anche perchè posso avrere uno stato

shadowing varaibile con stesso nome di una in scope 

funzioni binarie 2 modi : 
+ `method(T ,T)` , può essere ternaria con il this
+ `method(T)` la seconda è this

map asoscia key->vaklue


# 19/03/2024

*Mappe* : 

get data key ritorna valore 
set -> dato key+value -> add in una mappa
array di fatto è una mappa con tipo chiave è un int e valore quello che vogliamo

mappa = lista di coppie 

pairmap -> fatta con coppie
K , V type parameter -> prima volta che li dichiaro 

non ha senso che estendiamo collection perdi la chiave come polimorfismo 
also add all prende collection potrei mettere un array -> non va in map also add ha solo V non la key , contains chiederebbe un valore strange
remove sarebbe by V , se ci sono più key ???????

collection pair<K,V> -> collection di pair 
se scorro con iteratore ritorno coppie K,V

iterare in Collection<Pair<K,V>>

Iterable di pair K , V ->  sono tenuto ad implemetntare iterator

in collection add diventa alias della put 
contains di pair K ,V -> sarebbe meglio contains by K e poi ritorna il Valore 

limiterei ad essere un Iterable 

# 21/03/2024

SU codice
# 26/03/2024

ritornare un oggetto non è eseguire il suo codice , signifi intanziare un oggetto O(1) -> solo quando chiamo i metodi di quell'oggetto allora la complessità si srotola

---

Programmazione *pseudofunzionale ad oggetti*

cpp multiparadigma : ad oggetti e imperativo , posso ignorare gli oggetti 
java class centrico , ma se uso solo metodi statici senza campi ne constructor $\to$ non è di fatto programmare ad oggetti 

```java
public class myMath{

	public static double mult(double x){
		return x*x;
	} 

}
```
Utility class ( ex java.util.Arrays )

python -> mutliparadigma -> object system ma posso non usarlo

java : per definire un nuovo tipo di dato -> costrutto class
cpp : struct , class (sinonimi)

tipi prodotto / somma 

data type non object oriented 

```cpp
class person{

	string name; 
	int age;

}

persona pippo; // chiama default constructor
```

non finisce in virtualtable in cpp solo i metodi virtual vanno in virtualtable , come ovverride

```c
struct {

	char* name
	int age

}

persona pippo;
// in c comunqe c'è default constructor
```

object oriented -> polimorfismo -> dynamic dispatching ( deve esserci la virtual table )

```java
class Person{

	public string name; 
	public int age;

}

Person pippo; // non ho metodi che vanno in virtual table -> non sto programmando ad oggetti anche se il linguaggio lo è
```

paradigmi di programmazione è imperativo , paradigma imperativo solo per l'assegnamento se non c'è non è imperativo 

```java
public static void main(String[] args){
	int x = 10; // binding , inizializzazione , assegno un nuovo nome + valore iniziale
	x = 3; // assegnamento , x esiste già , modifico il valore del nome   
}
```

Linguaggi senza assegnamento > funzionali ( in pratica lo implementano ) , lambda in linguaggi ad assegnamento vengono da quelli funzionali

in java caratteristiche che vengono dai linguaggi funzionali -> **lambda** 

**lambda** : invenzione dei funzionali : 
	Funzione anonima ( haskell , Camel , F# , Scala , Closure )
```java
public class lambda{
	int (int x) {x++;}
}
```

funzione anonima -> input , output e implementazione 
può essere chiamata dando un nome dopo

C funzioni di ordine superiore 


CPU hanno solo il necessario -> ciò che è necessario per programmare 

# 28/03/2024

**lambda**

```java
public static <T> void foreach(Collection<T> , Function<T , void> f){

} 
```

funzione di ordine superiore , prende collection , funzione , applica la funzione ad  ogni el della collection

funzione di ordine superiore -> funzione di ordine superiore

tipo di una funzione : `Function` -> interfaccia che definisce tipo di una funzione che prende qualcosa e ritorna qualcos'altro

`Function<Integer , Integer>` ( gli integer sono tipi di input - output )
```java
Integer f( Integer x ) {return x+1}
```
può essere boxata con tipo `Function`

```java
Function test_f = 
```

entità del primo ordine ( manipolabile come un espressione ) -> hanno un tipo e possono essere messe in variabili 
-> tutti gli oggetti sono entità del primo ordine 

in c posso usare pointer a funzione per trasportare una funzione 
```c
int f(double x) {...}
// tipo di "funzione"
int(*)(double) g = f;
// nome della variabile dopo tipo di ritorno ( immmerso nel tipo )
// star agganciato alla variabile non al tipo
int(*g)(double) = f;
```

funzione che svolga foreach in c 
```c
void foreach(int* arr, size_t len, void(*f)(int)) // for che ad ogni iterazione fa qualcosa con la funzione che prende un el e fa qualcosa
{
	for(int i = 0; i<len; i++){
		f(arr[i]);
	}
}

void printint(int s){
	printf("%d\n",s);
}

int main(){
	int A[10];
	foreach(A,10,printint)
}
```

programmazione funzionale , funzioni generali che poi prendono una funzione particolare
```c
std::quesort() // prende un function pointer che prende funzione che dice quale dei due el è >
```

>[!warning] 
>ogni volta devo creare una funzione etcc , non posso dichiararla direttamente inline
```c
foreach(A,10,printf("\n"))
// funzione senza nome -> lambda non presenti in c
```

lambda funzione senza nome , "passi un pezzo di codice" che passi per argomento
```java

```

si possono simulare la lambda con le anonymus class 