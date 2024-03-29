# Comunicazione tra processi

I processi all'interno di un sistema operativo sono sempre in competizione per l'*utilizzo delle risorse*difatto la *gestione delle risorse* è il compito principale del sistema operativo 

Se non vengono gestite correttamente si potrebbe provocare : 
+ **Starvation** : 
	Un processo è bloccato indefinitivamente a causa di altri processi che monopolizzano le risorse 
>[!example]
>fork-bomb
```c
int main(){
	while(1)
		if(fork()<0)
			perror("errore fork");
}
```
+ **I/O** :
	Diminuzione delle prestazioni a causa dell'accesso di una perifericha di input/output da parte di più processi

Il sistema operativo deve *gestire la competizione* ( *race condition* ) per le risorse comuni in modo da ridurre le interferenze e per garantire la correttezza 

>[!example]
>+ Le stampe su una stampante devono essere accodate e non "mischiate"
>+ L'accesso al disco deve garantire che diversi processi non scrivano inavvertitamente sullo stesso blocco
>+ Garantire l'equietà per la distribuzione del tempo di CPU ai processi

Il sistema operativo *virtualizza* l'hardware in modo da dare l'impressione ad ogni processo di avere l'intera macchina a disposizione , i processi non si accorgono di altri processi in esecuzione contemporaneamente ( possiamo vedere solo se la nostra esecuzione è rallentata ma non conosciamo cosa causa questo rallentamento )

## Cooperazione tra Processi 

I processi vorrebbero comunicare nei seguenti casi : 
+ **Condivisione** : 
	Quando si vuole condividere delle informazioni ( es google docs ) 
+ **Prestazioni** :
	Potremmo utilizzare calcolo parallelo in architetture multi-core per ottimizzare le prestazioni di certi programmi
+ **Modularità** : 
	Il programma viene suddiviso in vari sottoprocessi con differenti compiti più semplici
+ **Multitasking** :
	Svolgimento di compiti differenti contemporaneamente ( esempio voglio stampare un documento e modificarlo mentre viene stampato )
+ **Replicazione** :
	Quando si necessita dell'esecuzione simultanea di diverse istanze dello stesso processo il processo viene replicato su più processi o thread ( un server si replica per servire diversi utenti )

## Modelli di comunicazione tra processi

Vi sono due modelli principapli di comunicazione tra processi : 
+ **Message passing**
	I *processi* o *thread* si scambiano informazioni tramite messaggi ( di riderimento per la comunicazione tra messaggi )
	![[Pasted image 20240216165331.png]]
+ **Shared memory** 
	I *processi* o *thread* condividono dati in memoria e accedono in lettura e scrittura ai dati condivisi ( modello di riferimento per i *thread* in quanto questi condividono la memoria del processo )
	![[Pasted image 20240216165529.png]]

### Message passing

I processi dispongono di funzioni che gli permettono di comunicare tra di loro : 
+ `{c} send(m)`
	Invia il messaggio `m`
+ `{c} recieve(&m)`
	Riceve un messaggio e lo salva in `&m`

Queste due funzioni primitive vengono realizzate attraverso chianate di sistema ( *System Call* ) dette *InnerProcess Communication* ( **IPC** )

Il mittente e destinatario possono essere comunicati *direttamente* o *indirettamente*
#### Nominazione Diretta

Il *mittente* e *destinatario* sono nominati esplicitamente nelle funzioni primitive
+ `{c}send(P,m)` 
	Invia il messagio `m` a `P`
+ `{c}recieve(Q,&m)`
	Riceve un messaggio da `Q` e lo salva in `m`

Necessitiamo di conoscere la reciproca identità , esiste una variante in cui si riceve da qualsiasi utente
+ `{c}recieve(&Q,&m)`
	Questo riceve un messaggio `m` da un qualsiasi utente

**Vantaggi** :
+ La nominazione diretta è molto semplice e permette la comunicazione diretta tra coppie di processi 

**Svantaggi**  : 
	Necessitiamo un accordo sui nomi dei processi
	Il *PID* ( *Process ID* ) non è adatto poichè questo viene allocato dinamicamente ai processi e non possiamo prevederne il valore 
	Per questo l'implementazione risulta essere complicata a meno che i processi non siano in relazione stretta di parentela genitore-figlio , in questo caso il genitore conosce i *PID* dei figli  
#### Nominazione Indiretta

Per ovviare ai difetti della nominazione diretta si utilizza una nominazione basata su **Porte** ( **mailbox** ) , queste vengono gestite dal sistema operativo che permettono la loro creazione e distruzione

+ `{c}send(A,m)` 
	Invia il messaggio `m` sulla *porta* `A`
+ `{c}recieve(A,&m)`
	Riceve un messaggio dalla *porta* `A` e lo salva in `m`

In questo modo non necessitiamo di conoscere il nome dei processi ma solo quello della *porta*

![[Pasted image 20240216173416.png]]

>[!note]
>Cosa accade quando due processi cercano di inviare/ricevere messaggi verso/dalla stessa *porta* ?
>Il sistema operativo si occuperà di gestire la competizione ( *race condition* ) 
>Spesso si associa una *porta* ad un *processo* ( creatore e possesore della porta ) che è l'unico a leggere dalla porta evitando così la *competizione* in ricezione , per evitare la competizione in arrivo basterà accodarli in modo lineare sulla *porta*

>[!note]
>In Unix le *pipe* implementano un meccanismo di scambio di messaggi a nominazione indiretta
#### Comunizazione sincrona e asincrona 

L'invio e ricezione dei messaggi può essere *sincroni* o *asincroni* , vediamo le varie combinazioni

+ *send sincrona* :
	Un messaggio viene inviato solo quando la corrispondente `recieve` viene eseguita 
	La `send` è bloccante finquando il messaggio non viene ricevuto
+ *send asincrona* :
	Il messaggio viene inviato indipendentemente dalla `recieve` , si utilizza un `buffer` ( una *coda* ) in cui depositare temporaneamente il messaggio  
+ *recieve sincrona* :
	Il messaggio viene ricevuto solo se presente , la `recieve` è bloccante finchè non c'è un messaggio da leggere 
+ *recieve asincrona* :
	La `recieve` ritorna un messaggio se presente o NULL se non ci sono messaggi da ricevere 

>[!example]
>*Produttore-consumatore*
>La comunicazione a scambio di messaggi è molto adatta nelle situazione in cui un processo *produce* un dato e un altro lo *consuma*
>UNIX inoltre utilizza nominazione indiretta con send asincrona e recieve sincrona

# Creazione di processi 

La creazione di un processo richiede alcune operazioni da parte del sistema operativo : 
+ Creazione di un nuovo ID ( *PID* - *Process Identifier* )
+ Allocazione di Memoria ( per il codice e i dati )
+ Allocazione di altre risorse ( stdin , stdout , dispositivi di I/O )
+ Gestione delle informazioni sul nuovo processo ( es. la priorità )
+ Creazione del *PCB* ( *Processo Control Block* ) contenente le informazioni del processo
## Processi in UNIX

Un processo è sempre creato da un'altro processo tramite una chiamata di sistema ( `{c}fork` ) 
Fa eccezione il processo `init` ( $pid=1$ ) che viene creato al momento del boot ( non ha un padre)

Il processo creante è detto *parent* mentre il processo creato *child* , questo crea una struttura di parentela ad albero :

![[ParentelaProcessiUNIX.excalidraw]]
### Relazioni Dinamiche

Dopo la creazione di un nuovo processo il genitore *attende* l'esecuzione del figlio

>[!example]
>`{bash}sleep 5` 
>L'esecuzione di `{bash}sleep` all'interno di una shell questa sarà il suo genitore e quindi dovremmo attenddere 5 secondi per continuare ad utilizzare la shell

>[!note]
>`ctrl+c` termina il processo in esecuzione ( metodo standard ) oppure il comando viene gestito dal programma

Il processo genitore potrebbe continuare l'esecuzione mandando il processo figlio ad eseguire in background 

>[!example]
>`{bash}sleep 5 &`
>`{bash}&` indica che il processo deve essere messo in esecuzione in background 

In questo caso i due processi sono in esecuzione in modo concorrente ( o per *time-sharing* in single core o su più core in un CPU multicore )

Il processo che termina verrà comunicato solo se interagiamo con la shell : 
```bash
-> sleep 5 &
[1] 234                       // -> indica il pid del processo in background 
-> ps                         // -> ci fa vedere i processi in esecuzione
PID TTY          TIME CMD
14695 pts/1    00:00:00 bash
17589 pts/1    00:00:00 sleep
17590 pts/1    00:00:00 ps
-> ls
[1]+ done      sleep 5        // -> indica il comando che è terminato
```

>[!note]
>`{bash}ps`
>Indica di default solo i processi associati al terminale da cui viene lanciato
>`{bash}-o` indica chè output vogliamo
>>[!example]
>>se vogliamo osservare il *pid* o *ppid* ( *Parent Process Identifier* ) utilizzeremo il comando
>>`{bash} ps -o pid,ppid`
>>Questo ci farà osservare che la shell sarà padre sia di `{bash}ps` che di `{bash}sleep`

Potrebbe essere utile che un processo si dissocia dal suo genitore , ad esempio i processi *Daemon* sono processi che devono rimanere attivi fino allo shutdown del sistema , necessitano quindi dissociarsi dalla shell per non essere terminati alla chisura del terminale

>[!note]
>Possiamo utilizzare `ctrl+Z` per sospendere il processo in *foreground* e sucessivamente utilizzare `{bash}bg` per rimandarlo in esecuzione in *background* , `{bash}fg` per riprendere l'esecuzione in *foreground*
### Relazione di contenuto

Ci sono due possibilità :
+ Il figlio è un duplicato del genitore ( in UNIX )
+ Il figlio esegue un programma differente ( in Windows )
### Fork

La chiamate a sistema `fork` permette di creare un processo duplicato del processo genitore 

>[!note]
>`fork` appartiene allo standard *POSIX* ( *Portable Operating System Interface* ) di *IEEE* ( *Institute of Eletrical and Elctronics Engineers* ) sraà quindi utilizzabile in tutti i sistemi che supportano *POSIX* 

