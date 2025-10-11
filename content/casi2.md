**Caso d’uso:** Esecuzione Ciclo di Lavaggio  
**Attore principale:** Sistema  
**Attore secondario:** Lavatrice (esegue fisicamente le operazioni)  
**Descrizione:**  
Il sistema controlla e coordina l’esecuzione del ciclo di lavaggio, comandando la lavatrice che gestisce tutte le operazioni (riempimento acqua, lavaggio, risciacquo, centrifuga, scarico) secondo i parametri ricevuti.

**Precondizioni:**  
- La lavatrice ha ricevuto i parametri del ciclo dal sistema.

**Flusso principale degli eventi:**  
1. Il sistema invia i parametri del ciclo di lavaggio alla lavatrice.
2. La lavatrice avvia il riempimento dell’acqua.
3. La lavatrice esegue la fase di lavaggio.
4. La lavatrice esegue il risciacquo.
5. La lavatrice esegue la centrifuga.
6. La lavatrice scarica l’acqua residua.
7. Il sistema richiama il caso d’uso "Invio Notifica" per notificare il completamento del ciclo.

**Inclusioni:**  
- [Include: Invio Notifica] Al termine del ciclo, il sistema invia una notifica di completamento.
- [Include: Errore tecnico] In caso di malfunzionamento o anomalia, può essere attivato il caso d’uso "Errore tecnico".

**Postcondizioni:**  
- Il ciclo di lavaggio è completato.
- Il sistema riceve la notifica di fine ciclo.

---

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
- [Estende: Esecuzione Ciclo di Lavaggio ] In qualsiasi momento durante il ciclo, si può mettere in pausa.

**Postcondizioni:**  
- Il ciclo di lavaggio è in pausa, pronto per essere ripreso o annullato.

---

**Caso d’uso:** Ripresa ciclo  
**Attore principale:** Utente o Sistema  
**Descrizione:**  
L’utente o il sistema può richiedere la ripresa del ciclo di lavaggio precedentemente messo in pausa.
**Condizioni di entrata:**  
- Il caso d’uso viene attivato come estensione durante "Pausa ciclo".

**Flusso principale degli eventi:**  
1. L’utente o il sistema invia il comando di ripresa.
2. La lavatrice riprende le operazioni di lavaggio dal punto in cui erano state sospese.

**Estensioni:**
- [Estende: Pausa ciclo] L’utente o il sistema può inviare il comando di ripresa.

**Postcondizioni:**  
- Il ciclo di lavaggio riprende normalmente.

---

**Caso d’uso:** Annullamento ciclo  
**Attore principale:** Utente o Sistema  
**Descrizione:**  
L’utente o il sistema può richiedere l’annullamento del ciclo di lavaggio durante la pausa.
**Condizioni di entrata:**  
- Il caso d’uso viene attivato come estensione durante "Pausa ciclo".

**Estensioni:**
- [Estende: Pausa ciclo ] L’utente o il sistema può inviare il comando di annullamento.

**Inclusioni:**  
- [Include: Invio Notifica] Il sistema invia una notifica di annulamento.

**Flusso principale degli eventi:**  
1. L’utente o il sistema invia il comando di annullamento.
2. La lavatrice interrompe definitivamente il ciclo di lavaggio e scarica l’acqua residua, se necessario.
3. La lavatrice richiama il caso d’uso "Invio Notifica" per notificare l’annullamento del ciclo.

**Postcondizioni:**  
- Il ciclo di lavaggio è terminato senza essere completato.
- Il sistema riceve la notifica di annullamento.

---

**Caso d’uso:** Errore tecnico  
**Attore principale:** Lavatrice  
**Attore secondario:** Sistema (riceve la segnalazione di errore e gestisce la comunicazione)  
**Descrizione:**  
Durante l’esecuzione del ciclo di lavaggio, la lavatrice rileva un malfunzionamento o un’anomalia tecnica (es. guasto, blocco, errore sensore).

**Condizioni di entrata:**  
- Il caso d’uso viene attivato come estensione durante "Esecuzione Ciclo di Lavaggio".

**Inclusioni:**  
- [Include: Pausa ciclo] La lavatrice mette in pausa il ciclo per sicurezza prima di gestire l’errore.
- [Include: Invio Notifica] Il sistema invia una notifica di errore.

