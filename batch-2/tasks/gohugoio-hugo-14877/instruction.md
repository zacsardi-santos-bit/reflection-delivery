I'm running into an issue with content-aware (smart) image cropping in Hugo.

*   Must implement expandCropRectToMinSize(r, bounds image.Rectangle, width, height int) image.Rectangle in package images at resources/images/smartcrop.go. The function expands rectangle r to be at least width pixels wide and height pixels tall while keeping it within bounds.

*   If the bounds rectangle is too small to fit a width×height region (i.e., bounds width < width or bounds height < height), the function must return r unchanged without any expansion in that axis.

*   Expansion must be balanced: the extra pixels needed are split evenly between the two sides of each axis, with any odd pixel going to the higher (right or bottom) side. For example: expanding width by 1 adds 0 to min and 1 to max; expanding by 2 adds 1 to each side.

*   After symmetric expansion, if the rectangle exceeds the bounds on one side, it must be shifted to stay within bounds. For example, if min goes below boundsMin, the rectangle is shifted right/down so that min equals boundsMin (and max is adjusted accordingly). If max exceeds boundsMax, the rectangle is shifted left/up.

*   Specific expected outputs: (1) r=Rect(0,0,899,560), bounds=Rect(0,0,900,562), width=900, height=561 → Rect(0,0,900,561). (2) r=Rect(1,2,900,562), bounds=Rect(0,0,900,562), width=900, height=561 → Rect(0,1,900,562). (3) r=Rect(10,10,109,109), bounds=Rect(0,0,200,200), width=101, height=101 → Rect(9,9,110,110). (4) r=Rect(0,0,899,560), bounds=Rect(0,0,899,560), width=900, height=561 → Rect(0,0,899,560) (unchanged because bounds are too small).

*   When using smart crop (with Smart anchor) to Crop or Fill an image, the output must match the exact requested dimensions. For example, cropping a 900×562 image to 900×561 using Smart anchor must produce an image that is exactly 900×561 (not 899×560 or any other size).

*   The smart crop version number constant (smartCropVersionNumber) must be incremented from 0 to exactly 1. This value is appended to the processing options for smart Crop and Fill operations when the version number is greater than 0, changing the cache key so that previously cached smart-cropped images are automatically regenerated. After this change, smart fill of a 200×100 image has RelPermalink /a/sunset_hu_e548e0b0d6759ee7.jpg and smart crop of a 200×200 image has RelPermalink /a/sunset_hu_2ffd7547f08a145d.jpg.


*   Interface details: Type: Function
Name: expandCropRectToMinSize
Location: resources/images/smartcrop.go
Signature: expandCropRectToMinSize(r, bounds image.Rectangle, width, height int) image.Rectangle
Description: Expands a smart crop rectangle r so that it is at least width×height pixels while keeping it within bounds. If bounds is too small to contain a width×height rectangle, the rectangle is returned unchanged. Expansion is balanced between both sides of each axis: the required pixels are split evenly, with any odd pixel going to the higher side (right/bottom). If the expanded rectangle exceeds the bounds on one side, it is shifted to stay within bounds (clamped). The function is in the images package (unexported) and is called internally from the smart crop pipeline.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.