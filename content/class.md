# Bozze per Dia. classi UML

## Presentation Layer

**Classe WashPlanningForm**
```
class WashPlanningForm implements Observer is
    // Attributi
    selectedPiano: PianoLavaggio
    dataOra: DateTime // data/ora pianificazione
    controller: WashPlanningController // riferimento al controller
    ObservableCtx: SystemContext // for the rendering config
    options: map<String,String> // like temperature, spinSpeed etc.

    // Metodi
    submit()  // chiama controller.onPianificaLavaggio(selectedPiano, dataOra)
    render()
    update(e: Event)
```
// Descrizione: Form/view del Presentation Layer per la pianificazione di un ciclo (rappresenta la UI per la raccolta dei dati di pianificazione dal’utente). Raccoglie i dati dall’utente e, al submit, invoca il metodo onPianificaLavaggio del controller passando i parametri inseriti.

**Classe WashPlanningController**
```
class WashPlanningController is
    // Attributi
    washPlanningService: WashPlanningService // dipendenza verso Application Service

    // Metodi
    onPianificaLavaggio(piano: PianoLavaggio, dataOra: DateTime, options: map<String,String>): bool // chiama washPlanningService.pianificaLavaggio()
    onGetCatalog(): List<PianoLavaggio>
```
// Descrizione: Controller "sottile" (non contiene logica di business, delegata all'application Service) del Presentation Layer. che riceve i dati dal form/view per la pianificazione di cicli di lavaggio, ed un eventuale validazione lato presentazione (es. campi mancati). Gestisce l'iterazione con WashPlanningService (dell'application layer)

**Classe WashControlMenu**
```
class WashControlMenu implements Observer is
    // Attributi
    controller: WashControlController // riferimento al controller
    ObservableCtx: SystemContext // for the rendering config

    // Metodi
    render() // render the interface and call controller.onGetTaskProgress()/getTaskProgress() etc. at regular intervals
    inviaComandoPausa() // chiama eventualmente controller.onPausaLavaggio()
    inviaComandoRiprendi() // chiama eventualmente controller.onriPrendiLavaggio()
    inviaComandoAnnulla() // chiama eventualmente controller.onPausaLavaggio()
    update(e: Event)
```
// Descrizione: Menu/view del Presentation Layer che offre all’utente i comandi di controllo del lavaggio (pausa, riprendi, annulla) e visualizza lo stato corrente del lavaggio. Interagisce con WashControlController per inoltrare i comandi.

**Classe WashControlController**
```
class WashControlController is
    // Attributi
    washingControl: WashingManager // dipendenza verso Application Service

    // Metodi
    onPausaLavaggio() // chiama eventualmente WashingManager.pausaLavaggio()()
    onRiprendiLavaggio() // ...
    onAnnullaLavaggio()
    onGetState()
    onGetTaskProgress(): Int // call washingControl.getTaskProgress()
```
// Descrizione: Controller del Presentation Layer che espone le operazioni di controllo ciclo (pausa, riprendi, annulla, stato) verso la UI. Riceve i comandi dalle view/menu e li inoltra al WashingManager eventualmente dopo un verifica sullo stato corrente.

**Classe CommandDispatcher**
```
class CommandDispatcher is
    // Attributi
    washPlanningController: WashPlanningController
    washControlController: WashControlController
    ...

    // Metodi
    dispatch(command: string, sessionId: string)
```
// Descrizione: Riceve l’azione vocale/remoto già interpretata dall’Application Layer e chiama il controller appropriato nel Presentation Layer (es. WashPlanningController, WashControlController) per eseguire l’operazione richiesta, sessionId è necessaria per i commandi che richiedono autenticazione (per i vocali è di default system.sessionId).

**Classe LoginForm**
```
class LoginForm implements Observer is
    // Attributi
    user: User // riferimento all'utente da autenticare o registrare
    controller: AuthController // riferimento al controller
    ObservableCtx: SystemContext // for the rendering config

    // Metodi
    submitLogin() // chiama controller.onLogin(user: User) e utilizza il risultante sessionId per aggiornare la sessione in SystemContext
    submitLogout() // chiama controller.onLogout()
    submitSignup() // chiama controller.onSignup(user: User)
    recoverPassword(username: string)
    render()
    update(e: Event)
```
// Descrizione: Form/view del Presentation Layer per la gestione dell’autenticazione. Raccoglie le credenziali dall’utente e, al submit, invoca i metodi di login/logout del controller AuthController. Gestisce la visualizzazione di messaggi di errore o conferma.

