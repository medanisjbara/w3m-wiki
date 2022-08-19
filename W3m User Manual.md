# W3m User Manual

**This document is a work in progress.**

## Content
1. [Introduction](#introduction)
2. [Motives](#motives)
3. [Installation](#installation)
4. [Basic usage](#basic-usage)
5. [Tips and tricks](#tips-and-tricks)
6. [Troubleshooting](#troubleshooting)
7. [Useful Sources](#useful-sources)

## Introduction
This document is not the official user manual for w3m. Instead, it is the result of collected information over the internet and from the source code. This is the case for now, until the maintainers of w3m give this document a look.
## Motives
*This section will be removed since it's not relevant*  
On the [Old w3m README file](https://github.com/tats/w3m/blob/master/doc/README#L23) they pointed out in the *Current problems are* section that their online manuals are poor. Personally w3m saved me a couple of times when I had limited bandwidth or I didn't have enough money to by mobile data. And when I started reading the source code and looking online, there were a couple of things that I wished I knew when I needed them. That's why I'm creating this document.
## Installation
### Via the package manager
Since this fork isn't official, here we'll be listing the ways you can install the official [w3m](https://github.com/tats/w3m) repository.  
The official w3m is available for most distributions via the package manager. a couple of examples are listed below
#### Debian
```
sudo apt install w3m
```
#### Arch
```
sudo pacman -S w3m
```
### Compile from source
If you want to compile from source you should first install the [dependencies](https://github.com/medanisjbara/w3m-expantions#dependencies). along with `make`
```
git clone https://github.com/tats/w3m
cd w3m
./configure
sudo make
```


## Basic usage
*This section needs more information*  
w3m is a pager and an HTTP browser, meaning it can open files and URLs.
```
usage: w3m [options] [URL or filename]
```
to get general information on how to use w3m, read the man page `man w3m` or the output of `w3m --help`.
You can also have a look at the [old documentation](https://github.com/tats/w3m/blob/master/doc/MANUAL.html) for more information
### Bookmarks
You can add current page to bookmarks by pressing `ESC a`.
You can go to the bookmarks page ether by passing `-B` flag. Or by pressing `ESC b`.

## Tips and tricks
### W3m Images
If you see the word `image` in the output of `w3m -version`. This means that your version of w3m can infact display images. But not all terminals are able to display images properly. In order to get a gasp of what to do in this regard, continue reading this section and the [terminal emulators](#terminal-emulators) section.  
w3m has 5 methods of displaying images. Most of which do not work due to poor maintainance (even mlterm option doesn't work on `mlterm` terminal emulator for example, while the iTerm2 method is almost identical in most case to the default w3mimgdisplay (external)). But testing things out is always a good option, it might make images look better on your terminal (we haven't tested everything).  
You can choose which one to use in front of `Inline image display method` in the Opitons menue (press `o`). Don't forget to press the `[OK]` button or changes won't be saved. Restart w3m and check if the method of your choice is working. More on this in the next section.  
The default option is to use an external command, by default it is the `w3mimgdisplay` (located under `/usr/lib/w3m`)  
On some terminal emulators `w3mimgdisplay` displays the image for a split second before it vainshes. This is due to the fact that `w3mimgdisplay` draws the image on the front buffer. most terminals use double buffering, and having the back buffer overwrite the front buffer just deletes your image on frame after it has been drawn. Please check [w3mimgwrapper](#w3mimgwrapper) for more.  
The workarounds in this regard aren't really much. In st terminal, there's a [patch](https://st.suckless.org/patches/w3m/) that fixes this by copying the front buffer to the back buffer before drawing on it. Another good option developped by the writer of this manual is to use a wrapper around `w3mimgdisplay` to rewrite the image in a loop.  
### Terminal emulators
*This section needs more information*  
The best terminal emulator for using with w3m is Xterm. But other terminal emulators can be used. In fact, it is almost taken for granted that w3m will work on every terminal emulator out there. Images on the other hand aren't a garentee. So we created a database of terminals that give a good experience when used with w3m.  
OOTB: Out Of The Box
* TTY: OOTB
* Xterm: OOTB
* mlterm: OOTB (don't use mlterm display method, w3mimgdisplay just works).
* mrxvt: OOTB
* rxvt-unicode: OOTB (once page is loaded, you will need to move the cursor for images to show. But they do stay visible use w3amimgwrapper to fix that)
* st: ether by applying a [patch](https://st.suckless.org/patches/w3m/) or by using w3mimgwrapper script.
* Alacritty: needs w3mimgscript
* Kitty: works with kitty display method.
* qterminal: broken
### W3mimgwrapper
`w3mimgwrapper` is a script that works as a wrapper around `w3mimgdisplay` and executes it correctly in a loop. Effectively making the rendering process consistant on most terminals where the double buffers overwrite what `w3mimgdisplay` draws. Check [w3m images](#w3m-images) for more information.  
You can copy [the script](/scripts/w3mimgwrapper) to `/usr/lib/w3m/w3mimgwrapper` (or any name) and then make it executable. And then edit the option `External command to display image` from `w3mimgdisplay` to `w3mimgwrapper` in the Options menue. Or just replace `imgdisplay w3mimgdisplay` with `imgdisplay w3mimgwrapper` in your `~/w3m/config`. After that you can use `w3m` normally and see the results.  
If you still experience some flickering you can remove the `sleep 0.1` which will make it run much faster.  
**Known issues**  
* Server daemon doesn't stop automatically when w3m exists (user needs to `pkill w3mimgwrapper` manually).
### Open homepage when no arguments are supplied
In case of no arguments, w3m will look for the `HTTP_HOME` environment variable, If `HTTP_HOME` is not set, w3m will display the help message.
In some cases, It could be helpful to add this to your `~/.bashrc`
```
export HTTP_HOME=google.com
```
So that w3m will automatically launch to your search engine if no arguments were given.

### Combining with mpv and watching youtube.
This is one of the best tricks you can use with w3m it order to make the browsing experience richer.  
Start first by making a `keymap` file in `~/.w3m` if you haven't already. And write the following content to it.

```
keymap <key> EXTERN_LINK "(mpv --player-operation-mode=pseudo-gui --no-terminal %s &)"
```
Where *<key>* represents the key combination of your choice. You can add as many lines like these as you like.  
And combined with the `ytdl` implimentation of mpv, you can add special options to mpv to be able to customize the video player according to your needs.
#### Example:
```
# use "mn" key combination to play sound only
keymap mn EXTERN_LINK "(mpv --player-operation-mode=pseudo-gui --no-terminal --ytdl-raw-options='format=bestaudio' -ytdl %s &)"

# Use "ml" key combination to play with the most bandwidth economisation possible.
keymap ml EXTERN_LINK "(mpv --player-operation-mode=pseudo-gui --no-terminal --ytdl-raw-options='format=278+249' -ytdl %s &)"

...
```

You can add as many keymaps as you need, Not only for external video players like mpv. But also to other types of external interpretations of a link. See the [old manual](https://github.com/tats/w3m/blob/master/doc/MANUAL.html) for more details.

## Troubleshooting
### GC Memory allocation Warning
This error occurs mainly on termux on android but can occure on some other low end devices.
If you see an error that looks like this:
```
GC Warning: Repeated allocation of very large block (appr. size 110592):
	May lead to memory leak and poor performance
```
add this to your `~/.bashrc`
```
export GC_LARGE_ALLOC_WARN_INTERVAL=30000
```
## Useful sources
* [The old documentation](https://github.com/tats/w3m/blob/master/doc/MANUAL.html)
* [The old FAQ](https://github.com/tats/w3m/blob/master/doc/FAQ.html)
* [Subreddit](reddit.com/r/w3m)
* [W3M Rocks](http://w3m.rocks/) 
