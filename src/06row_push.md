# A proper hand, with multiple cards

First let's show the value of our hand on the GUI, to make it a bit more pretty, we'll also introduce a new widget, `Rule` (don't forget to import it), that draws a vertical or horizontal line, used for separation:

```rust
fn view(&self) -> Element<Self::Message> {

    let img_path = String::from("img/") + &self.player_hand.cards[0].get_id() + ".png";

    let table_col = col![
        image(img_path).height(Length::Fixed(500.)),
        Rule::horizontal(4.),
        text(self.player_hand.value().to_string()).size(35),
        button(text("Deal another card")).on_press(Message::DealCard)
    ].align_items(iced::Alignment::Center).spacing(10);
```
The `.size(35)` is what you would expect, it changes the size of the text.

We want a hand with multiple cards, as we will need in the actual game, so let's stop removing the previous card, just delete the pop() line we had on our update function:

```rust
fn update(&mut self, message: Self::Message) -> Command<Self::Message> {
    match message {
        Message::DealCard => {
            if let Some(new_card) = self.deck.deal_card() {
                self.player_hand.push(new_card);
```

Up to now, we had being using the shorthand functions that return us the actual widget, they make the code less verbose, but when it's not exactly known which or how many widgets we'll have inside a `Column`, or `Row`, we need to use it's `new()` and `push()` functions instead.

```rust
fn view(&self) -> Element<Self::Message> {

    let mut player_row = Row::new();
    for card in &self.player_hand.cards {
        player_row = player_row.push(image(String::from("img/") + &card.get_id() + ".png").height(Length::Fixed(250.)));
    }

    let table_col = col![
        player_row,
        Rule::horizontal(4.),
        text(self.player_hand.value().to_string()).size(35),
        button(text("Deal another card")).on_press(Message::DealCard)
    ].align_items(iced::Alignment::Center).spacing(10);

```
Here we created the `Row` first, with `Row::new()` iterate over the cards in our hand and call `.push()` to add each one to the row. We then add the `Row` (instead of the `Image` directly as before) to the "player_col" `Column`.

![screenshot of the current gui](/img/06row_push.jpg)
