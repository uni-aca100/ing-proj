
**Caso d’uso:** Modifica configurazione UI  
**Attore principale:** Utente  
**Descrizione:**  
L’utente accede al menu di configurazione dell’interfaccia utente e modifica una o più opzioni disponibili (es. tema, contrasto, dimensione font, lingua, modalità accessibilità). Le modifiche vengono applicate immediatamente o salvate per le sessioni future.

**Precondizioni:**  
- L’utente è autenticato.
- Il menu di configurazione UI è accessibile.

**Flusso principale degli eventi:**  
1. L’utente accede al menu di configurazione UI tramite l’interfaccia.
2. Il sistema mostra le opzioni configurabili (tema, contrasto, font ecc.).
3. L’utente seleziona e modifica una o più opzioni.
4. L’utente conferma e salva le modifiche.
5. Il sistema aggiorna la configurazione persistente.
6. Il sistema notifica l’utente dell’avvenuto aggiornamento.

**Postcondizioni:**  
- La configurazione UI è aggiornata secondo le preferenze dell’utente e salvata per le sessioni future.

**requisiti**
- Le modifiche apportate devono essere valide e coerenti con le opzioni disponibili.

---

**Caso d’uso:** Cerca dispositivi IoT  
**Attore principale:** Utente  
**Attore secondario:** Dispositivi IoT esterni
**Descrizione:**  
L’utente avvia la ricerca di dispositivi IoT compatibili presenti nella rete domestica tramite l’apposita funzione dell’interfaccia. Il sistema esegue una scansione della rete, individua i dispositivi disponibili e mostra all’utente l’elenco dei dispositivi rilevati, permettendo la selezione per l’associazione.

**Precondizioni:**  
- La lavatrice è connessa alla rete domestica.
- L’utente ha accesso al menu di gestione dispositivi IoT.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione “Cerca dispositivi IoT” nell’interfaccia.
2. Il sistema avvia la scansione della rete per individuare dispositivi IoT compatibili.
3. Il sistema raccoglie le informazioni sui dispositivi rilevati (nome, tipo, ecc.).
4. Il sistema mostra all’utente l’elenco dei dispositivi trovati.
5. L’utente può selezionare uno o più dispositivi per l’associazione o ottenere informazioni dettagliate.

**Postcondizioni:**  
- L’elenco dei dispositivi IoT disponibili è presentato all’utente.
- L’utente può procedere con l’associazione dei dispositivi rilevati.

**Requisiti:**
- Devono essere mostrati solo dispositivi presenti nella rete locale, e non già associati.

---

**Caso d’uso:** Elimina notifiche  
**Attore principale:** Utente  
**Descrizione:**  
L’utente accede al menu delle notifiche tramite l’interfaccia e seleziona una o più notifiche da eliminare. Il sistema rimuove le notifiche selezionate dalla lista e aggiorna la visualizzazione. È possibile anche eliminare tutte le notifiche in un’unica operazione.

**Precondizioni:**  
- L’utente ha accesso al menu notifiche.
- Sono presenti una o più notifiche nella lista.

**Flusso principale degli eventi:**  
1. L’utente accede al menu notifiche tramite l’interfaccia.
2. Il sistema mostra la lista delle notifiche disponibili.
3. L’utente seleziona una o più notifiche da eliminare, oppure sceglie l’opzione “Elimina tutte”.
4. Il sistema elimina le notifiche selezionate dalla lista.
5. Il sistema aggiorna la visualizzazione.

**Postcondizioni:**  
- Le notifiche selezionate sono state eliminate e non sono più visibili all’utente.

**Requisiti:**  
- Non devono essere eliminate notifiche non selezionate.

---

**Caso d’uso:** Leggi notifiche  
**Attore principale:** Utente  
**Descrizione:**  
L’utente accede al menu delle notifiche tramite l’interfaccia e visualizza la lista delle notifiche ricevute dal sistema (es. errori). L’utente può selezionare una notifica per leggerne il dettaglio.

**Precondizioni:**  
- L’utente ha accesso al menu notifiche.
- Sono presenti una o più notifiche nella lista.

**Flusso principale degli eventi:**  
1. L’utente accede al menu notifiche tramite l’interfaccia.
2. Il sistema mostra la lista delle notifiche disponibili.
3. L’utente seleziona una notifica per visualizzarne il dettaglio.
4. Il sistema mostra il contenuto dettagliato della notifica.
5. Il sistema segna la notifica come letta.

