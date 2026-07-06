I'm working with an Angular input wrapper component and I need to add advanced character counting support.

*   Must export a NzCountConfig interface from components/input/input-wrapper.component.ts with three optional fields: max (number), strategy ((value: string) => number), and exceedFormatter ((value: string, config: { max: number }) => string).

*   The nz-input-wrapper component (NzInputWrapperComponent) must accept a boolean nzShowCount input (defaulting to false, supporting attribute binding). When false, no character count suffix element is rendered. When true, a span element with the CSS class 'ant-input-show-count-suffix' is rendered in the suffix area.

*   The nz-input-wrapper component must accept an optional nzCount input of type NzCountConfig. When nzCount is not provided, the count suffix (if shown) displays the plain character count of the current input value.

*   When nzShowCount is true and no max is configured: the text content of the '.ant-input-show-count-suffix' element must equal the plain character count as a string (e.g. '0' for empty, '5' for a five-character value). The 'ant-input-out-of-range' class must NOT be applied to the host element.

*   When nzShowCount is true and nzCount.max is set: the text content of '.ant-input-show-count-suffix' must follow the format 'currentCount/max' (e.g. '5/10'). The 'ant-input-out-of-range' class must be applied to the host element when the character count exceeds max, and must be absent when within the limit.

*   When nzCount.strategy is provided, the strategy function is used to compute the character count instead of the default string length. The strategy function receives the current (possibly formatted) string value and returns a number. This count is used for both the suffix display and the out-of-range determination.

*   When nzCount.exceedFormatter is provided: if the current input value exceeds the max (as computed by the strategy or default length), the exceedFormatter function is called with the value and { max } to produce a trimmed string. The actual input element's value must be set to this trimmed result. The suffix must then display 'trimmedCount/max' (reflecting the trimmed value), and the 'ant-input-out-of-range' class must NOT be applied since the value is kept within range by the formatter.

*   The count suffix visibility is solely controlled by nzShowCount: setting nzShowCount to false must remove the '.ant-input-show-count-suffix' element regardless of the nzCount configuration.


*   Interface details: Type: Interface
Name: NzCountConfig
Location: components/input/input-wrapper.component.ts
Description: Exported interface for configuring the character counting behavior of the input wrapper. Must be exported so that consumers can type-annotate their count configuration objects.
Signature:
  max?: number                                                          // Optional maximum character limit
  strategy?: (value: string) => number                                  // Optional custom counting function
  exceedFormatter?: (value: string, config: { max: number }) => string  // Optional formatter called when value exceeds max

Type: Component Input
Name: nzShowCount
Location: components/input/input-wrapper.component.ts
Description: Boolean input on NzInputWrapperComponent that controls whether the character count suffix is displayed. Defaults to false. Must accept boolean attribute binding (e.g. nzShowCount without a value). When true, a span element with class "ant-input-show-count-suffix" is rendered inside the suffix area.

Type: Component Input
Name: nzCount
Location: components/input/input-wrapper.component.ts
Description: Optional input on NzInputWrapperComponent that accepts an NzCountConfig object to configure advanced counting behavior (max limit, custom strategy, exceed formatter).

Type: CSS Class (host binding)
Name: ant-input-out-of-range
Location: components/input/input-wrapper.component.ts (host class binding)
Description: Applied to the nz-input-wrapper host element when nzShowCount is true and the computed character count exceeds the configured max. Must be absent when count is within range or when no max is configured.

Type: CSS Class (rendered element)
Name: ant-input-show-count-suffix
Location: Rendered inside nz-input-wrapper template
Description: Class on the span element that displays the character count. Text content is the plain count (e.g. "5") when no max is set, or "current/max" (e.g. "5/10") when a max is configured via nzCount.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.