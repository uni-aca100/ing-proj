### Classe UIConfiguration

**Ruolo e Responsabilità:**
La classe `UIConfiguration` rappresenta la configurazione globale dell’interfaccia utente della lavatrice intelligente. Implementa il **pattern Singleton** per garantire che la configurazione sia unica e accessibile da tutte le componenti della UI e dei controller. Permette di personalizzare parametri come tema (dark/light), dimensione e tipo di font, contrasto, lingua, ecc.

**Collaborazioni:**
- Utilizzata da tutte le view/menu del Presentation Layer per il rendering.
- Modificata tramite `UIConfigurationMenu` e `UIConfigurationController`.
- utilizzata dal controller `LoginForm` per aggiornare la proprietà `sessionId` quando l'utente effettua il login.

**Principali attributi:**
- `instance`: istanza del singleton
- `mode`: tema dell’interfaccia (dark/light)
- `fontSize`: dimensione del font
- `contrast`: livello di contrasto (normal/high)
- `fontFamily`: tipo di font
- `language`: lingua dell’interfaccia
- `largeText`: dimensione del testo (grande/piccolo)
- `sessionId`: la corrente sessione di authentication della lavatrice (non quella remota), utilizzata come configurazione globale, il valore è null altrimenti

**Principali metodi:**
- `getInstance()`: restituisce l’istanza singleton
- `resetToDefaults()`: ripristina i valori di default per ogni parametro
- Getter/setter per ogni parametro

**motivazione:**
- Centralizza la gestione della personalizzazione e dell’accessibilità.
- Supporta la persistenza per utente o sistema (salvataggio delle preferenze in memoria).

---

### Classe UIConfigurationMenu

**Ruolo e Responsabilità:**
La classe `UIConfigurationMenu` gestisce l’interfaccia utente per la configurazione delle impostazioni della lavatrice intelligente. Permette agli utenti di modificare le preferenze di UI attraverso un menu dedicato.

**Collaborazioni:**
- Interagisce con `UIConfiguration` per il reperimento delle impostazioni correnti.
- Interagisce con `UIConfigurationController` per la gestione delle modifiche.

**Principali attributi:**
- `uiConfig`: riferimento all’istanza di `UIConfiguration`
- `options:`: opzioni disponibili nel menu di configurazione (key:value) per la modifica/interrogazione delle impostazioni

**Principali metodi:**
- `render()`: visualizza il menu di configurazione (qualsiasi sia la tecnologia UI utilizzata per implementare la GUI)
- `saveConfig()`: applica le modifiche effettuate dall’utente (presenti in `options`), richiamando i metodi di `UIConfigurationController` come `onSaveConfig(options: map<string, string>)`
- `resetToDefaults()`: ripristina le impostazioni di default, richiamando `UIConfiguration.resetToDefaults()`

**motivazione:**
- Fornisce un’interfaccia per la personalizzazione delle impostazioni di UI.

---

### Classe UIConfigurationController
**Ruolo e Responsabilità:**
La classe `UIConfigurationController` funge da intermediario tra il menu di configurazione (`UIConfigurationMenu`, `CommandDispatcher`) e la gestione effettiva delle impostazioni (`UIConfiguration`) come descritto dal Model-View-Controller (MVC) pattern. Gestisce la logica di validazione e l'interazione con l'application layer per l'applicazione delle modifiche alle impostazioni dell’interfaccia utente.

**Collaborazioni:**
- Interagisce con `UIConfiguration` per il reperimento delle impostazioni correnti e per l'applicazione delle modifiche.
- Interagisce con `UIConfigurationMenu` il quale fornisce un'interfaccia per la visualizzazione e l'interazione con l'utente.
- Interagisce con il `CommandDispatcher` per ricevere commandi di modifica delle impostazioni.

**Principali attributi:**
- `confService`: riferimento all’istanza di `UIConfiguration` che gestisce le impostazioni dell'interfaccia utente.

