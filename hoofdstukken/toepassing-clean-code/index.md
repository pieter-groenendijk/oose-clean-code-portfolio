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


## Comments




## Formatting




## Error Handling




## Boundaries




## Unit Tests




## Classes



