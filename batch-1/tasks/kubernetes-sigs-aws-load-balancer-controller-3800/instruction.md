Implement functionality to explicitly disable AWS security add-ons (WAF Classic, WAFv2, and Shield Advanced protection) on load balancers using ingress annotations. Ensure that error messages clearly identify the security service involved when reconciliation fails. Implement the following requirements:

*   Modify `ProtectionSpec` in `pkg/model/shield/protection.go`:
    *   Add a boolean field `Enabled` with JSON tag `enabled` to indicate shield protection status.

*   Update `protectionSynthesizer.Synthesize` in `pkg/deploy/shield/protection_synthesizer.go`:
    *   Return `nil` if no protection resources exist in the stack.
    *   Use the `Enabled` field from `ProtectionSpec` to determine protection status.
    *   Return error with prefix 'failed to get shield protection on LoadBalancer: ' if `GetProtection` fails.
    *   Use name 'managed by aws-load-balancer-controller' for new protections. Return error with prefix 'failed to create shield protection on LoadBalancer: ' if `CreateProtection` fails.
    *   Delete protection only if its name is 'managed by aws-load-balancer-controller' and `Enabled` is `false`. Return error with prefix 'failed to delete shield protection on LoadBalancer: ' if `DeleteProtection` fails.

*   Create `pkg/deploy/shield/protection_manager_mocks.go`:
    *   Export `NewMockProtectionManager(ctrl *gomock.Controller) *MockProtectionManager` that implements `ProtectionManager`.

*   Update `webACLAssociationSynthesizer.Synthesize` in `pkg/deploy/wafregional/web_acl_association_synthesizer.go`:
    *   Return `nil` if no WebACL association resources exist.
    *   Return error with prefix 'failed to get WAFClassic webACL association on LoadBalancer: ' if `GetAssociatedWebACL` fails.
    *   Return error with prefix 'failed to create WAFClassic webACL association on LoadBalancer: ' if `AssociateWebACL` fails.
    *   Return error with prefix 'failed to delete WAFClassic webACL association on LoadBalancer: ' if `DisassociateWebACL` fails.

*   Create `pkg/deploy/wafregional/web_acl_association_manager_mocks.go`:
    *   Export `NewMockWebACLAssociationManager(ctrl *gomock.Controller) *MockWebACLAssociationManager`.

*   Update `webACLAssociationSynthesizer.Synthesize` in `pkg/deploy/wafv2/web_acl_association_synthesizer.go`:
    *   Return `nil` if no WebACL association resources exist.
    *   Return error with prefix 'failed to get WAFv2 webACL association on LoadBalancer: ' if `GetAssociatedWebACL` fails.
    *   Return error with prefix 'failed to create WAFv2 webACL association on LoadBalancer: ' if `AssociateWebACL` fails.
    *   Return error with prefix 'failed to delete WAFv2 webACL association on LoadBalancer: ' if `DisassociateWebACL` fails.

*   Create `pkg/deploy/wafv2/web_acl_association_manager_mocks.go`:
    *   Export `NewMockWebACLAssociationManager(ctrl *gomock.Controller) *MockWebACLAssociationManager`.

*   Modify `buildWAFv2WebACLAssociation` in `pkg/ingress/model_build_load_balancer_addons.go`:
    *   Return `nil` if no member has the `wafv2-acl-arn` annotation.
    *   Return a `WebACLAssociation` with empty `WebACLARN` if annotation value is 'none'.
    *   Return error 'conflicting WAFv2 WebACL ARNs: [<sorted list>]' for conflicting values.
    *   Return a `WebACLAssociation` with resolved ARN otherwise.

*   Modify `buildWAFRegionalWebACLAssociation` in `pkg/ingress/model_build_load_balancer_addons.go`:
    *   Return `nil` if no member has `waf-acl-id` or `web-acl-id` annotation.
    *   Use `waf-acl-id` as primary and `web-acl-id` as fallback.
    *   Return a `WebACLAssociation` with empty `WebACLID` if resolved value is 'none'.
    *   Return error 'conflicting WAFClassic WebACL IDs: [<sorted list>]' for conflicting values.

*   Modify `buildShieldProtection` in `pkg/ingress/model_build_load_balancer_addons.go`:
    *   Return `nil` if no member has the `shield-advanced-protection` annotation.
    *   Return error 'conflicting enable shield advanced protection' for conflicting values.
    *   Return a `Protection` with `Enabled` set based on annotation value and `ResourceARN` set to load balancer ARN.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.