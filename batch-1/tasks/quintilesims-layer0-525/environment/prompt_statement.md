I'm working on simplifying the environment model in our infrastructure platform. Right now, when you create or update an environment, you have to specify both a minimum and a maximum instance count separately. This is overly complex — I just want to set a single desired scale value. I'd also like to introduce the concept of an environment "type": a "static" type that manages a fixed pool of instances (using the scale value), and a "dynamic" type that only provisions a cluster and security group without creating or managing any instance pool at all.

When creating a dynamic environment, no instance pool should be set up — just the networking and cluster infrastructure. When creating a static environment, the single scale value should drive both the minimum and maximum instance count.

For reading environments, the current count of running instances and the desired scale should be returned separately, replacing the old min/max pair. The environment type should also be returned and stored as a tag alongside the existing name and operating system tags.

The command-line interface should replace the separate minimum and maximum scale flags with a single combined scale flag. Passing a negative scale value should be treated as an input error. When a scale value is provided, the environment should be treated as static.

The environment listing output should be updated so that the environment type is shown for each environment, while the scale and instance type columns are removed. The environment summary listing should similarly gain a type column.

For infrastructure-as-code configurations, the resource and data source definitions for environments should expose a single scale setting and an environment type setting, removing the old separate minimum and maximum scale settings.
