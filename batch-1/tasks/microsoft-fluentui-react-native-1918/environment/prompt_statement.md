I'm working on updating the Badge component in our design system. There are two issues I need to fix.

First, the badge container currently uses a fixed height for each size variant. This means that if the badge's content is taller than expected — for example, if text wraps or an image is larger — the content just clips or overflows. I'd like the container to instead enforce a minimum height so the badge can grow to fit its content while still respecting the intended minimum size for each variant.

Second, the text inside the badge doesn't apply any font styling at all right now. The font family, size, weight, and padding are completely unspecified, so the text just inherits whatever happens to be around it. I'd like the badge to explicitly apply a font family (the primary one from the design system theme), a font size appropriate for each badge size variant, a semibold font weight, and some horizontal padding around the text. For the "large" size specifically, the font size should be 12, the font weight "600", the font family should resolve to "Segoe UI" from the theme, and the text padding should be 2.

These changes should be reflected in the badge's token definitions and its styling configuration so all size variants are updated consistently.
