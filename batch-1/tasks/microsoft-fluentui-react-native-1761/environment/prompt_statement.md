I'm building out our React Native component library and there's a gap I keep running into: we don't have a shared notification banner, so every app team hand-rolls their own inline and the styling drifts all over the place. I want to add a proper reusable notification component so we get visual consistency across products and nobody's rebuilding this from scratch.

Here's what I need it to do. It takes a variant prop to indicate the message type, something like primary (informational), neutral, danger, or warning, and it styles itself accordingly per variant. It takes an end-action label as a string (think "Undo" or "Sign in") and it takes children as the main message body. Layout is a horizontal row: message content on the left, action label aligned to the right.

The big thing for me is render stability. The styles shouldn't change between renders when the props haven't changed, so if I render it twice with the same variant and label I should get the same computed styles back, not a fresh object each time. And it needs to re-render cleanly without blowing up when props do change. Basically it should be well-behaved enough that we can drop it into any screen and trust it.

Go ahead and add it into the component library alongside the other shared components.