**Postcondizioni:**  
- Le notifiche lette sono marcate come tali.

**Requisiti:**  
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Esecuzione Ciclo di Lavaggio  
**Attore principale:** Sistema
**Descrizione:**  
Il sistema controlla e coordina l’esecuzione del ciclo di lavaggio, comandando la lavatrice che gestisce tutte le operazioni (riempimento acqua, lavaggio, risciacquo, centrifuga, scarico) secondo i parametri ricevuti.

**Precondizioni:**  
- La lavatrice è in stato di standby.
- La lavatrice ha ricevuto il piano di lavaggio da eseguire.

**Flusso principale degli eventi:**  
1. Scocca l'orario previsto per un piano di lavaggio pianificato.
2. Il sistema configura la lavatrice con i parametri pianificati per il ciclo di lavaggio.
3. La lavatrice avvia il riempimento dell’acqua.
4. La lavatrice esegue la fase di lavaggio.
5. La lavatrice esegue il risciacquo.
6. La lavatrice esegue la centrifuga.
7. La lavatrice scarica l’acqua residua.
8. Se durante il ciclo si verifica un errore, il sistema notifica l'utente dell'errore che procede alla cancellazione del ciclo.
9. Al termine del ciclo, il sistema notifica tramite l’interfaccia prevista del completamento del ciclo.

**Postcondizioni:**  
- Una pianificazione di lavaggio è stata completata o è stata annullata.
- L'utente viene notificato del completamento o dell'annullamento.

**Requisiti:**
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Pausa ciclo  
**Attore principale:** Utente
**Descrizione:**  
L’utente può richiedere la sospensione temporanea del ciclo di lavaggio in corso. La lavatrice si mette in pausa e attende un comando di ripresa o annullamento.

**Condizioni di entrata:**
- Il ciclo di lavaggio è attualmente in esecuzione.
- L’utente ha accesso al menu di gestione del ciclo di lavaggio.

**Flusso principale degli eventi:**  
1. L’utente preme il pulsante di pausa.
2. La lavatrice sospende tutte le operazioni in corso.

**Estensioni:**
- [Estende: Esecuzione Ciclo di Lavaggio] In qualsiasi momento durante il ciclo, si può mettere in pausa.

**Postcondizioni:**  
- Il ciclo di lavaggio è in pausa, pronto per essere ripreso o annullato.

**Requisiti:**
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Riprendi ciclo
**Attore principale:** Utente  
**Descrizione:**  
L’utente può richiedere la ripresa del ciclo di lavaggio precedentemente messo in pausa.

**Condizioni di entrata:**
- Il ciclo di lavaggio è attualmente in pausa.
- L’utente ha accesso al menu di gestione del ciclo di lavaggio.

**Flusso principale degli eventi:**  
1. L’utente preme il pulsante di ripresa.
2. La lavatrice riprende le operazioni di lavaggio dal punto in cui erano state sospese.

**Estensioni:**
- [Estende: Esecuzione Ciclo di Lavaggio] L’utente può inviare il comando di ripresa.

**Postcondizioni:**  
- Il ciclo di lavaggio riprende normalmente.

**Requisiti:**
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Annulla ciclo  
**Attore principale:** Utente  
**Descrizione:**  
L’utente può richiedere l’annullamento del ciclo di lavaggio durante la pausa.

**Condizioni di entrata:**  
- Il ciclo di lavaggio è attualmente in esecuzione o in pausa.
- L’utente ha accesso al menu di gestione del ciclo di lavaggio.

**Estensioni:**
- [Estende: Esecuzione Ciclo di Lavaggio] L’utente può inviare il comando di annullamento.

**Flusso principale degli eventi:**  
1. L’utente preme il pulsante di annullamento.
2. La lavatrice interrompe definitivamente il ciclo di lavaggio e scarica l’acqua residua, se necessario.
3. La lavatrice notifica l'utente dell’annullamento del ciclo.

**Postcondizioni:**  
- Il ciclo di lavaggio è terminato senza essere completato.
- L'utente riceve la notifica di annullamento.

**Requisiti:**  
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Pianifica Lavaggio  
**Attore principale:** Utente  
**Descrizione:**  
L’utente programma l’esecuzione di un ciclo di lavaggio, scegliendo se avviarlo immediatamente o in un momento futuro tramite lo scheduler del sistema.

