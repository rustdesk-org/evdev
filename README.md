`evdev`
=======

[![Travis](https://img.shields.io/travis/cmr/evdev.svg?style=flat-square)](https://travis-ci.org/cmr/evdev)
[![Crates.io](https://img.shields.io/crates/v/evdev.svg?style=flat-square)](https://crates.io/crates/evdev)

[Documentation](https://docs.rs/evdev)

Nice(r) access to `evdev` devices.

What is `evdev`?
===================

`evdev` is the Linux kernel's generic input interface, also implemented by other
kernels such as FreeBSD.

[libevdev](https://www.freedesktop.org/wiki/Software/libevdev/) is a userspace
library written in c for interacting with this system in a high level way rather
than using `ioctl` system calls directly.

This crate is a re-implementation of `libevdev` in rust. There is some trickery
involved, so please read the crate documentation.

There is also an alternative crate: [evdev-rs](https://crates.io/crates/evdev-rs)
which wraps `libevdev` instead.

Overview
========
This crate provides functionality for reading streams of events from input devices.

Like `libevdev`, this crate also provides functionality for interacting with
[uinput](https://www.kernel.org/doc/html/latest/input/uinput.html).
Uinput is a kernel module which allows virtual input devices to be created from userspace.


Synchronization
===============
This library exposes raw evdev events, but uses the Rust `Iterator` trait to
do so. When processing events via `fetch_events`, the library will handle
`SYN_DROPPED` events by injecting fake state updates in an attempt to ensure
callers see state transition messages consistent with actual device state. When
processing via `*_no_sync` this correction is not done, and `SYN_DROPPED` messages
will appear if the kernel ring buffer is overrun before messages are read. I try to
match [libevdev](https://www.freedesktop.org/software/libevdev/doc/latest/)
closely, where possible.


Limitations
===========
There is no abstraction for gamepad-like devices that allows mapping button
numbers to logical buttons, nor is one planned. Such a thing should take place
in a higher-level crate, likely supporting multiple platforms.


Example
=======

See <examples/evtest.rs> for an example of using this library (which roughly
corresponds to the userspace [evtest](https://cgit.freedesktop.org/evtest/)
tool.
