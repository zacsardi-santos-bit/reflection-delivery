I'm poking at the Anthropic integration config flow in Home Assistant and hit a real gap around the extended reasoning setup. Right now folks can set the thinking budget higher than (or equal to) the max tokens, and the form just accepts it silently, which is nonsense because if the thinking budget eats up everything there's nothing left for the actual response. The AI spends its whole budget on reasoning and produces nothing usable at runtime.

Part of why we can't catch this today is layout: the maximum token limit gets collected in an earlier general settings step while the thinking budget shows up later in the model-specific options step, so there's no way to compare them at input time. I want to move the max tokens field down into that same model-specific step, right next to the thinking budget and the other model-specific options, so they live together.

Then add validation there: if the thinking budget is greater than or equal to the max tokens, reject the submission and surface a clear error on the thinking budget field so the user knows exactly what's wrong. When it's valid (thinking budget strictly less than max tokens), the config should save and complete normally like it does today.

Oh and one more thing I noticed, that model-specific options step used to get skipped entirely for models that don't support extended thinking. Since max tokens now lives in that step, it needs to always be shown regardless of whether extended thinking is available, otherwise there'd be no place to set the token limit for those models.

The config flow logic lives under `@homeassistant/components/anthropic/config_flow.py`, so that's where the field reordering and the budget-versus-max-tokens check should land.
