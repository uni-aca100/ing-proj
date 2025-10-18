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

    // Metodi
    dispatch(voiceCommand: string)
```
// Descrizione: Riceve l’azione vocale/remoto già interpretata dall’Application Layer e chiama il controller appropriato nel Presentation Layer (es. WashPlanningController, WashControlController) per eseguire l’operazione richiesta.

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
```
// Descrizione: Controller del Presentation Layer dedicato alla gestione delle operazioni di login e logout. Riceve i dati dalla view di login/logout, effettua una verifica dei campi e li inoltra ad AuthenticationService.


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
    // Metodi
    interpretaComandoVocale(comando: string) // dispatcher.dispatch()
    onVoiceSignal(comando: string)
```
// Descrizione: Interpreta il segnale vocale ricevuto dal Hardware Layer tramite VoiceSignalReceiver, analizza il segnale e lo trasforma in una commando da inoltrare al Presentation Layer tramite il dispatcher.

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
    devices: List<IoTDevice> // dispositivi IoT associati
    washingManager: WashingManager // associazione per ottimizzazione
    auth: AuthenticationService // per autenticare dispositivi IoT

    // Metodi
    discoverDevices(): IoTDevice
    associateDevice(device: IoTDevice)
    receiveData(device: IoTDevice): string
    processIoTData(iotData: string)
```
// Descrizione: Classe dell'Application Layer che gestisce l'integrazione e la comunicazione con dispositivi IoT esterni (es. termostati, contatore intelligente). Utilizza NetworkInterface per la comunicazione di rete, mantiene la lista dei dispositivi associati, riceve dati e invia comandi. Il metodo processIoTData interpreta i dati ricevuti dai dispositivi IoT e invoca washingManager.optimizeCycle(iotData) per ottimizzare il ciclo di lavaggio in base alle informazioni raccolte.

**classe IoTDevice**
```
classe IoTDevice is
    id: string // UUID o MAC
    ipAddress: string
    nome: string
```
// Descrizione: rappresenta un generico dispositivo IoT integrabile con la lavatrice (es. termostato, contatore intelligente, sensore ambiente).

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
```
// Descrizione: Application Service che gestisce l’autenticazione locale di utenti e dispositivi. Espone operazioni di login/logout e di verifica, verifica e rinnovo della sessione locale.
// Il sistema attuale è progettato per autenticazione locale e gestione multi-sessione (lista di Session), per un sistemi chiusi.
La lavatrice mantiene lo stato delle sessioni attive (Session), può invalidare una sessione, gestire login/logout in modo diretto.
I dispositivi IoT e remoti inviano la sessione (idSession token della sezione), che viene validata localmente.

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
- IoTIntegrationService *gestisce* IoTDevice (aggregazione)
- AuthenticationService *crea e gestisce* Session (composizione)
- AuthenticationService *utilizza* NetworkInterface (associazione)
- RemoteCommandManager *utilizza* AuthenticationService per autenticare comandi remoti (associazione)
- IoTIntegrationService *utilizza* AuthenticationService per autenticare dispositivi IoT (associazione)
- AuthController (associato a) AuthenticationService
- LoginForm (associato a) AuthController

**Dependency**:
- WashPlanningService 'utilizza' PianoLavaggio per la creazione di nuovo Piano
- WashPlanningService 'utilizza' Schedule per la creazione di nuovo Schedule