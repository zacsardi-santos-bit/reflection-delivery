I'm working with a slide transition component that animates elements in and out of view.

*   The getTranslateOffsets function must accept a CSS transform string (or undefined) and return an object with numeric offsetX and offsetY fields representing the translation components.

*   When given a matrix() transform string (e.g. 'matrix(1, 0, 0, 1, 20, 30)'), getTranslateOffsets must extract the 5th and 6th values as offsetX and offsetY respectively.

*   When given a matrix3d() transform string (e.g. 'matrix3d(1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 40, 50, 0, 1)'), getTranslateOffsets must extract the 13th and 14th values as offsetX and offsetY respectively.

*   When given a translate() transform string (e.g. 'translate(60px, 70px)'), getTranslateOffsets must extract both axis values and return them as offsetX and offsetY (numeric, without units).

*   When given a translate3d() transform string (e.g. 'translate3d(80px, 90px, 0px)'), getTranslateOffsets must extract the first two axis values as offsetX and offsetY (numeric, without units).

*   When given a translateX() transform string (e.g. 'translateX(100px)'), getTranslateOffsets must return { offsetX: 100, offsetY: 0 }. When given translateY() (e.g. 'translateY(110px)'), it must return { offsetX: 0, offsetY: 110 }.

*   When given undefined, 'none', or an unsupported transform type (e.g. 'rotate(45deg)'), getTranslateOffsets must return { offsetX: 0, offsetY: 0 }.

*   When the Slide component exits in the 'up' direction and the child element already has a CSS transform with a Y translation (e.g. from a touch drag), the exit transform must account for that existing Y offset. Specifically, the exit translateY value must equal window.innerHeight plus the element's current Y translation offset minus the element's top bounding rect value.


*   Interface details: Type: Function
Name: getTranslateOffsets
Location: packages/mui-material/src/transitions/utils.ts
Signature: getTranslateOffsets(transform: string | undefined) -> { offsetX: number, offsetY: number }
Description: Parses a CSS transform string and extracts the X and Y translation components as numeric values. Supports matrix(), matrix3d(), translate(), translate3d(), translateX(), and translateY() formats. Returns { offsetX: 0, offsetY: 0 } for undefined, 'none', or unrecognized transform types. Must be exported from the utils module so it can be imported by tests and consumed by the Slide component.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.