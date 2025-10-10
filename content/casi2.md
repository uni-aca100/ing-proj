**Caso d’uso:** Esecuzione Ciclo di Lavaggio  
**Attore principale:** Lavatrice
**Descrizione:**  
La lavatrice esegue fisicamente il ciclo di lavaggio, gestendo tutte le operazioni (riempimento acqua, lavaggio, risciacquo, centrifuga, scarico) secondo i parametri ricevuti dal sistema.
**Precondizioni:**  
- La lavatrice ha ricevuto i parametri del ciclo dal sistema.
**Flusso principale degli eventi:**  
1. Il sistema invia i parametri del ciclo di lavaggio alla lavatrice.
2. La lavatrice avvia il riempimento dell’acqua.
3. La lavatrice esegue la fase di lavaggio.
4. La lavatrice esegue il risciacquo.
5. La lavatrice esegue la centrifuga.
6. La lavatrice scarica l’acqua residua.
7. La lavatrice richiama il caso d’uso "Segnala al Sistema" per notificare il completamento del ciclo.
**Estensioni:**  
- [Estende: Pausa ciclo] In qualsiasi momento durante il ciclo, può essere attivato il caso d’uso "Pausa ciclo".
- [Estende: Errore tecnico] In caso di malfunzionamento o anomalia, può essere attivato il caso d’uso "Errore tecnico".
**Inclusioni:**  
- [Include: Segnala al Sistema] Al termine del ciclo, il sistema riceve una notifica di completamento.
**Postcondizioni:**  
- Il ciclo di lavaggio è completato.
- Il sistema riceve la notifica di fine ciclo.

**Caso d’uso:** Pausa ciclo  
**Attore principale:** Utente o Sistema  
**Descrizione:**  
L’utente o il sistema può richiedere la sospensione temporanea del ciclo di lavaggio in corso. La lavatrice si mette in pausa e attende un comando di ripresa o annullamento.
**Condizioni di entrata:**  
- Il caso d’uso viene attivato come estensione durante "Esecuzione Ciclo di Lavaggio".
**Flusso principale degli eventi:**  
1. L’utente o il sistema invia il comando di pausa.
2. La lavatrice sospende tutte le operazioni in corso.
3. La lavatrice attende un comando di ripresa o annullamento.
**Estensioni:**  
- [Estende: Ripresa ciclo] L’utente o il sistema può inviare il comando di ripresa.
- [Estende: Annullamento ciclo] L’utente o il sistema può inviare il comando di annullamento.
**Postcondizioni:**  
- Il ciclo di lavaggio è in pausa, pronto per essere ripreso o annullato.

**Caso d’uso:** Ripresa ciclo  
**Attore principale:** Utente o Sistema  
**Descrizione:**  
L’utente o il sistema può richiedere la ripresa del ciclo di lavaggio precedentemente messo in pausa.
**Condizioni di entrata:**  
- Il caso d’uso viene attivato come estensione durante "Pausa ciclo".
**Flusso principale degli eventi:**  
1. L’utente o il sistema invia il comando di ripresa.
2. La lavatrice riprende le operazioni di lavaggio dal punto in cui erano state sospese.
**Postcondizioni:**  
- Il ciclo di lavaggio riprende normalmente.

**Caso d’uso:** Annullamento ciclo  
**Attore principale:** Utente o Sistema  
**Descrizione:**  
L’utente o il sistema può richiedere l’annullamento del ciclo di lavaggio durante la pausa.
**Condizioni di entrata:**  
- Il caso d’uso viene attivato come estensione durante "Pausa ciclo".
**Inclusioni:**  
- [Include: Segnala al Sistema] Il sistema invia una notifica di annulamento.
**Flusso principale degli eventi:**  
1. L’utente o il sistema invia il comando di annullamento.
2. La lavatrice interrompe definitivamente il ciclo di lavaggio e scarica l’acqua residua, se necessario.
3. La lavatrice richiama il caso d’uso "Segnala al Sistema" per notificare l’annullamento del ciclo.
**Postcondizioni:**  
- Il ciclo di lavaggio è terminato senza essere completato.
- Il sistema riceve la notifica di annullamento.

