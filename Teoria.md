#### Cosa è un Microcontrollore?

un MCU o Microcontrollore è un circuito integrato che include tutte le parti di un computer :

- CPU;
- RAM;
- Memoria di Massa;

con i suoi PIN di collegamento un MCU non collega le periferiche in un BUS condiviso, ma mette in collegamento le singole periferiche tra loro, esistono vari tipi di linee e quindi di periferiche e sono : 

- linee digitali;
- linee analogiche;
> in queste linee dobbiamo prestare attenzione agli ADC ed ai DAC, sono periferiche che servono a fare la conversione del segnale analogica in digitale e viceversa.
- Timer;
- linee digitali speciali;
- interfacce di comunicazione;
    - USB;
    - SPI;
    - $I^2C$;
    - CAN;
    - Ethernet

gli MCU hanno vari utilizzi alcuni sono:

- Misure di precisione;
- industria dell'automotive;
- rendere smart alcuni device;
- robotica;
- Domotica;

solitamente gli MCU fanno girare il proprio software in maniera nativa, senza l'uso di un sistema operativo, in questo caso lo sviluppatore dovrà lavorare con la programmazione delle periferiche di I/O, nei casi in cui l'MCU dispone di un sistema operativo, avrà funzionalità minime, non vi sarà uno scheduling delle attività, la programmazione di un MCU si basa sull'utilizzo dei circuiti logici e sul concetto che il circuito potrà assumere solo 2 valori :

- 0 = corrisponde a linea spenta quindi a corrente che non passa.

- 1 = corrisponde a linea aperta quindi a corrente che passa e può essere da 1.8/3.3/5 V

i circuiti logici si suddividono in 
1) Circuiti combinatori:
> sono quei circuiti che dipendono solo dallo stato corrente dell'input e sono 
    - AND;
    - OR;
    - NOT;

2) circuiti Combinatori speciali :
> Sono quei circuiti che funzionano come degli switch di segnali e sono
    - Multiplexers;

3) Circuiti Sequenziali:
> Sono quei circuiti che il loro funzionamento dipende dallo stato passate e dallo stato corrente dell'input ed il comportamento è rappresentato da una macchina a stati finiti pilotata da un segnale di clock e sono 
    - Flip-Flop;
    - Counter;
    - Register;

in questi circuiti si utilizzano i segnali che a loro volta fanno uso di :

- Variabili;
> sono gli stati logici che possono variare nel tempo e sono caratterizzati da fronti di salita e di discesa segnalati con il bit 0 o 1 e abbiamo due tipologie :
    - il bit varia da 0 a 1 avremo un RISING EDGE, questi fronti sono rappresentati da dei triangoli semplici;
    - il bit varia da 1 a 0 avremo un FALLING EDGE, questi fronti sono rappresentati da cerchi + triangoli;

- Costanti;
> sono gli stati logici che non variano nel tempo.

il segnale a sua volta può essere diviso in :
- Segnale Simmetrico;
> è quel segnale dove la durata del suo stato 0 e del suo stato 1 è la stessa ed è uguale a :
$T_0 = T_1 = \frac{P}{2}$
- Segnale Asimmetrico;
> è quel segnale dove la durata del suo stato 0 e del suo stato 1 è differente $$T_0 \neq T_1$$

le connessioni tra software e hardware in un MCU sono date da registri e contatori, i registri vengono mappati in memoria ad indirizzi specifici che servono per effettuare le interazioni di lettura e scrittura

#### Digital I/O Interface

è un interfaccia dove ogni PIN elettrico può avere 2 stati : 

- 1 (passa corrente in base a quella fornita dalla fonte di alimentazione)
- 0 (non passa corrente)

ogni linea può essere usata come :
- input, l'MCU riceve informazioni;
Es.
leggere la pressione di un pulsante.
- output, l'MCU genera informazioni
Es.
1 LED che lampeggia o il display;

nel nostro MCU le porte digitali vengono chiamate come :
- GPIOA;
- GPIOB;
- GPIOC;

ogni porta ha 16 pin elettrici che corrispondono a 16 bit, ogni PIN può avere anche funzioni alternative relative alla periferica o non essere mappate, per utilizzare una porta digitale bisogna riconoscerla nel seguente modo :
- $Pxy$ dove x è il nome della porta (A, B, C, D, etc) mentre y è il bit interessato(0, 1, 2, 3, ..., 15)
Es.
>PA8
>stiamo andando a lavorare sulla porta logica A al pin 8.