**Classe AuthController**
```
class AuthController is
    // Attributi
    authService: AuthenticationService // dipendenza verso Application Service

    // Metodi
    onLogin(user: User): sessionId: string // chiama authService.login()
    onLogout(): bool // chiama authService.logout()
    OnRecoverPassword(username: string)
    onSignup(user: User)
    getQr(): string
```
// Descrizione: Controller del Presentation Layer dedicato alla gestione delle operazioni di login e logout. Riceve i dati dalla view di login/logout, effettua una verifica dei campi e li inoltra ad AuthenticationService.

**Classe IoTDeviceMenu**
```
class IoTDeviceForm implements Observer is
    // Attributi
    devices: List<IDevice> // elenco dispositivi trovati
    selected: Device // dispositivo selezionato
    controller: DeviceController // riferimento al controller
    ObservableCtx: SystemContext // for the rendering config

    // Metodi
    scanDevices() // chiama controller.onScanDevices() e ottiene i dispositivi disponibili
    associate() // chiama controller.onAssociateDevice(selectedDevice, sessionId)
    dissociate() // chiama controller.onDissociateDevice(selectedDevice, sessionId)
    turnOffOptimization()
    turnOnOptimization()
    render()
    update(e: Event)
```
// Descrizione: menu del Presentation Layer per la gestione dei dispositivi IoT. Permette all’utente di avviare la scansione, selezionare un dispositivo, associarlo/dissociarlo e visualizzare lo stato.

**Classe IoTDeviceController**
```
class IoTDeviceController is
    // Attributi
    iotService: IoTIntegrationService // dipendenza verso Application Service

    // Metodi
    onScanDevices(): List<Device> // chiama iotService.discoverDevices()
    onAssociateDevice(device: Device, sessionId: string): bool // chiama iotService.associateDevice(device, sessionId)
    onDissociateDevice(device: Device, sessionId: string): bool // chiama iotService.dissociateDevice(device, sessionId)
    onTurnOffOptimization(): bool
    onTurnOnOptimization(): bool
```
// Descrizione: Controller del Presentation Layer che gestisce le operazioni di scansione, associazione e dissociazione dispositivi IoT. Riceve le azioni dal form/menu e le inoltra al servizio di integrazione IoT.

**Classe VoiceCommandMenu**
```
class VoiceCommandMenu implements Observer is
    // Attributi
    controller: VoiceCommandController // riferimento al controller
    ObservableCtx: SystemContext // for the rendering config

    // Metodi
    activateVoiceCommands() // chiama controller.onActivateVoiceCommands(system.sessionId)
    disableVoiceCommands() // chiama controller.onDisableVoiceCommands()
    render()
    update(e: Event)
```
// Descrizione: Menu/view del Presentation Layer che offre all’utente la possibilità di attivare o disattivare i comandi vocali tramite la UI. Interagisce con VoiceCommandController per inoltrare le richieste e visualizzare lo stato corrente.

**Classe VoiceCommandController**
```
class VoiceCommandController is
    // Attributi
    voiceInterpreter: VoiceCommandInterpreter // dipendenza verso Application Layer

    // Metodi
    onActivateVoiceCommands(sessionId: string) // chiama voiceInterpreter.activateVoiceCommands(sessionId)
    onDisableVoiceCommands() // chiama voiceInterpreter.disableVoiceCommands()
```
// Descrizione: Controller del Presentation Layer che riceve le richieste di attivazione/disattivazione dei comandi vocali dalla UI, effettua eventuali validazioni e invoca i metodi appropriati su VoiceCommandInterpreter.

**Classe ResetMenu**
```
class ResetMenu implements Observer is
    // Attributi
    controller: ResetController // riferimento al controller
    ObservableCtx: SystemContext // for the rendering config

    // Metodi
    render()
    reset() // chiama controller.onResetLavatrice(sessionId)
    update(e:Event)
```
// Descrizione: View/menu del Presentation Layer che offre all’utente la possibilità di avviare la procedura di reset, visualizza lo stato e l’esito dell’operazione.

