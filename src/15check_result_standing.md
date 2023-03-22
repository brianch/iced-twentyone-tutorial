# Check for result when player stands

Now let's continue with the endgame scenarios and check the hand values after the player stand, we can just add the logic to our `update()`:

```rust
} Message::Stand => {
    self.game_stage = GameStage::Standing;
    if self.dealer_hand.value() == self.player_hand.value() {
        self.game_stage = GameStage::Tie;
    } else if self.dealer_hand.value() > self.player_hand.value()  {
        self.game_stage = GameStage::HouseWon;
    } else {
        self.game_stage = GameStage::PlayerWon;
    }
}
```

Since we already have the view prepared, this was all we needed.

![screenshot of the current gui](/img/15check_result_standing.jpg)
