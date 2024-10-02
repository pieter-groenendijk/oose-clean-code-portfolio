# Analyse smells

## Comments

### 1. Processing: MouseEvent
Locatie: [MouseEvent.java](https://github.com/processing/processing/blob/master/core/src/processing/event/MouseEvent.java)  
Omvang: 38 t/m 63  
Smell Code: C5 Commented-Out Code

```java
protected int x, y;
protected int button;
//  protected int clickCount;
//  protected float amount;
protected int count;


//  public MouseEvent(int x, int y) {
//    this(null,
//         System.currentTimeMillis(), PRESSED, 0,
//         x, y, PConstants.LEFT, 1);
//  }


public MouseEvent(Object nativeObject,
long millis, int action, int modifiers,
int x, int y, int button, int count) {  //float amount) {  //int clickCount) {
super(nativeObject, millis, action, modifiers);
this.flavor = MOUSE;
this.x = x;
this.y = y;
this.button = button;
//this.clickCount = clickCount;
//this.amount = amount;
this.count = count;
}
```

<div id="figuur-1"></div>

_Figuur 1: Processing MouseEvent.java_ 

#### Wat deugt niet?

Het gaat hier om de code die uit gecomment is.

#### Waarom deugt het niet?

Het creëert de volgende problemen:

- **Wie weet of deze comment nog van waarde is?**  
  Hierdoor zal niemand het verwijderen aangezien het "mogelijk" ooit in
  de toekomst zal gebruikt worden. Daarnaast hoe langer deze comments zullen 
  blijven bestaan ook hoe irrelevanter deze worden.

#### Oplossing

De niet deugende code te verwijderen.



### Processing: ThinkDifferent
Locatie: [ThinkDiffernt.java](https://github.com/processing/processing/blob/master/core/src/processing/core/ThinkDifferent.java)
Omvang: 55 t/m 72
Smell Code: C3 Redundant Comment

```java
static public void init(final PApplet sketch) {
    if (application == null) {
        application = Application.getApplication();
    }

    application.setQuitHandler(new QuitHandler() {
        public void handleQuitRequestWith(QuitEvent event, QuitResponse response) {
        sketch.exit();
        if (PApplet.uncaughtThrowable == null &&  // no known crash
            !attemptedQuit) {  // haven't tried yet
            response.cancelQuit();  // tell OS X we'll handle this
            attemptedQuit = true;
        } else {
            response.performQuit();  // just force it this time
        }
      }
    });
  }
```

_Figuur 2: Processing ThinkDifferent.java_

#### Wat deugt niet?
Het gaat hier om de comments:
- `// no known crash`
- `// haven't tried yet`
- `// tell OS X we'll handle this`
- `// just force it this time`

#### Waarom deugt het niet?
Alle comments binnen deze `init` methode zijn redundant of kunnen dat zijn. Daarmee bedoel ik te zeggen dat als deze code goed geschreven was al deze comments weggelaten zouden kunnen worden. De daadwerkelijke code lezen levert betere informatie dan de comments. 

Neem de regel `!attemptedQuit) { // haven't tried yet`. De variable `attemptedQuit` geeft al duidelijk aan dat er geprobeerd is om af te sluiten[^1]. De comment `// haven't tried yet`. verteld, in het beste geval, dezelfde informatie. 

[^1]: `userAttemptedToQuit` zou nog veel duidelijker zijn.

#### Oplossing
Alle benoemde comments zullen verwijderd moeten worden. 

Vervolgens zou de code ook leesbaarder kunnen gemaakt worden. 
Mogelijk zou het statement `PApplet.uncaughtThrowable == null` 
kunnen worden _geextract_ naar zijn eigen methode: 
`PApplet.hasNoUncaughtExceptions()`.



## Naamgeving

### ThinkDifferent



