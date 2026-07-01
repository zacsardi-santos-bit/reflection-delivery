Implement the Responsibles component to display a table of responsible persons for a technology, with an optional feature to hide the academic profile identifier column. Ensure the table renders correctly even when no responsible persons are provided.

*   Implement the Responsibles component in `packages/web/components/Technology/Details/Tables/Responsibles.js`.
*   Accept the following props:
    *   `data`: An array of responsible objects, defaulting to an empty array. Each object may include fields: id, name, email, lattes_id, lattes_url, and verified.
    *   `hideLattesInfo`: A boolean that defaults to false. When true, omit the "ID Lattes" column header and all Lattes-related cells from the table.
*   Render an HTML table with the following behavior:
    *   Include columns for Nome, E-mail, Telefone, ID Lattes, and Cadastrado.
    *   If `hideLattesInfo` is true, exclude the "ID Lattes" column header and cells from all rows.
    *   When `data` is an empty array, render the table with all column headers but an empty body (no rows).
    *   When `data` contains responsible objects, render each as a table row:
        *   Include the "ID Lattes" column with a clickable link (target='_blank', rel='noreferrer') displaying the `lattes_id` value unless `hideLattesInfo` is true.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.