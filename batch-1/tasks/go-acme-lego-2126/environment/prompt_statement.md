I'm trying to get lego working with Shellrent for my domains and there's just nothing there, no integration at all, so I can't automate my DNS-01 challenges through them. I want to add a new DNS provider for the Shellrent hosting platform so customers like me can automate ACME certificate management (create and remove the TXT records lego needs during the challenge).

Auth is a username plus an API token. I need to be able to pull those from environment variables and also set them up programmatically through a config, and both paths should check that both credentials are actually present and give me a clear error message if either the username or the token is missing, don't just silently fail.

On the API side the provider has to talk to Shellrent to list my purchased services, fetch the details for a specific service and the domain tied to it, and then create or delete DNS TXT records on that domain to satisfy the challenge.

Oh and the annoying part: Shellrent's API only takes a fixed set of TTL values, so whatever TTL gets passed in needs to be rounded up to the nearest supported one automatically. And if the value's bigger than the largest supported TTL, fall back to the smallest supported value instead of erroring out. Wire it in like the other lego DNS providers so it's usable the same way.
