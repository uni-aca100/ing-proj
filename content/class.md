# Bozze per Dia. classi UML

## Presentation Layer

**Classe WashPlanningForm**
```
class WashPlanningForm is
    // Attributi
    parametriPiano: map<string, string> // input utente (nome, temperatura, durata, ecc.)
    dataOra: DateTime // data/ora pianificazione
    controller: WashPlanningController // riferimento al controller

    // Metodi
    submit()  // chiama controller.onPianificaLavaggio(parametriPiano, now)
    render()
```
// Descrizione: Form/view del Presentation Layer per la pianificazione di un ciclo (rappresenta la UI per la raccolta dei dati di pianificazione dal’utente). Raccoglie i dati dall’utente e, al submit, invoca il metodo onPianificaLavaggio del controller passando i parametri inseriti.

**Classe WashStartForm**
```
class WashStartForm is
    // Attributi
    parametriPiano: map<string, string> // con alcuni campi precompilati (eventualmente nascosti)
    dataOra: DateTime = now // campo nascosto o preimpostato all'istante corrente
    controller: WashPlanningController // riferimento al controller

    // Metodi
    submit() // chiama controller.onPianificaLavaggio(parametriPiano, now)
    render()
```
// Descrizione: Form/view specializzato per l'avvio immediato del lavaggio (rappresenta la UI per la raccolta dei dati del piano di lavaggio dal’utente). Al submit invia i parametri e la data/ora corrente al controller tramite onPianificaLavaggio.

**Classe WashPlanningController**
```
class WashPlanningController is
    // Attributi
    washPlanningService: WashPlanningService // dipendenza verso Application Service

    // Metodi
    onPianificaLavaggio(parametriPiano: map<string, string>, dataOra: DateTime)
```
// Descrizione: Controller "sottile" (non contiene logica di business, delegata all'application Service) del Presentation Layer. che riceve i dati dal form/view per la pianificazione di cicli di lavaggio, ed un eventuale validazione lato presentazione (es. campi mancati). Gestisce l'iterazione con WashPlanningService (dell'application layer)

**Classe WashControlMenu**
```
class WashControlMenu is
    // Attributi
    controller: WashControlController // riferimento al controller

    // Metodi
    render()
    inviaComandoPausa() // chiama eventualmente controller.onPausaLavaggio()
    inviaComandoRiprendi() // chiama eventualmente controller.onriPrendiLavaggio()
    inviaComandoAnnulla() // chiama eventualmente controller.onPausaLavaggio()
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
class LoginForm is
    // Attributi
    username: string
    password: string
    controller: AuthController // riferimento al controller

    // Metodi
    submitLogin() // chiama controller.onLogin(username, password)
    submitLogout() // chiama controller.onLogout()
    recoverPassword(username: string)
    render()
```
// Descrizione: Form/view del Presentation Layer per la gestione dell’autenticazione. Raccoglie le credenziali dall’utente e, al submit, invoca i metodi di login/logout del controller AuthController. Gestisce la visualizzazione di messaggi di errore o conferma.

**Classe AuthController**
```
class AuthController is
    // Attributi
    authService: AuthenticationService // dipendenza verso Application Service

    // Metodi
    onLogin(username: string, password: string): bool // chiama authService.login()
    onLogout(): bool // chiama authService.logout()
    OnRecoverPassword(username: string)
    getQr(): string
```
// Descrizione: Controller del Presentation Layer dedicato alla gestione delle operazioni di login e logout. Riceve i dati dalla view di login/logout, effettua una verifica dei campi e li inoltra ad AuthenticationService.

**Classe IoTDeviceMenu**
```
class IoTDeviceForm is
    // Attributi
    devices: List<IDevice> // elenco dispositivi trovati
    selected: Device // dispositivo selezionato
    controller: DeviceController // riferimento al controller

    // Metodi
    scanDevices() // chiama controller.onScanDevices() e ottiene i dispositivi disponibili
    selectDevice(device: Device) // seleziona dispositivo
    submitAssociate() // chiama controller.onAssociateDevice(selectedDevice)
    submitDissociate() // chiama controller.onDissociateDevice(selectedDevice)
    turnOffOptimization()
    turnOnOptimization()
    render()
```
// Descrizione: menu del Presentation Layer per la gestione dei dispositivi IoT. Permette all’utente di avviare la scansione, selezionare un dispositivo, associarlo/dissociarlo e visualizzare lo stato.