nel nostro corso per programmare un GPIO utilizzeremo il seguente schema : 
GPIO_init(periferica)
GPIO_config_output/input(periferica, bit)
GPIO_write(periferica, bit, valore)

Es.
> GPIO_init(GPIOB);
> GPIO_config_output(GPIOB, 0);
> GPIO_write(GPIOB, 0, 1);

in questo esempio abbiamo utilizzato la variabile globale GPIOB che fa da puntatore  e inizializzato la periferica B, abbiamo configurato come output il bit 0 dove abbiamo scritto 1 con il risultato di accendere il LED.

#### Display a 7 segmenti
nel nostro corso useremo una board supplementare collegata al nostro MCU, questa board è dotata di un display a 7 segmenti e quando il display è attivo avremo il timer TIM5 disabilitato

#### EXTI External Interrupt Controller
ogni linea digitale quando attiva può generare degli IRQ, questi possono essere configurati in 3 maniere:
- FALLING EDGE;
- RISING EDGE;
- BOTH EDGES;

il circuito che gestisce queste configurazioni è chiamato EXTI, External Interrupt Controller, questo gestisce 23 linee da EXTI0 a EXTI22, dove le prime 16 linee possono essere collegate agli ingressi digitali tramite un multiplexer,invece le ultime 7 linee sono collegate internamente, il multiplexes che serve per l'EXTI configura la sorgente di input per ogni linea EXTI.
Es
> se noi avessimo le linee GPIOB, GPIOC, GPIOD inizializzate come input tutti sulla stessa linea che per esempio diciamo sia la 1, dovremmo configurare ciascuna linea digitale con l'EXTI 1 nella seguente manira

GPIO_config_EXTI(GPIOB, EXTI1);
GPIO_config_EXTI(GPIOC, EXTI1);
GPIO_config_EXTI(GPIOD, EXTI1);

successivamente dobbiamo abilitare l'EXTI in fronte di discesa o fronte di salita, nella seguente maniera :

EXTI_enable(EXTI1, FALLING_EDGE);

così tutti gli interrupt della linea 1 saranno emessi in fronte di discesa ovvero al rilascio del pulsante.

#### Timers
il timer è un circuito che abilita l'MCU tramite software a comprendere lo scorrere del tempo, è una variabile globale di tipo contatore viene incrementata in base ad un clock programmabile, può generare degli interrupt e può essere utilizzato da circuiti secondari per generare particolari segnali periodici o misurare il periodo o l'impulso di un segnale in input, un timer è composto da 3 parti tutte programmabili :

1) Circuito di Clock;
>è il circuito che genera il tick dell'orologio.
2) Circuito time base;
>è il circuito che fa da contatore, da questo circuito deriva la granularità temporale, è formato da Prescaler, Counter ed Autoreload...
il prescale specifica il fattore di divisione del tempo, il contatore viene incrementato o decrementato in base al clock e deve arrivare fino all'ARR, una volta raggiunto il valore di ARR viene lanciato un evento che può attivare un interrupt
3) Circuiti secondari;
>Circuito che sfrutta la variabile temporizzatore per introdurre funzioni aggiuntive.

nel nostro MCU vi sono ben 11 differenti timer/contatori che possiedono le seguenti specifiche :
- Selezione di Clock;
- Risoluzione del contatore a 16/32bit
- Prescaler Programmabile;
- 4 canali configurabili e indipendenti;
- generatore di interrupt;

in un timer esistono diversi eventi che possono verificarsi, ogni evento genera un IRQ e quindi attiva la relativa ISR, queste funzionalità sono attivate da un bit in un timer SFR, per fare ciò si utilizza la funzione :
TIM_enable_irq(TIM, irq_type);
TIM serve a specificare di quale Timer stiamo parlando, mentre irq_type specifica il tip di ISR da attivare nel nostro caso utilizzavamo la IRQ_UPDATE una volta che l'evento avveniva chiamavamo l'ISR di TIMx_IRQHandler per gestire l'evento e tramite la funzione TIME_update_clear andavamo poi a pulire il bit e resettare l'evento 