La chiamata `fork` crea un nuovo processo che :
+ condivide l'area codice del processo genitore ( essendo immutable , read-only )
+ Utilizza una *copia* dell'area dati del processo genitore ( potrebbero essere modificati dal processo se non facessimo una copia potrebbe portare ad infonsistenze )

Per sapere che abbiamo creato un processo figlio basta osservare il valore dato in ritorno dalla chiamata `fork` :
+ se il valore è $0$ allora so di essere il processo figlio
+ se il valore è $>0$ allora sappiamo che siamo il padre poichè il valore di ritorno sarà il *pid* del figlio

>[!note]
>Il padre di un figlio non ha alcun modo per sapere il *pid* del figlio se non al momento della creazione attraverso il valore ritornato dalla `fork`

![[Pasted image 20240221181800.png]]

Lo schema di base per l'utilizzo di una `fork` è il seguente : 
```c
pid_t pid = fork(); // pid del figlio
if ( pid < 0 )
	perror("fork error");
else if ( pid == 0 ){
	// codice del figlio
} else {
	// codice del genitore
	//^ codici eseguiti contemporanemente
}
// codice svolto sia dal genitore che il figlio
```

>[!example]
>Esempio concreto
```c
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>

int main() {
  pid_t pid;
  
  printf("Prima della fork. pid = %d, pid del genitore = %d\n",getpid(), getppid());
  
  pid = fork();
  if ( pid < 0 )
    perror("fork error"); // stampa la descrizione dell'errore
  else if (pid == 0) { 
    // figlio
    printf("[Figlio] pid = %d, pid del genitore = %d\n",getpid(), getppid());
  } else { 
    // genitore
    printf("[Genitore] pid = %d, pid del mio genitore = %d\n",getpid(), getppid());
    printf("[Genitore] Mio figlio ha pid = %d\n",pid);
    sleep(1); // attende 1 secondo
  }
  // entrambi i processi
  printf("PID %d termina.\n", getpid());
}
```
>[!example] .
>Questo dà il seguente output : 
```bash
$ ./fork
 Prima della fork. pid = 25267, pid del genitore = 329
 [Genitore] pid = 25267, pid del mio genitore = 329
 [Genitore] Mio figlio ha pid = 25268
 [Figlio] pid = 25268, pid del genitore = 25267
 PID 25268 termina.
 PID 25267 termina.
$
```

>[!note]
>`{c}pid_t` è un signed integer in molti sistemi ma potrebbe essere un altro tipo in altri sistemi

>[!note]
>`{c}getpid` è una funzione per ricevere il proprio *pid*
>`{c}getppid` è una funzione per ricavare il *pid* del padre ( ossia il *ppid* )

>[!warning]
>La `fork` può fallire , in questo caso ritorna un numero negativo
>Fallisce quando finiscono le risorse , questo è molto pericoloso per il sistema operativo perchè potrebbe significare che non ci sono risorse nemmeno per il sistema operativo stesso 
>Per questo esistono dei limiti ( `{bash}ulimit` , `{bash}cgroups` ) di processi in esecuzione in modo da mantenere sempre delle risorse minimi per il sistema operativo
>>[!example]
>>Questo potrebbe causare un fallimento ( fork-bomb )
```c
int main() {
	while(1)
		if (frok() < 0)
			perror("errore fork") // il numero di processi è esponenziale poichè ogni processo crea altri processi
}
```

Cosa succede alla terminazione di un processo figlio o padre ? : 

+ Se il genitore termina prima del figlio , i figli diventano processi **orfani** 
+ Se il figlio termina prima del genitore questo dovrebbe accorgersi della sua terminazione e raccogliere le informazioni del suo *PCB* ( *Process Control Block* ) , se questo non viene svolto dal processo genitore si dice che il processo figlio è un processo **zombie** ( questi vengono segnati come `<defunct>` se facciamo un `{bash}ps` )

I processi *orfani* vengono "adottati" dal processo `init` che ciclicamente raccoglie le loro informazioni e libera la memoria 
Se il genitore di un processo *zombie* termina questo diventa *zombie orfano* che viengono anch'essi "adottati" dal processo `init`

>[!example]
>>[!todo]
#### Esempi

Visto che non possiamo fare alcuna assunzione sullo scheduler che utilizza il sistema non possiamo nemmeno fare alcuna assunzione sul modo in cui due processi vengono eseguiti in modo parallelo : 

Per visualizzare come i processi si alternano nell'esecuzione possiamo scrivere il seguente programma : 

```c
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>

int main() {
  pid_t pid;
  int i;
  
  pid = fork();
  if ( pid < 0 )
    perror("fork error"); // stampa la descrizione dell'errore
  else if (pid == 0) { 
    while(1) {
      for (i=0;i<10000;i++) {} // riduce il numero di printf
      printf("Figlio: pid = %d, pid del genitore = %d\n",getpid(), getppid());
    }
  } else { 
    while(1) {
      for (i=0;i<10000;i++) {} // riduce il numero di printf
      printf("genitore: pid = %d, pid di mio genitore = %d\n",getpid(), getppid());
    }
  }
}
```

