I'm trying to use sqlfluff to lint T-SQL scripts, but it fails to parse database restore statements.

*   The T-SQL dialect must successfully parse RESTORE DATABASE statements without leaving any unparsed tokens.

*   Each RESTORE DATABASE statement must be parsed as a node of type 'restore_database_statement' in the parse tree. The grammar segment must be registered as a valid statement type in the T-SQL dialect (added to StatementSegment).

*   The grammar must support all source types: DISK, TAPE, and URL, each followed by '=' and a quoted path literal or parameter variable.

*   The database name in a RESTORE DATABASE statement may be either a bare identifier (parsed as a 'database_reference' containing a 'naked_identifier') or a variable parameter (parsed as a 'database_reference' containing a 'parameter').

*   The backup source path value may be a quoted literal or a variable parameter (e.g., @backup_path).

*   The grammar must support optional pre-FROM FILE and FILEGROUP clauses (FILE = 'name', FILEGROUP = 'name') appearing before the FROM clause.

*   The WITH clause must accept the following options as keywords: RECOVERY, NORECOVERY, REPLACE, CHECKSUM, NO_TRUNCATE.

*   The WITH clause must accept value-bearing options using '=' followed by a literal or parameter: STANDBY = path, STATS = number, MEDIANAME = name, BLOCKSIZE = number, BUFFERCOUNT = number, MAXTRANSFERSIZE = number.

*   The WITH clause must accept MOVE 'file' TO 'path' options (MOVE followed by quoted literal, TO keyword, quoted literal).

*   Multiple WITH options must be separable by commas.

*   The '=' signs in the parse tree must appear as 'comparison_operator' nodes containing a 'raw_comparison_operator' child with value '='.

*   Numeric values in WITH clause options must be parsed as 'numeric_literal' nodes.

*   The following keywords must be added to the UNRESERVED_KEYWORDS list in the T-SQL keywords file (src/sqlfluff/dialects/dialect_tsql_keywords.py) so they are recognized as keyword tokens rather than naked identifiers: BLOCKSIZE, BUFFERCOUNT, CHECKSUM, CONTINUE_AFTER_ERROR, MAXTRANSFERSIZE, MEDIANAME, MEDIAPASSWORD, MOVE, NO_CHECKSUM, NO_TRUNCATE, NORECOVERY, NOREWIND, NOUNLOAD, READ_WRITE_FILEGROUPS, RESTART, REWIND, STANDBY, STATS, STOP_ON_ERROR, TAPE, UNLOAD, URL.


*   Interface details: Type: Class
Name: RestoreDatabaseStatementSegment
Location: src/sqlfluff/dialects/dialect_tsql.py
Description: Grammar segment that parses T-SQL RESTORE DATABASE statements. The class name follows sqlfluff's naming convention (PascalCase + "Segment"), which produces parse tree nodes of type "restore_database_statement". This segment must be registered as a valid statement type in the T-SQL dialect's StatementSegment grammar so that RESTORE DATABASE SQL is recognized at the statement level.

The segment must produce a flat parse tree with the following node types as direct children:
- keyword: for RESTORE, DATABASE, FROM, WITH, DISK, TAPE, URL, RECOVERY, NORECOVERY, REPLACE, CHECKSUM, NO_TRUNCATE, STANDBY, STATS, MOVE, TO, FILE, FILEGROUP, MEDIANAME, BLOCKSIZE, BUFFERCOUNT, MAXTRANSFERSIZE
- database_reference: wrapping a naked_identifier (for bare names) or a parameter (for @variables)
- comparison_operator: wrapping a raw_comparison_operator with value '='
- quoted_literal: for string values (file paths, backup source paths, file names)
- parameter: for T-SQL variables used as backup source paths (e.g., @backup_path)
- numeric_literal: for integer values in options like STATS, BLOCKSIZE, BUFFERCOUNT, MAXTRANSFERSIZE
- comma: separating multiple WITH options


Type: Keyword List Addition
Name: UNRESERVED_KEYWORDS
Location: src/sqlfluff/dialects/dialect_tsql_keywords.py
Description: The following keywords must be added to the UNRESERVED_KEYWORDS list in the T-SQL keywords file so they are parsed as keyword tokens (not identifiers) in the restore grammar. Without these additions, the parse tree will have naked_identifier nodes instead of keyword nodes, causing parse tree structure mismatches.

Keywords to add:
- BLOCKSIZE
- BUFFERCOUNT
- CHECKSUM
- CONTINUE_AFTER_ERROR
- MAXTRANSFERSIZE
- MEDIANAME
- MEDIAPASSWORD
- MOVE
- NO_CHECKSUM
- NO_TRUNCATE
- NORECOVERY
- NOREWIND
- NOUNLOAD
- READ_WRITE_FILEGROUPS
- RESTART
- REWIND
- STANDBY
- STATS
- STOP_ON_ERROR
- TAPE
- UNLOAD
- URL


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.