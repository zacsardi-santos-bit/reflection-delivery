I'm working on a platform that displays a table of responsible persons for each technology entry. The table currently always shows an academic profile identifier column, but in some contexts — like the review step of the registration form — we don't want to show that column at all. I need the table component to support an optional setting that hides this column when it's not needed.

There's also a bug: when the list of responsible persons is empty, the table body renders some unexpected content instead of just being empty. This needs to be fixed so that passing an empty list results in an empty table body.

The component should continue to work as expected when responsible persons are provided without the hide setting — showing all columns including the academic profile identifier as a clickable link.
