Implement a function to improve the Horizontal Pod Autoscaler controller by clearly distinguishing between different reasons for scaling decisions. Extract the logic into a standalone function that applies specific rules and returns both the normalized replica count and the reason for any adjustments.

Requirements:

*   Implement the `convertDesiredReplicasWithRules` function with the signature:
    *   `convertDesiredReplicasWithRules(currentReplicas, desiredReplicas, hpaMinReplicas, hpaMaxReplicas int32) (int32, string, string)`
    *   Place the function in the file: `vendor/k8s.io/kubernetes/pkg/controller/podautoscaler/horizontal.go`
*   Ensure the function returns three values: a normalized int32 replica count, a string condition reason, and a string message.
*   Handle the following scenarios:
    *   If `desiredReplicas` is within the range `[hpaMinReplicas, hpaMaxReplicas]` and does not exceed the scale-up rate limit, return the `desiredReplicas` unchanged with condition reason 'DesiredWithinRange'.
    *   If `desiredReplicas` is less than `hpaMinReplicas`, return `hpaMinReplicas` as the normalized count with condition reason 'TooFewReplicas'.
    *   If `hpaMinReplicas` is 0 or less and `desiredReplicas` is 0 or less, enforce a minimum of 1 replica and return condition reason 'TooFewReplicas'.
    *   If `desiredReplicas` exceeds `hpaMaxReplicas` and `hpaMaxReplicas` is less than the scale-up rate limit, return `hpaMaxReplicas` with condition reason 'TooManyReplicas'.
    *   If `desiredReplicas` exceeds the scale-up rate limit but `hpaMaxReplicas` is greater than or equal to the scale-up rate limit, return the scale-up rate limit (as computed by `calculateScaleUpLimit(currentReplicas)`) with condition reason 'ScaleUpLimit'.
    *   If the HPA would naturally scale above `maxReplicas` based on metrics (e.g., desired is 24 but `maxReplicas` is 20), cap the replica count at `maxReplicas` and report the ScalingLimited condition with Status=True and Reason='TooManyReplicas'.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.