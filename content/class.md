# Bozze per Dia. classe UML

**Classe Scheduler**
```
class Scheduler {
    // Attributi
    - List<Schedule> schedules

    // Metodi
    pianificaCiclo(Schedule)
    avviaCiclo(PianoLavaggio)
}
```

**Classe Schedule**
```
class Schedule {
    // Attributi
    int id;
    DateTime dataOra;
    PianoLavaggio piano;
    StatoSchedule stato; // es: pianificato, in esecuzione, completato, annullato

    // Metodi
    aggiornaDataOra(DateTime nuovaDataOra);
}
```

# Classe PianoLavaggio
```
class PianoLavaggio {
    // Attributi
    String nome;
    int temperatura;
    int durata;
    int velocitaCentrifuga;
    bool eco;
    // altri parametri opzionali: prelavaggio, extra risciacquo, ecc.

    // Metodi principali
    validaParametri()
}
```

**Relazioni principali:**
- Scheduler *contiene* una lista di Schedule (aggregazione).
- Schedule *contiene* un PianoLavaggio (associazione).