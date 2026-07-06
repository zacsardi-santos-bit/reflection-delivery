I'm working on improving the accessibility and consistency of the Explanation widget in our Perseus question renderer. Right now, the widget has separate rendering paths for mobile vs. desktop and article vs. exercise contexts. On mobile it uses an anchor tag styled to look like a button, and on desktop it wraps the button text in brackets — neither of which is properly accessible or semantically correct.

I'd like to redesign the widget so it uses a single, unified implementation that works in all contexts. Specifically, it should use a proper button element with standard accessibility attributes that communicate the current state (expanded or collapsed) to assistive technologies. The button's label should reflect the current state and change when toggled.

The content section controlled by the button should always be present in the DOM (rather than conditionally rendered), with its visibility managed through CSS so we can support smooth animated transitions when expanding or collapsing. These transitions should only apply when the user hasn't opted for reduced motion — we should check the user's motion preference and skip transitions if they prefer reduced motion.

Users should be able to expand and collapse the widget using a mouse click, the Enter key, or the Space bar.

I'd also like to update the graded-group widget's hint button labels from the current "[Hint]" / "[Hide hint]" style to something more natural — specifically "Explain" to show the hint and "Hide explanation" to dismiss it.
