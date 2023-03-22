# Adding a new theme variant

Now it's very easy to add more color themes, let's add another constant to our `TwentyOnePalette`:

```rust
pub const BURGUNDY: Self = Self {
    primary: Color::from_rgb(
        0x99 as f32 / 255.0,
        0x62 as f32 / 255.0,
        0x6b as f32 / 255.0,
    ),
    secondary: Color::from_rgb(
        0x4b as f32 / 255.0,
        0x30 as f32 / 255.0,
        0x34 as f32 / 255.0,
    ),
    tertiary: Color::WHITE,
    text_dark: Color::BLACK,
    text_light: Color::WHITE,
};
```

And update our `TwentyOneTheme` and `palette()` function accordingly:

```rust
#[derive(Debug, Clone, Copy, PartialEq, Eq, Default)]
pub enum TwentyOneTheme {
    Green,
    #[default]
    Burgundy
}

impl TwentyOneTheme {
    pub fn palette(&self) -> TwentyOnePalette {
        match self {
            TwentyOneTheme::Green => TwentyOnePalette::GREEN,
            TwentyOneTheme::Burgundy => TwentyOnePalette::BURGUNDY,
        }
    }
}
```

Much easier to change color variants with them separated like this than having to copy paste colors everywhere.

You can switch in which variant you put the `#[default]` to check that both are working, in the next chapter we'll add an option to switch theme in-game.

![screenshot of the current gui](/img/21burgundy.jpg)
