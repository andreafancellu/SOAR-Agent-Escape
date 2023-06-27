# Progetto ESCAPE

## Esecuzione del programma
In questo progetto è stato necessario utilizzare l'architettura cognitiva SOAR per modellare il comportamento di un agente rinchiuso in una stanza con l'obiettivo di scappare attraverso una finestra. 
Tale gente ha a disposizione diversi oggetti di cui però all'inizio non conosce il funzionamento, tuttavia attraverso un meccanismo di **Reinforcement Learning** l'agente imparerà a fare le giuste combinazioni di oggetti al fine di creare una fionda, rompere la finestra e scappare.

## Ipotesi di lavoro
L'ambiente è immaginato come una stanza con all'interno degli oggetti e una finestra. L'ambiente ha un attributo _^height_ che tiene traccia dell'altezza massima raggiungibile dall'agente in ogni momento.

Tutti gli oggetti (pietre, elastico, rametto, tronchi e finestra) sono  dispersi nell'ambiente. I movimenti dell'agente sono unicamente da e verso gli oggetti presenti nel mondo. 

Supponiamo inoltre che
- l'agente all'inizio di ogni episodio si trova in una posizione di partenza, *start*;
- l'agente può spostarsi verso gli oggetti nella stanza e raccoglierli;
- una volta che l'agente raccoglie due oggetti, ha la possibilità di combinarli, e nel caso lo faccia, non è più in grado di scomporli. Pertanto se dovesse creare una combinazione che non porta alla fuga, non avrebbe più alcun modo per fuggire. Ciò comporta, chiaramente, la terminazione del run . 

## Descrizione delle azioni
Le azioni che l'agente, in generale, può compiere sono le seguenti: *move*, *take*, *craft*, *shoot*, *stack*, _escape_. 

Muoversi verso un oggetto: ***move***
- *Condizioni:* 
	- la destinazione deve essere diversa dalla posizione di partenza;
- _Azioni:_ modificata la posizione dell'agente.

Prendere un oggetto: **_take_**
- _Condizioni:_ 
	- Devo essere in una location dove è presente un oggetto;
	- L'oggetto non deve essere già stato preso;
	- L'oggetto non deve essere la finestra.
- _Azioni:_ l'oggetto viene segnato come preso.

Comporre due oggetti: ***craft***
- _Condizioni:_ 
	- aver raccolto due oggetti componibili;
	- gli oggetti non devono essere stati utilizzati per altre combinazioni.
- _Azioni:_ l'agente possiede l'oggetto combinato e i due oggetti che l'hanno creato vengono segnati come utilizzati.
- _Ipotesi fatte:_ Le combinazioni possibili sono solamente tre: 
	- fionda (elastico-rametto)
	- pietre-rametto
	- pietre-elastico. 
	Le informazioni su queste combinazioni sono presenti nell'ambiente. Lo stato S, infatti, possiede un attributo _^combination_ che tiene traccia delle combinazioni possibili. Queste sono considerate come oggetti ma hanno due attributi _^item che specificano quali oggetti combinabili servano per costruirle.

Usare la fionda: ***shoot***
- _Condizioni:_ 
	- Trovarsi vicino alla finestra;
	- Aver costruito una fionda;
	- Aver raccolto i sassolini;
	- La finestra non deve essere già rotta.
- _Azioni:_ si prova a rompere la finestra, 
- _Ipotesi fatte:_ Abbiamo cinque posizioni in cui sparare alla finestra. *up*, *down*, *right*, *left*, *center*. Solo colpendo la posizione giusta, *right*, l'agente riesce a rompere la finestra.

Usare un tronco: ***stack***
- _Condizioni:_ 
	- Trovarsi vicino alla finestra;
	- Aver raccolto i tronchi.
- _Azioni:_ si posiziona il tronco vicino alla finestra, eventualmente sopra un altro tronco nel caso in cui questo sia presente
- _Ipotesi fatte:_ I tronchi hanno un attributo numerico _^height_ che specifica, appunto, quanto sono alti. L'applicazione di _utilize*tronco_ si limita, molto semplicemente, a sommare i valori di _^height_ del tronco attualmente in possesso dell'agente con l'attributo *^height* dell'ambiente.

Fuggire: **_escape_**
- _Condizioni:_ 
	- la finestra è rotta
	- l'agente può raggiungerla, cioè ha impilato i due tronchi uno sopra l'altro.
- _Azioni:_ fuga dalla stanza
- _Ipotesi fatte:_ Dopo che l'agente è fuggito, il run termina.

## Ricompense
Le ricompense vengono fornite all'agente dopo le seguenti azioni:
- movimento: 0 se verso la finestra, -1 altrimenti;
- combinazione di due oggetti: 1 se fionda, -1 altrimenti;
- colpo di fionda sulla finestra: 1 se rompe la finestra, -1 altrimenti.