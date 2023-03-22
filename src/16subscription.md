# Let dealer draw cards: A Subscription

The game isn't very fair right now, the dealer should be able to get more cards too.

And it would be a bit boring to just add all cards they want all at once, it would be better if we added them one by one, this isn't the main use of it, but it's a good time to introduce the iced `Subscription`.

A `Subscription` is used when you need to listen (without blocking the GUI) to external events, like window events, listening to an external process stdout, getting data from a device or a network connection, etc...). You run the `Subscription` and the Subscription thread will keep running and sending `Messages` to your application when necessary.

Here our use case is super simple, we will have a Subscription that produces a message at a set interval and will deal a new card to the dealer each time until they no longer need more. `iced_futures` has a [`every()`](https://docs.rs/iced_futures/latest/iced_futures/backend/native/smol/time/fn.every.html) function that does just that.

For more normal use cases, you would probably want to use the [`subscription::unfold()`](https://docs.iced.rs/iced/subscription/fn.unfold.html) function that will run your code (which should be an async `Stream`). It's a complicated thing if you're not familiar with `async` code, but on iced's discord you can find a bunch of discussions about this, which might help you.

-----------
But let's prepare the game logic first, we need to define the `Message` the subscription will return to us when it's time to deal another card to the dealer, don't worry about the argument it receives, the `every()` function returns the time that has passed in case we need, but we don't care about it in this case:

```rust
#[derive(Debug, Clone, Copy)]
enum Message {
    Start,
    DealCard,
    Stand,
    DealerDraw(Instant),
}
```

I think using blackjack's rule about the dealer hitting on a hand lower than 17 works nicely here too. We can keep our `game_stage` field at "*Standing*" and draw cards until we have a result.

We're also processing the `DealerDraw(_)` message on our `update()`, no mysteries there, just draw a card and check if we got a result.

```rust
} Message::Stand => {
    self.game_stage = GameStage::Standing;
    if self.dealer_hand.value() == self.player_hand.value() {
        self.game_stage = GameStage::Tie;
    } if self.dealer_hand.value() > self.player_hand.value()  {
        self.game_stage = GameStage::HouseWon;
    } else if self.dealer_hand.value() < 17 {
        self.game_stage = GameStage::Standing;
    } else {
        self.game_stage = GameStage::PlayerWon;
    }
} Message::DealerDraw(_) => {
    self.dealer_hand.push(self.deck.deal_card().unwrap());
    if self.dealer_hand.value() > 21 {
        self.game_stage = GameStage::PlayerWon;
    } else if self.dealer_hand.value() > self.player_hand.value()  {
        self.game_stage = GameStage::HouseWon;
    } else if self.dealer_hand.value() == self.player_hand.value()  {
        self.game_stage = GameStage::Tie;
    }
}

```

For `async` stuff Iced has three backends, `tokio`, `async-std`, and `smol`. To use the `every()` function I mentioned before, we need to choose one of these using the feature of the same name. It doesn't really matter much which one, let's go wtih smol, add it to your Cargo.toml:

```sh
iced = {version = "0.8.0", features = ["image", "smol"] }
```

Add these new imports to our main.rs:

```rust
use iced::Subscription;
use std::time::{Duration, Instant};
use iced::time;
```

This will be our `Subscription` function, place it under the `view()`:

```rust
fn subscription(&self) -> Subscription<Message> {
    match self.game_stage {
        GameStage::Standing => {
            time::every(Duration::from_millis(1000)).map(Message::DealerDraw)
        } _ => Subscription::none(),
    }
}
```

While the game is at the "standing" stage we keep getting cards for the dealer (which are being processed on our `update()` and when they don't want more cards we return `Subscription::none()`. Not calling the subscription anymore is how we stop it.

And that was all we needed.

![screenshot of the current gui](/img/16subscription.jpg)
