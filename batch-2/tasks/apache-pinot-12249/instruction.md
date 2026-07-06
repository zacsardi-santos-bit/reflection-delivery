Implement a utility class `KafkaSSLUtils` to manage SSL/TLS keystore and truststore file creation and updates for Kafka stream ingestion based on certificate and key material provided as configuration properties.

*   Implement `KafkaSSLUtils.initTrustStore(Properties consumerProps)` to:
    *   Read a Base64-encoded server certificate and its type from the properties.
    *   Create or overwrite a JKS trust store file at the path specified by `ssl.truststore.location`.
    *   Store exactly one trusted certificate entry using the password from `ssl.truststore.password`.

*   Implement `KafkaSSLUtils.initKeyStore(Properties consumerProps)` to:
    *   Read a Base64-encoded client certificate, a Base64-encoded private key, the key algorithm, and the certificate type from the properties.
    *   Create or overwrite a PKCS12 key store file at the path specified by `ssl.keystore.location`.
    *   Store exactly one key entry (private key + certificate) using the passwords from `ssl.keystore.password` and `ssl.key.password`.

*   Implement `KafkaSSLUtils.initSSL(Properties consumerProps)` to:
    *   Initialize both the trust store and the key store when all corresponding properties are present.
    *   Ensure only one certificate entry in the trust store and one key entry in the key store.
    *   Initialize only the trust store without error if key store certificate properties are absent.
    *   Avoid creating the trust store file if `stream.kafka.ssl.server.certificate` is absent, ensuring a `java.io.FileNotFoundException` if accessed.
    *   Support certificate renewal by updating stores with new data, maintaining exactly one entry each.
    *   Maintain backward compatibility by leaving existing stores unchanged if certificate properties are absent on subsequent calls.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.