# amux

An agent multiplexer. Fork of [tmux](https://github.com/tmux/tmux).
The mux is tmux's: daemon, session, window, pane, process, client.
The product is not tmux's status line. It is launch and awareness of
ACP agents.

## Goal

Keep tmux's multiplexer. Replace its chrome and application layer.

A shell sits on a PTY. An ACP agent sits on stdio. The daemon is
parent of both. Detach never kills. The status strip is windows as
tabs across the top, and agent state: idle, turning, needs you.

This tree is that fork. Upstream tmux is `~/src/ext/tmux` (git remote
`upstream`). Do not reinvent PTY, layout, or damage paint. Take over
chrome (`status.c`, `screen-redraw.c`, `options-table.c`) and add ACP
as a pane encoding.

## Principle

**Radical simplicity.** Every concept must earn its place.
**Zhengming (正名):** right names, common computer terms only.

Six kernel words: **daemon**, **session**, **window**, **pane**,
**process**, **client**. Chrome is the roster and the tiles. ACP is
how a process talks. If you need another kernel word, stop.

The binary is `amux`. It must not collide with `tmux` on the box:
socket `$TMPDIR/amux-<uid>/default`, config `~/.amux.conf`, env
`AMUX` / `AMUX_PANE`.

## Merits

1. **Mux is tmux.** Detach, PTY, tiles, prefix, paint into a
   rectangle. Do not composite an application's screen in our UI.
2. **Chrome is the product.** Tabs across the top are windows.
   Agent marks are awareness, not a second window manager.
3. **One parent.** ACP stdio and PTY children live in this process.
   Not a sidecar next to tmux.
4. **These words only.** A new noun is a last resort.
5. **Detach never kills.** Restore the tty from the view in the daemon.

## Concepts

```
daemon
 └── session          domain
       └── window     activity
             └── pane  ──views──▶  process
client  ──attaches──▶  session
```

A pane views a process. Two encodings of the same view: a character
grid (PTY) or an ACP stream (stdio). PTY is already here. ACP is the
graft.

## Build

```bash
sh autogen.sh
./configure --prefix="$HOME/.local"
make
make install   # ~/.local/bin/amux
```

Run: `amux`. Prefix is still `Ctrl-b`. Prefix `d` detaches.

Tests: `make check` when the regress suite is configured.

## Git

Work on `main`. Upstream tmux is `upstream` (`master`). Commit
frequently. Do not rewrite tmux internals to rename every `tmux_*`
symbol — the binary and the user-facing names are amux; the C
identifiers can stay.

ISC license (COPYING) stays. Nicholas Marriott and contributors keep
their copyright. Our commits are chrome, ACP, and this charter.

## Caretaker

A change earns its place by making the multiplexer more correct or
the roster more true. New words go in this file only after they have
earned it.

Last updated: 2026-08-28.
