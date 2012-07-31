---
comments: true
date: 2011-01-31 08:58:05
layout: post
slug: code-completion-for-python-and-django-in-vim
title: Code Completion (IntelliSense) in VIM
wordpress_id: 21
categories:
- django
- python
- Technology
- vim
tags:
- django
- python
- vim
---

[VIM](http://www.vim.org/) has been my editor of choice for at least 15 years. I love how fast I can edit files, perform menial tasks, and wreak general havoc on any code project I am working on at any given moment. One of the things that I have missed about VIM from an IDE perspective has been code completion (a.k.a. "IntelliSense"). I have spent a lot of time on websites and man pages trying to figure out syntax and function names for several types of languages, and just recently discovered a long-included feature of VIM called omni completion, or Omnicomplete.

Since my life is mostly centered around [django](http://www.djangoproject.com) these days, I will discuss how I've benefited from omnicomplete and how I've set it up in my own environment.

First, since django is a web development framework, I want to make sure that I can get omnicompletion for HTML, Python, JavaScript and CSS. Omnicompletion works for almost any programming language that VIM has syntax highlighting support for, and these languages are no exception.



Here's what omnicomplete looks like for CSS files, for example:

{% img http://www.belchak.com/wp-content/uploads/2011/01/Screen-shot-2011-01-30-at-3.37.00-PM-1-30-11-300x213.png 300 "omnicomplete for vim" %}

Setting this up for your django project is simple as pie. It is helpful to have all your django projects in one parent directory for the following setup. You can obviously customize this to your needs, but this is the way I've set it up in my environment.

Add the following to a script in a directory of your choosing (~/bin/vim_wrapper is where mine is):

``` bash
#!/bin/bash
export PROJECT=`python -c "import os; print os.path.basename(os.getcwd())"`
export PYTHONPATH="/path/to/your/projects/parent/directory/"
export DJANGO_SETTINGS_MODULE=$PROJECT.settings vim
$@
```


Then add the following line to your ~/.bash_profile or equivalent:


```
alias vi="vim_wrapper"
```


Or, you can call your vim_wrapper script by hand. (`vim_wrapper file_to_edit.py`)

Next, add the following lines to your ~/.vimrc file:


```
filetype plugin on
autocmd FileType python set omnifunc=pythoncomplete#Complete
autocmd FileType javascript set omnifunc=javascriptcomplete#CompleteJS
autocmd FileType html set omnifunc=htmlcomplete#CompleteTags
autocmd FileType css set omnifunc=csscomplete#CompleteCSS
```


I also prefer to re-map the default key binding (<C-x><C-o>) to <C-space>, so I accomplish this by also adding the following line to my ~/.vimrc file:

```
inoremap <C-space> <C-x><C-o>
```


I also found [this trick](http://blogs.gnome.org/lharris/2008/07/20/code-completion-with-vim-7/) today while searching around...


> What this function does is that if there is no completion that could happen it will insert a tab.  Otherwise it checks to see if there is an omnifunction available and, if so, uses it. Otherwise it falls back to Dictionary completion if there is a dictionary defined.  Finally it resorts to simple known word completion.  In general, hitting the Tab key will just do the right thing for you in any given situation.


Add the following to your ~/.vimrc and you should be good to go. It works like a charm for me.

```
function! SuperCleverTab()
    if strpart(getline('.'), 0, col('.') - 1) =~ '^\s*$'
        return "\"
    else
        if &omnifunc != ''
            return "\\"
        elseif &dictionary != ''
            return "\"
        else
            return "\"
        endif
    endif
endfunction

inoremap <Tab> <C-R>=SuperCleverTab()<cr>
```


If you find yourself writing code in other languages, the following lines in your vimrc should be adequate:

```
filetype plugin on
set ofu=syntaxcomplete#Complete
```


You can now test that your installation works by changing directories to one of your django projects, firing up vim and running the following command:

```
:python from django import db
```


If you do not get a horrible error, you are good to go!

You can now access code completion by the following methods:




  * `<C-p>` - Shows a list of all local symbols. This is good if you do not have a tags file associated with the file you are editing.


  * `<C-space>` - Shows a list of all available symbols. You need to set up a tags file, which is outside the scope of this blog post


  * `<C-x><C-o>` - The original keystroke sequence that we re-mapped C-space to.


  * `<Tab>` - The all-powerful tab!`


I hope you enjoy your new-found power with vim as much as I do!
