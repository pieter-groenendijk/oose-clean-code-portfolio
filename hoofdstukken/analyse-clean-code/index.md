# Analyse clean code

## Meaningful Names

## Functions

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

## Formatting

## Objects and Data Structures

## Error Handling

## Boundaries

## Unit Tests

## Classes

