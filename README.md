# Libriperera - Progetto Database

Questo è il repository del progetto "Libriperera", un e-commerce per la vendita di prodotti editoriali online.

## Descrizione del progetto

Il progetto consiste nella creazione di un e-commerce chiamato Libriperera, i clienti possono acquistare prodotti editoriali salvandoli nel carrello e effettuando l'ordine.
L'ordine e composto da informazioni legati al cliente come pagamento e indirizzo di spedizione.

 - Il sito web è accessibile ai clienti che si registrano e accedono utilizzando un nome utente e una password.
 -  I clienti hanno la possibilità di cercare prodotti nel catalogo completo di informazioni come descrizione, prezzo e disponibilità del prodotto, e di aggiungere gli articoli desiderati al carrello.
 -  Gli articoli rimarranno nel carrello anche dopo la disconnessione del cliente e verranno rimossi o modificati solo in determinate situazioni, ad esempio quando viene effettuato l'ordine, quando vengono rimossi manualmente dall'utente o quando lo stato degli articoli nel magazzino cambia (ad esempio, se gli articoli non sono più disponibili o se è disponibile una quantità inferiore).
  

- Oltre ai clienti, ci sono altri due tipi di utenti nel sistema: l'Admin e il Super-admin.
  - L'Admin, oltre ad essere un cliente, ha il privilegio di gestire le informazioni del catalogo degli articoli e può anche registrare nuovi admin.
  - Il Super-admin è un admin con ulteriori privilegi, inclusi:
    - l'assegnazione e la revoca dei permessi di amministratore
    - la gestione degli ordini cliente come rimborso o rimborso parziale.
  
- Il database è arricchito da Trigger di segnalazione di operazioni rilevanti come:
  - creazione di un ordine da parte di un cliente
  - creazione o elimina di un articolo da parte di un admin
  - modifica di un articolo da parte di un admin 
  - rimborso parziale o totale di un ordine
  - segnare un ordine come spedito
  - restock o rimozione di una certa quantità dello stock di un determinato articolo
  - registrazione di un nuovo admin
  - subscription di un nuovo cliente sul sito


## Glossario

Termini  | Descrizione | Sinonimi | Collegamenti
-------- | ----------- | -------- | ------------
Cliente | Utente iscritto al sito che può acquistare articoli e effettuare ordini | User | Indirizzo, Carta di credito, Carrello, Piattaforma cliente
Admin | Utente iscritto al sito con privilegi di gestione degli articoli nel catalogo | Gestore sito | Piattaforma admin, Articolo
Super-admin | Admin con privilegi aggiuntivi per la gestione degli articoli, degli ordini e dei pagamenti | Gestore sito | Ordine, Pagamento, Carta di credito/PayPal
Articolo | Prodotto editoriale disponibile per l'acquisto | Lista di articoli | Carrello, Ordine
Ordine | Lista di articoli acquistati da un cliente pronti per essere spediti | Acquisto | Indirizzo, Carta di credito
ituoiordini | Lista di ordini acquistati da un cliente con il relativo status dell'ordine | Acquisto | Indirizzo, Carta di credito
Indirizzo | Informazioni necessarie per la spedizione a un domicilio | Domicilio | Cliente, Carrello
Carrello | Lista di articoli salvati dall'utente per un eventuale acquisto | Articoli desiderati | Cliente, Articolo, Piattaforma cliente
Piattaforma cliente | Sito web lato cliente che permette di acquistare articoli e effettuare ordini | Sito web | Cliente
Piattaforma admin | Sito web lato admin che permette di gestire il catalogo degli articoli | Interfaccia di gestione | Admin

## Schema ER

Alcune scelte compiute direttamente nello schema ER del progetto sono:
- di fare un carrello non in sessione così da dare la comodità ad un cliente di fare logout mantenendo  gli articoli salvati per un futuro acquisto,chiaramente questa scelta ha portato ad una gestione più complessa, visto che è necessario che il cliente faccia sempre l'acquisto di articoli disponibili nella quantità richiesta.
- Separare Articolo in 3 tabelle differenti
  - articolo: tutte le informazioni relative al prodotto come Nome, Editore, Prezzo, Descrizione ecc...
  - stock:  informazioni relative alla disponibilità degli articoli nel magazzino dell'ipotetico negozio online
  - immagine_articolo: tutte le immagini di un articolo, così da dare la possibilità di avere 'n' immagini per articolo nel caso il sito ne dovesse avere bisogno in futuro (attualmente ne sono richieste 3)
