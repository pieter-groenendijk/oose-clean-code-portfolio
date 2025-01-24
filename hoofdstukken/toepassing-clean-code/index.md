# Toepassing clean code
## Meaningful Names
### 1. OOSE Library Management System: Repositories
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
### 2. OOSE Library Management System: NotificationSendStrategyRegistry
Locatie: [Onbeschikbaar]
Omvang: -
Clean Code Regel: Switch Statements

```java
public NotificationSendStrategy fromStrategyType(SendStrategyType type) {
    return switch (type) {
        case ALERT -> ALERT_STRATEGY;
        case WARNING -> WARNING_STRATEGY;
        case REMINDER -> REMINDER_STRATEGY;
    };
}
```

#### Wat deugt er?
Hoe een switch statement gebruikt wordt.

#### Waarom deugt het?
Switch statements doen technisch gezien altijd meer dan één ding, dat is eenmaal zo. Het is realiteit dat we niet de
`NotificationSendStrategy` goed kunnen opslaan op zichzelf, daarom wordt i.p.v. een enum gebruikt. Dit heeft het
effect dat er minimaal één switch statement gebruikt moet worden. Nu is het systeem zo gebouwd dat dit de enige switch
ook is. Vervolgens kunnen we dan met behulp van polymorfisme het verder gebruiken. 

Op deze manier wordt het Open/Closed principle zo weinig mogelijk overtreden. Daarom is deze techniek beter dan het alternatief;
meerdere switch statements. Je kan natuurlijk ook overal switch statements om dan gedrag te sturen. Hierdoor wordt het
gelijk al een wrak i.v.m. Open/Closed principle. 

De switch kunnen we dus niet totaal vermijden, maar wel zoveel mogelijk.


### 3. OOSE Library Management System: ChannelNotifier.java
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


### 4. OOSE Library Management System: NotificationService.java
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


### 5. OOSE Library Management System: ChannelNotifier.java
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


### 6. OOSE Library Management System: ChannelNotifier.java
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


### 7. OOSE Library Management System: ConfigurationBuilder.java
Locatie: [ConfigurationBuilder.java]()  
Omvang: 12 t/m 70
Clean Code Regel: Function arguments (count)

```java
public ConfigurationBuilder setQuotingOfIdentifiers(boolean isQuoted) {
    setProperty(
        "hibernate.globally_quoted_identifiers",
        isQuoted
    );

    return this;
}

public ConfigurationBuilder addAnnotatedClass(Class annotatedClass) {
    CONFIGURATION.addAnnotatedClass(annotatedClass);

    return this;
}

public ConfigurationBuilder setDriver(String driverClass) {
    setProperty(
        "hibernate.connection.driver_class",
        driverClass
    );

    return this;
}

public ConfigurationBuilder setDialect(String dialectClass) {
    setProperty(
        "hibernate.dialect",
        dialectClass
    );

    return this;
}

public ConfigurationBuilder setSchemaHandlingMethod(String schemaHandlingMethod) {
    setProperty(
        "hibernate.hbm2ddl.auto",
        schemaHandlingMethod
    );

    return this;
}

public ConfigurationBuilder setShowingOfSQL(boolean isShown) {
    setProperty(
        "hibernate.show_sql",
        isShown
    );

    return this;
}

public ConfigurationBuilder setFormattingOfSQL(boolean isFormatted) {
    setProperty(
        "hibernate.format_sql",
        isFormatted
    );

    return this;
}
```

#### Wat deugt er?
Het aantal argumenten dat bovenstaande functies om vragen.

#### Waarom deugt het? // TODO: Rewrite
Er is hier gebruik gemaakt van een builder pattern. Een alternatief zou één super grote constructor maken. In dit geval
zal dit het voornamelijk nadeel creëren dat men niet meer weet welk argument op welke plek moet. De argumenten worden op
verkeerde plekken gezet, en indien er overlap is in de types, zal er niet eens een fout oplopen. Door gebruik te maken
van een builder pattern, wordt het create patroon _verbose_, maar ook voorspelbaar.


### 8. Onbekend.js
Locatie: [Onbeschikbaar]  
Omvang: -  
Clean Code Regel: Command Query Separation  

```js
isValid() {
    this.#validationRules.forEach((validationRule) => {
        if (validationRule.getValidationErrorFor(this.value())) {
            return true;
        }
    })

    return false;
}

showErrorToUser(errorMessage) {
    this.#validationErrorPatcher = new FieldErrorPatcher(
        this.#inputElement,
        errorMessage
    );
}
```

#### Wat deugt er?
Er worden geen _command_ en _query_ functies gemixt.

