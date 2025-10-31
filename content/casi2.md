**Caso d’uso:** Esecuzione Ciclo di Lavaggio  
**Attore principale:** Sistema  
**Attore secondario:** Lavatrice (esegue fisicamente le operazioni)  
**Descrizione:**  
Il sistema controlla e coordina l’esecuzione del ciclo di lavaggio, comandando la lavatrice che gestisce tutte le operazioni (riempimento acqua, lavaggio, risciacquo, centrifuga, scarico) secondo i parametri ricevuti.

**Precondizioni:**  
- La lavatrice ha ricevuto i parametri del ciclo dal sistema.

**Flusso principale degli eventi:**  
1. Il sistema invia i parametri del ciclo di lavaggio alla lavatrice.
2. richiama il caso d’uso "Rilevamento Carico"
3. La lavatrice avvia il riempimento dell’acqua.
4. La lavatrice esegue la fase di lavaggio.
5. La lavatrice esegue il risciacquo.
6. La lavatrice esegue la centrifuga.
7. La lavatrice scarica l’acqua residua.
8. Se durante il ciclo si verifica un errore tecnico (es. guasto, blocco, errore sensore), il sistema mette in pausa il ciclo, notificando l'utente delll'errore e suggerisce eventuali azioni correttive (es. reset lavatrice).
9. Al termine del ciclo, il sistema notifica tramite l’interfaccia prevista (app, display, ecc.) del completamento del ciclo.

**Inclusioni:**  
- [Include: Rilevamento Carico] Prima di avviare il ciclo, il sistema esegue il rilevamento automatico del carico.

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

**Flusso principale degli eventi:**  
1. L’utente o il sistema invia il comando di annullamento.
2. La lavatrice interrompe definitivamente il ciclo di lavaggio e scarica l’acqua residua, se necessario.
3. La lavatrice notifica l'utente dell’annullamento del ciclo tramite l’interfaccia prevista (app, display, ecc.).

**Postcondizioni:**  
- Il ciclo di lavaggio è terminato senza essere completato.
- Il sistema riceve la notifica di annullamento.

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

**Inclusioni:**  
- [Include: Esecuzione Ciclo di Lavaggio] L’esecuzione del ciclo viene avviata automaticamente o immediatamente secondo la pianificazione.

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
5. Il sistema richiama notifica tramite l’interfaccia prevista (app, display, ecc.) l’esito della diagnostica all’utente.

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
- Si è in procinto di effettuare un ciclo di lavaggio (il ciclo è stato programmato o avviato, ma non ancora iniziato fisicamente).

**Flusso principale degli eventi:**  
1. La lavatrice esegue il rilevamento automatico del carico nel cestello.
2. La lavatrice comunica i dati rilevati al sistema.
3. Il sistema valuta la presenza e la quantità di carico.
4. Se il carico è assente o anomalo, il sistema attiva un errore tecnico per informare l’utente.
5. Se il carico è corretto, il sistema procede con l’avvio del ciclo di lavaggio.

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
- L’utente è autenticato nel sistema.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione di reset tramite l’interfaccia, oppure il sistema avvia il reset automaticamente (ad esempio dopo un errore tecnico persistente).
2. Il sistema invia il comando di reset alla lavatrice.
3. La lavatrice interrompe tutte le operazioni in corso.
4. La lavatrice scarica eventuale acqua residua e azzera gli errori.
5. Il sistema notifica l'utente per comunicare l’esito del reset.

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
5. Il sistema notifica l'utente per informarlo delle scelte effettuate o di eventuali ottimizzazioni.

**Postcondizioni:**  
- Il ciclo di lavaggio è ottimizzato in base ai dati dei dispositivi IoT.
- L’utente è informato delle ottimizzazioni effettuate.

---

**Caso d’uso:** Annulla Ottimizzazione IoT  
**Attore principale:** Utente  
**Attore secondario:** Sistema  
**Descrizione:**  
L’utente può annullare in qualsiasi momento l’ottimizzazione dei consumi energetici basata sull’integrazione con dispositivi IoT

**Precondizioni:**  
- È attiva una strategia di ottimizzazione tramite integrazione IoT.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione per annullare l’ottimizzazione IoT tramite l’interfaccia.
2. Il sistema interrompe la strategia di ottimizzazione e ripristina le modalità standard.
3. Il sistema notifica l'utente per informarlo dell’avvenuto annullamento.

**Estensioni:**  
- [Estende: Integrazione IoT ottimizzazione Consumi] L’utente può annullare l’ottimizzazione in qualsiasi momento.

