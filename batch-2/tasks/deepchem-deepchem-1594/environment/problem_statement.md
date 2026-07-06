I'm adding uncertainty estimation to our neural net model framework and hitting two gaps I need you to close.

First problem, when I call the prediction method on a model I only get back the model's declared outputs, there's no way to ask for the output of some arbitrary internal layer or tensor. I want to inspect learned representations, debug, and sometimes grab raw pre-activation values from a hidden layer so I can apply the activation myself downstream. So the predict API needs an optional parameter where I can specify which internal tensors to return values for, and when I pass it, it should return those instead of the usual declared outputs.

Second, dropout. Standard dropout auto-disables itself during inference, which kills Monte Carlo dropout-based uncertainty estimation since that needs dropout active at prediction time. I need a dropout layer that takes an explicit on/off switch as an input value, so the caller controls whether dropout applies regardless of whether the model's in training or inference mode. I turn it on for uncertainty passes, off for normal predictions.

Related, the data generator on the base model class currently takes a boolean flag to say prediction mode or not, but that's not enough anymore, I need it to accept a string mode argument instead so I can tell apart regular prediction, training, and uncertainty estimation. And it should be overridable in subclasses so they can yield appropriately structured inputs for each mode, passing the right switch values alongside the feature data.

Point of all this is one architecture that applies dropout during uncertainty estimation but makes clean deterministic predictions during normal inference, no separate models or hacky workarounds.