**Precondizioni:**
- L’utente ha accesso al menu di pianificazione.
- La lavatrice è in stato di standby.

**Flusso principale degli eventi:**  
1. L’utente seleziona il piano di lavaggio, i parametri e la data/ora desiderata del ciclo di lavaggio.
2. Il sistema registra la pianificazione e aggiorna lo scheduler.
3. Se la data/ora è "subito", avvia immediatamente il caso d’uso "Esecuzione Ciclo di Lavaggio".
4. Se pianificato, il sistema avvia automaticamente il caso d’uso "Esecuzione Ciclo di Lavaggio" all’orario stabilito.

**Postcondizioni:**  
- Il ciclo di lavaggio è programmato e verrà eseguito automaticamente all’orario scelto oppure avviato subito.

**requisiti**
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Avvio Lavaggio  
**Attore principale:** Utente  
**Descrizione:**  
Corrisponde a una pianificazione con esecuzione immediata: l’utente avvia subito un ciclo di lavaggio.

**Specializzazione:**  
- [Specializza: Pianificazione Lavaggio]

**Precondizioni:**
- L’utente ha accesso al menu di avvio.
- La lavatrice è pronta per un nuovo ciclo.

**Flusso principale degli eventi:**  
1. L’utente seleziona il piano di lavaggio, i parametri del ciclo di lavaggio e sceglie l’avvio immediato.
2. Il sistema avvia subito il caso d’uso "Esecuzione Ciclo di Lavaggio".

**Postcondizioni:**  
- Il ciclo di lavaggio è avviato immediatamente.

**requisiti**
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Avvia Diagnostica  
**Attore principale:** Utente
**Descrizione:**  
L’utente può avviare una procedura di diagnostica. La diagnostica verifica lo stato dei componenti della lavatrice, rileva eventuali anomalie o malfunzionamenti e fornisce un report all’utente.

**Precondizioni:**
- L’utente è autenticato.
- La lavatrice è in stato di standby.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione di avvio diagnostica tramite l’interfaccia.
2. Il sistema invia i comandi di test ai vari componenti della lavatrice (motore, sensori, valvole, ecc.).
3. La lavatrice esegue i test richiesti e restituisce i risultati al sistema.
4. Il sistema analizza i risultati e determina se sono presenti anomalie o malfunzionamenti.
5. Il sistema notificherà l’esito della diagnostica all’utente.

**Postcondizioni:**  
- L’utente riceve un report aggiornato sull’stato corrente della lavatrice.
- Eventuali anomalie vengono segnalate.

**Requisiti:**
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Reset Lavatrice  
**Attore principale:** Utente
**Descrizione:**  
L’utente, tramite l’interfaccia può avviare una procedura di reset per riportare la lavatrice nello stato di fabbrica. Il reset interrompe tutte le operazioni in corso, scarica eventuale acqua residua, azzera errori e blocchi, e prepara la lavatrice per un nuovo ciclo.

**Precondizioni:**  
- L'utente ha accesso all'interfaccia per il reset della lavatrice.
- L’utente è autenticato.
- La lavatrice è in stato di standby.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione di reset tramite l’interfaccia.
3. La lavatrice interrompe tutte le operazioni in corso.
4. La lavatrice scarica eventuale acqua residua e reimposta la configurazione di fabbrica.
5. Il sistema notifica l'utente per comunicare l’esito del reset.

**Postcondizioni:**  
- La lavatrice è riportata alla configurazione di fabbrica.
- L’utente è informato dell’avvenuto reset e di eventuali problemi risolti.

**Requisiti:**
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** ottimizzazione Consumi IoT   
**Attore principale:** Sistema  
**Attore secondario:** Dispositivi IoT esterni (es. contatori intelligenti)  
**Descrizione:**  
Il sistema si integra con altri dispositivi IoT presenti nell’abitazione (come smart meter) per ottimizzare i consumi energetici durante il ciclo di lavaggio. In base alle informazioni ricevute (temperatura ambiente, dispendio energetico), regolare l’esecuzione del ciclo per massimizzare l’efficienza e ridurre il dipendio energetico.

**Precondizioni:**  
- La lavatrice è connessa alla rete domestica.
- La lavatrice è connessa a almeno un dispositivo IoT compatibile.