**Postcondizioni:**  
- L’ottimizzazione IoT è disattivata e il ciclo di lavaggio segue le modalità standard.
- L’utente è informato dell’annullamento.

---

**Caso d’uso:** Attiva Ottimizzazione IoT  
**Attore principale:** Utente  
**Attore secondario:** Sistema, Dispositivi IoT esterni  
**Descrizione:**  
L’utente può riattivare in qualsiasi momento la strategia di ottimizzazione dei consumi energetici basata sull’integrazione con dispositivi IoT, dopo che era stata precedentemente disattivata. Il sistema riprende a utilizzare i dati dei dispositivi IoT per ottimizzare i cicli di lavaggio.

**Precondizioni:**  
- L’ottimizzazione IoT è disattivata.  
- Esiste almeno un dispositivo IoT associato e disponibile.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione per riattivare l’ottimizzazione IoT tramite l’interfaccia.  
2. Il sistema verifica la presenza di dispositivi IoT associati e disponibili.  
3. Il sistema riattiva la strategia di ottimizzazione dei consumi tramite integrazione IoT.  
4. Il sistema notifica l’utente dell’avvenuta riattivazione o segnala eventuali errori.

**Estensioni:**  
- [Estende: Integrazione IoT ottimizzazione Consumi] L’utente può riattivare l’ottimizzazione in qualsiasi momento, se sono disponibili dispositivi IoT associati.

**Postcondizioni:**  
- L’ottimizzazione IoT è attiva e i cicli di lavaggio successivi seguiranno la strategia ottimizzata.  
- L’utente è informato dell’avvenuta riattivazione o di eventuali problemi.

---

**Caso d’uso:** Associa Dispositivo IoT  
**Attore principale:** Utente  
**Attore secondario:** Sistema, Dispositivi IoT esterni  
**Descrizione:**  
L’utente può associare un nuovo dispositivo IoT compatibile (es. termostato, Contatore intelligente) al sistema della lavatrice per abilitare funzionalità avanzate di integrazione e ottimizzazione dei consumi. L’associazione può avvenire tramite una procedura guidata nell’interfaccia utente, che rileva i dispositivi disponibili e ne consente la configurazione.

**Precondizioni:**  
- Il dispositivo IoT è acceso e in modalità di associazione.  
- La lavatrice è connessa alla rete domestica.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione per aggiungere un nuovo dispositivo IoT tramite l’interfaccia.  
2. Il sistema esegue la scansione della rete per rilevare dispositivi IoT compatibili.  
3. L’utente seleziona il dispositivo da associare dall’elenco proposto.  
4. Il sistema avvia la procedura di associazione e configura il dispositivo per l’integrazione con la lavatrice.  
5. Il sistema notifica l'utente per confermare l’avvenuta associazione o segnalare eventuali errori.

**Postcondizioni:**  
- Il nuovo dispositivo IoT è associato e pronto per l’integrazione con la lavatrice.  
- L’utente è informato dell’esito dell’operazione.

---

**Caso d’uso:** Dissocia Dispositivo IoT  
**Attore principale:** Utente  
**Attore secondario:** Sistema, Dispositivi IoT esterni  
**Descrizione:**  
L’utente può dissociare un dispositivo IoT precedentemente associato alla lavatrice, interrompendo la comunicazione e l’integrazione tra i due sistemi. La dissociazione può essere richiesta tramite l’interfaccia utente, che mostra l’elenco dei dispositivi attualmente associati e consente la selezione di quello da rimuovere.

**Precondizioni:**  
- Esistono uno o più dispositivi IoT già associati alla lavatrice.  
- La lavatrice è connessa alla rete domestica.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione per dissociare un dispositivo IoT tramite l’interfaccia.  
2. Il sistema mostra l’elenco dei dispositivi IoT associati.  
3. L’utente seleziona il dispositivo da dissociare.  
4. Il sistema avvia la procedura di dissociazione e rimuove la configurazione relativa al dispositivo.  
5. Il sistema comunica al dispositivo IoT la richiesta di disconnessione, se supportato.  
6. Il sistema notifica l'utente per confermare l’avvenuta dissociazione o segnalare eventuali errori.

**Postcondizioni:**  
- Il dispositivo IoT selezionato è stato dissociato e non comunica più con la lavatrice.  
- L’utente è informato dell’esito dell’operazione.

---

