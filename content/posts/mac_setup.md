---
title: "Mac setup (Part 0)"
date: 2024-04-27T20:25:20+02:00
draft: false
---

I've dodged using the Mac for nearly eight years, but now I have to give in.

Coming from the Linux world of personal computers and having used [tiling windows managers](https://en.wikipedia.org/wiki/Tiling_window_manager) for a while now, I always struggle with using Mac OS at work. I don't like it a single bit. I hate inability of solving problems, the lack of customization and control you have over the OS. But if there's one thing that I hate the most is the fact that you as an user must forfeit to Apples concept of what a good UX is and not the other way around. Having the change to bring the UX closer to the individual, while maintaining a usable software for the herd, is something they just don't care and I don't blame them. Nevertheless, there are some things I have done to try to make my experience better.

## The windows

Even if not really tiling, windows can be arranged side by side using only your keyboard using a third party app. There are a bunch of applications. [Yabai](https://github.com/koekeishiya/yabai), [Raycast](https://www.raycast.com/), [Rectangle](https://rectangleapp.com/), etc. I'm currently using Rectangle since it only does windows arrangements using native floating windows. You can also set up key combinations to achieve this.

Moving through desktops can be both configured in under the Mission Control settings `Settings > Keyboard > Keybaord Shortcuts > Missions Control` but it's limited to 4 Desktops. Cycling through Desktops (workspaces) keybind can also be changed from here.

Changing focus is somethings that I have resign to do with the keyboard. I have to pick the mouse, and **CLICK** the window since putting the cursor over it won't focus it (something that I have also resign to set up). What sometimes do, is to switch focus within the active and last active windows, a shortcut for this can be set up on `Settings > Keyboard > Keybaord Shortcuts > Keyboard`.

## The Cmd/Ctrl inferno

The thing that affects the most here is the **Copy/Paste/Cut** that we are used to do with `Ctrl + C`,  `Ctrl + V` and  `Ctrl + X`. In Mac one would have to switch the `Ctrl` key for the `Cmd` key. For this, I have found that OS X let's you override, what they've called, "*keyboard shortcuts*". This setting is useful to override more than one keyboard shortcut that is done with the **Command** key rather than the **Control** key.

To access, navigate to `Settings > Keyboard > Keybaord Shortcuts > App Shortcuts`

![ShortcutsSettings](/images/posts/mac_setup0.jpg)

Shortcuts can be override for All Applications or for custom applications.


The "Menu Title" refers to Mac's menu name display at the top bar for each windows.

Here, "Paste" would be the name of our Menu Title and the `^V` means that I have remapped this to `Ctrl + V`.

| | |
| ------------- | -------------- |
| ![MenuTitle](/images/posts/mac_setup1.jpg) | ![MenuTitle2](/images/posts/mac_setup2.jpg)| Item1 |

Bare in mind that these Menu Titles might change with updates from each application and you should always.

Here's a list of some keyboard shortcuts I have set up:

| Menu Title &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;| Combination &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; | Application &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; | Description |
|------------|-------------|-----|-------------|
| Paste      | Ctrl+V      | All Applicationws   | Paste |
| Cut      | Ctrl+X      | All Applications   | Cut |
| Copy      | Ctrl+C      | All Applications   | Copy |
| New Tab    | Ctrl+T      | Chrome | Opens a new Tab |
| Close Tab    | Ctrl+W      | Chrome | Closes a new Tab |
| Reload This Page    | Ctrl+W      | Chrome | Reloads Page|
| Find...    | Ctrl+F      | Chrome | Reloads Page|
| Paste    | Ctrl+Shift+V      | iTerm | Paste on terminal|
| Open Location...    | Ctrl+L      | Chrome | Cursor to the URL bar|
| ... | ... | ... | ... |

Additionally, changing things like *Taking screenshots* and Spotlight can be achieve in the same settings: `Settings > Keyboard > Keybaord Shortcuts > ...`

## Brew

There are some [discussions](https://news.ycombinator.com/item?id=26036834) on weather Brew is good or bad. In my case, I had better experiences with other package managers but I never really get to learn it properly. But for many things [Mac Ports](https://ports.macports.org/) has given me less issues.

## Navigating through text (soon..)

This is something I haven't solve quite yet but as soon as I do I will update the post, but I have an idea on how to do it

Skipping through words is something i do a lot both for **moving the cursor** around or **selecting chunks of sentences**. I use `Ctrl + Left/Right` but on OS X it's default is `Option + Left/Right`. Other things such as selecting all text `Ctrl + A`. For these things I have found that they are mapped to a set of escape characters. Luckily I have a [qmk](https://en.wikipedia.org/wiki/QMK) compatible [keyboard](https://github.com/foostan/crkbd) that lets me send escape characters for a determine key combination, I'm trying to achieve a seamless experiences regardless of the OS you are using, but I'm not there yet.
<!-- Skipping through words is something i do a lot both for **moving the cursor** around or **selecting chunks of sentences**. I use `Ctrl + Left/Right` but on OS X it's default is `Option + Left/Right`. Other things such as selecting all text `Ctrl + A`. For these things I have found that they are mapped to a set of escape characters. Luckily I have a [qmk](https://en.wikipedia.org/wiki/QMK) compatible [keyboard](https://github.com/foostan/crkbd) that lets me set up these escape characters for a key combination, resulting in a seamless experiences regardless of the OS you are using, since the escape characters are always the same what changes is the key combination that sends them. -->

## Terminal and Neovim

Soon..
