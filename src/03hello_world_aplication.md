# Hello World with the Application trait

It's not needed yet for this app, but in real world programs, it will be a bit more common to implement the `Application` trait instead of `Sandbox`, so let's do it in this example already, there are very few differences needed:
```rust
use iced::widget::{container, text};
use iced::executor;
use iced::{Application, Element, Settings, Theme, Command};

#[derive(Default)]
struct IcedTwentyOne;

#[derive(Debug, Clone, Copy)]
enum Message {
}

impl Application for IcedTwentyOne {
    type Executor = executor::Default;
    type Flags = ();
    type Message = Message;
    type Theme = Theme;

    fn new(_flags: ()) -> (IcedTwentyOne, Command<Self::Message>) {
        (IcedTwentyOne::default(), Command::none())
    }

    fn title(&self) -> String {
        String::from("Iced Twenty-One")
    }

    fn update(&mut self, _message: Self::Message) -> Command<Self::Message> {
        Command::none()
    }

    fn view(&self) -> Element<Self::Message> {
        let hello = text("Hello, Iced! (with application trait)");
        container(hello).into()
    }
}

pub fn main() -> iced::Result {
    IcedTwentyOne::run(Settings::default())
}
```

Not much changed, right? Usually you don't need to worry about the `executor` or about the `flags` if you don't need them, the first is the async executor used (default is fine) and the second are flags your main() may pass to your new() function when you start your iced app.
<br>

```rust
fn update(&mut self, _message: Self::Message) -> Command<Self::Message> {
    Command::none()
}
```

You can see that we are returning a `Command::none()` in the `update()` and `new()` functions, this is a Command that do nothing. Returning an actual Command is how you can execute async actions (Usually with [`Command::perform()`](https://docs.rs/iced/latest/iced/struct.Command.html#method.perform))

 This is all the info you really need by now, but if you want, there's a few more details about the `Application` trait on the [iced docs](https://docs.iced.rs/iced/application/trait.Application.html)