#### ADC
un ADC è un circuito sequenziale che trasforma un segnale analogico in un segnale digitale, nel nostro caso trasforma tramite una legge lineare l'elettricità in un valore intero proporzionato al suo range di utilizzo, nel nostro caso il range di utilizzo della corrente era da 0 a 3.3V che era la corrente che arrivava alla board dalla presa USB, il funzionamento di un ADC si può descrivere in 3 modi :

    1) Campionamento;
    in questa fase viene caricato il condensatore per fare ciò l'interruttore deve essere chiuso, la sua durata viene chiamata Tempo di Campionamento.
    2) Quantizzazione;
    in questa fase viene aperto l'interruttore ed il segnale viene convertito conservato in una varibile a 16 bit, la sua durata viene chiamata tempo di quantizzazione.
    3) Fine della Quantizzazione;
    un bit di stato viene posto a 0 per indicare la fine della quantizzazione.

l'utilizzo di un ADC avviene tramite un multiplexer ed è esclusivo, una volta che un canale viene aperto, non se ne possono aprire altri fino alla chiusura, nel nostro caso i pin di input degli ADC sono gli stessi delle GPIO, in particolare alcuni pin GPIO possono essere programmati in maniera tale da essere usate come canale di input analogico per tanto non sarà utilizzata come GPIO, nel nostro STM32F4xx l'ADC ha più risoluzione e possono essere :

- 6 bit;
si lavora in un range da 0 a 63
- 8 bit;
si lavora in un range da 0 a 255
- 10 bit;
si lavora in un range da 0 a 1023
- 12 bit;
si lavora in un range da 0 a 4095

inoltre possiamo scegliere se allineare la conversione a destra o sinistra, ogni ADC ha diversi registri funzioni accessibili grazie a variabili globali chiamate ADCx nel nostro caso abbiamo solo ADC, per utilzzare l'ADC dovremmo usare le seguenti funzioni:
ADC_init(ADC, res, align);
> inizializza l'ADC con la risoluzione desiderata e l'allineamento.

ADC_channel_config(ADC, port, pin, chan);

> associa il canale, la porta e i pin da utilizzare con l'ADC.

ADC_on(ADC);
> accende l'ADC.

ADC_sample_channel(ADC, chan);
> dice all'ADC quale canale deve convertire.

successivamente in un loop devono essere eseguite queste funzioni:

ADC_start(ADC);
> inizia la conversione.

while(!ADC_completed(ADC)){};
> controlla se la conversione è stata completata.

ADC_read(ADC);
> legge il valore quantizzato.

dove :
ADC = nome dell'ADC da utilizzare;
res = Risoluzione da utilizzare da scegliere nel range [6-8-10-12];
align = bit di allineamento della conversione;
port = porta di input dell'ADC;
chan = canale associato all'input;

#### Opeazione Bitwise
sono quelle operazioni che vengono eseguite sui singoli bit e sono :

- AND;
questa operazione restituisce valore di verità solo ed esclusivamente quando il valore mascheratore con il valore mascherato hanno entrambi valore 1 nello stesso bit

Es.

vogliamo applicare una maschera con un AND per modificare il primo bit

1   |   0   |   0   |   1   |
--- |-------|-------|-------|

0   |   0   |   0   |   1   |
--- |-------|-------|-------|

diventerà

0  |   0   |   0   |   1   |
--- |-------|-------|-------|

- OR;
questa operazione restituisce valore di verità quando tra valore mascherato e valore mascheratore nella posizione vi è almeno uno dei due bit ad 1

Es.

1   |   0   |   0   |   1   |
--- |-------|-------|-------|

0   |   0   |   0   |   1   |
--- |-------|-------|-------|

diventerà 

1  |   0   |   0   |   1   |
--- |-------|-------|-------|

- XOR;
questa operazione restituisce valore di verità quando tra valore mascherato e valore mascheratore nella posizione vi è esclusivamente un solo 1.

Es.

1   |   0   |   0   |   1   |
--- |-------|-------|-------|

0   |   0   |   0   |   1   |
--- |-------|-------|-------|

diventerà 

 1  |   0   |   0   |   0   |
--- |-------|-------|-------|

- NOT;
si nega il valore quindi un bit 1 diventa 0 e viceversa

Es.

1   |   0   |   0   |   1   |
--- |-------|-------|-------|

diventerà 

0   |   1   |   1   |   0   |
--- |-------|-------|-------|

per le operazioni che utilizzeremo noi di set, clear, toggle si utilizzano le seguenti operazioni e maschere :