- L'admin come cliente con privilegi di gestione del sito, per dargli il punto di vista di un cliente al 100%, e per evitare di far fare ad un admin un altro account lui stesso può essere un cliente.
  

![ER_libriperera drawio](https://github.com/Coco01010/Libriperera-site/assets/99124492/be9c01ef-cfa6-4f4f-a6a7-77d5d800bd01)

## Diagramma E-R Ristrutturato


![ER-ristrutturato_libriperera drawio (1)](https://github.com/Coco01010/Libriperera-site/assets/99124492/e92806f0-1921-48da-aea1-4e9a784f2c88)


Lo schema ER del progetto è stato ristrutturato per migliorare l'efficienza del database. Alcune informazioni ridondanti sono state eliminate e le generalizzazioni sono state risolte combinando le entità genitore con le entità figlie. Di seguito sono riportati i principali cambiamenti:

1. La generalizzazione tra "admin" e "persona":  è stata eliminata e l'entità "cliente" è stata estesa per includere anche gli admin. È stato aggiunto un attributo "ruolo" per distinguere i ruoli degli utenti sulla piattaforma, con i due ruoli previsti attualmente: cliente e admin (che è un cliente con privilegi amministrativi).
2. La relazione "transazione admin": è stata rimossa e la relazione "transazione_cliente" è stata rinominata in "transazione_utente".
3. L'attributo "immagine" è stato rimosso dall'entità "articolo": in quanto un articolo può avere più di un'immagine. È stata aggiunta un'entità separata chiamata "immagine_articolo" e uno storico chiamato "gestore_immagine" per registrare le operazioni di modifica delle immagini.
4. Lo storico delle modifiche: alle informazioni degli articoli è stato suddiviso in due entità distinte: "storico_articolo" per l'inserimento/eliminazione di nuovi articoli e "registro_modifiche_articolo" per le modifiche ai singoli campi degli articoli esistenti.
5. suddivisione di operazione articolo: tra storico_articolo e articolo, avremo le row: descrizione , prezzo, editore, nome in entrambi che danno la stessa informazione, questa è una ridondanza ma che è utile piu avanti quando si modficheranno i dati in ‘articolo’, cosi avremo i dati originali di un articolo, e le ulteriori modifiche verranno segnate su un altra tabella ‘modifiche_articolo’ che invece di salvarsi tutte le row ogni volta, si salva solo il singolo dato cambiato(operazione che ritengo più probabile). Se togliessi scegliere di eliminare la prima ridondanza(che potrebbe essere anche temporanea) dovrei necessariamente togliere le row da articolo rendendo tutto piu confusionario.
6. separazione di articolo da  immagine_articolo : banalmente il sito utilizza massimo  3 immagini , nulla vietà di inserirne altri per ogni articolo e utilizzarli nell'eventualità di un aumento di immagine da mostrare in un inserzione o per altre funzionalità future. (sistema scalabile).


## Lista delle operazioni

Il sistema supporta le seguenti operazioni:

- Login: permette all'utente di accedere utilizzando nome utente e password.
- Logout: permette all'utente di disconnettersi dall'account.
- Add to cart: permette all'utente di aggiungere un articolo al carrello specificando la quantità desiderata.
- Remove: permette all'utente di rimuovere un articolo dal carrello.
- Ricerca articolo: permette all'utente di cercare articoli utilizzando una parola chiave.
- Filtro catalogo: permette all'utente di filtrare gli articoli nel catalogo in base alla tipologia di prodotto editoriale.
- I tuoi articoli: lista degli ordini del singolo utente con informazioni relative all'ordine.
- Acquisto tramite PayPal: pagamento effettuato dal cliente tramite il servizio PayPal. (simulato da un tasto acquista)

Operazioni riservate agli Admin:

- Edit articolo: modificare le informazioni relative a un articolo.
- set/unset articolo: mostra sul sito lato cliente l'articolo oppure lo toglie.
- Delete articolo: rimuovere un articolo dal catalogo.
- Add articolo: aggiungere un nuovo articolo al catalogo.
- Mostra articolo: mostrare le informazioni relative a un singolo articolo.
- Registrare nuovo Admin: registrare un nuovo admin sul sito.

## Traduzione verso il modello relazionale:

utente (id_utente, username, ruolo, email, password)

articolo (ISBN, Nome, Autore, Editore, Descrizione, Prezzo, Tipologia)

stock (id_prodotto, Disponibilità)

immagine_articolo (ISBN*, numero_immagine, image)

carrello (id_utente*, id_prodotto*, quantità)

indirizzo(id_indirizzo, id_cliente*, via, admin_area, countrycode, postalcode)

pagamento(id_pagamento, id_movimento, id_utente*, payment_status, payer_email, operazione, importo)

ordine(id_ordine, id_cliente*, id_pagamento*, id_indirizzo*, Data_inserimento, costo_totale, status_ordine)

prodotto_ordinato (id_prodotto*, id_ordine, quantità, status_prodotto)

operazione_stock(id_operazione_stock, utente*, id_prodotto*, id_ordine*, operazione, quantità, data_operazione)

gestione_stock(id_gestione_stock, admin*, id_prodotto*, operazione, valore_precedente, valore_successivo)

storico_articolo(id_storico_art, admin*, ISBN*, operazione, data_operazione, Nome, Autore, Editore, Descrizione, Prezzo, Tipologia)

registro_modifiche_articolo(id_operazione_art, campo_modificato, admin*, ISBN*, data_operazione, valore_precedente)

gestione_immagine(id_gestione_immagine, admin*, ISBN*, numero_immagine*, operazione, immagine)

operazione_ordine(id_operazione_ordine, admin*, ISBN*, operazione, data_operazione)

creazione_admin(id_creazione_admin, admin_loggato*, admin_aggiunto*, data_inserimento)

iscrizione_utente(id_iscrizione_utente*, id_utente, data_iscrizione)



## Triggers
Il database su Altervista è stato arricchito di Trigger per compiere principalemente segnalazione di operazioni importanti in modo indipendente dal sito web,
altre operazioni sono state implementate sottoforma di trigger per l'assenza di funzionalità in Altervista( Campi calcolati assenti in altervista) o per altri motivi legati all'indipendenza che volevo far ottenere al DB.

### Lista triggers


<details>
<summary>Creazione di un ordine (trace_new_order)</summary>
 
```sql
CREATE TRIGGER `trace_new_order` AFTER INSERT ON `ordine`
FOR EACH ROW BEGIN
    INSERT INTO operazione_su_ordine (id_ordine, id_utente, operazione)
    VALUES (NEW.id_ordine, NEW.id_cliente, 'new_order');
END
```
</details>

- Quando viene creato un nuovo ordine da parte di un cliente, viene registrata un'operazione nella tabella   "operazione_su_ordine" per tracciare l'evento.


<details>
<summary>Segnalazione ordinazione di ogni prodotto in un ordine (trace_single_ordered_product)</summary>

```sql
CREATE TRIGGER `trace_single_ordered_product` AFTER INSERT ON `prodotto_ordinato`
FOR EACH ROW BEGIN
    IF NEW.status_prodotto = 'ordered' THEN
        INSERT INTO operazione_stock (id_ordine, id_stock, quantity, operazione)
        VALUES (NEW.id_ordine, NEW.id_stock, NEW.quantity, 'ordered');
    END IF;
END

```
</details>

- Quando viene creato un nuovo ordine da parte di un cliente, viene registrato ogni prodotto in "prodotto_ordinato" segnando lo "status_ordine = ordered".
Questa tabella è infatti una lista dei prodotti dentro un ordine, rimborsati e non.

<details>
<summary>Campo calcolato stock (update_disponibilità_1 e update_disponibilità_2)</summary>
 
```sql
-- GESTIONE STOCK
CREATE TRIGGER `update_disponibilità_1` AFTER INSERT ON `gestione_stock`
FOR EACH ROW BEGIN
    IF NEW.operazione = 'restock' THEN
        UPDATE stock
        SET disponibilità = disponibilità - NEW.quantity
        WHERE id_stock = NEW.id_stock;
    ELSEIF NEW.operazione = 'remove' THEN
        UPDATE stock
        SET disponibilità = disponibilità + NEW.quantity
        WHERE id_stock = NEW.id_stock;
    END IF;
END

-- OPERAZIONE STOCK
CREATE TRIGGER `update_disponibilità_2` AFTER INSERT ON `operazione_stock`
FOR EACH ROW BEGIN
    IF NEW.operazione = 'ordered' THEN
        UPDATE stock
        SET disponibilità = disponibilità - NEW.quantity
        WHERE id_stock = NEW.id_stock;
    ELSEIF NEW.operazione = 'refunded' THEN
        UPDATE stock
        SET disponibilità = disponibilità + NEW.quantity
        WHERE id_stock = NEW.id_stock;
    END IF;
END

```

</details>

- Campo calcolato implementato con due trigger, quindi un campo che è la sommatoria delle entrate e delle uscite degli admin (restock, e rimozione articolo) e cliente (ordinazione e rimborso parziale o totale) dei campi gestione_stock e operazione_stock.
- Questa è stata la soluzione all'assenza dei computed column (serve per rendere i due campi comunicanti rispetto alla 'disponibilità').
ovvero un campo che ha come tipo (intero, varchar, ecc.) una formula.
Non è presente questa funzionalità su Altervista, quindi ho implementato il tutto con due trigger: update_disponibilità_1 e update_disponibilità_2.

<details>
<summary>Aggiunta e rimozione di un articolo (remove_articolo_trace e trace_new_articolo)</summary>
 
```sql
CREATE TRIGGER `trace_remove_articolo` BEFORE DELETE ON `articolo`
FOR EACH ROW BEGIN
    -- Salvo la quantità da stock
    DECLARE stockquantity INT;
    SELECT disponibilità INTO stockquantity
    FROM stock
    WHERE ISBN = OLD.ISBN;

    -- Inserisco la row di segnalazione
    INSERT INTO storico_articolo (operazione, ISBN, Nome, Editore, Autore, Descrizione, Prezzo, Quantità, Tipologia)
    VALUES ('removed', OLD.ISBN, OLD.Nome, OLD.Editore, OLD.Autore, OLD.Descrizione, OLD.Prezzo, stockquantity, OLD.Tipologia);

    DELETE FROM stock WHERE ISBN = OLD.ISBN;
END

```
- Per la creazione di un articolo, invece, è la stessa cosa, ma lascio la quantità di default. Se necessario, viene segnato con PHP.

```sql
CREATE TRIGGER `trace_new_articolo` AFTER INSERT ON `articolo`
FOR EACH ROW BEGIN
    -- Inserisco la row di segnalazione
    INSERT INTO storico_articolo (operazione, ISBN, Nome, Editore, Autore, Descrizione, Prezzo, Tipologia)
    VALUES ('added', NEW.ISBN, NEW.Nome, NEW.Editore, NEW.Autore, NEW.Descrizione, NEW.Prezzo, NEW.Tipologia);
END

```

</details>

- Questo trigger viene utilizzato per tracciare la rimozione di un articolo nella tabella 'articolo'. Prima di eliminare la riga dell'articolo, vengono registrati tutti i dati dell'articolo insieme alla data dell'operazione, consentendo di recuperare le informazioni relative all'articolo rimosso in precedenza.

- Inoltre, viene effettuata la cancellazione della riga corrispondente nella tabella 'stock', e la quantità viene aggiunta al tracciamento.

- Per implementare la compilazione del campo 'admin', che indica quale amministratore ha effettuato questa operazione, è necessario gestirlo attraverso il codice PHP.

<details>
 
<summary>Semplice aggiornamento prodotto_ordinato/ordine (update_ordine_refunding_case)</summary>

```sql
CREATE TRIGGER `update_ordine_refunding_case` AFTER UPDATE ON `prodotto_ordinato`
FOR EACH ROW BEGIN
    DECLARE total_refunded INT;
    DECLARE total_items INT;

    IF NEW.status_prodotto = 'refunded' THEN
        SELECT COUNT(*) INTO total_refunded
        FROM prodotto_ordinato
        WHERE id_ordine = NEW.id_ordine AND status_prodotto = 'refunded';

        SELECT COUNT(*) INTO total_items
        FROM prodotto_ordinato
        WHERE id_ordine = NEW.id_ordine;

        IF total_refunded = total_items THEN
            UPDATE ordine
            SET status_ordine = 'refunded'
            WHERE id_ordine = NEW.id_ordine;

            INSERT INTO operazione_su_ordine (id_ordine, operazione)
            VALUES (NEW.id_ordine, 'mark_as_refunded');
        ELSE
            UPDATE ordine
            SET status_ordine = 'partially_refunded'
            WHERE id_ordine = NEW.id_ordine AND status_ordine = 'in_attesa';

            INSERT INTO operazione_su_ordine (id_ordine, operazione)
            VALUES (NEW.id_ordine, 'mark_as_partially_refunded');
        END IF;
    END IF;
END

```

</details>

- Questo trigger si attiva all'update di status_prodotto in 'refunded' nella tabella prodotto_ordinato.

- Cambia lo status_ordine della tabella ordine da 'in_attesa' a 'partially_refunded' oppure 'refunded' (lo segna anche in operazione_su_ordine per entrambi i casi) se tutti i prodotti in quell'ordine sono stati refundati.

<details>
<summary>Segnalazione dei prodotti del reso (trace_refund_operation)</summary>

```sql
CREATE TRIGGER `trace_single_refund` AFTER UPDATE ON `prodotto_ordinato`
FOR EACH ROW BEGIN
    IF NEW.status_prodotto = 'refunded' THEN
        INSERT INTO operazione_stock (id_ordine, id_stock, quantity, operazione)
        VALUES (NEW.id_ordine, NEW.id_stock, NEW.quantity, 'refunded');
    END IF;
END

```

</details>

- Crea una riga per ogni articolo refundato in operazione_stock (si sommano le quantità con stock, quindi ritornano gli articoli nel magazzino). Devo dare un id unico ad ogni riga di operazione_stock, così si può identificare una singola operazione di refund su più prodotti (la primary key è composta da id_operazione_stock e id_stock). Questa cosa va fatta in PHP.


<details>
<summary>Un ordine è stato spedito (update_ordine)</summary>

```sql
CREATE TRIGGER `update_ordine` AFTER UPDATE ON `ordine`
FOR EACH ROW BEGIN
    IF NEW.status_ordine = 'shipped' THEN
        -- Inserisce una riga in operazione_su_ordine
        INSERT INTO operazione_su_ordine (id_ordine, operazione)
        VALUES (NEW.id_ordine, 'mark_as_shipped');

        -- Aggiorna lo status_prodotto dei prodotti in prodotto_ordinato
        UPDATE prodotto_ordinato
        SET status_prodotto = 'shipped'
        WHERE id_ordine = NEW.id_ordine AND status_prodotto = 'ordered';
    END IF;
END

```

</details>

- Se un ordine cambia status_ordine a 'shipped', allora lo segna nella tabella operazione_su_ordine e cambia lo status_prodotto di tutti i prodotti in quell'ordine da 'ordered' a 'shipped' (tranne quelli refundati).


<details>
<summary>Traccia creazione admin e iscrizione utente (trace_new_admin e trace_subscription)</summary>
 
```sql
-- Per admin
CREATE TRIGGER `trace_new_admin` AFTER INSERT ON `utente`
FOR EACH ROW BEGIN
    IF NEW.ruolo = 'Admin' THEN
        INSERT INTO creazione_admin (admin_aggiunto) VALUES (NEW.id_utente);
    END IF;
END

-- Per cliente
CREATE TRIGGER `trace_subscription` AFTER INSERT ON `utente`
FOR EACH ROW BEGIN
    IF NEW.ruolo = 'Cliente' THEN
        INSERT INTO iscrizione_utente (id_cliente) VALUES (NEW.id_utente);
    END IF;
END

```

</details>


- Implementazione e segnalazione su due tabelle separate perché l'admin ha bisogno di un campo in più (l'admin che lo ha creato), mentre il cliente si iscrive da solo (ed è molto più probabile che si iscriva, quindi non volevo che ci fosse un campo con un'informazione inutile frequente).

- Due trigger segnalano il nuovo utente aggiunto in base al ruolo che hanno (cliente o admin). In quello per l'admin, successivamente in PHP, marchio l'admin creatore nella riga. 

<details>
<summary>Traccia le modifiche di un campo di articolo (trace_update_field_articolo)</summary>

```sql
CREATE TRIGGER `trace_update_field_articolo` AFTER UPDATE ON `articolo`
FOR EACH ROW BEGIN
    -- Campo Nome
    IF NEW.Nome <> OLD.Nome THEN
        INSERT INTO registro_modifiche_articolo (campo_modificato, nuovo_valore, ISBN)
        VALUES ('Nome', NEW.Nome, OLD.ISBN);
    END IF;

    -- Campo Editore
    IF NEW.Editore <> OLD.Editore THEN
        INSERT INTO registro_modifiche_articolo (campo_modificato, nuovo_valore, ISBN)
        VALUES ('Editore', NEW.Editore, OLD.ISBN);
    END IF;

    -- Campo Autore
    IF NEW.Autore <> OLD.Autore THEN
        INSERT INTO registro_modifiche_articolo (campo_modificato, nuovo_valore, ISBN)
        VALUES ('Autore', NEW.Autore, OLD.ISBN);
    END IF;

    -- Campo Descrizione
    IF NEW.Descrizione
        INSERT INTO registro_modifiche_articolo (campo_modificato, nuovo_valore, ISBN)
        VALUES ('Descrizione', NEW.Descrizione, OLD.ISBN);
    END IF;

    -- Campo Prezzo
    IF NEW.Prezzo <> OLD.Prezzo THEN
        INSERT INTO registro_modifiche_articolo (campo_modificato, nuovo_valore, ISBN)
        VALUES ('Prezzo', NEW.Prezzo, OLD.ISBN);
    END IF;

    -- Campo articolo_status
    IF NEW.articolo_status <> OLD.articolo_status THEN
        INSERT INTO registro_modifiche_articolo (campo_modificato, nuovo_valore, ISBN)
        VALUES ('articolo_status', NEW.articolo_status, OLD.ISBN);
    END IF;

    -- Campo Tipologia
    IF NEW.Tipologia <> OLD.Tipologia THEN
        INSERT INTO registro_modifiche_articolo (campo_modificato, nuovo_valore, ISBN)
        VALUES ('Tipologia', NEW.Tipologia, OLD.ISBN);
    END IF;
END
```

</details>

- Viene segnato con una riga in 'registro_modifiche_articolo' per ogni campo modificato.

<details>
<summary>Traccia aggiunta immagine (trace_add_image)</summary>

```sql
CREATE TRIGGER `trace_add_image` AFTER INSERT ON `immagine`
FOR EACH ROW BEGIN
    INSERT INTO registro_modifiche_immagine (operazione, id_immagine, id_articolo)
    VALUES ('added', NEW.id_immagine, NEW.id_articolo);
END

```

</details>

- Quando viene aggiunta un'immagine a un articolo, viene creata una riga nella tabella "registro_modifiche_immagine" per tracciare l'operazione.
  
<details>
<summary>Traccia modifica immagine (trace_update_image)</summary>

```sql
CREATE TRIGGER `trace_update_image` AFTER UPDATE ON `immagine`
FOR EACH ROW BEGIN
    INSERT INTO registro_modifiche_immagine (operazione, id_immagine, id_articolo)
    VALUES ('updated', NEW.id_immagine, NEW.id_articolo);
END

```
</details>

- Quando viene modificata un'immagine associata a un articolo, viene creata una riga nella tabella "registro_modifiche_immagine" per tracciare l'operazione. 
