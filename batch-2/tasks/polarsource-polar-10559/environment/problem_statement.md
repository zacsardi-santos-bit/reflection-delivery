# Detect cross-domain redirects on checkout URLs during organization review

## Description

When organizations set up checkout flows, they provide success and return URLs that customers land on after completing or abandoning a payment. During organization review we inspect these URLs — but right now we only look at the domains as declared. We don't actually follow the URLs to verify where they ultimately send users.

Bad actors have been found to supply their own API endpoints as checkout destination URLs, which then silently redirect customers to prohibited content (adult sites, gambling, etc.). The declared URL looks legitimate, but the actual destination is not.

## Expected Behavior

- During organization review, the system should follow checkout success and return URLs and record where each one ultimately lands.
- If a URL redirects to a **different domain**, that should be surfaced as a high-risk signal.
- If a URL does not redirect, or redirects within the same domain, it should be recorded but not flagged as a cross-domain redirect.
- Errors during URL resolution (timeouts, connection failures) should be captured and associated with the URL rather than causing the review process to fail.
- The redirect results should be stored in the setup data alongside the existing URL and domain lists, so they are available for downstream analysis.

## Why This Matters

Relying solely on declared domains during review makes it trivial to pass review while still routing customers to harmful destinations. Following URLs at review time closes this gap and gives reviewers a much stronger signal when a checkout URL is being used to redirect users outside the organization's own domain.
