We're adding a new pipeline step to our CI/CD library that pulls runtime app security findings from Contrast Security so we can wire them into our automated security gates. Right now teams have no way to fetch vuln data from Contrast and enforce thresholds inside the pipeline, so everything's manual and the window between discovery and fix stays wide open. I want to close that.

The step takes config for the Contrast creds (an API key, a username, and a service key) plus the app identifiers (server URL, organization ID, application ID). Validate all that upfront so every required field is non-empty, and if the server URL is missing the secure protocol prefix, normalize it automatically by prepending `https://`. From the server, org, and app IDs it should build the correct API URL and the GUI (user-facing) URL for a given application.

For auth, produce the encoded credential string from the username and service key (that's the header Contrast expects). Then fetch vulnerability findings from the API with full pagination support so all pages come back automatically, don't stop at the first page.

Once we've got findings, group them into two tiers by severity: a higher-priority bucket covering critical, high, and medium, and a lower-priority bucket covering low and informational. For each tier track the total count and also the reviewed count, where a finding counts as reviewed if its status is anything other than newly reported (so "Reported" isn't reviewed, anything else is).

Last thing, produce a structured tool record for the scanned app capturing the application name, URL, server, and identifier. The app identifier is required, return an error if it's missing. Also set the server as the tool instance and include a single key entry describing the application.
