I'm working on the Podman Desktop extension for Podman and I need to add a feature that synchronizes the host machine's trusted certificates into running Podman virtual machines.

*   The PodmanCertificateSync class must accept two constructor arguments: a Map from machine name string to ProviderConnectionStatus, and a Map from machine name string to MachineInfo.

*   The getSystemCertificates method must retrieve certificates from three stores — 'system', 'bundled', and 'extra' — and return them as a deduplicated string array. When multiple stores contain the same certificate, it must appear only once in the result. If all stores are empty, the method must return an empty array.

*   The getRunningMachineNames method must return an array of machine names that are in 'started' status AND have a non-empty vmType field in their MachineInfo. Machines in 'stopped' or 'starting' status must be excluded. Machines with an empty vmType must also be excluded.

*   The synchronizeAll method must call extensionApi.window.showWarningMessage with the exact message 'No running Podman machines found.' when there are no running machines. When running machines do exist, it must attempt to synchronize each one in turn. If synchronization of one machine fails, it must continue to the next machine without throwing.

*   The synchronize method must wrap all work inside extensionApi.window.withProgress, called with options: { location: extensionApi.ProgressLocation.TASK_WIDGET, title: 'Synchronizing certificates to {machineName}', cancellable: true }.

*   When the certificates array passed to synchronize is empty, the method must report { message: '[{machineName}] No certificates found on the host to synchronize', increment: -1 } and must not invoke execPodman.

*   When synchronizing, the method must first create the remote anchors directory via 'machine ssh {machineName} sudo mkdir -p /etc/pki/ca-trust/source/anchors' (execPodman called with vmType and a token option), reporting increment 5. It must then probe existing certificates reporting increment 10.

*   Certificates must be stored at the remote path /etc/pki/ca-trust/source/anchors/podman-desktop-{fingerprint}.crt, where fingerprint is the first 16 lowercase hex characters of the SHA-256 hash of the PEM string.

*   During synchronize, the method must query existing remote certificate filenames (using 'ls -1' on the anchors path), extract their fingerprints, then for each host certificate: skip it if its fingerprint is already present, otherwise upload it via base64 encoding. Only new certificates must be uploaded.

*   During synchronize, stale remote certificates (present on the VM but not in the current host cert set) must be deleted using 'machine ssh {machineName} sudo rm -f /etc/pki/ca-trust/source/anchors/podman-desktop-{fingerprint}.crt'. If this deletion fails, the method must report { message: '[{machineName}] Synchronization failed — check logs for details' } and must not proceed to update-ca-trust.

*   After any certificates are added or removed, the method must run 'machine ssh {machineName} sudo update-ca-trust' (reporting increment 90), then 'machine ssh {machineName} sudo systemctl restart podman.socket podman.service' (reporting increment 95), then report increment 100 and increment -1 (cleanup). If the diff is empty (no certs added or removed), both steps must be skipped and the method must report { message: '[{machineName}] Already up to date (N certificates)', increment: 100 } where N is the certificate count.

*   Progress increments reported during synchronize must include: 5 (after mkdir), 10 (after checking existing), 90 (after update-ca-trust), 95 (after service restart), 100 (done), and -1 (cleanup).

*   On any synchronization error during synchronize, the method must call console.error with 'Certificate sync failed for machine {machineName}:' followed by the Error, and must report { message: '[{machineName}] Synchronization failed — check logs for details' } and { increment: -1 }.

*   The synchronize method must check the cancellation token at multiple points. If cancellation is requested before starting, no execPodman calls must be made. If cancellation is requested mid-process (e.g., after mkdir), further commands must be skipped. If cancellation is requested during the upload loop, no further uploads must occur.

*   The private getCertificateFingerprint method must accept a PEM string and return a 16-character lowercase hex string (first 16 chars of the SHA-256 hash of the PEM content). The same input must always produce the same output, and different PEM contents must produce different fingerprints.

*   The private buildSyncSummary method must accept (deleted: number, added: number, unchanged: number) and return: 'No changes' when all are zero; 'Certificates: N added' when only additions; 'Certificates: N removed' when only deletions; 'Certificates: N unchanged' when only unchanged; 'Certificates: N added, N removed, N unchanged' (in that order) when multiple non-zero counts.

*   The private getRemoteCertificateFingerprints method must accept (machineName: string, anchorsPath: string) and return a Promise<Set<string>>. It must parse filenames matching 'podman-desktop-{fingerprint}.crt' from the ls output and return their fingerprints as a Set. It must return an empty Set when there are no certs. It must re-throw any error from the ls command so the sync aborts.


*   Interface details: Type: Class
Name: PodmanCertificateSync
Location: extensions/podman/packages/extension/src/certificate-sync/podman-certificate-sync.ts
Description: Manages synchronization of host system TLS certificates into running Podman virtual machines.
Signature:
  constructor(machineStatuses: Map<string, ProviderConnectionStatus>, machineInfos: Map<string, MachineInfo>)
  getSystemCertificates(): string[]
  synchronize(machineName: string, certs: string[]): Promise<void>
  getRunningMachineNames(): string[]
  synchronizeAll(): Promise<void>
  private getCertificateFingerprint(pem: string): string
  private buildSyncSummary(deleted: number, added: number, unchanged: number): string
  private getRemoteCertificateFingerprints(machineName: string, anchorsPath: string): Promise<Set<string>>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.