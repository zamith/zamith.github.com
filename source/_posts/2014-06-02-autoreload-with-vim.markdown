---
layout: post
title: "Autoreload with Vim"
date: 2014-06-02 23:08
comments: true
categories: [vim, rails]
---

I made it so that any time I save an html file, chrome will reload. I took the original idea from [Joe
Ferris](https://github.com/jferris/dotfiles/blob/master/vim/plugin/chrome.vim)
and then just ran with it.

## Reloading Chrome

The first step is to be able to reload chrome from the command line, and you can
get whichever script you want that achieves this goal.

I wrote one in bash and apple script.

``` bash
osascript -e 'tell application "Google Chrome" to tell the active tab of its first window to reload'
```

## Calling it from Vim

The next step is being able to call it from inside of vim, which can be done by
writing a Vim Script function.

``` vim
function! ReloadChrome()
  wall
  silent :!reload-chrome
  redraw!
endfunction
```

The call to `redraw` is not needed if you use gvim, but in terminal vim your
screen will be all messed up unless you do it.

As a bonus you can also define a mapping to call this function easier.

``` vim
nmap <Leader>rl :call ReloadChrome()<CR>
```

This is as far as Joe went, but I wanted to take it further.

## FTPlugin

Vim has an awesome feature called are ftplugins or file type plugins, which are
nothing but config files that are only loaded once you enter a buffer with a
file of a specific type (type `:h ftplugins` in vim for more info).

I created one for html files, simply by creating a `~/.vim/ftplugin/html.vim`
file.

``` vim
if exists("b:did_html_ftplugin")
  finish
endif
let b:did_html_ftplugin = 1

cabbrev <buffer> w <c-r>=(getcmdtype()==':' && getcmdpos()==1 ? 'call ReloadChrome()' : 'w')<CR>
```

This is just checking if the plugin is already loaded, and if it is not it
defines a command mode abbreviation. In this case I'm redefining `:w` to `:call
ReloadChrome()`.

The `getcmdtype()==':' && getcmdpos()==1` bit makes sure we don't
substitute any `w` that we type in a command, just if it the first letter.

A very important part of this code is the `<buffer>`, which ensures the
abbreviation is only valid for the current buffer.

Now, every time we save an html file, the current tab on chrome will reload.

## The extra mile

Since I spend a lot of time developing Rails apps, I thought I could go
a step further and do something more clever than reloading the focused chrome
tab.

For that I got a script that reloads a specific URL if there is an open tab with it,
or opens a new tab.

``` bash
osascript <<CODE
tell application "Google Chrome"
  activate
  set theUrl to "$1"

  if (count every window) = 0 then
    make new window
  end if

  set found to false
  set theTabIndex to -1
  repeat with theWindow in every window
    set theTabIndex to 0
    repeat with theTab in every tab of theWindow
      set theTabIndex to theTabIndex + 1
      if theTab's URL = theUrl then
        set found to true
        exit
      end if
    end repeat

    if found then
      exit repeat
    end if
  end repeat

  if found then
    tell theTab to reload
    set theWindow's active tab index to theTabIndex
    set index of theWindow to 1
  else
    tell window 1 to make new tab with properties {URL:theUrl}
  end if
end tell
CODE
```

Notice that `theUrl` is being set to the first variable of the script, so that
it can be called from vim like this:

```vim
function! ReloadChromeRails()
  wall
  silent :!reload-chrome-url http://localhost:3000/
  redraw!
endfunction
```

And given that I use Slim as an html preprocessor, I can create an `slim.vim`
ftplugin for this.

``` vim
if exists("b:did_slim_ftplugin")
  finish
endif
let b:did_slim_ftplugin = 1

cabbrev <buffer> w <c-r>=(getcmdtype()==':' && getcmdpos()==1 ? 'call ReloadChromeRails()' : 'w')<CR>
```

And that's it, everytime I save a view file, chrome reloads. All of this using
(mostly) vim. Neat.

## Pitfalls

For some reason my vim version was running html ftplugins for markdown files.
You can check this by running `:scriptnames` which gives you a list of all the
files that were loaded, and if there is something like `vim/ftplugin/html.vim`
you have the same problem I had.

Look for a `vim/7.4.253/share/vim/vim74/ftplugin/markdown.vim` (or similar),
open it and remove this line `runtime! ftplugin/html.vim
ftplugin/html_*.vim ftplugin/html/*.vim`.
