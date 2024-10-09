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

### Processing: PApplet.java
Locatie: [PApplet.java](https://github.com/processing/processing/blob/master/core/src/processing/core/PApplet.java)
Omvang: 2242 t/m 2340
Smell Code: Flag Arguments

```java
  protected PGraphics makeGraphics(int w, int h,
                                   String renderer, String path,
                                   boolean primary) {
    if (!primary && !g.isGL()) {
      if (renderer.equals(P2D)) {
        throw new RuntimeException("createGraphics() with P2D requires size() to use P2D or P3D");
      } else if (renderer.equals(P3D)) {
        throw new RuntimeException("createGraphics() with P3D or OPENGL requires size() to use P2D or P3D");
      }
    }

    try {
      Class<?> rendererClass =
        Thread.currentThread().getContextClassLoader().loadClass(renderer);

      Constructor<?> constructor = rendererClass.getConstructor(new Class[] { });
      PGraphics pg = (PGraphics) constructor.newInstance();

      pg.setParent(this);
      pg.setPrimary(primary);
      if (path != null) {
        pg.setPath(savePath(path));
      }
      pg.setSize(w, h);

      // everything worked, return it
      return pg;

    } catch (InvocationTargetException ite) {
      String msg = ite.getTargetException().getMessage();
      if ((msg != null) &&
          (msg.indexOf("no jogl in java.library.path") != -1)) {
        // Is this true anymore, since the JARs contain the native libs?
        throw new RuntimeException("The jogl library folder needs to be " +
          "specified with -Djava.library.path=/path/to/jogl");

      } else {
        printStackTrace(ite.getTargetException());
        Throwable target = ite.getTargetException();
        throw new RuntimeException(target.getMessage());
      }

    } catch (ClassNotFoundException cnfe) {
      if (external) {
        throw new RuntimeException("You need to use \"Import Library\" " +
                                   "to add " + renderer + " to your sketch.");
      } else {
        throw new RuntimeException("The " + renderer +
                                   " renderer is not in the class path.");
      }

    } catch (Exception e) {
      if ((e instanceof IllegalArgumentException) ||
          (e instanceof NoSuchMethodException) ||
          (e instanceof IllegalAccessException)) {
        if (e.getMessage().contains("cannot be <= 0")) {
          throw new RuntimeException(e);
        } else {
          printStackTrace(e);
          String msg = renderer + " needs to be updated " +
            "for the current release of Processing.";
          throw new RuntimeException(msg);
        }
      } else {
        printStackTrace(e);
        throw new RuntimeException(e.getMessage());
      }
    }
  }
```

_Figuur N: Processing PApplet.java (Commented code al weggelaten)_

### Wat deugt niet?
De boolean argument `boolean primary`.

### Waarom deugt het niet? 
Een functie hoort 1 ding te doen. Een flag argument geeft al automatisch aan dat dit in dit geval niet zo is.

Daarnaast maakt het een functie onnodig complex. Het vraagt om vijf argumenten! (Daar gaat het een ander keer over)

## General

### Processing: KeyEvent.java
Locatie: [KeyEvent.java](https://github.com/processing/processing/blob/master/core/src/processing/event/KeyEvent.java)  
Omvang: 1 t/m 1  
Smell Code: G1 Multiple Languages in One Source File

```java
/* -*- mode: java; c-basic-offset: 2; indent-tabs-mode: nil -*- */
```

_Figuur N: Processing KeyEvent.java_

#### Wat deugt niet?
Het gehele snippet. 

#### Waarom deugt het niet?