**Classe ResetController**
```
class ResetController is
    // Attributi
    resetHandler: ResetHandler // dipendenza verso Application Layer

    // Metodi
    onReset(sessionId: string) // chiama resetHandler.resetLavatrice(sessionId)
```
// Descrizione: Controller del Presentation Layer che riceve la richiesta di reset dalla view/menu, effettua eventuali validazioni (es. sessione utente), inoltra la richiesta al ResetHandler.

**Classe DiagnosticMenu**
```
class DiagnosticMenu implements Observer is
    // Attributi
    controller: DiagnosticController // riferimento al controller
    ObservableCtx: SystemContext // for the rendering config
    lastReport: DiagnosticReport // ultimo report diagnostico visualizzato

    // Metodi
    render()
    startDiagnostica() // chiama controller.onStartDiagnostica(sessionId)
    update(e: Event)
```
// Descrizione: View/menu del Presentation Layer che permette all’utente di avviare la diagnostica e visualizzare i risultati.

**Classe DiagnosticController**
```
class DiagnosticController is
    // Attributi
    diagnosticHandler: DiagnosticHandler // dipendenza verso Application Layer

    // Metodi
    onStartDiagnostica(sessionId: string): DiagnosticReport // chiama diagnosticHandler.execute(sessionId)
    onGetLastReport(): DiagnosticReport
```
// Descrizione: Controller del Presentation Layer che riceve le richieste dalla view/menu, effettua eventuali validazioni e inoltra la richiesta all’application layer.

**Classe NotificationMenu**
```
class NotificationMenu implements Observer is
    // Attributi
    controller: NotificationController // riferimento al controller
    Notifications: List<Notification> // lista notifiche da visualizzare
    conf: SystemContext // for the rendering config

    // Metodi
    render()
    open(id: string) chiama controller.onGet(id)
    markAsRead(id: string) // chiama controller.onMarkAsRead(id)
    delete(id: string) // chiama controller.onDeleteNotification(id)
    clearAll()
    update(e: Event)
```
// Descrizione: View/menu del Presentation Layer che mostra la lista delle notifiche all’utente, permette di segnarle come lette o eliminarle. Interagisce con NotificationController per tutte le operazioni.

**Classe NotificationController**
```
class NotificationController is
    // Attributi
    notificationService: NotificationService // dipendenza verso Application Service

    // Metodi
    onGet(id:string): Notification 
    onGetAll(): List<Notification> // chiama notificationService.getAll()
    onMarkAsRead(id: string) // chiama notificationService.get(id).markAsRead()
    onDelete(id: string) // chiama notificationService.delete(id)
    onClearAll() // chiama notificationService.clearAll()
```
// Descrizione: Controller del Presentation Layer che gestisce la logica di presentazione delle notifiche. Riceve le richieste dalla view/menu e le inoltra a NotificationService.

**Classe ConfigurationMenu**
```
class ConfigurationMenu implements Observer is
    // Attributi
    controller: UIConfigurationController // riferimento al controller
    ObservableCtx: SystemContext // configurazione corrente (singleton)
    options: map<string, string>
    page: int // pagina corrente del menu (es. 1 list all options, 2 change font size)

    // Metodi
    render() // mostra le opzioni di personalizzazione
    saveConfig() // chiama controller.onSaveConfig(options)
    resetToDefaults() // chiama controller.onResetConfig()
    enableVoiceCommands() // chiama controller.onEnableVoiceCommands(sessionId)
    disableVoiceCommands() // chiama controller.onDisableVoiceCommands(sessionId)
    update(e: Event)
```
// Descrizione: Menu/view del Presentation Layer che permette all’utente di personalizzare la configurazione UI (tema, font, contrasto, ecc.) salvare o resettare ai valori di default. Interagisce con UIConfigurationController per tutte le operazioni.

**Classe ConfigurationController**
```
class ConfigurationController is
    // Attributi
    ctx: SystemContext // dipendenza verso Application Layer

    // Metodi
    onSaveConfig(options: map<string, string>, sessionId: string) // valida e aggiorna la configurazione
    onEnableVoiceCommands(sessionId: string) // abilita i comandi vocali nel SystemContext
    onDisableVoiceCommands(sessionId: string) // disabilita i comandi vocali nel SystemContext
    onReset() // resetta ai valori di default
```
// Descrizione: Controller del Presentation Layer che riceve le richieste di personalizzazione dalla UI, valida i dati, aggiorna il UIConfiguration del singleton SystemContext

