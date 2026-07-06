I'm working on reducing the project's dependency on some small, deprecated external packages.

*   Must create a module at libs/core/src/lib/npmlog/gauge/has-unicode.ts that exports a named function hasUnicode(): boolean (not a default export).

*   hasUnicode() must return false when the OS type is 'Windows_NT'. On all other OS types, it must read locale environment variables in priority order: LC_ALL first, then LC_CTYPE, then LANG. It returns true if the resolved variable's value matches /UTF-?8$/i (e.g. 'en_US.UTF-8', 'UTF-8', 'de_DE.utf8'); returns false when no locale variable is set or the value does not match.

*   Must create a module at libs/core/src/lib/npmlog/gauge/color-support.ts that exports a named function colorSupport and a TypeScript interface ColorSupportResult with fields: level (number), hasBasic (boolean), has256 (boolean), has16m (boolean).

*   colorSupport(stream?) must accept an optional object with an isTTY boolean property and return one of four ColorSupportResult values: no-color { level:0, hasBasic:false, has256:false, has16m:false }, basic { level:1, hasBasic:true, has256:false, has16m:false }, 256 { level:2, hasBasic:true, has256:true, has16m:false }, or 16m { level:3, hasBasic:true, has256:true, has16m:true }.

*   colorSupport must return no-color when stream.isTTY is false, when TERM is 'dumb' and COLORTERM is not set, when a CI environment variable is set but TRAVIS is not, or when TEAMCITY_VERSION is set.

*   colorSupport must return basic for win32 platform; for TERM values matching screen, xterm (non-256), vt100, linux; and when COLORTERM is set but no higher color tier is detected.

*   colorSupport must return 256 for TMUX environment, iTerm.app with version starting with 0-2, Apple_Terminal, TRAVIS (when CI is also set), and TERM='xterm-256color'.

*   colorSupport must return 16m for iTerm.app with version 3 or higher, and for TERM_PROGRAM values Hyper, HyperTerm, and MacTerm.

*   Must create a module at libs/core/src/lib/npmlog/gauge/console-control-strings.ts that exports all terminal control string functions listed in the interface. Cursor movement functions (up, down, forward, back, nextLine, previousLine) must accept an optional numeric count and return the ANSI escape sequence with the count omitted when not provided. horizontalAbsolute(num) must throw an error when called without an argument.

*   The color(...args) function must map named styles to their ANSI codes (e.g. bold=1, white=37, bgBlue=44, reset=0) and join them as '\x1b[<codes>m'. It must throw an error with the message 'Unknown color or style name: <name>' when an unrecognized style name is passed.

*   The existing module at libs/core/src/lib/log-packed.ts must import hasUnicode as a named export from './npmlog/gauge/has-unicode' rather than from the external 'has-unicode' package.

*   The existing module at libs/core/src/lib/npmlog/gauge/plumbing.ts must require console-control-strings from './console-control-strings' (the local module) rather than the external 'console-control-strings' package.


*   Interface details: Type: Function
Name: hasUnicode
Location: libs/core/src/lib/npmlog/gauge/has-unicode.ts
Signature: hasUnicode(): boolean
Description: Named export that detects whether the terminal supports Unicode characters. Returns false on Windows (os.type() === "Windows_NT"). On non-Windows, evaluates locale environment variables in priority order: LC_ALL, then LC_CTYPE, then LANG. Returns true if the resolved locale value matches the pattern /UTF-?8$/i (e.g. "en_US.UTF-8", "UTF-8", "de_DE.utf8").

Type: Function
Name: colorSupport
Location: libs/core/src/lib/npmlog/gauge/color-support.ts
Signature: colorSupport(stream?: { isTTY?: boolean }): ColorSupportResult
Description: Named export that detects terminal color support level. Returns a ColorSupportResult object based on the stream's isTTY flag, platform, and environment variables.

Type: Interface
Name: ColorSupportResult
Location: libs/core/src/lib/npmlog/gauge/color-support.ts
Description: Exported TypeScript interface describing color support level with the following fields:
  - level: number (0 = none, 1 = basic, 2 = 256-color, 3 = 16M-color)
  - hasBasic: boolean
  - has256: boolean
  - has16m: boolean
The four possible return values are:
  - No color: { level: 0, hasBasic: false, has256: false, has16m: false }
  - Basic:    { level: 1, hasBasic: true,  has256: false, has16m: false }
  - 256:      { level: 2, hasBasic: true,  has256: true,  has16m: false }
  - 16m:      { level: 3, hasBasic: true,  has256: true,  has16m: true  }

Type: Module (all named exports)
Name: console-control-strings
Location: libs/core/src/lib/npmlog/gauge/console-control-strings.ts
Description: Exports ANSI/VT100 terminal control string functions. All functions return strings:
  - up(num?: number): string   — returns "\x1b[" + (num||"") + "A"
  - down(num?: number): string — returns "\x1b[" + (num||"") + "B"
  - forward(num?: number): string — returns "\x1b[" + (num||"") + "C"
  - back(num?: number): string — returns "\x1b[" + (num||"") + "D"
  - nextLine(num?: number): string — returns "\x1b[" + (num||"") + "E"
  - previousLine(num?: number): string — returns "\x1b[" + (num||"") + "F"
  - eraseData(): string — returns "\x1b[J"
  - eraseLine(): string — returns "\x1b[K"
  - hideCursor(): string — returns "\x1b[?25l"
  - showCursor(): string — returns "\x1b[?25h"
  - horizontalAbsolute(num: number): string — returns "\x1b[<num>G"; throws an error when called without argument
  - color(...args: string[]): string — maps style/color names to ANSI codes joined by ";", wrapped as "\x1b[<codes>m"; throws Error("Unknown color or style name: <name>") for unrecognized names
  - goto(x: number, y: number): string — returns "\x1b[<y>;<x>H"
  - gotoSOL(): string — returns "\r"
  - beep(): string — returns "\x07"


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.