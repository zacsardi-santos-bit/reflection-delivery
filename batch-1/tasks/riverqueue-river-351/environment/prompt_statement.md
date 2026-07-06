I'm working on adding tag validation to River's job insertion logic. Right now, users can attach tags with any content — including commas, which are used as delimiters internally and cause silent breakage. I need to introduce a regex-based format rule that enforces a consistent tag structure.

The rules I want to enforce are: tags must be at least 3 characters long; they must start and end with a letter, digit, or underscore (not a hyphen); hyphens and underscores are allowed in the middle; and commas or other special characters are never allowed. A package-level regex variable should encode these rules so it can be applied wherever tags are validated.

Can you add a package-level regex variable in the appropriate file in the river package that enforces these tag format requirements?
