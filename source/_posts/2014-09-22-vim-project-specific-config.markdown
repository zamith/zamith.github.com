---
layout: post
title: "Vim project specific configs"
date: 2014-09-22 11:17
comments: true
categories: [vim]
---

Adding specific configs for a specific project in vim is actually quite easy.
The first thing you need to realize is that vim doesn't really have the notion
of a project, so we'll rely on folder structures and paths.

## Setting up the environment

The first thing to do is to call a function to set up our environment whenever
we open vim, open a file on a new buffer or open a new file.

``` vim
autocmd! vimenter,BufReadPost,BufNewFile * call SetupEnv()
```

That function will then check the current path and conditionally load config
files.

``` vim
function! SetupEnv()
  let l:path = expand('%:p')
  if l:path =~ 'project-name'
    source ~/.vim/project-configs/project-name.vim
  endif
endfunction
```

The `project-name`s don't have to be the same, but I find it makes sense if they
do. And they usually refer to the root directory of the project.

## The specific configs

We can the add regular vim configs the `project-name.vim` config file. Something
that I find useful is to map shortcuts to commonly used directories (kind of a
poor man's Rails.vim `:R`).

``` vim
cnoremap %a <C-R>'app/resources/scripts/apps/
```

Now every time you write `%a` in the command line it will be replaced with
`app/resources/scripts/apps/`. So you can just `:e %a` and auto complete your
way into the file you want.

You can also add other settings such as number of spaces per tab, which may vary
from project to project. It's as easy as:

```
set tabstop=2
set shiftwidth=2
```
