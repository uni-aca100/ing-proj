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
```
// Descrizione: Form/view specializzato per l'avvio immediato del lavaggio (rappresenta la UI per la raccolta dei dati del piano di lavaggio dal’utente). Al submit invia i parametri e la data/ora corrente al controller tramite onPianificaLavaggio.

**Classe WashPlanningController**
```
class WashPlanningController is
    // Attributi
    WashPlanningService: IWashPlanningService // dipendenza verso Application Service

    // Metodi
    onPianificaLavaggio(parametriPiano: map<string, string>, dataOra: DateTime)
```
// Descrizione: Controller "sottile" (non contiene logica di business, delegata all'application Service) del Presentation Layer. che riceve i dati dal form/view per la pianificazione di cicli di lavaggio, ed un eventuale validazione lato presentazione (es. campi mancati). Gestisce l'iterazione con IWashPlanningService (dell'application layer)

**Classe WashControlMenu**
```
class WashControlMenu is
    // Attributi
    controller: WashControlController // riferimento al controller

    // Metodi
    buttonsRendering() // alternativa rappresentazione separata
    stateRendering()
    inviaComandoPausa() // chiama eventualmente controller.onPausaLavaggio()
    inviaComandoRiprendi() // chiama eventualmente controller.onriPrendiLavaggio()
    inviaComandoAnnulla() // chiama eventualmente controller.onPausaLavaggio()
```
// Descrizione: Menu/view del Presentation Layer che offre all’utente i comandi di controllo del lavaggio (pausa, riprendi, annulla) e visualizza lo stato corrente del lavaggio. Interagisce con WashControlController per inoltrare i comandi.

**Classe WashControlController**
```
class WashControlController is
    // Attributi
    washingControlService: WashingControlService // dipendenza verso Application Service

    // Metodi
    onPausaLavaggio() // chiama eventualmente WashingControlService.pausaLavaggio()()
    onRiprendiLavaggio() // ...
    onAnnullaLavaggio()
    onGetState()
```
// Descrizione: Controller del Presentation Layer che espone le operazioni di controllo ciclo (pausa, riprendi, annulla, stato) verso la UI. Riceve i comandi dalle view/menu e li inoltra al WashingControlService eventualmente dopo un verifica sullo stato corrente.

---

## Application Layer

**Interfaccia servizioPianificazioneCicli**
```
interface IWashPlanningService is
    // Metodi
    pianificaLavaggio(parametriPiano: map<string, string>, dataOra: DateTime)
```
// Descrizione: Application Service che espone le operazioni di pianificazione ciclo verso il Presentation Layer. Riceve i parametri dal livello superiore, li valida e li inoltra all'implementazione concreta.

**Classe servizioPianificazioneCicli**
```
class WashPlanningService implements IWashPlanningService  is
    // Attributi
    scheduler: Scheduler // associazione/collaborazione

    // Metodi
    validaParametri(parametriPiano: map<string, string>) 
    pianificaLavaggio(parametriPiano: map<string, string>, dataOra: DateTime)
```
// Descrizione: Implementa IWashPlanningService. Si occupa di validare i parametri ricevuti dal Presentation Layer, creare oggetti PianoLavaggio e Schedule, e aggiungere la pianificazione allo Scheduler.

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
    onTick(currentTime: DateTime) // reazione allo scorrere del tempo
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

**interfaccia WashingControlService**
```
class WashingControlService is
    // Metodi
    pausaLavaggio()
    riprendiLavaggio()
    annullaLavaggio()
    getState()
```
// Descrizione: Application Service che espone le operazioni di controllo del ciclo di lavaggio di lavaggio in corso (pausa, riprendi, annulla) verso il Presentation Layer. Riceve le richieste dal livello superiore per il controllo del lavaggio in cosrso.


**Classe Gestore lavaggio**
```
class WashingManager implements WashingControlService is
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
```
// Descrizione: Gestisce l'esecuzione delle fasi del ciclo di lavaggio, interfacciandosi con l'Hardware Layer tramite LavatriceHardwareInterface. Riceve richieste da Scheduler/Schedule e invia comandi all'hardware. Gestisce feedback/eventi dall'hardware (fine fase, errori, ecc.).

**Interfaccia ClockObserver**
```
interface ClockObserver is
    // Metodi
    onTick(currentTime: DateTime) // chiamato dal Clock ad ogni tick
```

---

## Hardware Abstraction Layer

**Interfaccia Clock**
```
interface IClock is
    // Metodi
    subscribe(observer: ClockObserver)
    unsubscribe(observer: ClockObserver)
    notifyObserverOnTick() // notifica l'evento
```
// Descrizione: Rappresenta l'orologio/logica temporale del sistema (Hardware Layer). Permette la registrazione di observer (es. Scheduler) e notifica periodicamente gli eventi di avanzamento temporale (tick) tramite il metodo onTick.

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

### Relazioni

**Relazioni principali:**
- Scheduler *contiene* una lista di Schedule (aggregazione).
- Schedule *contiene* un PianoLavaggio (associazione).
- Clock (Hardware Layer) *notifica* gli observer (es. Scheduler) tramite onTick.
- Scheduler *implementa* ClockObserver e aggiorna la pianificazione ad ogni tick.
- Scheduler *collabora* con WashingManager per l'avvio dei cicli (associazione).
- WashingManager *utilizza* LavatriceHardwareInterface per comandare l'hardware (associazione).
- WashingManager *contiene* un PianoLavaggio (associazione).
- WashPlanningService *implementa* IWashPlanningService.
- WashPlanningService *contiene* Scheduler per l'aggiunta di nuovi schedule.
- WashPlanningController *utilizza* IWashPlanningService per la pianificazione dei cicli (dipendenza Presentation → Application Layer).
- WashPlanningForm *utilizza* WashPlanningController per inviare i dati di pianificazione (dipendenza Presentation → Controller).
- WashStartForm *utilizza* WashPlanningController per avviare subito il ciclo (dipendenza Presentation → Controller).
- WashingManager *implementa* WashingControlService
- WashControlController *utilizza* WashingControlService per il controllo ciclo (dipendenza Presentation → Application Layer).
- WashControlMenu *utilizza* WashControlController per inviare i comandi di controllo (dipendenza Presentation → Controller).

**Dependency**:
- WashPlanningService 'utilizza' PianoLavaggio per la creazione di nuovo Piano
- WashPlanningService 'utilizza' Schedule per la creazione di nuovo Schedule