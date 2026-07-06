I'm working on the CircleCI CLI's policy evaluation commands and I'd like to add config compilation support before policy decisions are made.

Right now, when I run the policy evaluation or raw evaluation commands with an input config file, the config is sent directly to the policy decision API in its raw form. The problem is that in real CircleCI pipelines, configs go through a compilation step first (orb expansion, etc.), and policies should be evaluated against the compiled version — not just the source. Evaluating raw configs can lead to misleading policy results.

I want the commands to compile the config through the CircleCI API by default before sending it for evaluation. The compiled result should be included alongside the original source config under a special sentinel key so policies can inspect both. If the source config already has that sentinel key at the top level, the compiled output should have it stripped before being embedded.

I also need a way to opt out of compilation — a flag that lets users skip the compile step and evaluate against the source config directly, which is useful in local development or when compilation isn't needed.

Since compilation requires calling the CircleCI API, it also needs an owner ID. When compilation is enabled and a local policy file is used without providing an owner ID, the command should fail with a clear error pointing users to the opt-out flag. Also, the current restriction that prevents using a local policy path together with an owner ID should be removed, since that combination is now useful when compilation is enabled.
