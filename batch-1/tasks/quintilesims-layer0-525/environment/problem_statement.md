## Description

The current environment model requires users to specify both a minimum and a maximum instance count when creating or updating environments. This is unnecessarily complex — most use cases only need a single desired scale value. Additionally, the system has no concept of environment "type": there is currently no way to create a dynamically-scaled environment that only provisions a cluster without managing a fixed pool of instances.

## Expected Behavior

- When creating or updating an environment, users should specify a single scale value instead of separate min and max values.
- Environments should support two types: a "static" type where the user controls the number of running instances, and a "dynamic" type where the platform manages scaling without a fixed instance count.
- Creating a dynamic environment should only provision a cluster and security group — no instance pool should be created.
- The environment listing output should display the environment type prominently. The scale and instance type columns should be removed from the listing view, showing instead the environment type for each row.
- The environment summary listing should also show the environment type.
- When reading environment details, the current scale should reflect the number of actually-running instances, and a desired scale should be returned rather than a min/max pair.
- Infrastructure-as-code configurations for environments should use a single scale attribute and an environment type attribute, replacing the separate min/max scale attributes.

## Why This Matters

The min/max model adds cognitive overhead without providing real value for most users, who simply want to set a target number of instances. Supporting a dynamic environment type also enables more flexible deployment patterns where scaling is handled automatically rather than requiring users to manage a fixed instance range.
