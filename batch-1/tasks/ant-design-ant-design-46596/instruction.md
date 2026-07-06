Implement a global configuration API for static feedback components in your design system to allow theming and layout settings through a render wrapper. Ensure static APIs like toast messages, notifications, and confirm dialogs can inherit custom class prefixes, RTL layout, and locale settings from this wrapper.

*   Update `ConfigProvider.config()` method in `components/config-provider/index.tsx`:
    *   Accept a `holderRender` option, a function wrapping static feedback containers.
    *   Ensure `holderRender` can apply custom `prefixCls` and `iconPrefixCls` to message, notification, and modal elements.
    *   Implement RTL layout support by applying RTL CSS classes when `direction='rtl'` is set in `holderRender`.
    *   Implement a priority system for `prefixCls`:
        *   Global `ConfigProvider.config({ prefixCls })`
        *   `holderRender` wrapper `prefixCls`
        *   Component-specific config (`message.config`, `notification.config`, `Modal.config`)

*   For message elements:
    *   Apply custom `prefixCls` and `iconPrefixCls` from `holderRender`.
    *   Implement RTL support with `.ant-message-rtl` class.
    *   Respect `maxCount` constraint set in `holderRender`.

*   For notification elements:
    *   Apply custom `prefixCls` and `iconPrefixCls` from `holderRender`.
    *   Implement RTL support with `.ant-notification-rtl` class.
    *   Respect `maxCount` constraint set in `holderRender`.

*   For modal elements:
    *   Apply custom `prefixCls` and `iconPrefixCls` from `holderRender`.
    *   Implement RTL support with `.ant-modal-confirm-rtl` class.
    *   Apply locale settings for button labels in `Modal.confirm()` dialogs.
    *   Render OK and Cancel buttons with classes based on custom `prefixCls`.

*   Export `actDestroy` function in `components/message/index.tsx`:
    *   Signature: `actDestroy() -> void`
    *   Purpose: Destroy current static message holder state for test isolation.

*   Export `actDestroy` function in `components/notification/index.tsx`:
    *   Signature: `actDestroy() -> void`
    *   Purpose: Destroy current static notification holder state for test isolation.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.