**Principali metodi:**
- `onSaveConfig(options: map<string, string>)`: gestisce il salvataggio delle modifiche alle impostazioni, validandole e applicandole tramite `UIConfiguration` (via i setter dei singoli attributi). le `options` contengono le nuove impostazioni da applicare.
- `onResetToDefaults()`: gestisce il ripristino delle impostazioni di default, richiamando `UIConfiguration.resetToDefaults()`.

**motivazione:**
- Controller in linea con il pattern MVC. permette di separare la logica di gestione delle impostazioni dalla loro rappresentazione e interfaccia utente.
- Facilita l’estendibilità e il riutilizzo riducendo la ridondanza, molteplici fonti di input possono essere gestite in modo uniforme (es. UI, comandi vocali, comandi remoti) dal controller (non interagendo direttamente con `UIConfiguration`).

---

### Classe WashingManager

**Ruolo e Responsabilità:**
La classe `WashingManager` è il componente per la gestione dei cicli di lavaggio. Coordina tutte le fasi operative della lavatrice, interfacciandosi con l’Hardware Layer per comandare i dispositivi fisici (compatibili) e con l’Application Layer per ricevere richieste di avvio, pausa, ripresa, annullamento e ottimizzazione dei cicli da altri componenti. Gestisce lo stato del lavaggio, monitora il progresso e segnala errori/eventi tramite notifiche.

**Collaborazioni:**
- Riceve richieste da `Scheduler` per l’avvio dei cicli pianificati quando è il momento di eseguire il ciclo.
- Interagisce con `LavatriceHardwareInterface` per il controllo diretto dell’hardware.
- Collabora con `NotificationService` per inviare notifiche all’utente (es. fine ciclo, errori).
- Espone operazioni di controllo ciclo ai controller (MVC pattern) del Presentation Layer (`WashControlController`).
- Può ricevere dati da `IoTIntegrationService` per ottimizzare i cicli in base alle informazioni esterne (es. stato energetico del sistema da uno smart meter).

**Principali attributi:**
- `stato`: stato corrente del lavaggio (in esecuzione, in pausa, completato, errore)
- `piano`: riferimento al `PianoLavaggio` in corso di esecuzione
- `hardware`: interfaccia verso l’Hardware Layer, che consente il controllo dei dispositivi fisici
- `notification`: riferimento a `NotificationService`

**Principali metodi:**
- `avviaLavaggio(piano: PianoLavaggio)`: avvia un nuovo ciclo di lavaggio, basato sul piano fornito
- `pausaLavaggio()`: mette in pausa il ciclo corrente, aggiornando lo stato a "in pausa"
- `riprendiLavaggio()`: riprende il ciclo messo in pausa, aggiornando lo stato a "in esecuzione"
- `annullaLavaggio()`: annulla il ciclo in corso
- `gestisciErrore(errore: ErroreLavatrice)`: gestisce errori tecnici, eventualmente notificando l’utente tramite `NotificationService` invocando il metodo `push()` indicando l’errore riscontrato
- `aggiornaStato()`: aggiorna lo stato del ciclo, veroificando i progressi e lo stato attuale
- `getState()`: restituisce lo stato corrente
- `optimizeCycle(iotData: string)`: ottimizza il ciclo in base ai dati IoT, tenendo conto delle condizioni di consumo energetico nell'ambiente circostante
- `getTaskProgress()`: restituisce la percentuale di avanzamento (0-100) del ciclo di lavaggio corrente

---

### Classe WashControlController

**Ruolo e Responsabilità:**
La classe `WashControlController` è il controller del MVC pattern, l'intermediario tra il Presentation Layer e il WashingManager (Application Layer). È responsabile della validazione e della gestione della sequenza di comandi di controllo del ciclo di lavaggio (pausa, riprendi, annulla, stato, avanzamento). Riceve i comandi dalle view/menu/dispatcher (`WashControlMenu`, `CommandDispatcher`) dell’interfaccia con utente e li inoltra al `WashingManager`.

