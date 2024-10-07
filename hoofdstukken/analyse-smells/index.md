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
Locatie: [ThinkDifferent.java](https://github.com/processing/processing/blob/master/core/src/processing/core/ThinkDifferent.java)
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
Alle comments binnen deze `init` methode zijn redundant of kunnen dat zijn. Daarmee bedoel ik te zeggen dat als deze 
code goed geschreven was al deze comments weggelaten zouden kunnen worden. De daadwerkelijke code lezen levert betere 
informatie dan de comments. 

Neem de regel `!attemptedQuit) { // haven't tried yet`. De variable `attemptedQuit` geeft al duidelijk aan dat er 
geprobeerd is om af te sluiten[^1]. De comment `// haven't tried yet` vertelt, in het beste geval, dezelfde 
informatie. 

[^1]: `userAttemptedToQuit` zou nog veel duidelijker zijn.

#### Oplossing
Alle benoemde comments zouden verwijderd moeten worden. 

De code kan ook leesbaarder gemaakt worden.
Mogelijk zou het statement `PApplet.uncaughtThrowable == null` 
kunnen worden geabstraheerd naar zijn eigen methode: 
`PApplet.hasNoUncaughtExceptions()`.



## Naamgeving

### ThinkDifferent

## Functions

### Processing: PImage.java
Locatie: [PImage.java]()  
Omvang: 482 t/m 504  
Smell Code: F1 Too Many Arguments  

```java
public void updatePixels(int x, int y, int w, int h) {  // ignore
    int x2 = x + w;
    int y2 = y + h;

    if (!modified) {
        mx1 = PApplet.max(0, x);
        mx2 = PApplet.min(pixelWidth, x2);
        my1 = PApplet.max(0, y);
        my2 = PApplet.min(pixelHeight, y2);
        modified = true;
      
    } else {
        if (x < mx1) mx1 = PApplet.max(0, x);
        if (x > mx2) mx2 = PApplet.min(pixelWidth, x);
        if (y < my1) my1 = PApplet.max(0, y);
        if (y > my2) my2 = PApplet.min(pixelHeight, y);

        if (x2 < mx1) mx1 = PApplet.max(0, x2);
        if (x2 > mx2) mx2 = PApplet.min(pixelWidth, x2);
        if (y2 < my1) my1 = PApplet.max(0, y2);
        if (y2 > my2) my2 = PApplet.min(pixelHeight, y2);
    }
}
```

_Figuur 3: Processing PImage.java_

#### Wat deugt niet?
Het gaat hier om de arguments in de definitie van deze functie, namelijk: `int x, int y, int w, int h`. Specifieker, 
het aantal arguments.

#### Waarom deugt het niet?
Het is moeilijk om te begrijpen. Men moet goed opletten welke waardes op welke positie moeten geplaatst worden. Zelfs na
uitgebreid gebruik kan dit nog steeds verwarring veroorzaken. In het slechtste geval ziet een functie call eruit als:
```java
image.updatePixels(20, 30, 100, 200);
```

En in een iets betere wereld misschien:

```java
image.updatePixels(width, height, x, y);
```

Als u goed heeft opgelet erkende u dat ik sommige arguments in het laatste voorbeeld heb omgedraaid. Een hogere 
hoeveelheid arguments kan er gemakkelijk voor zorgen daar arguments worden omgedraaid. In dit geval zou de compiler
er zelfs niks van zeggen, het zijn allen `int`'s.

Dan zijn er de mensen die bij twijfeling de functie definitie zullen raadplegen. Echter is dat ook een probleem.
Dat is aandacht en tijd gestoken in iets triviaals, verspilde tijd dus.

#### Oplossing
```java
public void updatePixels(Point upperLeftCorner, Size size) {
    ...
}
```

_Figuur N: PImage.java updatePixels() verbeterde definitie

Er hoeven maar twee concepten gegeven worden aan de functie. Deze concepten verdienen hun eigen naam! In bovenstaand
voorbeeld gaat het eigenlijk over:
- `Point`: de `upperLeftCorner` die bestaat uit een `x` en `y`.
- `Size`: de `size` die bestaat uit een `width` en `height`.

Het zal nu veel duidelijker zijn om de functie aan te roepen:
```java
image.updatePixels(
    new Point(20, 30),
    new Size(100, 200)
);
```

of wat realistischer:

```java
image.updatePixels(
    upperLeftCorner,
    size
);
```

In het geval dat de arguments alsnog worden omgedraaid zal de compiler dit tegenhouden. Een `Size` object kan namelijk
niet in dezelfde plek gebruikt worden als een `Point`.