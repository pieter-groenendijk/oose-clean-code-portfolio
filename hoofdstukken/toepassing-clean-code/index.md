# Toepassing clean code
## Meaningful Names

### OOSE Library Management System: Repositories
Locatie: []()  
Omvang: 
Clean Code Regel: Pick One Word per Concept  

// TODO: Add additional repositories

```java
public interface INotificationTaskRepository {
    public List<NotificationTask> retrieve(LocalDateTime scheduledUntil);
}
```

#### Wat deugt er?
Naamgeving van de functie.

#### Waarom deugt het?
In elke repository binnen de applicatie wordt dezelfde term gebruikt voor hetzelfde concept. `retrieve...()` betekent
"data ophalen uit de database", en niks anders. De consequenties van het uitvoeren van zo'n functie is voorspelbaar,
dus duidelijk en transparant.


## Functions

### OOSE Library Management System: ChannelNotifier.java
Locatie: [ChannelNotifier.java](https://github.com/pieter-groenendijk/oose-library-management-system/blob/notifications/application/src/main/java/com/github/pietergroenendijk/notifications/notifiers/ChannelNotifier.java)  
Omvang: 21 t/m 38
Clean Code Regel: Extract Try/Catch Blocks

```java
private void send(NotificationTask task, byte amountOfAttempts) {
    try {
        this.attempt(task);
    } catch (Exception e) {
        this.handleSendingError(task, amountOfAttempts);
    }
}

protected abstract void attempt(NotificationTask task) throws Exception;

private void handleSendingError(NotificationTask task, byte amountOfAttempts) {
    if (amountOfAttempts < this.MAXIMUM_AMOUNT_OF_ATTEMPTS) {
        ++amountOfAttempts;
        this.send(task, amountOfAttempts);
    } else {
        logFailedSending(task);
    }
}
```

#### Wat deugt er?
De try/catch blocks bevatten enkel calls naar andere functies. De onderliggende logica is geabstraheerd. 

#### Waarom deugt het?
Errors afhandelen in een doel op zich. Het wordt verwarrend als de onderliggende logica rechtstreeks 
geplaatst wordt in de try/catch blocks. Je hoeft niet actief de error afhandeling weg te filteren terwijl 
je de onderliggende logica probeert te begrijpen. Door de scheiding van de twee doelen is de code 
makkelijker te begrijpen en aan te passen.

### OOSE Library Management System: NotificationService.java
Locatie: [NotificationService.java]()  
Omvang: 8 t/m 28  
Clean Code Regel: Functions: Small!!!

```java
public class NotificationService {
    private final NotificationTaskFactory TASK_FACTORY;
    private final NotificationTaskScheduler SCHEDULER;

    public NotificationService(TaskScheduler scheduler) {
        NotificationTaskRepository repository = new NotificationTaskRepository();

        this.TASK_FACTORY = new NotificationTaskFactory(repository);
        this.SCHEDULER = new NotificationTaskScheduler(scheduler, repository);
    }

    public void scheduleNewLendingBundle(AccountBase account, Lending lending) {
        this.scheduleReturnDateNotification(account, lending);
    }

    private void scheduleReturnDateNotification(AccountBase account, Lending lending) {
        this.SCHEDULER.schedule(
            this.TASK_FACTORY.createReturnDateNotificationTask(account, lending)
        );
    }
}
```

#### Wat deugt er?
De grootte van de functies in de klasse.

#### Waarom deugt het?
De functies zijn klein, enkel een paar regels. Dit maakt de code erg makkelijk te lezen, namelijk:
1. Meer gebruik van functies i.p.v. rechtstreekse code. Men kan een functie een naam geven en dus op
die manier goed duidelijk maken wat de code doet.
2. Duidelijk wat de focus is van de functie. 
3. Minder behoefte om dingen te onthouden om de rest van de functie te begrijpen.



### OOSE Library Management System: ChannelNotifier.java
Locatie: [ChannelNotifier.java]()  
Omvang: 21 t/m 27  
Clean Code Regel: Do One Thing

```java
private void send(NotificationTask task, byte amountOfAttempts) {
    try {
        this.attempt(task);
    } catch (Exception e) {
        this.handleSendingError(task, amountOfAttempts);
    }
}
```

_Figuur N: OOSE LMS: ChannelNotifier.java_

#### Wat deugt er?
De body van de functie.

#### Waarom deugt het?
De functie doet 1 ding, niet meer. Het is te beschrijven in een korte paragraaf: "Het doet een poging
de notification task uit te voeren. In het geval van problemen delegeert die het afhandelen van de 
error.". Daarnaast is de functie, niet op een nuttige manier te splitsen. Het doet dus 1 ding.


### OOSE Library Management System: ChannelNotifier.java
Locatie: [ChannelNotifier.java]()  
Omvang: 5 t/m 38  
Clean Code Regel: Don't Repeat Yourself (DRY)  

```java
public abstract class ChannelNotifier implements Notifier {
    private final byte MAXIMUM_AMOUNT_OF_ATTEMPTS;

    public ChannelNotifier(byte maximumAmountOfAttempts) {
        this.MAXIMUM_AMOUNT_OF_ATTEMPTS = maximumAmountOfAttempts;
    }

    public ChannelNotifier() {
        this.MAXIMUM_AMOUNT_OF_ATTEMPTS = 3;
    }

    @Override
    public void send(NotificationTask task) {
        this.send(task, (byte)1);
    }

    private void send(NotificationTask task, byte amountOfAttempts) {
        try {
            this.attempt(task);
        } catch (Exception e) {
            this.handleSendingError(task, amountOfAttempts);
        }
    }

    protected abstract void attempt(NotificationTask task) throws Exception;

    private void handleSendingError(NotificationTask task, byte amountOfAttempts) {
        if (amountOfAttempts < this.MAXIMUM_AMOUNT_OF_ATTEMPTS) {
            ++amountOfAttempts;
            this.send(task, amountOfAttempts);
        } else {
            logFailedSending(task);
        }
    }

    private void logFailedSending(NotificationTask task) {
        System.out.println("Failed to send notification: " + task.toString());
    }
}
```

_Figuur N: OOSE LMS: ChannelNotifier.java_

```java
public class EmailChannelNotifier extends ChannelNotifier 
```

```java
public class AppChannelNotifier extends ChannelNotifier 
```

```java
public class SMSChannelNotifier extends ChannelNotifier
```

#### Wat deugt er?
Subklassen die gebruik maken van `ChannelNotifier`.

#### Waarom deugt het?
Door gebruik te maken van inheritance wordt ervoor gezorgt dat er geen sprake is van duplicatie.
De generieke logica komt hierdoor 1 keer voor. 

Er is 1 bron van waarheid. Dat betekent dat bij een wijziging aan het algoritme maar op 1 plek iets
verandert hoeft te worden. Hoe minder plekken hoe groter de kans is dat er geen fouten worden 
gemaakt. 



## Comments




## Formatting




## Error Handling



## Objects and Data Structures

### OOSE Library Management System: NotificationTaskScheduler.java
Locatie: [NotificationTaskScheduler.java]()  
Omvang: 37 t/m 48  
Clean Code Regel: Law of Demeter

```java
private void scheduleFromDatabase() {
    NotificationTask[] tasks = this.REPOSITORY.retrieve(this.getScheduledUntilDateTime());

    for(NotificationTask task: tasks) {
        if (isHandledManually(task)) {
            handledManually.remove(task);
            continue;
        }

        scheduleInMemory(task);
    }
}
```

#### Wat deugt er?
De calls naar andere functies

#### Waarom deugt het?
Het volgt de Law Of Demeter. De functie roept alleen maar functies aan van:
- De klasse zelf
- Een object gemaakt door de functie
- Een object doorgegeven als parameter aan de functie
- Een object opgeslagen (als referentie) in de klasse



## Boundaries




## Unit Tests




## Classes

### OOSE Library Management System: ChannelNotifier.java
Locatie: [ChannelNotifier.java](https://github.com/pieter-groenendijk/oose-library-management-system/blob/notifications/application/src/main/java/com/github/pietergroenendijk/notifications/notifiers/ChannelNotifier.java)  
Omvang: 5 t/m 43  
Clean Code Regel: Classes Should Be Small  

```java
public abstract class ChannelNotifier implements Notifier {
    private final byte MAXIMUM_AMOUNT_OF_ATTEMPTS;

    public ChannelNotifier(byte maximumAmountOfAttempts) {
        this.MAXIMUM_AMOUNT_OF_ATTEMPTS = maximumAmountOfAttempts;
    }

    public ChannelNotifier() {
        this.MAXIMUM_AMOUNT_OF_ATTEMPTS = 3;
    }

    @Override
    public void send(NotificationTask task) {
        this.send(task, (byte)1);
    }

    private void send(NotificationTask task, byte amountOfAttempts) {
        try {
            this.attempt(task);
        } catch (Exception e) {
            this.handleSendingError(task, amountOfAttempts);
        }
    }

    protected abstract void attempt(NotificationTask task) throws Exception;

    private void handleSendingError(NotificationTask task, byte amountOfAttempts) {
        if (amountOfAttempts < this.MAXIMUM_AMOUNT_OF_ATTEMPTS) {
            ++amountOfAttempts;
            this.send(task, amountOfAttempts);
        } else {
            logFailedSending(task);
        }
    }

    private void logFailedSending(NotificationTask task) {
        System.out.println("Failed to send notification: " + task.toString());
    }
}
```

#### Wat deugt er?
De omvang, en daarmee de verantwoordelijkheid van de klasse.

#### Waarom deugt het?
De naam van de klasse geeft duidelijk aan wat de verantwoordelijkheid is van deze klasse. Je zou er gemakkelijk een 
korte beschrijving in een paar zinnen van kunnen maken. Zo weet men dat deze klasse goed te begrijpen is.

Het enige twijfelgeval hier is de methode `logFailedSending()`. Daarom zal in de toekomst dit gedelegeerd worden naar
een Logger. 

