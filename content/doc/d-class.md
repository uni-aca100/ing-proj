### classe SystemContext

**Ruolo e Responsabilità:**
La classe `SystemContext` rappresenta il contesto globale dell'applicazione, mantenendo le configurazioni, stati e le informazioni condivise tra i vari componenti. Implementa il **pattern Singleton** per garantire che ci sia un'unica istanza accessibile da tutte le parti dell'applicazione.
Inoltre è conforme al pattern **Observer** per permettere ad altre classi (es. view, menu) di registrarsi come osservatori ed essere avvertite quando lo stato globale cambia (es. modifica delle preferenze utente).

**Collaborazioni:**
- Contiene le configurazioni UI tramite `UIConfiguration`.
- Utilizzata `Event` per fornire informazioni sui cambiamenti del contesto (es. modifica delle preferenze utente).
- Utilizzata da tutte le view per accedere alla Configurazione UI globale tramite `UIConfiguration` utilizzata nel rendering/presentazione.
- Il `NotificationService` interagisce con `SystemContext` per indicare la presenza di notifiche non lette.
- Utilizzata dal `LoginForm` dopo una richiesta di login che ha avuto successo per aggiornare la proprietà `sessionId`.

**Principali attributi:**
- `sessionId`: identificatore della sessione utente corrente (per la lavatrice, non per l'app mobile), utilizzata per autenticare le richieste, il valore è null altrimenti
- `config` (`UIConfiguration`): istanza della configurazione UI globale
- `observers` (`List<Observer>`): lista degli osservatori registrati per le notifiche di cambiamento del contesto
- `hasUnreadNotifications`: flag per indicare la presenza di notifiche non lette, permette di mostrare un indicatore nell'interfaccia utente
- `storage` (`IStorage`): interfaccia per l'accesso alla memoria di massa, utilizzata per salvare/caricare le configurazioni persistenti

**Principali metodi:**
- `getInstance()`: restituisce l’istanza singleton
- `notifyObservers()`: notifica tutti gli osservatori registrati di un cambiamento nel contesto globale
    - richiama il metodo `update(event: Event)` di ogni osservatore registrato fornendo l'oggetto `Event` con i dettagli del cambiamento
    - `Event`: oggetto evento contenente informazioni sul cambiamento, es. con attributi come `type="UIConfChange"` e `data={fontSize: "large", contrast: "high"}` per indicare che la configurazione UI è cambiata
- `register(o: Observer)`: aggiunge un nuovo osservatore alla lista `observers`
- `unregister(o: Observer)`: rimuove un osservatore esistente dalla lista `observers`
- `saveInStorage()`: salva la configurazione/stato corrente (`UIConfiguration`, `sessionId`, `hasUnreadNotifications`) nella memoria di massa tramite l'interfaccia `IStorage.write(...)`, permettendo la persistenza delle impostazioni utente
    - durante l'invocazione del Constructor di `SystemContext`, carica lo stato salvato (se presente) dalla memoria di massa per inizializzare gli attributi tramite `storage.read(...)`
- Getter/setter per `sessionId`, `hasUnreadNotifications` e `config`, con i setter che invocano `notifyObservers()` per informare gli osservatori delle modifiche

**motivazione:**
- Centralizza lo stato e le configurazioni globali dell'applicazione, facilitando l'accesso e la gestione condivisa.
- Permette una comunicazione efficiente tra componenti tramite il pattern Observer, riducendo il coupling diretto e migliorando la manutenibilità del codice.
- Facilita l'implementazione di funzionalità reattive nell'interfaccia utente, poiché le view possono aggiornarsi automaticamente in risposta ai cambiamenti nel contesto.

---

### Classe UIConfiguration

**Ruolo e Responsabilità:**
La classe `UIConfiguration` rappresenta la configurazione globale dell’interfaccia utente della lavatrice intelligente. viene esposta e mantenuta una configurazione tramite `SystemContext` per garantire che la configurazione sia unica e accessibile da tutte le componenti della UI. Permette di personalizzare parametri come tema (dark/light), dimensione e tipo di font, contrasto, lingua, ecc.

**Collaborazioni:**
- Utilizzata da tutte le view/menu del Presentation Layer indirettamente tramite `SystemContext` per il rendering

**Principali attributi:**
- `instance`: istanza del singleton
- `mode`: tema dell’interfaccia (dark/light)
- `fontSize`: dimensione del font
- `contrast`: livello di contrasto (normal/high)
- `fontFamily`: tipo di font
- `language`: lingua dell’interfaccia
- `largeText`: dimensione del testo (grande/piccolo)

**Principali metodi:**
- `getInstance()`: restituisce l’istanza singleton
- `resetToDefaults()`: ripristina i valori di default per ogni parametro
- Getter/setter per ogni parametro


---

### Classe UIConfigurationMenu

**Ruolo e Responsabilità:**
La classe `UIConfigurationMenu` gestisce l’interfaccia utente per la configurazione delle impostazioni della lavatrice intelligente. Permette agli utenti di modificare le preferenze di UI attraverso un menu dedicato.
**Implementa l'interfaccia Observer** dell'Observer Pattern per ricevere eventi quando il contesto globale (`SystemContext` e indirettamente `UIConfiguration`) cambiano.

**Collaborazioni:**
- Interagisce con  `SystemContext` per il reperimento delle impostazioni correnti (sia stato notifiche che la `UIConfiguration` corrente).
- Interagisce con `UIConfigurationController` per la gestione delle modifiche.

**Principali attributi:**
- `ObservableCtx`: (`SystemContext`): riferimento al contesto di sistema per accedere alla configurazione UI globale e lo stato delle notifiche
- `options:`: opzioni disponibili nel menu di configurazione (key:value) per la modifica/interrogazione delle impostazioni

**Principali metodi:**
- `render()`: visualizza il menu di configurazione (qualsiasi sia la tecnologia UI utilizzata per implementare la GUI), recuperando le impostazioni correnti da `SystemContext` (come `UIConfiguration` e `hasUnreadNotifications`)
- `saveConfig()`: applica le modifiche effettuate dall’utente (presenti in `options`), richiamando i metodi di `UIConfigurationController` come `onSaveConfig(options: map<string, string>)`
- `resetToDefaults()`: ripristina le impostazioni di default, richiamando `UIConfiguration.resetToDefaults()`
- `update(e: Event)`: gestisce le notifiche di cambiamento dal contesto globale `SystemContext`, aggiornando la visualizzazione del menu di pianificazione se necessario.

**motivazione:**
- Fornisce un’interfaccia per la personalizzazione delle impostazioni di UI.

---

### Classe UIConfigurationController
**Ruolo e Responsabilità:**
La classe `UIConfigurationController` funge da intermediario tra il menu di configurazione (`UIConfigurationMenu`, `CommandDispatcher`) e la gestione effettiva delle impostazioni (`UIConfiguration` tramite `SystemContext`) come descritto dal Model-View-Controller (MVC) pattern. Gestisce la logica di validazione e l'interazione con l'application layer per l'applicazione delle modifiche alle impostazioni dell’interfaccia utente.

**Collaborazioni:**
- Interagisce con `UIConfiguration` tramite `SystemContext` per il reperimento delle impostazioni correnti e per l'applicazione delle modifiche.
- Interagisce con `UIConfigurationMenu` il quale fornisce un'interfaccia per la visualizzazione e l'interazione con l'utente.
- Interagisce con il `CommandDispatcher` per ricevere commandi vocali/remoti di modifica delle impostazioni.

**Principali attributi:**
- `ctx`: riferimento all’istanza di `SystemContext` che gestisce le impostazioni dell'interfaccia utente tramite `UIConfiguration`.

**Principali metodi:**
- `onSaveConfig(options: map<string, string>)`: gestisce il salvataggio delle modifiche alle impostazioni, validandole e applicandole tramite `SystemContext` e `UIConfiguration` (via i setter dei singoli attributi). le `options` contengono le nuove impostazioni da applicare.
- `onResetToDefaults()`: gestisce il ripristino delle impostazioni di default, richiamando `UIConfiguration.resetToDefaults()` tramite `SystemContext.resetUIConf()`.

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
- `storage`: riferimento a `IStorage` per salvare lo stato e il piano del lavaggio in corso

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
- `saveInStorage()`: salva lo stato corrente e il piano del lavaggio in corso nella memoria di massa tramite l'interfaccia `IStorage.write(...)`, permettendo la persistenza del ciclo in corso in caso di interruzioni.
    - `storage.read(...)` è utilizzato durante l'inizializzazione (Constructor) per caricare lo stato salvato (se presente)

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
**Implementa l'interfaccia Observer** dell'Observer Pattern per ricevere eventi quando il contesto globale (`SystemContext` e indirettamente `UIConfiguration`) cambiano.

**Collaborazioni:**
- Interagisce con `WashControlController` per inviare comandi e ricevere aggiornamenti sullo stato del ciclo.
- Utilizza `UIConfiguration` per adattare la visualizzazione alle preferenze dell’utente (es. accessibilità).

**Principali attributi:**
- `controller`: riferimento al `WashControlController` che gestisce l'interazione con il Application Layer per la gestione del ciclo di lavaggio.
- `ObservableCtx`: riferimento alla configurazione UI globale (`SystemContext`) per il reperimento delle informazioni necessarie al rendering/accessibilità.

**Principali metodi:**
- `render()`: implementa la visualizzazione dell’interfaccia di controllo (qualsiasi sia la tecnologia UI utilizzata per implementare la GUI), richiama i metodi `onGetTaskProgress()` e `onGetState()` del controller `WashControlController` per aggiornare la visualizzazione dell'avanzamento e dello stato del ciclo, ecc.
    - Adatta la visualizzazione in base alle impostazioni correnti da `SystemContext` (come `UIConfiguration` e `hasUnreadNotifications`)
- `inviaComandoPausa()`: invia il comando di pausa tramite il controller `WashControlController.onPausaLavaggio()`.
- `inviaComandoRiprendi()`: invia il comando di ripresa tramite il controller `WashControlController.onRiprendiLavaggio()`.
- `inviaComandoAnnulla()`: invia il comando di annullamento tramite il controller `WashControlController.onAnnullaLavaggio()`.
- `update(e: Event)`: gestisce le notifiche di cambiamento dal contesto globale `SystemContext`, aggiornando la visualizzazione del menu di pianificazione se necessario.

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
- `storage`: riferimento a `IStorage` per salvare/caricare le pianificazioni nella memoria di massa

**Principali metodi:**
- `onTick(currentTime: DateTime)`: metodo chiamato periodicamente dal clock di sistema; verifica se ci sono cicli da avviare (la data corrente è superiore alla data di inizio della pianificazione) e, in caso positivo, delega l'esecuzione a `WashingManager` tramite `WashingManager.avviaLavaggio()` con il piano corrispondente.
- `aggiungiSchedule(schedule: Schedule)`: aggiunge una nuova pianificazione alla lista.
- `validaSchedule(schedule: Schedule)`: valida una pianificazione prima di aggiungerla alla lista (es. controlla conflitti di orario), richiamata da `WashPlanningService` per la validazione di nuove pianificazioni.
- `saveInStorage()`: salva le pianificazioni correnti nella memoria di massa tramite l'interfaccia `IStorage.write(...)`, permettendo la persistenza delle pianificazioni in caso di interruzioni.
    - `storage.read(...)` è utilizzato durante l'inizializzazione (Constructor) per caricare le pianificazioni salvate (se presenti)

**motivazione:**
- Implementa la logica di scheduling separando la gestione temporale della pianificazione dei lavaggi dalla logica dell'esecuzione dei cicli di lavaggio.
- gestisce più pianificazioni, in modo da garantire che ogni ciclo di lavaggio venga eseguito al momento giusto.

---

### Classe Schedule

**Ruolo e Responsabilità:**
La classe `Schedule` rappresenta una singola pianificazione di un ciclo di lavaggio. Contiene le informazioni necessarie per eseguire il ciclo in un momento specifico, come il piano di lavaggio associato e la data/ora di inizio.

**Collaborazioni:**
- Utilizzata da `WashPlanningService` per creare nuove pianificazioni.
- Utilizzata da `Scheduler` per gestire l'insieme di pianificazioni e l’esecuzione temporale delle pianificazioni.

**Principali attributi:**
- `pianoLavaggio`: riferimento al `PianoLavaggio` associato alla pianificazione in una specifica data/ora.
- `dataOra`: data e ora in cui il ciclo di lavaggio deve essere avviato.

**Principali metodi:**
- Getter/setter per ogni attributo.

**motivazione:**
- Rappresenta in modo strutturato le informazioni di una singola pianificazione.

---

### Classe WashPlanningService

**Ruolo e Responsabilità:**
La classe `WashPlanningService` è l'application service responsabile della pianificazione dei cicli di lavaggio. Espone le operazioni di pianificazione verso il Presentation Layer tramite un catalogo di piani di lavaggio disponibili, crea gli oggetti `Schedule` e le aggiunge alle pianificazioni dello scheduler. Inoltre si occupa di notificare l'utente sia in caso di successo che di errore.

**Collaborazioni:**
- Riceve input e depone i piani disponibili a `WashPlanningController` (Presentation Layer).
- Utilizza `PianoLavaggio` per definire i dettagli del ciclo di lavaggio disponibili.
- Utilizza `Schedule` per la creazione e gestione delle pianificazioni.
- Interagisce con `Scheduler` per aggiungere nuove pianificazioni e aggiornarle.

**Principali attributi:**
- `scheduler`: riferimento al componente che gestisce la pianificazione temporale (`Scheduler`).
- `catalog`: lista delle piani di lavaggio disponibili del sistema.
- `notification`: riferimento al servizio di notifica per eventuali messaggi all'utente (`NotificationService`) come errori o avvisi di successo.
- `storage`: riferimento a `IStorage` per salvare/caricare i piani di lavaggio nella memoria di massa

**Principali metodi:**
- `getCatalog()`: `List<PianoLavaggio>` : restituisce la lista dei piani di lavaggio disponibili nel sistema (solo piani predefiniti).
- `pianificaLavaggio(piano: PianoLavaggio, dataOra: DateTime)`: crea una nuova pianificazione (`schedule`) basata sul piano e la data/ora forniti (dal controller), le aggiunge allo scheduler tramite `Scheduler.aggiungiSchedule()` che in caso di conflitto restituisce `false` indicando l'insuccesso.
    - Invia una notifica all’utente tramite `NotificationService.push()` indicando il risultato finale (sia esso errore o successo).
- `saveInStorage()`: salva i piani di lavaggio correnti nella memoria di massa tramite l'interfaccia `IStorage.write(...)`, permettendo la persistenza del catalogo di Piani predefiniti in caso di interruzioni.
    - `storage.read(...)` è utilizzato durante l'inizializzazione (Constructor) per caricare i piani salvati (se presenti)

**motivazione:**
- Garantire che le pianificazioni siano corrette e non in conflitto.
- espone le operazioni predefinite di pianificazione verso il Presentation Layer.

---

### Classe WashPlanningController
**Ruolo e Responsabilità:**
La classe `WashPlanningController` funge da intermediario tra il menu di pianificazione (`WashPlanningMenu`, `CommandDispatcher`) e la gestione effettiva delle pianificazioni (`WashPlanningService`) come descritto dal Model-View-Controller (MVC) pattern. Gestisce la logica di validazione (parametri selezionati, formato data/ora ecc.) e l'interazione con l'application layer per la creazione e gestione delle pianificazioni di lavaggio.

**Collaborazioni:**
- Interagisce con `WashPlanningService` per la creazione e gestione delle pianificazioni di lavaggio.
- Interagisce con `WashPlanningMenu` il quale fornisce un'interfaccia per la visualizzazione e l'interazione con l'utente.
- Interagisce con il `CommandDispatcher` per ricevere commandi di pianificazione dei lavaggi vocali/remoti.

**Principali attributi:**
- `planningService`: riferimento all’istanza di `WashPlanningService` che gestisce le pianificazioni di lavaggio.

**Principali metodi:**
- `onGetCatalog(): List<PianoLavaggio>`: restituisce il catalogo dei piani di lavaggio disponibili, richiamando `WashPlanningService.getCatalog()`.
- `onPianificaLavaggio(piano: PianoLavaggio, dataOra: DateTime)`: gestisce la richiesta di pianificazione di un ciclo di lavaggio, richiamando `WashPlanningService.pianificaLavaggio(piano, dataOra)` ed effettuando una prima verifica.

**motivazione:**
- Controller in linea con il pattern MVC. permette di separare la logica di gestione delle pianificazioni dalla loro rappresentazione e interfaccia utente.
- Facilita l’estendibilità e il riutilizzo riducendo la ridondanza, molteplici fonti di input possono essere gestite in modo uniforme (es. UI, comandi vocali, comandi remoti) dal controller (non interagendo direttamente con `WashPlanningService`).

### Classe WashPlanningMenu
**Ruolo e Responsabilità:**
La classe `WashPlanningMenu` gestisce l’interfaccia utente per la pianificazione dei cicli di lavaggio della lavatrice intelligente. Permette all’utente di selezionare un piano di lavaggio dal catalogo disponibile e di specificare la data/ora per l’esecuzione del ciclo.
**Implementa l'interfaccia Observer** dell'Observer Pattern per ricevere eventi quando il contesto globale (`SystemContext` e indirettamente `UIConfiguration`) cambiano.

**Collaborazioni:**
- Interagisce con `WashPlanningController` per ottenere il catalogo dei piani di lavaggio e per inviare le richieste di pianificazione.
- Utilizza `PianoLavaggio` per visualizzare i dettagli dei piani di lavaggio disponibili.

**Principali attributi:**
- `controller`: riferimento al `WashPlanningController` che gestisce la logica di pianificazione e restituisce i piani di lavaggio disponibili.
- `selectedPiano` (`PianoLavaggio`) selezionato dall’utente per la pianificazione.
- `dataOra`: data e ora selezionata dall’utente per l’esecuzione del ciclo di lavaggio.
- `ObservableCtx` (`SystemContext`) riferimento al contesto globale per il rendering/accessibilità.

**Principali metodi:**
- `render()`: visualizza il menu di pianificazione (qualsiasi sia la tecnologia UI utilizzata per implementare la GUI), mostrando il catalogo dei piani di lavaggio e i campi per la selezione della data/ora.
    - Adatta la visualizzazione in base alle impostazioni correnti da `SystemContext` (come `UIConfiguration` e `hasUnreadNotifications`)
- `submit()`: invia la richiesta di pianificazione al controller tramite `controller.onPianificaLavaggio(selectedPiano, dataOra)`.
- `update(e: Event)`: gestisce le notifiche di cambiamento dal contesto globale `SystemContext`, aggiornando la visualizzazione del menu di pianificazione se necessario.

**motivazione:**
- Implementa la view del pattern MVC, separando la presentazione dalla logica di controllo.
- Permettendo all’utente di accedere facilmente alle funzionalità di pianificazione e di selezionare i piani di lavaggio desiderati.      

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
- `storage`: riferimento a `IStorage` per salvare/caricare i report di diagnostica.

**Principali metodi:**
- `execute(sessionId: string): DiagnosticReport`: avvia la procedura di diagnostica, esegue i test sui componenti, raccoglie e analizza i risultati, genera e restituisce un report
    - Prima di effettuarlo verifica i permessi dell’utente tramite `AuthenticationService.verify(sessionId)`. Al termine invia una notifica all’utente tramite `NotificationService.push()` con l’esito della diagnostica.
- `saveLastReportInStorage()`: salva l'ultimo report diagnostico nella memoria di massa tramite l'interfaccia `IStorage.write(...)`, permettendo la persistenza del report in caso di interruzioni.
    - `storage.read(...)` è utilizzato durante l'inizializzazione (Constructor) per caricare l'ultimo report salvato (se presente)
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
**Implementa l'interfaccia Observer** dell'Observer Pattern per ricevere eventi quando il contesto globale (`SystemContext` e indirettamente `UIConfiguration`) cambiano.

**Collaborazioni:**
- Interagisce con `DiagnosticController` per avviare la diagnostica e ottenere i report.
- Utilizza `DiagnosticReport` per visualizzare i risultati dei test diagnostici.

**Principali attributi:**
- `controller`: riferimento al `DiagnosticController` che gestisce la logica di diagnostica.
- `lastReport`: ultimo report diagnostico visualizzato.

**Principali metodi:**
- `render()`: visualizza il menu di diagnostica e lo stato attuale (ad esempio, pulsante per avviare la diagnostica, area per visualizzare il report).
    - (qualsiasi sia la tecnologia UI utilizzata per implementare la GUI)
    - Adatta la visualizzazione in base alle impostazioni correnti da `SystemContext` (come `UIConfiguration` e `hasUnreadNotifications`)
- `startDiagnostica()`: invia la richiesta di diagnostica al controller tramite `controller.onStartDiagnostica(sessionId)` dove `sessionId` è fornito dalla `UIConfiguration.sessionId`.
- `update(e: Event)`: gestisce le notifiche di cambiamento dal contesto globale `SystemContext`, aggiornando la visualizzazione del menu di pianificazione se necessario.

**motivazione:**
- Implementa la view del pattern MVC, separando la presentazione dalla logica di controllo e gestione della diagnostica.
- Permettendo all’utente di accedere facilmente alle funzionalità di diagnostica e ai risultati.

---

### Classe DiagnosticReport

**Ruolo e Responsabilità:**
La classe `DiagnosticReport` rappresenta il risultato di una procedura di diagnostica eseguita sulla lavatrice intelligente. Contiene le informazioni, eventuali anomalie rilevate ed eventuali warnings

**Collaborazioni:**
- Viene generata da `DiagnosticHandler` al termine della procedura di diagnostica.
- Viene utilizzata da `DiagnosticController` e `DiagnosticMenu` per la visualizzazione dei risultati all’utente.

**Principali attributi:**
- `esito`: bool, se esito della procedura di diagnostica è true lo stato della lavatrice è OK.
- `errori`: `List<ErroreLavatrice>` , lista degli errori/warning rilevati durante la diagnostica.

**Principali metodi:**
- setter and getter per gli attributi, impliciti.

---

### Interface IStorage
**Ruolo e Responsabilità:**
L'interfaccia `IStorage` definisce i metodi standard per l'accesso e la gestione dei dati persistenti all'interno della lavatrice intelligente. Indipendentemente dal tipo di storage utilizzato (es. database, file system, cloud).

**Principali Metodi:**
- `read(address: int, length: int): byte[]`: legge un blocco di dati dalla memoria.
- `write(address: int, data: byte[]): bool`: scrive un blocco di dati nella memoria.

**motivazione:**
- Fornisce un'interfaccia uniforme per l'accesso ai dati persistenti, facilitando la sostituzione o l'aggiornamento del sistema di storage senza influenzare il resto dell'applicazione.
- Promuove la separazione delle preoccupazioni, consentendo alle classi di business logic di interagire con i dati senza doversi preoccupare dei dettagli di implementazione dello storage.
- Facilita il testing e la manutenzione del codice, poiché le implementazioni concrete possono essere facilmente sostituite con mock o stub durante i test.
- Consente il riutilizzo del codice in diversi contesti o progetti (componenti dell'application layer esplicitano le loro dipendenze attraverso questa interfaccia).