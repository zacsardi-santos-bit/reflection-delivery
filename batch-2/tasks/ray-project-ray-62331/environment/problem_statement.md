## Description

The autoscaling delay mechanism in Ray Serve is broken when the control loop runs slower than the standard interval. Currently the code determines when to fire a scale-up or scale-down by counting how many control loop iterations have occurred, then comparing to a fixed count derived from the configured delay. This assumption — that each iteration takes exactly the standard interval — breaks down in practice.

On a loaded cluster, a single control loop iteration may take several times longer than the baseline interval. Because the delay logic counts iterations rather than measuring real elapsed time, a configured 100-second upscale delay can silently stretch to 600 seconds or more, causing deployments to react to traffic changes far later than intended.

## Expected Behavior

- The autoscaling delay logic should track elapsed wall-clock time from when a consistent scaling direction was first detected, and fire the scaling decision once that elapsed time meets or exceeds the configured delay.
- Regardless of how long individual control loop iterations take, a configured delay of N seconds should result in the scaling action firing after approximately N seconds of real elapsed time — not after a fixed number of iterations that might represent many times N seconds.
- The overshoot should be at most one iteration duration beyond the configured delay, not a multiple of the iteration duration.

## Why This Matters

Operators rely on upscale and downscale delay settings to tune autoscaling responsiveness. If those delays are not respected in wall-clock time, deployments under load scale far too slowly, leading to queued requests, latency spikes, and unpredictable behavior. Fixing this makes autoscaling predictable and trustworthy even when the control loop is under pressure.
