<img src="https://github.com/echasnovski/media/blob/main/mini.nvim/logo/logo_completion.png" style="width: 100%"/>

<!-- badges: start -->
[![GitHub license](https://badgen.net/github/license/echasnovski/mini.nvim)](https://github.com/echasnovski/mini.nvim/blob/main/LICENSE)
<!-- badges: end -->

### Autocompletion and signature help plugin

- Async (with customizable 'debounce' delay) 'two-stage chain completion': first builtin LSP, then configurable fallback.
- Has functionality for completion item info and function signature (both in floating window appearing after customizable delay).

See more details in [Features](#features) and [help file](doc/mini-completion.txt).

---

⦿ This is a part of [mini.nvim](https://github.com/echasnovski/mini.nvim) library. Please use [this link](https://github.com/echasnovski/mini.nvim/blob/main/README.md) if you want to mention this module.

⦿ All contributions (issues, pull requests, discussions, etc.) are done inside of 'mini.nvim'.

⦿ See the repository page to learn about common design principles and configuration recipes.

---

If you want to help this project grow but don't know where to start, check out [contributing guides of 'mini.nvim'](https://github.com/echasnovski/mini.nvim/blob/main/CONTRIBUTING.md) or leave a Github star for 'mini.nvim' project and/or any its standalone Git repositories.

## Demo

https://user-images.githubusercontent.com/24854248/173044355-90bfc230-70c4-4932-b66b-103284558994.mp4

## Features

- Two-stage chain completion:
    - First stage is an LSP completion. Supports `additionalTextEdits`, like auto-import and others.
    - If first stage is not set up or resulted into no candidates, fallback action is executed. The most tested actions are Neovim's built-in insert completion.
- Automatic display in floating window of completion item info (via 'completionItem/resolve' request) and signature help (with highlighting of active parameter if LSP server provides such information).
- Automatic actions are done after some configurable amount of delay. This reduces computational load and allows fast typing (completion and signature help) and item selection (item info)
- User can force two-stage completion via or fallback completion.
- Highlighting of LSP kind (like "Function", "Keyword", etc.). Requires enabled ['mini.icons'](https://github.com/echasnovski/mini.nvim/blob/main/readmes/mini-icons.md) (uses its "lsp" category) and Neovim>=0.11.

## Dependencies

For full experience needs (still works without any of suggestions):

- Enabled ['mini.icons'](https://github.com/echasnovski/mini.nvim/blob/main/readmes/mini-icons.md) module to highlight LSP kind (requires Neovim>=0.11). Otherwise `MiniCompletion.default_process_items()` does not add highlighting. Also take a look at `MiniIcons.tweak_lsp_kind()`.

## Installation

This plugin can be installed as part of 'mini.nvim' library (**recommended**) or as a standalone Git repository.

There are two branches to install from:

- `main` (default, **recommended**) will have latest development version of plugin. All changes since last stable release should be perceived as being in beta testing phase (meaning they already passed alpha-testing and are moderately settled).
- `stable` will be updated only upon releases with code tested during public beta-testing phase in `main` branch.

Here are code snippets for some common installation methods (use only one):

<details>
<summary>With <a href="https://github.com/echasnovski/mini.nvim/blob/main/readmes/mini-deps.md">mini.deps</a></summary>
<table>
    <thead>
        <tr>
            <th>Github repo</th>
            <th>Branch</th> <th>Code snippet</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=2>'mini.nvim' library</td> <td>Main</td> <td rowspan=2><i>Follow recommended 'mini.deps' installation</i></td>
        </tr>
        <tr>
            <td>Stable</td>
        </tr>
        <tr>
            <td rowspan=2>Standalone plugin</td> <td>Main</td> <td><code>add('echasnovski/mini.completion')</code></td>
        </tr>
        <tr>
            <td>Stable</td> <td><code>add({ source = 'echasnovski/mini.completion', checkout = 'stable' })</code></td>
        </tr>
    </tbody>
</table>
</details>

<details>
<summary>With <a href="https://github.com/folke/lazy.nvim">folke/lazy.nvim</a></summary>
<table>
    <thead>
        <tr>
            <th>Github repo</th>
            <th>Branch</th> <th>Code snippet</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=2>'mini.nvim' library</td>
            <td>Main</td> <td><code>{ 'echasnovski/mini.nvim', version = false },</code></td>
        </tr>
        <tr>
            <td>Stable</td> <td><code>{ 'echasnovski/mini.nvim', version = '*' },</code></td>
        </tr>
        <tr>
            <td rowspan=2>Standalone plugin</td>
            <td>Main</td> <td><code>{ 'echasnovski/mini.completion', version = false },</code></td>
        </tr>
        <tr>
            <td>Stable</td> <td><code>{ 'echasnovski/mini.completion', version = '*' },</code></td>
        </tr>
    </tbody>
</table>
</details>

<details>
<summary>With <a href="https://github.com/junegunn/vim-plug">junegunn/vim-plug</a></summary>
<table>
    <thead>
        <tr>
            <th>Github repo</th>
            <th>Branch</th> <th>Code snippet</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=2>'mini.nvim' library</td>
            <td>Main</td> <td><code>Plug 'echasnovski/mini.nvim'</code></td>
        </tr>
        <tr>
            <td>Stable</td> <td><code>Plug 'echasnovski/mini.nvim', { 'branch': 'stable' }</code></td>
        </tr>
        <tr>
            <td rowspan=2>Standalone plugin</td> <td>Main</td> <td><code>Plug 'echasnovski/mini.completion'</code></td>
        </tr>
        <tr>
            <td>Stable</td> <td><code>Plug 'echasnovski/mini.completion', { 'branch': 'stable' }</code></td>
        </tr>
    </tbody>
</table>
</details>

<br>

**Important**: don't forget to call `require('mini.completion').setup()` to enable its functionality.

**Note**: if you are on Windows, there might be problems with too long file paths (like `error: unable to create file <some file name>: Filename too long`). Try doing one of the following:
- Enable corresponding git global config value: `git config --system core.longpaths true`. Then try to reinstall.
- Install plugin in other place with shorter path.

## Default config

```lua
-- No need to copy this inside `setup()`. Will be used automatically.
{
  -- Delay (debounce type, in ms) between certain Neovim event and action.
  -- This can be used to (virtually) disable certain automatic actions by
  -- setting very high delay time (like 10^7).
  delay = { completion = 100, info = 100, signature = 50 },

  -- Configuration for action windows:
  -- - `height` and `width` are maximum dimensions.
  -- - `border` defines border (as in `nvim_open_win()`).
  window = {
    info = { height = 25, width = 80, border = 'single' },
    signature = { height = 25, width = 80, border = 'single' },
  },

  -- Way of how module does LSP completion
  lsp_completion = {
    -- `source_func` should be one of 'completefunc' or 'omnifunc'.
    source_func = 'completefunc',

    -- `auto_setup` should be boolean indicating if LSP completion is set up
    -- on every `BufEnter` event.
    auto_setup = true,

    -- A function which takes LSP 'textDocument/completion' response items
    -- and word to complete. Output should be a table of the same nature as
    -- input items. Common use case is custom filter/sort.
    process_items = --<function: MiniCompletion.default_process_items>,
  },

  -- Fallback action. It will always be run in Insert mode. To use Neovim's
  -- built-in completion (see `:h ins-completion`), supply its mapping as
  -- string. Example: to use 'whole lines' completion, supply '<C-x><C-l>'.
  fallback_action = --<function: like `<C-n>` completion>,

  -- Module mappings. Use `''` (empty string) to disable one. Some of them
  -- might conflict with system mappings.
  mappings = {
    force_twostep = '<C-Space>', -- Force two-step completion
    force_fallback = '<A-Space>', -- Force fallback completion
  },

  -- Whether to set Vim's settings for better experience (modifies
  -- `shortmess` and `completeopt`)
  set_vim_settings = true,
}
```

## Similar plugins

- [hrsh7th/nvim-cmp](https://github.com/hrsh7th/nvim-cmp)
- [Shougo/ddc.vim](https://github.com/Shougo/ddc.vim)
