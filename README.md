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

# ProgettoSETI_Unige

# Ping Pong Project - TCP and UDP Performance Analysis

This project consists of a suite of client-server tools developed in C to evaluate and measure network performance, calculating Round Trip Time (RTT), latency, and bandwidth over TCP and UDP protocols.

## Authors and Acknowledgments

**Important note:** The project skeleton, the entire software architecture, and the vast majority of the base source code were designed and written by professors **Giovanni Chiola** and **Giovanni Lagorio**. 

My work within this repository was strictly limited to "filling in" the missing code blocks, completing the program parts necessary for the application to function.

## Modifications to Original Files

As required by the license terms for derivative works, it is declared that the original source files have been altered. Specifically, I removed the educational markers (`/*** TO BE DONE ***/`) and implemented the operational logic in the following files:
* `tcp_ping.c` and `udp_ping.c`: implementation of the client logic.
* `pong_server.c`: server-side request handling.
* `readwrite.c`: functions for secure datagram sending and receiving.
* `statistics.c`: algorithms for statistical RTT calculation.

## Repository Structure

To keep the project clean, the repository exclusively tracks files useful for development and analysis:
* **Source code:** `.c` and `.h` files.
* **Automation:** Bash scripts and the provided `Makefile` for compilation.
* **Visual results:** The graphs generated from the measurements.
* **Licenses and documentation:** This `README`, the `COPYING` file, and the final report.

Through a specific `.gitignore` file, the following have been intentionally excluded:
* All precompiled binaries, executable files (`pong_server`, `tcp_ping`, `udp_ping`), and libraries (`libpingpong.a`) originally contained in `bin/`.
* All raw data extracted from tests, including the heavy text output files (`.out`, `.out.broken`, `.dat`) accumulated in `data/`.

## Test Results

The complete network measurement results, including a detailed analysis of bottlenecks caused by bandwidth and latency, are documented in the **`RELAZIONE.md`** file located in the main directory. 

## License

This project is released under the **GNU General Public License (GPL) v2**, in accordance with the original license.

All original copyright notices belonging to Giovanni Chiola and Giovanni Lagorio are kept intact at the beginning of each source file. The modifications made in this repository are redistributed under the same GPLv2 license free of charge for all third parties. For more details, please consult the `COPYING` document included in the repository.
