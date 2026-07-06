I'm hitting a bunch of related bugs with metric tracking on multi-output models when I pass metrics as a dictionary, and I think they all live in the same metric-to-output mapping logic. Basically I've got named outputs and I'm defining per-output metrics via a dict, and I can't trust what gets tracked, sometimes metrics get silently attributed to the wrong output which makes eval useless.

First thing, when my model's internal layer names match the keys in my metrics dict but the actual output variables have different user-facing names, the mapping breaks. What I'd expect is that when the dict keys match the model's output names, the metrics get mapped using those output names positionally, in order, even though the output variables use different names. Right now it either fails or produces wrong values.

Second, if I declare the metrics dict in a different order than the model's output ordering, they end up misaligned. They should always get reordered to match the canonical output order automatically, no matter what order I wrote them in.

Third, I want to define metrics using nested structures, like a dict where some values are themselves nested dicts or lists of metric objects, and have every one of them flattened out and tracked independently with its own name. Right now that's just not supported and it crashes.

Last one, when I accidentally use an invalid key in the metrics dict (one that doesn't map to any output), the error I get is vague and unhelpful. I want it to clearly say the metrics configuration is invalid and include the actual problematic key name so I can find it fast.

Can you go into the mapping code and fix all four of these so named outputs with dict-defined metrics behave correctly in these common real-world setups? oh and make sure the reordering and the nested flattening both hold up together.