**Flusso principale degli eventi:**  
1. La lavatrice rileva un errore tecnico.
2. La lavatrice mette in pausa il ciclo (richiama il caso d’uso "Pausa ciclo").
3. La lavatrice richiama il caso d’uso "Invio Notifica" per notificare l’errore e fornisce eventuali informazioni sul tipo di errore.

**Postcondizioni:**  
- Il ciclo di lavaggio è interrotto a causa di un errore.
- Il sistema e/o l’utente sono informati dell’errore.

---

**Caso d’uso:** Invio Notifica  
**Attore principale:** Lavatrice o Sistema  
**Descrizione:**  
La lavatrice o il sistema invia una notifica per comunicare eventi rilevanti (completamento ciclo, errore tecnico, annullamento, ecc.). La notifica è inoltrata all’utente tramite i canali previsti.
**Condizioni di entrata:**  
- Il caso d’uso viene incluso da altri casi d’uso che necessitano di inviare una notifica.

**Flusso principale degli eventi:**  
1. La lavatrice o il sistema genera il messaggio di notifica in base all’evento.
2. Il sistema invia la notifica all’utente tramite l’interfaccia prevista (app, display, ecc.).
3. L’utente riceve la comunicazione relativa all’evento.

**Postcondizioni:**  
- Il sistema e/o l’utente sono informati dell’evento.

---

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

---

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

---

**Caso d’uso:** Diagnostica Sistema  
**Attore principale:** Utente o Sistema  
**Attore secondario:** Lavatrice (esegue i test diagnostici)  
**Descrizione:**  
L’utente può avviare manualmente una procedura di diagnostica, oppure il sistema può eseguirla automaticamente (ad esempio a intervalli programmati o in seguito a un evento anomalo). La diagnostica verifica lo stato dei componenti della lavatrice, rileva eventuali anomalie o malfunzionamenti e fornisce un report all’utente.

**Precondizioni:**  
- La lavatrice è accesa e collegata al sistema.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione di diagnostica tramite l’interfaccia, oppure il sistema avvia la diagnostica automaticamente.
2. Il sistema invia i comandi di test ai vari componenti della lavatrice (motore, sensori, valvole, ecc.).
3. La lavatrice esegue i test richiesti e restituisce i risultati al sistema.
4. Il sistema analizza i risultati e determina se sono presenti anomalie o malfunzionamenti.
5. Il sistema richiama il caso d’uso "Invio Notifica" per comunicare l’esito della diagnostica all’utente.

**Inclusioni:**  
- [Include: Invio Notifica] Il sistema invia una notifica con il report della diagnostica.
- [Include: Errore tecnico] Se viene rilevato un malfunzionamento durante la diagnostica, viene attivato il caso d’uso "Errore tecnico".

**Postcondizioni:**  
- L’utente riceve un report sull’esito della diagnostica.
- Eventuali anomalie vengono segnalate e gestite dal sistema.

---

**Caso d’uso:** Rilevamento Carico  
**Attore principale:** Lavatrice  
**Attore secondario:** Sistema  
**Descrizione:**  
Prima di avviare un ciclo di lavaggio, la lavatrice rileva automaticamente la presenza e la quantità di carico nel cestello. Il sistema utilizza queste informazioni per ottimizzare il ciclo o, in caso di assenza o anomalia del carico, per notificare l’utente e bloccare l’avvio del lavaggio.

**Precondizioni:**  
- La lavatrice è accesa e pronta per un nuovo ciclo.

**Flusso principale degli eventi:**  
1. La lavatrice esegue il rilevamento automatico del carico nel cestello.
2. La lavatrice comunica i dati rilevati al sistema.
3. Il sistema valuta la presenza e la quantità di carico.
4. Se il carico è assente o anomalo, il sistema attiva il caso d’uso "Errore tecnico" per informare l’utente.
5. Se il carico è corretto, il sistema procede con l’avvio del ciclo di lavaggio.

**Inclusioni:** 
- [Include: Errore tecnico] Se il carico è assente o anomalo.

**Postcondizioni:**  
- Il sistema ha ricevuto i dati sul carico e agisce di conseguenza (avvio ciclo o segnalazione errore).
- L’utente è informato in caso di problemi con il carico.

