# Showing a Card

To don't lose time with non-iced stuff, I'll post here the data structures for the card game, it uses the `rand` crate, so add it to your Cargo.toml:

```sh
[dependencies]
iced = "0.10.0"
rand = "0.8.5"
```

And this is our card related code, save it as card.rs:

```rust
use rand::thread_rng;
use rand::seq::SliceRandom;

#[derive(Debug, Clone, Copy, PartialEq, Eq)]
pub enum Suit {
    Hearts, Diamonds, Clubs, Spades
}

#[allow(dead_code)]
impl Suit {
    pub const ALL: [Suit; 4] = [Suit::Hearts, Suit::Diamonds, Suit::Clubs,Suit::Spades];
}

impl std::fmt::Display for Suit {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        write!(
            f,
            "{}",
            match self {
                Suit::Hearts => "Hearts",
                Suit::Diamonds => "Diamonds",
                Suit::Clubs => "Clubs",
                Suit::Spades => "Spades",
            }
        )
    }
}

#[derive(Debug, Clone, Copy, PartialEq, Eq)]
pub enum Rank {
    Ace, Two, Three, Four, Five, Six, Seven, Eight, Nine, Ten, Jack, Queen, King
}

impl Rank {
    pub fn value(&self) -> u8 {
        match &self {
            Rank::Ace => 1, Rank::Two => 2, Rank::Three => 3,
            Rank::Four => 4, Rank::Five => 5, Rank::Six => 6,
            Rank::Seven => 7, Rank::Eight => 8, Rank::Nine => 9,
            Rank::Ten => 10, Rank::Jack => 10, Rank::Queen => 10,
            Rank::King => 10,
        }
    }
    pub const ALL: [Rank; 13] =
        [Rank::Ace, Rank::Two, Rank::Three, Rank::Four, Rank::Five,
        Rank::Six, Rank::Seven, Rank::Eight, Rank::Nine, Rank::Ten,
        Rank::Jack, Rank::Queen, Rank::King];
}

impl std::fmt::Display for Rank {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        write!(f, "{}",
            match self {
                Rank::Ace => "Ace", Rank::Two => "Two",
                Rank::Three => "Three", Rank::Four => "Four",
                Rank::Five => "Five", Rank::Six => "Six",
                Rank::Seven => "Seven", Rank::Eight => "Eight",
                Rank::Nine => "Nine", Rank::Ten => "Ten",
                Rank::Jack => "Jack", Rank::Queen => "Queen",
                Rank::King => "King",
            }
        )
    }
}

#[derive(Debug, Clone, Copy, PartialEq, Eq)]
pub struct Card {
    suit: Suit,
    rank: Rank,
}

impl Card {
    #[allow(dead_code)]
    pub fn new(suit: Suit, rank: Rank) -> Self {
        Self {suit, rank}
    }

    #[allow(dead_code)]
    pub fn get_id(&self) -> String {
        format!("{}_of_{}", self.rank.to_string(), self.suit.to_string())
    }
}

impl std::fmt::Display for Card {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        write!(f, "{} of {}", self.rank, self.suit)
    }
}

#[derive(Debug, Clone)]
pub struct Deck {
    cards: Vec<Card>,
}

impl Default for Deck {
    fn default() -> Deck {
        Deck::new()
    }
}

impl Deck {
    pub fn new() -> Self {
        let mut deck = Vec::new();
        for rank in Rank::ALL {
            for suit in Suit::ALL {
                deck.push(Card {rank, suit});
            }
        }
        Deck {cards: deck}
    }

    pub fn shuffle(&mut self) {
        self.cards.shuffle(&mut thread_rng());
    }
    pub fn deal_card(&mut self) -> Option<Card> {
        self.cards.pop()
    }
}

#[allow(dead_code)]
#[derive(Debug, Clone)]
pub struct Hand {
    pub soft: bool,
    pub cards: Vec<Card>,
}

#[allow(dead_code)]
impl Hand {
    pub fn new() -> Self {
        Self { cards: Vec::new(), soft: false }
    }
    pub fn value(&self) -> u8 {
        let mut hand_value = if self.soft {
            10
        } else {
            0
        };
        for card in &self.cards {
            hand_value = hand_value + card.rank.value();
        }
        hand_value
    }
    pub fn push(&mut self, card: Card) {
        if card.rank == Rank::Ace && self.value() < 11 {
            self.soft = true;
        }
        self.cards.push(card);
        if self.soft && self.value() > 21 {
            self.soft = false;
        }
    }
    pub fn pop(&mut self) -> Option<Card> {
        self.cards.pop()
    }
    pub fn is_blackjack(&self) -> bool {
        if self.cards.len() == 2 && self.value() == 21 {
            true
        } else {
            false
        }
    }
}
```