**Flusso principale degli eventi:**  
1. Il sistema rileva la presenza di dispositivi IoT compatibili nella rete domestica.
2. Il sistema acquisisce dati dai dispositivi IoT (es. dispendio energetico).
3. Il sistema elabora le informazioni e valuta la strategia ottimale per l’esecuzione del ciclo di lavaggio.
5. Il sistema notifica l'utente per informarlo delle scelte effettuate.

**Postcondizioni:**  
- Il ciclo di lavaggio è ottimizzato in base ai dati dei dispositivi IoT.
- L’utente è informato delle ottimizzazioni effettuate.

**Requisiti:**
- I dati raccolti devono provenire solo da dispositivi IoT compatibili e associati.

---

**Caso d’uso:** Disattiva Ottimizzazione IoT  
**Attore principale:** Utente  
**Descrizione:**  
L’utente può disattivare in qualsiasi momento l’ottimizzazione dei consumi energetici basata sull’integrazione con dispositivi IoT. Una volta disattivata, il sistema non utilizza più i dati dei dispositivi IoT per regolare i cicli di lavaggio.

**Precondizioni:**
- L’utente è autenticato nel sistema.
- È attiva una strategia di ottimizzazione tramite integrazione IoT.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione per disattivare l’ottimizzazione IoT tramite l’interfaccia.
2. Il sistema interrompe la strategia di ottimizzazione e ripristina le modalità standard.
3. Il sistema notifica l'utente per informarlo dell’avvenuta disattivazione.

**Postcondizioni:**  
- L’ottimizzazione IoT è disattivata e il ciclo di lavaggio segue le modalità standard.
- L’utente è informato dell’avvenuta disattivazione.

**Requisiti:**
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Attiva Ottimizzazione IoT  
**Attore principale:** Utente  
**Descrizione:**  
L’utente può riattivare in qualsiasi momento la strategia di ottimizzazione dei consumi energetici basata sull’integrazione con dispositivi IoT, dopo che era stata precedentemente disattivata. Il sistema riprende a utilizzare i dati dei dispositivi IoT per ottimizzare i cicli di lavaggio.

**Precondizioni:**
- L’utente è autenticato nel sistema.
- Non è attiva la funzionalità di ottimizzazione tramite integrazione IoT.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione per riattivare l’ottimizzazione IoT tramite l’interfaccia.  
2. Il sistema riattiva la strategia di ottimizzazione dei consumi tramite integrazione IoT.  
3. Il sistema notifica l’utente dell’avvenuta riattivazione o segnala eventuali errori.

**Postcondizioni:**  
- L’ottimizzazione IoT è attiva e i cicli di lavaggio successivi seguiranno la strategia ottimizzata.  
- L’utente è informato dell’avvenuta riattivazione o di eventuali problemi.

**Requisiti:**
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Associa Dispositivo IoT  
**Attore principale:** Utente  
**Attore secondario:** Dispositivi IoT esterni  
**Descrizione:**  
L’utente può associare un nuovo dispositivo IoT compatibile (es. termostato, Contatore intelligente) al sistema della lavatrice per abilitare funzionalità avanzate di integrazione e ottimizzazione dei consumi. L’associazione può avvenire tramite una procedura guidata nell’interfaccia utente, che rileva i dispositivi disponibili e ne consente la configurazione.

**Precondizioni:**
- L’utente è autenticato e ha accesso al menu di gestione dispositivi IoT.
- Sono stati rilevati dispositivi IoT compatibili disponibili nella rete domestica.
- La lavatrice è connessa alla rete domestica.

**Flusso principale degli eventi:**  
1. L’utente seleziona il dispositivo da associare dall’elenco proposto tramite l’interfaccia.  
2. Il sistema avvia la procedura di associazione e configura il dispositivo per l’integrazione con la lavatrice.  
3. Il sistema notifica l'utente per confermare l’avvenuta associazione o segnalare eventuali errori.

**Postcondizioni:**  
- Il nuovo dispositivo IoT è associato e pronto per l’integrazione con la lavatrice.  
- L’utente è informato dell’esito dell’operazione.

**Requisiti:**
- Devono essere associati solo dispositivi compatibili e non già associati.

---

**Caso d’uso:** Dissocia Dispositivo IoT  
**Attore principale:** Utente  
**Attore secondario:** Dispositivi IoT esterni  
**Descrizione:**  
L’utente può dissociare un dispositivo IoT precedentemente associato alla lavatrice, interrompendo la comunicazione e l’integrazione tra i due sistemi. La dissociazione può essere richiesta tramite l’interfaccia utente, che mostra l’elenco dei dispositivi attualmente associati e consente la selezione di quello da rimuovere.