**Collaborazioni:**
- Riceve input da `WashControlMenu` (UI/menu di controllo lavaggio) o il `CommandDispatcher` (comandi vocali o remoti).
- Inoltra le richieste operative a `WashingManager` (Application Layer).

**Principali attributi:**
- `washingManager`: riferimento al componente `WashingManager` che esegue le operazioni richieste.

**Principali metodi:**
- `onPausaLavaggio()`: richiede la pausa del ciclo corrente, richiamando `WashingManager.pausaLavaggio()`
- `onRiprendiLavaggio()`: richiede la ripresa del ciclo messo in pausa, richiamando `WashingManager.riprendiLavaggio()`
- `onAnnullaLavaggio()`: richiede l’annullamento del ciclo in corso, richiamando `WashingManager.annullaLavaggio()`
- `onGetState()`: richiede lo stato attuale del ciclo, richiamando `WashingManager.getState()`
- `onGetTaskProgress()`: richiede la percentuale di avanzamento del ciclo, richiamando `WashingManager.getTaskProgress()`

**motivazione:**
- Implementa il pattern Controller del MVC, separando la logica di presentazione dalla logica di business e gestendo l'interazione tra di esse.
- Garantisce che solo comandi validi (teoricamente) siano inoltrati al gestore del lavaggio, migliorando la robustezza e la sicurezza dell’interazione utente.
- Facilita l’estendibilità e il riutilizzo riducendo la ridondanza, molteplici fonti di input possono essere gestite in modo uniforme (es. UI, comandi vocali, comandi remoti) dal controller (non interagendo direttamente con `WashingManager`).

---

### Classe WashControlMenu

**Ruolo e Responsabilità:**
La classe `WashControlMenu` gestisce l’interfaccia utente dedicata al controllo del ciclo di lavaggio. Permette all’utente di inviare comandi (pausa, riprendi, annulla) e di visualizzare lo stato di avanzamento del ciclo.

**Collaborazioni:**
- Interagisce con `WashControlController` per inviare comandi e ricevere aggiornamenti sullo stato del ciclo.
- Utilizza `UIConfiguration` per adattare la visualizzazione alle preferenze dell’utente (es. accessibilità).

**Principali attributi:**
- `controller`: riferimento al `WashControlController` che gestisce l'interazione con il Application Layer per la gestione del ciclo di lavaggio.
- `conf`: riferimento alla configurazione UI globale (`UIConfiguration`) per il reperimento delle informazioni necessarie al rendering/accessibilità.

**Principali metodi:**
- `render()`: implementa la visualizzazione dell’interfaccia di controllo (qualsiasi sia la tecnologia UI utilizzata per implementare la GUI), richiama i metodi `onGetTaskProgress()` e `onGetState()` del controller `WashControlController` per aggiornare la visualizzazione dell'avanzamento e dello stato del ciclo, ecc.
- `inviaComandoPausa()`: invia il comando di pausa tramite il controller `WashControlController.onPausaLavaggio()`.
- `inviaComandoRiprendi()`: invia il comando di ripresa tramite il controller `WashControlController.onRiprendiLavaggio()`.
- `inviaComandoAnnulla()`: invia il comando di annullamento tramite il controller `WashControlController.onAnnullaLavaggio()`.

**motivazione:**
- Implementa la view del pattern MVC, separando la presentazione dalla logica di controllo.

---

### Classe Scheduler

**Ruolo e Responsabilità:**
La classe `Scheduler` è responsabile della pianificazione e gestione temporale dei cicli di lavaggio. Mantiene la lista delle pianificazioni (`Schedule`).
Questa classe è l'Observer (**TODO: implementa Observer pattern**) del clock di sistema e implementa il metodo onTick per ricevere notifiche periodiche. Ad ogni tick (effetuato ad intervalli regolari), verifica se ci sono cicli pianificati da avviare e, in caso affermativo, delega l'esecuzione dei cicli al `WashingManager` tramite `WashingManager.avviaLavaggio()` con il piano corrispondente, separando la logica di scheduling dalla logica di esecuzione dei cicli.

