I'm working with the kanidm identity management system and I've run into two problems related to POSIX numeric identifiers.

First, I need to be able to remove a specific attribute from a group through the client library — specifically to clear and reset the numeric group identifier so it can be regenerated. Right now there's no method on the client for this. I'd like to be able to delete individual attributes from a group via the REST API.

Second, the validation logic for manually specified numeric identifiers seems wrong. When I try to assign a numeric user or group ID with a value like 1000, it gets rejected even though values at or above 1000 are the conventional start of the regular-user range on Linux. Only values below 1000 should be treated as system-reserved and rejected. Values of 1000 and above should be considered valid for manual assignment.

Both of these need to work together: after purging a group's numeric identifier attribute, it should be possible to assign a new value in the valid range without error.