**Precondizioni:**  
- L’utente è autenticato e ha accesso al menu di gestione dispositivi IoT.
- Esistono uno o più dispositivi IoT già associati alla lavatrice.
- La lavatrice è connessa alla rete domestica.

**Flusso principale degli eventi:**  
1. L’utente consulta l’elenco dei dispositivi IoT associati tramite l’interfaccia.  
2. L’utente seleziona il dispositivo da dissociare.  
3. Il sistema avvia la procedura di dissociazione e rimuove la configurazione relativa al dispositivo.  
4. Il sistema comunica al dispositivo IoT la richiesta di disconnessione.  
5. Il sistema notifica l'utente per confermare l’avvenuta dissociazione o segnalare eventuali errori.

**Postcondizioni:**  
- Il dispositivo IoT selezionato è stato dissociato e non comunica più con la lavatrice.  
- L’utente è informato dell’esito dell’operazione.

**Requisiti:**
- Nessun requisito aggiuntivo.

---

**Caso d’uso:** Login  
**Attore principale:** Utente  
**Descrizione:**  
L’utente inserisce le proprie credenziali (username/email e password) per autenticarsi nel sistema della lavatrice intelligente, accedendo così alle funzionalità personalizzate e protette.

**Precondizioni:**
- L’utente è già registrato nel sistema.
- L'utente ha accesso all'interfaccia di login.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione di login sull’interfaccia.
2. Il sistema richiede l’inserimento di username/email e password.
3. L’utente inserisce le credenziali richieste.
4. Il sistema verifica la correttezza delle credenziali.
5. Se le credenziali sono corrette, l’utente accede alle funzionalità protette.
6. Se le credenziali sono errate, il sistema mostra un messaggio di errore e consente un nuovo tentativo.

**Postcondizioni:**  
- L’utente è autenticato e può accedere alle funzionalità riservate.

**Requisiti:**
- Le credenziali devono essere valide e corrispondere a un utente registrato.

---

**Caso d’uso:** Recupero password  
**Attore principale:** Utente  
**Descrizione:**  
L’utente che ha dimenticato la password può avviare la procedura di recupero, ricevendo un email con una nuova password per accedere nuovamente al sistema.

**Precondizioni:**  
- L’utente è registrato nel sistema.
- L’utente non ricorda la password.
- L'utente ha accesso all'interfaccia di login.

**Flusso principale degli eventi:**  
1. L’utente seleziona “Recupero password” dalla schermata di login.
2. Il sistema richiede l’inserimento dell’email.
3. L’utente inserisce l’email.
4. Il sistema verifica la presenza dell’utente.
5. Se l’utente esiste, il sistema genera una nuova password assegnata all’utente.
6. Il sistema invia un’email con la nuova password per accedere al sistema.
7. L’utente riceve l’email e utilizza la nuova password per accedere.

**Estensioni:**  
- [Estende: Login] Il caso d’uso viene attivato dalla schermata di login.

**Postcondizioni:**  
- La password dell’utente è stata reimpostata.
- L’utente può accedere nuovamente al sistema.

**Requisiti:**
- L’email deve essere valida e corrispondere a un utente registrato.

---

**Caso d’uso:** Logout
**Attore principale:** Utente
**Descrizione:**  
L’utente autenticato può terminare volontariamente la propria sessione, disconnettendosi dal sistema della lavatrice intelligente. Il logout invalida la sessione attiva, impedendo ulteriori operazioni protette fino a nuovo login.

**Precondizioni:**
- L’utente è autenticato e dispone di una sessione attiva.
- L'utente ha accesso all'interfaccia di logout.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione di logout tramite l’interfaccia.
2. Il sistema riceve la richiesta di logout.
3. Il sistema invalida la sessione attiva dell’utente.
4. Il sistema conferma l’avvenuto logout all’utente.

**Postcondizioni:**  
- La sessione dell’utente è stata invalidata.
- L’utente non può più accedere alle funzionalità protette fino a nuovo login.

**Requisiti:**
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Attiva Comandi Vocali  
**Attore principale:** Utente  
**Descrizione:**  
L’utente può attivare la funzionalità di controllo vocale della lavatrice tramite l’interfaccia (app o display). Una volta attivati, i comandi vocali permettono di impartire istruzioni alla lavatrice usando la voce.

