Implement the `getViewForTimeMachine` function to manage the loading of dashboard cell views efficiently by checking the application state before making network requests. Ensure that the user experience is seamless by avoiding unnecessary loading indicators and server fetches when data is already available locally.

*   Update `getViewForTimeMachine` in `ui/src/dashboards/actions/views.ts`:
    *   Check if a view for the given `cellID` exists in the Redux store using the `getViewFromState` selector.
    *   If a view exists:
        *   Do not dispatch a `SET_VIEW` action.
        *   Do not make an API call to fetch the view.
        *   Dispatch `SET_ACTIVE_TIME_MACHINE` with the payload `{ activeTimeMachineID: timeMachineId, initialState: { view: <view> } }`, where `<view>` is the cached view from the store.
    *   If no view exists:
        *   Dispatch `SET_VIEW` with the payload `{ id: cellID, view: null, status: RemoteDataState.Loading }`.
        *   Fetch the view from the API.
        *   Dispatch `SET_ACTIVE_TIME_MACHINE` with the payload `{ activeTimeMachineID: timeMachineId, initialState: { view: <view> } }`, where `<view>` is the fetched view.
*   Ensure the action dispatch order:
    *   When the view is absent from the store, dispatch `SET_VIEW` (loading) first, followed by `SET_ACTIVE_TIME_MACHINE`.
    *   When the view is present in the store, dispatch only `SET_ACTIVE_TIME_MACHINE`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.