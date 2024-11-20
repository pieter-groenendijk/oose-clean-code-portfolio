# Analyse clean code

## Meaningful Names

### Apache Commons: Range.java
Locatie: [Range.java](https://github.com/apache/commons-lang/blob/master/src/main/java/org/apache/commons/lang3/Range.java)    
Omvang:  163 t/m 184  
Clean Code Regel: Use Intention-Revealing Names  

```java
/**
 * Creates a range with the specified minimum and maximum values (both inclusive).
 *
 * <p>The range uses the specified {@link Comparator} to determine where
 * values lie in the range.</p>
 *
 * <p>The arguments may be passed in the order (min,max) or (max,min).
 * The getMinimum and getMaximum methods will return the correct values.</p>
 *
 * @param <T> the type of the elements in this range
 * @param fromInclusive  the first value that defines the edge of the range, inclusive
 * @param toInclusive  the second value that defines the edge of the range, inclusive
 * @param comparator  the comparator to be used, null for natural ordering
 * @return the range object, not null
 * @throws NullPointerException when fromInclusive is null.
 * @throws NullPointerException when toInclusive is null.
 * @throws ClassCastException if using natural ordering and the elements are not {@link Comparable}
 * @since 3.13.0
 */
public static <T> Range<T> of(final T fromInclusive, final T toInclusive, final Comparator<T> comparator) {
    return new Range<>(fromInclusive, toInclusive, comparator);
}
```

#### Wat deugt er?
Zeer duidelijke naamgeving van de functie, en nog meer van de variabelen.

#### Waarom deugt het?
##### Functienaam
De functienaam `of(...)` lijkt misschien op eerste gezicht een onduidelijke functie naam. Echter is dat niet hoe je hem zal aanroepen. Een daadwerkelijke realistische aanroep zal eruit
zien als `Range.of(2, 10, Integer::compare)`. Het maakt gelijk duidelijk dat je een range aanmaakt. Het leest als een zin: "Een range van" ...

##### Parameternamen
De parameternamen `fromInclusive` en `toInclusive` maken het zeer duidelijk waar de boundaries liggen van de range die we aanmaken.
Een versimpeling zou `from` en `to` kunnen zijn. Echter is het met de gekozen namen gelijk duidelijk, er kan moeilijk verwarring 
plaatsvinden. 

## Functions

### Apache Commons: CharUtils.java
Locatie: [CharUtils.java](https://github.com/apache/commons-lang/blob/master/src/main/java/org/apache/commons/lang3/CharUtils.java)  
Omvang: 80 t/m 173  
Clean Code Regel: Command Query Separation  

```java
/**
 * Checks whether the character is ASCII 7 bit.
 *
 * <pre>
 *   CharUtils.isAscii('a')  = true
 *   CharUtils.isAscii('A')  = true
 *   CharUtils.isAscii('3')  = true
 *   CharUtils.isAscii('-')  = true
 *   CharUtils.isAscii('\n') = true
 *   CharUtils.isAscii('&copy;') = false
 * </pre>
 *
 * @param ch  the character to check
 * @return true if less than 128
 */
public static boolean isAscii(final char ch) {
    return ch < 128;
}

/**
 * Checks whether the character is ASCII 7 bit alphabetic.
 *
 * <pre>
 *   CharUtils.isAsciiAlpha('a')  = true
 *   CharUtils.isAsciiAlpha('A')  = true
 *   CharUtils.isAsciiAlpha('3')  = false
 *   CharUtils.isAsciiAlpha('-')  = false
 *   CharUtils.isAsciiAlpha('\n') = false
 *   CharUtils.isAsciiAlpha('&copy;') = false
 * </pre>
 *
 * @param ch  the character to check
 * @return true if between 65 and 90 or 97 and 122 inclusive
 */
public static boolean isAsciiAlpha(final char ch) {
    return isAsciiAlphaUpper(ch) || isAsciiAlphaLower(ch);
}

/**
 * Checks whether the character is ASCII 7 bit alphabetic lower case.
 *
 * <pre>
 *   CharUtils.isAsciiAlphaLower('a')  = true
 *   CharUtils.isAsciiAlphaLower('A')  = false
 *   CharUtils.isAsciiAlphaLower('3')  = false
 *   CharUtils.isAsciiAlphaLower('-')  = false
 *   CharUtils.isAsciiAlphaLower('\n') = false
 *   CharUtils.isAsciiAlphaLower('&copy;') = false
 * </pre>
 *
 * @param ch  the character to check
 * @return true if between 97 and 122 inclusive
 */
public static boolean isAsciiAlphaLower(final char ch) {
    return ch >= 'a' && ch <= 'z';
}

/**
 * Checks whether the character is ASCII 7 bit numeric.
 *
 * <pre>
 *   CharUtils.isAsciiAlphanumeric('a')  = true
 *   CharUtils.isAsciiAlphanumeric('A')  = true
 *   CharUtils.isAsciiAlphanumeric('3')  = true
 *   CharUtils.isAsciiAlphanumeric('-')  = false
 *   CharUtils.isAsciiAlphanumeric('\n') = false
 *   CharUtils.isAsciiAlphanumeric('&copy;') = false
 * </pre>
 *
 * @param ch  the character to check
 * @return true if between 48 and 57 or 65 and 90 or 97 and 122 inclusive
 */
public static boolean isAsciiAlphanumeric(final char ch) {
    return isAsciiAlpha(ch) || isAsciiNumeric(ch);
}

/**
 * Checks whether the character is ASCII 7 bit alphabetic upper case.
 *
 * <pre>
 *   CharUtils.isAsciiAlphaUpper('a')  = false
 *   CharUtils.isAsciiAlphaUpper('A')  = true
 *   CharUtils.isAsciiAlphaUpper('3')  = false
 *   CharUtils.isAsciiAlphaUpper('-')  = false
 *   CharUtils.isAsciiAlphaUpper('\n') = false
 *   CharUtils.isAsciiAlphaUpper('&copy;') = false
 * </pre>
 *
 * @param ch  the character to check
 * @return true if between 65 and 90 inclusive
 */
public static boolean isAsciiAlphaUpper(final char ch) {
    return ch >= 'A' && ch <= 'Z';
}
```

#### Wat deugt er?
De functiedefinities

#### Waarom deugt het?
Al deze functie definities maken het duidelijk dat ze iets beantwoorden, **en dat doen
ze dan ook**. Functies zijn duidelijk gescheiden in _doe_ en _beantwoord_ functies, ze
worden niet gemengt. 

### Apache Commons: AnnotationUtils.java
Locatie: [AnnotationUtils.java]()  
Omvang: 71 t/m 76  
Clean Code Regel: Have No Side Effects  

```java
/**
 * {@inheritDoc}
 */
@Override
protected void appendDetail(final StringBuffer buffer, final String fieldName, Object value) {
    if (value instanceof Annotation) {
        value = AnnotationUtils.toString((Annotation) value);
    }
    super.appendDetail(buffer, fieldName, value);
}
```

#### Wat deugt er?
De functie body.

#### Waarom deugt het?
De functie body zorgt niet voor side-effects. De functie zorgt er alleen voor dat het doel beschreven in de naam, namelijk
`appendDetail` gedaan wordt, dat is de enige consequentie van het uitvoeren van de functie.

Niet logische consequenties van een functie kunnen voor verwarring zorgen, en verwarring zorgt voor bugs. Daarom is het
goed dat deze functie alleen zorgt voor één logische consequentie.

### Guava: ClassPath.java
Locatie: [ClassPath.java](https://github.com/google/guava/blob/master/guava/src/com/google/common/reflect/ClassPath.java)  
Omvang: 377 t/m 384  
Clean Code Regel: Prefer Exceptions to Returning Error Codes  

```java
public Class<?> load() {
    try {
        return loader.loadClass(className);
    } catch (ClassNotFoundException e) {
    // Shouldn't happen, since the class name is read from the class path.
        throw new IllegalStateException(e);
    }
}
```

#### Wat deugt er?
Het gebruik van exceptions. 

#### Waarom deugt het?
Door exceptions te gebruiken is er geen behoefte voor if statements die de code
minder leesbaarder maken. Het scheidt de _happy path_ duidelijk van _alternative_
en/of mogelijke _exceptional_ flows.


## Comments

### Apache Commons: StringUtils.java
Locatie: [StringUtils.java](https://github.com/apache/commons-lang/blob/master/src/main/java/org/apache/commons/lang3/StringUtils.java)  
Omvang: 604 t/m 644  
Clean Code Regel: Javadocs in Public APIs  

```java
/**
 * Centers a String in a larger String of size {@code size}.
 * Uses a supplied String as the value to pad the String with.
 *
 * <p>If the size is less than the String length, the String is returned.
 * A {@code null} String returns {@code null}.
 * A negative size is treated as zero.</p>
 *
 * <pre>
 * StringUtils.center(null, *, *)     = null
 * StringUtils.center("", 4, " ")     = "    "
 * StringUtils.center("ab", -1, " ")  = "ab"
 * StringUtils.center("ab", 4, " ")   = " ab "
 * StringUtils.center("abcd", 2, " ") = "abcd"
 * StringUtils.center("a", 4, " ")    = " a  "
 * StringUtils.center("a", 4, "yz")   = "yayz"
 * StringUtils.center("abc", 7, null) = "  abc  "
 * StringUtils.center("abc", 7, "")   = "  abc  "
 * </pre>
 *
 * @param str  the String to center, may be null
 * @param size  the int size of new String, negative treated as zero
 * @param padStr  the String to pad the new String with, must not be null or empty
 * @return centered String, {@code null} if null String input
 * @throws IllegalArgumentException if padStr is {@code null} or empty
 */
public static String center(String str, final int size, String padStr) {
    if (str == null || size <= 0) {
        return str;
    }
    if (isEmpty(padStr)) {
        padStr = SPACE;
    }
    final int strLen = str.length();
    final int pads = size - strLen;
    if (pads <= 0) {
        return str;
    }
    str = leftPad(str, strLen + pads / 2, padStr);
    return rightPad(str, size, padStr);
}
```

#### Wat deugt er?
Duidelijke Javadoc comment (604 t/m 629).

#### Waarom deugt het?
De javadoc comment legt duidelijk en accuraat uit wat de functie doet. Dat is super nuttig als je deze package gaat gebruiken in je eigen code. Geen onzekerheden wat de 
code doet.

### Guava: Files.java
Locatie: [Files.java]()  
Omvang: 375 t/m 393
Clean Code Regel: Good Comments: Explanation of Intent

```java
public static boolean equal(File file1, File file2) throws IOException {
    checkNotNull(file1);
    checkNotNull(file2);
    if (file1 == file2 || file1.equals(file2)) {
        return true;
    }

    /*
     * Some operating systems may return zero as the length for files denoting system-dependent
     * entities such as devices or pipes, in which case we must fall back on comparing the bytes
     * directly.
     */
    long len1 = file1.length();
    long len2 = file2.length();
    if (len1 != 0 && len2 != 0 && len1 != len2) {
        return false;
    }
    return asByteSource(file1).contentEquals(asByteSource(file2));
}
```

#### Wat deugt er?
De comment.

#### Waarom deugt het?
Het legt uit waarom ervoor gekozen is om niet altijd van uit de file lengths uit te gaan. Het beschrijft het feit dat 
wellicht inaccuraat de file lengths beide 0 zouden kunnen zijn. 

De comment zorgt ervoor dat er gemakkelijker begrepen wordt waarom een keuze gemaakt is binnen de code.



## Formatting

### Guava: Booleans.java
Locatie: [Booleans.java](https://github.com/google/guava/blob/master/guava/src/com/google/common/primitives/Booleans.java)  
Omvang: 237 t/m 249
Clean Code Regel: Vertical Distance

```java
public static boolean[] concat(boolean[]... arrays) {
    long length = 0;
    for (boolean[] array : arrays) {
        length += array.length;
    }
    boolean[] result = new boolean[checkNoOverflow(length)];
    int pos = 0;
    for (boolean[] array : arrays) {
        System.arraycopy(array, 0, result, pos, array.length);
        pos += array.length;
    }
    return result;
}
```

#### Wat deugt er?
De verticale afstand tussen de declaratie en gebruik van lokale variabelen binnen de functie.

#### Waarom deugt het? 
De lokale variabelen `length`, `result` en `pos` worden zo dicht mogelijk bij hun daadwerkelijke 
gebruik geplaatst. Door dit principe toe te passen blijft de code goed leesbaar. Men kan in 1 beeld 
de code aflezen.



## Objects and Data Structures

### Apache Commons: RandomStringUtils.java
Locatie: [RandomStringUtils.java]()  
Omvang: 251 t/m 378
Clean Code Regel: Law of Demeter

```java
    /**
 * Creates a random string based on a variety of options, using supplied source of randomness.
 *
 * <p>
 * If start and end are both {@code 0}, start and end are set to {@code ' '} and {@code 'z'}, the ASCII printable
 * characters, will be used, unless letters and numbers are both {@code false}, in which case, start and end are set
 * to {@code 0} and {@link Character#MAX_CODE_POINT}.
 *
 * <p>
 * If set is not {@code null}, characters between start and end are chosen.
 * </p>
 *
 * <p>
 * This method accepts a user-supplied {@link Random} instance to use as a source of randomness. By seeding a single
 * {@link Random} instance with a fixed seed and using it for each call, the same random sequence of strings can be
 * generated repeatedly and predictably.
 * </p>
 *
 * @param count   the length of random string to create
 * @param start   the position in set of chars to start at (inclusive)
 * @param end     the position in set of chars to end before (exclusive)
 * @param letters if {@code true}, generated string may include alphabetic characters
 * @param numbers if {@code true}, generated string may include numeric characters
 * @param chars   the set of chars to choose randoms from, must not be empty. If {@code null}, then it will use the
 *                set of all chars.
 * @param random  a source of randomness.
 * @return the random string
 * @throws ArrayIndexOutOfBoundsException if there are not {@code (end - start) + 1} characters in the set array.
 * @throws IllegalArgumentException       if {@code count} &lt; 0 or the provided chars array is empty.
 * @since 2.0
 */
public static String random(int count, int start, int end, final boolean letters, final boolean numbers,
                            final char[] chars, final Random random) {
    if (count == 0) {
        return StringUtils.EMPTY;
    }
    if (count < 0) {
        throw new IllegalArgumentException("Requested random string length " + count + " is less than 0.");
    }
    if (chars != null && chars.length == 0) {
        throw new IllegalArgumentException("The chars array must not be empty");
    }

    if (start == 0 && end == 0) {
        if (chars != null) {
            end = chars.length;
        } else if (!letters && !numbers) {
            end = Character.MAX_CODE_POINT;
        } else {
            end = 'z' + 1;
            start = ' ';
        }
    } else if (end <= start) {
        throw new IllegalArgumentException(
                "Parameter end (" + end + ") must be greater than start (" + start + ")");
    } else if (start < 0 || end < 0) {
        throw new IllegalArgumentException("Character positions MUST be >= 0");
    }

    if (end > Character.MAX_CODE_POINT) {
        // Technically, it should be `Character.MAX_CODE_POINT+1` as `end` is excluded
        // But the character `Character.MAX_CODE_POINT` is private use, so it would anyway be excluded
        end = Character.MAX_CODE_POINT;
    }

    // Optimizations and tests when chars == null and using ASCII characters (end <= 0x7f)
    if (chars == null && end <= 0x7f) {
        // Optimize generation of full alphanumerical characters
        // Normally, we would need to pick a 7-bit integer, since gap = 'z' - '0' + 1 = 75 > 64
        // In turn, this would make us reject the sampling with probability 1 - 62 / 2^7 > 1 / 2
        // Instead we can pick directly from the right set of 62 characters, which requires
        // picking a 6-bit integer and only rejecting with probability 2 / 64 = 1 / 32
        if (letters && numbers && start <= ASCII_0 && end >= ASCII_z + 1) {
            return random(count, 0, 0, false, false, ALPHANUMERICAL_CHARS, random);
        }

        if (numbers && end <= ASCII_0 || letters && end <= ASCII_A) {
            throw new IllegalArgumentException(
                    "Parameter end (" + end + ") must be greater then (" + ASCII_0 + ") for generating digits "
                            + "or greater then (" + ASCII_A + ") for generating letters.");
        }

        // Optimize start and end when filtering by letters and/or numbers:
        // The range provided may be too large since we filter anyway afterward.
        // Note the use of Math.min/max (as opposed to setting start to '0' for example),
        // since it is possible the range start/end excludes some of the letters/numbers,
        // e.g., it is possible that start already is '1' when numbers = true, and start
        // needs to stay equal to '1' in that case.
        // Note that because of the above test, we will always have start < end
        // even after this optimization.
        if (letters && numbers) {
            start = Math.max(ASCII_0, start);
            end = Math.min(ASCII_z + 1, end);
        } else if (numbers) {
            // just numbers, no letters
            start = Math.max(ASCII_0, start);
            end = Math.min(ASCII_9 + 1, end);
        } else if (letters) {
            // just letters, no numbers
            start = Math.max(ASCII_A, start);
            end = Math.min(ASCII_z + 1, end);
        }
    }

    final StringBuilder builder = new StringBuilder(count);
    final int gap = end - start;
    final int gapBits = Integer.SIZE - Integer.numberOfLeadingZeros(gap);
    // The size of the cache we use is an heuristic:
    // about twice the number of bytes required if no rejection
    // Ideally the cache size depends on multiple factor, including the cost of generating x bytes
    // of randomness as well as the probability of rejection. It is however not easy to know
    // those values programmatically for the general case.
    final CachedRandomBits arb = new CachedRandomBits((count * gapBits + 3) / 5 + 10, random);

    while (count-- != 0) {
        // Generate a random value between start (included) and end (excluded)
        final int randomValue = arb.nextBits(gapBits) + start;
        // Rejection sampling if value too large
        if (randomValue >= end) {
            count++;
            continue;
        }

        final int codePoint;
        if (chars == null) {
            codePoint = randomValue;

            switch (Character.getType(codePoint)) {
                case Character.UNASSIGNED:
                case Character.PRIVATE_USE:
                case Character.SURROGATE:
                    count++;
                    continue;
            }

        } else {
            codePoint = chars[randomValue];
        }

        final int numberOfChars = Character.charCount(codePoint);
        if (count == 0 && numberOfChars > 1) {
            count++;
            continue;
        }

        if (letters && Character.isLetter(codePoint) || numbers && Character.isDigit(codePoint)
                || !letters && !numbers) {
            builder.appendCodePoint(codePoint);

            if (numberOfChars == 2) {
                count--;
            }

        } else {
            count++;
        }
    }
    return builder.toString();
}
```

#### Wat deugt er?
De gehele body van de functie.

#### Waarom deugt het?
De gehele body van de functie volgt de _law of demeter_. De functie roept alleen maar functies aan van:
- De klasse zelf
- Een object gemaakt door de functie
- Een object doorgegeven als parameter aan de functie
- Een object opgeslagen (als referentie) in de klasse



## Error Handling

## Boundaries

## Unit Tests

## Classes