**Precondizioni:**  
- L’utente è autenticato nel sistema.
- La funzionalità di comandi vocali non è attiva.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione per attivare i comandi vocali tramite l’interfaccia.
2. Il sistema abilita la funzionalità di riconoscimento vocale.
3. Il sistema notifica l’utente dell’avvenuta attivazione.

**Postcondizioni:**  
- I comandi vocali sono attivi e pronti per essere utilizzati.

**Requisiti:**  
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Disattiva Comandi Vocali  
**Attore principale:** Utente  
**Descrizione:**  
L’utente può disattivare la funzionalità di controllo vocale della lavatrice tramite l’interfaccia (app o display). Una volta disattivati, i comandi vocali non sono più accettati dal sistema.

**Precondizioni:**
- L’utente è autenticato nel sistema.
- La funzionalità di comandi vocali è attiva.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione per disattivare i comandi vocali tramite l’interfaccia.
2. Il sistema disabilita la funzionalità di riconoscimento vocale.
3. Il sistema notifica l’utente dell’avvenuta disattivazione.

**Postcondizioni:**  
- I comandi vocali sono disattivati e non possono essere utilizzati fino a nuova attivazione.

**Requisiti:**  
- Nessun requisito aggiuntivo specifico.

---

**Caso d’uso:** Registra nuovo profilo utente  
**Attore principale:** Utente  
**Descrizione:**  
L’utente accede alla funzione di registrazione tramite l’interfaccia e inserisce i dati richiesti (es. nome, email, password, ecc.). Il sistema crea un nuovo profilo utente e conferma l’avvenuta registrazione. In caso di dati non validi o già utilizzati, il sistema segnala l’errore e richiede la correzione.

**Precondizioni:**  
- L’utente non è autenticato (non ha ancora un profilo attivo).
- L’interfaccia di registrazione è accessibile.

**Flusso principale degli eventi:**  
1. L’utente seleziona l’opzione “Registrati” nell’interfaccia.
2. Il sistema mostra il modulo di registrazione.
3. L’utente inserisce i dati richiesti (nome, email, password, ecc.).
4. Il sistema verifica la validità e l’unicità dei dati inseriti.
5. Se i dati sono validi, il sistema crea il nuovo profilo utente.
6. Il sistema conferma l’avvenuta registrazione e consente l’accesso alle funzionalità protette.
7. Se i dati non sono validi o già utilizzati, il sistema notificherà l’utente dell’errore.

**Postcondizioni:**  
- Un nuovo profilo utente è stato creato e memorizzato nel sistema.
- L’utente può autenticarsi ed accedere alle funzionalità protette.

**Requisiti:**  
- I dati inseriti devono essere validati e univoci (es. email non già registrata).
- La password deve rispettare i criteri di sicurezza definiti.

---

**Caso d’uso:** Modifica profilo utente  
**Attore principale:** Utente  
**Descrizione:**  
L’utente autenticato accede alla sezione di gestione del profilo tramite l’interfaccia e modifica uno o più dati personali (es. nome, email, password, preferenze). Il sistema verifica la validità dei nuovi dati, aggiorna il profilo utente e conferma l’avvenuta modifica. In caso di dati non validi o già utilizzati (es. email), il sistema segnala l’errore.

**Precondizioni:**  
- L’utente è autenticato e ha accesso alla sezione di gestione del profilo.

**Flusso principale degli eventi:**  
1. L’utente accede alla sezione Gestione profilo tramite l’interfaccia.
2. Il sistema mostra i dati attuali del profilo.
3. L’utente modifica uno o più dati (es. nome, email, password, preferenze).
4. Il sistema verifica la validità e l’unicità dei nuovi dati inseriti.
5. Se i dati sono validi, il sistema aggiorna il profilo utente.
6. Il sistema conferma l’avvenuta modifica.
7. Se i dati non sono validi o già utilizzati, il sistema notificherà l’utente dell’errore.

**Postcondizioni:**  
- Il profilo utente è stato aggiornato con i nuovi dati.
- L’utente riceve conferma dell’avvenuta modifica.

**Requisiti:**  
- I dati modificati devono essere validati e univoci (es. email non già registrata).
- La password deve rispettare i criteri di sicurezza definiti.


**attori**:
Utente
  ^
  |
Remote Control (App Mobile)

Sistema

Lavatrice

Dispositivo IoT Esterno