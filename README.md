# Incapache_Unige

# Progetto incApache (Is Not Comparable to APACHE)

Questo repository contiene `incApache`, un server web concorrente sviluppato in C per gestire richieste HTTP. Il progetto è stato originariamente realizzato a supporto del laboratorio di Sistemi di Elaborazione e Trasmissione dell'Informazione (SETI) presso l'Università degli Studi di Genova.

## Autori e Riconoscimenti

**Nota importante:** L'architettura software e la quasi totalità del codice sorgente di base sono stati progettati e scritti dai professori **Giovanni Chiola** e **Giovanni Lagorio**.

Il mio lavoro all'interno di questo repository si è limitato a completare l'implementazione (sostituendo i blocchi didattici `/*** TO BE DONE ***/`) per rendere il server operativo, in grado di gestire la concorrenza, le risposte di rete e la logica del protocollo HTTP.

## Caratteristiche del Server

* **Supporto HTTP/1.0 e HTTP/1.1:** Il server è in grado di gestire richieste singole per connessione (HTTP/1.0) oppure più richieste successive sulla stessa connessione in modalità "pipeline" (HTTP/1.1), sfruttando thread multipli per velocizzare l'interazione con il client.
* **Sicurezza tramite chroot:** Per limitare i file accessibili al solo contenuto della directory `www-root`, il server utilizza la chiamata di sistema `chroot()`[cite: 12]. Per questo motivo, l'eseguibile richiede i privilegi di amministrazione (tramite `sudo` e flag `setuid` nel `Makefile`).
* **Risoluzione Mime-Type:** Il server avvia in background un processo figlio che utilizza il comando di sistema `file` (es. `file -i nomefile`) per determinare in tempo reale il `mime-type` da inserire nell'header HTTP.
* **Gestione Cookie:** Utilizza i Cookie HTTP per assegnare un identificatore a ogni nuovo utente e tracciare il numero di richieste provenienti dallo stesso client.

## Modifiche Apportate ai File Originali

Nel rispetto dei termini di licenza, dichiaro di aver modificato i seguenti file sorgente per implementare la logica mancante:
* **`incApache_http.c`**: Implementazione del parsing delle richieste (estrazione di metodo, file, protocollo e intestazioni come `If-Modified-Since`), calcolo e formattazione delle date GMT, impostazione dei cookie e trasferimento efficiente dei file al client tramite `sendfile()`.
* **`incApache_main.c`**: Configurazione del socket di ascolto (`getaddrinfo`, `socket`, `bind`), de-escalation dei privilegi di root prima di avviare il webserver e creazione del pool di thread iniziali.
* **`incApache_threads.c`**: Gestione sicura delle race conditions (tramite l'uso di mutex) per l'interrogazione del mime-type e implementazione della logica di accodamento e sincronizzazione dei thread di risposta (`pthread_join`).
* **`incApache_aux.c`**: Modifica della funzione `my_timegm` per la conversione sicura dei timestamp tramite manipolazione temporanea della variabile d'ambiente `TZ`.

## Compilazione ed Esecuzione

Per compilare il progetto, eseguire il comando `make` all'interno della root del repository. Il Makefile genererà il binario nella cartella `bin/` e, tramite `sudo`, imposterà il flag `setuid` e cambierà la proprietà in `root` per permettere l'uso di `chroot()`.

```bash
make all
```
Per lanciare il server, è necessario specificare la directory radice per i file web e, opzionalmente, la porta TCP (di default 8000)
```bash
bin/incapache www-root 8000
```