#### Waarom deugt het?
Dit houdt de applicatie voorspelbaar. Het is onvoorspelbaar voor de caller dat een methode zoals `isValid()` bijv. ook 
gelijk de errors zou laten zien aan de gebruiker met de functie `showErrorToUser()`. Net zoals je niet verwacht dat
`showErrorToUser()` eerst gaat kijken of er überhaupt errors zijn. 

Er is een isolatie van wie de _state_ mag muteren. Er zijn natuurlijk gevallen waar je de _state_ zou willen aanpassen
en gelijk bij die waarde zou willen komen. Dit zorgt ervoor dat de code onnodig complexer zou worden, daarnaast verliest
de caller een klein beetje controle. De gecallde functie beslist nu namelijk deels wat er moet gebeuren met de nieuwe 
_state_, niet de daadwerkelijke die de functie aanroept.


### 9. Library Management System: DetachedNotificationFactory.java
Clean Code Regel: One Level Of Abstraction

Locatie: [DetachedNotificationFactory.java]()  
Omvang: 22 t/m 27  

Locatie: [DetachedNotificationGenerator]()
Omvang: 23 t/m 28

```java
public DetachedTask<LoanNotification> createOverdueLoanNotification(Account account, Loan loan) {
    return OVERDUE_GENERATOR.generate(
        account,
        loan
    );
}
```

```java
public final DetachedTask<K> generate(Account account, T context) {
    return new DetachedTask<>(
        this.generateUnderlyingTask(account, context),
        this.generateStorage(context)
    );
}
```

#### Wat deugt er?
Consistent niveau van abstractie.

#### Waarom deugt het?
Het laat concreet zien wat er daadwerkelijk gebeurt, zonder dat te verstoppen in 100 details. De code is sneller en beter
te begrijpen. Men wil focussen op wat de essentiële concepten zijn.



## Comments
### 10. OOSE Library Management System: EventEmitterPool.java
Locatie: [EventEmitterPool.java]()  
Omvang: 62 t/m 71  
Clean Code Regel: Informative Comments

```java
/**
 * Fails silently if the emitter is not found in the pool.
 */
public <T> void emit(@NotNull EventType type, T context) {
    EventEmitter<T> emitter = this.get(type);

    if (emitter == null) return;

    emitter.emit(context);
}
```

#### Wat deugt er?
De comment.

#### Waarom deugt het?
Het maakt het voor andere developers duidelijk van buitenaf wat er gebeurt in een specifieke situatie binnen de functie;
het leegt een stukje implementatie bloot. Zo komen die developer minder snel tegen problemen aan omdat ze begrijpen wat
er in die situatie gebeurt. 


### 11. OOSE Library Management System: FineManager.java
Locatie: [Onbeschikbaar]  
Omvang: -  
Clean Code Regel: TODO Comments

```java
// TODO: Better name?
public class FineManager {
```

#### Wat deugt er?
De _TODO_ comment.

#### Waarom deugt het?
Het is een taak die de programmeur denkt dat er gedaan moet worden, maar op dit moment niet kan doen. De taak is hier het
bedenken van een betere naam. 

Het is gemakkelijk om snel een todo toe te voegen, wanneer je in de code bezig bent. Je hebt een openbaring
over hoe de code beter kan zijn, maar weet dat je momenteel daar niet de tijd voor hebt. Met een comment kan je in 
veel van zulke situaties met weinig _overhead_ het documenteren.

Langdurige taken, of taken waar niet alleen developers bij betrokken zijn kunnen natuurlijk
beter gezet worden in een _issue tracking system_.

Iemand wordt gelijk aan het denken gezet wanneer deze langs de comment komt, en kan vervolgens de keuze maken om aan de
slag te gaan met de _todo_ of niet.


### 12. OOSE Library Management: DetachedEventGenerator
Locatie: [Onbeschikbaar] DetachedEventGenerator  
Omvang: -  
Clean Code Regel: Explanation of Intent  

```java
/**
 * A generic is used to specify what the DetachedEvent will wrap. This generic exists because:
 * - We want the event types to follow Liskov's substitution principle. We can't make a separate entity that holds a reference to the event
 * and the association for this reason. A side effect is that we also lose control over the db structure; we can't choose
 * an inheritance type.
 * - For that reason we need a subclass that extends Event (generics are removed at runtime, so we need a subclass). To
 * be able to correctly store this subclass though it needs to be known to the ORM.
 * - For that reason the generator also needs to know what it will generate. Since it passes the mechanism to store the
 * generated (wrapped) event.
 */
public abstract class DetachedEventGenerator<Association, AssociatedEvent extends Event<Association>> {
```

#### Wat deugt er?
De comment

#### Waarom deugt het?
Hoewel niet perfect geschreven, helpt deze comment met het begrijpen waarom de developer gekozen heeft een bepaalde 
generic te kiezen. Een andere dappere developer zal hierdoor minder snel proberen de generic uit het systeem te werken,
omdat die de keuze niet begrijpt. Met deze informatie kan beter een keuze gemaakt worden.



