# Analyse smells

## 1. Comments

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


### 2. Processing: ThinkDifferent
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


### 3. Processing: Archiver.java
Locatie: [Archiver.java](https://github.com/processing/processing/blob/master/app/src/processing/app/tools/Archiver.java)  
Omvang: 41  
Smell Code: C1 Inappropriate Information

```java
    // someday these will be settable
boolean useDate;
int digits = 3;
```

_Figuur N: Processing Archiver.java_

#### Wat deugt er niet?
De comment.

#### Waarom deugt het niet?
De comment geeft duidelijk aan dat er dus een taak (de velden _settable_ maken) moet worden verricht. Het is veel
duidelijker en directer om dit rechtstreeks in _issue tracking system_ te zetten.

#### Oplossing
- Comment verwijderen
- Taak toevoegen in _issue tracking system_


## 2. Names

### 3. Processing: ThinkDifferent.Java
Locatie: [ThinkDifferent.Java](https://github.com/processing/processing/blob/master/core/src/processing/core/ThinkDifferent.java)  
Omvang: 1 t/m 69  
Smell Code: N1 Choose Descriptive Names

```java
public class ThinkDifferent {
```

_Figuur N: Processing ThinkDifferent.java_

#### Wat deugt er niet?
De naamgeving van de _class_ `ThinkDifferent`.

#### Waarom deugt het niet?
Deze naam is zo vaag als het maar kan. Ik kan enkel een idee vormen, hoewel beperkt, wat de klasse doet door de _JSDoc_ erbij te lezen.

#### Oplossing
De naam zal veranderd moeten worden naar iets wat de daadwerkelijke implementatie representeert. Op basis van de huidige implementatie
zou men uitkomen op de naam `OSXIntegrationHelper`. De klasse bezit namelijk methodes die helpen met de integratie specifiek voor het
_OS X_ platform.


### 4. Processing: JAppleMenuBar.java
Locatie: [JAppleMenuBar.java]()  
Omvang: 66 t/m 68  
Smell Code: N7 Names Should Describe Side-Effects

#### Wat deugt er niet?
De naamgeving van de functie `sadness()`.

#### Waarom deugt het niet?
Het is zeer simpel. Als de functie niet beschrijft wat de functie daadwerkelijke doet weet alleen de maker wat de functie
doet, totdat ook hij dat vergeet. Programmeurs zullen alleen weten wat de `sadness()` methode doet, als zij net de functie
definitie hebben doorgenomen.

#### Oplossing
Verander de naam van de functie naar `printFullscreenModeDisabledError`.



## 3. Functions

### 4. Processing: PVector.java
Locatie: [PVector.java](https://github.com/processing/processing/blob/master/core/src/processing/core/PVector.java)  
Omvang: 343 t/m 356  
Smell Code: F2 Ouput Arguments

```java
  /**
 * Make a new 2D unit vector from an angle
 *
 * @param target the target vector (if null, a new vector will be created)
 * @return the PVector
 */
static public PVector fromAngle(float angle, PVector target) {
    if (target == null) {
        target = new PVector((float)Math.cos(angle),(float)Math.sin(angle),0);
    } else {
        target.set((float)Math.cos(angle),(float)Math.sin(angle),0);
    }
    return target;
}
```

#### Wat deugt er niet?
Het gaat hier om het argument `PVector target`.

#### Waarom deugt het niet?
Het kan verwarrend zijn. Op dit moment is de lijn tussen _input_ en _output_ vaag. Dit kan voor verwarring zorgen.

#### Oplossing
```java
  static public PVector fromAngle(float angle) {
    return new PVector(
            (float)Math.cos(angle),
            (float)Math.sin(angle),
            0
    );
}

public void fromAngle(float angle) {
    this.set(
            (float)Math.cos(angle),
            (float)Math.sin(angle),
            )
}
```

Op deze manier wordt er geen gebruik gemaakt van _output arguments_.

Er is nogsteeds sprake van duplicatie. Dit is geisoleerd opgelost in een ander hoofdstuk.


### 5. Processing: PImage.java
Locatie: [PImage.java](https://github.com/processing/processing/blob/master/core/src/processing/core/PImage.java)  
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

_Figuur N: Processing PImage.java_

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

### 6. Processing: PApplet.java
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

#### Wat deugt niet?
De boolean argument `boolean primary`.

#### Waarom deugt het niet?
Een functie hoort 1 ding te doen. Een flag argument geeft al automatisch aan dat dit in dit geval niet zo is.

Daarnaast maakt het een functie onnodig complex. Het vraagt om vijf argumenten! (Daar gaat het een ander keer over)

#### Oplossing
TODO


### N: Processing: PApplet.java
Locatie [PApplet.jav](https://github.com/processing/processing/blob/master/core/src/processing/core/PApplet.java)  
Omvang: 1 t/m 13  
Smell Code: G30 Functions Should Do One Thing  

```java
public int displayDensity(int display) {
    if (PApplet.platform == PConstants.MACOSX) {
        // This should probably be reset each time there's a display change.
        // A 5-minute search didn't turn up any such event in the Java 7 API.
        // Also, should we use the Toolkit associated with the editor window?
        final String javaVendor = System.getProperty("java.vendor");
        if (javaVendor.contains("Oracle")) {
            GraphicsDevice device;
            GraphicsEnvironment env = GraphicsEnvironment.getLocalGraphicsEnvironment();

            if (display == -1) {
                device = env.getDefaultScreenDevice();

            } else if (display == SPAN) {
                throw new RuntimeException("displayDensity() only works with specific display numbers");

            } else {
                GraphicsDevice[] devices = env.getScreenDevices();
                if (display > 0 && display <= devices.length) {
                device = devices[display - 1];
                } else {
                if (devices.length == 1) {
                    System.err.println("Only one display is currently known, use displayDensity(1).");
                } else {
                    System.err.format("Your displays are numbered %d through %d, " +
                    "pass one of those numbers to displayDensity()%n", 1, devices.length);
                }
                throw new RuntimeException("Display " + display + " does not exist.");
            }
        }

        try {
            Field field = device.getClass().getDeclaredField("scale");
            if (field != null) {
                field.setAccessible(true);
                Object scale = field.get(device);

                if (scale instanceof Integer && ((Integer)scale).intValue() == 2) {
                    return 2;
                }
            }
        } catch (Exception ignore) { }
        }
    } else if (PApplet.platform == PConstants.WINDOWS || PApplet.platform == PConstants.LINUX) {
        if (suggestedDensity == -1) {
            // TODO: detect and return DPI scaling using JNA; Windows has
            //   a system-wide value, not sure how it works on Linux
            return 1;
        } else if (suggestedDensity == 1 || suggestedDensity == 2) {
            return suggestedDensity;
        }
    }
    return 1;
}
```

#### Wat deugt er niet?
De functie body

#### Waarom deugt het niet?
Ik denk dat het erg voor de hand liggend is om te zeggen dat deze functie meer dan een ding doet. De functie kan minimaal 
opgeslitst worden in de volgende stappen:
1. Stel vast of MACOSX is.
2. Als dat zo is, _return_ display density van een scherm binnen een Mac OS X omgeving.
3. Als dat niet zo was, stel vast of de omgeving WINDOWS of LINUX is.
4. Als dat zo is, _return_ display density van een scherm binnen een Windows of Linux omgeving.
5. Als dat niet zo was, ga uit van een _normal-density_ scherm.

#### Oplossing
```java
public int displayDensity(int display) {
    if (this.usingMacOsXPlatform()) {
        return displayDensityMacOsX(display);
    } else if (this.usingWindowsPlatform() || this.usingLinuxPlatform()) {
        return displayDensityWindowsOrLinux(display);
    } else {
        return 1;
    }
}

private bool usingMacOsXPlatform() {
    return PApplet.platform == PConstants.MACOSX;
}

private bool usingWindowsPlatform() {
    return PApplet.platform == PConstants.WINDOWS;
}

private bool usingLinuxPlatform() {
    return PApplet.platform == PConstants.LINUX;
}

private int displayDensityMacOsX(int display) {
    ...
}

private int displayDensityWindowsOrLinux(int display) {
    ...
}
```


## 4. General

### 7. Processing: KeyEvent.java
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
Dezelfde reden waarom boeken niet geschreven worden in meerdere talen. Het is simpelweg verwarrend om telkens je
anders moeten instellen om te begrijpen wat er staat.

#### Oplossing
Weghalen. Volgens ChatGPT zou deze _emacs_ configuratie ook in een extern bestand gezet kunnen worden.


### 8. Processing: PVector.java
Locatie: [PVector.java](https://github.com/processing/processing/blob/master/core/src/processing/core/PVector.java)  
Omvang: 343 t/m 356  
Smell Code: G5 Duplication

```java
  /**
 * Make a new 2D unit vector from an angle
 *
 * @param target the target vector (if null, a new vector will be created)
 * @return the PVector
 */
static public PVector fromAngle(float angle, PVector target) {
    if (target == null) {
        target = new PVector((float)Math.cos(angle),(float)Math.sin(angle),0);
    } else {
        target.set((float)Math.cos(angle),(float)Math.sin(angle),0);
    }
    return target;
}
```

#### Wat deugt niet?
De berekening van de `float x` en `float y` met middel van een `float angle` wordt exact herhaald. Het gaat dan om
de herhaalde stukken code: `(float)Math.cos(angle)` en `(float)Math.cos(angle)`.

#### Waarom deugt het niet?
In dit geval is het erg _error prone_. Elke keer dat je deze berekening opnieuw doet is er een goede kans dat je
het dit keer verkeerd uittypt. Daarnaast doordat je het niet geextraheerd hebt naar zijn eigen methode zal het
ook moeilijker unittestbaar zijn.

Los daarvan is het ook gewoon verspilde tijd om telkens dezelfde code opnieuw te schrijven.

#### Oplossing
```java
  static public float calculateXFromAngle(float angle) {
    return (float)Math.cos(angle);
}

static public float calculateYFromAngle(float angle) {
    return (float)Math.sin(angle);
}

/**
 * Make a new 2D unit vector from an angle
 *
 * @param target the target vector (if null, a new vector will be created)
 * @return the PVector
 */
static public PVector fromAngle(float angle, PVector target) {
    float x = this.calculateXFromAngle(angle);
    float y = this.calculateYFromAngle(angle);

    if (target == null) {
        target = new PVector(x, y, 0);
    } else {
        target.set(x, y, 0);
    }

    return target;
}
```

De berekeningen van zowel `x` en `y` zijn nu gescheiden in een eigen methode. Verder is de code, hoewel die niet perfect is,
behouden hoe die eerder was.

De duplicatie is zo afgenomen.


### N. Processing: PImage.java
Locatie: [PImage.java](https://github.com/processing/processing/blob/master/core/src/processing/core/PImage.java)
Omvang: 438 t/m 443
Smell Code: G28: Encapsulate Conditionals

```java
public void loadPixels() {  // ignore
    if (pixels == null || pixels.length != pixelWidth*pixelHeight) {
        pixels = new int[pixelWidth*pixelHeight];
    }
    setLoaded();
}
```

#### Wat deugt er niet?
De conditional `pixels == null || pixels.length != pixelWidth*pixelHeight`.

#### Waarom deugt het niet?
Boolean logica is moelijk genoeg te begrijpen. Elke keer dat iemand deze functie langsloopt is er sprake van de
_mental overhead_ om de conditional te begrijpen.

#### Oplossing
Extraheer de conditional naar zijn eigen functie, namelijk:

```java
public void loadPixels() {  // ignore
    if (!isPixelsArrayInitialized()) {
        pixels = new int[pixelWidth*pixelHeight];
    }
    setLoaded();
}

private boolean isPixelsArrayInitialized() {
    return pixels == null || pixels.length != pixelWidth*pixelHeight;
}
```


### Processing: PApplet.java
Locatie: [PApplet.java]()  
Omvang: 2393 t/m 2517
Smell Code: G9 Dead Code

```java
public void handleDraw() {
    //debug("handleDraw() " + g + " " + looping + " " + redraw + " valid:" + this.isValid() + " visible:" + this.isVisible());

    // canDraw = g != null && (looping || redraw);
    if (g == null) return;
    if (!looping && !redraw) return;
//    System.out.println("looping/redraw = " + looping + " " + redraw);

    // no longer in use by any of our renderers
//    if (!g.canDraw()) {
//      debug("g.canDraw() is false");
//      // Don't draw if the renderer is not yet ready.
//      // (e.g. OpenGL has to wait for a peer to be on screen)
//      return;
//    }

    // Store the quality setting in case it's changed during draw and the
    // drawing context needs to be re-built before the next frame.
//    int pquality = g.smooth;

    if (insideDraw) {
        System.err.println("handleDraw() called before finishing");
        System.exit(1);
    }

    insideDraw = true;
    g.beginDraw();
    if (recorder != null) {
        recorder.beginDraw();
    }

    long now = System.nanoTime();

    if (frameCount == 0) {
        // 3.0a5 should be no longer needed; handled by PSurface
        //surface.checkDisplaySize();

//        try {
        //println("Calling setup()");
        setup();
        //println("Done with setup()");

//        } catch (RendererChangeException e) {
//          // Give up, instead set the new renderer and re-attempt setup()
//          return;
//        }
//      defaultSize = false;

    } else {  // frameCount > 0, meaning an actual draw()
        // update the current frameRate

        // Calculate frameRate through average frame times, not average fps, e.g.:
        //
        // Alternating 2 ms and 20 ms frames (JavaFX or JOGL sometimes does this)
        // is around 90.91 fps (two frames in 22 ms, one frame 11 ms).
        //
        // However, averaging fps gives us: (500 fps + 50 fps) / 2 = 275 fps.
        // This is because we had 500 fps for 2 ms and 50 fps for 20 ms, but we
        // counted them with equal weight.
        //
        // If we average frame times instead, we get the right result:
        // (2 ms + 20 ms) / 2 = 11 ms per frame, which is 1000/11 = 90.91 fps.
        //
        // The counter below uses exponential moving average. To do the
        // calculation, we first convert the accumulated frame rate to average
        // frame time, then calculate the exponential moving average, and then
        // convert the average frame time back to frame rate.
        {
            // Get the frame time of the last frame
            double frameTimeSecs = (now - frameRateLastNanos) / 1e9;
            // Convert average frames per second to average frame time
            double avgFrameTimeSecs = 1.0 / frameRate;
            // Calculate exponential moving average of frame time
            final double alpha = 0.05;
            avgFrameTimeSecs = (1.0 - alpha) * avgFrameTimeSecs + alpha * frameTimeSecs;
            // Convert frame time back to frames per second
            frameRate = (float) (1.0 / avgFrameTimeSecs);
        }

        if (frameCount != 0) {
            handleMethods("pre");
        }

        // use dmouseX/Y as previous mouse pos, since this is the
        // last position the mouse was in during the previous draw.
        pmouseX = dmouseX;
        pmouseY = dmouseY;

        //println("Calling draw()");
        draw();
        //println("Done calling draw()");

        // dmouseX/Y is updated only once per frame (unlike emouseX/Y)
        dmouseX = mouseX;
        dmouseY = mouseY;

        // these are called *after* loop so that valid
        // drawing commands can be run inside them. it can't
        // be before, since a call to background() would wipe
        // out anything that had been drawn so far.
        dequeueEvents();

        handleMethods("draw");

        redraw = false;  // unset 'redraw' flag in case it was set
        // (only do this once draw() has run, not just setup())
    }
    g.endDraw();

//    if (pquality != g.smooth) {
//      surface.setSmooth(g.smooth);
//    }

    if (recorder != null) {
        recorder.endDraw();
    }
    insideDraw = false;

    if (frameCount != 0) {
        handleMethods("post");
    }

    frameRateLastNanos = now;
    frameCount++;
}
```

#### Wat deugt er niet?
Het gaat hier om de conditional `if (frameCount != 0)` (regel 2472) en de code daarbinnen.

#### Waarom deugt het niet?
Het is simpelweg code dat nooit uitgevoerd zal worden. de `if` statement controleert een conditie die niet waar
kan zijn in de context. Het maakt de code moeilijker te begrijpen.

#### Oplossing
Verwijder de besproken conditional en bijhorende code block.


## 5. Java
### 9. Processing: Webserver.java
Locatie: [Webserver.java](https://github.com/processing/processing/blob/master/app/src/processing/app/WebServer.java)  
Omvang: 17 en 529 t/m 572  
Smell Code: J2 Don't inherit Constants

```java
public class WebServer implements HttpConstants {
```

_Figuur N: Processing Webserver.java_

```java
interface HttpConstants {
    /** 2XX: generally "OK" */
    public static final int HTTP_OK = 200;
    public static final int HTTP_CREATED = 201;
    public static final int HTTP_ACCEPTED = 202;
    public static final int HTTP_NOT_AUTHORITATIVE = 203;
    public static final int HTTP_NO_CONTENT = 204;
    public static final int HTTP_RESET = 205;
    public static final int HTTP_PARTIAL = 206;

    /** 3XX: relocation/redirect */
    public static final int HTTP_MULT_CHOICE = 300;
    public static final int HTTP_MOVED_PERM = 301;
    public static final int HTTP_MOVED_TEMP = 302;
    public static final int HTTP_SEE_OTHER = 303;
    public static final int HTTP_NOT_MODIFIED = 304;
    public static final int HTTP_USE_PROXY = 305;

    /** 4XX: client error */
    public static final int HTTP_BAD_REQUEST = 400;
    public static final int HTTP_UNAUTHORIZED = 401;
    public static final int HTTP_PAYMENT_REQUIRED = 402;
    public static final int HTTP_FORBIDDEN = 403;
    public static final int HTTP_NOT_FOUND = 404;
    public static final int HTTP_BAD_METHOD = 405;
    public static final int HTTP_NOT_ACCEPTABLE = 406;
    public static final int HTTP_PROXY_AUTH = 407;
    public static final int HTTP_CLIENT_TIMEOUT = 408;
    public static final int HTTP_CONFLICT = 409;
    public static final int HTTP_GONE = 410;
    public static final int HTTP_LENGTH_REQUIRED = 411;
    public static final int HTTP_PRECON_FAILED = 412;
    public static final int HTTP_ENTITY_TOO_LARGE = 413;
    public static final int HTTP_REQ_TOO_LONG = 414;
    public static final int HTTP_UNSUPPORTED_TYPE = 415;

    /** 5XX: server error */
    public static final int HTTP_SERVER_ERROR = 500;
    public static final int HTTP_INTERNAL_ERROR = 501;
    public static final int HTTP_BAD_GATEWAY = 502;
    public static final int HTTP_UNAVAILABLE = 503;
    public static final int HTTP_GATEWAY_TIMEOUT = 504;
    public static final int HTTP_VERSION = 505;
}
```

_Figuur N: Processing Webserver.java_

#### Wat deugt niet?
De manier hoe _WebServer_ klasse gebruik maakt van de constanten.

#### Waarom deugt het niet?
De constanten zijn helemaal verstopt bovenin de _inheritance hierarchy_. Zover mogelijk verstopt dus. Je bent niks daadwerkelijk
aan het implementeren. De constanten staan puur in een _interface_ omdat dit eenmaal gemakkelijk is aangezien elk veld
in een _interface_ standaard `public static final` is. Echter moet deze dan niet daadwerkelijker gebruikt worden als iets
wat geïmplementeerd moet worden.

#### Oplossing
In dit geval kan de `implements HttpConstants` weggehaald worden. De interface staat al in hetzelfde bestand. Normaliter
wanneer dit niet het geval is kan een static import gebruikt worden zoals `import static processing.app.HttpConstants.*`.


### 10. Processing Webserver.java
Locatie: [Webserver.java](https://github.com/processing/processing/blob/master/app/src/processing/app/WebServer.java)   
Omvang: 529 t/m 572  
Smell Code: J3 Constants versus Enums

```java
interface HttpConstants {
    /** 2XX: generally "OK" */
    public static final int HTTP_OK = 200;
    public static final int HTTP_CREATED = 201;
    public static final int HTTP_ACCEPTED = 202;
    public static final int HTTP_NOT_AUTHORITATIVE = 203;
    public static final int HTTP_NO_CONTENT = 204;
    public static final int HTTP_RESET = 205;
    public static final int HTTP_PARTIAL = 206;

    /** 3XX: relocation/redirect */
    public static final int HTTP_MULT_CHOICE = 300;
    public static final int HTTP_MOVED_PERM = 301;
    public static final int HTTP_MOVED_TEMP = 302;
    public static final int HTTP_SEE_OTHER = 303;
    public static final int HTTP_NOT_MODIFIED = 304;
    public static final int HTTP_USE_PROXY = 305;

    /** 4XX: client error */
    public static final int HTTP_BAD_REQUEST = 400;
    public static final int HTTP_UNAUTHORIZED = 401;
    public static final int HTTP_PAYMENT_REQUIRED = 402;
    public static final int HTTP_FORBIDDEN = 403;
    public static final int HTTP_NOT_FOUND = 404;
    public static final int HTTP_BAD_METHOD = 405;
    public static final int HTTP_NOT_ACCEPTABLE = 406;
    public static final int HTTP_PROXY_AUTH = 407;
    public static final int HTTP_CLIENT_TIMEOUT = 408;
    public static final int HTTP_CONFLICT = 409;
    public static final int HTTP_GONE = 410;
    public static final int HTTP_LENGTH_REQUIRED = 411;
    public static final int HTTP_PRECON_FAILED = 412;
    public static final int HTTP_ENTITY_TOO_LARGE = 413;
    public static final int HTTP_REQ_TOO_LONG = 414;
    public static final int HTTP_UNSUPPORTED_TYPE = 415;

    /** 5XX: server error */
    public static final int HTTP_SERVER_ERROR = 500;
    public static final int HTTP_INTERNAL_ERROR = 501;
    public static final int HTTP_BAD_GATEWAY = 502;
    public static final int HTTP_UNAVAILABLE = 503;
    public static final int HTTP_GATEWAY_TIMEOUT = 504;
    public static final int HTTP_VERSION = 505;
}
```

_Figuur N: Processing Webserver.java_

#### Wat deugt er niet?
De gehele interface.

#### Waarom deugt het niet?
Zoals eerder al genoemd, een interface implementeer je. Je implementeert geen constanten, je gebruikt ze. Hier is dus
geen inheritance voor nodig.

#### Oplossing
```java
public enum HTTPStatus {
    OK(200),
    CREATED(201),
    ACCEPTED(202),
    NOT_AUTHORITATIVE(203),
    NO_CONTENT(204),
    RESET(205),
    PARTIAL(206),

    MULT_CHOICE(300),
    MOVED_PERM(301),
    MOVED_TEMP(302),
    SEE_OTHER(303),
    NOT_MODIFIED(304),
    USE_PROXY(305),

    BAD_REQUEST(400),
    UNAUTHORIZED(401),
    PAYMENT_REQUIRED(402),
    FORBIDDEN(403),
    NOT_FOUND(404),
    BAD_METHOD(405),
    NOT_ACCEPTABLE(406),
    PROXY_AUTH(407),
    CLIENT_TIMEOUT(408),
    CONFLICT(409),
    GONE(410),
    LENGTH_REQUIRED(411),
    PRECON_FAILED(412),
    ENTITY_TOO_LARGE(413),
    REQ_TOO_LONG(414),
    UNSUPPORTED_TYPE(415),

    SERVER_ERROR(500),
    INTERNAL_ERROR(501),
    BAD_GATEWAY(502),
    UNAVAILABLE(503),
    GATEWAY_TIMEOUT(504),
    VERSION(505);

    private final int code;

    HttpStatus(int code) {
        this.code = code;
    }

    public int code() {
        return this.code;
    }
}
```