---

## Application Layer

**Classe UIConfiguration**
```
class UIConfiguration is
    // Attributi
    darkMode: bool // dark | light
    fontSize: int // es. 12..36
    highContrast: bool // normal | high
    highReadabilityFont: bool
    colorBlindMode: bool

    // Metodi
    // setter and getter impliciti per ogni parametro
    static resetToDefaults()
```
// Descrizione: rappresenta la configurazione dell'interfaccia (tema, dimensione font, contrasto, lingua, accessibilità).

**classe SystemContext**
```
class SystemContext is
    // utilizza il sigleton pattern
    static instance: SystemContext
    // Attributi
    auth: AuthenticationService
    config: UIConfiguration
    storage: StorageInterface
    observers: List<Observer>
    sessionId: string
    currentUser: User
    hasUnreadNotifications: bool
    voiceCommandsEnabled: bool

    // Metodi
    static getInstance(): SystemContext
    register(o: Observer)
    unregister(o: Observer)
    notifyObservers()
    resetUIConf()
    saveInStorage()
    // setter and getter impliciti con parametro sessionId
```

// Descrizione: Oggetto (singleton) che rappresenta il contesto globale del sistema, inclusa la sessione utente corrente e la configurazione UI attiva.

**Interface Observer**
```
interface Observer is
    // Metodi
    update(event: Event)
```
// Descrizione: Interfaccia per gli observer che vogliono essere notificati di eventi specifici nel sistema.

**class Event**
```
class Event is
    // Attributi
    type: string // tipo di evento
    data: map<string, any> // dati associati all'evento
```
// Descrizione: Rappresenta un evento generico nel sistema, con un tipo e dati associati.

**Classe servizioPianificazioneCicli**
```
class WashPlanningService  is
    // Attributi
    scheduler: Scheduler // associazione/collaborazione
    notification: NotificationService // notifica il risultato della pianifica
    catalog: List<PianoLavaggio> // catalogo piani di lavaggio disponibili
    storage: IStorage // per salvare/caricare le pianificazioni

    // Metodi
    pianificaLavaggio(piano: PianoLavaggio, dataOra: DateTime)
    getCatalog(): List<PianoLavaggio>
    saveInStorage() // salva le pianificazioni nella memoria di massa
```
// Descrizione: Application Service che espone le operazioni di pianificazione ciclo verso il Presentation Layer. Si occupa di validare i parametri ricevuti dal Presentation Layer, creare oggetti PianoLavaggio e Schedule, e aggiungere la pianificazione allo Scheduler.

**Classe Scheduler**
```
class Scheduler
    // Attributi
    schedules: List<Schedule>
    manager: WashingManager // associazione/collaborazione
    storage: StorageInterface // per salvare le pianificazioni

    // Metodi
    validaSchedule(sch: schedule);
    aggiungiSchedule(newShedule: schedule)
    onTick(currentTime: DateTime) // reazione allo scorrere del tempo chiamato dal Clock ad ogni
    saveInStorage() // salva le pianificazioni nella memoria di massa
```
// Descrizione: Si occupa della pianificazione e gestione temporale dei cicli di lavaggio. Mantiene la lista delle pianificazioni (Schedule), riceve eventi temporali (onTick) e, quando necessario, delega l'esecuzione dei cicli al WashingManager. Garantisce la separazione tra logica di scheduling e logica di esecuzione.
onTock incova manager.avviaLavaggio(piano)

**Classe Schedule**
```
class Schedule is
    // Attributi
    dataOra: DateTime
    piano: PianoLavaggio
    stato: StatoSchedule // es: pianificato, in esecuzione, completato, annullato
```

**Classe PianoLavaggio**
```
class PianoLavaggio is
    // Attributi
    nome: String 
    temperatura: int 
    durata: int
    velocitaCentrifuga: int
    eco: bool
    // altri parametri opzionali: prelavaggio, extra risciacquo, ecc.
```