**Classe IoTDeviceController**
```
class IoTDeviceController is
    // Attributi
    iotService: IoTIntegrationService // dipendenza verso Application Service

    // Metodi
    onScanDevices(): List<Device> // chiama iotService.discoverDevices()
    onAssociateDevice(device: Device): bool // chiama iotService.associateDevice(device)
    onDissociateDevice(device: Device): bool // chiama iotService.dissociateDevice(device)
    onTurnOffOptimization(): bool
    onTurnOnOptimization(): bool
```
// Descrizione: Controller del Presentation Layer che gestisce le operazioni di scansione, associazione e dissociazione dispositivi IoT. Riceve le azioni dal form/menu e le inoltra al servizio di integrazione IoT.

**Classe VoiceCommandMenu**
```
class VoiceCommandMenu is
    // Attributi
    controller: VoiceCommandController // riferimento al controller

    // Metodi
    activateVoiceCommands() // chiama controller.onActivateVoiceCommands(system.sessionId)
    disableVoiceCommands() // chiama controller.onDisableVoiceCommands()
    render()
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
class ResetMenu is
    // Attributi
    controller: ResetController // riferimento al controller

    // Metodi
    render()
    reset() // chiama controller.onResetLavatrice(sessionId)
    checkMachineStatus() // controller.onCheckMachineStatus()
    renderResetResult(esito: boolean, dettagli: string) // incovato da reset() al termine del reset 
```
// Descrizione: View/menu del Presentation Layer che offre all’utente la possibilità di avviare la procedura di reset, visualizza lo stato e l’esito dell’operazione.

**Classe ResetController**
```
class ResetController is
    // Attributi
    resetHandler: ResetHandler // dipendenza verso Application Layer

    // Metodi
    onReset(sessionId: string) // chiama resetHandler.resetLavatrice(sessionId)
    onCheckMachineStatus() // chiama resetHandler.verificaStatoLavatrice()
```
// Descrizione: Controller del Presentation Layer che riceve la richiesta di reset dalla view/menu, effettua eventuali validazioni (es. sessione utente), inoltra la richiesta al ResetHandler.

**Classe DiagnosticMenu**
```
class DiagnosticMenu is
    // Attributi
    controller: DiagnosticController // riferimento al controller

    // Metodi
    render()
    startDiagnostica() // chiama controller.onAvviaDiagnostica(sessionId)
    renderReport(report: DiagnosticReport) // chimata al termine di avviaDiagnostica()
```
// Descrizione: View/menu del Presentation Layer che permette all’utente di avviare la diagnostica e visualizzare i risultati.

**Classe DiagnosticController**
```
class DiagnosticController is
    // Attributi
    diagnosticHandler: DiagnosticHandler // dipendenza verso Application Layer

    // Metodi
    onStartDiagnostica(sessionId: string): DiagnosticReport // chiama diagnosticHandler.execute(sessionId)
```
// Descrizione: Controller del Presentation Layer che riceve le richieste dalla view/menu, effettua eventuali validazioni e inoltra la richiesta all’application layer.

---

## Application Layer

**Classe servizioPianificazioneCicli**
```
class WashPlanningService  is
    // Attributi
    scheduler: Scheduler // associazione/collaborazione

    // Metodi
    validaParametri(parametriPiano: map<string, string>) 
    pianificaLavaggio(parametriPiano: map<string, string>, dataOra: DateTime)
```
// Descrizione: Application Service che espone le operazioni di pianificazione ciclo verso il Presentation Layer. Si occupa di validare i parametri ricevuti dal Presentation Layer, creare oggetti PianoLavaggio e Schedule, e aggiungere la pianificazione allo Scheduler.

