I'm working on the Braintree Android SDK's 3D Secure module and need to address several robustness and API consistency issues.

First, when the lookup continuation step is called without a challenge observer, the code currently doesn't guard against this null case. It should throw a clear exception immediately with a descriptive message instead of proceeding in an undefined state.

Second, during the tokenization step, if the payment authentication result is missing either the security parameters object or the authentication token, the code should detect this early and return a descriptive failure result, while also recording the appropriate failure analytics events — rather than proceeding and likely crashing.

Third, the method used to send HTTP POST requests throughout the module had its signature updated to include an additional map of headers between the body and the response callback. All calls within the module need to be updated to use this new four-argument form.

Fourth, the analytics event method was also updated to accept a second argument — an analytics parameters object. Every call to this method across the module needs to pass a new instance of this parameters object.

Fifth, the launcher component used to expose an internal field for the activity result launcher directly, allowing callers to assign to it. This should be replaced with a proper setter method.

Finally, a core data class used throughout the flow previously allowed all its fields to default to null, which meant it could be constructed with no arguments. The defaults should be removed so that all three fields must be provided explicitly by callers.
