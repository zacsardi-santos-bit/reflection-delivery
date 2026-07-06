I'm working in the Paddle-Lite framework and I need a reusable timing utility. Right now several parts of the codebase each define their own local way to get the current time in microseconds and compute durations, which is messy and hard to maintain.

I'd like a simple timer class under the utilities directory that I can use to start timing, stop and get the elapsed milliseconds back as a float, and sleep for a specified number of milliseconds. It should also keep track of the minimum, maximum, and average elapsed times across multiple start/stop cycles, and have a method to print those statistics. The timing measurements should be accurate — within about 10% of the actual wall-clock time measured independently.

Could you implement this timer utility so it compiles cleanly and passes a basic accuracy test?
