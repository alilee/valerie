# Valerie

[![CI](https://github.com/emmanuelantony2000/valerie/workflows/CI/badge.svg)](
https://github.com/emmanuelantony2000/valerie/actions)
[![License](https://img.shields.io/badge/license-MIT%20OR%20Apache--2.0-blue.svg)]()
[![Cargo](https://img.shields.io/crates/v/valerie.svg)](
https://crates.io/crates/valerie)
[![Documentation](https://docs.rs/valerie/badge.svg)](
https://docs.rs/valerie)
[![Discord](https://img.shields.io/discord/731850006713204777)](https://discord.gg/xx2sArF)

Rust front-end framework for building web apps.

*Valerie is still in a very early phase.
A lot of features are not available at the moment.
A lot of work is left and you are welcome to try it out.*

 - No Virtual DOM.
 - UI can be made in a simple manner,
 by following an MVVM architecture rather an MVC architecture.
 - Use state variables to update the UI where required.
 - Written without any unsafe code.

## Architecture

 - Every UI element has to implement the `Component` trait.
 - A page is a function which returns a `Node`.
 - Two type of State variables 
   - `StateAtomic` for types implementing `Copy`.
   - `StateMutex` for types implementing `Clone`.

## Setting up

 - Run `cargo new --lib some_name`
 - Add `valerie` to the dependencies
 - Create a `static` directory and create an `index.html` inside it

```html
<!doctype html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>Title</title>
        <script type="module">
            import init from "./wasm.js"
            init()
        </script>
    </head>
    <body></body>
</html>
```

 - Also in the `Cargo.toml` enable lto.
 
```toml
[profile.release]
lto = true
opt-level = 3
```

 - Compile it using [`wasm-pack`](https://github.com/rustwasm/wasm-pack) by running
 `wasm-pack build --target web --out-name wasm --out-dir ./static`
 - Use some server, like [`miniserve`](https://github.com/svenstaro/miniserve),
 to host the `./static` folder and try it out.
 
Take a look at `wasm-pack` [docs](https://rustwasm.github.io/docs/wasm-pack/) for more options.

## Examples

### Hello world

```rust
use valerie::prelude::components::*;
use valerie::prelude::*;

fn ui() -> Node {
    h1!("Hello World").into()
}

#[valerie(start)]
pub fn run() {
    App::render_single(ui());
}
```

### Add and Subtract one using a Button

```rust
use valerie::prelude::components::*;
use valerie::prelude::*;

fn ui() -> Node {
    let value = StateAtomic::new(0isize);

    div!(
        h1!("Value ", value.clone()),
        button!("Add 1")
            .on_event("click", value.clone(), move |x, _| {
                *x += 1;
            }),
        button!("Subtract 1")
            .on_event("click", value.clone(), move |x, _| {
                *x -= 1;
            })
    )
    .into()
}

#[valerie(start)]
pub fn run() {
    App::render_single(ui());
}
```

### Time Counter

```rust
use valerie::prelude::components::*;
use valerie::prelude::*;
use wasm_timer::Delay;

fn ui() -> web_sys::Node {
    let timer = StateAtomic::new(0);

    execute(time(1, timer.clone()));

    p!("Seconds passed: ", timer).into()
}

async fn time(n: u64, mut timer: StateAtomic<usize>) {
    while Delay::new(core::time::Duration::from_secs(n))
        .await
        .is_ok() {
            timer += 1;
        }
}

#[valerie(start)]
pub fn run() {
    App::render_single(ui());
}
```

There are more examples in the examples directory.

## Missing features

 - Library support for CSS.
 - Using States for CSS.
 - Routing and multi-page support.
 - Global Variables for Multiple Pages.

## Issues and Contributing

Pick out some issues and start contributing. Our contribution guidelines are available [here](https://github.com/emmanuelantony2000/valerie/blob/master/.github/CONTRIBUTING.md). 
