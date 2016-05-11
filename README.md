# ariadne

Ariadne enables comprehensive zsh cli history logging combined with interactive searching
using a modified version of Masafumi Oyamada's percol (https://github.com/mooz/percol). 
The cli logging is modified from the the following:

- http://stackoverflow.com/questions/945288/saving-current-directory-to-bash-history
- https://gist.github.com/jeetsukumaran/2202879)

I made this because I wanted to be able to search for previous commands based on path as
well as the command itself. I tend to use deeply nested and descriptive directory names
and want to quickly jump to them based on the command. This also makes it easy to copy
and paste commands in a new project directory based on a similar older project. This is 
hopefully handy for those running scientific software in the unix world (and who derive 
little joy from memorizing the relevant incantations and arcana)

- [What's this](#whats-this)
- [Installation](#installation)
  - [Zsh](#zsh)
  - [Bash](#bash)
- [Usage](#usage)
- [Configuration](#configuration)

## What's this

ariadne allows:

1. Reverse searching through previous commands and the paths where they were run
2. Extracting either the path or command
3. Quick filtering with current path
4. Filtering of duplicate commands
5. Toggling of date, command, path fields in search output

![animation](https://github.com/gawells/demos/blob/master/ariadne1.gif)


## Installation

### Zsh

First, clone ariadne (I keep it `~/.oh-my-zsh/custom`)

    $ mkdir -p ~/.oh-my-zsh/custom
    $ cd ~/.oh-my-zsh/custom
    $ git clone https://github.com/gawells/ariadne

Modify `~/.zshrc` to inject ariadne in `precmd`
    
    source ~/.oh-my-zsh/custom/ariadne/ariadne.zsh
    precmd() {
        _ariadne -h -t -u 
    }

### Bash

    $ mkdir -p ~/.config/bash
    $ cd ~/.config/bash
    $ git clone https://github.com/gawells/ariadne

Add the following to `~/.bashrc`:

    source $HOME/.config/bash/ariadne/ariadne.sh
    export PROMPT_COMMAND='_ariadne -h -t -u '

### Fish
    
Clone into `~/.config/fish/functions`
    
    $ cd ~/.config/fish/functions
    $ git clone https://github.com/gawells/ariadne
    $ cat ariadne/config.fish >> ../config.fish
    $ cp ariadne/fish_user_key_bindings.fish .

## Usage

- Ctrl+R          : Invoke command history search

In ariadne:

- F1,F2,F3        : Hide/show date, execution path and command, respectively
- Enter,Ctrl+R    : Extract command(s)
- Ctrl+R          : Extract path(s)
- Ctrl+L          : Filter by current directory
- Alt+R           : Filter out duplicate commands
- Ctrl+SPC        : Select entry (useful for extracting salient commands for future recipes?)
- Ctrl+S          : Push command to stack
- Alt+S           : Pop command from stack
- Ctrl+T          : Save stack (as 'rerun'sh') and exit

## Configuration

Configuration is specified in `rc.py`, currently under `$HOME/.oh-my-zsh/custom/ariadne/`

Default config:

```python
percol.view.CANDIDATES_LINE_BASIC    = ("on_default", "default")
percol.view.CANDIDATES_LINE_SELECTED = ("underline", "on_blue", "white","bold")
percol.view.CANDIDATES_LINE_MARKED   = ("bold", "on_cyan", "black")
percol.view.CANDIDATES_LINE_QUERY    = ("green", "bold")
percol.view.FIELD_SEP = ' <> '
percol.view.FOLDED = '…'

percol.command.set_field_sep(percol.view.FIELD_SEP)

percol.import_keymap({
    "C-i"         : lambda percol: percol.switch_model(),
    # text
    "C-h"         : lambda percol: percol.command.delete_backward_char(),
    "<backspace>" : lambda percol: percol.command.delete_backward_char(),
    "C-w"         : lambda percol: percol.command.delete_backward_word(),
    "C-u"         : lambda percol: percol.command.clear_query(),
    "<dc>"        : lambda percol: percol.command.delete_forward_char(),
    # caret
    "<left>"      : lambda percol: percol.command.backward_char(),
    "<right>"     : lambda percol: percol.command.forward_char(),
    # line
    "<down>"      : lambda percol: percol.command.select_next(),
    "<up>"        : lambda percol: percol.command.select_previous(),
    # page
    "<npage>"     : lambda percol: percol.command.select_next_page(),
    "<ppage>"     : lambda percol: percol.command.select_previous_page(),
    # top / bottom
    "<home>"      : lambda percol: percol.command.select_top(),
    "<end>"       : lambda percol: percol.command.select_bottom(),
    # mark
    "C-SPC"       : lambda percol: percol.command.toggle_mark_and_next(),
    # finish
    "RET"         : lambda percol: percol.finish(), # Is RET never sent? #seems not, doesn't respond to finish_f either - gaw
    "C-m"         : lambda percol: percol.finish(),
    "C-j"         : lambda percol: percol.finish(),
    "C-c"         : lambda percol: percol.cancel(),

    "<f1>" : lambda percol: percol.command.toggle_date(),
    "<f2>" : lambda percol: percol.command.toggle_execdir(),
    "<f3>" : lambda percol: percol.command.toggle_command(),
    "C-l" : lambda percol: percol.command.cwd_filter(),
    "C-d" : lambda percol: percol.finish(field=1),
    "C-r" : lambda percol: percol.finish(field=2),
    "M-r" : lambda percol: percol.command.toggle_recent(),
    "C-s" : lambda percol: percol.command.fill_stack(),
    "M-s" : lambda percol: percol.command.pop_stack(),
    "C-t" : lambda percol: percol.finish_and_save(),
})    
```

