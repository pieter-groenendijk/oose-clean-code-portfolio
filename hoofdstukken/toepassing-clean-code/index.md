# Toepassing clean code
## Meaningful Names



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

