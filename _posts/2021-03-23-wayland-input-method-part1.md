---
layout: post
title: "Wayland Input method - Part 1"
---

Usually my posts are in Vietnamese, but now I'm on wayland so there is no
Vietnamese input method yet, so this post will be in English.

In this series, I'll try to blog my process of writing an input method in
wayland for Sway desktop environment. I start this because I forgot mostly
everything the last time I learned (groped) it.

## Build

First of all, I need to compile wlroots with examples enabled.

```bash
$ meson build -Dexamples=true -Dc_args='-Og'
$ ninja -C build
```

`-Og` flag enables gdb debug (optimized).

## Debug

Right now wlroots has a [bug](https://github.com/swaywm/wlroots/issues/2790) in input method example that I logged.

With some help from maintainer, I got them full backtrace.

```bash
$ gdb build/examples/input-method

(gdb) run

...segmentation fault...

(gdb) bt full
```

Let's set a breakpoint and see what's going under the hood.

```bash
$ gdb build/examples/input-method
(gdb) b 197

(gdb) run

(gdb) p current
$1 = {
  change_cause = ZWP_TEXT_INPUT_V3_CHANGE_CAUSE_INPUT_METHOD, content_type = {
    hint = ZWP_TEXT_INPUT_V3_CONTENT_HINT_NONE,
    purpose = ZWP_TEXT_INPUT_V3_CONTENT_PURPOSE_TERMINAL}, surrounding = {text = 0x0, cursor = 0, anchor = 0}}
```

The bug's caused by compare null pointer `current.surrounding.text` to string (if I
understand it correctly).


## Fix

Today, I look at it's code and there's a TODO comment to init surrounding text.

So let's init current.surrounding.text.

### 1st attempt

I put `current.surrounding.text = ""` at line 181.

It does not work. The `text` variable is a pointer and maybe it's located a
memory address. I just only need to assign value to it. Maybe I'm wrong.

### 2nd attempt

Actually it's my first try.

I put `current.surrounding.text = strdup("")` at line 181.

And it's working!
