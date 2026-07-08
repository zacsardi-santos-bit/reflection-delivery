I'm poking at the inference service API types and there's no clean way to configure default resource requests and limits for serving containers through the config object. Right now the default CPU and memory values get pulled from a globally shared mutable variable, which makes behavior unpredictable, especially when different parts of the system run concurrently or when tests run in sequence, and it means operators can't set different defaults per environment.

What I want is a dedicated resource configuration section on the inference service configuration so operators can specify default CPU and memory requests and limits as part of the standard config. Then all the serving runtime types, the built-in predictors, explainers, and transformers, should read those defaults from the passed-in config object instead of reaching for that global value when they apply defaults to containers that don't have explicit resource requirements set.

The key behavior: when a config specifies certain CPU and memory values, any container going through the defaulting logic ends up with those exact values applied as both resource requests and limits, rather than from some global or hardcoded default.

Oh and the Python SDK needs a matching model for this new resource config section too, and the main inference service configuration model should accept it as a field.
