---
tags: programming,edit
---


# My programming key bindings

I like the idea of editing of *VIM*, but I still think this is not the best.
Some keys of *vscode*, *emacs* and particularly *Kakoune* ans *helix* are modern.

## General idea
Action follows selection.

## Major modes
- [Normal](#normal-mode)
- [Insert](#insert-mode)
- [Picker](#picker-mode)
- [Prompt](#prompt-mode)
- [Popup](#popup-mode)

## Minor modes
- [View](#view-mode)
- [Select](#select-mode)
- [Goto](#goto-mode)
- [Match](#match-mode)
- [Window](#window-mode)
- [Space](#space-mode)
- [Unimpaired](#unimpaired)

## Keys
### Normal mode
#### Movement

| Key                | Desc.                                             |
|--------------------|---------------------------------------------------|
| `h`, `Left`        | Move left                                         |
| `j`, `Down`        | Move down                                         |
| `k`, `Up`          | Move up                                           |
| `l`, `Right`       | Move right                                        |
| `w`                | Move next word start                              |
| `b`                | Move previous word start                          |
| `e`                | Move next word end                                |
| `f`                | Find next char                                    |
| `t`                | Find 'till next char                              |
| `F`                | Find previous char                                |
| `T`                | Find 'till previous char                          |
| `Ctrl-b, PageUp`   | Move page up                                      |
| `Ctrl-f, PageDown` | Move page down                                    |
| `Ctrl-u`           | Move cursor and page half page up                 |
| `Ctrl-d`           | Move cursor and page half page down               |
|                    |                                                   |
| `0`                | Jump to the start of the line                     |
| `^`                | Move to the first non-blank character of the line |
| `$`                | Move cursor line end                              |
| `G`                | Go to the last line of the document               |
| `Ctrl-o`, `Ctrl--` | Go previous cursor location                       |
| `Ctrl-i`           | Go next cursor location                           |

#### Changes

| Key        | Desc.                                                 |
|------------|-------------------------------------------------------|
| `r`        | Replace with a character                              |
| `R`        | Replace with yanked text                              |
| `~`        | Switch case of the selected text                      |
| `i`        | Insert before selection                               |
| `a`        | Insert after selection (append)                       |
| `I`        | Insert at the start of the line                       |
| `A`        | Insert at the end of the line                         |
| `o`        | Open new line below selection                         |
| `O`        | Open new line above selection                         |
| `u`        | Undo change                                           |
| `U`        | Redo change                                           |
| `y`        | Yank selection                                        |
| `p`        | Paste after selection                                 |
| `P`        | Paste before selection                                |
| `"<reg>`   | Select a register to yank to or paste from            |
| `>`        | Indent selection                                      |
| `<`        | Unindent selection                                    |
| `=`        | Format selection (LSP)                                |
| `d`        | Delete selection                                      |
| `c`        | Change selection (delete and enter insert mode)       |
| `Q`        | Start/stop macro recording to the selected register   |
| `q`        | Play back a recorded macro from the selected register |
| `Alt-Up`   | Move line upward                                      |
| `Alt-Down` | Move line downward                                    |

#### Selection

| Key          | Desc.                                                         |
|--------------|---------------------------------------------------------------|
| `s`          | Select all regex matches inside selections                    |
| `C`          | Copy selection onto the next line (Add cursor below)          |
| `,`          | Keep only the primary selection                               |
| `%`, `Cmd-a` | Select entire file                                            |
| `x`          | Select current line, if already selected, extend to next line |

#### Searching

| Key | Desc.                                       |
|-----|---------------------------------------------|
| `/` | Search for regex pattern                    |
| `?` | Search for previous pattern                 |
| `n` | Select next search match                    |
| `N` | Select previous search match                |
| `*` | Use current selection as the search pattern |

#### File

| Key     | Desc.             |
|---------|-------------------|
| `Cmd-s` | Save current file |
| `Cmd-n` | Open new buffer   |


### Insert mode
Press `i` in normal mode

| Key                | Desc.                   |
|--------------------|-------------------------|
| `Escape`           | Switch to normal mode   |
| `Ctrl-a`           | Goto line begin         |
| `Ctrl-e`           | Goto line end           |
| `Ctrl-d`, `Delete` | Delete next char        |
| `Ctrl-j`, `Enter`  | Insert new line         |
| `Ctrl-x`           | Autocomplete            |
| `Alt-Delete`       | Delete word backward    |
| `Ctrl-u`           | Delete to start of line |
| `Ctrl-k`           | Delete to end of line   |

### Picker mode

| Key                         | Desc.             |
|-----------------------------|-------------------|
| `Shift-Tab`, `Up`, `Ctrl-p` | Previous entry    |
| `Tab`, `Down`, `Ctrl-n`     | Next entry        |
| `PageUp`, `Ctrl-u`          | Page up           |
| `PageDown`, `Ctrl-d`        | Page down         |
| `Home`                      | Go to first entry |
| `End`                       | Go to last entry  |
| `Enter`                     | Open selected     |
| `Escape`, `Ctrl-c`          | Close picker      |

### Prompt mode

| Key                                         | Desc.                       |
|---------------------------------------------|-----------------------------|
| `Escape`, `Ctrl-c`                          | Close prompt                |
| `Alt-b`, `Ctrl-Left`                        | Backward a word             |
| `Ctrl-f`, `Right`                           | Forward a char              |
| `Ctrl-b`, `Left`                            | Backward a char             |
| `Alt-f`, `Ctrl-Right`                       | Forward a word              |
| `Ctrl-e`, `End`                             | Move prompt end             |
| `Ctrl-a`, `Home`                            | Move prompt start           |
| `Ctrl-w`, `Alt-Backspace`, `Ctrl-Backspace` | Delete previous word        |
| `Alt-d`, `Alt-Delete`, `Ctrl-Delete`        | Delete next word            |
| `Ctrl-u`                                    | Delete to start of line     |
| `Ctrl-k`                                    | Delete to end of line       |
| `Backspace`, `Ctrl-h`, `Shift-Backspace`    | Delete previous char        |
| `Delete`, `Ctrl-d`                          | Delete next char            |
| `Ctrl-p`, `Up`                              | Select previous history     |
| `Ctrl-n`, `Down`                            | Select next history         |
| `Tab`                                       | Select next completion item |
| `Enter`                                     | Open selected               |

### Popup mode

| Key      | Desc.       |
|----------|-------------|
| `Ctrl-u` | Scroll up   |
| `Ctrl-d` | Scroll down |

### View mode
Press `z` in normal mode

| Key                  | Desc.                                      |
|----------------------|--------------------------------------------|
| `z`, `c`             | Vertically center the line                 |
| `t`                  | Align the line to the top of the screen    |
| `b`                  | Align the line to the bottom of the screen |
| `m`, `z`             | Align the line to the middle of the screen |
| `j`, `down`          | Scroll the view downwards                  |
| `k`, `up`            | Scroll the view upwards                    |
| `Ctrl-f`, `PageDown` | Move page down                             |
| `Ctrl-b`, `PageUp`   | Move page up                               |
| `Ctrl-u`             | Move cursor and page half page up          |
| `Ctrl-d`             | Move cursor and page half page down        |

### Select mode
Press `v` in normal mode

| Key | Desc.                  |
|-----|------------------------|
| `;` | Cancel selected region |

### Goto mode
Press `g` in normal mode

| Key | Desc.                                                                           |
|-----|---------------------------------------------------------------------------------|
| `g` | Go to line number <n> else start of file                                        |
| `e` | Go to the end of the file                                                       |
| `f` | Go to files in the selections                                                   |
| `h` | Go to the start of the line                                                     |
| `l` | Go to the end of the line                                                       |
| `s` | Go to first non-whitespace character of the line                                |
| `t` | Go to the top of the screen                                                     |
| `c` | Go to the middle of the screen                                                  |
| `b` | Go to the bottom of the screen                                                  |
| `d` | Go to definition (LSP)                                                          |
| `y` | Go to type definition (LSP)                                                     |
| `r` | Go to references (LSP)                                                          |
| `i` | Go to implementation (LSP)                                                      |
| `a` | Go to the last accessed/alternate file                                          |
| `m` | Go to the last modified/alternate file                                          |
| `n` | Go to next buffer                                                               |
| `p` | Go to previous buffer                                                           |
| `w` | Show labels at each word and select the word that belongs to the entered labels |

### Match mode
Press `m` in normal mode

| Key            | Desc.                                       |
|----------------|---------------------------------------------|
| `m`            | Goto matching bracket                       |
| `s <char>`     | Surround current selection with <char>      |
| `r <from><to>` | Replace surround character <from> with <to> |
| `d <char>`     | Delete surround character <char>            |
| `a <object>`   | Select around textobject                    |
| `i <object>`   | Select inside textobject                    |

### Window mode
Press `Ctrl-w` in normal mode

| Key                    | Desc.                                                |
|------------------------|------------------------------------------------------|
| `w`, `Ctrl-w`          | Switch to next window                                |
| `v`, `Ctrl-v`          | Vertical right split                                 |
| `s`, `Ctrl-s`          | Horizontal bottom split                              |
| `f`                    | Go to files in the selections in horizontal splits   |
| `F`                    | Go to files in the selections in vertical splits     |
| `h`, `Ctrl-h`, `Left`  | Move to left split                                   |
| `j`, `Ctrl-j`, `Down`  | Move to split below                                  |
| `k`, `Ctrl-k`, `Up`    | Move to split above                                  |
| `l`, `Ctrl-l`, `Right` | Move to right split                                  |
| `q`, `Ctrl-q`          | Close current window                                 |
| `o`, `Ctrl-o`          | Only keep the current window, closing all the others |
| `H`                    | Swap window to the left                              |
| `J`                    | Swap window downwards                                |
| `K`                    | Swap window upwards                                  |
| `L`                    | Swap window to the right                             |

### Space mode
Press `Space` in normal mode

| Key | Desc.                                                     |
|-----|-----------------------------------------------------------|
| `f` | Open file picker                                          |
| `F` | Open file picker at current working directory             |
| `b` | Open buffer picker                                        |
| `j` | Open jumplist picker                                      |
| `k` | Show documentation for item under cursor in a popup (LSP) |
| `s` | Open document symbol picker (LSP)                         |
| `S` | Open workspace symbol picker (LSP)                        |
| `d` | Open document diagnostics picker (LSP)                    |
| `D` | Open workspace diagnostics picker (LSP)                   |
| `r` | Rename symbol (LSP)                                       |
| `a` | Apply code action (LSP)                                   |
| `h` | Select symbol references (LSP)                            |
| `c` | Comment/uncomment selections                              |
| `/` | Global search in workspace folder                         |
| `?` | Open command palette                                      |

### Unimpaired

| Key  | Desc.                                    |
|------|------------------------------------------|
| `]d` | Go to next diagnostic (LSP)              |
| `[d` | Go to previous diagnostic (LSP)          |
| `]D` | Go to last diagnostic in document (LSP)  |
| `[D` | Go to first diagnostic in document (LSP) |
| `]f` | Go to next function (TS)                 |
| `[f` | Go to previous function (TS)             |
| `]t` | Go to next type definition (TS)          |
| `[t` | Go to previous type definition (TS)      |
| `]a` | Go to next argument/parameter (TS)       |
| `[a` | Go to previous argument/parameter (TS)   |
| `]c` | Go to next comment (TS)                  |
| `[c` | Go to previous comment (TS)              |
| `]T` | Go to next test (TS)                     |
| `[T` | Go to previous test (TS)                 |
| `]p` | Go to next paragraph                     |
| `[p` | Go to previous paragraph                 |


## Editors
I current use *vscode* for its light-weight and extensions.
I use the extension [Dance - Helix Alpha](https://marketplace.visualstudio.com/items?itemName=silverquark.dancehelix) and [EasyMotion](https://marketplace.visualstudio.com/items?itemName=shadowndacorner.vscode-easymotion) to simulate Helix's key bindings.

Also, I add some key bindings:

```json
// Place your key bindings in this file to override the defaultsauto[]
[
    {
        "key": "Escape",
        "command": "dance.modes.set.normal",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+w",
        "command": "vscode-easymotion.jumpToWord",
        "when": "editorTextFocus && dance.mode == 'insert'"
    },
    {
        "key": "=",
        "command": "editor.action.formatDocument",
        "when": "editorTextFocus && dance.mode == 'normal'"    
    },
    {
        "key": "Ctrl+[",
        "command": "workbench.action.navigateBack",
        "when": "editorTextFocus && dance.mode == 'normal'"
    },
    {
        "key": "Ctrl+]",
        "command": "workbench.action.navigateForward",
        "when": "editorTextFocus && dance.mode == 'normal'"
    }
]
```

When using Vscode Vim plugin, I include the following settings:
```json
{
    "vim.easymotion": true,
    "vim.incsearch": true,
    "vim.useSystemClipboard": true,
    "vim.useCtrlKeys": true,
    "vim.hlsearch": true,
    "vim.insertModeKeyBindings": [
        {
            "before": ["j", "j"],
            "after": ["<Esc>"]
        },
        {
            "before": ["<C-j>"],
            "commands": [
                "cursorDown"
            ]
        },
        {
            "before": ["C-k"],
            "commands": ["cursorUp"]
        },
        {
            "before": ["C-h"],
            "commands": ["cursorLeft"]
        },
        {
            "before": ["<C-l>"],
            "commands": ["cursorRight"]
        }
    ],
    "vim.normalModeKeyBindingsNonRecursive": [
        {
            "before": ["<leader>", "d"],
            "after": ["d", "d"]
        },
        {
            "before": ["<C-n>"],
            "commands": [":nohl"]
        },
        {
            "before": ["K"],
            "commands": ["lineBreakInsert"],
            "silent": true
        }
    ],
    "vim.leader": "<space>",
    "vim.handleKeys": {
        "<C-a>": false,
        "<C-e>": false,
        "<C-f>": false
    },
    
    "vim.smartRelativeLine": true,
    "vim.cursorStylePerMode.insert": "line-thin",
    "vim.cursorStylePerMode.normal": "block",
    "vim.normalModeKeyBindings": [
        {
            "before" : ["<leader>","w"],
            "commands" : [
                "workbench.action.switchWindow",
            ]
        },
        {
            "before" : ["<leader>","s"],
            "commands" : [
                "workbench.action.showAllSymbols",
            ]
        },
        {
            "before" : ["<leader>","f"],
            "commands" : [
                "workbench.action.quickOpen",
            ]
        },
        {
            "before" : ["<leader>","r"],
            "commands" : [
                "workbench.action.openRecent",
            ]
        },
        {
            "before" : ["<leader>","a"],
            "commands" : [
                "editor.action.quickFix",
            ]
        },
        {
            "before": ["<leader>", "p"],
            "commands": [
                "workbench.action.showCommands"
            ]
        },
        {
            "before": ["g", "r"],
            "commands": [
                "editor.action.goToReferences"
            ]
        },
        {
            "before": ["g", "i"],
            "commands": [
                "editor.action.goToImplementation"
            ]
        }
    ],
}
```