**Classe Gestore lavaggio**
```
class WashingManager is
    // Attributi
    stato: StatoLavaggio // es: in esecuzione, in pausa, completato, errore
    piano: PianoLavaggio
    hardware: LavatriceHardwareInterface
    notification: NotificationService // to push notification when needed
    storage: StorageInterface // per salvare lo stato del lavaggio in corso

    // Metodi
    avviaLavaggio(piano: PianoLavaggio)
    pausaLavaggio()
    riprendiLavaggio()
    annullaLavaggio()
    gestisciErrore(errore: ErroreLavatrice)
    aggiornaStato()
    getState()
    optimizeCycle(iotData: string)
    getTaskProgress(): Int 0 to 100
    saveInStorage() // salva lo stato corrente e il piano del lavaggio in corso
```
// Descrizione: Gestisce l'esecuzione delle fasi del ciclo di lavaggio, interfacciandosi con l'Hardware Layer tramite LavatriceHardwareInterface. Riceve richieste da Scheduler/Schedule e invia comandi all'hardware. Gestisce feedback/eventi dall'hardware (fine fase, errori, ecc.).
Inotre si comporta come Application Service che espone le operazioni di controllo del ciclo di lavaggio di lavaggio in corso (pausa, riprendi, annulla) verso il Presentation Layer. Riceve le richieste dal livello superiore per il controllo del lavaggio in cosrso.

**Classe VoiceCommandInterpreter**
```
class VoiceCommandInterpreter is
    // Attributi
    dispatcher: CommandDispatcher // riferimento al dispatcher del Presentation Layer
    authService: AuthenticationService // dipendenza per verifica autenticazione
    ctx: SystemContext // riferimento al contesto di sistema

    // Metodi
    interpretaComandoVocale(comando: string) // dispatcher.dispatch() only if ctx.voiceCommandsEnabled=true and authService.verifySession(system.sessionId)=true
    onVoiceSignal(comando: string)
```
// Descrizione: Interpreta il segnale vocale ricevuto dal Hardware Layer tramite VoiceSignalReceiver, analizza il segnale e lo trasforma in una commando da inoltrare al Presentation Layer tramite il dispatcher. Ora verifica l'autenticazione prima di attivare i comandi vocali.

**Class RemoteCommandInterpreter**
```
class RemoteCommandInterpreter is
    // Attributi
    dispatcher: CommandDispatcher // riferimento al dispatcher Presentation Layer

    // Metodi
    interpretCommand(cmd: string)
    forwardToDispatcher(cmd: string)
```
// Descrizione: Gestisce la comunicazione con dispositivi remoti di controllo (app mobile, smart speaker, ecc.). interpreta i commandi ricevuti e li inoltra al dispatcher del Presentation Layer. Si appoggia a RemoteControlManager per la gestione e verifica dei dispositivi di controllo. Non gestisce dispositivi IoT (che sono gestiti da IoTIntegrationService).

**Class RemoteControlManager**
```
class RemoteControlManager is
    // Attributi
    decices: list<Device> // device remoti connessi
    network: NetworkInterface // associazione con Hardware Layer
    auth: AuthenticationService // per autenticare i device remoti
    interpreter: RemoteCommandInterpreter // riferimento per la gestione dei dispositivi di controllo
    storage: IStorage // per salvare/caricare i dispositivi remoti

    // Metodi
    accept(device: Device)
    remove(device: Device)
    listen() // start connection
    handleCommunication(device: Device) // interpreter.interpretCommand(smd) e verifica l'auth del device
    broadcast(data: string) // send data to all connected devices 
    saveDevicesInStorage() // salva i dispositivi remoti nella memoria di massa
```
// RemoteControlManager gestisce la registrazione, autenticazione (tramite AuthenticationService) e comunicazione con i dispositivi di controllo remoto (ad esempio app mobile, smart speaker, tablet, ecc.) che interagiscono con la lavatrice intelligente.
gestire la comunicazione, ascoltare nuove connessioni e inviare dati a tutti i dispositivi remoti associati.

