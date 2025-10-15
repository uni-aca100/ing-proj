# Bozze per Dia. classi UML

## Presentation Layer

**Classe CyclePlanningForm**
```
class CyclePlanningForm is
    // Attributi
    parametriPiano: map<string, string> // input utente (nome, temperatura, durata, ecc.)
    dataOra: DateTime // data/ora pianificazione
    controller: CyclePlanningController // riferimento al controller

    // Metodi
    submit()  // chiama controller.onPianificaCiclo(parametriPiano, now)
```
// Descrizione: Form/view del Presentation Layer per la pianificazione di un ciclo (rappresenta la UI per la raccolta dei dati di pianificazione dal’utente). Raccoglie i dati dall’utente e, al submit, invoca il metodo onPianificaCiclo del controller passando i parametri inseriti.

**Classe StartCycleForm**
```
class StartCycleForm is
    // Attributi
    parametriPiano: map<string, string> // con alcuni campi precompilati (eventualmente nascosti)
    dataOra: DateTime = now // campo nascosto o preimpostato all'istante corrente
    controller: CyclePlanningController // riferimento al controller

    // Metodi
    submit() // chiama controller.onPianificaCiclo(parametriPiano, now)
```
// Descrizione: Form/view specializzato per l'avvio immediato del lavaggio (rappresenta la UI per la raccolta dei dati del piano di lavaggio dal’utente). Al submit invia i parametri e la data/ora corrente al controller tramite onPianificaCiclo.

**Classe CyclePlanningController**
```
class CyclePlanningController is
    // Attributi
    cyclePlanningService: ICyclePlanningService // dipendenza verso Application Service

    // Metodi
    onPianificaCiclo(parametriPiano: map<string, string>, dataOra: DateTime)
```
// Descrizione: Controller "sottile" (non contiene logica di business, delegata all'application Service) del Presentation Layer. che riceve i dati dal form/view per la pianificazione di cicli di lavaggio, ed un eventuale validazione lato presentazione (es. campi mancati). Gestisce l'iterazione con ICyclePlanningService (dell'application layer)

---

## Application Layer

**Interfaccia servizioPianificazioneCicli**
```
interface ICyclePlanningService is
    // Metodi
    pianificaCiclo(parametriPiano: map<string, string>, dataOra: DateTime)
```
// Descrizione: Application Service che espone le operazioni di pianificazione ciclo verso il Presentation Layer. Riceve i parametri dal livello superiore, li valida e li inoltra all'implementazione concreta.

**Classe servizioPianificazioneCicli**
```
class CyclePlanningService is
    // Attributi
    scheduler: Scheduler // associazione/collaborazione

    // Metodi
    validaParametri(parametriPiano: map<string, string>) 
    pianificaCiclo(parametriPiano: map<string, string>, dataOra: DateTime)
```
// Descrizione: Implementa ICyclePlanningService. Si occupa di validare i parametri ricevuti dal Presentation Layer, creare oggetti PianoLavaggio e Schedule, e aggiungere la pianificazione allo Scheduler.

**Classe Scheduler**
```
class Scheduler is
    // Attributi
    schedules: List<Schedule>
    cycleManager: CycleManager // associazione/collaborazione

    // Metodi
    validaSchedule(sch: schedule);
    aggiungiSchedule(newShedule: schedule)
    avviaCiclo(piano: PianoLavaggio) // invoca CycleManager.avviaCiclo()
    onTick(currentTime: DateTime) // reazione allo scorrere del tempo
```
// Descrizione: Si occupa della pianificazione e gestione temporale dei cicli di lavaggio. Mantiene la lista delle pianificazioni (Schedule), riceve eventi temporali (onTick) e, quando necessario, delega l'esecuzione dei cicli al CycleManager. Garantisce la separazione tra logica di scheduling e logica di esecuzione.

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

**Classe GestoreCiclo**
```
class CycleManager is
    // Attributi
    stato: StatoCiclo // es: in esecuzione, in pausa, completato, errore
    piano: PianoLavaggio
    hardware: LavatriceHardwareInterface

    // Metodi
    avviaCiclo(piano: PianoLavaggio)
    pausaCiclo()
    riprendiCiclo()
    annullaCiclo()
    gestisciErrore(errore: ErroreLavatrice)
    aggiornaStato()
```
// Descrizione: Gestisce l'esecuzione delle fasi del ciclo di lavaggio, interfacciandosi con l'Hardware Layer tramite LavatriceHardwareInterface. Riceve richieste da Scheduler/Schedule e invia comandi all'hardware. Gestisce feedback/eventi dall'hardware (fine fase, errori, ecc.).

**Interfaccia ClockObserver**
```
interface ClockObserver is
    // Metodi
    onTick(currentTime: DateTime) // chiamato dal Clock ad ogni tick
```

---

## Hardware Layer

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
// Descrizione: Espone i comandi per il controllo diretto dell'hardware della lavatrice. Utilizzato da CycleManager.

### Relazioni

**Relazioni principali:**
- Scheduler *contiene* una lista di Schedule (aggregazione).
- Schedule *contiene* un PianoLavaggio (associazione).
- Clock (Hardware Layer) *notifica* gli observer (es. Scheduler) tramite onTick.
- Scheduler *implementa* ClockObserver e aggiorna la pianificazione ad ogni tick.
- Scheduler *collabora* con CycleManager per l'avvio dei cicli (associazione).
- CycleManager *utilizza* LavatriceHardwareInterface per comandare l'hardware (associazione).
- CycleManager *contiene* un PianoLavaggio (associazione).
- CyclePlanningService *implementa* ICyclePlanningService.
- CyclePlanningService *contiene* Scheduler per l'aggiunta di nuovi schedule.
- CyclePlanningController *utilizza* ICyclePlanningService per la pianificazione dei cicli (dipendenza Presentation → Application Layer).
- CyclePlanningForm *utilizza* CyclePlanningController per inviare i dati di pianificazione (dipendenza Presentation → Controller).
- StartCycleForm *utilizza* CyclePlanningController per avviare subito il ciclo (dipendenza Presentation → Controller).

**Dependency**:
- CyclePlanningService 'utilizza' PianoLavaggio per la creazione di nuovo Piano
- CyclePlanningService 'utilizza' Schedule per la creazione di nuovo Schedule