Se eseguiamo questo codice in un sistema *multicore* questi verrano eseguiti in modo parallelo su due core differenti , per questo gli output saranno alternati ( a meno di buffering dell'output ) 
Mentre se il sistema è *single-core* i due processi vengono eseguiti in time-sharing portando ad un output suddiviso in blocchi di esecuzione dei due processi  

>[!todo]

### Exec

Per eseguire un programma differente da quello che ha effettuato la `frok` si utilizzala chiamata di sistema `exec`

Questa chiamata **sostituisce** *codice* e *dati* di un processo con quelli di un programma differente

![[Pasted image 20240222165718.png]]

#### Copy-on-Write

Da notare come la `exec` "butta via" la copia dei dati creata dalla `fork` , questo è chiaramente *inefficente* soprattutto quando l'`exec` viene eseguita subito dopo una `fork`

Per rendere questa operazione più efficente viene copiata inizialmente dalla `fork` solo la *page-table* e le pagine contententi i dati sono etichettate come *read-only*
Quando si tenta di scrivere queste pagine genrea un errore che verrà gestito dal kernel :
+ Copia al volo la pagina fisica e aggiorna la *page-table* in modo da farla puntare alla nuova copia
+ Imposta le pagine a *read-write* in quanto da ora in poi le due copie sono indipendenti 

>[!note] 
>Se si esegue la `fork` e subito `exec` non avviene nessuna scrittura e quindi nessuna pagina viene effettivamente copiata ( avviene solo quando facciamo una scrittura )

#### Sintassi

L'`exec` ha diverse varianti che si differiscono in base al : 
+ formato degli argomenti ( lista o array `argv[]` )
+ utilizzo o meno del path della shell 

```c
execl("/full/shell/path/command", arg0, arg1, ..., NULL) 
execlp("command", arg0, arg1, ..., NULL)
execv("/full/shell/path/command", argv[])
execvp("command", argv[])
```

>[!note] 
>La presenza della `p` alla fine del nome della `exec` indica che viene utilizzato il path della shell ( non necessitiamo di scrivere l'intero path ) 

>[!note]
>Le prime due varianti prendono in input un lista di argomenti terminata da `NULL` ( il primo argomento contiene il nome del file associato al programma da eseguire ) , mentre le altre due prendono i parametri sotto froma di un array di stringe

#### Valore di ritorno

L'`exec` ritorna solamente in caso di errore ( valore $-1$ ) , in caso di successo il vecchio codice è completamente sostituito dal nuovo e non è più possibile tornare al programma originale

```c
#include <stdio.h>
#include <unistd.h>
int main() {
    printf("provo a eseguire ls\n");

    execl("/bin/ls","/bin/ls","-l",NULL);
    // oppure : execlp("ls","ls","-l",NULL);

    printf("non scrivo questo! \n");
    // questa printf non viene eseguita se la exec va a buon fine
}
```
Darà in output :
```bash
provo a eseguire ls
total 16
-rwxr-xr-x 1 focardi focardi 6619 2008-03-05 17:02 a.out
-rw-r--r-- 1 focardi focardi  226 2008-03-05 17:02 exec1.c
-rw-r--r-- 1 focardi focardi  225 2008-03-05 17:02 exec1.c
```

Se invece runniamo il seguente comando : 
```c
execlp("ls2","ls2","-l",NULL);
```
Riitorneremo un errore in quanto `{bash}ls2` non è un comando riconosciuto , in questo caso infatti il risultato sarà : 
```bash
provo a eseguire ls
non scrivo questo!
```
Poichè la `exec` è andato in errore

>[!note]
>Risulta essere buona norma in *c* testare i codici di errore di chiamate a libreria o a sistema 
```c
if (execlp("ls2","ls2","-l",NULL) == -1) {
      perror("errore durante la exec");
      // eventualmente si esce: exit(EXIT_FAILURE);
}
```
Questo stamperà l'errore che è stato ritornato ( presente nel *PCB* ) :
```c
provo a eseguire ls
errore durante la exec: No such file or directory
non scrivo questo!
```

>[!warning]
>Qualora si verificassero errori all'interno del programma invocato l'`exec` *non fallisce* , quindi non ritorna e non esegue nessuna istruzione del programma che l'ha invocata
```c
if (execlp("ls","ls","-z",NULL) == -1) {
      perror("errore durante la exec");
      // eventualmente si esce: exit(EXIT_FAILURE);
}
```
Avremo il seguente output :
```bash
provo a eseguire ls
ls: invalid option -- z
Try `ls --help' for more information.
```

#### Example : shell

>[!todo]
>#todo
### Terminazione di un processo

La terminazione di un processo rilascia le risorse allocate dal *SO* al momento della creazione ( memoria e file aperti ) e *segnala* la terminazione al genitore : alcune informazioni di stato ( *PCB* ) vengono messe a disposizione al processo genitore e rimangono memorizzate finchè non sono processate 

Il *SO* mantiene almeno : 
+ *pid*
+ lo stato di terminazione
+ il tempo di CPU utilizzato dal processo 

Esistono 2 chiamate a sistema pertinenti alla terminazione di un processo : 
+ `exit` : termina il processo 
+ `wait` : attende la terminazione di un figlio ( se uno dei figli è uno *zombie* ritorna subito senza bloccarsi )

>[!note]
>Un processo può anche terminare in modo anomalo a causa di un errore o perchè terminato dal *SO* o da altri processi
#### Sintassi

+ `{c}exit(int stato)` : termina il processo ritornando lo `stato` al genitore ( gli stati possono essere `EXIT_FAILURE` e `EXIT_SUCCESS` reciprocamente uguali ad 1 e 0 )
+ `{c}pid = wait(int &stato)` : ritorna il `pid` e lo `stato` del figlio che ha terminato 
	Si utilizza `{c}wait(NULL)` se non interessa lo stato
	Se non ci sono figli ritorna $-1$

#### Valore di ritorno della wait

Lo stato ritornato dalla `wait` deve essere gestito con opportune macro : 

+ `{c}WIFEXITED(status) == true` se il figlio è uscito correttamente con una `exit`
	`{c}WEXITSTATUS(status)` ritorna gli 8 bit di stato passati dalla `exit` 
	Esempio : 
```c
if (WIFEXITED(status))
    printf("OK: status = %d\n",WEXITSTATUS(status));
```

+ `{c}WIFSIGNALED(status)==true` se il figlio è stato terminato in modo anomalo
	`{c}WTERMSIG(status)` ritorna il *segnale* che ha causato la terminazione 
	Esempio : 
```c
if (WIFSIGNALED(status))
    printf("ANOMALO: status = %d\n",WTERMSIG(status));
```

+ Esistono ulteriori macro per lo *stop* e *resume* , utili per il tracing dei processi ( `{c}WIFSTOPPED` , `{c}WSTOPSIG` , `{c}WIFCONTINUED`)

##### Wait per un processo particolare

Se si vuole attendere un processo particolare ( o precessi appartententi a un gruppo particolare ) si può utilizzare la chiamata a sistema : 
```c
pid = waitpid(pid2, &stato, options) 
```

Questo attende il processo `pid2` ( valori $\le 0$ permettono di attendere gruppi di processi ) 
Se `{c}pid2 == -1` attende un qualsiasi figlio , risulta uguale alla `wait`

### Segnali

I **Segnali** sono una forma minima di comunicazione tra processi ( tecnicamente sono *interruzioni* causate da svariati eventi ) 
>[!example]
>+ `ctrl-c` ( `SIGINT` ) viene generato da terminale e viene utilizzato per terminare l'esecuzione di un programma in esecuzione all'interno di una shell
>+ Eccezioni ( divisioni per 0 , *segmentation fault* ( riferimento ad area di memoria non allocata ) ) 
>+ Segnali esplicitamente inviati da un processo all'altro 
>+ Eventi *asincroni* che vengono notificati ai processi : `SIGALARM` ( utilizzato per settare un timer e poi essere svegliato ) ( utilizzato nello scheduling , soprattutto nel modello time-sharing in cui lo scheduler mette in `exec` un processo e setta il `SIGALARM` per svegliarlo dopo l'intervallo di tempo assegnato a quel processo per metterlo in pausa e mandare in `exec` un'altro processo )

Cosa possiamo fare quando riceviamo un segnale : 
+ Ignorarlo
+ Gestirlo
+ Lasciare questo compito al gestore del sistema operativo

Alcuni segnali *POSIX* ( *Portable Operating System Interface* ) supportati su Linux :
Ecco il risultato dato da `man 7 signal`

```bash
First the signals described in the original POSIX.1-1990 standard.

Signal     Value     Action   Comment
-----------------------------------------------------------------------
SIGHUP        1       Term    Hangup detected on controlling terminal
                              or death of controlling process
SIGINT        2       Term    Interrupt from keyboard     <== ctrl-C
SIGQUIT       3       Core    Quit from keyboard          <== ctrl-\
SIGILL        4       Core    Illegal Instruction
SIGABRT       6       Core    Abort signal from abort(3)
SIGFPE        8       Core    Floating point exception
SIGKILL       9       Term    Kill signal                 <== kill -9 (da shell)
SIGSEGV      11       Core    Invalid memory reference
SIGPIPE      13       Term    Broken pipe: write to pipe with no readers
SIGALRM      14       Term    Timer signal from alarm(2)
SIGTERM      15       Term    Termination signal          <== kill (da shell)
SIGUSR1   30,10,16    Term    User-defined signal 1
SIGUSR2   31,12,17    Term    User-defined signal 2
SIGCHLD   20,17,18    Ign     Child stopped or terminated <== gestito da wait()
SIGCONT   19,18,25    Cont    Continue if stopped
SIGSTOP   17,19,23    Stop    Stop process
SIGTSTP   18,20,24    Stop    Stop typed at tty
SIGTTIN   21,21,26    Stop    tty input for background process
SIGTTOU   22,22,27    Stop    tty output for background process

The  signals SIGKILL and SIGSTOP cannot be caught, blocked, or ignored.
```

Ecco alcuni segnali : 
+ `SIGHUP` : 
	Viene inviato a un processo quando il terminale a cui è associato viene chiuso 
+ `SIGINT` : 
	Viene inviato a un processo digitando `ctrl-C` per interromperlo
+ `SIGQUIT` :
	Viene inviato per far terminare un processo tramite `ctrl-\` ( fa anche il core-dump del processo ( ossia vengono salvati i dati ) )
+ `SIGILL` , `SIGABRT` , `SIGFPE` , `SIGSEGV` : 
	Corrispondono ad azioni del precesso stesso : 
>[!example] 
>+ Istruzione Illegale
>+ Invocazione di `abort`
>+ Errore aritmetico
>+ Riferimento a memoria illegale
+ `SIGKILL` , `SIGTERM` : 
	Sono segnali inviati da un processo ad un altro utilizzati per terminare un processo 
>[!example]
>Questi segnali possono essere inviati tramite shell grazie al comando `{bash}kill -9 pid` oppure `{bash} kill pid` 
>`SIGKILL` termina *sempre* un processo 
>`SIGTERM` può essere gestito o ignorato dal processo 
+ `SIGPIPE` : 
	Inviato ad un processo che prova a scrivere su una *pipe* che non ha lettori 
+ `SIGALRM` : 
	Un timer che "sveglia" un processo simile al timer usato dallo scheduler per implementare il time-sharing
+ `SIGCHLD` : 
	Viene inviato a un processo quando un suo processo figlio termina ( utilizzato per svegliare un processo che ha invocato la `wait` altrimente viene ignorato )

Comportamenti di **default** : 
+ `Term` : l'azione di *default* è quella di *terminare* il processo
+ `Ign`   : l'azione di *default* è quella di *ignorare* il segnale
+ `Core` : l'azione di *default* è quella di *terminare* il processo e *dump core* ( salvare stato del processo )
+ `Stop` : l'azione di *default* è quella di *fermare* il processo
+ `Cont` : l'azione di *default* è quella di *continuare* il processo se è al momento *fermato*

#### Esempio utilizzo di segnali : `alarm`

La chiamata di sistema `{c}alarm(n)` manda un `SIGALRM` dopo $n$ secondi .
Il default handler termina il programma
>[!note]
>La shell analizza lo stato di uscita del programma e stama il motivo della terminazione ( in questo caso stamperebbe "Alarm clock" )

>[!example]
```c
#include <unistd.h>
int main()
{
    alarm(3); 
    while(1){} 
}
```

La shell setta l'allarme tra 3 secondi , nel frattempo svolge il ciclo while e alla fine dei 3 secondi il processo termina visto che `alarm` ha come azione di *default* quello di terminare il processo

```bash
$ ./alarm
 <... dopo 3 secondi....> 
 Alarm clock
$
```

#### Impostare il gestore dei segnali tramite `signal`

Tramite la call di sistema `signal` è possibile cambiare il gestore dei segnali 
Questa prende come parametri un *segnale* e un *puntatore a funzione* che da quel momento diventerà il nuovo gestore del segnale

>[!example]
```c
#include <stdio.h>
#include <signal.h>
#include <unistd.h>

void alarmHandler()
{
    printf("questo me lo gestisco io!\n");
    alarm(3); // ri-setta il timer a 3 secondi
}

int main() { 
    signal(SIGALRM, alarmHandler);
    alarm(3);
    while(1){}
}
```

Dopo 3 secondi arriva il segnale `SIGALRM` , a questo punto avendo rimappato l'*handler* verrà chiamata la funzione `{c}alarmHandler()` che stampa `{c}"questo me lo gestico io!"` e reimposta l'allarme doppo 3 secondi . A questo punto la funzione ritorna al punto in cui il programma era stato interrotto ( ossia all'interno del ciclo `{c}while` ) , qui attenderemo che arrivi il sucessivo segnale che lancerà di nuovo `{c}alarmHandler()` etcc...

```bash
$ ./alarm
 <... Aspetto 3 secondi ...>
 questo me lo gestisco io!   <=== dopo 3 secondi
 questo me lo gestisco io!   <=== dopo 3 secondi
 questo me lo gestisco io!   <=== dopo 3 secondi
 .......
```

>[!note]
>`SIGALRM` , `SIGKILL` etcc sono *macro* di c e quindi potrebbero essere sostituiti da i loro valori numerici

##### Parametri particolari della `signal`

Possiamo passare alla `signal` le costanti `SIG_IGN` o `SIG_DFL` al posto della funzione handler per indicare rispettivamente : 
+ che il segnale va ignorato
+ che l'handler è quello di default di sistema

Il valore di ritorno di `signal` è :
+ `SIG_ERR` in caso di errore
+ l'handler precedente in caso di successo 

>[!note]
>L'implementazione di `signal` può essere differente da diversi sistemi `POSIX` 
>Per questo si utilizza `sigaction` per un'implementazione più consistente nelle varie versioni `UNIX` 
>>[!example]
>>La `signal` originale in `UNIX` fa il reset dell'handler a `SIG_DFL` ogni volta che viene ricevuto il segnale ( si può ottenere questo comportamento con il flag `--ansi` ) 

>[!example]
>Proteggersi da `ctrl-c`
```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>
int main() {
    void (*old)(int);
        
    old = signal(SIGINT,SIG_IGN);
    printf("Sono protetto!\n");
    sleep(3);
        
    signal(SIGINT,old);
    printf("Non sono più protetto!\n");
    sleep(3);
}
```

>[!note]
>Salviamo il primo handler di ritorno , quando vogliamo ritornare all'hander originale è sufficente passare l'handler salvato a `signal` 

Se runniamo il programma per 3 secondi non potremmo effettuare `ctrl-c` , appena reimpostato il vecchio gestore invece `ctrl-c` funzionerà

```bash
$ ./ctrlc
 Sono protetto!
 <ctrl-c>
 <ctrl-c>
 <ctrl-c>        <==== nessun effetto
 Non sono più protetto!
 <ctrl-c>        <==== esce!
```
##### Sospensione e ripristino di processi tramite `kill`

La chiamata a sistema `kill` manda un segnale *qualsiasi* ad un processo.

>[!example]
>Utilizziamo la chiamata a sistema `kill` per sospendere , ripristinare e terminare un processo
```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <signal.h>
int main(){
        pid_t pid1,pid2;
        pid1 = fork();
        if ( pid1 < 0 ) {
                perror("errore fork"); exit(EXIT_FAILURE);
        } else if (pid1 == 0)
                while(1) { // primo figlio
                        printf("%d è vivo !\n",getpid());
                        sleep(1);
                        }
        pid2 = fork();
        if ( pid2 < 0 ) {
                perror("errore fork"); exit(EXIT_FAILURE);
        } else if (pid2 == 0)
                while(1) { // secondo figlio
                        printf("%d è vivo !\n",getpid());
                        sleep(1);
                        }
        // processo genitore
        sleep(2);
        kill(pid1,SIGSTOP); // sospende il primo figlio
        sleep(5);
        kill(pid1,SIGCONT); // risveglia il primo figlio
        sleep(2);
        kill(pid1,SIGINT); // termina il primo figlio
        kill(pid2,SIGINT); // termina il secondo figlio       
}
```

Quando eseguiamo il programma notiamo che il primo figlio viene sospeso per 5 secondi e che alla fine i processi figli sono terminati

```bash
$ ./kill
 6720 è vivo !
 6721 è vivo !
 6720 è vivo !
 6721 è vivo !
 6720 è vivo !
 6721 è vivo !    <==== sospende 6720
 6721 è vivo !
 6721 è vivo !
 6721 è vivo !
 6721 è vivo !
 6720 è vivo !    <==== risveglia 6720 6721 è vivo ! 6720 è vivo ! 6721 è  vivo ! >
```
#### Mascherare i segnali

A volte risulta utile bloccare temporaneamente la ricezione dei segnali per poi riattivarli , i segnali non sono ignorati ma solamente *posticipati*

>[!note]
>*POSIX* non specifica se più occorrenze dello stesso segnale debbano essere memorizzate oppure no 
>Tipicamente se più segnali uguali vengono generati , solamente uno verrà recapitato quando il blocco viene tolto
>

La chiamata a sistema `{c}sigprocmask(int action, sigset_t *newmask, sigset_t *oldmask)`
compie azioni differenti a seconda del valore del parametro `action` : 
+ `SIG_BLOCK` : l'insieme dei segnali `newmask` viene *unito* all'insieme dei segnali attualmente bloccati che sono restituiti in `oldmask`
+ `SIG_UNBLOCK` : l'insieme dei segnali `newmask` viene *sottratto* all'insieme dei segnali attualmente bloccati che sono restituiti in `oldmask`
+ `SIG_SETMASK` : l'insieme dei segnali `newmask` *sostituisce* quello dei segnali attualmente bloccati ( `oldmask` )

Per gestire gli insiemi di segnali ( che sono rappresentati dal tipo `sigset_t` ) si utilizzano le seguenti chiamate di sistema : 
+ `{c}sigemptyset(sigset_t *set)` : Inizializza l'insieme `set` all'insieme vuoto
+ `{c}sigaddset(sigset_t *set, int signum)` : Aggiunge il segnale `signum` all'insieme set 

>[!example] 
>Esempio di come bloccare `SIGINT` e poi ripristinarlo
```c
#include <stdio.h>
#include <signal.h>
#include <unistd.h>
#include <stdlib.h>
int main() {
        sigset_t newmask,oldmask;

        sigemptyset(&newmask);          // insieme vuoto
        sigaddset(&newmask, SIGINT);    // aggiunge SIGINT alla "maschera"
        // setta la nuova maschera e memorizza la vecchia
        if (sigprocmask(SIG_BLOCK, &newmask, &oldmask) < 0) {
                perror("errore settaggio maschera"); exit(1); }

        printf("Sono protetto!\n");
        sleep(3);

        // reimposta la vecchia maschera
        if (sigprocmask(SIG_SETMASK, &oldmask, NULL) < 0) {
                perror("errore settaggio maschera"); exit(1); }

        printf("Non sono piu' protetto!\n");
        sleep(3);
}
```

Se digito `ctrl-c` mentre il segnale è mascherato questo viene sospeso , appena la maschera viene rimossa il segnale è ricevuto dal processo che viene interrotto ( è diverso da quello che abbiamo fatto in precedenza poichè ora non modifichiamo il gestore del segnale )

```bash
$ a.out
 Sono protetto!
 <ctrl-c>
 <ctrl-c>
 <ctrl-c>        <==== per 3 secondi nessun effetto
				 <==== esce appena la maschera viene tolta (senza dare  ulteriori ctrl-c)!
```

>[!note]
>Tramite la chiamata a sistema `sigpending` è possibile ottenere l'insieme dei segnali "pendenti" ( ossia quei segnali non ancora inviati al processo , quelli mascherati )

>[!note]
>`{c}sigprocmask`  non aderisce allo standard *ANSI* ( *ISO C99* ) ma solamente allo standard *POSIX* , la gestione dei segnali da parte di *ANSI-C* è si riduce quindi a `signal` , `raise(sig)` ( equivalente di `kill(getpid(),sig)` ) e `abort()`
>*ANSI-C* non prevede quindi multi-processing e non prevede l'invio di segnali ad altri processi
#### Attendere un segnale tramite `pause`

Per attendere un segnale , in precedenza abbiamo usato `{c}while(1){}` per attendere un segnale ( questo metodo viene detto *busy-waiting* ) ( consuma tempo di CPU ) , la system call `pause()` invece attende il segnale senza consumare tempo di CPU

>[!example]
```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>

void alarmHandler()
{
    printf("questo me lo gestisco io!\n");
}

int main()
{
    signal(SIGALRM, alarmHandler);
    alarm(3);
    pause();
    printf("termino!\n");
}
```
#### Interferenze e funzioni 'safe' in *POSIX*

L'uso della `{c}printf` nell'handler dell'`alarm` è rischioso perchè usa strutture dati condivise da diversi processi , se anche il programma interrotto stava facendo operazioni di I/O i due potrebbero interferire 
>[!note]
>`printf` non è *safe*

La lista di funzioni *safe* che possono essere utilizzate all'interno di un *handler* possono essere ricavata dal comando `{bash}man 7 signal`

Eccone alcuni
```c
_Exit(),   _exit(),  abort(),  accept(), shutdown(),  sigaction(), times(), umask(), uname(), unlink(), utime(), wait(), waitpid(), write() ....
```

>[!example]
>Questo esempio fa interferire le `printf` , all'interno di un ciclo `{c}while` nel main abbiamo inserto la stampa di un stringa , se il programma viene interrotto proprio durante la stampa la `printf` dell'*handler* potrebbe interferire con quella del programma
```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>

void alarmHandler();    // gestore
static int i=0;     // contatore globale `volatile'

int main() { 
    signal(SIGALRM, alarmHandler);
    alarm(1);
    while(1){
        printf("prova\n");
    }
}
void alarmHandler()
{
    printf("questo me lo gestisco io %d!\n",i++);
    alarm(1);   // ri-setta il timer a 1 secondo
}
```

Le stampe tipicamente sono troncate o mischiate tra `{c}"prova"` e `{c}"questo me lo gestisco io $"` , in alcuni casi possiamo perdere alcune `{c}printf` portando a skippare il contatore `{c}i`

Possiamo vedere le interferenze filtrando le stampe della parola `{c}"prova"` che altrimenti avvengono continuamente attraverso la *pipe* : `{bash}./safe | grep io` che fa si che vengano stampate solo le linee contenti la stringa `{c}"io"` 

```bash
$ ./safe | grep io
 questo me lo gestisco io 0!
 questo me lo gestisco io 2!
 questo me lo gestisco io 4!
 questo me lo gestisco io 6!
 questo me lo gestisco io 7!
 questo me lo gestisco io 8!
 questo me lo gestisco io 12!
 questo me lo gestisco io 14!
 questo me lo gestisco io 15!
 questo me lo gestisco io 16!
 questo me lo gestisco io 17!
 provaquesto me lo gestisco io 18!
 provaquesto me lo gestisco io 19!
```

>[!note]
>La `{bash} |` fa in modo che l'output del programma che sta alla sua destra venga reindirizzato al comando sucessivo ( `{bash}grep` che filtra l'output precedente ) come input
>>[!note] 
>>In pratica si crea un canale di comunicazione *message passing* tra i due processi
### Pipe

La *pipe* sono la forma più antica di comunicazione tra processi *UNIX* 

Una *pipe* costituisce una *porta* di comunicazione ( nominaizone indiretta ) con send asincrona e recieve sincrona 

Esistono 2 tipi di *pipe* : 
+ Con nome : 
	Queste hanno un nome nel filesystem ( in pratica sono dei file ) e costituiscono delle porte che tutti i processi possono utilizzare
+ Senza nome :
	Sono utilizzabili sono da processi con antenati comuni in quanto sono una risorsa che viene ereditata dai parenti

#### Pipe senza nome

Le *pipe* senza nome sono generalmente utilizzate per combinare comandi *UNIX* nella shell tramite `|` questo crea 2 processi , ognuno per un comando , crea sucessivamente una pipe e direziona l'output del primo comando verso l'input del secondo programma 

Per creare un *pipe* si utilizza la *system call* `{c} pipe(int filedes[2])` che restituisce in `filedes` 2 descrittori di file ( quelli di input e output ) : 
+ `filedes[0]` : file per la scrittura
+ `filedes[1]` : file per la lettura

>[!note]
>Le *pipe* sono *half-duplex* ( monodirezionali )

Per il resto la *pipe* si utilizza come un normale file

>[!example]
```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
int main() {
	int fd[2]; /* Array che conterrà i descrittori di file */
 
	pipe(fd); /* crea la pipe , ora in fd[0] e fd[1] ci saranno i descrittori per i file di input e output */
	if (fork() == 0) {
		char *phrase = "prova a inviare questo!";

		close(fd[0]);                         /* chiude in lettura */
	    write(fd[1],phrase,strlen(phrase)+1); /* invia anche 0x00 */
	    close(fd[1]);                         /* chiude in scrittura */
	} else {
		char message[100];
	    memset(message,0,100);
	    int bytesread;
 
	    close(fd[1]);                         /* chiude in scrittura */
	    bytesread = read(fd[0],message,99);
	    printf("ho letto dalla pipe %d bytes: '%s' \n",bytesread,message);
		close(fd[0]);                         /* chiude in lettura */
	}
}
```

**Spiegazione** : 
+ Linee 7,8 : 
	Viene creata una *pipe* e subito dopo un nuovo processo 
+ La *pipe* viene ereditata dai figli e quindi entrambi i processi dopo la fork condividono la pipe 
+ Linea 12 :
	Il processo figlio invia una stringa , incluso il terminatore `0x00` , questo per fare in modo che quando leggiamo dal file si capisca dove dobbiammo finire di leggere
+ Linea 20 :
	Il processo genitore legge il file 
+ Linee 11 , 19 : 
	Prima di scrivere o leggere chiudiamo il descrittore del file complementare a quello che deve usare quel processo ( se dobbiamo scrivere chiuderemo quello per leggere e viceversa ) , questo per fare in modo che il sistema operativo non mantenghi in memoria risorse non utilizzate , infatti quando facciamo una `fork()` copiamo i dati e quindi anche l'array contenente i due descrittori di file , il sistema libera tutte le risorse quando tutti i descrittori di file sono stati chiusi  , per questo se non chiudiamo i descrittori che non utilizziamo alla fine dell'esecuzione risulterà che avremo 2 descrittori ancora aperti e il sistema operativo non libererà le risorse

##### Invio e ricezione su una *pipe* "chiusa"

+ Cosa accade se si fa una *read* da una *pipe* che è vuota ed è stata chiusa in scrittura ( non ci sono più dati nel buffer e non ci sono più scrittori sulla *pipe* ) :
	La *read* ritorna 0 , è come se avesse incontrato un *end-of-file* ( *EOF* )
+ Cosa accade se si fa una *write* su una *pipe* che è stata chiusa in lettura ( non ci sono più *lettori* sulla pipe ) : 
	Viene generato il segnale `SIGPIPE` che di default termina il processo ( questo poichè altrimenti scriverei in un posto in cui nessuno leggerebbe , in pratica "butterei" via byte di memoria ) . Se lo si ignora o si gestisce il segnale la *write* ritorna un errore ( `errno` viene settato a `EPIPE` )

>[!note]
> Per questo è molto importante chiudere le una risorsa non utilizzata 
> Se il processo che legge solamente non chiude in scrittura il file , tale processo non riceverà l'*EOF* nel caso l'altro processo chiuda la pipe in scrittura , infatti esiste ancora un processo scrittore attivo e il sistema tiene aperta la pipe . 
> Lo stesso accade con `SIGPIPE` se il processo che scrive solamente non chiude la *pipe* in lettura 

>[!example] *Pipe* della shell
>Replichiamo in modo semplificato il funzionamento della *pipe* della shell
```c
#include <stdio.h>
#include <unistd.h>
int main(int argc, char * argv[])
{
  int fd[2], bytesread;
 
  pipe(fd);
  if (fork() == 0)  {
    close(fd[0]);       /* chiude in lettura */
    dup2(fd[1],1);      /* fa si che 1 (stdout) sia una copia di fd[1] */
                        /* da qui in poi l'output va sulla pipe */
    close(fd[1]);       /* chiude il descrittore fd[1] */
    execlp(argv[1],argv[1],NULL);   /* esegue il comando */
    perror("errore esecuzione primo comando");
  } else {
    close(fd[1]);       /* chiude in scrittura */
    dup2(fd[0],0);      /* fa si che 0 (stdin) sia una copia di fd[0] */
                        /* da qui in poi l'input proviene dalla pipe */
    close(fd[0]);       /* chiude il descrittore fd[0] */
    execlp(argv[2],argv[2],NULL);   /* esegue il comando */
    perror("errore esecuzione secondo comando");
  }
}
```

Per fare in modo che l'input provenga dal primo comando e l'output del secondo venga scritto sullo `stdout` dobbiamo ridirezionare lo stream , per fare questo usiamo la chiamata di sistema `dup2(fd[1],1)` questo fa sì che il descrittore dello `stdout` ( 1 ) sia quello che viene puntato da `fd[1]` ( in pratica diventano interscambiabili ) , in questo modo tutto ciò che mandiamo allo standard output verrà mandato alla *pipe* , dopo il ridirezionamento possiamo chiudere il descrittore della *pipe* in quanto non verrà più utilizzato

Alla fine i descrittore dello standard output e standard input vengono chiusi da `execlp`

##### Atomicità

Le scritture sulle *pipe* sono atomiche ( ossia vengono eseguite in un unico blocco ) se inferiori alla dimensione di `PIPE_BUF` ( `limits.h` ) ossia 4096 bytes 
Sopra questa dimensione l'atomicità non è garantita , sopra tale dimensione se più processi scrivono contemporaneamente non è detto che i byte in scittura non si mischino tra loro

>[!todo]
>inserisci example
>#todo

#### Pipe con nome

Queste *pipe* vengono costruite con il comando `mkfifo` 

>[!example]
>`{bash}mkfifo myPipe`
>`{bash}prw-rw-r--    1 focardi  focardi     0 mag 23 00:57 myPipe`

Da questo momento la *pipe* è presente nel filesystem è qualsiasi processo con i diritti per accedere al file può utilizzarla

>[!note]
>La *open* è boccante in lettura finchè qualche altro processo non la apre in scrittura 

>[!example]
>Creiamo un processo *lettore* ( il destinatario ) che accetta , su una *pipe con nome* , messsaggi provenienti da più processi *scrittori* ( i mittenti ) che mandano 3 messaggi e poi terminano .
>Quando tutti gli scrittori chiudono la *pipe* il lettore ottiene 0 come valore di ritorno della `read` ed esce

**Lettore**
```c
#include <stdio.h>
#include <sys/types.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/stat.h>
#include <stdlib.h>

#define PNAME "/tmp/aPipe"
int main() {
    int fd;
    char leggi;
    
    mkfifo(PNAME,0666); // crea la pipe con nome, se esiste gia' non fa nulla

    fd = open(PNAME,O_RDONLY); // apre la pipe in lettura
    if ( fd < 0 ) { 
        perror("errore apertura pipe"); 
        exit(1);
    }
    
    while (read(fd,&leggi,1)) { // legge un carattere alla volta fino a EOF
        if (leggi == '\0'){
            printf("\n"); // a capo dopo ogni stringa
        } else {
            printf("%c",leggi);
        }
    }

    close(fd);          // chiude il descrittore
    unlink(PNAME);      // rimuove la pipe

    return 0;
}
```


**Scrittore**
```c
#include <stdio.h>
#include <sys/types.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/stat.h>
#include <string.h>
#include <stdlib.h>
#define PNAME "/tmp/aPipe"
int main() {
    int fd, i, lunghezza;
    char *message;
    mkfifo(PNAME,0666); // crea la pipe con nome, se esiste gia' non fa nulla
    // crea la stringa
    lunghezza = snprintf(NULL,0,"Saluti dal processo %d",getpid()); // trovo la lunghezza della stringa
    message = malloc(lunghezza+1); // allocco spazio necessario
    snprintf(message,lunghezza+1,"Saluti dal processo %d",getpid()); // metto la stringa nell'array + 0x00 
    fd = open(PNAME,O_WRONLY); // apre la pipe in scrittura
    if ( fd < 0 ) {    
        perror("errore apertura pipe"); exit(1);
    }
    for (i=1; i<=3; i++){     // scrive tre volte la stringa
        write (fd,message,strlen(message)+1); // include terminatore
        sleep(2);
    }
    close(fd);    // chiude il descrittore
    free(message);
    return 0;
}
```

Ora lanciamo ( con `&` per mandarli in esecuzione in parallelo in background ) i processi : 
```bash
$ ./lettore & ./scrittore & ./scrittore & ./scrittore
 [1] 46998 -> lettore
 [2] 46999 -> scrittore1 
 [3] 47000 -> scrittore2
 Saluti dal processo 47000
 Saluti dal processo 46999
 Saluti dal processo 47001 ->scrittore3
 Saluti dal processo 47000
 Saluti dal processo 47001
 Saluti dal processo 46999
 Saluti dal processo 47000
 Saluti dal processo 46999
 Saluti dal processo 47001
 [1]   Done                    ./lettore
 [2]-  Exit 255                ./scrittore
 [3]+  Exit 255                ./scrittore
```

>[!note]
>Il *lettore* legge un byte alla volta , non ho nessuna garanzia che ogni lettore legga completamente il messaggio , si possono mischiare dei byte

>[!warning]
>Le *pipe* possono essere aperte sia in lettura che scrittura con l'opzione `O_RDRW` , questo però ci porterebbe a leggere noi stessi .
>Es nello stesso processo scrivo e poi leggo  
>Questa modalità è utilizzata solamente per fare in modo di aprire *pipe* in scrittura quando nessuno l'ha ancora aperta in lettura 

### Produttore e Consumatore

Il modello a scambio di messaggi si presta ad applicazioni strutturate come *produttore*/*consumatore* ossia un processo produce dati e l'altro li utilizza 

#### Scambio di messaggi

Nel modello a scambio di messaggi lo schema *produttore*/*costruttore* si realizza come segue :

```c
port A

produttore(){
	while(1){
		// produce un dato
		send(A,d);
	}
}

consumatore(){
	while(1){
		recieve(A,&d);
		// consuma d
	}
}
```

Se utilizziamo una *send asincrona* ( bloccante solo quando il buffer è pieno ) e una *recieve sincrona* otteniamo una soluzione soddisfacente : il consumatore attende se non ci sono dati da consumare , il produttore attende se non c'è spazio nel buffer

#### Memoria condivisa

Il modello a *memoria condivisa* viene utilizzato dai *thread* ( un filone di istruzioni all'interno di un processo )  , in quanto questi condividono le risorse del loro processo padre .
Quando abbiamo più thread in un singolo processo si parla di *multi-threading* . In questo caso ogni *thread* avrà un ID distinto ma condivideranno la memoria del loro processo di origine

##### Produttore/Consumatore con memoria condivisa e buffer illimitato

Utilizzeremo la memoria come *canale di comunicazione* senza ricorrere a meccanismi di comunicazione tra processi come le *PIPE*

Consideriamo un buffer illimitato , implementato tramite un array `buffer[]` condiviso e due indici `inserisci` e `preleva` inizializzati a 0 

Vediamo una prima soluzione ==Errata==

```c
data_t buffer[...]; // un buffer "teorico" di dimensione illimitata
int inserisci=0, preleva=0; // indici per l'accesso al buffer
 
Produttore() {
	while(1) {
	    /* produce un elemento d */
	    buffer[inserisci] = d;
	    inserisci++;
	}
}
 
Consumatore() {
	while(1) {
	    d = buffer[preleva];
	    preleva++;
	    /* consuma d */
	}
}
```

>[!warning]
>Il *consumatore* non attende che vi siano elementi nel buffer e quindi legge memoria "sporca" nel caso sia eseguito pipù velocemente del *produttore* . Visto che non possiamo fare assunzioni sullo scheduling dobbiamo introdurre un meccanismo di attesa 

>[!solution]
>Utilizziamo la tecnica *busy-waiting* : il thread cicla a vuoto finchè non ci sono elementi da consumare

```c
data_t buffer[...]; // un buffer "teorico" di dimensione illimitata
int inserisci=0, preleva=0; // indici per l'accesso al buffer
 
Produttore() {
	while(1) {
	    /* produce un elemento d */
	    buffer[inserisci] = d;
	    inserisci++;
	}
}
 
Consumatore() {
	while(1) {
	    while (inserisci == preleva) {}; // buffer vuoto attendo
	    d = buffer[preleva];
	    preleva++;
	    /* consuma d */
	}
}
```

>[!note]
>La soluzione assume l'esistenza di un buffer illimitato

##### Produttore/Consumatore con memoria condivisa e buffer circolare

Utilizziamo un buffer circolare di dimensione `MAX` 

Suluzione ==Errata==
```c
data_t buffer[MAX]; // un buffer di dimensione MAX
int inserisci=0, preleva=0; // indici per l'accesso al buffer
 
Produttore() {
	while(1) {
	    /* produce un elemento d */
	    buffer[inserisci] = d;
	    inserisci=(inserisci+1) % MAX // Il buffer è circolare
	}
}
 
Consumatore() {
	while(1) {
	    while (inserisci == preleva) {}; // buffer vuoto attendo
	    d = buffer[preleva];
	    preleva=(preleva+1) % MAX // Il buffer è circolare
	    /* consuma d */
	}
}
```

>[!warning]
>Se il buffer ha dimensione limitata il produttore , raggiunto `MAX` , inizierà a scrivere dalla casella 0 , *sovrascrivendo* i dati non ancora letti dal cosumatore . 
>Nel caso della `send` il produttore si deve bloccare quando il buffer è pieno

>[!solution]
>Sarebbe necessario aggiungere un *busy-waiting* anche sul produttore ma non è possibile poichè `inserisci==preleva` andrebbe in stallo visto che all'inizio avremo che sicuramente `inserisci==preleva` il produttore quindi si bloccherebbe sul `{c}while(inserisci==preleva){}` senza mai produrre nulla 
>Una soluzione sarebbe quella di considerare il buffer pieno quando `(inserisci+1) % MAX` coincide con `preleva` ( ossia abbiamo solo una cella libera )

```c
data_t buffer[MAX]; // un buffer di dimensione MAX
int inserisci=0, preleva=0; // indici per l'accesso al buffer
 
Produttore() {
	while(1) {
    /* produce un elemento d */
	    while ((inserisci+1) % MAX == preleva) {}; // buffer pieno attendo
	    buffer[inserisci] = d;
	    inserisci=(inserisci+1) % MAX // Il buffer è circolare
	}
}
 
Consumatore() {
	while(1) {
	    while (inserisci == preleva) {}; // buffer vuoto attendo
	    d = buffer[preleva];
	    preleva=(preleva+1) % MAX // Il buffer è circolare
	    /* consuma d */
	}
}
```

Questa soluzione ha alcuni difetti : 
1. Spreca una cella di memoria per distinguere buffer pieno da buffer vuoto
2. Spreca tempo di CPU nel *busy-waiting*
3. Non scala con la presenza di più costruttori e più consumatori poichè la condivisione di `inserisci` e `preleva` conporterebbe altre interferenze

##### Interferenze tra variabili condivise

Per risolvere il primo problema si potrebbe usare un contatore inizializzato a 0 

```c
data_t buffer[MAX]; // un buffer di dimensione MAX
int inserisci=0, preleva=0; // indici per l'accesso al buffer
int contatore=0; // contatore per le condizioni di attesa
 
Produttore() {
  while(1) {
    /* produce un elemento d */
    while (contatore == MAX) {}; // buffer pieno attendo
    buffer[inserisci] = d;
    inserisci=(inserisci+1) % MAX // Il buffer è circolare
    contatore++; // aggiorno il contatore
  }
}
 
Consumatore() {
  while(1) {
    while (contatore == 0) {}; // buffer vuoto attendo
    d = buffer[preleva];
    preleva=(preleva+1) % MAX // Il buffer è circolare
    contatore--; // aggiorno il contatore
    /* consuma d */
  }
}
```

>[!warning]
>I thread aggiornano la variabile `contatore` contemporaneamente , questo crea interferenze compromettendo l'integrità dei dati

>[!example]
>In pseudo-assembly avremo che la modifica del contatore sarà : 
```asm
r1 = contatore // lettura dalla memoria in un registro
r1 = r1 + 1 
contatore = r1 // salvo il nuovo valore in memoria
```
```asm
r2 = contatore
r2 = r2 - 1
contatore = r2
```

Visto che non possiamo essere certi dell'ordine con il quale vengono effettuate le precedenti operazioni non possiamo nemmeno essere certi del risultato che otteniamo :
Potremmo avere 3 risulati a seconda dell'ordine con cui eseguiamo le istruzioni ( partendo da `contatore=5` ) :
1. `contatore = 6`
2. `contatore = 4`
3. `contatore = 5`

*Race condition* : si sviluppa una competizione per la stessa risorsa ( in questo caso il `contatore` ) , l'output del risultato dipende dalla temporizzazione degli eventi 

Serve un meccanismo per sincronzzare i thread , una soluzione sarebbe avere una parte di codice in cui i thread non possono svolgerla contemporaneamente ( *Sezione critica* )

### Sezione critica

La *sezione critica* è una parte del codice in cui i processi accedono a dati condivisi dai thread , in questa parte per mantenere la consistenza dei dati necessitiamo di implementare meccanismi che permettino l'esecuzione ordinata dei *thread*

Vogliamo quindi garantire che un solo *thread* alla volta possa accedere alla sezione critica , questa proprietà viene detta *mutua esclusione* ( questa deve valere a prescindere dalla schedulazione dei thread )

#### Soluzione software

##### lock

Utilizziamo una variabile booleana globale `lock` inizialzzata a `{c}false` per indicare se qualche *thread* è dentro la *sezione critica* 

```c
bool lock = false; // in nessuna sezione critica

t0{
	....
	while(lock){} // se lock true aspetto ( busy waiting ) 
	lock = true; // sono dentro alla sezione critica
	<sezione critica>
	lock = false; // sono uscito dalla sezione critica
	....
}

t1{
	....
	while(lock){}
	lock = true;
	<sezione critica>
	lock = false;
	....
}
```

*Descrizione* : 
	Il codice di ingresso attende ciclando a vuoto ( busy waiting ) finchè `lock` è `{c}true` Quando il `lock` viene rilasciato usciremo dal ciclo `{c}while` e acquisisce il `lock` ponendolo a `true` . Alla fine della sezione critica il `lock` viene settato a `false` e quindi rilasciato

>[!warning]
>Se due *thread* entrano contemporaneamente in *sezione critica* potrebbe accadere che superino il `while` entrambi , in quanto leggono il valore `false` prima che l'altro *thread* abbia settato `lock` a `true` , in tale caso è come se entrambi acquisissero il `lock` e la *mutua esclusione* non è garantita

##### turno

Utilizziamo una variabile booleana globale `turno` inizializzata a 1 . 
I *thread* eseguono codice che dipende dal proprio ID

```c
int turno = 1; // in nessuna szezione critica

t0{
	....
	while(turno!=0){} // non è il tuo turno -> turno di 1 poichè !=0
	<sezione critica>
	turno = 1;
	....
}

t1{
	....
	while(turno!=1){}
	<sezione critica>
	turno = 0;
	....
}
```

*Descrizione* : 
	Il codice di ingresso attende ciclando a vuoto finchè non è il propro turno , alla fine della *sezione critica* il turno viene dato all'altro thread

Con questo codice la *mutua esclusione* è garantita dal fatto che `turno` non può valere contemporaneamente 0 e 1 , di conseguenza uno dei due *thread* rimarrà bloccato in attesa sul ciclo `{c}while` 

>[!warning]
>Supponiamo che `t0` volgia entrare due volte di seguito nella sezione critica mentre `t1` sta eseguendo altro codice 
>A questo punto `t0` esce dalla prima sezione critica e assegna il turno a `t1` che però sta eseguendo dell'altro codice e quindi non sappiamo prevedere quando `t1` entrerà in *sezione critica*


Manca una seconda proprietà fondamentale : il **progresso** : se nessun *thread* è in *sezione critica* un *thread* che richiede di accedere deve poterlo fare immediatamente

##### pronto

Utilizziamo un array di booleani globale `pronto[2]` inizializzato `false` 
I *thread* eseguono codice che dipende dal proprio ID

```c
pronto[2]={false,false};

t0{
	....
	pronto[0]=true;
	while(pronto[1]){} // se l'altro è pronto
	<sezione critica>
	pronto[0] = false;
	....
}

t1{
	....
	pronto[1]=true;
	while(pronto[0]){}
	<sezione critica>
	pronto[1] = false;
	....
}

```

*Descrizione* : 
	Quando il *thread* $i$-esimo vuole entrare pone a `true` la variabile `pronto[i]` , per segnalare all'altro *thread* la volontà di accedere , sucessiva cicla a vuoto se anche l'altro *thread* vuole accedere . Quando esce dalla sezione critica il thread pone a `false` `pronto[i]` per indicare che non ha più bisongno della sezione critica

**Mutua esclusione** : 
	La mutua esclusione è garantita dal fatto che se un *thread* è in *sezione critica* la sua variabile pronto è settata a `true` e l'altro *thread* si bloccherà sul ciclo `{c}while` 

>[!warning]
>Anche se la soluzione garantisce *mutua esclusione* esistono esecuzioni problematiche : supponiamo che `t0` e `t1` vogliano entrare entrambi in *sezione critica* , potrebbe accedere che settino simultaneamente `pronto[i]=true` e poi si blocchino entrambi sul proprio ciclo `{c}while` . Questo porta a *deadlock*
>
>Se inveriamo i `{c}while` e l'assegnamento `pronto[i]=true` potrebbe accadere che entrambi i *thread* superino il ciclo `{c}while` rompendo la *mutua esclusione*

##### Algoritmo di Peterson

Il difetto della soluzione *pronto* è che esiste un caso in cui i *thread* vanno in stallo. Per evitare questa situazione utilizziamo il metodo dei *turni* solo nel caso problematico ( ossia quando entrambi i thread sono pronti ) , se uno solo è pronto tale thread può accedere senza problemi alla *sezione critica* 

```c
pronto[2]={false,false};
int turno = 0;

t0{
	....
	pronto[0]=true;
	turno = 1; // turno viene concesso
	while(pronto[1]&&turno!=0){} // se l'altro è pronto
	<sezione critica>
	pronto[0] = false;
	....
}

t1{
	....
	pronto[1]=true;
	turno = 0;
	while(pronto[0]&&turno!=1){}
	<sezione critica>
	pronto[1] = false;
	....
}
```

*Descrizione* : 
	Quando il *thread* $i$-esimo vuole entrare pone a `true` la variabile `pronto[i]` per segnalare all'altro thread che vuole accedere alla *sezione critica* 
	Cede quindi il `turno` all'altro thread e cicla a vuoto se l'altro thread è pronto e non è il suo turno 
	Quando esce dalla sezione critica, il *thread* pone a `false` `pronto[i]` per indicare che non ha più bisogno della *sezione critica*

**Mutua esclusione** : 
	Consideriamo 2 casi : 
	1. `t0` supera il proprio `{c}while` quando `t1` è prima di `turno=0` , in questo caso quando `t1` eseguirà `turno=0` si bloccherà nel ciclo `{c}while` in quanto avremo che `pronto[0]` è `{c}true` e `turno` è `0` ossia diverso da `1` 
	2. `t0` super il proprio `{c}while` quando `t1` è `turno=0` . Poichè `t0` sta superando il `{c}while` deve essere `turno==0` . Quindi avremo che `t1` si blocca nel suo `{c}while` ed attende

**Progresso** : 
	Se nessuno è in *sezione critica* `pronto[i]` vale `{c}false` . Chiunque voglia entrare ci riuscirà in quanto la prima condizione nel `{c}while` sarà falsa

**Assenza di stallo** : 
	Supponiamo che entrambi i *thread* siano bloccati nel rispettivo `{c}while` . In questo caso avremo che `turno != 0` e `!= 1` ma questo non è possibile , quindi uno dei due *thread*  uscirà dal ciclo `{c}while`

>[!note]
>L'algoritmo di Peterson funziona per 2 *thread* ma esistono algoritmi che funzionano per $n$ *thread*
##### Conclusione

Le soluzioni via *software* presentano le seguenti problematiche : 
+ I cicli `{c}while` fatti per implementare il busy-waiting consumano tempo di CPU , su macchine con un solo core il problema è rilevante pochè fino alla fine del tempo che è stato allocato a quel *thread* la CPU è bloccata , in CPU multicore risulta più accettabile poichè solo alcuni core rimarrebbero bloccati
+ I compiler e processori effettuando delle ottimizzazioni che potrebbero riordinare delle istruzioni del processo per motivi di efficenza , ciò però potrebbe compromettere la correttezza del nostro codice , per questo se si utilizza la soluzione puramente via software dovremmo disabilitare queste ottimizzazioni che potrebbe però portare ad un decremento considerevole delle performance
#### Soluzioni Hardware 

##### Disabilitare le interruzioni

La tecnica consiste nel *disabilitare* le *interruzioni* della CPU in modo da monopolizzare la CPU per il tempo necessario ad eseguire il codice critico , in CPU single core viene momentaneamente sospesa l'esecuzione parallela di *thread* e *processi* garantendo la *mutua esclusione*

>[!warning] 
>In CPU multi core esiste ancora il parallelismo tra core e quindi la *mutua esclusione* potrebbe non essere garantita 
>
>Inoltre non può essere utilizzata in programmi utente poichè un errore di programmazione bloccherebbe l'intero sistema

##### Istruzioni speciali

 Ci vengono fornite dal sistema operativo delle *istruzioni speciali* che ci permettono di eseguire dei blocchi di istruzioni in modo indivisibile 

Un esempio è la `test&set` : questa ci permette di testare un valore ed assegnarlo in modo indivisibile 

```c
bool test&set(bool *x){
	bool tmp = *x;
	*x = true;
	return tmp;
}
```

Pone a `{c}true` il valore della variabile `x` ritornando il valore booleano prima dell'assegnamento , ci permette quindi di testare ed assegnare il valore di `x` in modo indivisibile

Possiamo usarla per risolvere il problema del `lock`  : 

```c
t0{
	....
	while(test&set(&lock)){}
	<sezione critica>
	lock = false;
	....
}
```

In *Intel* c'è un altra funzione che ci permette di eseguire operazioni in modo atomico : 

**XCHG** : questa scambia due varaibili booleane tra loro in modo atomico
```c
XCHG(boolean *x , *y){
	boolean temp = *x;
	*x = y*;
	*y = temp;
}
```

L'esempio precedente può essere scritto con `XCHG` nel seguente modo : 
```c
bool lock = false;
t0{
	bool b0 = true;
	while(b0){ XCHG( &b0, &lock); } // se un thread ha fatto exchange qui continuerà a scambiare sempre true perchè lock è diventato true
	<sezione critica>
	XCGH( &b0, &lock )
}

t1{
	bool b1 = true;
	while(b1){ XCHG( &b1, &lock); } // variabile locale non da race condition
	<sezione critica>
	XCGH( &b1, &lock );
}
```

In pratica manteniamo in memoria condivisa il *flag* che ci permette di entrare in sezione critica e con `XCHG` lo scambiamo con uno locale che ci permette di far rimanere all'interno del ciclo `{c}while`l'altro *thread*

>[!note]
>Quando utilizziamo questo tipo di funzioni il compiler e l'architettura di sistema evita di ottimizzare le tighe comprese tra le funzioni *atomiche* ( *XCHG* e *test&set* )
### Thread in POSIX

Un *thread* in POSIX è un'unità di esecuzione all'interno di un processo
Un *processo* può avere più *thread* in esecuzione , che tipicamente condividono le risorse di un processo , in particolare condividono la memoria con il processo 
Lo standard *POSIX* definisce un insieme di funzioni per la creazione e la sincronizzazione dei thread :

+ `{c}pthread_create(pthread_t *thread, pthread_attr_t *attr, void * (*start_routine)(void *), void *arg)`
	I suoi 4 argomenti rappresentano : 
	
1. `thread` : un puntatore a `{c}pthread_t` , l'analogo di `{c}pid_t` ma per i *thread* ( non viene però implementato con un intero , in Linux è implementato con un `{c}unsigned long int` )
2. `attr` : attributi del nuovo thread creato ( `NULL` se non vogliamo modificarli )
3. `start_routine` : Pointer a *funzione* ( codice da eseguire all'interno del thread ) , questa deve prendere un pointer a *void* e tornare un pointer a void
>[!example]
```c
void * codice_thread(void * a)
```
4. `arg` : eventuali argomenti da passare alla funzione `NULL` altrimenti

+ `{c}pthread_exit(void *retval)` termina l'esecuzione di un thread creato con `{c}pthread_create` 

>[!note]
>Quando facciamo la `exit` da un processo vengono terminati tutti i sui thread figli

+ `{c}pthread_join(pthread_t th, void **thread_return)` 
	Ci permette di attendere la terminazione del *thread* `th` , valori di ritorno 
	+ 0 se ha successo la join e un pointer al valore di ritorno di `th` ( `{c}void **thread_return` ) 
	+ $\neq 0$ se è stato generato un errore 
>[!note]
>Se non vogliamo ricevere valori di ritorno basta porre a `NULL` `{c}void **thread_return`
+ `{c}pthread_detach(pthread_t th)` : se non si vuole attende la fine dell'esecuzione di quel *thread* specifico , pone `th` in uno stato distaccato dal suo processo d'origine in modo da non farlo diventare zombie quando termina
>[!note] 
>Nessun processo può fare `{c}thread_join` su quel thread
>Non rimane comunque attivo dopo che il processo padre termina con `exit()`

+ `{c}pthread_t pthread_self()` ritorna il *thread ID* del thread dove è stato chiamato
>[!note]
>*Pthread ID* è l'ID della libreria `pthread` non quello di sistema , dipende quindi dall'implementazione di questa  
>Per visualizzare l'ID di sistema si può usare la syscall ( presente in Linux ) `syscall(SYS_getiid)`

>[!todo]
>esempi
### Semafori

In tutti i codici precedentemente visiti si utilizzano dei cicli a vuoto per "fermare" un thread , questi sono *inefficenti* poichè si spreca tempo di CPU inutilmente , questi vengono utilizzati nel codice del sistema operativo per implementare la sincronizzazione tra thread 

I *semafori* sono una soluzione a questo problema 

Un *semaforo* non è altro che una struttura dati contenente un contatore e una coda di thread in attesa : 
```c
struct semaforo{
	int valore;
	thread *queue;
}
```

Il `valore` del semaforo va interpretato nel seguente modo : 
+ `{c}S.valore > 0` : rappresenta il numero di accessi consentiti prima che venga blocca l'entrata in sezione critica
+ `{c}S.valore <=0` : Indica il numero di thread che sono presenti in attesa all'interno della coda

Il `valore` non viene mai incrementato o decrementato direttamente ma solo attraverso delle funzioni speciali che gestiscono la sincronizzazione dei thread : 
+ `P(S)` : o `wait(S)` 
	Decrementa il valore del *semaforo* `S` . Se il semaforo era già "rosso" prima del decremento allora il thread corrente viene posto in fondo alla coda associata a quel semaforo
+ `V(S)` : o `post(S)` 
	Incrementa il valore del *semaforo* `S` . Se ci sono thread in attesa sul semaforo ( `valore<=0` ) allora viene mandato in esecuzione il primo thread che si trova sulla coda

Le loro implementazioni sono : 
```c
P(semaforo S){
	S.valore--;
	if(S.valore<0)
		< Metti il thread corrente in S.queue > 
}
```
```c
V(semaforo S){
	S.valore++;
	if(S.valore<=0)
		< Sblocca il primo thread presente in S.queue >
}
```

I semafori permettono di realizzare la sezione critica in modo semplice ed efficace .
Il *valore* del semaforo indica il numero di accessi consentiti prima di diventare rosso . 

>[!example] 
>Nel caso della sezione critica solo un *thread* alla volta deve accedere alla sezione critica in modo da mantenere la *mutua esclusione* , utilizziamo quindi un semaforo `mutex` , nel quale il *valore* sarà impostato ad 1 
>Per controllare l'accesso è sufficente eseguire una `P(mutex)` 
>Mentre all'uscita della *sezione critica* ci basterà eseguire una `V(mutex)` per ripristinare il valore del semaforo e sbloccare un *thread* eventualmente in attesa 
```c
thread T{
	...
	P(mutex)
	< Sezione critica >
	V(mutex)
	...
}
```

>[!example] 
>Esempio di esecuzione con tre *thread* in esecuzione su 3 core distinti.
>Consideriamo un semaforo `mutex` inizializzato a 1 
```bash
T1 -----------P====V----P==============V--------------

T2 --------------------------P         +====V---------

T3 -------------------------------P         +====V----

mutex
 valore: 1    0    1    0   -1   -2   -1    0    1  
 queue:  _    _    _    _    |    |    |    _    _  
                             T2   T2   T3  
                                  |  
                                  T3
```

**Spiegazione**

+ `{bash}-` indica l'esecuzione fuori dalla sezione critica 
+ `{bash}=` indica l'esecuzione in sezione critica
+ In basso è rappresentato dello stato del semaforo 

>[!note] 
>+ Per valori negativi del counter il suo modulo rappresenta il numero di thread in attessa nella queue
>+ Al di fuori della sezione critica i thread possono essere eseguiti liberamente anche contemporaneamente
>+ Viene realizzata *mutua esclusione* , ossia non ci sarà mai più di un thread in sezione critica alla volta 

#### Altri utilizzi dei semafori

**Sincronizzazione di codice**

>[!example] 
>Abbiamo un thread `T2` che deve eseguire del codice `D` dopo l'esecuzione di una sezione di codice `A` presente in un thread `T1` 
>Questo può essere fatto utilizzando un *semaforo* inizializzato a 0 
```c
semaforo S = 0;

T1{
	< A >
	V(S)
	< B >
}

T2{
	< C >
	P(S)
	< D >
}
```
>[!example] 
>Poichè `S` è inizializzato a 0 la `P(S)` di `T2` sarà bloccante e di conseguenza il codice `< D >` verrà eseguito necessariamente dopo il codice `< A >` 
>Se la `V(S)` viene eseguita prima della `P(S)` il *semaforo* diventerà `1` e la sucessiva `P(S)` non sarà bloccante
>>[!note]
>>Questo esempio ricorda la sincronizzazione che si ottiene nello scambio di messaggi : la `V(S)` è come una `send` *asincrona* mentre la `P(S)` è come una `recieve` *sincorna*

**Regolare l'accesso a risorse**

In generale un *semaforo* inizializzato a `MAX` permette `MAX` accessi prima di diventare bloccante , possiamo quindi ridefinire le operazioni `P` e `V` 
+ `P(S)` richiede una risorsa , se ce ne è almeno una disponibile viene assegnata altrimenti attendiamo che ve ne sia una disponibile 
+ `V(S)` rilascio di una risorsa , se ci sono *thread* in attesa il primo viene sbloccato

>[!example] 
>Se abbiamo 3 stampanti possiamo usare un *semaforo* inizializzato a 3 per regolare il loro utilizzo contemporaneo da un massimo di 3 thread alla volta , quando un processo avrà finito una stampa allora con `V(S)` si sbloccherà il primo *thread* in attesa di utilizzare la stampante

**Produttore e consumatore con semafori**

Cerchiamo di risolvere il problema del *produttore-consumatore* con i semafori 
Ci sono 2 sincronizzazioni da fare : 
+ Quando il buffer è pieno il *produttore* deve attendere per evitare di sovrascrivere i dati
+ Quando  il buffer è vuoto il *consumatore* deve attendere per evitare di leggere celle non ancora scritte

Possiamo utilizzare 2 *semafori* che regolano l'accesso alle risorse : 
+ `vuote` rappresenta le celle vuote che possono essere utilizzate , inizialmente inizializzata a `MAX`
+ `piene` rappresenta le celle piene , inizialmente inizializzata a 0 

```c
semaphore piene=0, vuote=MAX;

Produttore {
  while(1) {
    < produce d >
    P(vuote); // richiede una cella vuota
    buffer[inserisci] = d;
    inserisci = (inserisci+1) % MAX;
    V(piene); // rilascia una cella piena
  }
}
 
Consumatore {
  while(1) {
    P(piene); // richiede una cella piena
    d = buffer[preleva];
    preleva = (preleva+1) % MAX:
    V(vuote); // rilascia una cella vuota
    < consuma d >
  }
}
```

>[!warning] 
>Cosa accade quando abbiamo tanti *produttori* e tanti *consumatori* ?

Potrebbero verificarsi interferenze in scrittura e lettura sul buffer 
>[!example] 
>Due produttori potrebbero scrivere sulla stessa cella `buffer[inserisci]` sovrascrivendosi l'un l'altro e poi entrambi incrementare `inserisci` oppure interfierire sull'incremento di `inserisci` 
>

La soluzione è quella di proteggere il codice implementando una *sezione critica* ( ossia utilizzando un *semaforo* `mutex` inizializzato ad 1 )

```c
semaphore piene=0, vuote=MAX, mutex=1;

Produttore {
  while(1) {
    < produce d >
    P(vuote); // richiede una cella vuota
    P(mutex); // entra in sezione critica
    buffer[inserisci] = d;
    inserisci = (inserisci+1) % MAX;
    V(mutex); // esce dalla sezione critica
    V(piene); // rilascia una cella piena
  }
}
 
Consumatore() {
  while( {
    P(piene); // richiede una cella piena
    P(mutex); // entra in sezione critica
    d = buffer[preleva];
    preleva = (preleva+1) % MAX:
    V(mutex); // esce dalla sezione critica
    V(vuote); // rilascia una cella vuota
    < consuma d >
  }
}
```

>[!note] 
>Quando si inseriscono *sezioni critica* bisogna verificare che al loro interno non vi siano semafori bloccanti

## Semafori in POSIX

thread safe -> le funzioni possono o non essere thread safe 
`printf` è thread safe ( non safe per i segnali ) poichè `stdio` è thread safe

`mt_safe locale` -> sono safe da invocare in presenza di altri thread , locale -> legge senza sincronizzazione , ossia leggo se qualcuno modifica nella variabile locale , se uso funzioni che modificano il locale allora in questo caso sono unsafe altrimenti sono safe

codifica i mutex in `pthread mutex` utilizzati solo per sezione critica 

`pthread condition` -> utilizzati per implementare i monitor in C

libreria `semaphore.h`

`sem_t` -> tipo del semaforo da mettere globale 

funzione per inzialzzare semaforo -> primo indirizzo semaforo , secondo sempre a 0 ( per dire se fra thread 0 o processi !=0 ) ultimo è il valore del semaforo

init fatto prima di dichiarare i thread 

+ P(sem) -> `int sem_wait(sem_t *sem)`
+ V(sem) -> `int sem_post(sem_t *sem)`

`sem_getValue` -> non da utilizzare per la sincronizzazione ->l'implementazione potrebbe ritornare 0 anche se ci sono 100 thread in attesa 

`sem_destroy` per eliminare il semaforo , se elimino durante l'utilizzo comportamento non specificato

semafori con il nome come per le pipe sono semafori che esistono a livello del sistema operativo