**Caso d’uso:** Errore tecnico  
**Attore principale:** Lavatrice  
**Attore secondario:** Sistema (riceve la segnalazione di errore e gestisce la comunicazione)  
**Descrizione:**  
Durante l’esecuzione del ciclo di lavaggio, la lavatrice rileva un malfunzionamento o un’anomalia tecnica (es. guasto, blocco, errore sensore).
**Condizioni di entrata:**  
- Il caso d’uso viene attivato come estensione durante "Esecuzione Ciclo di Lavaggio".
**Inclusioni:**  
- [Include: Pausa ciclo] La lavatrice mette in pausa il ciclo per sicurezza prima di gestire l’errore.
- [Include: Segnala al Sistema] Il sistema invia una notifica di errore.
**Flusso principale degli eventi:**  
1. La lavatrice rileva un errore tecnico.
2. La lavatrice mette in pausa il ciclo (richiama il caso d’uso "Pausa ciclo").
3. La lavatrice richiama il caso d’uso "Segnala al Sistema" per notificare l’errore e fornisce eventuali informazioni sul tipo di errore.
**Postcondizioni:**  
- Il ciclo di lavaggio è interrotto a causa di un errore.
- Il sistema e/o l’utente sono informati dell’errore.

**Caso d’uso:** Segnala al Sistema  
**Attore principale:** Lavatrice o Sistema  
**Attore secondario:** Utente (riceve la comunicazione tramite notifica)  
**Descrizione:**  
La lavatrice o il sistema invia una segnalazione al sistema centrale per comunicare eventi rilevanti (completamento ciclo, errore tecnico, annullamento, ecc.). La segnalazione può comportare anche la notifica all’utente tramite i canali previsti.
**Condizioni di entrata:**  
- Il caso d’uso viene incluso da altri casi d’uso che necessitano di segnalare un evento al sistema.
**Flusso principale degli eventi:**  
1. La lavatrice o il sistema genera il messaggio di segnalazione in base all’evento.
2. Il sistema centrale riceve la segnalazione e, se necessario, invia una notifica all’utente tramite l’interfaccia prevista (app, display, ecc.).
**Postcondizioni:**  
- Il sistema centrale e/o l’utente sono informati dell’evento.

[comment]: <> (TODO Caso d’uso: gestione segnale)

**Caso d’uso:** Pianificazione Lavaggio  
**Attore principale:** Utente  
**Descrizione:**  
L’utente programma l’esecuzione di un ciclo di lavaggio, scegliendo se avviarlo immediatamente o in un momento futuro tramite lo scheduler del sistema.
**Precondizioni:**  
- La lavatrice è pronta per un nuovo ciclo.
**Flusso principale degli eventi:**  
1. L’utente seleziona i parametri del ciclo di lavaggio e la data/ora desiderata (o l’opzione "subito").
2. Il sistema registra la pianificazione e aggiorna lo scheduler.
3. Se la data/ora è "subito", il sistema avvia immediatamente il caso d’uso "Esecuzione Ciclo di Lavaggio".
4. Se pianificato, il sistema avvia automaticamente il caso d’uso "Esecuzione Ciclo di Lavaggio" all’orario stabilito.
**Generalizzazioni:**  
- [Generalizza: Avvio Lavaggio] Se l’utente sceglie l’avvio immediato.
**Postcondizioni:**  
- Il ciclo di lavaggio è programmato e verrà eseguito automaticamente all’orario scelto oppure avviato subito.

**Caso d’uso:** Avvio Lavaggio  
**Attore principale:** Utente  
**Descrizione:**  
Corrisponde a una pianificazione con esecuzione immediata: l’utente avvia subito un ciclo di lavaggio.
**Specializzazione:**  
- [Specializza: Pianificazione Lavaggio]
**Precondizioni:**  
- La lavatrice è accesa e pronta per un nuovo ciclo.
**Flusso principale degli eventi:**  
1. L’utente seleziona i parametri del ciclo di lavaggio e sceglie l’avvio immediato.
2. Il sistema avvia subito il caso d’uso "Esecuzione Ciclo di Lavaggio".
**Postcondizioni:**  
- Il ciclo di lavaggio è avviato immediatamente.