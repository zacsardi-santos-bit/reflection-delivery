Implement an unsaved-changes warning for the lecture editing page to prevent accidental data loss. Create a route guard to check for changes in the lecture's title, description, channel name, or time period, and prompt the user with a confirmation dialog if changes are detected. Update the lecture component to use reactive signals for state management and extract the date period settings into a standalone component.

Requirements:

*   Create a modal component:
    *   File: `app/lecture/close-edit-lecture-dialog/close-edit-lecture-modal.component.ts`
    *   Must be instantiable without error.

*   Implement a route guard function:
    *   File: `app/lecture/hasLectureUnsavedChanges.guard.ts`
    *   Function: `hasLectureUnsavedChangesGuard(component: LectureUpdateComponent, currentRoute: ActivatedRouteSnapshot, currentState: RouterStateSnapshot, nextState: RouterStateSnapshot): MaybeAsync<GuardResult>`
    *   Return `true` if `component.shouldDisplayDismissWarning` is `false`.
    *   Return `true` if `component.isChangeMadeToTitleOrPeriodSection` is `false`.
    *   If both properties are `true`, open a modal dialog using `NgbModal` and return the modal's result as an `Observable<boolean>`.

*   Update `LectureUpdateComponent`:
    *   File: `app/lecture/lecture-update.component.ts`
    *   `lecture`: Use `WritableSignal<Lecture>`, set with `.set()`, read with `()`.
    *   `lectureOnInit`: Store initial lecture state.
    *   `shouldDisplayDismissWarning`: Boolean indicating if the warning is active.
    *   `isChangeMadeToTitleOrPeriodSection`: Boolean indicating if changes are detected.
    *   `isChangeMadeToTitleSection()`: Return `true` if current lecture's title, channelName, or description differs from `lectureOnInit`. Treat `''` as equivalent to `undefined`.
    *   `isChangeMadeToPeriodSection()`: Return `true` if current lecture's visibleDate, startDate, or endDate differs from `lectureOnInit`. Treat invalid dayjs objects as equivalent to `undefined`.
    *   Ensure `onDatesValuesChanged()` and `save()` methods work with the signal-based `lecture`.

*   Create `LectureUpdatePeriodComponent`:
    *   File: `app/lecture/lecture-period/lecture-period.component.ts`
    *   Must accept a lecture input via Angular's signal input mechanism.
    *   Must initialize correctly with a `Lecture` instance.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.