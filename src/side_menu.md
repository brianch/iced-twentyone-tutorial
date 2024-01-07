# Adding a side column

Just to vary things a bit, let's add a sidebar with a Restart button to don't have to reopen the app to play again. You can try it first if you want, there's nothing we haven't seen before.

This is one way to do it:

```rust
let table_col = col![
    dealer_info,
    game_result,
    player_info,
].align_items(iced::Alignment::Center).spacing(10).padding(30);

let menu_col = col![
    container(
        button(text("Restart")),
    ).height(Length::Fill).center_y().width(Length::Fill).center_x()
].align_items(iced::Alignment::Center).spacing(10).width(Length::Fixed(120.));

let row_ui = row![menu_col, table_col];

container(row_ui)
    .width(Length::Fill)
    .height(Length::Fill)
    .center_y()
    .into()
```

we create a new `Column` (our sidebar) and add it and our "table_col" to a new `Row`.

You may have noticed that I also switched the padding from the main `Container` to the `table_col`, since it was mainly in that area that we wanted to have some sort of margin. In [chapter 16](./new_button_style.md) the padding on the main container would cause an issue, you may leave it there just to see what would happen.
