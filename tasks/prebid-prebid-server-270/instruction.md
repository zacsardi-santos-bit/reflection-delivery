Implement validation for DigiTrust consent signals in the prebid server and ensure the Rubicon adapter forwards DigiTrust identity data. Validate bid requests to respect user privacy preferences and pass necessary data to Rubicon.

*   Add a function `validateUser` in `endpoints/openrtb2/auction.go`:
    *   Signature: `validateUser(user *openrtb.User) error`
    *   Return nil if the user object is nil or has no Ext field.
    *   Return nil if the DigiTrust object in user.Ext has a Pref value of 0.
    *   Return a non-nil error if the DigiTrust Pref value is non-zero.

*   Define new types in `openrtb_ext/user.go`:
    *   `ExtUser` struct with a field:
        *   `DigiTrust *ExtUserDigiTrust` with JSON tag `"digitrust,omitempty"`
    *   `ExtUserDigiTrust` struct with fields:
        *   `ID string` with JSON tag `"id"`
        *   `KeyV int` with JSON tag `"keyv"`
        *   `Pref int` with JSON tag `"pref"`

*   Modify `rubiconUserExt` struct in `adapters/rubicon/rubicon.go`:
    *   Add a field `DigiTrust *openrtb_ext.ExtUserDigiTrust` with JSON tag `"digitrust"`.

*   Update Rubicon adapter logic:
    *   Extract the DigiTrust object from the incoming bid request's `user.ext`.
    *   Copy the DigiTrust data into the outgoing Rubicon request's user ext.
    *   Ensure `rpRequest.User.Ext` when unmarshaled into `rubiconUserExt` contains correct `DigiTrust.ID`, `DigiTrust.KeyV`, and `DigiTrust.Pref` values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.