---

**Caso d’uso:** Reset Lavatrice  
**Attore principale:** Utente o Sistema  
**Attore secondario:** Lavatrice  
**Descrizione:**  
L’utente, tramite l’interfaccia, o il sistema in modo automatico, può avviare una procedura di reset per riportare la lavatrice nello stato di fabbrica. Il reset interrompe tutte le operazioni in corso, scarica eventuale acqua residua, azzera errori e blocchi, e prepara la lavatrice per un nuovo ciclo.

**Precondizioni:**  
- La lavatrice è accesa e collegata al sistema.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione di reset tramite l’interfaccia, oppure il sistema avvia il reset automaticamente (ad esempio dopo un errore tecnico persistente).
2. Il sistema invia il comando di reset alla lavatrice.
3. La lavatrice interrompe tutte le operazioni in corso.
4. La lavatrice scarica eventuale acqua residua e azzera gli errori.
5. Il sistema richiama il caso d’uso "Invio Notifica" per comunicare l’esito del reset all’utente.

**Estensioni:**  
- [Estende: Errore tecnico] Se il reset viene richiesto a seguito di un errore tecnico.

**Inclusioni:**  
- [Include: Invio Notifica] Il sistema invia una notifica all’utente sull’esito del reset.

**Postcondizioni:**  
- La lavatrice è pronta per un nuovo ciclo.
- L’utente è informato dell’avvenuto reset e di eventuali problemi risolti.

---

**Caso d’uso:** Integrazione IoT ottimizzazione Consumi  
**Attore principale:** Sistema  
**Attore secondario:** Dispositivi IoT esterni (es. termostato)  
**Descrizione:**  
Il sistema si integra con altri dispositivi IoT presenti nell’abitazione (come termostati, smart meter, ecc.) per ottimizzare i consumi energetici durante il ciclo di lavaggio. In base alle informazioni ricevute (temperatura ambiente, dispendio energetico), regolare l’esecuzione del ciclo per massimizzare l’efficienza e ridurre il dipendio energetico.

**Precondizioni:**  
- La lavatrice è connessa alla rete domestica e ai dispositivi IoT compatibili.

**Flusso principale degli eventi:**  
1. Il sistema rileva la presenza di dispositivi IoT compatibili nella rete domestica.
2. Il sistema riceve dati dai dispositivi IoT (es. temperatura, dispendio energetico).
3. Il sistema elabora le informazioni e valuta la strategia ottimale per l’esecuzione del ciclo di lavaggio.
5. Il sistema richiama il caso d’uso "Invio Notifica" per informare l’utente delle scelte effettuate o di eventuali ottimizzazioni.

**Inclusioni:**  
- [Include: Invio Notifica] Il sistema invia una notifica all’utente sulle ottimizzazioni.

**Postcondizioni:**  
- Il ciclo di lavaggio è ottimizzato in base ai dati dei dispositivi IoT.
- L’utente è informato delle ottimizzazioni effettuate.

---

**Caso d’uso:** Annullamento Ottimizzazione IoT  
**Attore principale:** Utente  
**Attore secondario:** Sistema  
**Descrizione:**  
L’utente può annullare in qualsiasi momento l’ottimizzazione dei consumi energetici basata sull’integrazione con dispositivi IoT

**Precondizioni:**  
- È attiva una strategia di ottimizzazione tramite integrazione IoT.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione per annullare l’ottimizzazione IoT tramite l’interfaccia.
2. Il sistema interrompe la strategia di ottimizzazione e ripristina le modalità standard.
3. Il sistema richiama il caso d’uso "Invio Notifica" per informare l’utente dell’avvenuto annullamento.

**Estensioni:**  
- [Estende: Integrazione IoT ottimizzazione Consumi] L’utente può annullare l’ottimizzazione in qualsiasi momento.

**Inclusioni:**  
- [Include: Invio Notifica] Il sistema invia una notifica all’utente sull’annullamento dell’ottimizzazione.

**Postcondizioni:**  
- L’ottimizzazione IoT è disattivata e il ciclo di lavaggio segue le modalità standard.
- L’utente è informato dell’annullamento.