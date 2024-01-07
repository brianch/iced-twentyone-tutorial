# Organizing a palette

Our current theme code works but all those colors spread across the file are not ideal, so let's improve this.

At the end of our theme add a new struct:

```rust
#[derive(Debug, Clone, Copy, PartialEq)]
pub struct TwentyOnePalette {
    pub primary: Color,
    pub secondary: Color,
    pub tertiary: Color,
    pub text_light: Color,
    pub text_dark: Color,
}
```
Now we can create our current green palette as a constant:

```rust
impl TwentyOnePalette {
    pub const GREEN: Self = Self {
        primary: Color::from_rgb(
            0x47 as f32 / 255.0,
            0x7c as f32 / 255.0,
            0x47 as f32 / 255.0,
        ),
        secondary: Color::from_rgb(
            0x32 as f32 / 255.0,
            0x47 as f32 / 255.0,
            0x31 as f32 / 255.0,
        ),
        tertiary: Color::WHITE,
        text_dark: Color::BLACK,
        text_light: Color::WHITE,
    };
}
```

Let's create a helper function in our `TwentyOneTheme` to get the palette corresponding to our current theme variant:

```rust
#[derive(Debug, Clone, Copy, PartialEq, Eq, Default)]
pub enum TwentyOneTheme {
    #[default]
    Green,
}

impl TwentyOneTheme {
    pub fn palette(&self) -> TwentyOnePalette {
        match self {
            TwentyOneTheme::Green => TwentyOnePalette::GREEN,
        }
    }
}
```
There's no need for that match right now of course, but we'll create another theme in the next Chapter.

We can now use the colors from the palette instead of using them directly, here's our updated button StyleSheet:

```rust
impl button::StyleSheet for TwentyOneTheme {
    type Style = ButtonStyle;

    fn active(&self, style: &Self::Style) -> button::Appearance {
        match style {
            ButtonStyle::Table => {
                button::Appearance {
                    background: Some(iced::Background::Color(self.palette().tertiary)),
                    text_color: self.palette().text_dark,
                    ..Default::default()
                }
            } ButtonStyle::Menu => {
                button::Appearance {
                    background: Some(iced::Background::Color(self.palette().primary)),
                    text_color: self.palette().text_light,
                    ..Default::default()
                }
            }
        }
    }
    fn hovered(&self, style: &Self::Style) -> button::Appearance {
        match style {
            ButtonStyle::Table => {
                button::Appearance {
                    background: Some(iced::Background::Color(self.palette().secondary)),
                    text_color: Color::WHITE,
                    ..Default::default()
                }
            } ButtonStyle::Menu => {
                button::Appearance {
                    background: Some(iced::Background::Color(self.palette().tertiary)),
                    text_color: Color::BLACK,
                    ..Default::default()
                }
            }
        }
    }
```

And here's our updated `container::StyleSheet`:
```rust
impl container::StyleSheet for TwentyOneTheme {
    type Style = ContainerStyle;

    fn appearance(&self, style: &Self::Style) -> container::Appearance {
        match style {
            ContainerStyle::Table => {
                container::Appearance {
                    text_color: Some(self.palette().text_light),
                    background: Some(iced::Background::Color(self.palette().primary)),
                    ..Default::default()
                }
            } ContainerStyle::Menu => {
                container::Appearance {
                    text_color: Some(self.palette().text_light),
                    background: Some(iced::Background::Color(self.palette().secondary)),
                    ..Default::default()
                }
            }
        }
    }
}
```