## Error Handling
### 13. OOSE Library Management System: IFineRepository.java
Locatie: [IFineRepository.java](https://github.com/pieter-groenendijk/oose-library-management-system/blob/v0.1.0/application/core/src/main/java/com/github/pieter_groenendijk/datasource/repositories/fine/IFineRepository.java)  
Omvang: 12 t/m 12  
Clean Code Regel: Don't Return Null  

```java
Optional<FineType> retrieveFineType(String title) throws Exception;
```

#### Wat deugt er?
Het gebruik van de `Optional<?>`;

#### Waarom deugt het?
I.p.v. dat de methode `FineType` teruggeeft, geeft deze de ingebouwde klasse `Optional<FineType>` terug. Hierdoor is de
aanroepende partij gedwongen één van de ingebouwde methodes te gebruiken om waarde te krijgen. Men is gedwongen over het
feit te denken dat er null in de `Optional` kan zitten. 

Er kan zo geen `null` waarde door het systeem bewegen om vervolgens het systeem eruit te blazen. Deze techniek zorgt
ervoor dat `NullPointerException`'s praktisch gezien niet kunnen plaatsvinden.



## Unit Tests
### 14. OOSE Library Management System: DetachedEventGeneratorTest.java
Locatie: [DetachedEventGeneratorTest.java]()  
Omvang: 55 t/m 138  
Clean Code Regel: Single Concept per Test  

```java
    @Test
    void testGenerate() {
        this.generator = new MockDetachedEventGenerator<Loan, LoanEvent>(EventType.ALMOST_OVERDUE_LOAN) {
            @Override
            protected LoanEvent generateEmptyEvent() {
                return mock(LoanEvent.class);
            }
        };

        this.generator.generate(mock(Loan.class));
    }

    @Test
    void testGenerate_returnsTask() {
        this.generator = new MockDetachedEventGenerator<Loan, LoanEvent>(EventType.ALMOST_OVERDUE_LOAN) {
            @Override
            protected LoanEvent generateEmptyEvent() {
                return mock(LoanEvent.class);
            }
        };

        DetachedTask<LoanEvent> task = this.generator.generate(mock(Loan.class));

        assertNotNull(task);
    }

    @Test
    void testGenerate_correctScheduledDateTime() {
        LoanEvent returnedLoanEvent = new LoanEvent();

        LocalDateTime returnedScheduledDateTime = LocalDateTime.now();

        this.generator = new MockDetachedEventGenerator<Loan, LoanEvent>(EventType.ALMOST_OVERDUE_LOAN) {
            @Override
            protected LoanEvent generateEmptyEvent() {
                return returnedLoanEvent;
            }

            @Override
            protected LocalDateTime determineScheduledDateTime(Loan loan) {
                return returnedScheduledDateTime;
            }
        };

        this.generator.generate(mock(Loan.class));

        assertEquals(returnedScheduledDateTime, returnedLoanEvent.getScheduledAt());
    }

    @Test
    void testGenerate_correctEventType() {
        LoanEvent returnedLoanEvent = new LoanEvent();

        EventType eventType = EventType.ALMOST_OVERDUE_LOAN;

        this.generator = new MockDetachedEventGenerator<Loan, LoanEvent>(eventType) {
            @Override
            protected LoanEvent generateEmptyEvent() {
                return returnedLoanEvent;
            }
        };

        this.generator.generate(mock(Loan.class));

        assertEquals(eventType, returnedLoanEvent.getType());
    }

    @Test
    void testGenerate_correctAssociation() {
        LoanEvent returnedLoanEvent = new LoanEvent();

        this.generator = new MockDetachedEventGenerator<Loan, LoanEvent>(EventType.ALMOST_OVERDUE_LOAN) {
            @Override
            protected LoanEvent generateEmptyEvent() {
                return returnedLoanEvent;
            }
        };

        Loan association = mock(Loan.class);

        this.generator.generate(association);

        assertEquals(association, returnedLoanEvent.getLoan());
    }
```

#### Wat deugt er?
De opsplitsing van tests.

#### Waarom deugt het?
Doordat er enkel sprake is van één concept per test wordt ervoor gezorgd dat wanneer een test faalt, dat we ook gelijk
weten wat de gemiste functionaliteit is. 

Met één grote testfunctie zou je bij een fout eerst de test moeten
_debuggen_ om te kijken waar de fout daadwerkelijk plaatsvindt, vervolgens moet je dan bedenken wat de fout is. Enkel
daarna kan je daadwerkelijk gaan kijken naar de gemiste functionaliteit. Onpraktisch dus.



## Objects and Data Structures
### 15. OOSE Library Management System: NotificationTaskScheduler.java
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



## Classes
### 16. OOSE Library Management System: ChannelNotifier.java
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

