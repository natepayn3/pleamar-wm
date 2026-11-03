# Diagnostics: which message gets a program cut off

NOT for a pull request. wayland-rs cuts a client off without a protocol error when it cannot parse a request
(an object id it does not know, an unknown opcode) or a read/write fails; the client only sees
"Connection reset by peer". This patch to wayland-backend 0.3.17 prints, in the compositor's log:

    wayland · cut off a program: unreadable request on object N opcode M size S · object <interface>@N has K requests | unknown to us
    wayland · cut off a program: reading from it failed: <error>       (EPIPE lines are programs closing normally)
    wayland · cut off a program: writing <interface>.<event> to it failed: <error>

To use it:

    cp -r ~/.cargo/registry/src/index.crates.io-*/wayland-backend-0.3.17 ~/work/vendor/
    cd ~/work/vendor && patch -p1 < <this repo>/diagnostics/wayland-backend-0.3.17-cut-off-log.patch
    # then in Cargo.toml:
    [patch.crates-io]
    wayland-backend = { path = "/home/<you>/work/vendor/wayland-backend-0.3.17" }

Found while chasing the Vitreus bar being cut off every 1-2 minutes on pleamar 0.7.10 (see natepayn3/pleamar
branch zerocopy-0.7, handoff/HANDOFF-2026-11-02.md).