**Caso d’uso:** Login  
**Attore principale:** Utente  
**Descrizione:**  
L’utente inserisce le proprie credenziali (username/email e password) per autenticarsi nel sistema della lavatrice intelligente, accedendo così alle funzionalità personalizzate e protette.

**Precondizioni:**  
- L’utente è già registrato nel sistema.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione di login sull’interfaccia (display o app).
2. Il sistema richiede l’inserimento di username/email e password.
3. L’utente inserisce le credenziali richieste.
4. Il sistema verifica la correttezza delle credenziali.
5. Se le credenziali sono corrette, l’utente accede alle funzionalità protette.
6. Se le credenziali sono errate, il sistema mostra un messaggio di errore e consente un nuovo tentativo.

**Postcondizioni:**  
- L’utente è autenticato e può accedere alle funzionalità riservate.

---

**Caso d’uso:** Recupero password  
**Attore principale:** Utente  
**Descrizione:**  
L’utente che ha dimenticato la password può avviare la procedura di recupero, ricevendo codice per reimpostare la password e poter accedere nuovamente al sistema.

**Precondizioni:**  
- L’utente è registrato nel sistema.
- L’utente non ricorda la password.

**Flusso principale degli eventi:**  
1. L’utente seleziona “Recupero password” dalla schermata di login.
2. Il sistema richiede l’inserimento dell’email o username.
3. L’utente inserisce l’email/username.
4. Il sistema verifica la presenza dell’utente.
5. Se l’utente esiste, il sistema invia un’email/SMS con il codice di reset.
6. L’utente inserisce il codice ricevuto.
7. Il sistema richiede l’inserimento di una nuova password.
8. L’utente inserisce e conferma la nuova password.
9. Il sistema aggiorna la password e conferma l’avvenuto reset.
10. L’utente può ora effettuare il login con la nuova password.

**Estensioni:**  
- [Estende: Login] Il caso d’uso viene attivato dalla schermata di login.

**Postcondizioni:**  
- La password dell’utente è stata reimpostata.
- L’utente può accedere nuovamente al sistema.

---

**Caso d’uso:** Logout
**Attore principale:** Utente
**Descrizione:**  
L’utente autenticato può terminare volontariamente la propria sessione, disconnettendosi dal sistema della lavatrice intelligente. Il logout invalida la sessione attiva, impedendo ulteriori operazioni protette fino a nuovo login.

**Precondizioni:**  
- L’utente è autenticato e dispone di una sessione attiva.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione di logout tramite l’interfaccia (display o app).
2. Il sistema riceve la richiesta di logout.
3. Il sistema invalida la sessione attiva dell’utente (rimuove o marca come inattiva la sessione).
4. Il sistema conferma l’avvenuto logout all’utente.

**Postcondizioni:**  
- La sessione dell’utente è stata invalidata.
- L’utente non può più accedere alle funzionalità protette fino a nuovo login.

---

**Caso d’uso:** Attiva Comandi Vocali  
**Attore principale:** Utente  
**Attore secondario:** Sistema  
**Descrizione:**  
L’utente può attivare la funzionalità di controllo vocale della lavatrice tramite l’interfaccia (app o display). Una volta attivati, i comandi vocali permettono di impartire istruzioni alla lavatrice usando la voce.

**Precondizioni:**  
- L’utente è autenticato nel sistema.
- La lavatrice supporta la funzionalità di comandi vocali.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione per attivare i comandi vocali tramite l’interfaccia.
2. Il sistema abilita il modulo di riconoscimento vocale.
3. Il sistema notifica l’utente dell’avvenuta attivazione.

**Postcondizioni:**  
- I comandi vocali sono attivi e pronti per essere utilizzati.

---

**Caso d’uso:** Disattiva Comandi Vocali  
**Attore principale:** Utente  
**Attore secondario:** Sistema  
**Descrizione:**  
L’utente può disattivare la funzionalità di controllo vocale della lavatrice tramite l’interfaccia (app o display). Una volta disattivati, i comandi vocali non sono più accettati dal sistema.

**Precondizioni:**  
- I comandi vocali sono attivi.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione per disattivare i comandi vocali tramite l’interfaccia.
2. Il sistema disabilita il modulo di riconoscimento vocale.
3. Il sistema notifica l’utente dell’avvenuta disattivazione.

**Postcondizioni:**  
- I comandi vocali sono disattivati e non possono essere utilizzati fino a nuova attivazione.

---

**attori**:
Utente
  ^
  |
Remote Control (App Mobile)

Sistema

Lavatrice

Dispositivo IoT Esterno