# Libriperera - Progetto Database

Questo è il repository del progetto "Libriperera", un e-commerce per la vendita di prodotti editoriali online.

## Descrizione del progetto

Il progetto consiste nella creazione di un e-commerce chiamato Libriperera, dove i clienti possono acquistare prodotti editoriali. I clienti possono salvare e effettuare ordini, che verranno spediti all'indirizzo di spedizione specificato. I pagamenti possono essere effettuati tramite carta di credito o account PayPal.

Il sito web è accessibile ai clienti che si registrano e accedono utilizzando un nome utente e una password. I clienti hanno la possibilità di cercare prodotti nel catalogo completo di informazioni come descrizione, prezzo e disponibilità del prodotto, e di aggiungere gli articoli desiderati al carrello. Gli articoli rimarranno nel carrello anche dopo la disconnessione del cliente e verranno rimossi o modificati solo in determinate situazioni, ad esempio quando viene effettuato l'ordine, quando vengono rimossi manualmente dall'utente o quando lo stato degli articoli nel magazzino cambia (ad esempio, se gli articoli non sono più disponibili o se è disponibile una quantità inferiore).

Oltre ai clienti, ci sono altri due tipi di utenti nel sistema: l'Admin e il Super-admin. L'Admin, oltre ad essere un cliente, ha il privilegio di gestire le informazioni del catalogo degli articoli e può anche registrare nuovi admin. Il Super-admin è un admin con ulteriori privilegi, inclusi l'assegnazione e la revoca dei permessi di amministratore e la gestione degli ordini e dei pagamenti dei clienti.

## Glossario

Termini  | Descrizione | Sinonimi | Collegamenti
-------- | ----------- | -------- | ------------
Cliente | Utente iscritto al sito che può acquistare articoli e effettuare ordini | User | Indirizzo, Carta di credito, Carrello, Piattaforma cliente
Admin | Utente iscritto al sito con privilegi di gestione degli articoli nel catalogo | Gestore sito | Piattaforma admin, Articolo
Super-admin | Admin con privilegi aggiuntivi per la gestione degli articoli, degli ordini e dei pagamenti | Gestore sito | Ordine, Pagamento, Carta di credito/PayPal
Articolo | Prodotto editoriale disponibile per l'acquisto | Lista di articoli | Carrello, Ordine
Ordine | Lista di articoli acquistati da un cliente pronti per essere spediti | Acquisto | Indirizzo, Carta di credito
Indirizzo | Informazioni necessarie per la spedizione a un domicilio | Domicilio | Cliente, Carrello
Carrello | Lista di articoli salvati dall'utente per un eventuale acquisto | Articoli desiderati | Cliente, Articolo, Piattaforma cliente
Piattaforma cliente | Sito web lato cliente che permette di acquistare articoli e effettuare ordini | Sito web | Cliente
Piattaforma admin | Sito web lato admin che permette di gestire il catalogo degli articoli | Interfaccia di gestione | Admin

## Schema ER

Lo schema ER del progetto è stato ristrutturato per migliorare l'efficienza del database. Alcune informazioni ridondanti sono state eliminate e le generalizzazioni sono state risolte combinando le entità genitore con le entità figlie. Di seguito sono riportati i principali cambiamenti:

1. La generalizzazione tra "admin" e "persona" è stata eliminata e l'entità "cliente" è stata estesa per includere anche gli admin. È stato aggiunto un attributo "ruolo" per distinguere i ruoli degli utenti sulla piattaforma, con i due ruoli previsti attualmente: cliente e admin (che è un cliente con privilegi amministrativi).
2. La relazione "transazione admin" è stata rimossa e la relazione "transazione_cliente" è stata rinominata in "transazione_utente".
3. L'attributo "immagine" è stato rimosso dall'entità "articolo" in quanto un articolo può avere più di un'immagine. È stata aggiunta un'entità separata chiamata "immagine_articolo" e uno storico chiamato "gestore_immagine" per registrare le operazioni di modifica delle immagini.
4. Lo storico delle modifiche alle informazioni degli articoli è stato suddiviso in due entità distinte: "storico_articolo" per l'inserimento/eliminazione di nuovi articoli e "registro_modifiche_articolo" per le modifiche ai singoli campi degli articoli esistenti.

## Lista delle operazioni

Il sistema supporta le seguenti operazioni:

- Login: permette all'utente di accedere utilizzando nome utente e password.
- Logout: permette all'utente di disconnettersi dall'account.
- Add to cart: permette all'utente di aggiungere un articolo al carrello specificando la quantità desiderata.
- Remove: permette all'utente di rimuovere un articolo dal carrello.
- Ricerca articolo: permette all'utente di cercare articoli utilizzando una parola chiave.
- Filtro catalogo: permette all'utente di filtrare gli articoli nel catalogo in base alla tipologia di prodotto editoriale.
