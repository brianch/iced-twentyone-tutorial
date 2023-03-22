# Adding the dealer's hand

Going on with the actual game functions, it's about time we create and show the dealer's hand, we can just copy paste what we have from the player's hand, let's start by adding the dealer's hand to our state:

```rust
struct IcedTwentyOne {
    deck: Deck,
    player_hand: Hand,
    dealer_hand: Hand,
}
```

And start the dealer's hand the same way we did for the player's:
```rust
impl Default for IcedTwentyOne {
    fn default() -> IcedTwentyOne {
        let mut deck = Deck::new();
        let mut player = Hand::new();
        let mut dealer = Hand::new();
        deck.shuffle();
        player.push(deck.deal_card().unwrap());
        player.push(deck.deal_card().unwrap());
        dealer.push(deck.deal_card().unwrap());
        dealer.push(deck.deal_card().unwrap());

        IcedTwentyOne {
            deck: deck,
            player_hand: player,
            dealer_hand: dealer
        }
    }
}
```

And now that the state is there, we can show it on our `view()` just like the player's hand:

```rust
fn view(&self) -> Element<Message, iced::Renderer<theme::TwentyOneTheme>> {
    let mut dealer_row = Row::new();
    for card in &self.dealer_hand.cards {
        dealer_row = dealer_row.push(image(String::from("img/") + &card.get_id() + ".png").height(Length::Fixed(250.)));
    }
```

And add the dealer's info to the *table_col* too, again, just like we did the player's, including also the hand value:
```rust
    let table_col = col![
        text(self.dealer_hand.value().to_string()).size(35),
        Rule::horizontal(4.),
        dealer_row,
        player_row,
        Rule::horizontal(4.),
        text(self.player_hand.value().to_string()).size(35),
        button(text("Deal another card")).on_press(Message::DealCard),
    ].align_items(iced::Alignment::Center).spacing(10);
```

![screenshot of the current gui](/img/09dealer_hand.jpg)

