---
title: "Mac setup"
date: 2024-04-27T20:25:20+02:00
draft: false
---

Coming from the Linux world of personal computers, and having used [tiling windows managers](https://en.wikipedia.org/wiki/Tiling_window_manager) for a while now, I always struggle with using Mac OS for work. I don't like it a single bit. 

I hate inability of solving problems, the lack of customization and control you have over the OS. But if there's one thing that I hate the most is the fact that you as an user must forfeit to Apples concept of what a good UX is and not the other way around. Having the change to bring the UX closer to the individual, while maintaining a usable software for the herd, is something they just don't care and I don't blame them. Nevertheless, there are some things I have done to try to fix this

## Cycling workspaces (or Desktops)

## Text keybinds

Skipping through words is something i do a lot both for **moving the cursor** around or **selecting chunks of sentences**. I use `Ctrl + Left/Right` but on OS X it's default is `Option + Left/Right`. Other things such as selecting all text `Ctrl + A`. For these things I have found that they are mapped to a set of escape characters. Luckily I have a [qmk](https://en.wikipedia.org/wiki/QMK) compatible [keyboard](https://github.com/foostan/crkbd) that lets me set up these escape characters for a key combination, resulting in a seamless experiences regardless of the OS you are using, since the escape characters are always the same what changes is the key combination that sends them.

## The Command Control inferno

The thing that affects the most here is the **Copy/Paste/Cut** that we are used to do with `Ctrl + C`,  `Ctrl + V` and  `Ctrl + X`. In Mac one would have to switch the `Ctrl` key for the `Cmd` key. For this, I have found that OS X let's you override, what they've called, "*keyboard shortcuts*". This setting is useful to override more than one keyboard shortcut that is done with the **Command** key rather than the **Control** key.

Here's a list of the keyboard shortcuts I have set up.

| Menu Title | Combination | App | Description |
|------------|-------------|-----|-------------|
| Paste      | Ctrl+V      | *   |             |
| New Tab    | Ctrl+T      | Chrome | Opens a new Tab |



## Brew
