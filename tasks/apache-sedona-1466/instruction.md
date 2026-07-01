Implement two new geometry constructor functions in Apache Sedona across all supported backends. The first function should create a Point geometry with an M (measure) coordinate, and the second should construct a Linestring geometry from Well-Known Binary (WKB) data.

*   Implement `Constructors.makePointM` in the core Java library:
    *   Accepts three numeric arguments (x, y, m).
    *   Returns a Point geometry with WKT representation 'POINT M(x y m)'.
    *   Location: `common/src/main/java/org/apache/sedona/common/Constructors.java`.

*   Register and implement `ST_MakePointM` in Spark SQL:
    *   Accepts numeric inputs (including Decimal types).
    *   Returns a Point geometry with M coordinate; WKT: 'POINT M(x y m)'.

*   Implement `ST_MakePointM` in the Python DataFrame API:
    *   Accepts column references for x, y, and m.
    *   Returns null when inputs are null.
    *   Location: `python/sedona/sql/st_constructors.py`.

*   Implement `ST_MakePointM` in Apache Flink:
    *   As a table function in `Constructors.ST_MakePointM` inner class.
    *   Accepts (Double x, Double y, Double m) parameters.

*   Implement `ST_LinestringFromWKB` in Spark SQL:
    *   Accepts binary byte array or hex-encoded WKB string.
    *   Returns Linestring geometry or null for non-linestring WKBs.
    *   Throws an exception for invalid WKB input.
    *   Two-argument variant assigns SRID; EWKT output includes 'SRID=<value>;'.

*   Implement `ST_LinestringFromWKB` in the Python DataFrame API:
    *   Accepts a column of binary byte arrays or hex strings.
    *   Returns null for null inputs or non-linestring WKBs.
    *   Location: `python/sedona/sql/st_constructors.py`.

*   Implement `ST_LinestringFromWKB` in Apache Flink:
    *   As a table function in `Constructors.ST_LinestringFromWKB` inner class.
    *   Accepts byte array or hex string column.

*   Register `ST_LinestringFromWKB` UDFs in Snowflake:
    *   Single-argument variant for WKB bytes.
    *   Two-argument variant for WKB bytes with SRID; EWKT output includes 'SRID=<value>;'.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.