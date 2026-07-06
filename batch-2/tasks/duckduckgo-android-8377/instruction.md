I'm working on improving the telemetry sent by the PIR scan feature and need help with two related changes.

*   The manufacturer value added as a query parameter to PIR pixel requests must be normalized: the raw manufacturer string is converted to lowercase, then checked against a set of known common manufacturers. If the lowercased value is in the common set, it is used as-is; if not, the string 'other' is sent instead.

*   The set of recognized common manufacturers must include at minimum: 'samsung', 'google', 'xiaomi', 'huawei', 'honor', 'oneplus', 'oppo', 'vivo', 'motorola', 'realme', 'sony', 'lg', 'nokia', 'lenovo', 'asus'. Any manufacturer not in this set must be reported as 'other'.

*   The reportManualScanCompleted method in PirPixelSender must accept three additional parameters after the existing four: profileQueryCount (Int), brokerCount (Int), and isPowerSavingEnabled (Boolean) — for a total of 7 parameters.

*   When reportManualScanCompleted fires a pixel, the parameters map must include 'profile_queries' mapped to profileQueryCount as a string, 'broker_count' mapped to brokerCount as a string, and 'power_saving' mapped to isPowerSavingEnabled as a string.

*   The reportInitialScanDuration method in PirPixelSender must accept three additional parameters after the existing two (durationMs, profileQueryCount): isPowerSavingEnabled (Boolean), batteryOptimizationsEnabled (Boolean), and brokerCount (Int) — for a total of 5 parameters in that order.

*   When reportInitialScanDuration fires a pixel, the parameters map must include 'power_saving' mapped to isPowerSavingEnabled as a string, 'battery-optimizations' mapped to batteryOptimizationsEnabled as a string, and 'broker_count' mapped to brokerCount as a string.

*   All call sites that invoke reportManualScanCompleted must be updated to pass the three new arguments (profileQueryCount, brokerCount, isPowerSavingEnabled).

*   All call sites that invoke reportInitialScanDuration must be updated to pass the three new arguments (isPowerSavingEnabled, batteryOptimizationsEnabled, brokerCount) after the existing durationMs and profileQueryCount arguments.


*   Interface details: Type: Interface method
Name: reportManualScanCompleted
Location: pir/pir-impl/src/main/java/com/duckduckgo/pir/impl/pixels/PirPixelSender.kt
Signature: reportManualScanCompleted(totalTimeInMillis: Long, batteryOptimizationsEnabled: Boolean, totalScanJobs: Int, totalOptOutJobs: Int, profileQueryCount: Int, brokerCount: Int, isPowerSavingEnabled: Boolean)
Description: Reports that a manual PIR scan has completed. The fired pixel must include the keys "profile_queries" (profileQueryCount as string), "broker_count" (brokerCount as string), and "power_saving" (isPowerSavingEnabled as string) in addition to the existing parameters.

Type: Interface method
Name: reportInitialScanDuration
Location: pir/pir-impl/src/main/java/com/duckduckgo/pir/impl/pixels/PirPixelSender.kt
Signature: reportInitialScanDuration(durationMs: Long, profileQueryCount: Int, isPowerSavingEnabled: Boolean, batteryOptimizationsEnabled: Boolean, brokerCount: Int)
Description: Reports the duration of an initial PIR scan. The fired pixel must include the keys "power_saving" (isPowerSavingEnabled as string), "battery-optimizations" (batteryOptimizationsEnabled as string), and "broker_count" (brokerCount as string) in addition to the existing parameters.

Type: Class method (behavior change)
Name: intercept (manufacturer normalization logic)
Location: pir/pir-impl/src/main/java/com/duckduckgo/pir/impl/pixels/PirPixelInterceptor.kt
Signature: normalizedManufacturer() -> String  (private helper used by intercept)
Description: The manufacturer query parameter added to PIR pixel requests must be normalized. The raw manufacturer string is lowercased, then checked against a hardcoded set of recognized common manufacturers. If the lowercased value is in the set, it is used as-is; otherwise the string "other" is substituted. The recognized set must include at minimum: "samsung", "google", "xiaomi", "huawei", "honor", "oneplus", "oppo", "vivo", "motorola", "realme", "sony", "lg", "nokia", "lenovo", "asus".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.