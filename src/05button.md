# Drawing a card - Receiving our first Message

It's a bit clunky to have to restart the app to get another card, isn't it? It would be better to have a button to deal another one, so let's do it! This will also be our first time receiving a message in our application.

We can start by creating the `Button` itself. To organize our widgets, we will be using a `Column` now, the `column!` macro is a shorthand to create a Column widget containing all the widgets we pass inside the array it receives.

Since `rust-analyzer` (the thing that checks for errors, if you're on an IDE) may confuse the iced column! macro with the unrelated Rust one, I imported it with another name ("col") here:

```rust
use iced::widget::{image, container, button, text, column as col};

fn view(&self) -> Element<Self::Message> {

    let img_path = String::from("img/") + &self.player_hand.cards[0].get_id() + ".png";

    let table_col = col![
        image(img_path).height(Length::Fixed(500.)),
        button(text("Deal another card")),
    ].align_items(iced::Alignment::Center).spacing(10);

    container(table_col)
        .width(Length::Fill)
        .height(Length::Fill)
        .align_y(Vertical::Bottom)
        .center_x()
        .padding(40)
        .into()
}
```

New things:
- `.align_items(iced::Alignment::Center)` - Center the content of the column horizontally ("The container is already centered, so why do we need this?" Try removing it and you'll notice)

- `.spacing(10)` - Spacing between the elements of the column (So there's a bit of room between card and button). You may wonder what's the difference between spacing and padding, to answer this, try replacing this spacing with a .padding(10) on the button.

- `height(Length::Fixed(500.))` The card image itself is big enough that it wouldn't leave room for the button if we used it's total size, so we resize it to 500 height. Try removing this and see what happens.
---------------
If you run the program now, you'll notice that the button is currently grayed out. This is because we haven't passed anything to the button's `on_press()` function yet, so nothing can happen. Not calling `.on_press()` is actually how you disable a button.

But now it's time to finally create our "deal card" Message and process this event, let's update our `Message` enum like so:

```rust
enum Message {
    DealCard
}
```

And change the view to tell the button that he needs to create a DealCard message when someone clicks on it:

```rust
button(text("Deal another card")).on_press(Message::DealCard)
```

Now that the button is generating the `Message::DealCard`, we need to process it in our `update()`:

```rust
fn update(&mut self, message: Self::Message) -> Command<Self::Message> {
    match message {
        Message::DealCard => {
            if let Some(new_card) = self.deck.deal_card() {
                self.player_hand.pop();
                self.player_hand.push(new_card);
            }
        }
    }
    Command::none()
}
```

If we receive a `DealCard` message, we try to deal another card, if we're sucessfull (if there's cards left), we replace the card in our hand with this new one (it's not how the game actually works, but we'll get a bit closer in the next chapter).

As an exercise, you may want to disable the button if there are no more cards left in the deck.

![screenshot of the current gui](/img/05button.jpg)
