Refactor the home page search component to handle its own navigation logic internally, eliminating the need for a callback from its parent component. Ensure the search component properly redirects users to the search results page upon form submission or suggestion selection, and update the styling and structure of related elements for improved accessibility and visual consistency.

*   Update the `HomeSearch` component:
    *   Remove the `onSearchSubmit` prop and ensure no props are required.
    *   Implement internal navigation using `redirect` from `next/navigation` to `/recherche?q=<url-encoded-query>` when a user submits the form or selects a suggestion.
    *   Render the search container as a `<form>` element with `role="search"`.
    *   Ensure the search submit button has `type="submit"` and does not use the `mdDown:d_flex!` CSS utility class.
    *   Maintain `data-testid='search-input'` for the search input and add `type='search'`. Ensure form submission triggers the redirect.
    *   Update the suggestion dropdown list CSS classes to: `fr-p-0 fr-m-0 pos_absolute w_100% z_10 bg_var(--background-default-grey) li-t_none!`.

*   Update CSS exports in `Autocomplete.tsx`:
    *   Export `autocompleteListContainer` with: `position: "absolute"`, `width: "100%"`, `zIndex: 10`, `bg: "var(--background-default-grey)"`, `listStyleType: "none!"`.
    *   Export `suggestion` with: `cursor: "pointer"`, `color: "var(--text-action-high-blue-france)"`.
    *   Export `isHighlighted` with: `bg: "var(--background-default-grey-hover)"`, `fontWeight: "bold"`.

*   Update the `SearchInput` component in `header`:
    *   Use `autocompleteListContainer` for the dropdown suggestion list.
    *   Combine with local CSS for `top: 2.5rem` and `textAlign: left` to form the class string: `pos_absolute w_100% z_10 bg_var(--background-default-grey) li-t_none! top_2.5rem ta_left`.
    *   Replace local `suggestion` and `isHighlighted` CSS with shared exports from `Autocomplete`.

*   Modify the `Search` component in `home`:
    *   Render `HomeSearch` without passing `onSearchSubmit`.
    *   Adjust the `h1` title to use a plain text space before the subtitle span.
    *   Change the subtitle span's CSS class to `d_block` instead of `d_inline-block`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.