**Class IoTIntegrationService**
```
class IoTIntegrationService is
    // Attributi
    network: NetworkInterface // associazione con Hardware Layer
    devices: List<Device> // dispositivi IoT associati
    washingManager: WashingManager // associazione per ottimizzazione
    NotificationService // to push notification when needed
    auth: AuthenticationService // per autenticare i dispositivi IoT
    optimization: bool // attiva/disattiva
    storage: IStorage // per salvare/caricare le impostazioni di ottimizzazione e i dispositivi associati

    // Metodi
    discoverDevices(): List<Device>
    associateDevice(device: Device, sessionId: string): bool
    dissociateDevice(device: Device, sessionId: string): bool
    receiveData(device: Device): string
    processIoTData(iotData: string)
    turnOffOptimization()
    turnOnOptimization()
    saveInStorage() // salva i dispositivi associati e lo stato di ottimizzazione nella memoria di massa
```
// Descrizione: Classe dell'Application Layer che gestisce l'integrazione e la comunicazione con dispositivi IoT esterni (es. termostati, contatore intelligente). Utilizza NetworkInterface per la comunicazione di rete, mantiene la lista dei dispositivi associati, riceve dati e invia comandi. Il metodo processIoTData interpreta i dati ricevuti dai dispositivi IoT e invoca washingManager.optimizeCycle(iotData) per ottimizzare il ciclo di lavaggio in base alle informazioni raccolte.

**classe device**
```
classe Device is
    id: string // UUID o MAC
    ipAddress: string
    port: int // porta di comunicazione
    nome: string
    tipo: string // IoT, mobile device ecc.
```
// Descrizione: rappresenta un generico dispositivo che può essere un IoT integrabile con la lavatrice (es. termostato, contatore intelligente, sensore ambiente) o un device mobile per il controllo remoto ecc.

**Class AuthenticationService**
```
class AuthenticationService is
    // Attributi
    sessions: List<Session> // gestione multi-sessione per utenti/dispositivi
    storage: IStorage // per salvare/caricare le sessioni attive
    users: List<User> // elenco utenti registrati
    network: NetworkInterface // recezione del one-time token del QR code da app mobile

    // Metodi
    login(user: User): sessionId: string
    logout(): void
    verifySession(idSession: string): bool
    refreshSession(idSession: string): Session
    recoverPassword(username: string)
    isDeviceAuthenticated(deviceId: string): bool
    loginWithQR(token: string): bool
    generateLoginQR(): string // one-time token, l’app (già autenticata) invia userId al backend dopo la scansione.
    saveInStorage() // salva le sessioni attive e gli utenti registrati nella memoria di massa
    signup(user: User)
```
// Descrizione: Application Service che gestisce l’autenticazione locale di utenti e dispositivi. Espone operazioni di login/logout e di verifica, verifica e rinnovo della sessione locale.
// Il sistema attuale è progettato per autenticazione locale e gestione multi-sessione (lista di Session), per un sistemi chiusi.
La lavatrice mantiene lo stato delle sessioni attive (Session), può invalidare una sessione, gestire login/logout in modo diretto.
I dispositivi IoT e remoti inviano la sessione (idSession della sezione), che viene validata localmente.
loginWithQR: Si occupa di validare il QR code, estrarre le credenziali o il token, e autenticare l’utente.

**Class Session**
```
class Session is
    // Attributi
    sessionId: string
    userId: string
    deviceId: string
    createdAt: DateTime
    expiresAt: DateTime
    isActive: bool

    // Metodi
    isValid(): bool
    invalidate(): void
```
// Descrizione: Rappresenta una sessione di autenticazione attiva per utente o dispositivo. Gestisce scadenza, stato della sessione autenticata. userId identificativo dell’utente autenticato a cui è associata la sessione. deviceId rappresenta l’identificativo del dispositivo che ha avviato la sessione, permette alla lavatrice di essere controllata da più dispositivi (es. app mobile).

**Class User**
```
class User is
    // Attributi
    userId: string
    username: string
    passwordHash: string
    email: string
    language: string
    imgProfile: string // path immagine profilo
```
// Descrizione: Rappresenta un utente del sistema con credenziali..

**Classe Gestore reset lavatrice**
```
class ResetHandler is
    // Attributi
    hardware: LavatriceHardwareInterface // dipendenza verso Hardware Layer
    authService: AuthenticationService // dipendenza per verifica autenticazione
    notification: NotificationService // to push notification when needed

    // Metodi
    resetLavatrice(sessionId: String) // verifica autenticazione tramite authService.verifySessio (sessionId) prima di eseguire il reset, chima.notification() 
```
// Descrizione: Application Service dedicato alla gestione della logica di reset della lavatrice. Riceve richieste dal controller/menu, verifica l'autenticazione tramite AuthenticationService, coordina le operazioni di reset (interruzione operazioni, scarico acqua, azzeramento errori), comunica con l’interfaccia hardware e notifica l’esito.