**Collaborazioni:**
- Collabora con `WashPlanningService` per ricevere nuove pianificazioni.
- Interagisce con `Schedule` per la gestione delle singole pianificazioni.
- Inoltra le richieste di avvio ciclo a `WashingManager` quando una pianificazione è pronta per essere eseguita.
- riceve eventi dal clock di sistema o da un timer hardware/software (tramite il metodo `onTick`).

**Principali attributi:**
- `schedules`: lista delle pianificazioni attive (`Schedule`)
- `washingManager`: riferimento al componente che esegue i cicli (`WashingManager`)

**Principali metodi:**
- `onTick(currentTime: DateTime)`: metodo chiamato periodicamente dal clock di sistema; verifica se ci sono cicli da avviare (la data corrente è superiore alla data di inizio della pianificazione) e, in caso positivo, delega l'esecuzione a `WashingManager` tramite `WashingManager.avviaLavaggio()` con il piano corrispondente.
- `aggiungiSchedule(schedule: Schedule)`: aggiunge una nuova pianificazione alla lista.
- `validaSchedule(schedule: Schedule)`: valida una pianificazione prima di aggiungerla alla lista (es. controlla conflitti di orario), richiamata da `WashPlanningService` per la validazione di nuove pianificazioni.

**motivazione:**
- Implementa la logica di scheduling separando la gestione temporale della pianificazione dei lavaggi dalla logica dell'esecuzione dei cicli di lavaggio.
- gestisce più pianificazioni, in modo da garantire che ogni ciclo di lavaggio venga eseguito al momento giusto.

---

### Classe WashPlanningService

**TODO**: la classe va modificata per includere piani di defalult ( correntemente sono personalizzati ) 

**Ruolo e Responsabilità:**
La classe `WashPlanningService` è l'application service responsabile della pianificazione dei cicli di lavaggio. Espone le operazioni di pianificazione verso il Presentation Layer, riceve i parametri dal controller, valida i dati, crea oggetti `PianoLavaggio` e `Schedule`, e aggiunge la pianificazione allo Scheduler. Si occupa di garantire che le pianificazioni siano corrette e non in conflitto.

**Collaborazioni:**
- Riceve input da `WashPlanningController` (Presentation Layer) che raccoglie i dati dall’utente.
- Utilizza `PianoLavaggio` per definire i dettagli del ciclo di lavaggio.
- Utilizza `Schedule` per la creazione e gestione delle pianificazioni.
- Interagisce con `Scheduler` per aggiungere nuove pianificazioni e aggiornarle.

**Principali attributi:**
- `scheduler`: riferimento al componente che gestisce la pianificazione temporale (`Scheduler`).
- **TODO**: `piani`: lista delle piani di lavaggio disponibili del sistema.
- `notification`: riferimento al servizio di notifica per eventuali messaggi all'utente (`NotificationService`) come errori o avvisi di successo.

**Principali metodi:**
- **TODO fix usare piani prestabiliti**

**motivazione:**
- ... TODO fix usare piani prestabiliti

---

### Classe DiagnosticHandler

**Ruolo e Responsabilità:**
La classe `DiagnosticHandler` è responsabile della gestione delle procedure di diagnostica della lavatrice intelligente. Coordina l’esecuzione dei test diagnostici sui componenti hardware, raccoglie i risultati, li analizza e genera un report diagnostico da inviare all’utente/tecnico.

**Collaborazioni:**
- Riceve richieste di diagnostica da `DiagnosticController` (Presentation Layer), per l'esecuzione o l'ispezione dell'ultimo test.
- Interagisce con l’`Hardware Layer` tramite interfacce dedicate per eseguire i test sui componenti (motore, sensori, valvole, ecc.).
- Collabora con `NotificationService` per inviare notifiche all’utente sull’esito della diagnostica.
- utilizza `DiagnosticReport` per rappresentare i risultati della diagnostica.
- utilizza `AuthenticationService` per verificare che l’utente abbia i permessi necessari per eseguire la diagnostica.

