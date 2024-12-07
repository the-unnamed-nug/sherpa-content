---
title: Perché usare Nix/NixOS?
---

Una domanda ragionevole! Perché usare tutto questo? Perché abbandonare tutto ciò che sai su Linux e adottare questo sistema operativo di nicchia? Per capire, dobbiamo prima esaminare i vantaggi di Nix, il potente [sistema di gestione dei pacchetti](https://it.wikipedia.org/wiki/Sistema_di_gestione_dei_pacchetti) su cui è basato NixOS.

Vantaggi di Nix:

- **Il Nix Store**: Nix archivia tutti i pacchetti in una cartella speciale chiamata "nix store", che si trova nella directory /nix. Nel nix store, un pacchetto viene costruito insieme alle sue dipendenze e archiviato come un hash. Lo store è a sola lettura, il che significa che non è possibile modificarlo senza comandi specifici, _nemmeno come root_!
- **Aggiornamenti atomici**: quando i pacchetti installati con Nix devono essere aggiornati, Nix non consente che l'aggiornamento avvenga a meno che tutti i pacchetti non vengano aggiornati con successo. Questo elimina la possibilità di ritrovarsi con pacchetti danneggiati. Se un pacchetto non riesce ad essere compilato, basta aspettare il prossimo aggiornamento! Questo di solito richiede solo pochi giorni, durante i quali il tuo sistema sarà completamente funzionante.
- **Compilazioni riproducibili**: quando Nix compila un pacchetto, otterrà sempre ed esattamente lo stesso risultato su qualsiasi sistema su cui viene compilato.
- **Un unico gestore di pacchetti per domarli tutti**: stanco di usare NPM, pip, AUR e tutti gli altri gestori di pacchetti? Anche noi! Ecco perché siamo molto felici di dirvi che Nix ha oltre 100.000 pacchetti pronti per l’installazione ed è il gestore di pacchetti più aggiornato in circolazione! Ci piace pensarlo come la Regola 34 dei gestori di pacchetti: se esiste, è in nixpkgs. E se non c'è, sarà aggiunto.
- **Ottimo per lo sviluppo**: lo sviluppo è uno dei motivi principali dell'esistenza di Nix! Con strumenti come "direnv", creare ambienti di sviluppo è facile e beneficia di tutto ciò che Nix offre.
- **Provare pacchetti senza installarli con `nix shell` o `nix run`**: hai mai voluto provare un programma di, per esempio, KDE? Su altri sistemi, questo richiede di installare completamente tutte le dipendenze di KDE. Poi, se non ti piace il programma e lo disinstalli, quelle dipendenze rimangono sul sistema. Con i comandi sopra, invece, puoi provare rapidamente qualsiasi programma ti piaccia. E se no sei soddisfatto, nessun problema! Non resteranno sul tuo sistema quando li rimuovi.
- **Utilizza cache binarie**: Nix compila automaticamente i pacchetti nel suo repository in remoto, quindi compilare manualmente qualsiasi cosa presente in nixpkgs è inutile! E se hai bisogno di una cache binaria per il tuo programma, puoi configurarla facilmente.
- **Portatile**: Nix funziona ovunque: Linux, MacOS, WSL, e c'è persino un progetto chiamato nix-on-droid che ti consente di usarlo su Android.
- **Mischiare diverse versioni di pacchetti senza conflitti**: poiché i pacchetti vengono compilati in modo indipendente, dipendenze incluse, puoi avere più versioni di un pacchetto - tutti installati contemporaneamente! Non c'è nulla da configurare, funziona così di default.

Quindi, ora che sai perché dovresti usare Nix, perché usare NixOS?

Vantaggi di NixOS:

- **Configurazione dichiarativa**: la caratteristica centrale di NixOS, oltre al gestore di pacchetti, è il sistema di moduli. I moduli sono essenzialmente opzioni predefinite. Nella tua configurazione di NixOS, puoi fare riferimento a uno dei migliaia di moduli che NixOS offre per le applicazioni. Questo offre due vantaggi:
  - **_Configurazione facile_**: dimentica i giorni in cui configuravi manualmente i servizi o usavi Docker. Con NixOS, se esiste un modulo, puoi configurare tutto questo con poche righe di codice.
  - **_Manutenzione semplificata_**: dato che la configurazione è composta da poche righe di codice e si trova sempre nella stessa directory, non è necessario eseguire una moltitudine di comandi complessi per risolvere problemi. Basta cambiare alcuni valori nella configurazione di NixOS.
- **Sistema riproducibile**: questa è una delle caratteristiche principali di NixOS: portare la riproducibilità di Nix al livello del sistema operativo. Puoi fare il backup della tua configurazione in un repository Git e utilizzarla su quanti computer desideri, ottenendo sempre lo stesso sistema operativo, esclusa la tua /home directory. Questo semplifica enormemente i backup.
- **Rollback integrati**: un'altra caratteristica distintiva di NixOS (e di qualsiasi distribuzione immutabile). C'è un problema con il tuo sistema attuale? Nessun problema! Puoi tornare facilmente a una configurazione precedente.
- **Opzioni di configurazione per molti programmi e servizi**: ci sono migliaia di moduli e opzioni in NixOS, tutti scritti in un unico linguaggio.
- **Privo di effetti collaterali - Rimuove effettivamente i pacchetti e le loro dipendenze**: poiché le dipendenze vengono installate e archiviate con i pacchetti che le richiedono, nessuna dipendenza resterà inutilizzata quando rimuovi un pacchetto. E qualsiasi directory del nix store non necessaria può essere ripulita con il comando `nix-collect-garbage`.
- **Facile configurazione di macchine virtuali**: puoi configurare QEMU/KVM facilmente utilizzando un modulo. Non più problemi con systemd o interfacce grafiche ingombranti. Nix può fare tutto!
- **Testare altre configurazioni**: ricordi `nix shell` e `nix run`? Entrambi questi comandi possono anche costruire configurazioni da un URL. Basta indicare il repository Git dove si trova la configurazione e Nix farà il resto.

Se uno di questi vantaggi ti sembra interessante, passa alla pagina successiva per scoprire come installare NixOS sul tuo computer.