**Classe DiagnosticHandler**
```
class DiagnosticHandler is
    // Attributi
    hardware: LavatriceHardwareInterface // dipendenza verso Hardware Layer
    authService: AuthenticationService // dipendenza per verifica autenticazione
    notification: NotificationService // push notification when needed
    lastReport: DiagnosticReport
    storage: IStorage // per salvare/caricare i report di diagnostica

    // Metodi
    execute(sessionId: string): DiagnosticReport // verifica autenticazione, interroga hardware, costruisce report
    saveLastReportInStorage() // salva l'ultimo report nella memoria di massa
```
// Descrizione: Application Service che implementa la logica di diagnostica, interagisce con l’hardware, raccoglie i risultati e li restituisce al controller/menu.

**Classe DiagnosticReport**
```
class DiagnosticReport is
    // Attributi
    esito: boolean
    errori: List<ErroreLavatrice>
```
// Descrizione: Oggetto che rappresenta il risultato della diagnostica (esito, eventuali errori rilevati).

**Classe Notification**
```
class Notification is
    // Attributi
    id: string
    tipo: string // info, warning, error
    messaggio: string
    dataOra: DateTime
    stato: string // letta/non letta

    // Metodi
    markAsRead()
```
// Descrizione: Rappresenta una notifica generata dal sistema (evento, errore, stato, ecc.) e destinata all’utente. Può essere visualizzata su display, app mobile, ecc.

**Classe NotificationService**
```
class NotificationService is
    // Attributi
    notifications: List<Notification> // notifiche disponibili
    remote: RemoteControlManager
    global: SystemContext // per notificare la presenza di nuove notifiche
    storage: IStorage // per salvare/caricare le notifiche

    // Metodi
    get(id: string): Notification
    getAll(): List<Notification>
    delete(id: string)
    clearAll()
    saveNotificationInStorage() // salva le notifiche nella memoria di massa
```
// Descrizione: Service centralizzato che gestisce la creazione, memorizzazione e invio delle notifiche agli utenti. Può essere invocato da WashingManager, DiagnosticHandler, ResetHandler, ecc.

// Relazioni principali:
// - WashingManager, DiagnosticHandler, ResetHandler -> NotificationService (invocano push)
// - NotificationService -> Notification (gestisce la lista)
// - Presentation Layer (UI/Menu) → NotificationService (recupera notifiche per l’utente)

---

## Hardware Abstraction Layer

**Interfaccia Clock**
```
interface IClock is
    // Metodi
    notifyOnTick() // notifica l'evento
```
// Descrizione: Rappresenta l'orologio/logica temporale del sistema (Hardware Layer). Permette notificare periodicamente gli eventi di avanzamento temporale (tick) tramite il metodo onTick, allo scheduler.

**Interfaccia LavatriceHardware**
```
interface ILavatriceHardware is
    // Metodi
    avviaMotore()
    fermaMotore()
    apriValvolaAcqua()
    chiudiValvolaAcqua()
    attivaCentrifuga()
    arrestaCentrifuga()
    reset() // esegue il reset hardware
    // ...altri metodi per interagire con l'hardware...
```
// Descrizione: Espone i comandi per il controllo diretto dell'hardware della lavatrice. Utilizzato da WashingManager.

**Interfaccia VoiceInputDevice**
```
interface VoiceInputDevice is
    // Metodi
    notifySignalReceived()
```
// Descrizione: Interfaccia dell'Hardware Layer che gestisce la ricezione di segnali vocali dal microfono o modulo di riconoscimento. Quando riceve un comando vocale, lo inoltra al VoiceCommandInterpreter (dell'Application Layer).

**Interfaccia NetworkInterface**
```
interface NetworkInterface is
    // Metodi
    connect(config: NetworkConfig)
    bind(config: NetworkConfig)
    disconnect()
    sendData(data: NetworkPacket)
    receiveData(): NetworkPacket
    broadcast(data: NetworkPacket)
```
// Descrizione: Interfaccia dell'Hardware Layer che astrae la connettività di rete (Wi-Fi, Ethernet, ecc.). Permette alla lavatrice di connettersi a una rete, inviare/ricevere dati (per IoT, controllo remoto, aggiornamenti, ecc.). Implementata da moduli hardware specifici.