- Clear :
in questa operazione si utilizziamo un AND, con la maschera riempita di tutti 1 tranne nel bit che vogliamo pulire.
Es.
vogliamo pulire il bit 4 della nostra variabile A a 16 bit la mascherà sarà la seguente

1   |   1   |   1   |   1   |   1   |   1   |   1   |   1   |   1   |   1  |   1   |   0   |   1   |   1   |   1   |   1   |
----|-------|-------|-------|-------|-------|-------|-------|-------|------|-------|-------|-------|-------|-------|-------|

- Set:
in questa operazione si utilizza un OR, con la maschera riempita di 0 e 1 dove vogliamo settare il bit.


0   |   0   |   0  |   0   |   0   |   0   |   0   |   0   |   0   |   0  |   0   |   1   |   0   |   0   |   0   |   0   |
----|-------|------|-------|-------|-------|-------|-------|-------|------|-------|-------|-------|-------|-------|-------|

- Toggle:
in questa operazione si utilizza uno XOR, con la maschera riempita di 0 e 1 dove vogliamo settare il bit.

0   |   0   |   0  |   0   |   0   |   0   |   0   |   0   |   0   |   0  |   0   |   1   |   0   |   0   |   0   |   0   |
----|-------|------|-------|-------|-------|-------|-------|-------|------|-------|-------|-------|-------|-------|-------|

un'altro operazione da poter usare è lo Shift bit, si specifica il bit da shift e si applica un OR.
Es
A |= (1 << 3)
quindi avremo A = {B7, B6, B5, B4, B3, B2, B1, B0} e vogliamo applicare come da esempio uno shift di bit 1 al bit 3

0   |   0   |   0   |   0   |   1   |   0   |   0   |   0   |
----|-------|-------|-------|-------|-------|-------|-------|

#### UART

La UART o chiamata porta seriale è un ricevitore/trasmettitore asincrono universale, è una periferica per a comunicazione tra due device in modalità punto punto, un device può comunicare con un'altro device tramite UART usando i pin di comunicazione RX, TX... il Trasmettitore invia la comunciazione sul PIN TX e il Ricevitore la riceve su quello RX, poi si invertono se la comunicazione cambia direzione, questo tipo di trasmissione setta la linea dati di default a 1, ovvero quando non abbiamo passaggi di dati, invece quando vi è passaggio di dati la linea dati è impostata a 0, la codeword di trasmissione è formata da 1 bit iniziale a 0 e un bit finale a 1 i bit di mezzo saranno i dati da trasmettere, i parametri da settare per una trasmissione sono :
- velocità di trasmissione;
- numero di bit per caratteri;
- bit di parità;
- quantità di stop bit;

il circuito della UART possiamo suddividerlo in 3 blocchi :
1) Baud Rate Generator;
> è un divisore programmabile che è responsabile di generare il clock per la trasmissione dati, solitamene parte dalla metà della frequenza di clock.
2) Transmitter Circuit;
> questo circuito è responsabile di trasmettere i dati, per fare ciò i dati da trasmettere vengono caricati nel TX Data Register che poi verrà copiato nel TX Shift Register, una volta finita questa copiatura nel registro di stato verrà impostato il "TX Register Empty"
3) Receiver Circuit;
> questo circuito è responsabile di ricevere i dati, controlla la correttezza e il trasporto del data byte al programma, dentro questo circuito facciamo uso di un registro di shift che riceve 1 bit alla volta, una volta ricevuta l'intera codeword il contenuto dello shift register viene copiato nel Data Register RX e nel frattempo il valore "RX Register Not Empty" viene settato nel registro di stato

una trasmissione avviene in queste modalità : 

Configurazione del Baud Rate Generator -> il dato viene inviato al TX Data Register -> il dato viene spostato sul TX Shift Register -> il dato tramite i PIN TX ed RX viene trasmesso -> il dato viene ricevuto nel RX Shift Register -> il dato viene spostato sul RX Data Register

#### Basi di Elettronica
**Prima legge di Ohm**
la prima legge di Ohm stabilisce una relazione tra la Resistenza Elettrica, la Corrente Elettrica e la differenza di potenziale ed è la seguente :
$\Delta V = R*I$

dove :

> R = Resistenza;
> I = Intessità di Corrente;
> $\Delta V$ = Differenza di Potenziale

questa legge è fondamentale per sapere che tipo di resistore mettere nel circuito.

