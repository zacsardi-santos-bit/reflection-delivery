Implement the ability to predict intermediate layer outputs and control dropout during inference for uncertainty estimation in a neural network model framework. Extend the prediction API to specify internal tensors and create a new dropout layer with an explicit on/off switch.

*   Update the `predict_on_batch` method in `deepchem/models/keras_model.py`:
    *   Accept an optional `outputs` keyword argument (default `None`).
    *   Return numpy output values for specified Keras Tensors instead of standard prediction outputs when `outputs` is provided.
    *   Ensure returned values are numerically consistent with standard predictions, allowing activation functions to match within an absolute tolerance of 1e-4.

*   Create a new layer class `SwitchedDropout` in `deepchem/models/layers.py`:
    *   Subclass `tf.keras.layers.Layer`.
    *   Accept a `rate` parameter in the constructor.
    *   Implement functionality to accept two inputs: a tensor to apply dropout to and a scalar switch tensor.
    *   Apply dropout at the specified rate when the switch value is 1.0; pass input unchanged when the switch value is 0.0.

*   Modify the `default_generator` method in `deepchem/models/keras_model.py`:
    *   Replace the `predict` boolean parameter with a `mode` string parameter (default `'fit'`).
    *   Accept mode values: `'fit'`, `'predict'`, and `'uncertainty'`.
    *   Allow subclasses to override and use the mode parameter to control inputs yielded, such as passing a scalar dropout switch value.

*   Extend the following methods in `deepchem/models/keras_model.py` to accept an optional `outputs` argument:
    *   `predict_on_generator(self, generator, transformers=[], outputs=None) -> numpy array or list of numpy arrays`
    *   `predict(self, dataset, transformers=[], outputs=None) -> numpy array or list of numpy arrays`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.