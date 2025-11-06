### Sequence Diagram: Pianificazione Lavaggio
```
Attore Utente -> WashPlanningForm : Inserisce parametri e data/ora
WashPlanningForm -> WashPlanningForm : submit()
WashPlanningForm -> WashPlanningController : onPianificaLavaggio(parametriPiano: map<string, string>, dataOra: DateTime)
WashPlanningController -> WashPlanningService : pianificaLavaggio(parametri, data/ora): bool
WashPlanningService -> WashPlanningService : parametriValidi = validaParametri(parametriPiano: map<string, string>) : bool
alt [parametriValidi]
    WashPlanningService -> Schedule : <<create>>
    WashPlanningService -> Scheduler : scheduleValido = aggiungiSchedule(newSchedule: Schedule)
    Scheduler -> Scheduler : validaSchedule(newSchedule: schedule):bool
    Scheduler --> WashPlanningService : bool
    alt [not scheduleValido]
        WashPlanningService -> Schedule : <<destroy>>
else
WashPlanningService -> ref [push notification] 
WashPlanningService --> WashPlanningController
WashPlanningController --> WashPlanningForm

```
**Legenda lifeline:**
- Attore Utente
- WashPlanningForm (UI)
- WashPlanningController (Controller)
- WashPlanningService (Machine Management)
- Schedule (oggetto di dominio)
- Scheduler (Machine Management)

Nota: se la pianificazione risulta invalida, l’oggetto Schedule viene distrutto (`<<destroy>>`).


### Sequence Diagram: Associazione Dispositivo IoT (e scoperta)
```
// scan phase
Attore Utente -> IoTDeviceForm : Avvia scansione dispositivi
IoTDeviceForm -> IoTDeviceForm : scanDevices()
IoTDeviceForm -> IoTDeviceController : onScanDevices()
IoTDeviceController -> IoTIntegrationService : discoverDevices()
IoTIntegrationService -> NetworkInterface : broadcast()
IoTIntegrationService * -> NetworkInterface : receiveData()
NetworkInterface --> IoTIntegrationService
IoTIntegrationService * -> Device : <<create>>
IoTIntegrationService --> IoTDeviceController
IoTDeviceController --> IoTDeviceForm
IoTDeviceForm -> IoTDeviceForm : render() // mostra dispositivi

// association phase
Attore Utente -> IoTDeviceForm : Avvia associa dispositivo
IoTDeviceForm -> IoTDeviceForm : selectDevice()
IoTDeviceForm -> IoTDeviceForm : associate()
IoTDeviceForm -> IoTDeviceController : onAssociateDevice()
IoTDeviceController -> IoTIntegrationService : associateDevice()
IoTIntegrationService -> AuthenticationService : verifySession()
AuthenticationService --> IoTIntegrationService
IoTIntegrationService -> ref [push notification] // success/error notification
ref [push notification] --> IoTIntegrationService
IoTIntegrationService --> IoTDeviceController
IoTDeviceController --> IoTDeviceForm
```

**Legenda lifeline:**
- Attore Utente
- IoTDeviceForm (UI)
- IoTDeviceController (Controller)
- IoTIntegrationService (Application Layer)
- AuthenticationService (Security, opzionale)
- NetworkInterface (Hardware Layer)
- IoTDevice (oggetto/dispositivo)