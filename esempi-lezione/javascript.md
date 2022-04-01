# Appunti JavaScript

JavaScript (anche conosciuto come JS, ES o ECMAScript) è un linguaggio [interpretato](https://it.wikipedia.org/wiki/Interprete_(informatica)), [tipizzato dinamicamente](https://developer.mozilla.org/en-US/docs/Glossary/Dynamic_typing), [orientato agli oggetti](https://en.wikipedia.org/wiki/Object-oriented_programming), basato sui [prototipi](https://en.wikipedia.org/wiki/Prototype-based_programming) e che utilizza [funzioni di prima classe](https://developer.mozilla.org/en-US/docs/Glossary/First-class_Function).

La versione ES6 ha introtto numerose feature che risolvono alcune annose lacune del linguaggio. Prenderemo come riferimento questa versione per tutto il corso, ma vedremo anche alcune funzionalità deprecate che potreste incontrare su progetti già in essere.

Questi appunti non sono un manuale di JavaScript ma un copiaincolla (nemmeno completo al 100%) delle cose viste durante il live coding a lezione, solo un attimo riorganizzate. Ho cercato di aggiungere qualche informazione e copiaincolla da appunti vecchi per dare contesto a chi non ha seguito la lezione in diretta ma nulla di più.

Per gli approfondimenti e per avere maggior contesto, fate riferimento alla registrazione o alla solita documentazione su [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript).

Vi suggerisco di utilizzare [JSBin](https://jsbin.com/?js,console) per testare gli esempi di codice.

## Dichiarazione variabili

### Let e Const

La dichiarazione delle variabili in JS può avvenire in due modi:

```javascript
let a = 5;    // Se ho necessità di riassegnare un valore più avanti
const b = 7;  // Se voglio evitare che il valore venga riassegnato in futuro

a = 12; // Ok
b = 9;  // TypeError: Assignment to constant variable.
```

In entrambi i casi, non è necessario specificare quale sia il tipo di dato che vogliamo memorizzare (intero, stringa, array, ...) perché questo verrà inferito a run-time.

Come vedremo negli esempi successivi e soprattutto quando parleremo degli oggetti, in JS è preferibile utilizzare sempre `const` quando non abbiamo una reale necessità di riassegnare un valore alla nostra variabile.

### DON'T: Var
Nelle versioni di JS antecedenti alla ES6, veniva utilizzata la keyword `var` per dichiarare le variabili. 

Questa keyword è ancora supportata, tuttavia è preferibile evitare di usarla perché lo scope di visibilità delle variabili dichiarate con `var` non è limitato al blocco di codice in cui vengono dichiarate, bensì viene esteso all'intera funzione in cui avviene la dichiarazione.

```javascript
function myFunction() {
    var x = 10;

    if(x === 10) {
        var x = 20;
        console.log(x) // 20
    }

    console.log(x) // 20: il valore di x viene sovrascritto all'interno del blocco :(
}

function myFunction() {
    let x = 10;

    if(x === 10) {
        let x = 20;
        console.log(x) // 20
    }

    console.log(x) // 10 come ci aspettiamo :)
}
```

### DON'T: Non dichiarare le variabili
JavaScript permette di assegnare le variabili senza dichiararle.

In questo caso, la variabile assegnata viene aggiunta come  proprietà nello scope globale `window` (o `global` se siete su Node.js). Se quella proprietà esiste già, viene sovrascritta. 

Nell'esempio qui sotto andiamo ad assegnare una variabile che si chiama come la funzione `alert` definita da JS e che permette di aprire la finestra di modale sul browser.


```javascript
alert('Hello JS!'); // Viene mostrata la alert

function doNotTryAtHome() {
    alert = 'Non so dichiarare le variabili'; 
}

doNotTryAtHome();

alert('Hello JS!'); // ERRORE! Abbiamo sovrascritto una proprietà di window!
```

## Tipi di dato

JavaScript definisce 7 tipi di dati primitivi. I più importanti sono:

- **boolean** può assumere i valori *true* e *false*
- **number** un formato in virgola mobile per rappresentare dati numerici
- **string** per rappresentare dati testuali 

In aggiunta ai 7 tipi primitivi abbiamo **Object** che, come vedremo, sarà il tipo di qualsiasi altra entità esistente in JS: array, date, oggetti e anche funzioni, seppur con qualche particolarità.

Essendo un linguaggio dinamico, i tipi vengono assegnati a **run-time**;

```javascript
let a = 12;
console.log(typeof a); // number

a = 'Pippo'; 
console.log(typeof a); // string

a = false; 
console.log(typeof a); // boolean

a = {};
console.log(typeof a); // object
```

Il tipo di dato non viene quindi assegnato in modo statico quando si dichiara la variabile, ma viene determinato ad ogni assegnamento.

### Tipi e operazioni

Anche gli operatori si comporteranno in modo diverso in base al tipo degli operandi.

```javascript
// Entrambi gli operandi number
let a = 5;
let b = 3;

console.log(a + b); // 8
console.log(a - b); // 2

// Un operando number e uno string
a = 5;
b = '3';

console.log(a + b); // 53: il + effettua una concatenazione tra stringhe, convertendo il number in string
console.log(a - b); // 2: il - effettua la sottrazione, convertendo la stringa in number!
```

In generale è sconsigliato effettuare qualsiasi operazione con operandi di tipo diverso per non incorrere in comportamenti indesiderati.  

### Template literals (o template strings)

Per limitare la confusione, si può evitare di concatenare le stringhe con il + e utilizzare invece i template lirerals.

Un template literal è una stringa definita utilizzando il carattere *backtick* al posto degli apici.

```javascript
const saluto = `Ciao`;
```

Può contenere al suo interno dei *placeholder* che possiamo sfruttare per comporre stringhe complesse in modo più semplice e sicuro rispetto alla concatenzazione con il +.

```javascript
const nome = 'Paolino';
const cognome = 'Paperino';

const saluto = `Ciao ${nome} ${cognome}!`;

console.log(saluto); // Ciao Paolino Paperino!
```

## Oggetti

Un oggetto JavaScript è una collezione di coppie chiave-valore definita nel modo seguente:

```javascript
const user = {
    nome: 'Paolino',
    cognome: 'Paperino'
}
```

I valori possono essere un qualunque tipo di dato: number, string, altri oggetti e, come vedremo, anche funzioni.

Si può accedere alle proprietà dell'oggetto in due modi:

```javascript
console.log(user.nome); // Paolino
console.log(user['nome']); // Paolino

user.nome = 'Pippo';
console.log(user.nome); // Pippo
console.log(user['nome']); // Pippo
```

Il secondo metodo viene spesso usato quando bisogna gestire una risposta JSON da un backend che utilizza il kebab-case per definire le proprietà.

```javascript
const response = {
    "user-name": "Paolino",
    "user-surname": "Paperino",
}

console.log(response.user-name); // Errore: il trattino non è un carattere valido per variabili e proprietà in JS
console.log(response['user-name']); // Paolino
```

### const, let e proprietà degli oggetti

Nell'esempio precedente abbiamo visto come sia possibile modificare una proprietà di un oggetto nonostante sia stato dichiarato con la keyword `const`.

```javascript
const user = {
    nome: 'Paolino',
    cognome: 'Paperino'
}

user.nome = 'Gastone';
user.cognome = 'Paperone';

console.log(`${user.nome} ${user.cognome}`); // Gastone Paperone
```

Possiamo anche aggiungere delle proprietà a un oggetto esistente a run-time.

```javascript
user.city = 'Paperopoli';

console.log(`${user.nome} ${user.cognome}, ${user.city}`); // Gastone Paperone, Paperopoli
```

Ciò che `const` impedisce di fare è semplicemente di riassegnare l'oggetto `user` in quanto tale.

```javascript
user = {
    nome: 'Paperon',
    cognome: 'De Paperoni'
}; // TypeError: Assignment to constant variable.
```

Dal momento che questo riassegnamento può avvenire anche per errore, è buona pratica dichiarare tutte le variabili inizialmente come `const` e modificarle in `let` solo qualora fossimo sicuri di volerle riassegnare.

## Coercion e valori "truthy" e "falsy"
Quando JavaScript è stato sviluppato, si è pensato di rendere la verifica dell'uguaglianza di dati di tipo diverso più semplice e permettere di confrontare, ad esempio, numeri con stringhe o assegnare valori di verità o falsità arbitrari a entità come lo `0`, la stringa vuota, la stringa non vuota e così via.

Riporto alcuni esempi:

```javascript
12 == 12 // true
12 == 3 // false
12 == "12" // true
"" == 0 // true
0 == [] // true
[0] == 0 // true
[1] == 0 // false
```
Capite che spesso è difficile prevedere se un confronto restituirà `true` o `false`, perché l'algoritmo che effettua questo confronto si basa su una [tabella di uguaglianza]((https://dorey.github.io/JavaScript-Equality-Table/unified/)) estremamente complessa e onestamente molto arbitraria.

Se volete divertirvi a indovinare vi suggerisco:
- [JS Equality Jackpot](https://js-equality-jackpot.surge.sh/)
- [JavaScript Equality Table Game](https://eqeq.js.org/)

Per fortuna è stato introdotto anche l'operatore `===` che funziona in modo molto più intuitivo:
- Se il tipo degli operandi è diverso, restituisce `false`
- Se è uguale, restituisce `true` se anche i valori sono uguali, `false` altrimenti

Esiste anche `!==` che ha le stesse caratteristiche ma per verificare la non uguaglianza. 

## Costrutti condizionali

```javascript
const a = 5;
const b = 3;

if(a > b) {
    console.log('A è maggiore di B');
} else {
    console.log('A è minore o uguale a B');
}
```

```javascript
const a = 5;
const b = 8;
let result;
const op = "ADD";

switch(op) {
    case 'ADD':
        result = a + b;
        break;
    case 'SUB':
        result = a - b;
        break;
    case 'MUL':
        result = a * b;
        break;
    case 'DIV':
        result = a * b;
        break;
    default:
        result = null;
}
```

## Array

Gli array in JS sono collezioni di valori, e possono contenere anche tipi eterogenei.

```javascript
const pizze = ['Margherita', 'Marinara', 'Quattro Stagioni'];

// DON'T a meno che non sappiate cosa state facendo
const caos = [12, "Roma", {nome: 'Pippo'}];
```

Si accede agli elementi usando un indice che parte da 0 come nella maggior parte dei linguaggi di programmazione.

```javascript
console.log(pizze[1]); // Marinara
```

## Cicli
```javascript
const pizze = ['Margherita', 'Marinara', 'Quattro Stagioni', 'Diavola'];

// for "classico"
for(let i=0; i<pizze.length; i++) {
    console.log(pizze[i]);
}

// for in
for(let index in pizze) {
    console.log(pizze[index]);
}

// for of: raccomandato in quasi tutte le situazioni
for(let pizza of pizze) {
    console.log(pizza);
}

// L'output di tutti e tre i cicli sarà lo stesso:
// Margherita
// Marinara
// Quattro Stagioni
// Diavola
```

Oltre ai `for` sono ovviamente presenti tutti i cicli che avete visto negli altri linguaggi di programmazione, come il `while` e il `do while`.

## Funzioni

```javascript
function saluta(nome, cognome) {
  console.log(`Ciao ${nome} ${cognome}`);
}

saluta('Pinco', 'Pallo'); // Ciao Pinco Pallo
saluta('Tizio', 'Caio'); // Ciao Tizio Caio
saluta('Giorgio', 'Giorgi'); // Ciao Giorgio Giorgi
```

### Funzioni anonime (approfondimento)

JavaScript permette di definire delle funzioni senza nome, a patto che queste vengano invocate immediatamente o assegnate a una variabile.

```javascript
(function(nome, cognome) {
  console.log(`Io ti maledico ${nome} ${cognome}`);
})('Caio', 'Mario');

// Io ti maledico Caio Mario
```

Prima dell'avvento dei moduli, le funzioni anonime invocate immediatamente (IIFE) come la precedente, venivano utilizzate per implementare il cosiddetto **module pattern**.

L'obiettivo di questo pattern era quello di creare un sistema di namespace non "inquinare" l'oggetto globale window con proprietà definite dall'utente.

```javascript
window.tnv = {};

(function(namespace) {
    namespace.alert = (message) => console.log(message);
})(window.tnv)

alert('Hello JS!');  // Apre l'alert del browser
tnv.alert('Hello JS!');  // Usa la nosta implementazione custom
```

Oggi lo stesso scopo si può raggiungere con i moduli, che vedremo più avanti.

### First-class functions

JavaScrpt possiede quelle che vengono definite [First-class functions](https://developer.mozilla.org/en-US/docs/Glossary/First-class_Function), ovvero funzioni che possono essere trattate come qualsiasi altra variabile.

Dunque possono assegnate, passate come parametro di funzione, diventare proprietà di un oggetto e così via.

```javascript
const maledici = function(nome, cognome) {
  console.log(`Io ti maledico ${nome} ${cognome}`);
}

maledici('Gastone', 'Paperone'); // Io ti maledico Gastone Paperone
```

Questa definizione è esattamente equivalente a

```javascript
function maledici(nome, cognome) {
  console.log(`Io ti maledico ${nome} ${cognome}`);
}

maledici('Gastone', 'Paperone'); // Io ti maledico Gastone Paperone
```

### Hoisting, function declaration, e function expression

L'hoisting è un procedimento per il quale JS "solleva" le dichiarazioni nel punto più alto del loro scope prima di eseguire il codice.

```javascript
const nome = 'Paolino';
console.log(nome);
const cognome = 'Paperino';
console.log(cognome);
```

Verrà in effetti riscritto da JS come:

```javascript
const nome;
const cognome;

nome = 'Paolino';
console.log(nome);
cognome = 'Paperino';
console.log(cognome);
```

Uno degli effetti di questo procedimento che fa il precompilatore, è quello di permetterci di utilizzare funzioni prima che siano state effettivamente dichiarate nel codice.

```javascript
maledici('Gastone', 'Paperone'); // Io ti maledico Gastone Paperone

function maledici(nome, cognome) {
  console.log(`Io ti maledico ${nome} ${cognome}`);
}
```

Tutto funziona perché la function viene "sollevata" in questo modo:

```javascript
function maledici(nome, cognome) {
  console.log(`Io ti maledico ${nome} ${cognome}`);
}

maledici('Gastone', 'Paperone'); // Io ti maledico Gastone Paperone
```

Ma, attenzione, perché nel caso delle funzioni c'è una differenza importante nel modo in cui vengono "hoistate", in base alla sintassi usata per la dichiarazione.

Se infatti vengono dichiarate assegnando una funzione anonima a una variabile come abbiamo visto in precedenza, in seguito all'hoisting accade quanto segue:

```javascript
let maledici;

maledici('Gastone', 'Paperone'); // "ReferenceError: Cannot access 'maledici' before initialization

maledici = function(nome, cognome) {
  console.log(`Io ti maledico ${nome} ${cognome}`);
}
```

Questo non significa che un metodo sia migliore o peggiore dell'altro per dichiarare funzioni. A seconda di cosa dobbiamo fare entrambi hanno vantaggi e svantaggi. 

Bisogna però conoscere questo comportamento per evitre di incorrere in funzionamenti imprevisti ed errori.

## Arrow Functions

Esiste un terzo modo di esprimere una funzione in JavaScript e sarà probabilmente quello che useremo di più, perché presenta tutta una serie di vantaggi che vedremo più avanti.

La sintassi delle arrow functions consiste nell'indicare innanzitutto i parametri tra parentesi tonde poi la freccia `=>` (caratteri uguale e maggiore) e a seguire il corpo della funzione tra parentesi graffe.

```javascript
const benedici = (nome, cognome) => {
  return `Io ti benedico ${nome} ${cognome}`
};

console.log(benedici('Paolino', 'Paperino')); // Io ti benedico Paolino Giorgi
```

Se il corpo della funzione è costituito da una sola riga si possono evitare le graffe e la return

```javascript
const benedici = (nome, cognome) => `Io ti benedico ${nome} ${cognome}`;

console.log(benedici('Paolino', 'Paperino')); // Io ti benedico Paolino Paperino
```

Altri esempi di arrow functions possono essere:

```javascript
const totalLength = (nome, cognome) => nome.length + cognome.length;
const toUpperCase = (nome, cognome) => `${nome.toUpperCase()} ${cognome.toUpperCase()}`;
// ...
```


## Riepilogo sulle funzioni

```javascript
// Function declaration
function saluta(nome, cognome) {
  return `Ciao ${nome} ${cognome}`;
}

// Function expression
const maledici = function(nome, cognome) {
  return `Io ti maledico ${nome} ${cognome}`;
}

// Arrow function
const benedici = (nome, cognome) => {
  return `Io ti benedico ${nome} ${cognome}`
};

console.log(saluta('Pinco', 'Pallo')); // Ciao Pinco Pallo
console.log(maledici('Tizio', 'Caio')); // Ciao Tizio Caio
console.log(benedici('Giorgio', 'Giorgi')); // Ciao Giorgio Giorgi
```

## This binding
> Lo vediamo la prossima volta

## Prototype inheritance
> Lo vediamo la prossima volta

## Metodi sugli Array
> \[WARNING\] Abbiamo iniziato a vederli ma li riprendiamo la prossima volta, questa sezione potrebbe essere un po' confusionaria.

Abbiamo visto come visualizzare e stampare gli array in JS utilizzando dei cicli:

```javascript
const pizze = [
  {
    nome: 'Margherita',
    prezzo: 9,
    ingredienti: ['Farina', 'Pomodoro', 'Mozzarella']
  },
  {
    nome: 'Marinara',
    prezzo: 5,
    ingredienti: ['Farina', 'Pomodoro', 'Mozzarella', 'Aglio']
  },
  {
    nome: 'Contadina',
    prezzo: 19,
    ingredienti: ['Farina', 'Melanzane', 'Peperoni', 'Mozzarella']
  },
]

for(let pizza of pizze) {
  console.log(`${pizza.nome} - ${pizza.prezzo}`)
}

// Margherita - 13
// Marinara - 9
// Contadina - 19
```

Ma esiste un altro approccio che è preferibile perché ha diversi vantaggi, soprattutto nel momento in cui volessimo effettuare delle operazioni aggiuntive come filtrare dei dati, effettuare ordinamenti o mappare in modo diverso dei campi di un oggetto.

Ad esempio, ipotizziamo di voler copiare tutte le pizze che costano meno di 10 euro in un nuovo array utilizzando un ciclo for:

```javascript
let pizzeEconomiche = [];

for(let pizza of pizze) {
  if(pizza.prezzo < 10) {
    pizzeEconomiche.push(pizza); // Push inserisce un valore in coda a un array
  }
}

console.log(pizzeEconomiche);
```

La sintassi diventa molto imperativa: dobbiamo dichiarare un array in cui andare di volta in volta ad aggiungere le pizze dopo aver controllato che costino meno di 10 euro. Se dovessimo effettuare altre operazioni, la sintassi si complicherebbe ancora di più.

Possiamo perciò utilizzare i metodi sugli array che JS ci mette a disposizione. La caratteristica che li accomuna è che vanno a restituire un nuovo array dopo aver eseguito la funzione passata come argomento per ogni elemento dell'array.

### Filter

In alternativa potremmo usare il metodo Filter, con una sintassi molto più dichiarativa rispetto ai cicli:

```javascript
// Potete leggere la funzione come "x tale che x < 10"
const pizzeEconomiche = pizze.filter(x => x.prezzo < 10); 

console.log(pizze);
console.log(pizzeEconomiche);
```

### Map

Possiamo anche restituire un nuovo array "mappando" ogni elemento su qualcosa di diverso, nell'esempio sotto, degli oggetti su una stringa:

```javascript
const pizzeEconomiche = pizze
  .filter(x => x.prezzo < 10)              // pizze che costano meno di 10 euro
  .map(x => `${x.nome} - ${x.prezzo}`);    // modifichiamo l'array restituito in modo che contenga stringhe

console.log(pizzeEconomiche);
```

### Sort

Possiamo anche ordinare l'array con la funzione `sort`

```javascript
const pizzeEconomiche = pizze
  .filter(x => x.prezzo < 10)              // pizze che costano meno di 10 euro
  .map(x => `${x.nome} - ${x.prezzo}`)     // modifichiamo l'array restituito in modo che contenga stringhe
  .sort();                                 // Ordiniamo l'array restituito

console.log(pizzeEconomiche);
```

### Reduce

Altre funzioni ci permettono di effettuare operazioni che vanno a "combinare" gli elementi dell'array. Nel caso delle pizze potremmo ad esempio calcolare il totale di un ipotetico ordine.

```javascript
const totale = pizze
  .reduce((acc, x) => acc + x, 0);

console.log(`Totale: ${totale} euro`);
```
