I'm working on the release dashboard footer in our content management system, and I need it to correctly handle all possible release states. Right now the footer doesn't properly account for archived releases — it should suppress the primary action button entirely for archived releases (since there's nothing meaningful to publish or schedule), leaving only the overflow menu button in the footer actions area.

The footer should already be rendering the right buttons for other states: a publish button for active ASAP releases, a schedule option for active releases of the scheduled type that haven't been scheduled yet, an unschedule button for releases that are currently scheduled for publishing, and a revert button for already-published releases. The missing piece is just making sure that archived releases show none of these primary actions.

The footer actions container also needs to be identifiable by a dedicated test attribute so the correct area of the UI can be targeted.
