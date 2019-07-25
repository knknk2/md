*   `String`: A Java String. Its default size depends on the underlying backend (if you use JPA, it's 255 by default), but you can change it using the validation rules (putting a `max` size of 1024, for example).
*   `Integer`: A Java Integer.
*   `Long`: A Java Long.
*   `Float`: A Java Float.
*   `Double`: A Java Double.
*   `BigDecimal`: A [java.math.BigDecimal](https://docs.oracle.com/javase/8/docs/api/java/math/BigDecimal.html) object, used when you want exact mathematic calculations (often used for financial operations).
*   `LocalDate`: A [java.time.LocalDate](https://docs.oracle.com/javase/8/docs/api/java/time/LocalDate.html) object, used to correctly manage dates in Java.
*   `Instant`: A [java.time.Instant](https://docs.oracle.com/javase/8/docs/api/java/time/Instant.html) object, used to represent a timestamp, an instantaneous point on the time-line.
*   `ZonedDateTime`: A [java.time.ZonedDateTime](https://docs.oracle.com/javase/8/docs/api/java/time/ZonedDateTime.html) object, used to represent a local date-time in a given timezone (typically a calendar appointment). Note that time zones are neither supported by the REST nor by the persistence layers so you should most probably use `Instant` instead.
*   `Duration`: A [java.time.Duration](https://docs.oracle.com/javase/8/docs/api/java/time/Duration.html) object, used to represent an amount of time.
*   `UUID`: A [java.util.UUID](https://docs.oracle.com/javase/8/docs/api/java/util/UUID.html).
*   `Boolean`: A Java Boolean.
*   `Enumeration`: A Java Enumeration object. When this type is selected, the sub-generator will ask you what values you want in your enumeration, and it will create a specific `enum` class to store them.
*   `Blob`: A Blob object, used to store some binary data. When this type is selected, the sub-generator will ask you if you want to store generic binary data, an image object, or a CLOB (long text).  
