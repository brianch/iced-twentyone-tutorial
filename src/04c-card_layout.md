# Starting to change the layout 

Now the image is being displayed, but we could improve a bit the layout already, let's try to change the container like this:
```rust
container(card)
    .width(Length::Fill)
    .height(Length::Fill)
    .align_y(Vertical::Bottom)
    .center_x()
    .padding(40)
    .into()
```

And remember to add the new imports (in general Rust or your IDE will suggest these new imports to you, so I'll omit them going forward):

```rust
use iced::{executor, Length};
use iced::alignment::Vertical;
```
About the container changes:

- `padding` is self explanatory, but nice to point out that it's space you leave *inside* the container's bondaries. (so e.g. if the container has a background color, the padded area will still be colored).
- `.align_y(Vertical::Bottom)` Place the container's content at the bottom vertically.
- `center_x()` center the container's content horizontally.

Considering that the *center* is centering the content that's *inside* the container, it only makes sense if there's empty room left in the container to center things around. The default behaviour on width and height is to shrink to the content though, so that's why we also needed:

- `.width(Length::Fill)` The container will fill all the remaining horizontal space (in this case, of the window, since it's not inside anything).
- `.height(Length::Fill)`The container will fill all the remaining vertical space.

![screenshot of the current gui](/img/04c-card_layout.jpg)