**Legge delle Maglie**
Un'altra legge importante per l'elettronica è la cosiddetta legge delle maglie, che enuncia ciò:
> La somma delle correnti dirette verso un nodo di una rete elettrica è uguale alla somma delle correnti che se ne allontanano 

oppure 

> attribuendo un verso arbitrario alle correnti che confluiscono in un nodo la loro somma algebrica delle intensità deve essere nulla

per tanto in un nodo A che è il fulcro di 5 correnti 3 entranti e 2 uscenti :

$I_1 \rightarrow A$
$I_2 \rightarrow A$
$I_3 \rightarrow A$
$I_4 \leftarrow A$
$I_5 \leftarrow A$

avremo :

$I_1 + I_2 + I_3 = I_4 + I5$

in elettronica non ci sono infiniti valori di resistori, ma questi sono creati in maniera standard quindi una volta calcolata la resistenza dobbiamo andare a prendere la resistenza con il valore più vicino a quello del calcolo...

Es.

abbiamo un sensore che funziona a 24 V che deve essere adattato a 5 V

$V_{in} = 24$
$V_{out} = 5$

calcoliamo la differenza di potenziale tra i due :

$ \Delta V = \frac{V_{out}}{V_{in}} = \frac{5}{24} = 0.21$

ora scegliamo il valore di $R_2$ che nel nostro sarà 10K$\Omega$ ed applichiamo la formula 

$\frac{R_2}{R_1 + R_2}* \Delta V = \frac{10}{R_1 + 10} * 0.21$
applicando formule algebriche alla fine avremo :
$R_1 = 37.619 k\Omega$

una volta trovato ciò, per trovare la differenza di potenziale dovremo semplicemente applicare la seguente formula : 

$V_{out} = \frac{R_2}{R_1 + R_2}*V_{in} = \frac{10}{10 + 39}* 24 = 4.9$

**Diodi**
I Diodi sono semi-conduttori che hanno due poli, Anodo e Catodo, se vengono polarizzati direttamenti vi è una caduta di potenziale e vi è il passaggio di corrente, se invece sono polarizzati inversamenti impediscono il passaggio di corrente, i LED sono dei diodi ed hanno :
- Tensione Diretta che varia nel range [1,2 - 3,0];
- Corrente Diretta al suo variare cambia la luminosità del diodo, in genere si assesta nell'ordine dei 20 mA

**Digital Input e resistenze di PULL-UP o PULL-DOWN**
un ingresso digitale non può essere lasciato aperto, perchè come un antenna catturerebbe qualsiasi cosa portando a lettura imprecise, per ovviare ciò si utilizzano 2 configurazione :
- Resistenza di Pull-UP;
in questa configurazione si installa una resistenza tra il potenziale e il GPIO, in questa maniera se il pulsante non è premuto il pin avrà stato logico 1, altrimenti 0.
- Resistenza di Pull-DOWN;
in questa configurazione si installa una resistenza tra il GPIO e la massa, in questa maniera se il pulsante non è premuto si avrà stato logico 0, altrimenti 1.

**Transistor**
i Transistor sono dei semi-conduttori che hanno 3 poli e funzionano come un amplificatore di potenziale/corrente, abbiamo diversi tipi di Transistor e sono : 
- BJT;
- JFET;
- MOSFET;
Questi Transistor funzionano come amplificatore di corrente/potenziale, hanno 3 poli chiamati :
- Gate;
- Source;
- Drain;

quando una differenza di potenziale passa in direzione gate $\rightarrow$ source la linea drain $\rightarrow$ source inizia a condurre con conseguente flusso di corrente, il comportamento di un MOSFET è governato da una legge di transconduttanza lineare.
è Interessante seguire il comportamento di un MOSFET in circuiti digitali perchè non è lineare, in questo caso il MOSFET funziona come switch che quando è saturo cortocircuita la linea Source $\rightarrow$ drain, la non linearità la possiamo ritrovare anche nei transistor BJT.

**Capacitori**
i Condensatori sono quegli elementi del circuito elettrico che servon oad immagazzinare carica elettrica, sono formati da due piastre separate da materiale dielettrico la loro unità di misura è il Farad.

