# Start with the second card facing down

## Dealer's hand

We shouldn't be seeing the second card right away, let's fix this.

For the dealer's hand it's quite simple for now, since they're always with 2 cards, we can just change it to:

```rust
let dealer_row = row![
    image(String::from("img/") + &self.dealer_hand.cards[0].get_id() + ".png").height(Length::Fixed(200.)),
    image(String::from("img/back.png")).height(Length::Fixed(200.))
].spacing(10);
```

And let's hide the dealer's hand value as well, otherwise there's no point in hiding the card:
```rust
let dealer_info = container(
    col![
        dealer_row,
        text("?").size(35),
        Rule::horizontal(4.),
```

## Player's hand

Our hand is a bit more tricky, since there's more to do already.

This initial dealing of the 2 cards and the later part with the player dealing cards have quite a number of different UI elements, so perhaps it's good to have a way to know in which part of the game we're on, let's create a new enum to indicate in which stage of the game we're in and add it to our app's state:

(I'm only showing the changed lines, but you can click on the eye button to see everything)

```rust
#[derive(PartialEq)]
enum GameStage {
    Init, Dealing
}

struct IcedTwentyOne {
#    deck: Deck,
#    player_hand: Hand,
#    dealer_hand: Hand,
    game_stage: GameStage,
}
impl Default for IcedTwentyOne {
    fn default() -> IcedTwentyOne {
#        let mut deck = Deck::new();
#        let mut player = Hand::new();
#        let mut dealer = Hand::new();
#        deck.shuffle();
#        player.push(deck.deal_card().unwrap());
#        player.push(deck.deal_card().unwrap());
#        dealer.push(deck.deal_card().unwrap());
#        dealer.push(deck.deal_card().unwrap());

        IcedTwentyOne {
#            deck: deck,
#            player_hand: player,
#            dealer_hand: dealer,
            game_stage: GameStage::Init,
        }
    }
}
```

If we're gonna hide our second card, we also need a way to start the game, so let's create a new `Message` for that:

```rust
#[derive(Debug, Clone, Copy)]
enum Message {
    Start,
    DealCard
}
```

And add the processing of this message in our `update()`:
```rust
fn update(&mut self, message: Self::Message) -> Command<Self::Message> {
    match message {
        Message::DealCard => {
            if let Some(new_card) = self.deck.deal_card() {
                self.player_hand.push(new_card);
            }
        } Message::Start => {
            self.game_stage = GameStage::Dealing;
        }
    }
    Command::none()
}
```

But this message has to come from somewhere, so along with changing the card's image and the hand's value, we need to have a button to start the game. These are a lot of changes, so we'll need to separate our `column!` macro call in two.


We'll create the `Column` in a variable, with an `if` to check which version we want:

```rust
let player_info_col = if self.game_stage == GameStage::Init {
    col![
        Rule::horizontal(4.),
        text("?").size(35),
        row![
            image(String::from("img/") + &self.player_hand.cards[0].get_id() + ".png").height(Length::Fixed(200.)),
            image(String::from("img/back.png")).height(Length::Fixed(200.)),
        ].spacing(10),
        button(text("Start Game")).on_press(Message::Start),
    ].width(Length::Fill).align_items(iced::Alignment::Center).spacing(20)
} else {
    let mut player_row = Row::new().spacing(10);
    for card in &self.player_hand.cards {
        player_row = player_row.push(image(String::from("img/") + &card.get_id() + ".png").height(Length::Fixed(200.)));
    }
    col![
        Rule::horizontal(4.),
        text(self.player_hand.value().to_string()).size(35),
        player_row,
        button(text("Deal another card")).on_press(Message::DealCard),
    ].width(Length::Fill).align_items(iced::Alignment::Center).spacing(20)
};
```

we then, just pass that to the container later:
```rust
let player_info = container(player_info_col).height(Length::Fill).align_y(Vertical::Bottom);
```
## Summary

So, to get everything together, this is our current `view()`:
```rust
fn view(&self) -> Element<Message, iced::Renderer<theme::TwentyOneTheme>> {

    let dealer_row = row![
        image(String::from("img/") + &self.dealer_hand.cards[0].get_id() + ".png").height(Length::Fixed(200.)),
        image(String::from("img/back.png")).height(Length::Fixed(200.))
    ].spacing(10);

    let dealer_info = container(
        col![
            dealer_row,
            text("?").size(35),
            Rule::horizontal(4.),
        ].width(Length::Fill).align_items(iced::Alignment::Center).spacing(20)
    ).height(Length::Fill).align_y(Vertical::Top);

    let player_info_col = if self.game_stage == GameStage::Init {
        col![
            Rule::horizontal(4.),
            text("?").size(35),
            row![
                image(String::from("img/") + &self.player_hand.cards[0].get_id() + ".png").height(Length::Fixed(200.)),
                image(String::from("img/back.png")).height(Length::Fixed(200.)),
            ].spacing(10),
            button(text("Start Game")).on_press(Message::Start),
        ].width(Length::Fill).align_items(iced::Alignment::Center).spacing(20)
    } else {
        let mut player_row = Row::new().spacing(10);
        for card in &self.player_hand.cards {
            player_row = player_row.push(image(String::from("img/") + &card.get_id() + ".png").height(Length::Fixed(200.)));
        }
        col![
            Rule::horizontal(4.),
            text(self.player_hand.value().to_string()).size(35),
            player_row,
            button(text("Deal another card")).on_press(Message::DealCard),
        ].width(Length::Fill).align_items(iced::Alignment::Center).spacing(20)
    };

    let player_info = container(player_info_col).height(Length::Fill).align_y(Vertical::Bottom);

    let table_col = col![
        dealer_info,
        player_info,
    ].align_items(iced::Alignment::Center).spacing(10);

    container(table_col)
        .width(Length::Fill)
        .height(Length::Fill)
        .center_y()
        .padding(40)
        .into()
}
```

![screenshot of the current gui](/img/11face_down.jpg)