**Principali attributi:**
- `hardwareInterface`: riferimento all’interfaccia hardware per l’esecuzione dei test.
- `notification`: riferimento al servizio di notifica per comunicare l’esito della diagnostica.
- `lastReport`: ultimo report diagnostico generato, per l’ispezione dell’ultimo test.

**Principali metodi:**
- `execute(sessionId: string): DiagnosticReport`: avvia la procedura di diagnostica, esegue i test sui componenti, raccoglie e analizza i risultati, genera e restituisce un report
    - Prima di effettuarlo verifica i permessi dell’utente tramite `AuthenticationService.verify(sessionId)`. Al termine invia una notifica all’utente tramite `NotificationService.push()` con l’esito della diagnostica.
- setter and getter impliciti restituisce/modificare l’ultimo report diagnostico generato.

---

### Classe DiagnosticController

**Ruolo e Responsabilità:**
La classe `DiagnosticController` è il controller del Presentation Layer dedicato alla gestione delle operazioni di diagnostica. Riceve le richieste dalla UI/remoto/vocale (da `DiagnosticMenu`, `CommandDispatcher`), effettua eventuali validazioni (es. verifica dei dati di input) e inoltra la richiesta di diagnostica al `DiagnosticHandler`. Gestendo la comunicazione con l’utente in maniera uniforme.

**Collaborazioni:**
- Riceve input da `DiagnosticMenu` (UI/menu di diagnostica) o da sistemi esterni tramite `CommandDispatcher`.
- Inoltra le richieste operative a `DiagnosticHandler` (Application Layer) per l’esecuzione della diagnostica.
- utilizza `DiagnosticReport` per rappresentare i risultati della diagnostica.

**Principali attributi:**
- `diagnosticHandler`: riferimento al componente che esegue la diagnostica (`DiagnosticHandler`).

**Principali metodi:**
- `onStartDiagnostica(sessionId: string): DiagnosticReport`: avvia la procedura di diagnostica, inoltrando la richiesta a `DiagnosticHandler.execute(sessionId)` e ottenendo il risultato `DiagnosticReport` per la visualizzazione del report.
- `onGetLastReport(): DiagnosticReport`: restituisce l’ultimo report diagnostico generato da `DiagnosticHandler` per la visualizzazione.

**motivazione:**
- Implementa il pattern Controller del MVC, separando la logica di presentazione dalla logica di implementazione della diagnostica.
- Consente l’estendibilità per integrare nuove fonti di input o logiche di visualizzazione dei risultati (es. supporto per comandi vocali, comandi remoti ecc.).

---

### Classe DiagnosticMenu

**Ruolo e Responsabilità:**
La classe `DiagnosticMenu` gestisce l’interfaccia utente dedicata alle operazioni di diagnostica della lavatrice intelligente. Permette all’utente di avviare la procedura di diagnostica, visualizzare lo stato e i risultati dei test.

**Collaborazioni:**
- Interagisce con `DiagnosticController` per avviare la diagnostica e ottenere i report.
- Utilizza `DiagnosticReport` per visualizzare i risultati dei test diagnostici.

**Principali attributi:**
- `controller`: riferimento al `DiagnosticController` che gestisce la logica di diagnostica.
- `lastReport`: ultimo report diagnostico visualizzato.

**Principali metodi:**
- `render()`: visualizza il menu di diagnostica e lo stato attuale (ad esempio, pulsante per avviare la diagnostica, area per visualizzare il report).
    - (qualsiasi sia la tecnologia UI utilizzata per implementare la GUI)
- `startDiagnostica()`: invia la richiesta di diagnostica al controller tramite `controller.onStartDiagnostica(sessionId)` dove `sessionId` è fornito dalla `UIConfiguration.sessionId`.

**motivazione:**
- Implementa la view del pattern MVC, separando la presentazione dalla logica di controllo e gestione della diagnostica.
- Permettendo all’utente di accedere facilmente alle funzionalità di diagnostica e ai risultati.

---


