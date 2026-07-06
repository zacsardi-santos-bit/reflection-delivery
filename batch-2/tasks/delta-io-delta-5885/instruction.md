I'm working on the Delta Lake kernel and I need to add support for two new spatial data types: geometry (for planar/cartesian spatial data) and geography (for geodetic/spherical spatial data).

*   GeometryType.ofDefault() must return a GeometryType whose SRID is 'OGC:CRS84'. GeometryType.ofSRID(srid) must return a GeometryType with the given SRID string.

*   GeometryType.toString() must return the string 'Geometry(srid=<srid>)' (e.g. 'Geometry(srid=OGC:CRS84)'). Two GeometryType instances with the same SRID must be equal.

*   Deserializing the JSON string 'geometry' (no parameters) must produce a GeometryType equal to GeometryType.ofDefault(). Serializing any GeometryType must always produce the full form 'geometry(<srid>)' — never the bare word 'geometry'.

*   Deserializing 'geometry(<srid>)' must produce GeometryType.ofSRID(srid). The SRID parameter must contain a colon (authority:code notation such as 'OGC:CRS84', 'EPSG:4326'). SRID matching is case-sensitive and preserves the original casing.

*   Deserializing 'geometry()' must throw an IllegalArgumentException with a message that contains 'geometry() is not a supported delta data type'. Deserializing 'geometry(<value>)' where the value contains no colon (e.g. 'geometry(noCollon)') must throw an IllegalArgumentException with a message containing '<typestring> is not a supported delta data type'. Deserializing 'geometry(<srid>, extra)' (two comma-separated parameters) must throw an IllegalArgumentException with a message containing the full type string followed by ' is not a supported delta data type'.

*   GeographyType(srid, algorithm) constructor must validate that algorithm is one of: spherical, vincenty, thomas, andoyer, karney. An invalid algorithm must throw IllegalArgumentException with a message that contains 'Algorithm must be one of: spherical, vincenty, thomas, andoyer, karney'.

*   GeographyType.ofDefault() must return a GeographyType with SRID='OGC:CRS84' and algorithm='spherical'. GeographyType.ofSRID(srid) must return a GeographyType with the given SRID and default algorithm 'spherical'. GeographyType.ofAlgorithm(algorithm) must return a GeographyType with default SRID 'OGC:CRS84' and the given algorithm.

*   GeographyType.toString() must return the string 'Geography(srid=<srid>, algorithm=<algorithm>)' (e.g. 'Geography(srid=OGC:CRS84, algorithm=spherical)'). Two GeographyType instances with the same SRID and algorithm must be equal.

*   Deserializing the JSON string 'geography' (no parameters) must produce a GeographyType equal to GeographyType.ofDefault(). Serializing any GeographyType must always produce the full form 'geography(<srid>, <algorithm>)' — never abbreviated forms.

*   Deserializing 'geography(<param>)' must determine whether the single parameter is an SRID or an algorithm: if the parameter contains a colon it is treated as an SRID (using default algorithm 'spherical'); if the parameter has no colon it is treated as an algorithm name (using default SRID 'OGC:CRS84').

*   Deserializing 'geography(<srid>, <algorithm>)' must produce new GeographyType(srid, algorithm). The SRID is the first parameter (contains a colon) and the algorithm is the second.

*   Deserializing 'geography()' must throw IllegalArgumentException with a message containing 'geography() is not a supported delta data type'. Deserializing 'geography(<srid>, <algorithm>, extra)' (three parameters) must throw IllegalArgumentException. Deserializing 'geography(<srid>,)' (trailing comma) must throw IllegalArgumentException.

*   Both GeometryType and GeographyType must be usable as element types in ArrayType and as field types within StructType, including nested combinations thereof.

*   When deserializing a geography type string whose single parameter has no colon and is not one of the valid algorithm names, an IllegalArgumentException must be thrown with a message containing 'Algorithm must be one of: spherical, vincenty, thomas, andoyer, karney'.


*   Interface details: Type: Class
Name: GeometryType
Location: kernel/kernel-api/src/main/java/io/delta/kernel/types/GeometryType.java
Description: Represents the geometry data type (planar spatial data) with a Spatial Reference System Identifier (SRID). Must extend DataType. Two instances are equal if and only if they have the same SRID.
Signature:
  public final class GeometryType extends DataType
  public static GeometryType ofDefault()             // SRID defaults to "OGC:CRS84"
  public static GeometryType ofSRID(String srid)     // specified SRID
  public String toString()                            // returns "Geometry(srid=<srid>)"
  public boolean isNested()                           // returns false
  public boolean equals(Object o)
  public int hashCode()

Type: Class
Name: GeographyType
Location: kernel/kernel-api/src/main/java/io/delta/kernel/types/GeographyType.java
Description: Represents the geography data type (geodetic/spherical spatial data) with a Spatial Reference System Identifier (SRID) and a geodetic calculation algorithm. Must extend DataType. Two instances are equal if and only if they have the same SRID and algorithm. Valid algorithms are: spherical, vincenty, thomas, andoyer, karney.
Signature:
  public final class GeographyType extends DataType
  public GeographyType(String srid, String algorithm) // public constructor; throws IllegalArgumentException if algorithm is not one of the valid values
  public static GeographyType ofDefault()             // SRID="OGC:CRS84", algorithm="spherical"
  public static GeographyType ofSRID(String srid)     // specified SRID, default algorithm="spherical"
  public static GeographyType ofAlgorithm(String algorithm) // default SRID="OGC:CRS84", specified algorithm
  public String toString()                            // returns "Geography(srid=<srid>, algorithm=<algorithm>)"
  public boolean isNested()                           // returns false
  public boolean equals(Object o)
  public int hashCode()

Type: File (modify existing)
Name: DataTypeJsonSerDe
Location: kernel/kernel-api/src/main/java/io/delta/kernel/internal/types/DataTypeJsonSerDe.java
Description: Existing serialization/deserialization class for Delta data types. Must be updated to parse and serialize the new GeometryType and GeographyType. The parsing logic determines which type to create based on the JSON string patterns. The serialization logic must write the full canonical string form for both types.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.