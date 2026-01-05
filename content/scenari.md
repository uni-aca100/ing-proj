## SCENARI D’USO
In questa sezione descriveremo gli scenari d’uso: Descrivono situazioni tipiche in cui gli utenti interagiscono con il sistema della lavatrice intelligente per raggiungere specifici obiettivi. Ogni scenario include gli attori coinvolti, il flusso di eventi.

### Scenario: pianificaLavaggio

**Istanze attori partecipanti:**  
Giulia: Utente
Luca: Utente

**Flusso di eventi:**  
1. Giulia, rientrando a casa dopo il lavoro, nota che il cesto della biancheria è pieno. Decide di programmare un lavaggio per la sera, quando la tariffa energetica sarà più conveniente.
2. Dal display touch della lavatrice, Giulia seleziona il menu "Pianifica Lavaggio".
3. Il sistema mostra le opzioni disponibili: tipo di tessuto, temperatura, centrifuga, orario di avvio.
4. Giulia imposta il programma "Cotone", seleziona 40°C e sceglie come orario di avvio le 22:00.
6. Giulia conferma la pianificazione. Il sistema valida i dati, registra la pianificazione e invia una notifica di riepilogo.
7. Luca, tornando a casa, legge la notifica e aggiunge i suoi asciugamani nel cestello.
8. Alle 22:00, il sistema avvia automaticamente il ciclo di lavaggio programmato e invia una notifica all’app mobile di Giulia e Luca, informandoli dell’avvio del ciclo.

### Scenario: associazioneDispositivoIoT

**Istanze attori partecipanti:**  
Giulia: Utente autenticato
smartMeter: Dispositivo IoT compatibile

**Flusso di eventi:**  
1. Giulia desidera ottimizzare i consumi energetici della lavatrice integrandola con lo smart meter di casa.
2. Dal display della lavatrice, Giulia accede al menu "Gestione dispositivi IoT" e seleziona l’opzione "Cerca nuovi dispositivi".
3. Il sistema avvia la scansione della rete domestica e mostra l’elenco dei dispositivi IoT compatibili rilevati, tra cui "smartMeter".
4. Giulia seleziona "smartMeter" dall’elenco e avvia la procedura di associazione.
5. Il sistema richiede conferma e, una volta ricevuta, stabilisce una connessione sicura con il dispositivo.
6. Il sistema notifica Giulia dell’avvenuta associazione e mostra le nuove informazioni disponibili.
7. Giulia, può ora visualizzare l’elenco aggiornato dei dispositivi associati e può ora attivare o disattivare la modalità di lavaggio ottimizzata in base alla disponibilità energetica.
8. In caso di errore durante l’associazione, il sistema notifica l’errore e suggerisce possibili soluzioni (es. controllare la connessione di rete o la compatibilità del dispositivo).

### Scenario: controlloCicloLavaggio

**Istanze attori partecipanti:**  
Luca: Utente  

**Flusso di eventi:**  
1. Luca avvia un ciclo di lavaggio per i suoi vestiti tramite il display della lavatrice.
2. Dopo alcuni minuti, Luca si accorge di aver dimenticato di aggiungere una maglietta. Dal menu "Stato Lavaggio", seleziona l’opzione "Pausa".
3. Il sistema sospende tutte le operazioni in corso, blocca la centrifuga e mostra lo stato "In pausa" sul display.
4. Luca apre lo sportello, aggiunge la maglietta e richiude lo sportello.
5. Dal display, Luca seleziona l’opzione "Riprendi".
6. Il sistema verifica che lo sportello sia chiuso correttamente e che le condizioni di sicurezza siano rispettate.
7. Il ciclo di lavaggio riprende dal punto in cui era stato interrotto, e il display mostra lo stato aggiornato.
8. Al termine del ciclo, il sistema invia una notifica all’app mobile di Luca, informandolo che il lavaggio è completato.

### Scenario: registrazioneProfiloUtente

**Istanze attori partecipanti:**  
Giulia: utente  

**Flusso di eventi:**  
1. Giulia ha appena installato la lavatrice intelligente e desidera creare il proprio profilo utente per personalizzare le impostazioni e abilitare i controllo vocale.
2. Dal display touch della lavatrice, Giulia seleziona il menu "Gestione utenti" e sceglie l’opzione "Registra nuovo profilo".
3. Il sistema mostra un form di registrazione dove Giulia inserisce nome, cognome, email, lingua e crea una password.
5. Dopo aver inserito i dati, Giulia conferma la registrazione.
6. Il sistema valida i dati, crea il nuovo profilo utente e invia una notifica di conferma all’email.
7. Giulia riceve la notifica, accede con le nuove credenziali e personalizza le preferenze.
8. In caso di errore (es. email già registrata o dati mancanti), il sistema mostra una notifica di errore e suggerisce le correzioni necessarie.

### Scenario: personalizzazioneConfigurazioneUI

**Istanze attori partecipanti:**  
Luca: utente

**Flusso di eventi:**  
1. Luca desidera rendere l’interfaccia della lavatrice più leggibile e adatta alle sue esigenze visive.
2. Dal display touch, Luca accede al menu "Configurazione UI".
3. Il sistema mostra le opzioni disponibili: tema, contrasto, luminosità, dimensione del font, lingua, modalità accessibilità.
4. Luca seleziona un tema ad alto contrasto e aumenta la dimensione del font dello schermo.
5. Luca attiva la modalità accessibilità per daltonici.
6. Dopo aver modificato le impostazioni, Luca conferma le scelte.
7. Il sistema verifica che l'utente sia autenticato, se lo è, applica immediatamente le modifiche e salva la configurazione per le sessioni future.
8. Il sistema invia una notifica di conferma a Luca, informandolo che la configurazione è stata aggiornata con successo.
9. In caso di errore (es. valore non valido, mancanza di autorizzazione), il sistema mostra una notifica di errore e suggerisce le correzioni necessarie.

### Scenario: resetLavatrice

**Istanze attori partecipanti:**  
Giulia: utente

**Flusso di eventi:**  
1. Giulia nota che la lavatrice mostra un errore persistente.
2. Dal display touch, Giulia accede al menu "Gestione sistema" e seleziona l’opzione "Reset lavatrice".
3. Il sistema richiede conferma dell’operazione, avvisando che tutte le impostazioni e i cicli in corso verranno annullati.
4. Giulia conferma la richiesta di reset.
5. Il sistema verifica che l'utente sia autenticato, se lo è, interrompe tutte le operazioni in corso, scarica eventuale acqua residua e riporta la lavatrice allo stato di fabbrica.
6. Il sistema mostra una notifica di completamento reset e invita Giulia a riconfigurare le preferenze utente se necessario.
7. Giulia verifica che la lavatrice sia tornata operativa e, se necessario, ripristina le impostazioni personalizzate.
8. In caso di errore durante il reset (es. sportello non chiuso, utente non autenticato), il sistema mostra una notifica di errore e suggerisce le azioni correttive.