**Classe Scheduler**
```
class Scheduler implements ClockObserver is
    // Attributi
    schedules: List<Schedule>
    manager: WashingManager // associazione/collaborazione

    // Metodi
    validaSchedule(sch: schedule);
    aggiungiSchedule(newShedule: schedule)
    avviaCiclo(piano: PianoLavaggio) // invoca manager.avviaCiclo()
    onTick(currentTime: DateTime) // reazione allo scorrere del tempo chiamato dal Clock ad ogni tick
```
// Descrizione: Si occupa della pianificazione e gestione temporale dei cicli di lavaggio. Mantiene la lista delle pianificazioni (Schedule), riceve eventi temporali (onTick) e, quando necessario, delega l'esecuzione dei cicli al WashingManager. Garantisce la separazione tra logica di scheduling e logica di esecuzione.

**Classe Schedule**
```
class Schedule is
    // Attributi
    dataOra: DateTime
    piano: PianoLavaggio
    stato: StatoSchedule // es: pianificato, in esecuzione, completato, annullato

    // Metodi
    aggiornaDataOra(nuovaDataOra): DateTime;
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

    // Metodi principali
    validaParametri()
```


**Classe Gestore lavaggio**
```
class WashingManager is
    // Attributi
    stato: StatoLavaggio // es: in esecuzione, in pausa, completato, errore
    piano: PianoLavaggio
    hardware: LavatriceHardwareInterface

    // Metodi
    avviaLavaggio(piano: PianoLavaggio)
    pausaLavaggio()
    riprendiLavaggio()
    annullaLavaggio()
    gestisciErrore(errore: ErroreLavatrice)
    aggiornaStato()
    getState()
    optimizeCycle(iotData: string)
```
// Descrizione: Gestisce l'esecuzione delle fasi del ciclo di lavaggio, interfacciandosi con l'Hardware Layer tramite LavatriceHardwareInterface. Riceve richieste da Scheduler/Schedule e invia comandi all'hardware. Gestisce feedback/eventi dall'hardware (fine fase, errori, ecc.).
Inotre si comporta come Application Service che espone le operazioni di controllo del ciclo di lavaggio di lavaggio in corso (pausa, riprendi, annulla) verso il Presentation Layer. Riceve le richieste dal livello superiore per il controllo del lavaggio in cosrso.

**Classe VoiceCommandInterpreter**
```
class VoiceCommandInterpreter is
    // Attributi
    dispatcher: CommandDispatcher // riferimento al dispatcher del Presentation Layer
    authService: AuthenticationService // dipendenza per verifica autenticazione

    // Metodi
    interpretaComandoVocale(comando: string) // dispatcher.dispatch()
    onVoiceSignal(comando: string)
    disableVoiceCommands()
    activateVoiceCommands(sessionId: string)
            // verifica autenticazione tramite authService
        if authService.verifySession(sessionId) then
            // attiva comandi vocali
        else
            // operazione negata: utente/dispositivo non autenticato
```
// Descrizione: Interpreta il segnale vocale ricevuto dal Hardware Layer tramite VoiceSignalReceiver, analizza il segnale e lo trasforma in una commando da inoltrare al Presentation Layer tramite il dispatcher. Ora verifica l'autenticazione prima di attivare i comandi vocali.

**Class RemoteCommandManager**
```
class RemoteCommandManager is
    // Attributi
    network: NetworkInterface // associazione con Hardware Layer
    dispatcher: CommandDispatcher // riferimento al dispatcher Presentation Layer
    auth: AuthenticationService // per autenticare comandi remoti

    // Metodi
    receiveRemoteCommand(): string
    interpretCommand(cmd: string)
    forwardToDispatcher(cmd: string)
```
// Descrizione: Classe dell'Application Layer che gestisce la comunicazione con dispositivi remoti (app mobile, cloud, IoT). Utilizza NetworkInterface per ricevere comandi, li interpreta e li inoltra al dispatcher del Presentation Layer, che li smista ai controller appropriati. Gestisce sicurezza e validazione.