**$I^2$C**
è un bus di comunicazione che viaggia alla velocità massima di 400 Kbps, ed è uno standard digitale usato per interconnettere circuiti integrati della stessa scheda, questa interfaccia funziona a ruoli e sono : 
- Master;
è il capo del bus ed è colui che inizia la comunicazione, vi è un solo master in un network e solitamente è l'MCU.
- Slave;
sono tutti i altri dispositivi che rispondono alle comunicazioni.

questo circuito è composto da due 2 linee :
- SDA;
è una Linea di Trasmissione Dati Bidirezionale.
- SCL;
Linea di Clock per la Trasmissione Dati.

in un Network $I^2C$ ogni device secondario ha una lista di device conosciuti, questi vengono riconosciuti dall'address che solitamente è a 7 bit, ma in casi specifici può essere anche a 10 bit, durante la comunicazione il primo bit dell'address viene aggiunto per specificare la direzione di trasferimento e può assumere 2 valori :
- 1 = scrivi sul device secondario;
- 0 = leggi dal device secondario

ogni device secondario ha una mappa dei registri dove ogni registri è identificato da 8 bit di indirizzi e 8 bit di valore, i registri sono usati per configurare il dispositivo, mandare comandi, conservare dati, come possiamo capire ogni registro può essere letto o riscritto dal master

**Write Transaction**
una write è emessa tramite i seguenti passaggi :

Master Inizia la trasmissione $\rightarrow$ il master richiama il device dal suo indirizzo e imposta B0 = 0 $\rightarrow$ il master invia il numero di registro da scrivere $\rightarrow$ il master manda il register data da scrivere $\rightarrow$ STOP

ogni volta che il master scrive qualcosa, lo slave risponde con un ACK

**Read Transaction**
Master Inizia la trasmissione $\rightarrow$ il master richiama il device dal suo indirizzo e imposta B0 = 0 $\rightarrow$ il master invia il numero di registro da scrivere $\rightarrow$ il Master inizia un'altra trasmissione $\rightarrow$ il master richiama il device dal suo indirizzo e imposta B0 = 1 $\rightarrow$  lo slave manda che il register data è stato letto $\rightarrow$ STOP

per ogni dato inviato sulla linea SDA il dispositivo ricevente manderà un ACK

#### SPI - Serial Peripheral Interface
è un interfaccia hardware che si usa per connettere circuiti integrati ad un MCU appartenente alla stessa scheda, anche questa interfaccia è un interfaccia Master-Slave, è formata da 4 piste :
- SCK;
Clock generato dal Master
- MOSI;
Dati inviati da Master a Slave
- MISO;
Dati Inviato da Slave a Master
- SS
serve al master per svegliare un device addormentato.

la comunicazione viene iniziata dal master che porta la linea SS in uno stato logico 0, successivamente il master genera un segnale di clock ed a ogni clock chi deve mandare dati occupa la linea di riferimento, alla fine della trasmissione il master riporta la linea SS a stato logico 1.

**Network SPI**
nel caso in cui vi sia un network di connessione SPI le connessioni delle linee devono essere effettuate in parallelo per le linee SCK, MOSI e MISO, mentro per la linea SS dovrà essere presente una linea SS per ogni device. Oltre alla normale connessione in parallelo si può effettuare la connessione in Daisy-Chain ovvero la linee MOSI e MISO verranno collegato in sequenza, la linea MOSI di un device viene collegata alla MISO del successivo e così via fino a collegare l'ultimo device con il master.

il clock nell'interfaccia SPI può essere programmato ed è di due tipologie : 
- CPOL = specifica l'inizio dello stato logico;
- CPHA = specifica il clock usato per campionare i dati e i dati saranno presenti nella comunicazione successiva;

#### Control Area Network CAN
è un interfaccia che usa il paradigma di comunicazione dei computer, non esistono master e slave in questo tipo di comunicazione al loro posto vi è l'arbitrato del bus ed inoltre si fa uso del paradigma publisher-subscriber, l'arbitrato viene attivato se più device vogliono comunicare simultaneamente il device che manda per primo un bit dominante (0) vince e sarà lui ad avere l'uso del BUS, mentre il paradigma publisher-subscriber è quel paradigma che viene usato quando un device si iscrive ad un evento e ogni volta che un device pubblica questo evento i vari subscriber recepiranno le informazioni, altrimenti lasciano stare il pacchetto di dati che è composto da 3 parti : 
1) 8 bytes di payload.
2) 11 bit di CAN-ID usato per identificare il dato e la sua priorità.
3) vari bit di controllo.