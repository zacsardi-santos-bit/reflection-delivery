Refactor the `interactiveGraphQuestionBuilder` utility to improve its API consistency and add support for additional graph types. Implement methods to set question content and background images, and update default settings for decorative elements.

*   Implement a `withContent(content: string)` method to set the question content string.
*   Implement a `withBackgroundImage(url: string, width: number, height: number, options?: { scale?: number, bottom?: number, left?: number, right?: number, top?: number })` method to configure background images.
*   Update existing graph methods to use named options objects:
    *   `withSegments(options?: { numSegments?: number, coords?: Array<[[number, number], [number, number]]>, startCoords?: Array<[[number, number], [number, number]]> })`
        *   Default: `numSegments=1`, `coords=[[[-5,5],[5,5]]]`.
    *   `withLinear(options?: { coords?: [[number, number], [number, number]], startCoords?: [[number, number], [number, number]] })`
        *   Default `coords`: `[[-5, 5], [5, 5]]`.
    *   `withLinearSystem(options?: { coords?: Array<[[number, number], [number, number]]>, startCoords?: Array<[[number, number], [number, number]]> })`
        *   Default `coords`: `[[-5, 5], [5, 5]]` and `[[-5, -5], [5, -5]]`.
    *   `withRay(options?: { coords?: [[number, number], [number, number]], startCoords?: [[number, number], [number, number]] })`
        *   Default `coords`: `[[-5, 5], [5, 5]]`.
    *   `withQuadratic(options?: { coords?: [[number, number], [number, number], [number, number]], startCoords?: [[number, number], [number, number], [number, number]] })`
        *   Default `coords`: `[[-5, 5], [0, -5], [5, 5]]`.
    *   `withSinusoid(options?: { coords?: [[number, number], [number, number]], startCoords?: [[number, number], [number, number]] })`
        *   Default `coords`: `[[0, 0], [3, 2]]`.
    *   `withCircle(options?: { center?: [number, number], radius?: number, startCoords?: [number, number] })`
        *   Default: `center=[0, 0]`, `radius=2`.
    *   `withPolygon(snapTo: "grid" | "angles" | "sides", options?: { match?: string, numSides?: number | "unlimited", showAngles?: boolean, showSides?: boolean, coords?: Array<[number, number]>, startCoords?: Array<[number, number]> })`
        *   Default: `numSides=3`, `showAngles=false`, `showSides=false`, `coords=[[3, -2], [0, 4], [-3, -2]]`.
*   Add new graph type support:
    *   `withPoints(numPoints: number | "unlimited", options?: { coords?: Array<[number, number]>, startCoords?: Array<[number, number]> })`
        *   Default with "unlimited": `coords=[[0, 0]]`.
    *   `withAngle(options?: { coords?: [[number, number], [number, number], [number, number]], startCoords?: [[number, number], [number, number], [number, number]], showAngles?: boolean, allowReflexAngles?: boolean, angleOffsetDeg?: number, snapDegrees?: number, match?: string })`
        *   Default `coords`: `[[6.994907182610915, 0], [0, 0], [6.5778483455013586, 2.394141003279681]]`.
*   Update default settings for decorative elements:
    *   Change default color for locked points and lines to `grayH`.
    *   Set `showPoint1` and `showPoint2` to `false` for locked lines.
    *   Ensure locked points without specified color use `grayH` for fill and stroke.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.