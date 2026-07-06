I'm working on the inference service API types and I've noticed there's no proper way to configure default resource requests and limits for serving containers through the configuration object. Right now the defaults appear to be stored in a globally shared variable, which makes behavior unpredictable and means operators can't configure different defaults for different environments.

I'd like to add a dedicated resource configuration section to the inference service configuration so that operators can specify default CPU and memory requests and limits. All the serving runtime types — predictors, explainers, and transformers — should then read those defaults from the config object when applying defaults to containers that don't have explicit resource requirements set.

The Python SDK should also be updated to include a corresponding model for this new resource config section, and the main inference service configuration model should accept it as a field.

The key behavior I'm looking for is: when a configuration specifies certain CPU and memory values, any container that goes through the defaulting logic should end up with those values applied as both resource requests and limits, rather than pulling from some global or hardcoded value.
