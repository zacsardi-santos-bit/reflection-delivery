I'm working on the DSv2 streaming connector for Delta Lake and need to add foundational support for Change Data Capture streaming.

*   CDCDataFile.fromAddFile(addFile, commitTimestamp) must create a CDCDataFile that wraps the provided AddFile, stores the commit timestamp, and sets the change type to the CDC insert constant (the string 'insert' as returned by CDCReader.CDC_TYPE_INSERT()).

*   CDCDataFile.getAddFile() must return the exact same AddFile instance that was passed to fromAddFile (not a copy).

*   CDCDataFile.getChangeType() must return the string 'insert' (matching CDCReader.CDC_TYPE_INSERT()).

*   CDCDataFile.getCommitTimestamp() must return the commitTimestamp long value passed to fromAddFile.

*   CDCDataFile.getFileSize() must return the size of the wrapped AddFile.

*   CDCDataFile.toString() must produce a string of the form: CDCDataFile{addFile=<AddFile.toString()>, changeType='insert', commitTimestamp=<N>} — specifically, the string must start with 'CDCDataFile{addFile=AddFile{' and contain "changeType='insert', commitTimestamp=<N>}".

*   IndexedFile.sentinel(version, index) must create an IndexedFile with no file action: hasFileAction() returns false, getAddFile() returns null, getCDCFile() returns null, and getFileSize() throws IllegalStateException.

*   IndexedFile.sentinel(version, index).toString() must return the exact string 'IndexedFile{version=<version>, index=<index>}' with no additional fields.

*   IndexedFile.addFile(version, index, addFile) must create an IndexedFile where hasFileAction() returns true, getAddFile() returns the exact same AddFile instance, getCDCFile() returns null, and getFileSize() returns the AddFile's size.

*   IndexedFile.addFile(...).toString() must start with 'IndexedFile{version=<version>, index=<index>, addFile=AddFile{'.

*   IndexedFile.cdc(version, index, cdcFile) must create an IndexedFile where hasFileAction() returns true, getAddFile() returns null, getCDCFile() returns the exact same CDCDataFile instance, and getFileSize() returns the CDC file's size.

*   IndexedFile.cdc(...).toString() must start with 'IndexedFile{version=<version>, index=<index>, cdcFile=CDCDataFile{'.

*   All IndexedFile factory methods must store version and index such that getVersion() and getIndex() return the respective values passed to the factory method.


*   Interface details: Type: Class
Name: CDCDataFile
Location: spark/v2/src/main/java/io/delta/spark/internal/v2/read/CDCDataFile.java
Description: Wraps an AddFile with CDC metadata (change type and commit timestamp). Created via a static factory method.
Signature:
  - static CDCDataFile fromAddFile(io.delta.kernel.internal.actions.AddFile addFile, long commitTimestamp)
  - io.delta.kernel.internal.actions.AddFile getAddFile()
  - String getChangeType()
  - long getCommitTimestamp()
  - long getFileSize()
  - String toString()

Type: Class
Name: IndexedFile
Location: spark/v2/src/main/java/io/delta/spark/internal/v2/read/IndexedFile.java
Description: Represents an indexed file entry in the streaming read path. Supports three distinct states — sentinel (positional marker), regular AddFile entry, and CDC file entry — each created via a dedicated static factory method.
Signature:
  - static IndexedFile sentinel(long version, long index)
  - static IndexedFile addFile(long version, long index, io.delta.kernel.internal.actions.AddFile addFile)
  - static IndexedFile cdc(long version, long index, CDCDataFile cdcFile)
  - long getVersion()
  - long getIndex()
  - boolean hasFileAction()
  - io.delta.kernel.internal.actions.AddFile getAddFile()
  - CDCDataFile getCDCFile()
  - long getFileSize()
  - String toString()


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.