**Class IoTIntegrationService**
```
class IoTIntegrationService is
    // Attributi
    network: NetworkInterface // associazione con Hardware Layer
    devices: List<Device> // dispositivi IoT associati
    washingManager: WashingManager // associazione per ottimizzazione
    auth: AuthenticationService // per autenticare dispositivi IoT
    optimization: bool // attiva/disattiva

    // Metodi
    discoverDevices(): List<Device>
    associateDevice(device: Device): bool
    dissociateDevice(device: Device): bool
    receiveData(device: Device): string
    processIoTData(iotData: string)
    turnOffOptimization()
    turnOnOptimization()
```
// Descrizione: Classe dell'Application Layer che gestisce l'integrazione e la comunicazione con dispositivi IoT esterni (es. termostati, contatore intelligente). Utilizza NetworkInterface per la comunicazione di rete, mantiene la lista dei dispositivi associati, riceve dati e invia comandi. Il metodo processIoTData interpreta i dati ricevuti dai dispositivi IoT e invoca washingManager.optimizeCycle(iotData) per ottimizzare il ciclo di lavaggio in base alle informazioni raccolte.

**classe device**
```
classe Device is
    id: string // UUID o MAC
    ipAddress: string
    nome: string
    tipo: string // IoT, mobile device ecc.
```
// Descrizione: rappresenta un generico dispositivo che può essere un IoT integrabile con la lavatrice (es. termostato, contatore intelligente, sensore ambiente) o un device mobile per il controllo remoto ecc.

**Class AuthenticationService**
```
class AuthenticationService is
    // Attributi
    network: NetworkInterface // associazione con Hardware Layer
    sessions: List<Session> // gestione multi-sessione per utenti/dispositivi

    // Metodi
    login(username: string, password: string): bool
    logout(): void
    verifySession(idSession: string): bool
    refreshSession(idSession: string): Session
    recoverPassword(username: string)
    isDeviceAuthenticated(deviceId: string): bool
    loginWithQR(userId: sring, qrCodeData: string): bool
    generateLoginQR(): string // one-time token, l’app (già autenticata) invia userId al backend dopo la scansione.
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

**Classe Gestore reset lavatrice**
```
class ResetHandler is
    // Attributi
    hardware: LavatriceHardwareInterface // dipendenza verso Hardware Layer
    authService: AuthenticationService // dipendenza per verifica autenticazione

    // Metodi
    resetLavatrice(sessionId: String)// verifica autenticazione tramite authService.verifySessio (sessionId) prima di eseguire il reset
    checkStatusMachine(): string // controlla lo stato attuale
    notificaEsitoReset() // notifica l'esito all'utente/sistema 
```
// Descrizione: Application Service dedicato alla gestione della logica di reset della lavatrice. Riceve richieste dal controller/menu, verifica l'autenticazione tramite AuthenticationService, coordina le operazioni di reset (interruzione operazioni, scarico acqua, azzeramento errori), comunica con l’interfaccia hardware e notifica l’esito.

**Classe DiagnosticHandler**
```
class DiagnosticHandler is
    // Attributi
    hardware: LavatriceHardwareInterface // dipendenza verso Hardware Layer
    authService: AuthenticationService // dipendenza per verifica autenticazione

    // Metodi
    execute(sessionId: string): DiagnosticReport // verifica autenticazione, interroga hardware, costruisce report
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
    connect(networkConfig: NetworkConfig)
    disconnect()
    sendData(data: NetworkPacket)
    receiveData(): NetworkPacket
```
// Descrizione: Interfaccia dell'Hardware Layer che astrae la connettività di rete (Wi-Fi, Ethernet, ecc.). Permette alla lavatrice di connettersi a una rete, inviare/ricevere dati (per IoT, controllo remoto, aggiornamenti, ecc.). Implementata da moduli hardware specifici.

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
- RemoteCommandManager *utilizza* NetworkInterface ((associazione))
- RemoteCommandManager *inotra* comandi a CommandDispatcher (associazione)
- CommandDispatcher *inotra* a WashControlController, WashPlanningController, etc. (associazione)
- IoTIntegrationService *utilizza* NetworkInterface (associazione)
- IoTIntegrationService *inoltra a* WashingManager (associazione)
- IoTIntegrationService *gestisce* Device (aggregazione)
- AuthenticationService *crea e gestisce* Session (composizione)
- AuthenticationService *utilizza* NetworkInterface (associazione)
- RemoteCommandManager *utilizza* AuthenticationService per autenticare comandi remoti (associazione)
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

**Dependency**:
- WashPlanningService 'utilizza' PianoLavaggio per la creazione di nuovo Piano
- WashPlanningService 'utilizza' Schedule per la creazione di nuovo Schedule