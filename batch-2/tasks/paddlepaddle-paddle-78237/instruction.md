I've run into a crash when using convolution operations with a filter tensor that has a zero-size kernel dimension.

*   paddle.nn.functional.conv1d must raise ValueError when the weight (filter) tensor has a zero-size spatial kernel dimension (the third dimension of the weight tensor, i.e., weight shape like [C, 1, 0]).

*   paddle.nn.functional.conv2d must raise ValueError when any spatial kernel dimension of the weight tensor (height or width — dimensions 2 and 3 of the weight shape) is 0, regardless of whether the kernel height or kernel width is zero.

*   The zero-kernel validation for conv1d must apply to depthwise convolutions (where groups equals the number of input channels) for both 'NCL' and 'NLC' data formats.

*   The zero-kernel validation for conv1d must apply to both float32 and float64 weight tensors.

*   The zero-kernel validation for conv2d must apply to both depthwise convolutions (groups == in_channels) and regular convolutions (groups == 1).

*   The ValueError must be raised before any hardware-level computation is attempted, ensuring users receive a clear Python-level error rather than a low-level crash.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.