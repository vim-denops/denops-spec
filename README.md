# Denops specification

[Denops][] is an ecosystem of Vim and Neovim to write plugins in [Deno][].

[Denops]: https://github.com/vim-denops/denops.vim
[Deno]: https://deno.land/

[PlantUML]: https://plantuml.com/

## Architecture

Denops runs two server processes named "Channel" and "Service".
The "Channel" server translate stdin/stdout to TCP connection.
The "Service" server translate Denops RPC to corresponding protocol of Vim/Neovim and it holds all plugins as worker threads.

![Architecture](https://user-images.githubusercontent.com/546312/115285670-19c71100-a189-11eb-8070-9d5849f7359d.png)

### FAQ

#### Why do we need "Channel" layer

Without "Channel" layer, we cannot use `console.xxx()`(e.g. `console.log()`) to print messages while those messages are written into stdout which is used to communicate with Vim/Neovim.
It is quite unusual behavior for general Vim plugin developers who don't really familiar with Vim/Neovim's RPC mechanisms.
That's why we decided to add the "Chanel" layer to allow developers to use `console.xxx()` for debug printings.

#### Why do we use worker threads to run plugins

The initial version of denops run each plugins as separated processed but

- Running plugins as processes cost too much for users who install tons of plugins
- It's much simple to manage only the service server process rather than manage each plugins individually

So at least for now, each plugins are executed on isolated worker threads.

