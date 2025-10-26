## Architettura del sistema
Schema a livelli (Layered N-tier Architecture)

1. **Presentation Layer**: Gestisce l’interfaccia utente (display, app mobile, accessibilità).
2. **Application Layer**: Implementa la logica dei cicli di lavaggio, pianificazione, notifiche, ottimizzazione consumi, diagnostica, ecc.
3. **Hardware Abstraction Layer**: Interfaccia hardware, si occupa della comunicazione con i sensori, dispositivi IoT.
4. **Data Layer**: Gestisce la persistenza dei dati utente, configurazioni, ecc.

**Event-Driven**:
ogni evento (es. avvio ciclo, errore tecnico, ricezione dati IoT, comando utente) viene gestito tramite un event.

Esempio di flusso event-driven:
1. L’utente avvia un ciclo → Presentation Layer invia evento "AvvioCiclo".
2. Application Layer elabora e invia comandi all’Hardware Layer.
3. Se si verifica un errore, Hardware Layer emette un evento di Errore.
4. Application Layer intercetta l’evento e attiva la logica di gestione errori/notifiche.
5. Presentation Layer riceve evento "Notifica" e aggiorna l’interfaccia.

### Dettaglio Presentation Layer

Il Presentation Layer è suddiviso in tre sotto-componenti principali:

- **UI (User Interface)**: Comprende tutte le view, menu e form che interagiscono direttamente con l’utente tramite display integrato. La UI raccoglie input, mostra lo stato e i risultati delle operazioni, e invia le azioni al controller.

- **Controller**: Riceve input sia dalla UI sia dagli external system (es. comandi remoti, app mobile, dispositivi IoT). Il controller gestisce la logica di presentazione, effettua validazioni e inoltra le richieste all’Application Layer. Centralizza la logica di dispatch e validazione.

- **External System Adapter**: Moduli che ricevono input da sistemi esterni (app mobile, dispositivi IoT, controllo remoto) e li inoltrano ai controller tramite canali di rete. Questi sistemi sono trattati come “utenti remoti” dal Presentation Layer.

+-------------------+ +-----------------------+
|        UI         | |     External System   |
+-------------------+ +-----------------------+
+---------------------------------------------+
|                Controller                   |
+---------------------------------------------+
+---------------------------------------------+
|              Application Layer              |
+---------------------------------------------+ 