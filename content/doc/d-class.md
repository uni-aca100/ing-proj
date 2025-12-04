### Classe UIConfiguration

**Ruolo e Responsabilità:**
La classe `UIConfiguration` rappresenta la configurazione globale dell’interfaccia utente della lavatrice intelligente. Implementa il pattern Singleton per garantire che la configurazione sia unica e accessibile da tutte le componenti della UI e dei controller. Permette di personalizzare parametri come tema (dark/light), dimensione e tipo di font, contrasto, lingua, ecc.

**Collaborazioni:**
- Utilizzata da tutte le view/menu del Presentation Layer per il rendering.
- Modificata tramite `UIConfigurationMenu` e `UIConfigurationController`.

**Principali attributi:**
- `instance`: istanza del singleton
- `mode`: tema dell’interfaccia (dark/light)
- `fontSize`: dimensione del font
- `contrast`: livello di contrasto (normal/high)
- `fontFamily`: tipo di font
- `language`: lingua dell’interfaccia
- `largeText`: dimensione del testo (grande/piccolo)
- `userId`: configurazione per utente (opzionale)

**Principali metodi:**
- `getInstance()`: restituisce l’istanza singleton
- `resetToDefaults()`: ripristina i valori di default per ogni parametro
- Getter/setter per ogni parametro

**motivazione:**
- Centralizza la gestione della personalizzazione e dell’accessibilità.
- Supporta la persistenza per utente o sistema (salvataggio delle preferenze in memoria).

### classe UIConfigurationMenu

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

### classe UIConfigurationController
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