**Interfaccia StorageInterface**
```
interface IStorage is
    // Metodi
    read(address: int, length: int): byte[]
    write(address: int, data: byte[]): bool
```
// Descrizione: Interfaccia dell'Hardware Layer che astrae l'accesso alla memoria di massa (es. memoria flash, EEPROM, SD card). Permette di leggere e scrivere dati persistenti (configurazioni, ecc.). Implementata da moduli hardware specifici.

### Relazioni

**Relazioni principali:**
- Scheduler *contiene* una lista di Schedule (aggregazione).
- Schedule *contiene* un PianoLavaggio (associazione).
- Clock (Hardware Layer) *notifica* gli observer (Scheduler) tramite onTick.
- VoiceInputDevice *notifica* gli observer (VoiceCommandInterpreter) tramite onVoiceSignal.
- Scheduler *collabora* con WashingManager per l'avvio dei cicli (associazione).
- WashingManager *utilizza* LavatriceHardwareInterface per comandare l'hardware (associazione).
- WashingManager *contiene* un PianoLavaggio (associazione).
- WashPlanningService *implementa* IWashPlanningService.
- WashPlanningService *contiene* Scheduler per l'aggiunta di nuovi schedule.
- WashPlanningController *utilizza* IWashPlanningService per la pianificazione dei cicli (dipendenza Presentation → Application Layer).
- WashPlanningForm *utilizza* WashPlanningController per inviare i dati di pianificazione (dipendenza Presentation → Controller).
- WashStartForm *utilizza* WashPlanningController per avviare subito il ciclo (dipendenza Presentation → Controller).
- WashControlController *utilizza* WashingManager per il controllo ciclo (dipendenza Presentation → Application Layer).
- WashControlMenu *utilizza* WashControlController per inviare i comandi di controllo (dipendenza Presentation → Controller).
- VoiceCommandInterpreter *invia* il commando intepretato a CommandDispatcher (Application → Presentation Layer): il risultato dell'interpretazione viene passato al dispatcher che lo inoltra al controller appropriato.
- RemoteCommandInterpreter *utilizza* NetworkInterface ((associazione))
- RemoteCommandInterpreter *inotra* comandi a CommandDispatcher (associazione)
- CommandDispatcher *inotra* a WashControlController, WashPlanningController, etc. (associazione)
- IoTIntegrationService *utilizza* NetworkInterface (associazione)
- IoTIntegrationService *inoltra a* WashingManager (associazione)
- IoTIntegrationService *gestisce* Device (aggregazione)
- AuthenticationService *crea e gestisce* Session (composizione)
- AuthenticationService *utilizza* NetworkInterface (associazione)
- RemoteCommandInterpreter *utilizza* AuthenticationService per autenticare comandi remoti (associazione)
- IoTIntegrationService *utilizza* AuthenticationService per autenticare dispositivi IoT (associazione)
- AuthController (associato a) AuthenticationService
- LoginForm (associato a) AuthController
- IoTDeviceMenu (associato a) IoTDeviceController 
- IoTDeviceController (associato a) IoTIntegrationService
- VoiceCommandInterpreter (associato a) AuthenticationService
- VoiceCommandController (associato a) voiceInterpreter
- VoiceCommandMenu (associato a) VoiceCommandController
- ResetMenu (associato a) ResetController
- ResetController (associato a) ResetHandler
- ResetHandler (associato a) LavatriceHardwareInterface
- ResetHandler (associato a) AuthenticationService
- DiagnosticController (associato a) DiagnosticHandler
- DiagnosticMenu (associato a) DiagnosticController
- DiagnosticController (associato a) AuthenticationService
- DiagnosticController (associato a) LavatriceHardwareInterface
- RemoteControlManager *gestisce* Device (aggregazione)
- RemoteControlManager *utilizza* RemoteCommandInterpreter (associazione)

**Dependency**:
- WashPlanningService 'utilizza' PianoLavaggio per la creazione di nuovo Piano
- WashPlanningService 'utilizza' Schedule per la creazione di nuovo Schedule