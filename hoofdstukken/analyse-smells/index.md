# Analyse smells

## Comments

### 1. Processing: MouseEvent
Locatie: [ MouseEvent.java](https://github.com/processing/processing/blob/master/core/src/processing/event/MouseEvent.java)  
Omvang: 38 t/m 63  
Smell Code: C5

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