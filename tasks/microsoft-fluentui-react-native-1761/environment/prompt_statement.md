I'm working on a React Native component library and I need to add a new notification banner component. Right now there's no reusable notification component in the library, so each app team has to build their own inline — leading to inconsistent styling across products.

I need a component that takes a variant (to indicate message type, like primary, neutral, danger, or warning), an end-action label string, and children as the main message body. It should lay them out in a horizontal row with the message on the left and the action label on the right, styled appropriately for each variant.

The component needs to render stably — its styles shouldn't change between renders when props haven't changed, and it should re-render correctly without issues.
