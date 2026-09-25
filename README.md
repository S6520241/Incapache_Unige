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
## Licenza
Questo progetto è distribuito sotto la licenza GNU General Public License (GPL) v2.

Tutti gli avvisi di copyright originali appartenenti a Giovanni Chiola e Giovanni Lagorio sono mantenuti intatti. È permesso copiare, distribuire e modificare il codice sorgente rispettando le medesime condizioni previste dalla licenza. Per i termini completi, fare riferimento al file COPYING incluso nel repository

_____________________________________________________________

# Incapache_Unige

# incApache Project (Is Not Comparable to APACHE)

This repository contains `incApache`, a concurrent web server developed in C to handle HTTP requests. The project was originally created to support the Information Processing and Transmission Systems (SETI) laboratory at the University of Genoa.

## Authors and Acknowledgments

**Important note:** The software architecture and almost all of the base source code were designed and written by professors **Giovanni Chiola** and **Giovanni Lagorio**.

My work within this repository was limited to completing the implementation (replacing the educational blocks `/*** TO BE DONE ***/`) to make the server operational, capable of handling concurrency, network responses, and HTTP protocol logic.

## Server Features

* **HTTP/1.0 and HTTP/1.1 Support:** The server can handle single requests per connection (HTTP/1.0) or multiple successive requests on the same connection in "pipeline" mode (HTTP/1.1), utilizing multiple threads to speed up client interaction.
* **Security via chroot:** To limit accessible files strictly to the contents of the `www-root` directory, the server uses the `chroot()` system call. For this reason, the executable requires administrative privileges (via `sudo` and the `setuid` flag in the `Makefile`).
* **Mime-Type Resolution:** The server launches a child process in the background that uses the system `file` command (e.g., `file -i filename`) to determine the `mime-type` in real-time to be included in the HTTP header.
* **Cookie Management:** It uses HTTP Cookies to assign an identifier to each new user and track the number of requests coming from the same client.

## Modifications to Original Files

In compliance with the license terms, I declare that I have modified the following source files to implement the missing logic:
* **`incApache_http.c`**: Implementation of request parsing (extracting method, file, protocol, and headers like `If-Modified-Since`), GMT date calculation and formatting, cookie setting, and efficient file transfer to the client via `sendfile()`.
* **`incApache_main.c`**: Configuration of the listening socket (`getaddrinfo`, `socket`, `bind`), de-escalation of root privileges before starting the webserver, and creation of the initial thread pool.
* **`incApache_threads.c`**: Secure management of race conditions (through the use of mutexes) for mime-type querying, and implementation of the queuing and synchronization logic for response threads (`pthread_join`).
* **`incApache_aux.c`**: Modification of the `my_timegm` function for safe timestamp conversion via temporary manipulation of the `TZ` environment variable.

## Compilation and Execution

To compile the project, run the `make` command inside the repository root[cite: 14]. The Makefile will generate the binary in the `bin/` folder and, via `sudo`, will set the `setuid` flag and change ownership to `root` to allow the use of `chroot()`.

```bash
make all
```
To launch the server, you must specify the root directory for the web files and, optionally, the TCP port (default 8000)

```bash
bin/incapache www-root 8000
```

## License
This project is distributed under the GNU General Public License (GPL) v2.

All original copyright notices belonging to Giovanni Chiola and Giovanni Lagorio are kept intact. It is permitted to copy, distribute, and modify the source code while respecting the same conditions provided by the license[cite: 14]. For full terms, refer to the COPYING file included in the repository
