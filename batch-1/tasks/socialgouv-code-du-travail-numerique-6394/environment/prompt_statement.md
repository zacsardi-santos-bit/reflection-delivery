I'm updating the footer component for our labor law application. Right now, the footer only contains navigation links and legal information, and there's a separate "need more info" section placed outside the footer in the page layout. I want to consolidate that help section directly into the footer component itself, so the footer becomes a single self-contained unit.

Concretely, the footer should first show an informational banner that encourages users to find local labor ministry services — with a link to the service locator page — and then show the standard footer content below it. Once that's moved inside the footer, it should be removed from wherever it currently lives in the broader layout.

I also want to fix a couple of smaller issues: the "accessibility compliance" notice in the footer bottom bar is currently plain text, but it should be a real link pointing to the legal notices page. And external links in the footer should include proper security attributes.

Finally, the footer component and the related informational sub-components should be reorganized into a dedicated footer subfolder within the layout module, since they all belong together.
