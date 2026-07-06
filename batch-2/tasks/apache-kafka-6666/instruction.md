Implement the necessary changes to the consumer group coordinator to handle static group members correctly during rebalances. Ensure that static members are not incorrectly evicted and that leadership and error handling are managed appropriately.

*   During rebalance completion:
    *   Remove only dynamic (non-static) members who have not rejoined within the rebalance timeout.
    *   Retain static members who have not rejoined by the rebalance timeout, as long as their session timeout has not expired.

*   Handle group leadership:
    *   Elect a new leader from rejoined members if the current leader fails to rejoin by the rebalance timeout.
    *   Proceed to the next generation if at least one member has rejoined.

*   Manage group state when no members rejoin:
    *   Do not advance to the next generation if no members have rejoined and static members are still within their session timeout.
    *   Schedule a new rebalance delay to allow session timeouts to naturally remove non-responsive members.

*   Update join group results:
    *   Include instance identifiers of all tracked group members in the leader's join group result, even for static members who did not rejoin but have not session-timed-out.

*   Error handling for static members:
    *   Return an 'illegal generation' error on heartbeat for static members that did not rejoin within the rebalance timeout, instead of an 'unknown member' error.

*   Leadership election with protocol changes:
    *   Elect a static follower as the new leader if it rejoins with a changed protocol and the current leader has not rejoined.
    *   Ensure the non-rejoined static leader remains tracked, with its instance identifier registered in the join result.

*   Remove the assertion that all members must rejoin before advancing to the next generation, allowing rebalance completion even when some static members have not rejoined.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.